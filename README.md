# Analýza e-shopu Olist

End-to-end datový projekt: od surových dat po reporty.

## Obchodní otázky
1. Jak se vyvíjí tržby a počet objednávek v čase?
2. Které kategorie a státy přinášejí nejvíc tržeb?
3. Ovlivňuje zpoždění doručení hodnocení zákazníků?
4. Kolik zákazníků se vrací a jak je segmentovat?

## Nástroje
Excel (Power Query), PostgreSQL, Python (pandas), Tableau Public, Power BI, Jaspersoft Studio

## Data
[Brazilian E-Commerce Public Dataset by Olist](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce), licence CC BY-NC-SA 4.0.
Data nejsou součástí repozitáře.

## Průběh a zjištění:

## Poznámky ke kvalitě dat v tabulce ORDERS:
• Stav objednávek: 96 478 z 99 441 objednávek je delivered. Zbylých 2 963 je v ostatních stavech. Pozor, v datech se píše canceled (s jedním „l“).
• Chybí datum schválení (160 rows): 141 canceled (zrušeno před schválením), 5 created (čeká na schválení), 14 delivered → data quality issue.
• Chybí datum předání přepravci (1 783 rows): 609 unavailable + 550 canceled + 314 invoiced + 301 processing + 5 created + 2 approved + 2 delivered. Jde tedy o objednávky, které se ještě neodeslaly nebo byly zrušeny. Jen 2 delivered bez data předání jsou data quality issue. Hypotéza o osobním odběru se nepotvrdila. 75 zrušených objednávek (625 − 550) bylo zrušeno až po předání přepravci.
• Chybí datum doručení (2 965 rows): 2 963 nedoručených − 6 canceled, které datum doručení mají, + 8 delivered bez data doručení = 2 965 → dva data quality issues. Shipped (1 107) datum předání přepravci má, ale doručení ne, což odpovídá zásilkám na cestě.
• Stav unavailable: Olist stavy oficiálně nedokumentuje. Nejpravděpodobnější výklad: zboží nebylo dostupné, objednávka byla schválena, ale neodeslána. Data to podporují: všech 609 objednávek unavailable nemá datum předání přepravci ani doručení. Je to koncový stav podobný canceled.
• Důsledek pro analýzu: při výpočtu doby doručení budeme pracovat jen s objednávkami delivered, které mají vyplněné datum doručení.


## Datový slovník

| Tabulka | Rows | Obsah | Kvalita dat |
| --- | --- | --- | --- |
| orders | 99 441 | objednávky, status, časová osa | chybí schválení <1 %, předání přepravci 2 %, doručení 3 % (vysvětleno stavy objednávek) |
| order_items | 112 650 | položky objednávek (1 row = 1 kus), produkt, prodejce, cena, doprava | bez chyb |
| customers | 99 441 | zákazník u objednávky, unikátní ID zákazníka, PSČ, město, stát | bez chyb |
| payments | 103 886 | platby (typ, splátky, částka); objednávka může mít víc plateb | bez chyb |
| reviews | 99 224 | hodnocení 1–5 a komentáře | komentáře dobrovolné: chybí titulek 88 %, text 59 % |
| products | 32 951 | kategorie, rozměry, váha, popis | chybí kategorie 2 %, rozměry/váha <1 % |
| sellers | 3 095 | prodejci, PSČ, město, stát | bez chyb |
| category_translation | 71 | portugalský → anglický název kategorie | bez chyb |

## Datový model
![Entity relationship diagram for the Olist e-commerce data model. The main subject is a database schema with tables customers, orders, payments, reviews, order_items, products, sellers, and category_translation arranged around the central orders table. Customers connect to orders in a one-to-many relationship, while orders connect to payments, reviews, and order_items. Order_items connect to products and sellers, and products connect to category_translation. The diagram uses green bordered table boxes, gray connector lines, cardinality markers, and a light gray background. Visible text includes customer_id, order_id, order_status, product_id, seller_id, payment_type, review_id, and category_translation, along with the table names shown in the schema.](datovy_model.png)

## Zjištění z Power query:
Transformace v Power Query:
products: anglické kategorie přes Merge (Left Outer), chybějící překlad → portugalský název, chybějící kategorie → "unknown"
orders: datum_objednavky, dny_doruceni, zpozdeni_dny, je_zpozdeno
order_items: trzba_polozky = price + freight_value
KPI| Hodnota
Celkové tržby (price + freight): 15 843 553 BRL
Počet objednávek celkem: 99 441
Objednávek s položkami: 98 666
Počet položek: 112 650
AOV (tržby / objednávky s položkami): cca 160,6 BRL
Průměrná hodnota položky (tržby / položky): cca 140,6 BRL
Doručené včas / zpožděné / bez data doručení: 89 941 / 6 535 / 2 965
Podíl zpožděných (z objednávek s datem doručení): 6,8 %
Průměrná doba doručení: 12 dní
Průměrné hodnocení: včas / zpožděné: 4,29 / 2,27 (rozdíl 2 hvězdičky, cca o 47 % nižší)
Top 3 kategorie podle tržeb: health_beauty (1 441 248 BRL), watches_gifts (1 305 542 BRL), bed_bath_table (1 241 682 BRL).
Poznámka k podílu zpožděných: porovnáváme data bez času, takže doručení v den slibovaného termínu se počítá jako včas. Často citovaných zhruba 8 % vznikne, když se porovnává i čas a zpoždění o pár hodin se počítá jako zpoždění.
Count vs. Distinct Count: v orders je order_id primární klíč, každá hodnota je jen jednou, takže Count = Distinct Count = 99 441. V order_items je order_id cizí klíč a opakuje se u každé položky: Count = 112 650 (rows), Distinct Count = 98 666 (objednávek).



