---
title: Inleiding tot Azure Container Service voor Kubernetes
description: Met Azure Container Service voor Kubernetes kunt u eenvoudig toepassingen op basis van containers implementeren en beheren in Azure.
services: container-service
author: gabrtv
manager: timlt
ms.service: container-service
ms.topic: overview
ms.date: 11/13/2017
ms.author: gamonroy
ms.custom: mvc
ms.openlocfilehash: 5bfa445eb11ed8be608278d0b95249372f9976ab
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2018
---
# <a name="introduction-to-azure-container-service-aks-preview"></a>Inleiding tot AKS (Azure Container Service) preview

Azure Container Service (AKS) helpt bij het eenvoudig maken, configureren en beheren van een cluster virtuele machines die vooraf zijn geconfigureerd voor het uitvoeren van beperkte toepassingen. U kunt hierbij dan uitgaan van uw eigen vaardigheden of gebruikmaken van een grote en groeiende groep community experts om toepassingen op basis van containers te implementeren en beheren in Microsoft Azure.

Met AKS profiteert u van de hoogwaardige functies van Azure en behoudt u draagbaarheid van toepassingen via Kubernetes en het Docker-installatiekopieformaat.

> [!IMPORTANT]
> AKS (Azure Container Service) is momenteel in de **preview-fase**. Previews worden voor u beschikbaar gesteld op voorwaarde dat u akkoord gaat met de [aanvullende gebruiksvoorwaarden](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Sommige aspecten van deze functionaliteit kunnen wijzigen voordat deze functionaliteit algemeen beschikbaar wordt.
>

## <a name="managed-kubernetes-in-azure"></a>Managed Kubernetes in Azure

AKS verkleind de complexiteit en de operationele overhead die gepaard gaan met het beheer van een Kubernetes-cluster door veel van deze taken naar Azure over te hevelen. Azure handelt als een gehoste Kubernetes-service cruciale taken voor u af zoals statuscontrole en onderhoud. Bovendien betaalt u alleen voor de agentknooppunten binnen uw clusters, niet voor de hoofdknooppunten. Als een beheerde Kubernetes-service heeft AKS het volgende te bieden:

> [!div class="checklist"]
> * Geautomatiseerd upgraden van Kubernetes-versies en patching
> * Eenvoudige schaalmogelijkheden voor clusters
> * Zelfherstellende gehoste controlelaag (hoofdknooppunten)
> * Kostenbesparingen: betaal alleen voor actieve agentknooppunten in de groep

Omdat Azure het beheer van de knooppunten in uw AKS-cluster afhandelt, hoeft u veel taken niet meer handmatig uit te voeren, zoals het upgraden van clusters. Omdat Azure deze cruciale onderhoudstaken voor u uitvoert, hebt u via AKS geen directe toegang (zoals met SSH) tot het cluster.

## <a name="using-azure-container-service-aks"></a>Azure Container Service (AKS) gebruiken
AKS is bedoeld om een hostomgeving voor containers te bieden door gebruik te maken van de open source tools en technologieën die tegenwoordig populair zijn onder onze klanten. Om dit te bereiken, maken we de standaard API-eindpunten van Kubernetes beschikbaar. Met behulp van deze standaard eindpunten kunt u gebruikmaken van alle software die geschikt is voor communicatie met een Kubernetes-cluster. U kunt bijvoorbeeld [kubectl][kubectl-overview], [helm][helm] of [draft][draft] gebruiken.

## <a name="creating-a-kubernetes-cluster-using-azure-container-service-aks"></a>Een Kubernetes-cluster maken met Azure Container Service (AKS)
Als u AKS wilt gebruiken, implementeert u eerst een AKS-cluster met behulp van [Azure CLI][aks-quickstart] of via de portal (zoek in Marketplace op **Azure Container Service**). Als u een ervaren gebruiker bent en meer controle wilt over de Azure Resource Manager-sjablonen, kunt u het open source [acs-engine][acs-engine]-project gebruiken om uw eigen aangepaste Kubernetes-cluster te bouwen en dit te implementeren via de `az` CLI.

### <a name="using-kubernetes"></a>Kubernetes gebruiken
Kubernetes automatiseert het implementeren, schalen en beheren van toepassingen in containers. De applicatie bevat een uitgebreide set functies, zoals:
* Automatische binpacking
* Automatisch herstel
* Horizontaal schalen
* Servicedetectie en taakverdeling
* Geautomatiseerde implementaties en terugdraaiacties
* Geheimen- en configuratiebeheer
* Opslagindeling
* Batchuitvoering

## <a name="videos"></a>Video's

Azure Container Service (AKS) - Azure Friday, oktober 2017:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Container-Orchestration-Simplified-with-Managed-Kubernetes-in-Azure-Container-Service-AKS/player]
>
>

Hulpprogramma's voor het ontwikkelen en implementeren van toepassingen in Kubernetes (Azure OpenDev, juni 2017):

> [!VIDEO https://channel9.msdn.com/Events/AzureOpenDev/June2017/Tools-for-Developing-and-Deploying-Applications-on-Kubernetes/player]
>
>

U vindt meer informatie over het implementeren en beheren van AKS in de bijbehorende Quick Start van AKS.

> [!div class="nextstepaction"]
> [AKS-zelfstudie][aks-quickstart]

<!-- LINKS - external -->
[acs-engine]: https://github.com/Azure/acs-engine
[draft]: https://github.com/Azure/draft
[helm]: https://helm.sh/
[kubectl-overview]: https://kubernetes.io/docs/user-guide/kubectl-overview/

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md

