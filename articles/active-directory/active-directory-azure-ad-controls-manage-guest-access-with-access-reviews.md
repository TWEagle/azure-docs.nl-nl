---
title: Toegang voor gasten met Azure AD beheren beoordelingen toegang | Microsoft Docs
description: Gastgebruikers als leden van een groep beheren of toegewezen aan een toepassing met Azure Active Directory toegang beoordelingen
services: active-directory
documentationcenter: ''
author: markwahl-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: 564f4f4a3f7532a7419e15b91fdbae9ee12088fd
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2018
---
# <a name="manage-guest-access-with-azure-ad-access-reviews"></a>Toegang voor gasten met Azure AD beheren beoordelingen openen


Met Azure Active Directory (Azure AD), kunt u eenvoudig inschakelen samenwerking buiten de grenzen van de organisatie met behulp van de [Azure AD B2B-functie](active-directory-b2b-what-is-azure-ad-b2b.md). Gastgebruikers van andere tenants kunnen worden [uitgenodigd door beheerders](active-directory-b2b-admin-add-users.md) of door [andere gebruikers](active-directory-b2b-how-it-works.md). Deze mogelijkheid geldt ook voor sociale identiteiten zoals Microsoft-accounts.

U kunt eenvoudig bovendien dat gastgebruikers juiste toegang hebben. Zichzelf of van een zakelijke besluitvormer om deel te nemen in een toegang controleren en opnieuw certificeren (of attest) voor toegang tot de de gasten, kunt u de gasten vragen. De beoordelaars kunnen op basis van suggesties uit Azure AD hun mening geven over de mate waarin een gebruiker per se toegang moet krijgen. Wanneer een onderzoek access is voltooid, kunt u wijzigingen aanbrengen en verwijderen van toegang voor gasten die niet langer nodig hebt.

> [!NOTE]
> Dit document richt zich op de beoordeling van gastgebruikers toegang. Als u wilt bekijken, toegang tot alle gebruikers, niet alleen gasten Zie [beheren van toegang voor gebruikers met toegang beoordelingen](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md). Als u wilt bekijken gebruikers lidmaatschap van administratieve rollen, zoals hoofdbeheerder, Zie [een revisie toegang starten in Azure AD Privileged Identity Management](active-directory-privileged-identity-management-how-to-start-security-review.md). 
>
>

## <a name="prerequisites"></a>Vereisten 

Toegangsbeoordelingen zijn beschikbaar met de Premium P2-editie van Azure AD, die deel uitmaakt van Microsoft Enterprise Mobility + Security, E5. Zie [Azure Active Directory-edities](active-directory-editions.md) voor meer informatie. Elke gebruiker die deze functie gebruikt - bijvoorbeeld voor het maken van een beoordeling, het openen van een beoordeling of het toepassen van een beoordeling - heeft een licentie nodig.

Als u van plan bent om het gastgebruikers vragen om hun eigen toegang controleren, leest u over licentieverlening van Gast-gebruiker. Zie voor meer informatie [licentieverlening van Azure AD B2B-samenwerking](active-directory-b2b-licensing.md).

## <a name="create-and-perform-an-access-review-for-guests"></a>Maken en uitvoeren van een controle van toegang voor gasten

Eerst toegang beoordelingen worden weergegeven op een revisor toegang panelen inschakelen. Ga als globale beheerder naar de [pagina Toegangsbeoordelingen](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/). 

Azure AD kunt verschillende scenario's voor de beoordeling van gastgebruikers.

Selecteer een van de volgende opties:

 - Een groep in Azure AD met een of meer gasten als leden.
 - Een toepassing is verbonden met Azure AD met een of meer gastgebruikers zijn toegewezen. 

U kunt vervolgens beslissen of elke gast om te bekijken van hun eigen toegang of voor een of meer gebruikers vragen om te bekijken van elke gast toegang te vragen.

 Deze scenario's worden in de volgende secties besproken.

### <a name="ask-guests-to-review-their-own-membership-in-a-group"></a>Vraag gasten om te controleren van hun eigen lidmaatschap in een groep

U kunt toegang beoordelingen gebruiken om ervoor te zorgen dat gebruikers die zijn uitgenodigd en toegevoegd aan een groep blijven toegang nodig. U kunt eenvoudig vragen gasten om te controleren van hun eigen lidmaatschap in die groep.

1. Selecteer de revisie alleen leden van Gast gebruiker en leden lees zelf op te nemen voor het starten van een controle van toegang voor de groep. Raadpleeg voor meer informatie [Create an access review](active-directory-azure-ad-controls-create-access-review.md) (Een toegangscontrole maken).

2. Vraag elke gast om te controleren van hun eigen lidmaatschap. Standaard ontvangt elke gast die een uitnodiging geaccepteerd een e-mailbericht van Azure AD met een koppeling naar de revisie toegang. Azure AD-instructies voor gasten heeft over de [hun toegang controleren](active-directory-azure-ad-controls-perform-access-review.md).

3. Wanneer alle beoordelaars feedback hebben gegeven, kunt u de toegangsbeoordeling stoppen en de wijzigingen toepassen. Raadpleeg voor meer informatie [Complete an access review](active-directory-azure-ad-controls-complete-access-review.md) (Een toegangscontrole voltooien).

4. Naast de gebruikers die hun eigen behoefte continue toegang geweigerd, kunt u ook gebruikers die heeft niet gereageerd verwijderen. Niet-reagerende gebruikers ontvangen mogelijk niet langer e-mail.

5. Als de groep niet is gebruikt voor toegangsbeheer, kunt u ook gebruikers die niet zijn geselecteerd voor deelname aan de revisie omdat ze niet de uitnodiging accepteren verwijderen. Accepteert geen kan erop wijzen dat e-mailadres van de uitgenodigde gebruiker had een typefout gemaakt. Als een groep wordt gebruikt als een distributielijst, zijn niet mogelijk enkele gastgebruikers geselecteerd om deel te nemen omdat ze contact op met objecten.

### <a name="ask-a-sponsor-to-review-a-guests-membership-in-a-group"></a>Vraag een sponsor om te controleren van een gast lidmaatschap in een groep

U kunt een sponsor, zoals de eigenaar van een groep te bekijken van een gast nodig voor voortdurende lidmaatschap in een groep op te vragen.

1. Selecteer de revisie Gast gebruiker alleen leden wilt opnemen voor het starten van een controle van toegang voor de groep. Geef vervolgens een of meer revisoren. Raadpleeg voor meer informatie [Create an access review](active-directory-azure-ad-controls-create-access-review.md) (Een toegangscontrole maken).

2. Vraag de beoordelaars feedback te geven. Standaard ontvangen ze allemaal een e-mail van Azure AD met een koppeling naar het toegangsdeelvenster waar ze [hun toegangsbeoordeling kunnen uitvoeren](active-directory-azure-ad-controls-perform-access-review.md).

3. Wanneer alle beoordelaars feedback hebben gegeven, kunt u de toegangsbeoordeling stoppen en de wijzigingen toepassen. Raadpleeg voor meer informatie [Complete an access review](active-directory-azure-ad-controls-complete-access-review.md) (Een toegangscontrole voltooien).

### <a name="ask-guests-to-review-their-own-access-to-an-application"></a>Vraag gasten hun eigen toegang tot een toepassing bekijken

U kunt toegang beoordelingen gebruiken om ervoor te zorgen dat gebruikers die zijn uitgenodigd voor een bepaalde toepassing blijven toegang nodig. U kunt eenvoudig vragen de gasten zelf te bekijken van hun eigen nodig om toegang te krijgen.

1. Selecteer de revisie alleen gasten en dat gebruikers hun eigen toegang controleren op te nemen voor het starten van een controle van toegang voor de toepassing. Raadpleeg voor meer informatie [Create an access review](active-directory-azure-ad-controls-create-access-review.md) (Een toegangscontrole maken).

2. Vraag elke Gast aan hun eigen toegang tot de toepassing bekijken. Standaard ontvangt elke gast die een uitnodiging geaccepteerd een e-mailbericht van Azure AD met een koppeling naar de revisie toegang in uw organisatie toegang Configuratiescherm. Azure AD-instructies voor gasten heeft over de [hun toegang controleren](active-directory-azure-ad-controls-perform-access-review.md).

3. Wanneer alle beoordelaars feedback hebben gegeven, kunt u de toegangsbeoordeling stoppen en de wijzigingen toepassen. Raadpleeg voor meer informatie [Complete an access review](active-directory-azure-ad-controls-complete-access-review.md) (Een toegangscontrole voltooien).

4. Naast de gebruikers die hun eigen geweigerd voor continue toegang nodig, ook kunt u gebruikers die heeft niet gereageerd. Niet-reagerende gebruikers ontvangen mogelijk niet langer e-mail. U kunt ook gebruikers die niet zijn geselecteerd voor deelname, vooral als ze zijn niet onlangs uitgenodigd verwijderen. Deze gebruikers niet geaccepteerd door de uitnodiging en kan dus geen toegang tot de toepassing. 

### <a name="ask-a-sponsor-to-review-a-guests-access-to-an-application"></a>Vraag een sponsor een gast toegang tot een toepassing bekijken

U kunt een sponsor, zoals de eigenaar van een toepassing, om te controleren van Gast nodig voor permanente toegang tot de toepassing op te vragen.

1. Selecteer de revisie gasten alleen opnemen voor het starten van een controle van toegang voor de toepassing. Geef vervolgens een of meer gebruikers als revisoren. Raadpleeg voor meer informatie [Create an access review](active-directory-azure-ad-controls-create-access-review.md) (Een toegangscontrole maken).

2. Vraag de beoordelaars feedback te geven. Standaard ontvangen ze allemaal een e-mail van Azure AD met een koppeling naar het toegangsdeelvenster waar ze [hun toegangsbeoordeling kunnen uitvoeren](active-directory-azure-ad-controls-perform-access-review.md).

3. Wanneer alle beoordelaars feedback hebben gegeven, kunt u de toegangsbeoordeling stoppen en de wijzigingen toepassen. Raadpleeg voor meer informatie [Complete an access review](active-directory-azure-ad-controls-complete-access-review.md) (Een toegangscontrole voltooien).

### <a name="ask-guests-to-review-their-need-for-access-in-general"></a>Vraag gasten hun nodig voor toegang, in het algemeen controleren

In sommige organisaties gasten mogelijk niet op de hoogte van hun groepslidmaatschappen.

> [!NOTE]
> Eerdere versies van de Azure-portal beheerderstoegang tot door gebruikers met de UserType Gast niet is toegestaan. In sommige gevallen kan een beheerder in uw directory mogelijk zijn gewijzigd van een gast UserType-waarde in lid met behulp van PowerShell. Als deze wijziging is eerder is opgetreden in uw directory, kan niet alle gebruikers die in het verleden administratieve toegangsrechten heeft bevatten in de vorige query. In dit geval moet u de Gast UserType Wijzig of handmatig de Gast opnemen in het groepslidmaatschap.

1. Maak een beveiligingsgroep in Azure AD met de gasten als leden, als een geschikte groep nog niet bestaat. U kunt bijvoorbeeld een groep maken met een handmatig handhaven lidmaatschap van gasten. Of u kunt een dynamische groep maken met een naam, zoals 'Gasten van Contoso' voor gebruikers in de Contoso-tenant met de waarde van het UserType-kenmerk van de Gast.  Voor efficiëntie, zorg ervoor dat de groep is voornamelijk gasten - Selecteer niet als u een groep met gebruikers die niet moeten worden gecontroleerd.

2. Selecteer de revisoren de leden zelf voor het starten van een controle van toegang voor de groep. Raadpleeg voor meer informatie [Create an access review](active-directory-azure-ad-controls-create-access-review.md) (Een toegangscontrole maken).

3. Vraag elke gast om te controleren van hun eigen lidmaatschap. Standaard ontvangt elke gast die een uitnodiging geaccepteerd een e-mailbericht van Azure AD met een koppeling naar de revisie toegang in uw organisatie toegang Configuratiescherm. Azure AD-instructies voor gasten heeft over de [hun toegang controleren](active-directory-azure-ad-controls-perform-access-review.md).  Deze gasten die hun uitnodiging niet geaccepteerd door de wordt weergegeven in de resultaten bekijken als 'Niet gewaarschuwd'.

4. Nadat de revisoren invoer geven, stopt u de controle van toegang. Raadpleeg voor meer informatie [Complete an access review](active-directory-azure-ad-controls-complete-access-review.md) (Een toegangscontrole voltooien).

5. De gasttoegang verwijderen voor gasten die zijn geweigerd, de controle niet hebt voltooid of hun uitnodiging is eerder niet geaccepteerd. Als sommige van de gasten contactpersonen die zijn geselecteerd zijn voor deelname aan het controleren of ze eerder een uitnodiging niet accepteren, kunt u hun account uitschakelen met behulp van de Azure-portal of PowerShell. Als de Gast wordt niet langer toegang nodig heeft en is niet een contactpersoon, kunt u hun gebruikersobject van uw directory verwijderen met behulp van de Azure-portal of PowerShell te verwijderen van het gebruikersobject Gast.

## <a name="next-steps"></a>Volgende stappen

[Een toegangsbeoordeling maken voor leden van een groep of toegang tot een toepassing](active-directory-azure-ad-controls-create-access-review.md)







