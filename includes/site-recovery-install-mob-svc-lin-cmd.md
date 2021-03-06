1. Het installatieprogramma kopiëren naar een lokale map (bijvoorbeeld map) op de server die u wilt beveiligen. Voer de volgende opdrachten in een terminal:
  ```
  cd /tmp ;

  tar -xvzf Microsoft-ASR_UA*release.tar.gz
  ```
2. Voer de volgende opdracht voor het installeren van de Mobility-Service:

  ```
  sudo ./install -d <Install Location> -r MS -v VmWare -q
  ```
3. Nadat de installatie is voltooid, moet de Mobility-Service zijn geregistreerd met de configuratieserver. Voer de volgende opdracht om de Mobility-Service met de configuratieserver registreren:

  ```
  /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
  ```

#### <a name="mobility-service-installer-command-line"></a>Mobility-Service installer-opdrachtregel

```
Usage:
./install -d <Install Location> -r <MS|MT> -v VmWare -q
```

|Parameter|Type|Beschrijving|Mogelijke waarden|
|-|-|-|-|
|-r |Verplicht|Hiermee geeft u op of de Mobility-Service (MS) moet worden geïnstalleerd of MasterTarget (MT) moet worden geïnstalleerd.|MS </br> MT|
|-d |Optioneel|De locatie waar de Mobility-Service is geïnstalleerd.|/usr/local/ASR|
|-v|Verplicht|Hiermee geeft u het platform waarop de Mobility-Service is geïnstalleerd. </br> </br>- **VMware**: deze waarde wordt gebruikt als u de Mobility-Service op een virtuele machine uitgevoerd installeert op *VMware vSphere ESXi-hosts*, *Hyper-V-hosts*, en *fysieke servers*. </br> - **Azure**: deze waarde wordt gebruikt als u een agent op een virtuele machine van Azure IaaS installeren.| VMware </br> Azure|
|-q|Optioneel|Hiermee geeft u het installatieprogramma uitvoeren in de stille modus.| N/A|


#### <a name="mobility-service-configuration-command-line"></a>Mobility-Service configuration vanaf de opdrachtregel

```
Usage:
cd /usr/local/ASR/Vx/bin
UnifiedAgentConfigurator.sh -i <CSIP> -P <PassphraseFilePath>
```

|Parameter|Type|Beschrijving|Mogelijke waarden|
|-|-|-|-|
|-i |Verplicht|IP-adres van de configuratieserver|Een geldig IP-adres|
|-P |Verplicht|Volledig pad voor het bestand waar de wachtwoordzin op te geven voor het doorgeven van de verbinding wordt opgeslagen|Een geldige map|
