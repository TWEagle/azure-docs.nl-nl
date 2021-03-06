---
title: Azure Data Lake Analytics beheren met Python | Microsoft Docs
description: 'Leer hoe u met Python een Data Lake Store-account maakt en taken verzendt. '
services: data-lake-analytics
documentationcenter: ''
author: matt1883
manager: jhubbard
editor: cgronlun
ms.assetid: d4213a19-4d0f-49c9-871c-9cd6ed7cf731
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/18/2017
ms.author: saveenr
ms.custom: devcenter
ms.openlocfilehash: 0182a14979550c880904ec829f6b59dee016cad2
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/05/2018
---
# <a name="manage-azure-data-lake-analytics-using-python"></a>Azure Data Lake Analytics beheren met Python

## <a name="python-versions"></a>Python-versies

* Een 64-bits versie van Python gebruiken.
* U kunt de standaard Python-distributie gevonden op  **[Python.org downloadt](https://www.python.org/downloads/)**. 
* Veel ontwikkelaars vinden gemakkelijk te gebruiken de  **[Anaconda Python-distributie](https://www.continuum.io/downloads)**.  
* Dit artikel is geschreven met behulp van Python versie 3.6 van de standaard Python-distributie

## <a name="install-azure-python-sdk"></a>Azure Python-SDK installeren

De volgende modules installeren:

* De **azure-mgmt-resource** -module bevat andere Azure-modules voor Active Directory, enz.
* De **azure-mgmt-datalake-store** -module bevat de beheerbewerkingen voor Azure Data Lake Store-account.
* De **azure-datalake-store** -module bevat de bestandssysteembewerkingen Azure Data Lake Store. 
* De **azure-datalake-analytics** -module bevat de Azure Data Lake Analytics-bewerkingen. 

U hebt de meest recente eerst voor zorgen `pip` met de volgende opdracht:

```
python -m pip install --upgrade pip
```

Dit document is geschreven met `pip version 9.0.1`.

Gebruik de volgende `pip` opdrachten de modules installeren vanaf de opdrachtregel:

```
pip install azure-mgmt-resource
pip install azure-datalake-store
pip install azure-mgmt-datalake-store
pip install azure-mgmt-datalake-analytics
```

## <a name="create-a-new-python-script"></a>Een nieuwe pythonscript maken

Plak de volgende code in het script:

```python
## Use this only for Azure AD service-to-service authentication
#from azure.common.credentials import ServicePrincipalCredentials

## Use this only for Azure AD end-user authentication
#from azure.common.credentials import UserPassCredentials

## Required for Azure Resource Manager
from azure.mgmt.resource.resources import ResourceManagementClient
from azure.mgmt.resource.resources.models import ResourceGroup

## Required for Azure Data Lake Store account management
from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
from azure.mgmt.datalake.store.models import DataLakeStoreAccount

## Required for Azure Data Lake Store filesystem management
from azure.datalake.store import core, lib, multithread

## Required for Azure Data Lake Analytics account management
from azure.mgmt.datalake.analytics.account import DataLakeAnalyticsAccountManagementClient
from azure.mgmt.datalake.analytics.account.models import DataLakeAnalyticsAccount, DataLakeStoreAccountInfo

## Required for Azure Data Lake Analytics job management
from azure.mgmt.datalake.analytics.job import DataLakeAnalyticsJobManagementClient
from azure.mgmt.datalake.analytics.job.models import JobInformation, JobState, USqlJobProperties

## Required for Azure Data Lake Analytics catalog management
from azure.mgmt.datalake.analytics.catalog import DataLakeAnalyticsCatalogManagementClient

## Use these as needed for your application
import logging, getpass, pprint, uuid, time
```

Voer dit script om te controleren dat de modules die kunnen worden geïmporteerd.

## <a name="authentication"></a>Verificatie

### <a name="interactive-user-authentication-with-a-pop-up"></a>Interactieve gebruikersverificatie met een pop-upvenster

Deze methode wordt niet ondersteund.

### <a name="interactive-user-authentication-with-a-device-code"></a>Interactieve gebruikersverificatie met een apparaatcode

```python
user = input('Enter the user to authenticate with that has permission to subscription: ')
password = getpass.getpass()
credentials = UserPassCredentials(user, password)
```

### <a name="noninteractive-authentication-with-spi-and-a-secret"></a>Niet-interactieve verificatie met SPI en een geheim

```python
credentials = ServicePrincipalCredentials(client_id = 'FILL-IN-HERE', secret = 'FILL-IN-HERE', tenant = 'FILL-IN-HERE')
```

### <a name="noninteractive-authentication-with-api-and-a-certificate"></a>Niet-interactieve verificatie met de API en een certificaat

Deze methode wordt niet ondersteund.

## <a name="common-script-variables"></a>Algemene scriptvariabelen

Deze variabelen worden gebruikt in de voorbeelden.

```python
subid= '<Azure Subscription ID>'
rg = '<Azure Resource Group Name>'
location = '<Location>' # i.e. 'eastus2'
adls = '<Azure Data Lake Store Account Name>'
adla = '<Azure Data Lake Analytics Account Name>'
```

## <a name="create-the-clients"></a>De clients maken

```python
resourceClient = ResourceManagementClient(credentials, subid)
adlaAcctClient = DataLakeAnalyticsAccountManagementClient(credentials, subid)
adlaJobClient = DataLakeAnalyticsJobManagementClient( credentials, 'azuredatalakeanalytics.net')
```

## <a name="create-an-azure-resource-group"></a>Een Azure-resourcegroep maken

```python
armGroupResult = resourceClient.resource_groups.create_or_update( rg, ResourceGroup( location=location ) )
```

## <a name="create-data-lake-analytics-account"></a>Een Data Lake Analytics-account maken

Maak eerst een store-account.

```python
adlsAcctResult = adlsAcctClient.account.create(
    rg,
    adls,
    DataLakeStoreAccount(
        location=location)
    )
).wait()
```
Maakt een ADLA-account dat dit archief wordt op.

```python
adlaAcctResult = adlaAcctClient.account.create(
    rg,
    adla,
    DataLakeAnalyticsAccount(
        location=location,
        default_data_lake_store_account=adls,
        data_lake_store_accounts=[DataLakeStoreAccountInfo(name=adls)]
    )
).wait()
```

## <a name="submit-a-job"></a>Verzenden van een taak

```python
script = """
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
"""

jobId = str(uuid.uuid4())
jobResult = adlaJobClient.job.create(
    adla,
    jobId,
    JobInformation(
        name='Sample Job',
        type='USql',
        properties=USqlJobProperties(script=script)
    )
)
```

## <a name="wait-for-a-job-to-end"></a>Wacht totdat een taak beëindigen

```python
jobResult = adlaJobClient.job.get(adla, jobId)
while(jobResult.state != JobState.ended):
    print('Job is not yet done, waiting for 3 seconds. Current state: ' + jobResult.state.value)
    time.sleep(3)
    jobResult = adlaJobClient.job.get(adla, jobId)

print ('Job finished with result: ' + jobResult.result.value)
```

## <a name="list-pipelines-and-recurrences"></a>Lijst met pijplijnen en herhalingen
Afhankelijk van of uw taken pijplijn of terugkeerpatroon metagegevens gekoppeld hebt, kunt u pijplijnen en terugkeerpatronen aanbieden.

```python
pipelines = adlaJobClient.pipeline.list(adla)
for p in pipelines:
    print('Pipeline: ' + p.name + ' ' + p.pipelineId)

recurrences = adlaJobClient.recurrence.list(adla)
for r in recurrences:
    print('Recurrence: ' + r.name + ' ' + r.recurrenceId)
```

## <a name="manage-compute-policies"></a>Compute-beleid beheren

Het object DataLakeAnalyticsAccountManagementClient biedt methoden voor het beheren van de compute-beleidsregels voor een Data Lake Analytics-account.

### <a name="list-compute-policies"></a>Compute-beleidsregels weergeven

De volgende code haalt een overzicht van compute-beleidsregels voor een Data Lake Analytics-account.

```python
policies = adlaAccountClient.computePolicies.listByAccount(rg, adla)
for p in policies:
    print('Name: ' + p.name + 'Type: ' + p.objectType + 'Max AUs / job: ' + p.maxDegreeOfParallelismPerJob + 'Min priority / job: ' + p.minPriorityPerJob)
```

### <a name="create-a-new-compute-policy"></a>Maak een nieuw compute-beleid

De volgende code maakt een nieuw compute-beleid voor een Data Lake Analytics-account, de maximale AUs beschikbaar stellen voor de opgegeven gebruiker tot 50 en de prioriteit van de minimale job 250.

```python
userAadObjectId = "3b097601-4912-4d41-b9d2-78672fc2acde"
newPolicyParams = ComputePolicyCreateOrUpdateParameters(userAadObjectId, "User", 50, 250)
adlaAccountClient.computePolicies.createOrUpdate(rg, adla, "GaryMcDaniel", newPolicyParams)
```

## <a name="next-steps"></a>Volgende stappen

- Als u dezelfde zelfstudie wilt bekijken met een ander hulpprogramma, klikt u op de tabselectors boven aan de pagina.
- Zie [Aan de slag met de Azure Data Lake Analytics U-SQL-taal](data-lake-analytics-u-sql-get-started.md) om U-SQL te leren.
- Zie [Azure Data Lake Analytics beheren met Azure Portal](data-lake-analytics-manage-use-portal.md) voor informatie over beheertaken.

