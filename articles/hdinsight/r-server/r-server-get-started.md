---
title: Aan de slag met R Server op HDInsight - Azure | Microsoft Docs
description: Informatie over hoe u een Apache Spark op HDInsight-cluster maakt dat R-Server omvat en over het verzenden van een R-script op het cluster.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: b5e111f3-c029-436c-ba22-c54a4a3016e3
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/23/2018
ms.author: nitinme
ms.openlocfilehash: d6910ab257312626ca25126721410edeed6cdeae
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2018
---
# <a name="get-started-with-r-server-cluster-on-azure-hdinsight"></a>Aan de slag met R Server-cluster in Azure HDInsight

Azure HDInsight bevat een R Server-optie die kan worden geïntegreerd in uw HDInsight-cluster. Met deze optie kunnen R-scripts gebruikmaken van Spark en MapReduce om gedistribueerde berekeningen uit te voeren. In dit artikel leest u hoe u R Server op een HDInsight-cluster kunt maken. Vervolgens leert u hoe u een R-script uitvoert waarmee het gebruik van Spark voor gedistribueerde R-berekeningen wordt gedemonstreerd.


## <a name="prerequisites"></a>Vereisten

* **Een Azure-abonnement**: voordat u aan deze zelfstudie begint, moet u beschikken over een Azure-abonnement. Zie [Get Microsoft Azure free trial](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/) (Gratis proefversie van Microsoft Azure downloaden) voor meer informatie.
* **Een SSH-client (Secure Shell)**: er wordt een SSH-client gebruikt om extern verbinding te maken met het HDInsight-cluster en om opdrachten rechtstreeks uit te voeren op het cluster. Zie [SSH gebruiken met HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) voor meer informatie.


<a name="create-hdi-custer-with-aure-portal"></a>
## <a name="create-the-cluster-using-the-azure-portal"></a>Het cluster maken met Azure Portal

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

2. Klik op **Een resource maken** > **Gegevens en analyses** > **HDInsight**.

3. Voer bij **Basisbeginselen** de volgende informatie in:

    * **Clusternaam**: de naam van het HDInsight-cluster.
    * **Abonnement**: selecteer het abonnement dat u wilt gebruiken.
    * **Aanmeldingsgebruikersnaam** en -**wachtwoord** van cluster: de aanmeldingsgegevens voor toegang tot het cluster via HTTPS. U gebruikt deze referenties voor toegang tot services als de Ambari-webinterface of de REST-API.
    * **SSH-gebruikersnaam (Secure Shell)**: de aanmeldingsgegevens voor toegang tot het cluster via SSH. Het wachtwoord is standaard hetzelfde als het aanmeldingswachtwoord van het cluster.
    * **Resourcegroep**: de resourcegroep waarin het cluster wordt gemaakt.
    * **Locatie**: de Azure-regio waarin het cluster wordt gemaakt.

        ![Basisgegevens van clusters](./media/r-server-get-started/clustername.png)

4. Selecteer **Clustertype** en stel de volgende waarden in op de sectie **Clusterconfiguratie**:

    * **Clustertype**: R Server

    * **Besturingssysteem**: Linux

    * **Versie**: R Server 9.1 (HDI 3.6). Releaseopmerkingen voor de beschikbare versies van R Server vindt u op [docs.microsoft.com](https://docs.microsoft.com/machine-learning-server/whats-new-in-r-server#r-server-91).

    * **R Studio Community-editie voor R Server**: deze IDE op basis van een browser wordt standaard geïnstalleerd op het edge-knooppunt. Schakel het selectievakje uit als u de IDE niet wilt installeren. Als u ervoor kiest deze wel te laten installeren, is de URL naar de aanmeldingspagina voor RStudio Server beschikbaar op een portaltoepassingsblade voor het cluster nadat het is gemaakt.

        ![Basisgegevens van clusters](./media/r-server-get-started/clustertypeconfig.png)

4. Nadat u het clustertype hebt geselecteerd, gebruikt u de knop __Selecteren__ om het clustertype te selecteren. Gebruik vervolgens de knop __Volgende__ om de basisconfiguratie te voltooien.

5. In de sectie **Opslag** selecteert of maakt u een opslagaccount. Voor de stappen in dit document gebruikt u voor de andere velden in deze sectie de standaardwaarden. Gebruik de knop __Volgende__ om de opslagconfiguratie op te slaan.

    ![De instellingen van het opslagaccount voor HDInsight configureren](./media/r-server-get-started/cluster-storage.png)

6. Controleer in de sectie **Samenvatting** de configuratie van het cluster. Gebruik de koppeling __Bewerken__ om onjuiste instellingen te wijzigen. Gebruik tot slot de knop __Maken__ om het cluster te maken.

    ![De instellingen van het opslagaccount voor HDInsight configureren](./media/r-server-get-started/clustersummary.png)

    > [!NOTE]
    > Het kan tot 20 minuten duren om het cluster te maken.

<a name="connect-to-rstudio-server"></a>
## <a name="connect-to-rstudio-server"></a>Verbinding maken met RStudio Server

Als u ervoor kiest RStudio Server Community Edition te installeren als onderdeel van uw HDInsight-cluster, kunt u zich op een van de volgende twee methoden aanmelden bij RStudio:

* **Optie 1**: ga naar de volgende URL (waarbij **CLUSTERNAAM** de naam is van het R Server-cluster dat u hebt gemaakt):

        https://CLUSTERNAME.azurehdinsight.net/rstudio/

* **Optie 2**: open het R Server-cluster in Azure Portal onder **Snelkoppelingen** en klik op **R Server-dashboards**.

     ![De instellingen van het opslagaccount voor HDInsight configureren](./media/r-server-get-started/dashboard-quick-links.png)

    Klik vanuit **Clusterdashboards** op **R Studio Server**.

    ![De instellingen van het opslagaccount voor HDInsight configureren](./media/r-server-get-started/r-studio-server-dashboard.png)

   > [!IMPORTANT]
   > Ongeacht welke methode u kiest, de eerste keer dat u zich aanmeldt moet u zich twee keer verifiëren.  Bij de eerste verificatieprompt geeft u de *gebruikers-id* en het *wachtwoord* voor de beheerder op voor het cluster. Bij de tweede verificatieprompt geeft u de *gebruikers-id* en het *wachtwoord* voor SSH op. Bij alle volgende aanmeldingen zijn alleen SSH-referenties vereist.

Zodra u verbonden bent, moet het scherm vergelijkbaar zijn met de volgende schermafbeelding:

![Verbinding maken met RStudio](./media/r-server-get-started/connect-to-r-studio.png)

## <a name="run-a-sample-job"></a>Een voorbeeldtaak uitvoeren

U kunt een taak met ScaleR-functies verzenden. Hier volgt een voorbeeld van de opdrachten om een taak uit te voeren:

    # Set the HDFS (WASB) location of example data.
    bigDataDirRoot <- "/example/data"

    # Create a local folder for storaging data temporarily.
    source <- "/tmp/AirOnTimeCSV2012"
    dir.create(source)

    # Download data to the tmp folder.
    remoteDir <- "https://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012"
    download.file(file.path(remoteDir, "airOT201201.csv"), file.path(source, "airOT201201.csv"))
    download.file(file.path(remoteDir, "airOT201202.csv"), file.path(source, "airOT201202.csv"))
    download.file(file.path(remoteDir, "airOT201203.csv"), file.path(source, "airOT201203.csv"))
    download.file(file.path(remoteDir, "airOT201204.csv"), file.path(source, "airOT201204.csv"))
    download.file(file.path(remoteDir, "airOT201205.csv"), file.path(source, "airOT201205.csv"))
    download.file(file.path(remoteDir, "airOT201206.csv"), file.path(source, "airOT201206.csv"))
    download.file(file.path(remoteDir, "airOT201207.csv"), file.path(source, "airOT201207.csv"))
    download.file(file.path(remoteDir, "airOT201208.csv"), file.path(source, "airOT201208.csv"))
    download.file(file.path(remoteDir, "airOT201209.csv"), file.path(source, "airOT201209.csv"))
    download.file(file.path(remoteDir, "airOT201210.csv"), file.path(source, "airOT201210.csv"))
    download.file(file.path(remoteDir, "airOT201211.csv"), file.path(source, "airOT201211.csv"))
    download.file(file.path(remoteDir, "airOT201212.csv"), file.path(source, "airOT201212.csv"))

    # Set directory in bigDataDirRoot to load the data.
    inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

    # Create the directory.
    rxHadoopMakeDir(inputDir)

    # Copy the data from source to input.
    rxHadoopCopyFromLocal(source, bigDataDirRoot)

    # Define the HDFS (WASB) file system.
    hdfsFS <- RxHdfsFileSystem()

    # Create info list for the airline data.
    airlineColInfo <- list(
    DAY_OF_WEEK = list(type = "factor"),
    ORIGIN = list(type = "factor"),
    DEST = list(type = "factor"),
    DEP_TIME = list(type = "integer"),
    ARR_DEL15 = list(type = "logical"))

    # Get all the column names.
    varNames <- names(airlineColInfo)

    # Define the text data source in HDFS.
    airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)

    # Define the text data source in local system.
    airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)

    # Specify the formula to use.
    formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"

    # Define the Spark compute context.
    mySparkCluster <- RxSpark()

    # Set the compute context.
    rxSetComputeContext(mySparkCluster)

    # Run a logistic regression.
    system.time(
        modelSpark <- rxLogit(formula, data = airOnTimeData)
    )

    # Display a summary.
    summary(modelSpark)

<a name="connect-to-edge-node"></a>
## <a name="connect-to-the-cluster-edge-node"></a>Verbinding maken met het Edge-knooppunt van het cluster

In deze sectie leert u hoe verbinding maakt met het Edge-knooppunt van een R Server HDInsight-cluster via SSH. Zie [SSH gebruiken met HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) voor meer informatie over het gebruik van SSH.

De SSH-opdracht voor het maken van verbinding met het Edge-knooppunt van het R Server-cluster is:

   `ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net`

Om te zoeken naar de SSH-opdracht voor uw cluster klikt u in Azure Portal op de clusternaam, klikt u op **SSH- + clusteraanmeldgegevens** en selecteert u vervolgens voor **Hostnaam** het Edge-knooppunt. U ziet dan de SSH-eindpuntgegevens van het Edge-knooppunt.

![Afbeelding van het SSH-eindpunt voor het Edge-knooppunt](./media/r-server-get-started/sshendpoint.png)

Als u een wachtwoord hebt gebruikt om uw SSH gebruikersaccount te beveiligen, wordt u gevraagd het wachtwoord in te voeren. Als u een openbare sleutel hebt gebruikt, moet u mogelijk de `-i`-parameter gebruiken om de overeenkomende persoonlijke sleutel op te geven. Bijvoorbeeld:

    ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

Zodra er verbinding is gemaakt, wordt er een prompt weergegeven die er ongeveer als volgt uitziet:

    sshuser@ed00-myrclu:~$

<a name="use-r-console"></a>
## <a name="use-the-r-server-console"></a>De R Server-console gebruiken

1. Gebruik tijdens de SSH-sessie de volgende opdracht om de R-console te starten:  

        R

2. U moet uitvoer zien die naast andere informatie de versie van R Server bevat.
    
3. U kunt de R-code invoeren vanuit de `>`-prompt. R Server in HDInsight bevat pakketten waarmee u eenvoudig kunt werken met Hadoop en gedistribueerde berekeningen kunt uitvoeren. Gebruik bijvoorbeeld de volgende opdracht om de hoofdmap te bekijken van het standaardbestandssysteem voor het HDInsight-cluster:

        rxHadoopListFiles("/")

4. U kunt ook de adressering in WASB-stijl gebruiken.

        rxHadoopListFiles("wasb:///")

5. Gebruik de volgende opdracht om de R-console af te sluiten:

        quit()

## <a name="automated-cluster-creation"></a>Automatisch een cluster maken

U kunt het maken van het R Server-cluster voor HDInsight automatiseren met Azure Resource Manager-sjablonen, de SDK en de PowerShell.

* Zie [Een R Server implementeren voor HDInsight-cluster](https://azure.microsoft.com/resources/templates/101-hdinsight-rserver/) voor het maken van een R Server-cluster met een Azure Resource Manager-sjabloon.
* Zie [In HDInsight op Linux gebaseerde clusters maken met de .NET-SDK](../hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md) voor het maken van een R Server-cluster met behulp van de .NET-SDK.
* Zie het artikel [HDInsight-clusters maken met Azure PowerShell](../hdinsight-hadoop-create-linux-clusters-azure-powershell.md) voor het maken van een R Server-cluster met behulp van Azure PowerShell.

## <a name="delete-the-cluster"></a>Het cluster verwijderen

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Problemen oplossen

Zie [Vereisten voor toegangsbeheer](../hdinsight-administer-use-portal-linux.md#create-clusters) als u problemen ondervindt met het maken van HDInsight-clusters.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u meer geleerd over het maken van een nieuw R Server-cluster in Azure HDInsight en de basisbeginselen van het gebruik van de R-console van een SSH-sessie. In de volgende artikelen worden andere manieren beschreven om R Server op HDInsight te beheren en ermee te werken:

* [Taken verzenden vanuit R-hulpprogramma's voor Visual Studio](r-server-submit-jobs-r-tools-vs.md)
* [R Server-cluster beheren in HDInsight](r-server-hdinsight-manage.md)
* [R Server-cluster uitvoeren in HDInsight](r-server-operationalize.md)
* [Opties voor compute-context voor R Server-cluster in HDInsight](r-server-compute-contexts.md)
* [Opties voor Azure Storage voor R Server-cluster in HDInsight](r-server-storage.md)
