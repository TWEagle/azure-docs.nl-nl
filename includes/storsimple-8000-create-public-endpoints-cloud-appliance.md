#### <a name="to-create-public-endpoints-on-the-cloud-appliance"></a>Openbare eindpunten op het cloudapparaat maken

1. Meld u aan bij Azure Portal.
2. Ga naar **Virtuele machines** en selecteer en klik op de virtuele machine die wordt gebruikt als uw cloudapparaat.
    
3. U moet een netwerkbeveiligingsgroepregel (NSG) voor het beheren van de verkeersstroom in en uit uw virtuele machine maken. Voer de volgende stappen uit voor het maken van een NSG-regel.
    1. Selecteer **Netwerkbeveiligingsgroep**.
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt1.png)

    2. Klik op de standaardnetwerkbeveiligingsgroep die wordt weergegeven.
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt2.png)

    3. Selecteer **Inkomende beveiligingsregels**.
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt3.png)

    4. Klik op **Toevoegen** om een Inkomende beveiligingsregel te maken.
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt4.png)

        Doe het volgende op de blade Inkomende beveiligingsregel toevoegen:

        1. Typ voor **Naam** de volgende naam voor het eindpunt: WinRMHttps.
        
        2. Selecteer voor de **Prioriteit** een getal dat kleiner is dan 1000 (dit is de prioriteit voor de standaardregel). Hoe hoger de waarde, hoe lager de prioriteit.

        3. Stel de **Bron** in op **Willekeurig**.

        4. Voor de **Service** selecteert u **WinRM**. Het **Protocol** wordt automatisch ingesteld op **TCP** en het **Poortbereik** wordt ingesteld op **5986**.

        5. Klik op **OK** om de regel te maken.

            ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt5.png)

4. De laatste stap bestaat uit het koppelen van uw netwerkbeveiligingsgroep aan een subnet of een specifieke netwerkinterface. Voer de volgende stappen uit om uw netwerkbeveiligingsgroep te koppelen met een subnet.
    1. Ga naar **Subnetten**.
    2. Klik op **Koppelen**.
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt7.png)

    3. Selecteer het virtuele netwerk en selecteer vervolgens het juiste subnet.
    4. Klik op **OK** om de regel te maken.

        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt11.png)

Nadat de regel is gemaakt, kunt u de details van het eindpunt weergeven om het openbare VIP-adres (virtueel IP-adres) te bepalen. Noteer dit adres.


