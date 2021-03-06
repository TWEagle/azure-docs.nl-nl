---
title: Zelfstudie voor Azure Container Instances - App implementeren
description: Zelfstudie voor Azure Container Instances deel 3 van 3 - App implementeren
services: container-instances
author: mmacy
manager: timlt
ms.service: container-instances
ms.topic: tutorial
ms.date: 03/21/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 29d7114f288f7387d0c7cd5c6afe2eaaa7a8c560
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2018
---
# <a name="tutorial-deploy-a-container-to-azure-container-instances"></a>Zelfstudie: een container implementeren in Azure Container Instances

Dit is de laatste zelfstudie in een reeks van drie. U hebt eerder in de reeks [een containerinstallatiekopie gemaakt](container-instances-tutorial-prepare-app.md) en [gepusht naar Azure Container Registry](container-instances-tutorial-prepare-acr.md). In dit artikel wordt de reeks afgerond door de container te implementeren in Azure Container Instances.

In deze zelfstudie hebt u:

> [!div class="checklist"]
> * De Azure Container Registry-container implementeren in Azure Container Instances
> * De actieve toepassing weergeven in de browser
> * De logboeken van de container weergeven

## <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [container-instances-tutorial-prerequisites](../../includes/container-instances-tutorial-prerequisites.md)]

## <a name="deploy-the-container-using-the-azure-cli"></a>De container implementeren met behulp van de Azure CLI

In deze sectie gebruikt u Azure CLI om de installatiekopie te implementeren die u hebt gebouwd in de [eerste zelfstudie](container-instances-tutorial-prepare-app.md) en die u in de [tweede zelfstudie](container-instances-tutorial-prepare-acr.md) naar Azure Container Registry hebt gepusht. Zorg ervoor dat u deze zelfstudies hebt voltooid voordat u verdergaat.

### <a name="get-registry-credentials"></a>Registerreferenties ophalen

Wanneer u een installatiekopie implementeert die in een privécontainerregister wordt gehost, zoals de installatiekopie die u hebt gemaakt in de [tweede zelfstudie](container-instances-tutorial-prepare-acr.md), moet u de referenties van het register opgeven.

Haal eerst de volledige naam op van de aanmeldingsserver van het containerregister (vervang `<acrName>` door de naam van het register):

```azurecli
az acr show --name <acrName> --query loginServer
```

Haal vervolgens het wachtwoord van het containerregister op:

```azurecli
az acr credential show --name <acrName> --query "passwords[0].value"
```

### <a name="deploy-container"></a>Container implementeren

Gebruik nu de opdracht [az container create][az-container-create] om de container te implementeren. Vervang `<acrLoginServer>` en `<acrPassword>` door de waarden die u hebt opgehaald met de vorige twee opdrachten. Vervang `<acrName>` door de naam van het containerregister.

```azurecli
az container create --resource-group myResourceGroup --name aci-tutorial-app --image <acrLoginServer>/aci-tutorial-app:v1 --cpu 1 --memory 1 --registry-username <acrName> --registry-password <acrPassword> --dns-name-label aci-demo --ports 80
```

U ontvangt binnen enkele seconden een eerste reactie van Azure. De waarde `--dns-name-label` moet uniek zijn voor de Azure-regio waar u de containerinstallatiekopie maakt. Wijzig de waarde in de vorige opdracht als u een foutbericht ontvangt over het **DNS-naamlabel** wanneer u de opdracht uitvoert.

### <a name="verify-deployment-progress"></a>Voortgang van implementatie verifiëren

Gebruik [az container show][az-container-show] om de status van de implementatie te bekijken:

```azurecli
az container show --resource-group myResourceGroup --name aci-tutorial-app --query instanceView.state
```

Herhaal de opdracht [az container show][az-container-show] totdat de status verandert van *In behandeling* in *Wordt uitgevoerd*. Als het goed is, duurt dit minder dan een minuut. Wanneer de container de status *Wordt uitgevoerd* heeft, gaat u verder met de volgende stap.

## <a name="view-the-application-and-container-logs"></a>De toepassings- en containerlogboeken bekijken

Wanneer de implementatie is geslaagd, geeft u de volledig gekwalificeerde domeinnaam (FQDN) van de container weer met de opdracht [az container show][az-container-show]:

```bash
az container show --resource-group myResourceGroup --name aci-tutorial-app --query ipAddress.fqdn
```

Bijvoorbeeld:
```console
$ az container show --resource-group myResourceGroup --name aci-tutorial-app --query ipAddress.fqdn
"aci-demo.eastus.azurecontainer.io"
```

Als u de actieve toepassing wilt bekijken, navigeert u in uw browser naar de weergegeven DNS-naam:

![Hello world-app in browser][aci-app-browser]

U kunt ook de logboekuitvoer van de container bekijken:

```azurecli
az container logs --resource-group myResourceGroup --name aci-tutorial-app
```

Voorbeelduitvoer:

```bash
$ az container logs --resource-group myResourceGroup --name aci-tutorial-app
listening on port 80
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://aci-demo.eastus.azurecontainer.io/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resources die u in de zelfstudiereeks hebt gemaakt, niet langer nodig hebt, kunt u de opdracht [az group delete][az-group-delete] uitvoeren om de resourcegroep en alle resources daarin te verwijderen. Met deze opdracht verwijdert u het containerregister dat u hebt gemaakt, evenals de actieve container en alle gerelateerde resources.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het proces voor het implementeren van uw container naar Azure Container Instances voltooid. De volgende stappen zijn voltooid:

> [!div class="checklist"]
> * De container van Azure Container Registry implementeren met behulp van Azure CLI
> * De toepassing in de browser bekijken
> * De containerlogboeken ophalen

Nu u de basisprincipes onder de knie hebt, kunt u meer leren over Azure Container Instances, bijvoorbeeld hoe containergroepen werken:

> [!div class="nextstepaction"]
> [Containergroepen in Azure Container Instances](container-instances-container-groups.md)

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-container-show]: /cli/azure/container#az_container_show
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli-install]: /cli/azure/install-azure-cli
[prepare-app]: ./container-instances-tutorial-prepare-app.md
