---
title: Automation Azure die Run As-accounts maken
description: In dit artikel wordt beschreven hoe u uw Automation-account bijwerkt en Uitvoeren als-accounts maakt met PowerShell of vanuit de portal.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: b50c9f059d82d9378fba8db663aa2e8dce0273f5
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2018
---
# <a name="update-your-automation-account-authentication-with-run-as-accounts"></a>Uw Automation-account bijwerken met Uitvoeren als-accounts 
In de volgende gevallen kunt u een bestaand Automation-account bijwerken vanuit Azure Portal of met PowerShell:

* U maakt wel een Automation-account, maar geen Uitvoeren als-account.
* U gebruikt al een Automation-account voor het beheer van Resource Manager-resources en u wilt het account bijwerken met het Uitvoeren als-account voor runbookverificatie.
* U gebruikt al een Automation-account voor het beheer van klassieke resources en u wilt dit bijwerken, zodat u het klassieke Uitvoeren als-account kunt gebruiken in plaats van een nieuw account te maken, en uw runbooks en activa daarnaartoe te migreren.   

Tijdens dit proces worden de volgende items in uw Automation-account gemaakt.

**Voor Uitvoeren als-accounts:**

* Er wordt een Azure AD-toepassing met een zelf-ondertekend certificaat gemaakt. Daarnaast wordt er voor de toepassing in Azure AD een service-principalaccount gemaakt en wordt aan dit account de rol Inzender toegewezen in uw huidige abonnement. U kunt deze instelling wijzigen in Eigenaar of een andere rol. Zie [Op rollen gebaseerd toegangsbeheer in Azure Automation](automation-role-based-access-control.md) voor meer informatie.
* Er wordt een Automation-certificaatasset gemaakt met de naam *AzureRunAsCertificate* in het opgegeven Automation-account. Het certificaatasset bevat de persoonlijke sleutel van het certificaat die wordt gebruikt door de Azure AD-toepassing.
* Er wordt een Automation-verbindingsasset gemaakt met de naam *AzureRunAsConnection* in het opgegeven Automation-account. Het verbindingsasset bevat de toepassing-id, tenant-id, abonnement-id en certificaatvingerafdruk.

**Voor klassieke uitvoeren als-accounts:**

* Er wordt een Automation-certificaatasset gemaakt met de naam *AzureClassicRunAsCertificate* in het opgegeven Automation-account. Het certificaatasset bevat de persoonlijke sleutel van het certificaat die wordt gebruikt door het beheercertificaat.
* Er wordt een Automation-verbindingsasset gemaakt met de naam *AzureClassicRunAsConnection* in het opgegeven Automation-account. Het verbindingsasset bevat de naam van het abonnement, de abonnements-id en de certificaatassetnaam.

## <a name="prerequisites"></a>Vereisten
Als u [PowerShell wilt gebruiken voor het maken van Uitvoeren als-accounts](#create-run-as-account-using-powershell), is het volgende vereist voor dit proces:

* Windows 10 en Windows Server 2016 met Azure Resource Manager-modules 3.4.1 en hoger. Het PowerShell-script biedt geen ondersteuning voor eerdere versies van Windows.
* Azure PowerShell 1.0 en hoger. Zie [Azure PowerShell installeren en configureren](/powershell/azureps-cmdlets-docs) voor meer informatie over de PowerShell 1.0-release.
* Een Automation-account waarnaar wordt verwezen als de waarde voor de parameter *– AutomationAccountName* en *- ApplicationDisplayName*.

Ga als volgt te werk om de waarden op te halen voor *SubscriptionID*, *ResourceGroup* en *AutomationAccountName*; dit zijn vereiste parameters voor de scripts:

1. Klik in Azure Portal op **Alle services**. Typ in de lijst met resources **Automation**. Als u begint te typen, wordt de lijst gefilterd op basis van uw invoer. Selecteer **Automation-accounts**.
2. Selecteer op de Automation-accountpagina uw Automation-account en selecteer vervolgens onder **Accountinstellingen** de optie **Eigenschappen**.  
3. Noteer de waarden op de pagina **Eigenschappen**.<br><br> ![De blade Eigenschappen voor het Automation-account](media/automation-create-runas-account/automation-account-properties.png)  

### <a name="required-permissions-to-update-your-automation-account"></a>Machtigingen die zijn vereist om een Automation-account bij te werken
Als u een Automation-account wilt bijwerken, moet u de volgende specifieke machtigingen en bevoegdheden hebben om dit onderwerp te voltooien.   
 
* Uw AD-gebruikersaccount moet worden toegevoegd aan een rol met machtigingen die equivalent zijn aan de rol Inzender voor Microsoft.Automation bronnen, zoals wordt beschreven in artikel [toegangsbeheer op basis van rollen in Azure Automation](automation-role-based-access-control.md#contributor).  
* Gebruikers zonder beheerdersrechten in uw Azure AD-tenant kunnen [AD-toepassingen registreren](../azure-resource-manager/resource-group-create-service-principal-portal.md#check-azure-subscription-permissions) als de optie **Gebruikers kunnen toepassingen registreren** van de Azure AD-tenant op de pagina **Gebruikersinstellingen** is ingesteld op **Ja**. Als de app-registratie-instelling is ingesteld op **Nee**, moet de gebruiker die deze actie uitvoert een globale beheerder zijn in Azure AD.

Als u geen lid bent van het Active Directory-exemplaar van het abonnement voordat u wordt toegevoegd aan de rol van globale beheerder/medebeheerder van het abonnement, wordt u als gast toegevoegd aan Active Directory. In dat geval wordt de waarschuwing 'U bent niet gemachtigd om…' weergegeven op de blade **Automation-account toevoegen**. Gebruikers die zijn toegevoegd aan de rol van globale beheerder/medebeheerder, kunnen worden verwijderd uit het Active Directory-exemplaar van het abonnement en opnieuw worden toegevoegd, zodat ze een volledige gebruiker worden in Active Directory. U kunt deze situatie controleren door in het deelvenster **Azure Active Directory** van Azure Portal **Gebruikers en groepen** te selecteren. Selecteer vervolgens **Alle gebruikers**, de specifieke gebruiker en **Profiel**. De waarde van het kenmerk **Gebruikerstype** onder het gebruikersprofiel mag niet gelijk zijn aan **Gast**.

## <a name="create-run-as-account-from-the-portal"></a>Uitvoeren als-account maken vanuit de portal
In deze sectie voert u de volgende stappen uit om uw Azure Automation-account bij te werken in Azure Portal. U maakt afzonderlijke Uitvoeren als- en Klassiek Uitvoeren als-accounts. Als u geen klassieke resources hoeft te beheren, kunt u alleen de Uitvoeren als-account van Azure maken.  

1. Meld u aan bij Azure Portal met een account dat lid is van de rol Abonnementsbeheerders en dat medebeheerder is van het abonnement.
2. Klik in Azure Portal op **Alle services**. Typ in de lijst met resources **Automation**. Als u begint te typen, wordt de lijst gefilterd op basis van uw invoer. Selecteer **Automation-accounts**.
3. Selecteer op de pagina **Automation-accounts** uw Automation-account uit de lijst met Automation-accounts.
4. Selecteer in het linkerdeelvenster de optie **Uitvoeren als-accounts** in de sectie **Accountinstellingen**.  
5. Afhankelijk van welk account u nodig hebt, selecteert u **Uitvoeren als-account van Azure**  of **Klassiek Uitvoeren als-account van Azure**. Na deze selectie wordt het deelvenster **Azure uitvoeren als-account toevoegen** of **Klassiek Uitvoeren als-account toevoegen** weergegeven. Controleer de overzichtsgegevens en klik op **Maken** om verder te gaan met het maken van een Uitvoeren als-account.  
6. Terwijl in Azure het Uitvoeren als-account wordt gemaakt, kunt u in het menu onder **Meldingen** de voortgang hiervan volgen. Er wordt ook een banner weergegeven waarin staat dat het account wordt gemaakt. Dit proces kan enkele minuten duren.  

## <a name="create-run-as-account-using-powershell-script"></a>Uitvoeren als-account maken met een PowerShell-script
Dit PowerShell-script biedt ondersteuning voor de volgende configuraties:

* Een Uitvoeren als-account maken met een zelfondertekend certificaat.
* Een Uitvoeren als-account en een klassiek Uitvoeren als-account maken met een zelfondertekend certificaat.
* Maak een Uitvoeren als-account en een klassiek Uitvoeren als-account via een certificaat dat is uitgegeven door de certificeringsinstantie van uw bedrijf.
* Een Uitvoeren als-account en een klassiek Uitvoeren als-account maken met een zelfondertekend certificaat in de cloud van Azure Government.

>[!NOTE]
> Als u ervoor kiest om een klassiek Uitvoeren als-account te maken, moet u na het uitvoeren van het script het openbare certificaat (bestandsnaamextensie .cer) uploaden naar het beheerarchief voor het abonnement waarin het Automation-account is gemaakt.
> 

1. Sla het volgende script op uw computer op. Sla het bestand in dit voorbeeld op met de bestandsnaam *New-RunAsAccount.ps1*.

    ```powershell
    #Requires -RunAsAdministrator
    Param (
        [Parameter(Mandatory = $true)]
        [String] $ResourceGroup,
        
        [Parameter(Mandatory = $true)]
        [String] $AutomationAccountName,
        
        [Parameter(Mandatory = $true)]
        [String] $ApplicationDisplayName,
        
        [Parameter(Mandatory = $true)]
        [String] $SubscriptionId,
        
        [Parameter(Mandatory = $true)]
        [Boolean] $CreateClassicRunAsAccount,
        
        [Parameter(Mandatory = $true)]
        [String] $SelfSignedCertPlainPassword,
        
        [Parameter(Mandatory = $false)]
        [String] $EnterpriseCertPathForRunAsAccount,
        
        [Parameter(Mandatory = $false)]
        [String] $EnterpriseCertPlainPasswordForRunAsAccount,
        
        [Parameter(Mandatory = $false)]
        [String] $EnterpriseCertPathForClassicRunAsAccount,
        
        [Parameter(Mandatory = $false)]
        [String] $EnterpriseCertPlainPasswordForClassicRunAsAccount,
        
        [Parameter(Mandatory = $false)]
        [ValidateSet("AzureCloud", "AzureUSGovernment")]
        [string]$EnvironmentName = "AzureCloud",
        
        [Parameter(Mandatory = $false)]
        [int] $SelfSignedCertNoOfMonthsUntilExpired = 12
    )
        
    function CreateSelfSignedCertificate([string] $certificateName, [string] $selfSignedCertPlainPassword,
        [string] $certPath, [string] $certPathCer, [string] $selfSignedCertNoOfMonthsUntilExpired ) {
        $Cert = New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation cert:\LocalMachine\My `
            -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
            -NotAfter (Get-Date).AddMonths($selfSignedCertNoOfMonthsUntilExpired) -HashAlgorithm SHA256
        
        $CertPassword = ConvertTo-SecureString $selfSignedCertPlainPassword -AsPlainText -Force
        Export-PfxCertificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPath -Password $CertPassword -Force | Write-Verbose
        Export-Certificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPathCer -Type CERT | Write-Verbose
    }
        
    function CreateServicePrincipal([System.Security.Cryptography.X509Certificates.X509Certificate2] $PfxCert, [string] $applicationDisplayName) {  
        $keyValue = [System.Convert]::ToBase64String($PfxCert.GetRawCertData())
        $keyId = (New-Guid).Guid
        
        $startDate = Get-Date
        $endDate = (Get-Date $PfxCert.GetExpirationDateString()).AddDays(-1)
        
        #Create an Azure AD application, AD App Credential, AD ServicePrincipal
        $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $applicationDisplayName) -IdentifierUris ("http://" + $keyId) 
        $ApplicationCredential = New-AzureRmADAppCredential -ApplicationId $Application.ApplicationId -CertValue $keyValue -StartDate $startDate -EndDate $endDate 
        $ServicePrincipal = New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId 
        $GetServicePrincipal = Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id
        
        # Sleep here for a few seconds to allow the service principal application to become active (ordinarily takes a few seconds)
        Sleep -s 15
        $NewRole = New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
        $Retries = 0;
        While ($NewRole -eq $null -and $Retries -le 6) {
            Sleep -s 10
            New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
            $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
            $Retries++;
        }
        return $Application.ApplicationId.ToString();
    }
        
    function CreateAutomationCertificateAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $certifcateAssetName, [string] $certPath, [string] $certPlainPassword, [Boolean] $Exportable) {
        $CertPassword = ConvertTo-SecureString $certPlainPassword -AsPlainText -Force   
        Remove-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $certifcateAssetName -ErrorAction SilentlyContinue
        New-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Path $certPath -Name $certifcateAssetName -Password $CertPassword -Exportable:$Exportable  | write-verbose
    }
        
    function CreateAutomationConnectionAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $connectionAssetName, [string] $connectionTypeName, [System.Collections.Hashtable] $connectionFieldValues ) {
        Remove-AzureRmAutomationConnection -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -Force -ErrorAction SilentlyContinue
        New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -ConnectionTypeName $connectionTypeName -ConnectionFieldValues $connectionFieldValues
    }
        
    Import-Module AzureRM.Profile
    Import-Module AzureRM.Resources
        
    $AzureRMProfileVersion = (Get-Module AzureRM.Profile).Version
    if (!(($AzureRMProfileVersion.Major -ge 3 -and $AzureRMProfileVersion.Minor -ge 4) -or ($AzureRMProfileVersion.Major -gt 3))) {
        Write-Error -Message "Please install the latest Azure PowerShell and retry. Relevant doc url : https://docs.microsoft.com/powershell/azureps-cmdlets-docs/ "
        return
    }
        
    Login-AzureRmAccount -Environment $EnvironmentName 
    $Subscription = Select-AzureRmSubscription -SubscriptionId $SubscriptionId
        
    # Create a Run As account by using a service principal
    $CertifcateAssetName = "AzureRunAsCertificate"
    $ConnectionAssetName = "AzureRunAsConnection"
    $ConnectionTypeName = "AzureServicePrincipal"
        
    if ($EnterpriseCertPathForRunAsAccount -and $EnterpriseCertPlainPasswordForRunAsAccount) {
        $PfxCertPathForRunAsAccount = $EnterpriseCertPathForRunAsAccount
        $PfxCertPlainPasswordForRunAsAccount = $EnterpriseCertPlainPasswordForRunAsAccount
    }
    else {
        $CertificateName = $AutomationAccountName + $CertifcateAssetName
        $PfxCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".pfx")
        $PfxCertPlainPasswordForRunAsAccount = $SelfSignedCertPlainPassword
        $CerCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".cer")
        CreateSelfSignedCertificate $CertificateName $PfxCertPlainPasswordForRunAsAccount $PfxCertPathForRunAsAccount $CerCertPathForRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
    }
        
    # Create a service principal
    $PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPathForRunAsAccount, $PfxCertPlainPasswordForRunAsAccount)
    $ApplicationId = CreateServicePrincipal $PfxCert $ApplicationDisplayName
        
    # Create the Automation certificate asset
    CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $CertifcateAssetName $PfxCertPathForRunAsAccount $PfxCertPlainPasswordForRunAsAccount $true
        
    # Populate the ConnectionFieldValues
    $SubscriptionInfo = Get-AzureRmSubscription -SubscriptionId $SubscriptionId
    $TenantID = $SubscriptionInfo | Select TenantId -First 1
    $Thumbprint = $PfxCert.Thumbprint
    $ConnectionFieldValues = @{"ApplicationId" = $ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Thumbprint; "SubscriptionId" = $SubscriptionId}
        
    # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
    CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ConnectionAssetName $ConnectionTypeName $ConnectionFieldValues
        
    if ($CreateClassicRunAsAccount) {
        # Create a Run As account by using a service principal
        $ClassicRunAsAccountCertifcateAssetName = "AzureClassicRunAsCertificate"
        $ClassicRunAsAccountConnectionAssetName = "AzureClassicRunAsConnection"
        $ClassicRunAsAccountConnectionTypeName = "AzureClassicCertificate "
        $UploadMessage = "Please upload the .cer format of #CERT# to the Management store by following the steps below." + [Environment]::NewLine +
        "Log in to the Microsoft Azure portal (https://portal.azure.com) and select Subscriptions -> Management Certificates." + [Environment]::NewLine +
        "Then click Upload and upload the .cer format of #CERT#"
        
        if ($EnterpriseCertPathForClassicRunAsAccount -and $EnterpriseCertPlainPasswordForClassicRunAsAccount ) {
            $PfxCertPathForClassicRunAsAccount = $EnterpriseCertPathForClassicRunAsAccount
            $PfxCertPlainPasswordForClassicRunAsAccount = $EnterpriseCertPlainPasswordForClassicRunAsAccount
            $UploadMessage = $UploadMessage.Replace("#CERT#", $PfxCertPathForClassicRunAsAccount)
        }
        else {
            $ClassicRunAsAccountCertificateName = $AutomationAccountName + $ClassicRunAsAccountCertifcateAssetName
            $PfxCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".pfx")
            $PfxCertPlainPasswordForClassicRunAsAccount = $SelfSignedCertPlainPassword
            $CerCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".cer")
            $UploadMessage = $UploadMessage.Replace("#CERT#", $CerCertPathForClassicRunAsAccount)
            CreateSelfSignedCertificate $ClassicRunAsAccountCertificateName $PfxCertPlainPasswordForClassicRunAsAccount $PfxCertPathForClassicRunAsAccount $CerCertPathForClassicRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
        }
        
        # Create the Automation certificate asset
        CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountCertifcateAssetName $PfxCertPathForClassicRunAsAccount $PfxCertPlainPasswordForClassicRunAsAccount $false
        
        # Populate the ConnectionFieldValues
        $SubscriptionName = $subscription.Subscription.Name
        $ClassicRunAsAccountConnectionFieldValues = @{"SubscriptionName" = $SubscriptionName; "SubscriptionId" = $SubscriptionId; "CertificateAssetName" = $ClassicRunAsAccountCertifcateAssetName}
        
        # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
        CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountConnectionAssetName $ClassicRunAsAccountConnectionTypeName   $ClassicRunAsAccountConnectionFieldValues
        
        Write-Host -ForegroundColor red       $UploadMessage
    }
    ```

2. Start op uw computer **Windows PowerShell** op vanaf het **Start**scherm met verhoogde gebruikersrechten.
3. Ga vanuit de opdrachtregel-shell met verhoogde bevoegdheden naar de map die het script bevat dat u in stap 1 hebt gemaakt.  
4. Voer het script uit met de parameterwaarden voor de configuratie die u nodig hebt.

    **Een Uitvoeren als-account maken met een zelfondertekend certificaat**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false`

    **Een Uitvoeren als-account en een klassiek Uitvoeren als-account maken met een zelfondertekend certificaat**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true`

    **Een Uitvoeren als-account en een klassiek Uitvoeren als-account maken met een bedrijfscertificaat**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>`

    **Een Uitvoeren als-account en een klassiek Uitvoeren als-account maken met een zelfondertekend certificaat in de cloud van Azure Government**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment`

    > [!NOTE]
    > Nadat het script is uitgevoerd, wordt u gevraagd zich te verifiëren met Azure. Meld u aan met een account dat lid is van de rol Abonnementsbeheerders en dat medebeheerder is van het abonnement.
    >
    >

Let op het volgende nadat het script is uitgevoerd:
* Als u een klassiek Uitvoeren als-account hebt gemaakt met een zelfondertekend openbaar certificaat (.cer-bestand), wordt het door het script gemaakt en opgeslagen in de map met tijdelijke bestanden op uw computer, onder het gebruikersprofiel *%USERPROFILE%\AppData\Local\Temp*, dat u hebt gebruikt voor het uitvoeren van de PowerShell-sessie.
* Gebruik dit certificaat als u een klassiek Uitvoeren als-account hebt gemaakt met een openbaar certificaat (.cer-bestand). Volg de instructies voor [een beheer-API-certificaat uploaden naar de Azure-portal](../azure-api-management-certs.md), en vervolgens de verwijzingsconfiguratie met resources van klassieke implementatie te valideren met behulp van de [voorbeeldcode om te verifiëren met de klassieke Azure-implementatieresources](automation-verify-runas-authentication.md#classic-run-as-authentication). 
* Als u *geen* klassiek Uitvoeren als-account hebt gemaakt, verifieert u met Resource Manager-resources en valideert u de configuratie van de referenties met behulp van de [voorbeeldcode om verificatie met Service Management-resources uit te voeren](automation-verify-runas-authentication.md#automation-run-as-authentication).

## <a name="limiting-run-as-account-permissions"></a>Run As-accountmachtigingen beperken

Voor het beheren van doelen van automatisering op basis van bronnen in Azure Automation, krijgt het account uitvoeren als standaard Inzender-rechten in het abonnement. Als u beperken wat de RunAs-service-principal kunt doen wilt, kunt u het account niet verwijderen uit de rol Inzender aan het abonnement en als een inzender toevoegen aan de resourcegroepen die u wilt opgeven.

Selecteer in de Azure-portal **abonnementen** en kiest u het abonnement van uw Automation-Account. Selecteer **toegangsbeheer (IAM)** en zoek naar de service-principal voor uw Automation-Account (het lijkt erop dat \<AutomationAccountName\>_unique id). Selecteer het account en klik op **verwijderen** te verwijderen uit het abonnement.

![Abonnement medewerkers](media/automation-create-runas-account/automation-account-remove-subscription.png)

Om de service-principal toevoegen aan een resourcegroep, selecteert u de resourcegroep in de Azure portal en selecteert u een **toegangsbeheer (IAM)**. Selecteer **toevoegen**, Hiermee opent u de **machtigingen toevoegen** pagina. Voor **rol**, selecteer **Inzender**. In de **Selecteer** tekstvak Typ de naam van de service-principal voor de Run As-account in en selecteert u deze in de lijst. Klik op **Opslaan** om de wijzigingen op te slaan. Doe dit voor de resourcegroepen die u wilt uw Automation Azure die worden uitgevoerd als service principal toegang geven tot.

## <a name="next-steps"></a>Volgende stappen
* Zie voor meer informatie over Service-Principals [toepassingsobjecten en Service-Principal objecten](../active-directory/active-directory-application-objects.md).
* Zie voor meer informatie over certificaten en Azure-services [certificaten voor Azure Cloud Services-overzicht](../cloud-services/cloud-services-certs-create.md).
