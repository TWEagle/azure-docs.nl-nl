---
title: Pushmeldingen toevoegen aan uw Xamarin.Android-app | Microsoft Docs
description: Informatie over het gebruik van Azure App Service en Azure Notification Hubs pushmeldingen verzendt naar uw Xamarin.Android-app
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: crdun
editor: 
ms.assetid: 6f7e8517-e532-4559-9b07-874115f4c65b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 10/12/2016
ms.author: crdun
ms.openlocfilehash: a6cdff68d63859c6a6612b606664d3e1fbaae375
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/09/2018
---
# <a name="add-push-notifications-to-your-xamarinandroid-app"></a>Pushmeldingen toevoegen aan uw Xamarin.Android-app
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Overzicht
In deze zelfstudie hebt u pushmeldingen toevoegen de [Xamarin.Android Quick Start](app-service-mobile-windows-store-dotnet-get-started.md) project, zodat een pushmelding wordt verzonden naar het apparaat telkens wanneer een record wordt ingevoegd.

Als u het gedownloade Quick Start-serverproject niet gebruikt, moet u het push notification-uitbreidingspakket. Zie voor meer informatie de [werken met de .NET-back-endserver SDK voor Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) handleiding.

## <a name="prerequisites"></a>Vereisten
Deze zelfstudie vereist de installatie:

* Een actief Google-account. U kunt zich aanmelden voor een Google-account bij [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302).
* [Google Cloud Messaging-clientonderdeel](http://components.xamarin.com/view/GCMClient/).

## <a name="configure-hub"></a>Een Notification Hub configureren
[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a id="register"></a>Inschakelen van Firebase Cloud-berichten
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-azure-to-send-push-requests"></a>Azure voor het verzenden van pushmeldingen aanvragen configureren
[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a id="update-server"></a>Bijwerken van het serverproject om pushmeldingen te verzenden
[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a id="configure-app"></a>Het clientproject voor pushmeldingen configureren
[!INCLUDE [mobile-services-xamarin-android-push-configure-project](../../includes/mobile-services-xamarin-android-push-configure-project.md)]

## <a id="add-push"></a>Push notifications code toevoegen aan uw app
[!INCLUDE [app-service-mobile-xamarin-android-push-add-to-app](../../includes/app-service-mobile-xamarin-android-push-add-to-app.md)]

## <a name="test"></a>Pushmeldingen testen in uw app
U kunt de app testen met behulp van een virtueel apparaat in de emulator. Er zijn extra configuratiestappen vereist wanneer u gebruikmaakt van een emulator.

1. Het virtuele apparaat moet hebben Google APIs ingesteld als doel in Android Virtual Device (AVD) manager.
   
    ![](./media/app-service-mobile-xamarin-android-get-started-push/google-apis-avd-settings.png)
2. Een Google-account toevoegen aan de Android-apparaat door te klikken op **Apps** > **instellingen** > **account toevoegen**, volg de instructies.
   
    ![](./media/app-service-mobile-xamarin-android-get-started-push/add-google-account.png)
3. Voer de todolist-app als voordat en een nieuwe taak invoegen. Dit moment wordt een pictogram weergegeven in het systeemvak. U kunt de meldingenlijst om weer te geven van de volledige tekst van de melding openen.
   
    ![](./media/app-service-mobile-xamarin-android-get-started-push/android-notifications.png)

<!-- URLs. -->
[Xamarin.Android quick start]: app-service-mobile-xamarin-android-get-started.md
[Google Cloud Messaging Client Component]: http://components.xamarin.com/view/GCMClient/
[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/
