---
title: Beheerde Service-identiteit (MSI) voor Azure Active Directory
description: Een overzicht van beheerde Service-identiteit voor Azure-resources.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 12/19/2017
ms.author: skwan
ms.openlocfilehash: e4f9d9e4e0f84610ad072d889abf68b62c0dd41f
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2018
---
#  <a name="managed-service-identity-msi-for-azure-resources"></a>Managed Service-identiteit (MSI) voor Azure-resources

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Een algemene uitdaging bij het bouwen van cloud-toepassingen is het beheren van de referenties die moeten worden in uw code voor verificatie bij de cloud-services. Deze referenties veilig te houden, is een belangrijke taak. In het ideale geval ze nooit worden weergegeven op ontwikkelaars werkstations of ophalen ingecheckt in broncodebeheer. Azure Sleutelkluis biedt een manier voor het veilig opslaan van referenties en andere sleutels en geheimen, maar uw code moet worden geverifieerd voor Sleutelkluis om op te halen ze. Beheerde Service-identiteit (MSI) maakt het oplossen van dit probleem eenvoudiger door middel van een identiteit automatisch beheerde Azure-services in Azure Active Directory (Azure AD). U kunt deze identiteit gebruiken om alle services die ondersteuning biedt voor Azure AD-verificatie, met inbegrip van de Sleutelkluis, zonder dat u geen referenties hoeft in uw code te verifiëren.

## <a name="how-does-it-work"></a>Hoe werkt het?

Wanneer u een beheerde Service-identiteit op een Azure-service inschakelt, maakt Azure automatisch een identiteit voor het service-exemplaar in de Azure AD-tenant die wordt gebruikt door uw Azure-abonnement.  Achter de richt Azure de referenties voor de identiteit op het service-exemplaar.  Uw code kunt vervolgens lokale aanvraag toegangstokens ophalen voor services die ondersteuning bieden voor Azure AD-verificatie aanbrengen.  Azure zorgt voor het implementeren van de referenties die worden gebruikt door het service-exemplaar.  Als het service-exemplaar wordt verwijderd, ruimt Azure automatisch de referenties en de identiteit in Azure AD.

Hier volgt een voorbeeld van hoe de Service-identiteit beheerd met Azure Virtual Machines werkt.

![Voorbeeld van de MSI van de virtuele Machine](../media/msi-vm-example.png)

1. Azure Resource Manager ontvangt een bericht MSI op een virtuele machine inschakelen.
2. Azure Resource Manager maakt een Service-Principal in Azure AD om weer te geven van de identiteit van de virtuele machine. De Service-Principal gemaakt in de Azure AD-tenant die wordt vertrouwd door dit abonnement.
3. Azure Resource Manager configureert de details van de Service-Principal in de VM-extensie van de MSI van de virtuele machine.  Deze stap omvat het configureren van client-ID en certificaat dat wordt gebruikt door de uitbreiding toegangstokens ophalen uit Azure AD.
4. Nu dat de identiteit van de virtuele machine van de Service-Principal bekend is, is het kan dat deze toegang tot Azure-resources worden verleend.  Bijvoorbeeld, als uw code aan te roepen Azure Resource Manager, wilt u toewijzen van de VM-Service-Principal de juiste rol met behulp van op rollen gebaseerde toegangsbeheer (RBAC) in Azure AD.  Als uw code aan te roepen Sleutelkluis, kunt u uw code toegang tot het specifiek geheim of de sleutel in de Sleutelkluis wilt verlenen.
5. Uw code die wordt uitgevoerd op de virtuele machine vraagt een token van een lokaal eindpunt dat wordt gehost door de MSI-VM-extensie: http://localhost:50342/oauth2/token.  De resourceparameter geeft u de service die het token wordt verzonden. Bijvoorbeeld, als u wilt dat uw code te verifiëren voor Azure Resource Manager, gebruikt u resource =https://management.azure.com/.
6. De VM-extensie MSI maakt gebruik van de geconfigureerde client-ID en certificaat voor het aanvragen van een toegangstoken van Azure AD.  Azure AD retourneert een toegangstoken JSON Web Token (JWT).
7. Uw code verzendt het toegangstoken op een aanroep van een service die Azure AD-verificatie ondersteunt.

Elke Azure-service die ondersteuning biedt voor Service-identiteit beheerd heeft een eigen methode voor uw code verkrijgen van een toegangstoken. Bekijk de zelfstudies voor elke service om erachter te komen de specifieke methode voor het ophalen van een token.

## <a name="try-managed-service-identity"></a>Probeer beheerde Service-identiteit

Probeer een Service-identiteit beheerd-zelfstudie voor meer informatie over de end-to-end-scenario's voor toegang tot verschillende Azure-resources:
<br><br>
| MSI-functionaliteit-bron | Procedures voor |
| ------- | -------- |
| Azure VM (Windows) | [Toegang tot Azure Data Lake Store met een Windows-VM beheerde Service-identiteit](tutorial-windows-vm-access-datalake.md) |
|                    | [Toegang tot Azure Resource Manager met een Windows VM beheerde Service-identiteit](tutorial-windows-vm-access-arm.md) |
|                    | [Toegang tot Azure SQL met een Windows VM beheerde Service-identiteit](tutorial-windows-vm-access-sql.md) |
|                    | [Toegang tot Azure Storage via toegangssleutel met een Windows VM beheerde Service-identiteit](tutorial-windows-vm-access-storage.md) |
|                    | [Toegang tot Azure Storage via SAS met een Windows VM beheerde Service-identiteit](tutorial-windows-vm-access-storage-sas.md) |
|                    | [Toegang tot een niet-Azure AD-resource met een Windows VM beheerde Service-identiteit en Azure Sleutelkluis](tutorial-windows-vm-access-nonaad.md) |
| Azure virtuele machine (Linux)   | [Toegang tot Azure Data Lake Store met een virtuele Linux-machine beheerde Service-identiteit](tutorial-linux-vm-access-datalake.md) |
|                    | [Toegang tot Azure Resource Manager met een virtuele Linux-machine beheerde Service-identiteit](tutorial-linux-vm-access-arm.md) |
|                    | [Toegang tot Azure Storage via toegangssleutel met een Linux VM beheerde Service-identiteit](tutorial-linux-vm-access-storage.md) |
|                    | [Toegang tot Azure Storage via SAS met een virtuele Linux-machine beheerde Service-identiteit](tutorial-linux-vm-access-storage-sas.md) |
|                    | [Toegang tot een niet-Azure AD-resource met een Linux VM beheerde Service-identiteit en Azure Sleutelkluis](tutorial-linux-vm-access-nonaad.md) |
| Azure App Service  | [Beheerde Service-identiteit gebruiken met Azure App Service- of Azure-functies](/azure/app-service/app-service-managed-service-identity) |
| Azure Functions    | [Beheerde Service-identiteit gebruiken met Azure App Service- of Azure-functies](/azure/app-service/app-service-managed-service-identity) |
| Azure Service Bus  | [Beheerde Service-identiteit gebruiken met Azure Servicebus](../../service-bus-messaging/service-bus-managed-service-identity.md) |
| Azure Event Hubs   | [Beheerde Service-identiteit gebruiken met Azure Event Hubs](../../event-hubs/event-hubs-managed-service-identity.md) |

## <a name="which-azure-services-support-managed-service-identity"></a>Welke Azure-services ondersteuning bieden voor Service-identiteiten beheerd?

Azure-services die ondersteuning bieden voor Service-identiteit beheerd kunnen MSI te verifiëren bij services die ondersteuning bieden voor Azure AD-verificatie gebruiken.  We zijn bezig het MSI- en Azure AD authentication integreren in Azure.  Controleer regelmatig of er updates.

### <a name="azure-services-that-support-managed-service-identity"></a>Azure-services die ondersteuning bieden voor Service-identiteit beheerd

De volgende Azure-services ondersteuning bieden voor Service-identiteiten beheerd.

| Service | Status | Date | Configureren | Een token ophalen |
| ------- | ------ | ---- | --------- | ----------- |
| Azure Virtual Machines | Preview | September 2017 | [Azure Portal](qs-configure-portal-windows-vm.md)<br>[PowerShell](qs-configure-powershell-windows-vm.md)<br>[Azure-CLI](qs-configure-cli-windows-vm.md)<br>[Azure Resource Manager-sjablonen](qs-configure-template-windows-vm.md) | [REST](how-to-use-vm-token.md#get-a-token-using-http)<br>[.NET](how-to-use-vm-token.md#get-a-token-using-c)<br>[Bash/Curl](how-to-use-vm-token.md#get-a-token-using-curl)<br>[Go](how-to-use-vm-token.md#get-a-token-using-go)<br>[PowerShell](how-to-use-vm-token.md#get-a-token-using-azure-powershell) |
| Azure App Service | Preview | September 2017 | [Azure Portal](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Azure Resource Manager-sjabloon](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol) |
| Azure Functions<sup>1</sup> | Preview | September 2017 | [Azure Portal](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Azure Resource Manager-sjabloon](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol) |
| Azure Data Factory V2 | Preview | November 2017 | [Azure Portal](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity)<br>[PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-powershell)<br>[REST](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-rest-api)<br>[SDK](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-sdk) |

<sup>1</sup> azure Functions ondersteunt gebruikerscode voor het gebruik van een identiteit, maar triggers en bindingen mogelijk nog steeds verbindingsreeksen.

### <a name="azure-services-that-support-azure-ad-authentication"></a>Azure-services die ondersteuning voor Azure AD-verificatie

De volgende services ondersteuning bieden voor Azure AD-verificatie en getest met clientservices die gebruikmaken van beheerde Service-identiteit.

| Service | Resource-id | Status | Date | Toegang toewijzen |
| ------- | ----------- | ------ | ---- | ------------- |
| Azure Resource Manager | https://management.azure.com | Beschikbaar | September 2017 | [Azure Portal](howto-assign-access-portal.md) <br>[PowerShell](howto-assign-access-powershell.md) <br>[Azure-CLI](howto-assign-access-CLI.md) |
| Azure Key Vault | https://vault.azure.net | Beschikbaar | September 2017 | |
| Azure Data Lake | https://datalake.azure.net | Beschikbaar | September 2017 | |
| Azure SQL | https://database.windows.net | Beschikbaar | Oktober 2017 | |
| Azure Event Hubs | https://eventhubs.azure.net | Beschikbaar | December 2017 | |
| Azure Service Bus | https://servicebus.azure.net | Beschikbaar | December 2017 | |

## <a name="how-much-does-managed-service-identity-cost"></a>Wat kost Service-identiteit beheerd?

Beheerde Service-identiteit die wordt geleverd met Azure Active Directory vrij, dit is de standaardoptie voor Azure-abonnementen.  Er is geen extra kosten voor Service-identiteit beheerd.

## <a name="support-and-feedback"></a>Ondersteuning en feedback

We graag horen van u!

* Hoe kan ik vragen stellen op de Stack Overflow met het label [azure msi](http://stackoverflow.com/questions/tagged/azure-msi).
* Functie aanvragen of feedback geven over de [forum met feedback van Azure AD voor ontwikkelaars](https://feedback.azure.com/forums/169401-azure-active-directory/category/164757-developer-experiences).






