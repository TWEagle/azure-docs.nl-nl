---
title: Beveiligde Azure VPN-gateway RADIUS-verificatie met NPS-server voor meervoudige verificatie | Microsoft-Docs
description: Beschrijft het integreren van Azure-gateway RADIUS-verificatie met NPS-server voor multi-factor Authentication.
services: vpn-gateway
documentationcenter: na
author: ahmadnyasin
manager: willchen
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: 
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/13/2018
ms.author: genli
ms.openlocfilehash: f0d95cc0dabb253a72afdbc1bc518df882c4d861
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2018
---
# <a name="integrate-azure-vpn-gateway-radius-authentication-with-nps-server-for-multi-factor-authentication"></a>Integratie van Azure VPN-gateway RADIUS-verificatie met NPS-server voor multi-factor Authentication 

Het artikel wordt beschreven hoe Network Policy Server (NPS) integreren met Azure VPN-gateway RADIUS-verificatie voor het leveren van multi-factor Authentication (MFA) voor punt-naar-site VPN-verbindingen. 

## <a name="prerequisite"></a>Vereiste

De gebruikers moeten zich voor het inschakelen van MFA in Azure Active Directory (Azure AD), die moet worden gesynchroniseerd vanuit de on-premises of cloud-omgeving. Ook moet de gebruiker al hebt voltooid het proces voor automatische inschrijving voor MFA.  Zie voor meer informatie [Mijn account voor verificatie in twee stappen instellen](../multi-factor-authentication/end-user/multi-factor-authentication-end-user-first-time.md)

## <a name="detailed-steps"></a>Gedetailleerde stappen

### <a name="step-1-create-a-virtual-network-gateway"></a>Stap 1: Een virtuele netwerkgateway maken

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer in het virtuele netwerk die als host voor de virtuele netwerkgateway fungeert, **subnetten**, en selecteer vervolgens **gatewaysubnet** om een subnet te maken. 

    ![De afbeelding over het toevoegen van gatewaysubnet](./media/vpn-gateway-radiuis-mfa-nsp/gateway-subnet.png)
3. Maak een virtuele netwerkgateway door te geven van de volgende instellingen:

    - **Gatewaytype**: selecteer **VPN**.
    - **VPN-type**: Selecteer **op Route gebaseerde**.
    - **SKU**: Selecteer een SKU-type op basis van uw vereisten.
    - **Virtueel netwerk**: Selecteer het virtuele netwerk waarin u het gatewaysubnet gemaakt.

        ![De afbeelding over gateway-instellingen voor virtueel netwerk](./media/vpn-gateway-radiuis-mfa-nsp/create-vpn-gateway.png)


 
### <a name="step-2-configure-the-nps-for-azure-mfa"></a>Stap 2 de NPS configureren voor Azure MFA

1. Op de NPS-server [installeren van de NPS-extensie voor Azure MFA](../multi-factor-authentication/multi-factor-authentication-nps-extension.md#install-the-nps-extension).
2. Open de console NSP, met de rechtermuisknop op **RADUIS Clients**, en selecteer vervolgens **nieuw**. De client RADUIS maken door op te geven van de volgende instellingen:

    - **Beschrijvende naam**: Typ een naam.
    - **Adres (IP of DNS)**: Typ het gatewaysubnet dat u in stap 1 hebt gemaakt.
    - **Gedeeld geheim**: Typ een geheime sleutel en onthouden voor later gebruik.

    ![De afbeelding over RADUIS clientinstellingen](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client1.png)

 
3.  Op de **Geavanceerd** tabblad, stelt u de naam van de leverancier op **RADIUS-standaard** en zorg ervoor dat de **extra opties** selectievakje niet is ingeschakeld.

    ![De afbeelding over geavanceerde clientinstellingen RADUIS](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client2.png)

4. Ga naar **beleid** > **netwerkbeleid**, dubbelklikt u op **verbindingen met Microsoft Routing and Remote Access server** beleid, selecteer  **Toegang verlenen**, en klik vervolgens op **OK**.

### <a name="step-3-configure-the-virtual-network-gateway"></a>Stap 3-Configureer de virtuele netwerkgateway

1. Meld u aan bij [Azure-portal](https://portal.azure.com).
2. Open de virtuele netwerkgateway die u hebt gemaakt. Zorg ervoor dat het Gatewaytype is ingesteld op **VPN** is en dat het VPN-type **op route gebaseerde**.
3. Klik op **wijs siteconfiguratie** > **nu configureren**, en geef vervolgens de volgende instellingen:

    - **-Adresgroep**: Typ het gatewaysubnet dat u in de stap 1 hebt gemaakt.
    - **Verificatietype**: Selecteer **RADIUS-verificatie**.
    - **Het IP-adres**: het IP-adres van de NPS-server.

    ![De afbeelding over dat site-instellingen](./media/vpn-gateway-radiuis-mfa-nsp/configure-p2s.png)

## <a name="next-steps"></a>Volgende stappen

- [Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md)
- [Uw bestaande NPS-infrastructuur integreren met Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication-nps-extension.md)
