1. Het installatieprogramma kopiëren naar een lokale map (bijvoorbeeld C:\Temp) op de server die u wilt beveiligen. Voer de volgende opdrachten uit als beheerder bij een opdrachtprompt:

  ```
  cd C:\Temp
  ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
  MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted.
  ```
2. Voer de volgende opdracht voor het installeren van de Mobility-Service:

  ```
  UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
  ```
3. Nu moet de agent worden geregistreerd met de configuratieserver.

  ```
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe”  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
  ```

#### <a name="mobility-service-installer-command-line-arguments"></a>Opdrachtregelargumenten van de Mobility-Service-installatieprogramma

```
Usage :
UnifiedAgent.exe /Role <MS|MT> /InstallLocation <Install Location> /Platform “VmWare” /Silent
```

| Parameter|Type|Beschrijving|Mogelijke waarden|
|-|-|-|-|
|/ Functie|Verplicht|Hiermee geeft u op of de Mobility-Service (MS) moet worden geïnstalleerd of MasterTarget (MT) moet worden geïnstalleerd.|MS </br> MT|
|/InstallLocation|Optioneel|De locatie waar de Mobility-Service is geïnstalleerd.|Een map op de computer|
|/Platform|Verplicht|Hiermee geeft u het platform waarop de Mobility-Service is geïnstalleerd. </br> </br>- **VMware**: deze waarde wordt gebruikt als u de Mobility-Service op een virtuele machine uitgevoerd installeert op *VMware vSphere ESXi-hosts*, *Hyper-V-hosts*, en *fysieke servers*. </br> - **Azure**: deze waarde wordt gebruikt als u een agent op een virtuele machine van Azure IaaS installeren. | VMware </br> Azure|
|/ Silent|Optioneel|Hiermee geeft u het installatieprogramma uitvoeren in de stille modus.| N/A|

>[!TIP]
> De setup-Logboeken vindt u onder % ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log.

#### <a name="mobility-service-registration-command-line-arguments"></a>Opdrachtregelargumenten van de Mobility-Service-registratie

```
Usage :
UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
```

  | Parameter|Type|Beschrijving|Mogelijke waarden|
  |-|-|-|-|
  |/CSEndPoint |Verplicht|IP-adres van de configuratieserver| Een geldig IP-adres|
  |/PassphraseFilePath|Verplicht|Locatie van de wachtwoordzin |Een geldig UNC- of lokale bestandspad|


>[!TIP]
> De agentconfiguratie Logboeken vindt u onder % ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log.
