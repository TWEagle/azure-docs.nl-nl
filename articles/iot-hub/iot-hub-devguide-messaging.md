---
title: Inzicht in Azure IoT Hub messaging | Microsoft Docs
description: Handleiding voor ontwikkelaars - apparaat-naar-cloud- en cloud-naar-apparaat met IoT Hub berichten. Bevat informatie over berichtindelingen en ondersteunde communicatieprotocollen.
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 3fc5f1a3-3711-4611-9897-d4db079b4250
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: 4e3eb4682f2c7d8e22191b763457c4cca356b59d
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2018
---
# <a name="device-to-cloud-and-cloud-to-device-messaging-with-iot-hub"></a>Apparaat-naar-cloud- en cloud-naar-apparaat met IoT Hub messaging

Gebruik IoT Hub messaging om te communiceren met uw apparaten met:

* Verzenden van [apparaat-naar-cloud] [ lnk-d2c] berichten van uw apparaten aan uw oplossing voor back-end.
* Verzenden van [cloud-naar-apparaat] [ lnk-c2d] berichten van de oplossing voor back-end op uw apparaten.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Core-eigenschappen van de IoT Hub messaging-functionaliteit zijn de betrouwbaarheid en duurzaamheid van berichten. Deze eigenschappen inschakelen herstelmogelijkheden bij onregelmatige connectiviteit aan de kant van het apparaat en pieken in aan de kant van de cloud voor gebeurtenisverwerking laden. IoT Hub implementeert *ten minste eenmaal* levering wordt gegarandeerd dat voor zowel cloud-naar-apparaat als apparaat-naar-cloud-berichten.

Zie de artikelen voor een inleiding tot de mogelijkheden van IoT Hub, [Azure en Internet der dingen] [ lnk-azure-iot] en [overzicht van de service Azure IoT Hub][lnk-iot-hub-overview].

## <a name="when-to-use-iot-hub-messaging"></a>Het gebruik van IoT-Hub messaging

Apparaat-naar-cloud-berichten voor het verzenden van time series Telemetrie en waarschuwingen uit de app op uw apparaat en cloud-naar-apparaat-berichten gebruiken voor eenzijdige meldingen app op uw apparaat.

* Raadpleeg [apparaat-naar-cloud communicatie richtlijnen] [ lnk-d2c-guidance] indien in waarover twijfel bestaat tussen het gebruik van apparaat-naar-cloud-berichten, gemelde eigenschappen of bestand uploaden.
* Raadpleeg [Cloud-naar-apparaat communicatie richtlijnen] [ lnk-c2d-guidance] indien in waarover twijfel bestaat tussen het gebruik van cloud-naar-apparaat-berichten, gewenste eigenschappen of directe methoden.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over IoT Hub [apparaat-naar-cloud messaging][lnk-d2c].
* Meer informatie over IoT Hub [cloud-naar-apparaat messaging][lnk-c2d].

[lnk-azure-iot]: iot-hub-what-is-azure-iot.md
[lnk-iot-hub-overview]: iot-hub-what-is-iot-hub.md
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
[lnk-c2d]: iot-hub-devguide-messages-c2d.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md