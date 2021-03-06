---
title: Zoomen niveaus en Services op basis van blokken in Azure-locatie | Microsoft Docs
description: Meer informatie over zoomniveaus en Services op basis van blokken in Azure-locatie
services: location-based-services
keywords: 
author: jinzh-azureiot
ms.author: jinzh
ms.date: 3/6/2018
ms.topic: article
ms.service: location-based-services
documentationcenter: 
manager: cpendle
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 3a86bf84ad31933cc5008591a275d4f4aa52c9f1
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/08/2018
---
# <a name="zoom-levels-and-tile-grid"></a>Zoomen niveaus en -blokken
Services voor Azure op basis van locatie het coördinatensysteem van de bolvormige Mercator-projectie gebruiken (EPSG: 3857).

De hele wereld is onderverdeeld in vierkante tegels. Render (Raster) heeft 19 zoomniveaus, nummer 0 tot en met 18. Render (Vector) heeft 21 zoomniveaus, nummer 0 tot en met 20. Op zoomniveau 0, wordt de hele wereld op één venster past:

![Tegel wereld](./media/zoom-levels-and-tile-grid/world0.png)

4 tegels voor het weergeven van de hele wereld maakt gebruik van zoomniveau 1: een vierkant 2 x 2

![World tegel linksboven](./media/zoom-levels-and-tile-grid/world1a.png)     ![World tegel rechtsboven](./media/zoom-levels-and-tile-grid/world1c.png) 

![World tegel linksonder](./media/zoom-levels-and-tile-grid/world1b.png)     ![World tegel rechtsonder](./media/zoom-levels-and-tile-grid/world1d.png) 


Elke volgende zoomniveau quad delen de tegels van de vorige, voor het maken van een raster met 2<sup>zoomen</sup> x 2<sup>zoomen</sup>. Zoomniveau 20 is een raster 2<sup>20</sup> x 2<sup>20</sup>, of 1.048.576 x 1.048.576 tegels (109,951,162,778 in totaal).

De volledige tabel van de waarden voor inzoomen niveaus is hier:

|zoomniveau|meters/pixel|meters/tegel aan clientzijde|
|--- |--- |--- |
|0|156543|40075008|
|1|78271.5|20037504|
|2|39135.8|10018764.8|
|3|19567.9|5009382.4|
|4|9783.9|2504678.4|
|5|4892|1252352|
|6|2446|626176|
|7|1223|313088|
|8|611.5|156544|
|9|305.7|78259.2|
|10|152.9|39142.4|
|11|76.4|19558.4|
|12|38.2|9779.2|
|13|19.1|4889.6|
|14|9.6|2457.6|
|15|4.8|1228.8|
|16|2.4|614.4|
|17|1.2|307.2|
|18|0.6|152.8|
|19|0.3|76.4|
|20|0.15|38.2|

Tegels worden aangeroepen door zoomen niveau en de x en y-coördinaten overeenkomt met de tegel positie op het raster voor die zoomniveau.

Bij het bepalen van welke zoomniveau wilt gebruiken, houd er rekening mee dat elke locatie op een vaste positie op de tegel is. Dit betekent dat het aantal tegels om weer te geven van een bepaalde expanse van Territorium afhankelijk van de specifieke plaatsing van raster Inzoomen op de hele wereld is. Bijvoorbeeld, als er twee 900 meters uit elkaar, verwijst dit *kan* pas drie tegels om weer te geven van een route daartussen op zoomniveau 17. Als de West wijst is echter aan de rechterkant van de tegel en het eastern punt aan de linkerkant van het, duurt het vier tegels:

![Demo-/ uitzoomen](./media/zoom-levels-and-tile-grid/zoomdemo_scaled.png) 

Nadat het zoomniveau is vastgesteld, de x en y waarden kunnen worden berekend. De bovenste links tegel in elke raster inzoomen wordt x = 0, y = 0; de tegel rechtsonder is x 2 =<sup>zoomen van -1</sup>, y = 2<sup>zoomen 1</sup>.

Dit is het raster zoomniveau voor zoomniveau 1:

![Inzoomen raster voor zoomniveau 1](./media/zoom-levels-and-tile-grid/api_x_y.png)
