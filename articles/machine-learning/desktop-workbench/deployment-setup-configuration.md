---
title: Instellingen voor beheer van Azure Machine Learning-Model en configuratie | Microsoft Docs
description: Dit document beschrijft de stappen en -concepten betrokken bij het instellen en configureren van Model Management in Azure Machine Learning.
services: machine-learning
author: aashishb
ms.author: aashishb
manager: hjerez
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 12/6/2017
ms.openlocfilehash: 0859031ac26b061861aa51dce1093f2fe4350935
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2018
---
# <a name="model-management-setup"></a>Instellingen voor het beheer van model

Dit document helpt u op weg met het gebruik van Azure ML-model management implementeren en beheren van uw machine learning-modellen als webservices. 

Met behulp van Azure ML-model management, kunt u efficiënt implementeren en beheren van Machine Learning-modellen die zijn gebouwd met een aantal frameworks, met inbegrip van SparkML, Keras, TensorFlow, de Microsoft cognitieve Toolkit of Python. 

Aan het einde van dit document moet u het volgende kunnen uw model beheeromgeving ingesteld en klaar voor het implementeren van uw machine learning-modellen.

## <a name="what-you-need-to-get-started"></a>Wat u moet aan de slag
Als u de meest buiten deze handleiding, hebt u eigenaarsrechten toegang tot een Azure-abonnement of resourcegroep die u kunt uw modellen te implementeren.
De CLI is voorgeïnstalleerd op de Workbench van Azure Machine Learning en klik op [Azure DSVMs](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-virtual-machine-overview).

## <a name="using-the-cli"></a>Met behulp van de CLI
Met de opdrachtregelinterfaces (CLIs) van de Workbench, klikt u op **bestand** -> **opdrachtprompt openen**. 

Op een gegevens wetenschappelijke virtuele Machine, verbinding maken en open de opdrachtprompt. Type `az ml -h` om de opties te bekijken. Voor meer informatie over de opdrachten gebruiken de--help-vlag.

U moet de CLIs installeren op andere systemen.

### <a name="installing-or-updating-on-windows"></a>Installeren (of bijwerken) in Windows

Installeren van Python van https://www.python.org/. Zorg ervoor dat u hebt geselecteerd voor het installeren van pip.

Open een opdrachtprompt met als Administrator uitvoeren en voer de volgende opdrachten:

```cmd
pip install -r https://aka.ms/az-ml-o16n-cli-requirements-file
```

### <a name="installing-or-updating-on-linux"></a>Installeren (of bijwerken) op Linux
Voer de volgende opdracht vanaf de opdrachtregel en volg de aanwijzingen:

```bash
sudo -i
pip install -r https://aka.ms/az-ml-o16n-cli-requirements-file
```

### <a name="configuring-docker-on-linux"></a>Configureren van Docker op Linux
Om te kunnen Docker op Linux configureren voor gebruik door gebruikers niet-hoofdmap, volg de instructies hier: [na de installatie stappen voor Linux](https://docs.docker.com/engine/installation/linux/linux-postinstall/)

>[!NOTE]
> U kunt het onderstaande Docker correct configureren script uitvoeren op een DSVM Linux. **Houd er rekening mee u afmelden en weer aanmelden na het uitvoeren van het script.**
>```
>sudo /opt/microsoft/azureml/initial_setup.sh
>```

## <a name="deploying-your-model"></a>Implementeren van uw model
Gebruik de CLI modellen als webservices implementeren. De webservices kunnen worden geïmplementeerd, lokaal of naar een cluster.

Beginnen met een lokale implementatie, te valideren dat uw model en code werkt, klikt u vervolgens implementeert op een cluster voor gebruik in productieomgevingen schaal.

Als u wilt starten, moet u uw implementatieomgeving instellen. De instelling van de omgeving is een taak. Zodra de installatie voltooid is, kunt u de omgeving voor toekomstige implementaties opnieuw gebruiken. Zie de volgende sectie voor meer informatie.

Wanneer de omgeving-installatie te voltooien:
- U wordt gevraagd aan te melden bij Azure. Als u wilt aanmelden, kunt u een webbrowser gebruiken om de pagina te openen https://aka.ms/devicelogin en voer de opgegeven code om te verifiëren.
- U wordt gevraagd tijdens het verificatieproces voor een account te verifiëren met. Belangrijk: Selecteer een account met voldoende machtigingen om resources te maken in het account en een geldige Azure-abonnement. Als het aanmelden voltooid is, wordt uw abonnementsgegevens wordt weergegeven en u wordt gevraagd of u wilt doorgaan met het geselecteerde account.

### <a name="environment-setup"></a>Instellen van de omgeving
Om het installatieproces start, moet u enkele omgeving providers registreren door te voeren van de volgende opdrachten:

```azurecli
az provider register -n Microsoft.MachineLearningCompute
az provider register -n Microsoft.ContainerRegistry
az provider register -n Microsoft.ContainerService
```
#### <a name="local-deployment"></a>Lokale implementatie
Als u wilt implementeren en testen van uw web-service op de lokale computer, instellen van een lokale omgeving met de volgende opdracht. Naam van de resourcegroep is optioneel.

```azurecli
az ml env setup -l [Azure Region, e.g. eastus2] -n [your environment name] [-g [existing resource group]]
```
>[!NOTE] 
>Lokale web service-implementatie moet u Docker installeren op de lokale computer. 
>

De lokale omgeving setup-opdracht maakt u de volgende bronnen in uw abonnement:
- Een resourcegroep (als niet wordt opgegeven of als de opgegeven naam niet bestaat)
- een opslagaccount
- Een Azure Container Registry (ACR)
- Een Application insights-account

Nadat setup voltooid is, stelt u de omgeving moet worden gebruikt met de volgende opdracht:

```azurecli
az ml env set -n [environment name] -g [resource group]
```

#### <a name="cluster-deployment"></a>Implementatie van het cluster
Implementatie van het Cluster voor hoge schaalbaarheid productiescenario's gebruiken. Deze stelt een ACS-cluster met Kubernetes als de orchestrator. De ACS-cluster kan worden uitgebreid voor het afhandelen van grotere doorvoer voor uw web-serviceaanroepen.

Als u wilt uw webservice implementeren in een productieomgeving, Stel eerst de omgeving met de volgende opdracht:

```azurecli
az ml env setup --cluster -n [your environment name] -l [Azure region e.g. eastus2] [-g [resource group]]
```

De installatieopdracht van de cluster-omgeving maakt de volgende bronnen in uw abonnement:
- Een resourcegroep (als niet wordt opgegeven of als de opgegeven naam niet bestaat)
- een opslagaccount
- Een Azure Container Registry (ACR)
- Een Kubernetes-implementatie in een Azure Container Service (ACS)-cluster
- Een Application insights-account

>[!IMPORTANT]
> Om te kunnen maken met succes een clusteromgeving, moet u bijdrager toegang op de Azure-abonnement of de resourcegroep worden hebben.

De resourcegroep, een opslagaccount en een ACR worden snel gemaakt. De ACS-implementatie kan maximaal 20 minuten duren. 

Als u wilt de status van een doorlopende clusterinrichting controleren, moet u de volgende opdracht gebruiken:

```azurecli
az ml env show -n [environment name] -g [resource group]
```

Nadat setup voltooid is, moet u de omgeving moet worden gebruikt voor deze implementatie in te stellen. Gebruik de volgende opdracht:

```azurecli
az ml env set -n [environment name] -g [resource group]
```

>[!NOTE] 
> Nadat de omgeving is gemaakt voor de volgende implementaties, moet u alleen gebruik van de bovenstaande set-opdracht te gebruiken.
>

### <a name="create-a-model-management-account"></a>Een Model-Management-Account maken
Een model-management-account is vereist voor het implementeren van modellen. U moet hiervoor eenmaal per abonnement, en dezelfde account in meerdere implementaties kunnen gebruiken.

Gebruik de volgende opdracht voor het maken van een nieuw account:

```azurecli
az ml account modelmanagement create -l [Azure region, e.g. eastus2] -n [your account name] -g [resource group name] --sku-instances [number of instances, e.g. 1] --sku-name [Pricing tier for example S1]
```

Voor het gebruik van een bestaand account, moet u de volgende opdracht gebruiken:
```azurecli
az ml account modelmanagement set -n [your account name] -g [resource group it was created in]
```

Als gevolg van dit proces de omgeving gereed is en het model-management-account is gemaakt om de functies die nodig zijn voor het beheren en implementeren van Machine Learning-modellen (Zie [Azure Machine Learning-Model Management](model-management-overview.md) voor een overzicht).

## <a name="next-steps"></a>Volgende stappen

* Voor instructies over het implementeren van web-services worden uitgevoerd op een lokale computer of een cluster blijven op [implementeren van een Machine Learning-Model als een webservice](model-management-service-deploy.md).
* Voer een van de vele voorbeelden in de galerie.
