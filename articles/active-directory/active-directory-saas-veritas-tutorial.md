---
title: 'Zelfstudie: Azure Active Directory-integratie met eenmalige aanmelding Veritas Enterprise Vault.cloud | Microsoft Docs'
description: Informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en eenmalige aanmelding Veritas Enterprise Vault.cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: c47894b1-f5df-4755-845d-f12f4c602dc4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2017
ms.author: jeedes
ms.openlocfilehash: eb9243367d0817d37549fa147c6c5e1d2acf3761
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/01/2018
---
# <a name="tutorial-azure-active-directory-integration-with-veritas-enterprise-vaultcloud-sso"></a>Zelfstudie: Azure Active Directory-integratie met Veritas Enterprise Vault.cloud SSO

In deze zelfstudie leert u hoe Veritas Enterprise Vault.cloud SSO integreren met Azure Active Directory (Azure AD).

Veritas Enterprise Vault.cloud SSO integreren met Azure AD biedt de volgende voordelen:

- U kunt beheren in Azure AD die toegang tot Veritas Enterprise Vault.cloud SSO heeft
- U kunt uw gebruikers automatisch ophalen aangemeld bij Veritas Enterprise Vault.cloud SSO (Single Sign-On) inschakelen met hun Azure AD-accounts
- U kunt uw accounts op één centrale locatie - en de Azure-portal beheren

Als u weten van meer informatie over de integratie van de SaaS-app met Azure AD wilt, Zie [wat is er toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Vereisten

Voor het configureren van Azure AD-integratie met Veritas Enterprise Vault.cloud SSO, moet u de volgende items:

- Een Azure AD-abonnement
- Een Veritas Enterprise Vault.cloud SSO eenmalige aanmelding ingeschakeld abonnement

> [!NOTE]
> Test de stappen in deze zelfstudie, raden we niet met behulp van een productieomgeving.

Test de stappen in deze zelfstudie, moet u deze aanbevelingen volgen:

- Gebruik niet uw productieomgeving, tenzij het noodzakelijk is.
- Als u geen een proefabonnement Azure AD-omgeving hebt, kunt u een proefversie van één maand [hier](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeschrijving
In deze zelfstudie test u Azure AD eenmalige aanmelding in een testomgeving. Het scenario in deze zelfstudie bestaat uit twee belangrijkste bouwstenen:

1. Veritas Enterprise Vault.cloud SSO uit de galerie toevoegen
2. Configureren en testen van Azure AD eenmalige aanmelding

## <a name="adding-veritas-enterprise-vaultcloud-sso-from-the-gallery"></a>Veritas Enterprise Vault.cloud SSO uit de galerie toevoegen
Voor het configureren van de integratie van Veritas Enterprise Vault.cloud SSO in Azure AD, moet u Veritas Enterprise Vault.cloud SSO uit de galerie toevoegen aan de lijst met beheerde SaaS-apps.

**Als u wilt toevoegen Veritas Enterprise Vault.cloud SSO uit de galerie, moet u de volgende stappen uitvoeren:**

1. In de  **[Azure-portal](https://portal.azure.com)**, klik in het linkernavigatievenster op **Azure Active Directory** pictogram. 

    ![Active Directory][1]

2. Navigeer naar **bedrijfstoepassingen**. Ga vervolgens naar **alle toepassingen**.

    ![Toepassingen][2]
    
3. Om de nieuwe toepassing toevoegen, klikt u op **nieuwe toepassing** knop boven aan het dialoogvenster.

    ![Toepassingen][3]

4. Typ in het zoekvak **Veritas Enterprise Vault.cloud SSO**.

    ![Een Azure AD-testgebruiker maken](./media/active-directory-saas-veritas-tutorial/tutorial_veritas_search.png)

5. Selecteer in het deelvenster resultaten **Veritas Enterprise Vault.cloud SSO**, en klik vervolgens op **toevoegen** om toe te voegen van de toepassing.

    ![Een Azure AD-testgebruiker maken](./media/active-directory-saas-veritas-tutorial/tutorial_veritas_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configureren en testen van Azure AD eenmalige aanmelding
In deze sectie configureert en test eenmalige aanmelding Azure AD met Veritas Enterprise Vault.cloud eenmalige aanmelding op basis van een testgebruiker 'Britta Simon' genoemd.

Voor eenmalige aanmelding werkt, moet Azure AD weten wat de gebruiker equivalent in Veritas Enterprise Vault.cloud eenmalige aanmelding is voor een gebruiker in Azure AD. Met andere woorden, moet een koppeling relatie tussen een Azure AD-gebruiker en de betreffende gebruiker in Veritas Enterprise Vault.cloud eenmalige aanmelding tot stand worden gebracht.

Wijs in Veritas Enterprise Vault.cloud SSO, de waarde van de **gebruikersnaam** in Azure AD als de waarde van de **gebruikersnaam** de relatie van de koppeling tot stand brengen.

Om te configureren en testen van Azure AD eenmalige aanmelding met Veritas Enterprise Vault.cloud SSO, moet u de volgende bouwstenen voltooien:

1. **[Configureren van Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  : als u wilt dat uw gebruikers kunnen deze functie gebruiken.
2. **[Maken van een Azure AD-testgebruiker](#creating-an-azure-ad-test-user)**  - voor het testen van Azure AD eenmalige aanmelding met Britta Simon.
3. **[Maken van een testgebruiker Veritas Enterprise Vault.cloud SSO](#creating-a-veritas-enterprise-vaultcloud-sso-test-user)**  - Veritas Enterprise Vault.cloud eenmalige aanmelding die is gekoppeld aan de Azure AD-weergave van de gebruiker van een exemplaar van Britta Simon bevatten.
4. **[Toewijzen van de Azure AD-testgebruiker](#assigning-the-azure-ad-test-user)**  - Britta Simon gebruik van Azure AD eenmalige aanmelding inschakelen.
5. **[Testen van eenmalige aanmelding](#testing-single-sign-on)**  : om te controleren of de configuratie werkt.

### <a name="configuring-azure-ad-single-sign-on"></a>Eenmalige aanmelding Azure AD configureren

In dit gedeelte Azure AD eenmalige aanmelding inschakelen in de Azure portal en eenmalige aanmelding configureren in uw toepassing Veritas Enterprise Vault.cloud eenmalige aanmelding.

**Voor het configureren van Azure AD eenmalige aanmelding met Veritas Enterprise Vault.cloud SSO, moet u de volgende stappen uitvoeren:**

1. In de Azure-portal op de **Veritas Enterprise Vault.cloud SSO** toepassing Integratiepagina, klikt u op **eenmalige aanmelding**.

    ![Eenmalige aanmelding configureren][4]

2. Op de **eenmalige aanmelding** dialoogvenster Selecteer **modus** als **op basis van SAML aanmelding** voor eenmalige aanmelding inschakelen.
 
    ![Eenmalige aanmelding configureren](./media/active-directory-saas-veritas-tutorial/tutorial_veritas_samlbase.png)

3. Op de **Veritas Enterprise Vault.cloud SSO-domein en de URL's** sectie, voert u de volgende stappen uit:

    ![Eenmalige aanmelding configureren](./media/active-directory-saas-veritas-tutorial/tutorial_veritas_url.png)

    a. In de **aanmeldings-URL** textbox, typ een URL met het volgende patroon volgen:`https://personal.ap.archive.veritas.com/CID=<CUSTOMERID>`

    b. In de **id** tekstvak de URL aan de hand van het Datacenter gebruiken

    | Datacenter| URL |
    |----------|----|
    | Noord-Amerika| `https://auth.lax.archivecloud.net` |
    | Europa | `https://auth.ams.archivecloud.net` |
    | Azië en Stille Oceaan| `https://auth.syd.archivecloud.net`|

    c. In de **antwoord-URL** tekstvak de URL aan de hand van het Datacenter gebruiken

    | Datacenter| URL |
    |----------|----|
    | Noord-Amerika| `https://auth.lax.archivecloud.net` |
    | Europa | `https://auth.ams.archivecloud.net` |
    | Azië en Stille Oceaan| `https://auth.syd.archivecloud.net`|
    
    > [!NOTE] 
    > Deze waarde is geen echte. Deze waarde bijwerken met de werkelijke URL voor eenmalige aanmelding. Neem contact op met [Veritas Enterprise Vault.cloud SSO-Client-ondersteuningsteam](https://www.veritas.com/support/.html) deze waarde op te halen. 

4. Op de **SAML-certificaat voor ondertekening van** sectie, klikt u op **Certificate(Base64)** en sla het certificaatbestand op uw computer.

    ![Eenmalige aanmelding configureren](./media/active-directory-saas-veritas-tutorial/tutorial_veritas_certificate.png) 

5. Klik op **opslaan** knop.

    ![Eenmalige aanmelding configureren](./media/active-directory-saas-veritas-tutorial/tutorial_general_400.png)

6. Op de **Veritas Enterprise Vault.cloud SSO configuratie** sectie, klikt u op **configureren Veritas Enterprise Vault.cloud SSO** openen **eenmalige aanmelding configureren** venster. Kopieer de **SAML Single Sign-On Service-URL** van de **Naslaggids punt.**

    ![Eenmalige aanmelding configureren](./media/active-directory-saas-veritas-tutorial/tutorial_veritas_configure.png) 

7. Eenmalige aanmelding configureren op **Veritas Enterprise Vault.cloud SSO** zijde, moet u de gedownloade verzenden **Certificate(Base64)** en **SAML Single Sign-On Service-URL** naar [Veritas Enterprise Vault.cloud SSO-ondersteuningsteam](https://www.veritas.com/support/.html).

> [!TIP]
> U kunt nu een beknopte versie van deze instructies binnen lezen de [Azure-portal](https://portal.azure.com), terwijl u de app instelt!  Na het toevoegen van deze app uit de **Active Directory > bedrijfstoepassingen** sectie, klikt u op de **Single Sign-On** tabblad en toegang tot de ingesloten documentatie via de **configuratie** sectie onderaan. U kunt meer lezen over de ingesloten documentatie-functie: [embedded-documentatie voor Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken
Het doel van deze sectie is het een testgebruiker maken in de Azure portal Britta Simon aangeroepen.

![Azure AD-gebruiker maken][100]

**Als u wilt een testgebruiker maken in Azure AD, moet u de volgende stappen uitvoeren:**

1. In de **Azure-portal**, klik op het navigatiedeelvenster links **Azure Active Directory** pictogram.

    ![Een Azure AD-testgebruiker maken](./media/active-directory-saas-veritas-tutorial/create_aaduser_01.png) 

2. Als u wilt weergeven in de lijst met gebruikers, gaat u naar **gebruikers en groepen** en klik op **alle gebruikers**.
    
    ![Een Azure AD-testgebruiker maken](./media/active-directory-saas-veritas-tutorial/create_aaduser_02.png) 

3. Openen van de **gebruiker** dialoogvenster, klikt u op **toevoegen** boven aan het dialoogvenster.
 
    ![Een Azure AD-testgebruiker maken](./media/active-directory-saas-veritas-tutorial/create_aaduser_03.png) 

4. Op de **gebruiker** dialoogvenster pagina, voert u de volgende stappen uit:
 
    ![Een Azure AD-testgebruiker maken](./media/active-directory-saas-veritas-tutorial/create_aaduser_04.png) 

    a. In de **naam** textbox type **BrittaSimon**.

    b. In de **gebruikersnaam** textbox type de **e-mailadres** van BrittaSimon.

    c. Selecteer **wachtwoord weergeven** en noteer de waarde van de **wachtwoord**.

    d. Klik op **Create**.
 
### <a name="creating-a-veritas-enterprise-vaultcloud-sso-test-user"></a>Maken van een testgebruiker Veritas Enterprise Vault.cloud SSO

In deze sectie kunt u een gebruiker Britta Simon aangeroepen in Enterprise Vault.cloud SSO maken. Werken met [Veritas Enterprise Vault.cloud SSO-ondersteuningsteam](https://www.veritas.com/support/.html) om toe te voegen de gebruikers in de onderneming Vault.cloud SSO-platform. Gebruikers moeten worden gemaakt en worden geactiveerd voordat u eenmalige aanmelding gebruiken.

### <a name="assigning-the-azure-ad-test-user"></a>Toewijzen van de testgebruiker Azure AD

In deze sectie maakt inschakelen u Britta Simon gebruikt Azure eenmalige aanmelding toegang verlenen aan Veritas Enterprise Vault.cloud eenmalige aanmelding.

![Gebruiker toewijzen][200] 

**Britta Simon om aan te wijzen Veritas Enterprise Vault.cloud SSO, moet u de volgende stappen uitvoeren:**

1. Open de weergave toepassingen in de Azure-portal en gaat u naar de directoryweergave en gaat u naar **bedrijfstoepassingen** klikt u vervolgens op **alle toepassingen**.

    ![Gebruiker toewijzen][201] 

2. Selecteer in de lijst met toepassingen **Veritas Enterprise Vault.cloud SSO**.

    ![Eenmalige aanmelding configureren](./media/active-directory-saas-veritas-tutorial/tutorial_veritas_app.png) 

3. Klik in het menu aan de linkerkant op **gebruikers en groepen**.

    ![Gebruiker toewijzen][202] 

4. Klik op **toevoegen** knop. Selecteer vervolgens **gebruikers en groepen** op **toevoegen toewijzing** dialoogvenster.

    ![Gebruiker toewijzen][203]

5. Op **gebruikers en groepen** dialoogvenster Selecteer **Britta Simon** in de lijst gebruikers.

6. Klik op **Selecteer** knop op **gebruikers en groepen** dialoogvenster.

7. Klik op **toewijzen** knop op **toevoegen toewijzing** dialoogvenster.
    
### <a name="testing-single-sign-on"></a>Testen van eenmalige aanmelding

In deze sectie kunt u uw Azure AD eenmalige aanmelding configuratie met behulp van het toegangsvenster testen.

Als u op de tegel Veritas Enterprise Vault.cloud SSO in het deelvenster toegang, u moet ophalen automatisch aangemeld bij uw toepassing Veritas Enterprise Vault.cloud eenmalige aanmelding.

## <a name="additional-resources"></a>Aanvullende resources

* [Lijst met zelfstudies over het integreren van SaaS-Apps met Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Wat is de toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-veritas-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-veritas-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-veritas-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-veritas-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-veritas-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-veritas-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-veritas-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-veritas-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-veritas-tutorial/tutorial_general_203.png

