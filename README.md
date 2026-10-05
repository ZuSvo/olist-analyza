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

## Datový slovník:
#Tabulka orders: 
- 99441 řádků
- objednávky, jejich status, data a časy změny stavů
- prádné buňky:
    • Order_approved_at: prázdné <1%
	• Order_delivered_carrier_date: prázdné 2%
	• Order_delivered_customer_date: prázdné 3%

#Tabulka order_items:
- 112650 řádků
- druh a počet položek objednávky, prodejce, cena, cena dopravy, limitní den odeslání
- 0 chybných a prázdných řádků

#Tabulka customers:
- 99441 řádků
- ID zákazníka u zakázky, unikátní ID zákazníka, město, PSČ a stát
- 0 chybných a prázdných řádků

#Tabulka payments:
- 103886 řádků
- ID objednávky, detaily platby a způsob platby
- 0 chybných a prádných řádků

#Tabulka reviews:
- 99224 řádků
- skóre a recenze k jednoltivým objednávkám a datum jeji přijetí
- prázdné řádky:
    • Review_comment_title: prázdné 88%
	• Review_comment_message: prázdné 59%

#Tabulka Products:
- 32951 řádků
- detaily produktu jako rozměry, váha, hmotnost, zařazení do kategorie
- chybějící data:
    • Product_category_name: prázdné 2%
	• Products_name_length: prázdné 2%
	• Products_desctription_length: prázdné 2%
	• Products_photos_qty: prázdné 2%
	• Product_weight_g: prázdné <1%
	• Products_length_cm: prázdné <1%
	• Product_height_cm: prázdné <1%
	• Product_width_cm: prázdné <1%

#Tabulka sellers:
- 3095 řádků
- seznam prodejců, PSČ, město, stát
- 0 chybných nebo prázdných řádků

#Tabulka category_translation:
- 72 řádků
- seznam a popis kategorií
- 0 chybných a prázdných řádků





