---
title: "Maken van een toepassingsgateway met SSL-beëindiging - Azure PowerShell | Microsoft Docs"
description: "Informatie over het maken van een toepassingsgateway en toevoegen van een certificaat voor SSL-beëindiging met Azure PowerShell."
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/25/2018
ms.author: davidmu
ms.openlocfilehash: 4972597e8e2db36be47c86b9aa1e592d94d4c2fe
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2018
---
# <a name="create-an-application-gateway-with-ssl-termination-using-azure-powershell"></a>Maken van een toepassingsgateway met SSL-beëindiging met Azure PowerShell

U kunt Azure PowerShell gebruiken voor het maken een [toepassingsgateway](application-gateway-introduction.md) met een certificaat voor [SSL-beëindiging](application-gateway-backend-ssl.md) die gebruikmaakt van een [virtuele-machineschaalset](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) voor back-endservers. In dit voorbeeld bevat de schaalaanpassingsset twee virtuele machine-exemplaren die zijn toegevoegd aan de standaardgroep voor back-end van de toepassingsgateway. 

In dit artikel leert u hoe:

> [!div class="checklist"]
> * Een zelfondertekend certificaat maken
> * Een netwerk instellen
> * Een toepassingsgateway maken met het certificaat
> * Een virtuele-machineschaalset ingesteld met het standaard back-end-adresgroep maken

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

Voor deze zelfstudie is moduleversie 3,6 of hoger van Azure PowerShell vereist. Voer `Get-Module -ListAvailable AzureRM` uit om de versie te bekijken. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-azurerm-ps). Als u PowerShell lokaal uitvoert, moet u ook `Login-AzureRmAccount` uitvoeren om verbinding te kunnen maken met Azure.

## <a name="create-a-self-signed-certificate"></a>Een zelfondertekend certificaat maken

Voor gebruik in productieomgevingen, moet u een geldig certificaat dat is ondertekend door een vertrouwde provider importeren. Voor deze zelfstudie maakt u een zelfondertekend certificaat met [nieuw SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate). U kunt [Export PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate) met de vingerafdruk die is geretourneerd voor het exporteren van een pfx-bestand van het certificaat.

```powershell
New-SelfSignedCertificate `
  -certstorelocation cert:\localmachine\my `
  -dnsname www.contoso.com
```

U ziet er ongeveer zo dit resultaat:

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com
```

Gebruik de vingerafdruk van het pfx-bestand te maken:

```powershell
$pwd = ConvertTo-SecureString -String "Azure123456!" -Force -AsPlainText
Export-PfxCertificate `
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 `
  -FilePath c:\appgwcert.pfx `
  -Password $pwd
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. Maak een Azure-resourcegroep met de naam *myResourceGroupAG* met [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). 

```powershell
New-AzureRmResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>Maken van netwerkbronnen

Configureer de subnetten met de naam *myBackendSubnet* en *myAGSubnet* met [nieuw AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). Maken van het virtuele netwerk met de naam *myVNet* met [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) met de subnetconfiguraties. En maak ten slotte het openbare IP-adres met de naam *myAGPublicIPAddress* met [nieuw AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress). Deze resources worden gebruikt om de netwerkverbinding met de toepassingsgateway en de bijbehorende bronnen opgeven.

```powershell
$backendSubnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.1.0/24
$agSubnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.2.0/24
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $backendSubnetConfig, $agSubnetConfig
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Dynamic
```

## <a name="create-an-application-gateway"></a>Een toepassingsgateway maken

### <a name="create-the-ip-configurations-and-frontend-port"></a>De IP-configuraties en de front-endpoort maken

Koppelen *myAGSubnet* die u eerder hebt gemaakt met de application gateway via [nieuw AzureRmApplicationGatewayIPConfiguration](/powershell/module/azurerm.network/new-azurermapplicationgatewayipconfiguration). Wijs *myAGPublicIPAddress* met de application gateway via [nieuw AzureRmApplicationGatewayFrontendIPConfig](/powershell/module/azurerm.network/new-azurermapplicationgatewayfrontendipconfig).

```powershell
$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet
$subnet=$vnet.Subnets[0]
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration `
  -Name myAGIPConfig `
  -Subnet $subnet
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -PublicIPAddress $pip
$frontendport = New-AzureRmApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -Port 443
```

### <a name="create-the-backend-pool-and-settings"></a>Maak de back-endpool en -instellingen

Maken van de back-endpool met de naam *appGatewayBackendPool* voor de application gateway met [nieuw AzureRmApplicationGatewayBackendAddressPool](/powershell/module/azurerm.network/new-azurermapplicationgatewaybackendaddresspool). Configureer de instellingen voor de back-end-pool met [nieuw AzureRmApplicationGatewayBackendHttpSettings](/powershell/module/azurerm.network/new-azurermapplicationgatewaybackendhttpsettings).

```powershell
$defaultPool = New-AzureRmApplicationGatewayBackendAddressPool `
  -Name appGatewayBackendPool 
$poolSettings = New-AzureRmApplicationGatewayBackendHttpSettings `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-default-listener-and-rule"></a>De standaard-listener en regel maken

Een listener is vereist voor het inschakelen van de toepassingsgateway voor het verkeer op de juiste wijze te routeren naar de back-endpool. In dit voorbeeld maakt u een basis-listener naar HTTPS-verkeer bij de basis-URL luistert. 

Maak een certificaat object met [nieuw AzureRmApplicationGatewaySslCertificate](/powershell/module/azurerm.network/new-azurermapplicationgatewaysslcertificate) en maak vervolgens een listener met de naam *mydefaultListener* met [ Nieuwe AzureRmApplicationGatewayHttpListener](/powershell/module/azurerm.network/new-azurermapplicationgatewayhttplistener) met frontend configuratie, de frontend-poort en het certificaat dat u eerder hebt gemaakt. Een regel is vereist voor de listener te weten welke back-endpool moet worden gebruikt voor binnenkomend verkeer. Maken van een eenvoudige regel met naam *rule1* met [nieuw AzureRmApplicationGatewayRequestRoutingRule](/powershell/module/azurerm.network/new-azurermapplicationgatewayrequestroutingrule).

```powershell
$pwd = ConvertTo-SecureString `
  -String "Azure123456!" `
  -Force `
  -AsPlainText
$cert = New-AzureRmApplicationGatewaySslCertificate `
  -Name "appgwcert" `
  -CertificateFile "c:\appgwcert.pfx" `
  -Password $pwd
$defaultlistener = New-AzureRmApplicationGatewayHttpListener `
  -Name mydefaultListener `
  -Protocol Https `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport `
  -SslCertificate $cert
$frontendRule = New-AzureRmApplicationGatewayRequestRoutingRule `
  -Name rule1 `
  -RuleType Basic `
  -HttpListener $defaultlistener `
  -BackendAddressPool $defaultPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway-with-the-certificate"></a>De toepassingsgateway maken met het certificaat

Nu dat u de benodigde ondersteunende resources hebt gemaakt, Geef parameters op voor de toepassingsgateway met de naam *myAppGateway* met [nieuw AzureRmApplicationGatewaySku](/powershell/module/azurerm.network/new-azurermapplicationgatewaysku), en maak vervolgens met behulp van [ Nieuwe-AzureRmApplicationGateway](/powershell/module/azurerm.network/new-azurermapplicationgateway) met het certificaat.

### <a name="create-the-application-gateway"></a>De toepassingsgateway maken

```azurepowershell-interactive
$sku = New-AzureRmApplicationGatewaySku `
  -Name Standard_Medium `
  -Tier Standard `
  -Capacity 2
$appgw = New-AzureRmApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $defaultPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendport `
  -HttpListeners $defaultlistener `
  -RequestRoutingRules $frontendRule `
  -Sku $sku `
  -SslCertificates $cert
```

## <a name="create-a-virtual-machine-scale-set"></a>Maken van een virtuele-machineschaalset

In dit voorbeeld maakt u een virtuele-machineschaalset ingesteld voor het uitvoeren van servers voor de back endpool in de toepassingsgateway. U de schaal is ingesteld op de back-endpool wanneer u de IP-instellingen configureren.

```azurepowershell-interactive
$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet
$appgw = Get-AzureRmApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway
$backendPool = Get-AzureRmApplicationGatewayBackendAddressPool `
  -Name appGatewayBackendPool `
  -ApplicationGateway $appgw
$ipConfig = New-AzureRmVmssIpConfig `
  -Name myVmssIPConfig `
  -SubnetId $vnet.Subnets[1].Id `
  -ApplicationGatewayBackendAddressPoolsId $backendPool.Id
$vmssConfig = New-AzureRmVmssConfig `
  -Location eastus `
  -SkuCapacity 2 `
  -SkuName Standard_DS2 `
  -UpgradePolicyMode Automatic
Set-AzureRmVmssStorageProfile $vmssConfig `
  -ImageReferencePublisher MicrosoftWindowsServer `
  -ImageReferenceOffer WindowsServer `
  -ImageReferenceSku 2016-Datacenter `
  -ImageReferenceVersion latest
Set-AzureRmVmssOsProfile $vmssConfig `
  -AdminUsername azureuser `
  -AdminPassword "Azure123456!" `
  -ComputerNamePrefix myvmss
Add-AzureRmVmssNetworkInterfaceConfiguration `
  -VirtualMachineScaleSet $vmssConfig `
  -Name myVmssNetConfig `
  -Primary $true `
  -IPConfiguration $ipConfig
New-AzureRmVmss `
  -ResourceGroupName myResourceGroupAG `
  -Name myvmss `
  -VirtualMachineScaleSet $vmssConfig
```

### <a name="install-iis"></a>IIS installeren

```azurepowershell-interactive
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/davidmu1/samplescripts/master/appgatewayurl.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
$vmss = Get-AzureRmVmss -ResourceGroupName myResourceGroupAG -VMScaleSetName myvmss
Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $publicSettings
Update-AzureRmVmss `
  -ResourceGroupName myResourceGroupAG `
  -Name myvmss `
  -VirtualMachineScaleSet $vmss
```

## <a name="test-the-application-gateway"></a>Testen van de toepassingsgateway

U kunt [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) ophalen van het openbare IP-adres van de toepassingsgateway. Het openbare IP-adres Kopieer en plak deze in de adresbalk van uw browser.

```azurepowershell-interactive
Get-AzureRmPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Waarschuwing beveiligen](./media/application-gateway-ssl-arm/application-gateway-secure.png)

Voor het accepteren van de beveiligingswaarschuwing als u een zelfondertekend certificaat gebruikt, selecteert u **Details** en vervolgens **gaat u naar de webpagina**. Uw beveiligde IIS-website wordt weergegeven zoals in het volgende voorbeeld:

![Basis-URL te testen in de toepassingsgateway](./media/application-gateway-ssl-arm/application-gateway-iistest.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Een zelfondertekend certificaat maken
> * Een netwerk instellen
> * Een toepassingsgateway maken met het certificaat
> * Een virtuele-machineschaalset ingesteld met het standaard back-end-adresgroep maken

Blijven de artikelen voor meer informatie over Toepassingsgateways en de bijbehorende resources.