---
title: JavaScript-gebruiker gedefinieerde functies in Azure Stream Analytics
description: Dit artikel wordt beschreven hoe u geavanceerde query mechanisme met JavaScript-gebruiker gedefinieerde functies uitvoert in Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: 462bd55dfae3a2c471d1111637a6de0bc95e6bfa
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2018
---
# <a name="azure-stream-analytics-javascript-user-defined-functions"></a>Azure Stream Analytics JavaScript gebruiker gedefinieerde functies
Azure Stream Analytics ondersteunt de gebruiker gedefinieerde functies die zijn geschreven in JavaScript. Met de uitgebreide set **tekenreeks**, **RegExp**, **Math**, **matrix**, en **datum** methoden die JavaScript biedt, complexe gegevenstransformaties met Stream Analytics-taken eenvoudiger worden te maken.

## <a name="javascript-user-defined-functions"></a>De gebruiker gedefinieerde functies JavaScript
De gebruiker gedefinieerde functies JavaScript ondersteuning voor stateless, compute-alleen scalaire functies die niet nodig voor externe verbinding hebt. De geretourneerde waarde van een functie mag alleen een scalaire waarde (single). Nadat u een JavaScript-gebruiker gedefinieerde functie aan een taak toevoegt, kunt u de functie overal in de query, zoals een ingebouwde scalaire functie.

Hier volgen enkele scenario's waar JavaScript gebruiker gedefinieerde functies interessant mogelijk:
* Parseren en manipuleren van tekenreeksen die functies van de reguliere expressie, bijvoorbeeld hebben **Regexp_Replace()** en **Regexp_Extract()**
* Decoderen en gegevens, bijvoorbeeld conversie van binair naar hexadecimale codering
* Rekenkundige berekeningen met JavaScript uitvoeren **Math** functies
* Uitvoeren van bewerkingen zoals sorteren, join, zoeken en opvulling matrix

Hier volgen enkele dingen die u met een JavaScript gebruiker gedefinieerde functie in Stream Analytics kan niet doen:
* Vestigen op externe REST-eindpunten, bijvoorbeeld: uitvoeren van IP-lookup- of opvragen referentiegegevens omkeren van een externe bron
* Indeling van aangepaste gebeurtenisserialisatie uitvoeren of deserialiseren van de invoer/uitvoer
* Maken van aangepaste statistische functies

Hoewel u functies zoals **Date.GetDate()** of **Math.random()** worden niet geblokkeerd in de definitie van de functies, Vermijd het gebruik ervan. Deze functies **niet** hetzelfde resultaat retourneren telkens wanneer u deze aanroept en de Azure Stream Analytics-service geen logboek van de functie aanroepen houdt en resultaten geretourneerd. Als een functie verschillende resultaten op dezelfde gebeurtenissen retourneert, herhaalbaarheid niet worden gegarandeerd wanneer een taak opnieuw wordt gestart door u of door de Stream Analytics-service.

## <a name="add-a-javascript-user-defined-function-in-the-azure-portal"></a>Toevoegen van een gebruiker gedefinieerde JavaScript-functie in de Azure portal
Voer deze stappen voor het maken van een eenvoudige op JavaScript gebruiker gedefinieerde functie onder een bestaande Stream Analytics-taak:

1.  Zoek uw Stream Analytics-taak in de Azure-portal.
2.  Onder **taak TOPOLOGIE**, selecteert u de functie. Een lege lijst met functies wordt weergegeven.
3.  Selecteer voor het maken van een nieuwe, door de gebruiker gedefinieerde functie **toevoegen**.
4.  Op de **nieuwe functie** blade voor **functietype**, selecteer **JavaScript**. Een standaardsjabloon voor de functie wordt weergegeven in de editor.
5.  Voor de **UDF alias**, voer **hex2Int**, en de implementatie van de functie als volgt wijzigen:

    ```
    // Convert Hex value to integer.
    function main(hexValue) {
        return parseInt(hexValue, 16);
    }
    ```

6.  Selecteer **Opslaan**. De functie wordt weergegeven in de lijst met functies.
7.  Selecteer de nieuwe **hex2Int** functioneren en controleren van de functiedefinitie. Alle functies een **UDF** voorvoegsel toegevoegd aan de functie-alias. U moet *het voorvoegsel* wanneer u de functie aanroepen in de Stream Analytics-query. In dit geval u aanroepen **UDF.hex2Int**.

## <a name="call-a-javascript-user-defined-function-in-a-query"></a>Een gebruiker gedefinieerde functie van JavaScript-aanroep in een query

1. In de query-editor, onder **taak TOPOLOGIE**, selecteer **Query**.
2.  Uw query bewerken en roept u vervolgens de gebruiker gedefinieerde functie, als volgt:

    ```
    SELECT
        time,
        UDF.hex2Int(offset) AS IntOffset
    INTO
        output
    FROM
        InputStream
    ```

3.  Als u wilt uploaden het voorbeeldgegevensbestand, met de rechtermuisknop op de taak-invoer.
4.  Als u wilt uw query testen, selecteer **testen**.


## <a name="supported-javascript-objects"></a>Ondersteunde JavaScript-objecten
Azure Stream Analytics JavaScript gebruiker gedefinieerde functies ondersteuning voor ingebouwde JavaScript-objecten. Zie voor een lijst van deze objecten [globale objecten](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects).

### <a name="stream-analytics-and-javascript-type-conversion"></a>Conversie van stream Analytics en JavaScript

Er zijn verschillen in de typen dat de Stream Analytics query language- en JavaScript-ondersteuning. Deze tabel bevat de conversie-toewijzingen tussen de twee:

Stream Analytics | Javascript
--- | ---
bigint | Nummer (JavaScript kan alleen bestaan uit gehele getallen maximaal nauwkeurig 2 ^ 53)
DateTime | Datum (JavaScript alleen ondersteund in milliseconden)
dubbele | Aantal
nvarchar(max) | Tekenreeks
Gegevens vastleggen | Object
Matrix | Matrix
NULL | Null


Hier volgen JavaScript aan Stream Analytics-conversies:


Javascript | Stream Analytics
--- | ---
Aantal | Bigint (als het getal ronde en tussen lang is. MinValue en een lange. MaxValue; anders is de dubbele)
Date | DateTime
Tekenreeks | nvarchar(max)
Object | Gegevens vastleggen
Matrix | Matrix
Null is en niet-gedefinieerd | NULL
Een ander type (bijvoorbeeld, een functie of fout) | Niet ondersteund (resulteert in een runtime-fout)

## <a name="troubleshooting"></a>Problemen oplossen
JavaScript-runtime-fouten worden beschouwd als onherstelbare en worden opgehaald via het activiteitenlogboek. Voor het ophalen van het logboek, Ga in de Azure-portal aan uw project en selecteer **activiteitenlogboek**.


## <a name="other-javascript-user-defined-function-patterns"></a>Andere patronen van de gebruiker gedefinieerde functie JavaScript

### <a name="write-nested-json-to-output"></a>Geneste JSON naar uitvoer schrijven
Als er een vervolgactie verwerkingsstap die gebruikmaakt van een Stream Analytics-taak uitvoer als invoer en hiervoor een JSON-indeling, kunt u een JSON-tekenreeks naar uitvoer schrijven. Het volgende voorbeeld wordt de **JSON.stringify()** functie voor het inpakken van alle naam/waarde-paren van de invoer en vervolgens als één enkele tekenreekswaarde in de uitvoer geschreven.

**Definitie van de gebruiker gedefinieerde functie JavaScript:**

```
function main(x) {
return JSON.stringify(x);
}
```

**Voorbeeldquery:**
```
SELECT
    DataString,
    DataValue,
    HexValue,
    UDF.json_stringify(input) As InputEvent
INTO
    output
FROM
    input PARTITION BY PARTITIONID
```

## <a name="get-help"></a>Help opvragen
Voor aanvullende hulp kunt u proberen onze [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Volgende stappen
* [Inleiding tot Azure Stream Analytics](stream-analytics-introduction.md)
* [Aan de slag met Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics-taken schalen](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics query language reference](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics management REST API-referentiemateriaal](https://msdn.microsoft.com/library/azure/dn835031.aspx)
