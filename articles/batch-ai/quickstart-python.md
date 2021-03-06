---
title: 'Azure-snelstart: CNTK-training met Batch AI - Python | Microsoft Docs'
description: Leer snel om een CNTK-trainingstaak uit te voeren met Batch AI met behulp van de Python SDK
services: batch-ai
documentationcenter: na
author: lliimsft
manager: Vaman.Bedekar
editor: tysonn
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: Python
ms.topic: quickstart
ms.date: 10/06/2017
ms.author: lili
ms.openlocfilehash: f535c9adf4926f29ae9cade6382debedab73937d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2018
---
# <a name="run-a-cntk-training-job-using-the-azure-python-sdk"></a>Een CNTK-trainingstaak uitvoeren met behulp van Azure Python SDK

In deze snelstart wordt gedetailleerd beschreven hoe u de Azure Python SDK gebruikt om een CNTK-trainingstaak (Microsoft Cognitive Toolkit) uit te voeren met behulp van de Batch AI-service. 

In dit voorbeeld gebruikt u de MNIST-database met handgeschreven installatiekopieën om een CNN (Convolutional Neural Network) te trainen op een GPU-cluster met één knooppunt. 

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: als u nog geen Azure-abonnement hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint. 

* Azure Python SDK: raadpleeg de [installatie-instructies](/python/azure/python-sdk-azure-install)

* Azure-opslagaccount: zie [Een Azure-opslagaccount maken](../storage/common/storage-create-storage-account.md)

* Referenties voor de service-principal in Azure Active Directory: zie [een service-principal maken met de CLI](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md)

* Registreer de Batch AI-resourceproviders één keer voor uw abonnement met behulp van Azure Cloud Shell of Azure CLI. Het registreren van een provider duurt maximaal 15 minuten.

  ```azurecli
  az provider register -n Microsoft.BatchAI
  az provider register -n Microsoft.Batch
  ```


## <a name="configure-credentials"></a>Referenties configureren
Maak deze parameters in uw Python-script, waarbij u uw eigen waarden vervangt:

```Python
# credentials used for authentication
client_id = 'my_aad_client_id'
secret = 'my_aad_secret_key'
token_uri = 'my_aad_token_uri'
subscription_id = 'my_subscription_id'

# credentials used for storage
storage_account_name = 'my_storage_account_name'
storage_account_key = 'my_storage_account_key'

# specify the credentials used to remote login your GPU node
admin_user_name = 'my_admin_user_name'
admin_user_password = 'my_admin_user_password'
```

Het is een aanbevolen procedure om alle referenties op te slaan in een afzonderlijk configuratiebestand. Dit wordt niet getoond in dit voorbeeld. Raadpleeg de [recepten](https://github.com/Azure/BatchAI/tree/master/recipes) voor het implementeren van een configuratiebestand. 

## <a name="authenticate-with-batch-ai"></a>Verifiëren met Batch AI

Als u toegang wilt tot Azure Batch AI, moet u verifiëren met behulp van Azure Active Directory. Hieronder vindt u de verificatiecode voor de service (maak een `BatchAIManagementClient`-object). Hierbij wordt gebruikgemaakt van de referenties voor de service-principal en de abonnements-id:

```Python
from azure.common.credentials import ServicePrincipalCredentials
import azure.mgmt.batchai as batchai
import azure.mgmt.batchai.models as models

creds = ServicePrincipalCredentials(
        client_id=client_id, secret=secret, token_uri=token_uri)

batchai_client = batchai.BatchAIManagementClient(credentials=creds,
                                         subscription_id=subscription_id
)
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Batch AI-clusters en -taken zijn Azure-resources en moeten in een Azure-resourcegroep worden geplaatst. In het volgende fragment wordt een resourcegroep gemaakt:

```Python
from azure.mgmt.resource import ResourceManagementClient

resource_group_name = 'myresourcegroup'

resource_management_client = ResourceManagementClient(
        credentials=creds, subscription_id=subscription_id)

resource = resource_management_client.resource_groups.create_or_update(
        resource_group_name, {'location': 'eastus'})
```


## <a name="prepare-azure-file-share"></a>Azure-bestandsshare voorbereiden
Ter illustratie wordt in deze snelstart een Azure-bestandsshare gebruikt om de trainingsgegevens en -scripts voor de Learning-taak te hosten. 

1. Maak een bestandsshare met de naam *batchaiquickstart*.

  ```Python
  from azure.storage.file import FileService 
 
  azure_file_share_name = 'batchaiquickstart' 
 
  service = FileService(storage_account_name, storage_account_key) 
 
  service.create_share(azure_file_share_name, fail_on_exist=False)
  ``` 
 
2. Maak een map in de share met de naam *mnistcntksample* 

  ```Python
  mnist_dataset_directory = 'mnistcntksample' 
 
  service.create_directory(azure_file_share_name, mnist_dataset_directory, fail_on_exist=False) 
  ```
3. Download het [voorbeeldpakket](https://batchaisamples.blob.core.windows.net/samples/BatchAIQuickStart.zip?st=2017-09-29T18%3A29%3A00Z&se=2099-12-31T08%3A00%3A00Z&sp=rl&sv=2016-05-31&sr=b&sig=hrAZfbZC%2BQ%2FKccFQZ7OC4b%2FXSzCF5Myi4Cj%2BW3sVZDo%3D) en pak dit uit. Upload de inhoud naar de map waarin het Python-script wordt uitgevoerd.

  ```Python
  for f in ['Train-28x28_cntk_text.txt', 'Test-28x28_cntk_text.txt', 'ConvNet_MNIST.py']:     
     service.create_file_from_path(
             azure_file_share_name, mnist_dataset_directory, f, f) 
  ```

## <a name="create-gpu-cluster"></a>GPU-cluster maken

Maak een Batch AI-cluster. In dit voorbeeld bestaat het cluster uit een enkel STANDARD_NC6 VM-knooppunt. Deze VM-grootte heeft één NVIDIA K80-GPU. Koppel de bestandsshare aan een map met de naam *azurefileshare*. Het volledige pad van deze map op het GPU-rekenknooppunt is $AZ_BATCHAI_MOUNT_ROOT/azurefileshare.

```Python
cluster_name = 'mycluster'

relative_mount_point = 'azurefileshare' 
 
parameters = models.ClusterCreateParameters(
    # Location where the cluster will physically be deployed
    location='eastus', 
 
    # VM size. Use NC or NV series for GPU
    vm_size='STANDARD_NC6', 
 
    # Configure the ssh users
    user_account_settings=models.UserAccountSettings(
         admin_user_name=admin_user_name,
         admin_user_password=admin_user_password), 
 
    # Number of VMs in the cluster
    scale_settings=models.ScaleSettings(
         manual=models.ManualScaleSettings(target_node_count=1)
     ), 
 
    # Configure each node in the cluster
    node_setup=models.NodeSetup( 
 
        # Mount shared volumes to the host
         mount_volumes=models.MountVolumes(
             azure_file_shares=[
                 models.AzureFileShareReference(
                     account_name=storage_account_name,
                     credentials=models.AzureStorageCredentialsInfo(
         account_key=storage_account_key),
         azure_file_url='https://{0}.file.core.windows.net/{1}'.format(
               storage_account_name, azure_file_share_name),
                  relative_mount_path = relative_mount_point)],
         ), 
    ), 
) 
batchai_client.clusters.create(resource_group_name, cluster_name, parameters).result() 
```

## <a name="get-cluster-status"></a>Clusterstatus ophalen

Controleer de clusterstatus met behulp van de volgende opdracht: 

```Python
cluster = batchai_client.clusters.get(resource_group_name, cluster_name)
print('Cluster state: {0} Target: {1}; Allocated: {2}; Idle: {3}; '
      'Unusable: {4}; Running: {5}; Preparing: {6}; leaving: {7}'.format(
    cluster.allocation_state,
    cluster.scale_settings.manual.target_node_count,
    cluster.current_node_count,
    cluster.node_state_counts.idle_node_count,
    cluster.node_state_counts.unusable_node_count,
    cluster.node_state_counts.running_node_count,
    cluster.node_state_counts.preparing_node_count,
    cluster.node_state_counts.leaving_node_count)) 
```

Met de vorige code worden basisgegevens over de clustertoewijzing afgedrukt, bijvoorbeeld:

```Shell
Cluster state: AllocationState.steady Target: 1; Allocated: 1; Idle: 0; Unusable: 0; Running: 0; Preparing: 1; Leaving 0
 
```  

Het cluster is gereed wanneer de knooppunten zijn toegewezen en de voorbereiding is voltooid (zie het kenmerk `nodeStateCounts`). Als er iets is misgegaan, bevat het kenmerk `errors` de foutbeschrijving.

## <a name="create-training-job"></a>Trainingstaak maken

Nadat het cluster gereed is, configureert en verzendt u de Learning-taak. 

```Python
job_name = 'myjob' 
 
parameters = models.job_create_parameters.JobCreateParameters( 
 
     # Location where the job will run
     # Ideally this should be co-located with the cluster.
     location='eastus', 
 
     # The cluster this job will run on
     cluster=models.ResourceId(cluster.id), 
 
     # The number of VMs in the cluster to use
     node_count=1, 
 
     # Override the path where the std out and std err files will be written to.
     # In this case we will write these out to an Azure Files share
     std_out_err_path_prefix='$AZ_BATCHAI_MOUNT_ROOT/{0}'.format(relative_mount_point), 
 
     input_directories=[models.InputDirectory(
         id='SAMPLE',
         path='$AZ_BATCHAI_MOUNT_ROOT/{0}/{1}'.format(relative_mount_point, mnist_dataset_directory))], 
 
     # Specify directories where files will get written to 
     output_directories=[models.OutputDirectory(
        id='MODEL',
        path_prefix='$AZ_BATCHAI_MOUNT_ROOT/{0}'.format(relative_mount_point),
        path_suffix="Models")], 
 
     # Container configuration
     container_settings=models.ContainerSettings(
        models.ImageSourceRegistry(image='microsoft/cntk:2.1-gpu-python3.5-cuda8.0-cudnn6.0')), 
 
     # Toolkit specific settings
     cntk_settings = models.CNTKsettings(
        python_script_file_path='$AZ_BATCHAI_INPUT_SAMPLE/ConvNet_MNIST.py',
        command_line_args='$AZ_BATCHAI_INPUT_SAMPLE $AZ_BATCHAI_OUTPUT_MODEL')
 ) 
 
# Create the job 
batchai_client.jobs.create(resource_group_name, job_name, parameters).result() 
```

## <a name="monitor-job"></a>Taak controleren
U kunt de taakstatus achterhalen met behulp van de volgende opdracht: 
 
```Python
job = batchai_client.jobs.get(resource_group_name, job_name) 
 
print('Job state: {0} '.format(job.execution_state.name))
```

De uitvoer is vergelijkbaar met: `Job state: running`.

De `executionState` bevat de huidige uitvoeringsstatus van de taak:
* `queued`: de taak wacht tot de clusterknooppunten beschikbaar zijn
* `running`: de taak wordt uitgevoerd
* `succeeded` (of `failed`): de taak is voltooid en `executionInfo` bevat details over het resultaat
 
## <a name="list-stdout-and-stderr-output"></a>Stdout- en stderr-uitvoer weergeven
Gebruik de volgende opdracht om koppelingen weer te geven naar de stdout- en stderr-logboekbestanden:

```Python
files = batchai_client.jobs.list_output_files(resource_group_name, job_name, models.JobsListOutputFilesOptions("stdouterr")) 
 
for file in list(files):
     print('file: {0}, download url: {1}'.format(file.name, file.download_url)) 
```
## <a name="delete-resources"></a>Resources verwijderen

Gebruik de volgende opdracht om de taak te verwijderen:
```Python
batchai_client.jobs.delete(resource_group_name, job_name) 
```

Gebruik de volgende opdracht om het cluster te verwijderen:
```Python
batchai_client.clusters.delete(resource_group_name, cluster_name)
```
## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u geleerd hoe u een CNTK-trainingstaak kunt uitvoeren op een Batch AI-cluster met behulp van de Azure Python SDK. Zie de [trainingsrecepten](https://github.com/Azure/BatchAI) voor meer informatie over het gebruik van Batch AI met verschillende toolkits.
