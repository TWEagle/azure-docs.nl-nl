---
title: Een Jenkins-server maken in Azure
description: Op basis van de sjabloon voor de Jenkins-oplossing een virtuele Linux-machine van Azure installeren en een Java-voorbeeldtoepassing bouwen.
author: tomarcher
manager: rloutlaw
ms.service: multiple
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: tarcher
ms.custom: Jenkins
ms.openlocfilehash: c9f86ab2536d3c598bb8c7084524395b41f18db0
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2018
---
# <a name="create-a-jenkins-server-on-an-azure-linux-vm-from-the-azure-portal"></a>Vanuit Azure Portal een Jenkins-server maken op een Azure-VM met Linux

Deze Quick Start laat zien hoe u [Jenkins](https://jenkins.io) installeert op een virtuele machine met Ubuntu Linux met behulp van de hulpprogramma's en invoegtoepassingen die zijn geconfigureerd om te werken met Azure. Wanneer u klaar bent, beschikt u over een Jenkins-server die wordt uitgevoerd in Azure voor het bouwen van een Java-voorbeeld-app uit [GitHub](https://github.com).

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement
* Toegang tot SSH vanaf de opdrachtregel van uw computer (zoals de Bash-shell of [PuTTY](http://www.putty.org/))

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-jenkins-vm-from-the-solution-template"></a>De virtuele machine met Jenkins maken van de oplossingssjabloon
Jenkins ondersteuning biedt voor een model waarin de server gemachtigden op een of meer agents werken waarmee één Jenkins installatie voor het hosten van een groot aantal projecten of om verschillende omgevingen die nodig zijn voor Jenkins bouwt of test. De stappen in deze sectie helpt u bij het installeren en configureren van een Jenkins-server op Azure.

[!INCLUDE [jenkins-install-from-azure-marketplace-image](../../includes/jenkins-install-from-azure-marketplace-image.md)]

## <a name="connect-to-jenkins"></a>Verbinding maken met Jenkins

Navigeer naar de virtuele machine (bijvoorbeeld http://jenkins2517454.eastus.cloudapp.azure.com/) in uw webbrowser. De Jenkins-console is niet toegankelijk via onbeveiligde HTTP. Om die reden bevat de pagina instructies om de console veilig vanaf uw computer te gebruiken via een SSH-tunnel.

![Jenkins ontgrendelen](./media/install-jenkins-solution-template/jenkins-ssh-instructions.png)

Stel de tunnel op de pagina in met behulp van de opdracht `ssh` op de opdrachtregel. Vervang `username` hierbij door de naam van de gebruiker met beheerdersrechten van de virtuele machine die u eerder hebt gekozen bij het instellen van de virtuele machine op basis van de oplossingssjabloon.

```bash
ssh -L 127.0.0.1:8080:localhost:8080 jenkinsadmin@jenkins2517454.eastus.cloudapp.azure.com
```

Nadat u de tunnel hebt gestart, gaat u naar http://localhost:8080/ op uw lokale computer. 

Vraag het initiële wachtwoord op door de volgende opdracht uit te voeren op de opdrachtregel terwijl u via SSH bent verbonden met de Jenkins-VM.

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Gebruik dit initiële wachtwoord om het Jenkins-dashboard voor de eerste keer te ontgrendelen.

![Jenkins ontgrendelen](./media/install-jenkins-solution-template/jenkins-unlock.png)

Selecteer **Install suggested plugins** op de volgende pagina en maak vervolgens een Jenkins-gebruiker met beheerdersrechten om toegang te krijgen tot het Jenkins-dashboard.

![Jenkins is klaar.](./media/install-jenkins-solution-template/jenkins-welcome.png)

De Jenkins-server is nu klaar voor het bouwen van code.

## <a name="create-your-first-job"></a>Uw eerste taak maken

Selecteer **Create new jobs** in de Jenkins-console, geef het project de naam **mySampleApp**, selecteer **Freestyle project** en selecteer **OK**.

![Een nieuwe taak maken](./media/install-jenkins-solution-template/jenkins-new-job.png) 

Selecteer het tabblad **Source Code Management**, schakel het keuzerondje **Git** in en voer in het veld **Repository URL** de volgende URL in: `https://github.com/spring-guides/gs-spring-boot.git`

![De Git-opslagplaats definiëren](./media/install-jenkins-solution-template/jenkins-job-git-configuration.png) 

Selecteer het tabblad **Build** en selecteer vervolgens **Add build step**, **Invoke Gradle script**. Selecteer **Use Gradle Wrapper** en voer vervolgens `complete` in bij **Wrapper location** en `build` bij **Tasks**.

![De Gradle-wrapper gebruiken om de code te bouwen](./media/install-jenkins-solution-template/jenkins-job-gradle-config.png) 

Selecteer **Advanced..** en voer vervolgens in het veld **Root Build script** de waarde `complete` in. Selecteer **Opslaan**.

![Geavanceerde instellingen opgeven in de stap voor het bouwen van de Gradle-wrapper](./media/install-jenkins-solution-template/jenkins-job-gradle-advances.png) 

## <a name="build-the-code"></a>De code bouwen

Selecteer **Build Now** om de code te compileren en een pakket te maken van de voorbeeld-app. Als de build is voltooid, selecteert u de koppeling **Workspace** voor het project.

![Bladeren naar de werkruimte om het JAR-bestand van de build te zoeken](./media/install-jenkins-solution-template/jenkins-access-workspace.png) 

Navigeer naar `complete/build/libs` en controleer of u daar het bestand `gs-spring-boot-0.1.0.jar` ziet, zodat u weet dat de build is gelukt. De Jenkins-server is nu gereed voor het bouwen van uw eigen projecten in Azure.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Virtuele Azure-machines toevoegen als Jenkins-agents](jenkins-azure-vm-agents.md)
