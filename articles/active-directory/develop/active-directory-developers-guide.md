---
title: Azure Active Directory voor ontwikkelaars | Microsoft Docs
description: Dit artikel geeft een overzicht van het aanmelden bij werk- en schoolaccounts van Microsoft met Azure Active Directory.
services: active-directory
author: dstrockis
manager: mtillman
editor: ''
ms.assetid: 5c872c89-ef04-4f4c-98de-bc0c7460c7c2
ms.service: active-directory
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/07/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 580dc7414baea80ef005826d27fb5f2d35ee8895
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2018
---
# <a name="azure-active-directory-for-developers"></a>Azure Active Directory voor ontwikkelaars
Azure Active Directory (Azure AD) is een identiteitsservice in de cloud waarmee ontwikkelaars apps kunnen maken die ervoor zorgen dat elke gebruiker met een Microsoft-werkaccount of -schoolaccount zich veilig kan aanmelden. Azure AD biedt ondersteuning voor ontwikkelaars die Line-Of-Business-apps met één tenant maken en voor ontwikkelaars die apps met meerdere tenants willen ontwikkelen. Naast eenvoudige aanmelding maakt Azure AD ook mogelijk dat apps Microsoft-API's zoals [Microsoft Graph](https://developer.microsoft.com/en-us/graph/docs/concepts/overview) en aangepaste API's die zijn gemaakt op het Azure AD-platform aanroepen.  In deze documentatie wordt beschreven hoe u Azure AD-ondersteuning aan uw app kunt toevoegen met standaardprotocollen zoals OAuth 2.0 en OpenID Connect.

> [!NOTE]
> De meeste inhoud op deze pagina is gericht op het Azure AD v1-eindpunt, dat alleen ondersteuning biedt voor Microsoft-werkaccounts en -schoolaccounts. Als u persoonlijke Microsoft-accounts of Microsoft-accounts van klanten wilt aanmelden, kunt u meer informatie over het [Azure AD v2.0-eindpunt](active-directory-appmodel-v2-overview.md) bekijken. Het Azure AD v2.0-eindpunt biedt een uniforme ontwikkeling voor apps met aanmelding voor zowel gebruikers met Azure AD-accounts (werk en school) als gebruikers met persoonlijke Microsoft-accounts.

| | |
| --- | --- |
|[De basisbeginselen van verificatie](active-directory-authentication-scenarios.md) | Een inleiding tot verificatie met Azure AD. |
|[Soorten toepassingen](active-directory-authentication-scenarios.md#application-types-and-scenarios) | Een overzicht van de verificatiescenario's die worden ondersteund door Azure AD. |                                

## <a name="get-started"></a>Aan de slag
De onderstaande handleidingen begeleiden u bij het maken van een app op het platform van uw voorkeur met behulp van de Azure Active Directory Library (ADAL) SDK. Raadpleeg onze documentatie over het [Azure AD v2.0-eindpunt](active-directory-appmodel-v2-overview.md) als u informatie zoekt over het gebruik van de Microsoft Authentication Library (MSAL).

|  |  |  |  |
| --- | --- | --- | --- |
| <center>![Mobiele en bureaubladapps](./media/active-directory-developers-guide/NativeApp_Icon.png)<br />Mobiele en bureaubladapps</center> | [Overzicht](active-directory-authentication-scenarios.md#native-application-to-web-api)<br /><br />[iOS](active-directory-devquickstarts-ios.md)<br /><br />[Android](active-directory-devquickstarts-android.md) | [.NET (WPF)](active-directory-devquickstarts-dotnet.md)<br /><br />[.NET (UWP)](active-directory-devquickstarts-windowsstore.md)<br /><br />[Xamarin](active-directory-devquickstarts-xamarin.md) | [Cordova](active-directory-devquickstarts-cordova.md) |
| <center>![Web-apps](./media/active-directory-developers-guide/Web_app.png)<br />Web-apps</center> | [Overzicht](active-directory-authentication-scenarios.md#web-browser-to-web-application)<br /><br />[ASP.NET](active-directory-devquickstarts-webapp-dotnet.md)<br /><br />[Java](active-directory-devquickstarts-webapp-java.md) | [Python](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi)<br/><br/> [Node.js](active-directory-devquickstarts-openidconnect-nodejs.md) | |
| <center>![Apps met één pagina](./media/active-directory-developers-guide/SPA.png)<br />Apps met één pagina</center> | [Overzicht](active-directory-authentication-scenarios.md#single-page-application-spa)<br /><br />[AngularJS](active-directory-devquickstarts-angular.md)<br /><br />[JavaScript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |  |  |
| <center>![Web-API's](./media/active-directory-developers-guide/Web_API.png)<br />Web-API's</center> | [Overzicht](active-directory-authentication-scenarios.md#web-application-to-web-api)<br /><br />[ASP.NET](active-directory-devquickstarts-webapi-dotnet.md)<br /><br />[Node.js](active-directory-devquickstarts-webapi-nodejs.md) | &nbsp; |
| <center>![Service naar service](./media/active-directory-developers-guide/Service_App.png)<br />Service naar service</center> | [Overzicht](active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api)<br /><br />[.NET](active-directory-code-samples.md#server-or-daemon-application-to-web-api)|  |

## <a name="how-to-guides"></a>Handleidingen
De onderstaande handleidingen behandelen een paar van de meest voorkomende taken in Azure AD.

|                                                                           |  |
|---------------------------------------------------------------------------| --- |
|[Een toepassing registreren](active-directory-integrating-applications.md)           | Een toepassing registreren in Azure AD. |
|[Toepassingen voor meerdere tenants](active-directory-devhowto-multi-tenant-overview.md)    | Aanmelden bij een Microsoft-werkaccount. |
|[OAuth- en OpenID Connect-protocollen](active-directory-protocols-openid-connect-code.md)| Het aanmelden van gebruikers en aanroepen van web-API's met de Microsoft-verificatieprotocollen. |
|[Aanvullende handleidingen](active-directory-developers-guide-index.md#guides)        |  Een lijst met handleidingen die beschikbaar zijn voor Azure AD.   |

## <a name="reference-topics"></a>Onderwerpen met naslaginformatie
De volgende artikelen bieden gedetailleerde informatie over de API's, protocolberichten en termen die worden gebruikt in Azure AD.

|                                                                                   | |
| ----------------------------------------------------------------------------------| --- |
| [Verificatiebibliotheken (ADAL)](active-directory-authentication-libraries.md)   | Een overzicht van de bibliotheken en SDK's die worden geleverd door Azure AD. |
| [Codevoorbeelden](active-directory-code-samples.md)                                  | Een lijst met alle Azure AD-codevoorbeelden. |
| [Woordenlijst](active-directory-dev-glossary.md)                                      | Termen en definities van woorden die in deze documenten worden gebruikt. |
| [Aanvullende onderwerpen met naslaginformatie](active-directory-developers-guide-index.md#reference)| Een lijst met onderwerpen met naslaginformatie die beschikbaar zijn voor Azure AD.   |


[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
