---
title: Een webserver met SSL-certificaten in Azure beveiligen | Microsoft Docs
description: Meer informatie over het beveiligen van de NGINX-webserver met SSL-certificaten op een Linux-VM in Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/14/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 02118533c4ab552f81157f644bb794e68fbc4ce3
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/09/2018
---
# <a name="secure-a-web-server-with-ssl-certificates-on-a-linux-virtual-machine-in-azure"></a>Een webserver met SSL-certificaten op een virtuele Linux-machine in Azure beveiligen
Om webservers te beveiligen, kan een Secure Sockets Layer (SSL)-certificaat worden gebruikt voor het versleutelen van internetverkeer. Deze SSL-certificaten kunnen worden opgeslagen in Azure Key Vault en beveiligde implementaties van certificaten aan virtuele Linux-machines (VM's) in Azure toestaan. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een Azure Key Vault maken
> * Een certificaat genereren of uploaden naar de Key Vault
> * Een virtuele machine maken en de NGINX-webserver installeren
> * Het certificaat invoeren in de virtuele machine en NGINX configureren met een SSL-binding

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor deze zelfstudie Azure CLI 2.0.22 of later uitvoeren. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren]( /cli/azure/install-azure-cli).  


## <a name="overview"></a>Overzicht
Azure Key Vault beschermt cryptografische sleutels en geheimen, zoals certificaten of wachtwoorden. Key Vault helpt het beheerproces voor certificaten te stroomlijnen en zorgt dat u de controle houdt over de sleutels waarmee deze certificaten toegankelijk zijn. U kunt een zelfondertekend certificaat in Key Vault maken of een bestaand, vertrouwd certificaat dat u al bezit uploaden.

In plaats van met een aangepaste VM-installatiekopie die standaard certificaten bevat, kunt u certificaten invoeren in een actieve virtuele machine. Dit proces zorgt ervoor dat de meest recente certificaten tijdens de implementatie op een webserver zijn geïnstalleerd. Als u een certificaat wilt vernieuwen of vervangen, hoeft u niet ook een nieuwe aangepaste VM-installatiekopie te maken. De meest recente certificaten worden automatisch toegevoegd als u extra virtuele machines maakt. Tijdens het hele proces verlaten de certificaten nooit het Azure-platform of zijn ze blootgesteld in een script, opdrachtregelgeschiedenis of sjabloon.


## <a name="create-an-azure-key-vault"></a>Een Azure Key Vault maken
Voordat u een Key Vault en certificaten kunt maken, moet u eerst een resourcegroep maken met [az group create](/cli/azure/group#az_group_create). In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroupSecureWeb* gemaakt op de locatie *VS Oost*:

```azurecli-interactive 
az group create --name myResourceGroupSecureWeb --location eastus
```

Maak vervolgens een Key Vault met [Az keyvault maken](/cli/azure/keyvault#az_keyvault_create) en schakel deze in voor gebruik wanneer u een virtuele machine implementeert. Elke Key Vault moet een unieke naam hebben van alleen kleine letters. Vervang  *<mykeyvault>*  in het volgende voorbeeld met de naam van uw eigen unieke Key Vault:

```azurecli-interactive 
keyvault_name=<mykeyvault>
az keyvault create \
    --resource-group myResourceGroupSecureWeb \
    --name $keyvault_name \
    --enabled-for-deployment
```

## <a name="generate-a-certificate-and-store-in-key-vault"></a>Een certificaat genereren en opslaan in Key Vault
Voor gebruik in de productie, moet u een geldig certificaat importeren dat is ondertekend door een vertrouwde provider met [az keyvault certificate import](/cli/azure/keyvault/certificate#az_keyvault_certificate_import). Voor deze zelfstudie toont het volgende voorbeeld hoe kunt u een zelfondertekend certificaat kunt genereren met [az keyvault certificate create](/cli/azure/keyvault/certificate#az_keyvault_certificate_create) dat gebruikmaakt van het standaardbeleid voor certificaten:

```azurecli-interactive 
az keyvault certificate create \
    --vault-name $keyvault_name \
    --name mycert \
    --policy "$(az keyvault certificate get-default-policy)"
```

### <a name="prepare-a-certificate-for-use-with-a-vm"></a>Een certificaat voor gebruik met een virtuele machine voorbereiden
Om het certificaat te gebruiken tijdens het creatieproces van de virtuele machine, verkrijgen de ID van het certificaat met [az keyvault secret list-versions](/cli/azure/keyvault/secret#az_keyvault_secret_list_versions). Converteren van het certificaat met [az vm format-secret](/cli/azure/vm#az_vm_format_secret). Het volgende voorbeeld wijst de uitvoer van deze opdrachten toe aan variabelen voor eenvoudig gebruik in de volgende stappen:

```azurecli-interactive 
secret=$(az keyvault secret list-versions \
          --vault-name $keyvault_name \
          --name mycert \
          --query "[?attributes.enabled].id" --output tsv)
vm_secret=$(az vm format-secret --secret "$secret")
```

### <a name="create-a-cloud-init-config-to-secure-nginx"></a>Maak een cloud-init-config om NGINX te beveiligen
[Cloud-init](https://cloudinit.readthedocs.io) is een veelgebruikte benadering voor het aanpassen van een Linux-VM als deze voor de eerste keer wordt opgestart. U kunt cloud-init gebruiken voor het installeren van pakketten en schrijven van bestanden, of om gebruikers en beveiliging te configureren. Als de initialisatie van de cloud-init wordt uitgevoerd tijdens het opstartproces, zijn er geen extra stappen of agents vereist om uw configuratie toe te passen.

Wanneer u een virtuele machine maakt, worden certificaten en sleutels opgeslagen in de beveiligde */var/lib/waagent/* directory. Gebruik cloud-init voor het automatiseren van het toevoegen van het certificaat aan de virtuele machine en het configureren van de webserver. In dit voorbeeld installeert en configureert u de NGINX-webserver. U kunt hetzelfde proces gebruiken voor het installeren en configureren van Apache. 

Maak een bestand met de naam *cloud-init-web-server.txt* en plak de volgende configuratie:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 443 ssl;
        ssl_certificate /etc/nginx/ssl/mycert.cert;
        ssl_certificate_key /etc/nginx/ssl/mycert.prv;
      }
runcmd:
  - secretsname=$(find /var/lib/waagent/ -name "*.prv" | cut -c -57)
  - mkdir /etc/nginx/ssl
  - cp $secretsname.crt /etc/nginx/ssl/mycert.cert
  - cp $secretsname.prv /etc/nginx/ssl/mycert.prv
  - service nginx restart
```

### <a name="create-a-secure-vm"></a>Een beveiligde virtuele machine maken
Maak een virtuele machine met [az vm create](/cli/azure/vm#az_vm_create). De gegevens van het certificaat worden geïnjecteerd uit de Sleutelkluis met de parameter `--secrets`. U kunt de cloud init-configuratie doorgeven met de parameter `--custom-data`:

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroupSecureWeb \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init-web-server.txt \
    --secrets "$vm_secret"
```

Het duurt enkele minuten voordat de virtuele machine wordt gemaakt, de pakketten worden geïnstalleerd en de app gestart. Wanneer de virtuele machine is gemaakt, let op de `publicIpAddress` weergegeven door de Azure CLI. Dit adres wordt gebruikt voor toegang tot uw site in een webbrowser.

Zodat beveiligde webverkeer uw virtuele machine bereiken kan, open poort 443 vanaf het Internet met [az vm open-port](/cli/azure/vm#az_vm_open_port):

```azurecli-interactive 
az vm open-port \
    --resource-group myResourceGroupSecureWeb \
    --name myVM \
    --port 443
```


### <a name="test-the-secure-web-app"></a>Testen van de beveiligde web-app
Nu kunt u een webbrowser openen en *https://<publicIpAddress>*  in de adresbalk invoeren. Geef uw eigen openbare IP-adres op uit het creatieproces van de virtuele machine proces. Als u een zelfondertekend certificaat gebruikt, aanvaardt u de beveiligingswaarschuwing:

![Beveiligingswaarschuwing voor web browser accepteren](./media/tutorial-secure-web-server/browser-warning.png)

Uw beveiligde NGINX-site wordt vervolgens weergegeven zoals in het volgende voorbeeld:

![Beveiligde actieve NGINX-site weergeven](./media/tutorial-secure-web-server/secured-nginx.png)


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u een NGINX-webserver beveiligd met een SSL-certificaat dat is opgeslagen in Azure Key Vault. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Een Azure Key Vault maken
> * Een certificaat genereren of uploaden naar de Key Vault
> * Een virtuele machine maken en de NGINX-webserver installeren
> * Het certificaat invoeren in de virtuele machine en NGINX configureren met een SSL-binding

Volg deze link om voorbeelden te zien van vooraf gemaakte virtuele machinescripts.

> [!div class="nextstepaction"]
> [Voorbeelden van virtuele Linux-machines](./cli-samples.md)

