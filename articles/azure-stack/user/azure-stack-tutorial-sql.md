---
title: Maximaal beschikbare SQL-databases maken in Azure Stack | Microsoft Docs
description: Meer informatie over het maken van een resourceprovider van SQL Server-hostcomputer en maximaal beschikbare SQL AlwaysOn-databases met Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/18/2018
ms.author: jeffgilb
ms.reviewer: quying
ms.openlocfilehash: b353b1c359ea663201b3fa5a72b28ab3298acd09
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/19/2018
ms.locfileid: "46369160"
---
# <a name="tutorial-create-highly-available-sql-databases"></a>Zelfstudie: Een maximaal beschikbare SQL-databases maken

Als de gebruiker van een Azure Stack-tenant, kunt u server-machines naar host SQL Server-databases configureren. Na een SQL host-server met succes is gemaakt en beheerd door Azure Stack, SQL-databases eenvoudig door gebruikers die zijn geabonneerd op SQL-services kunnen maken.

In deze zelfstudie ziet u hoe u een Azure Stack-quickstart-sjabloon maken een [SQL Server AlwaysOn-beschikbaarheidsgroep](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server?view=sql-server-2017), toe te voegen als een Azure Stack SQL die als host fungeert Server, en maak vervolgens een maximaal beschikbare SQL-database.

Wat u leert:

> [!div class="checklist"]
> * Een SQL Server AlwaysOn-beschikbaarheidsgroep maken met een sjabloon
> * Een Azure Stack-Server voor het hosten van SQL maken
> * Een maximaal beschikbare SQL-database maken

In deze zelfstudie wordt een twee VM SQL Server AlwaysOn-beschikbaarheidsgroep wordt gemaakt en geconfigureerd met behulp van de beschikbare Azure Stack marketplace-items. 

Voordat u de stappen in deze zelfstudie begint, zorg ervoor dat de Azure Stack-Operator heeft de volgende items beschikbaar gesteld in de Azure Stack marketplace:

> [!IMPORTANT]
> Alle van de volgende opties zijn vereist voor de Azure Stack-quickstart-sjabloon moet worden gebruikt.

- [Windows Server 2016 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WindowsServer) marketplace-installatiekopie.
- SQL Server 2016 SP1 of SP2 (Enterprise, Standard of ontwikkelaar) op Windows Server 2016 server-installatiekopie. In deze zelfstudie wordt de [SQL Server 2016 SP2 Enterprise op Windows Server 2016](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft.sqlserver2016sp2enterprisewindowsserver2016) marketplace-installatiekopie.
- [SQL Server IaaS-extensie](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension) versie 1.2.30 of hoger. De SQL IaaS-extensie installeert de vereiste onderdelen die voor de Marketplace SQL Server-items voor alle Windows-versies vereist zijn. Hiermee kunt specifieke SQL-instellingen worden geconfigureerd op SQL-machines. Als de extensie is niet geïnstalleerd op de lokale markt, mislukt inrichting van SQL.
- [Aangepaste scriptextensie voor Windows](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.CustomScriptExtension) versie 1.9.1 of hoger. Aangepaste Scriptextensie is een hulpprogramma dat kan worden gebruikt voor het automatisch starten na de implementatie van VM-aanpassingstaken.
- [PowerShell Desired State Configuration (DSC)](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.DSC-arm) versie 2.76.0.0 of hoger. DSC is een beheerplatform in Windows PowerShell waarmee implementeren en beheren van configuratiegegevens voor softwareservices en beheren van de omgeving waarin de services worden uitgevoerd.

  > [!TIP]
  > U niet mogelijk om te zien van de SQL Server IaaS-extensie, de extensie voor aangepaste scripts of de PowerSHell DSC marketplace-items bij het maken van een virtuele machine van de gebruikersportal. Neem contact op met uw Azure Stack-operators om ervoor te zorgen dat deze items zijn gedownload van Azure voordat u begint met de stappen in deze zelfstudie.

Zie voor meer informatie over het toevoegen van items aan de Azure Stack marketplace, de [overzicht van Azure Stack Marketplace](.\.\azure-stack-marketplace.md).

## <a name="create-a-sql-server-alwayson-availability-group"></a>Een SQL Server AlwaysOn-beschikbaarheidsgroep maken
Gebruik de stappen in deze sectie voor het implementeren van de SQL Server AlwaysOn-beschikbaarheidsgroep met behulp van de [sqlalwayson-2016 Azure Stack-snelstartsjabloon](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sql-2016-alwayson). Deze sjabloon implementeert twee SQL Server Enterprise, Standard- of ontwikkelaar-exemplaren in een AlwaysOn-beschikbaarheidsgroep. Met deze sjabloon maakt u de volgende resources:

- Een netwerkbeveiligingsgroep
- Een virtueel netwerk
- Vier storage-accounts (één voor Active Directory (AD), één voor SQL, één voor de bestandsshare-witness en regel VM diagnostics)
- Vier openbare IP-adressen (één voor AD twee voor elke SQL-VM en één voor openbare load balancer is gebonden aan de SQL AlwaysOn-listener)
- Één externe load balancer voor SQL-VM's met openbare IP-adres dat is gebonden aan de SQL AlwaysOn-listener
- Een VM (Windows Server 2016) geconfigureerd als domeincontroller voor een nieuw forest met één domein
- Twee virtuele machines (Windows Server 2016) geconfigureerd met SQL Server 2016 SP1 of SP2 Enterprise, Standard en Developer Edition en geclusterd. Deze moeten marketplace-installatiekopieën worden.
- Een VM zijn geconfigureerd als de bestandsshare-witness voor het cluster (Windows Server 2016)
- Een beschikbaarheidsset met de SQL- en bestandsshare-witness VM 's  

> [!NOTE]
> Deze stappen van de gebruikersportal van Azure Stack uitgevoerd als een tenant-gebruiker met een abonnement voor het leveren van IaaS-mogelijkheden (compute, netwerk, opslagservices).

1. Meld u aan bij de gebruikersportal aanmeldt:
    - Voor de implementatie van een geïntegreerd systeem, wordt de portal-adres variëren, afhankelijk van de regio en externe domeinnaam van uw oplossing. Dit is in de indeling van https://portal.&lt; *regio*&gt;.&lt; *FQDN*&gt;.
    - Als u de Azure Stack Development Kit (ASDK), wordt de gebruiker portal adres [ https://portal.local.azurestack.external ](https://portal.local.azurestack.external).

2. Selecteer **\+** **een resource maken** > **aangepaste**, en vervolgens **sjabloonimplementatie**.

   ![Implementatie van de aangepaste sjabloon](media/azure-stack-tutorial-sqlrp/custom-deployment.png)


3. Op de **aangepaste implementatie** Selecteer **template bewerken** > **Quickstart-sjabloon** en vervolgens de vervolgkeuzelijst met beschikbare aangepaste sjablonen te gebruiken Selecteer de **sqlalwayson-2016** sjabloon, klikt u op **OK**, en vervolgens **opslaan**.

   ![Quickstart-sjabloon selecteren](./media/azure-stack-tutorial-sqlrp/quickstart-template.png)


4. Op de **aangepaste implementatie** Selecteer **parameters bewerken** en bekijk de standaardwaarden. Wijzig de waarden die nodig is voor de vereiste parameterinformatie te verstrekken en klik vervolgens op **OK**.<br><br> Ten minste:

    - Complexe wachtwoorden voor de parameters ADMINPASSWORD SQLSERVERSERVICEACCOUNTPASSWORD en SQLAUTHPASSWORD opgeven.
    - Voer het DNS-achtervoegsel voor reverse lookup in kleine letters voor de parameter DNSSUFFIX (**azurestack.external** voor installaties ASDK).
    
    ![Aangepaste implementatie-parameters](./media/azure-stack-tutorial-sqlrp/edit-parameters.png)

5. Op de **aangepaste implementatie** blade, kiest u het abonnement te gebruiken en maak een nieuwe resourcegroep of Selecteer een bestaande resourcegroep voor de aangepaste implementatie.<br><br> Selecteer vervolgens de locatie van de resourcegroep (**lokale** voor installaties ASDK) en klik vervolgens op **maken**. De aangepaste implementatie-instellingen worden gevalideerd en vervolgens de implementatie wordt gestart.

    ![Aangepaste implementatie-parameters](./media/azure-stack-tutorial-sqlrp/create-deployment.png)


6. Selecteer in de gebruikersportal **resourcegroepen** en vervolgens de naam van de resourcegroep waarin u hebt gemaakt voor de aangepaste implementatie (gewoon **resourcegroep** voor dit voorbeeld). Bekijk de status van de implementatie om te controleren of dat alle implementaties zijn voltooid.<br><br>Vervolgens bekijkt u de resource-group-items en selecteer de **SQLPIPsql\<groepsnaam voor accountresources\>**  openbaar IP-adres-item. Noteer het openbare IP-adres en de volledige FQDN-naam van de load balancer openbaar IP-adres. U moet dit aan een Azure Stack-Operator bieden, zodat ze een SQL-hostserver gebruik te maken van dit SQL AlwaysOn-beschikbaarheidsgroep kunnen maken.

   > [!NOTE]
   > De sjabloonimplementatie duurt enkele uren.

   ![Aangepaste implementatie-parameters](./media/azure-stack-tutorial-sqlrp/deployment-complete.png)

### <a name="enable-automatic-seeding"></a>Automatische seeding inschakelen
Nadat de sjabloon is geconfigureerd de SQL AlwaysON-beschikbaarheidsgroep en is geïmplementeerd, moet u inschakelen [automatische seeding](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group) op elk exemplaar van SQL Server in de beschikbaarheidsgroep. 

Wanneer u een beschikbaarheidsgroep maakt met automatische seeding, maakt SQL Server automatisch de secundaire replica's voor elke database in de groep zonder andere handmatige interventie nodig zijn om te zorgen voor hoge beschikbaarheid van AlwaysOn-databases.

Deze SQL-opdrachten gebruiken voor het configureren van automatische seeding voor de AlwaysOn-beschikbaarheidsgroep.

Op het primaire SQL-exemplaar (Vervang <InstanceName> met de naam van het primaire exemplaar SQL Server):

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>]
      MODIFY REPLICA ON '<InstanceName>'
      WITH (SEEDING_MODE = AUTOMATIC)
  GO
  ```

>  ![Primaire exemplaar van SQL-script](./media/azure-stack-tutorial-sqlrp/sql1.png)

Op de secundaire SQL-exemplaren (< availability_group_name > vervangen door de naam van de beschikbaarheidsgroep AlwaysOn):

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>] GRANT CREATE ANY DATABASE
  GO
  ```
>  ![Secundaire SQL-exemplaar-script](./media/azure-stack-tutorial-sqlrp/sql2.png)

### <a name="configure-contained-database-authentication"></a>Ingesloten database-verificatie configureren
Voordat u een ingesloten database aan een beschikbaarheidsgroep toevoegt, zorg ervoor dat de ingesloten database authentication server-optie is ingesteld op 1 op elke serverexemplaar dat als host fungeert voor een beschikbaarheidsreplica voor de beschikbaarheidsgroep. Zie voor meer informatie, [databaseverificatie](https://docs.microsoft.com/en-us/sql/database-engine/configure-windows/contained-database-authentication-server-configuration-option?view=sql-server-2017).

Deze opdrachten gebruiken om in te stellen van de ingesloten database-server-optie voor verificatie voor elk exemplaar van SQL Server in de beschikbaarheidsgroep:

  ```sql
  EXEC sp_configure 'contained database authentication', 1
  GO
  RECONFIGURE
  GO
  ```
>  ![Database-verificatie instellen die zijn opgenomen](./media/azure-stack-tutorial-sqlrp/sql3.png)

## <a name="create-an-azure-stack-sql-hosting-server"></a>Een Azure Stack-Server voor het hosten van SQL maken
Nadat de AlwayOn van SQL Server-beschikbaarheidsgroep is gemaakt en geconfigureerd, moet een Azure Stack SQL die als host fungeert Server zodat de capaciteit beschikbaar voor gebruikers om databases te maken maken in een Azure Stack-Operator. 

Zorg ervoor dat u de Azure Stack-Operator de openbaar IP-adres of de volledige FQDN-naam opgeven voor het openbare IP-adres van de SQL-load balancer die was eerder vastgelegd wanneer de resourcegroep van de SQL AlwaysOn-beschikbaarheidsgroep is gemaakt (**SQLPIPsql\<resource naam van groep\>**). Bovendien moet de operator de referenties voor verificatie gebruikt voor toegang tot de SQL-exemplaren in de AlwaysOn-beschikbaarheidsgroep SQL-Server weten.

> [!NOTE]
> Deze stap moet worden uitgevoerd vanaf de Azure Stack-beheerportal door een Azure Stack-Operator.

Met de SQL AlwaysOn-beschikbaarheidsgroep load balancer listener openbaar IP-adres en de SQL-aanmelding verificatiegegevens opgegeven door de tenant-gebruiker, een Azure Stack-Operator kunnen nu [maken van een SQL host-Server met behulp van de groep SQL AlwaysOn-beschikbaarheid ](.\.\azure-stack-sql-resource-provider-hosting-servers.md#provide-high-availability-using-sql-always-on-availability-groups). 

Ook voor zorgen dat de Azure Stack-Operator heeft gemaakt plannen en aanbiedingen voor het maken van de SQL AlwaysOn-database beschikbaar maken voor gebruikers. De operator moet toevoegen de **Microsoft.SqlAdapter** service naar een plan en maak een nieuw quotum specifiek voor databases met hoge beschikbaarheid. Zie voor meer informatie over het maken van abonnementen [Plan, aanbieding, quota en abonnementen overzicht](.\.\azure-stack-plan-offer-quota-overview.md).

> [!TIP]
> De **Microsoft.SqlAdapter** service niet meer beschikbaar zijn om toe te voegen aan plannen totdat de [resourceprovider van SQL Server is geïmplementeerd](.\.\azure-stack-sql-resource-provider-deploy.md).

## <a name="create-a-highly-available-sql-database"></a>Een maximaal beschikbare SQL-database maken
Nadat de SQL AlwaysOn-beschikbaarheidsgroep is gemaakt, geconfigureerd en toegevoegd als een Azure Stack SQL die als host fungeert Server door een Azure Stack-Operator, kunt ondersteuning van SQL-databases maken en een tenant-gebruiker met een abonnement met inbegrip van de mogelijkheden van SQL Server-database AlwaysOn-functionaliteit met de volgende stappen in deze sectie. 

> [!NOTE]
> Deze stappen van de gebruikersportal van Azure Stack uitgevoerd als een tenant-gebruiker met een abonnement van mogelijkheden voor SQL Server (Microsoft.SQLAdapter service).

1. Meld u aan bij de gebruikersportal aanmeldt:
    - Voor de implementatie van een geïntegreerd systeem, wordt de portal-adres variëren, afhankelijk van de regio en externe domeinnaam van uw oplossing. Dit is in de indeling van https://portal.&lt; *regio*&gt;.&lt; *FQDN*&gt;.
    - Als u de Azure Stack Development Kit (ASDK), wordt de gebruiker portal adres [ https://portal.local.azurestack.external ](https://portal.local.azurestack.external).

2. Selecteer **\+** **een resource maken** > **gegevens \+ opslag**, en vervolgens **SQL-Database**.<br><br>Geef de vereiste database eigenschapsgegevens zoals naam, sortering maximale grootte en het abonnement, resourcegroep en locatie moet worden gebruikt voor de implementatie. 

   ![SQL-database maken](./media/azure-stack-tutorial-sqlrp/createdb1.png)

3. Selecteer **SKU** en kies vervolgens de juiste SQL die als host fungeert Server SKU om het te gebruiken. In dit voorbeeld wordt de Azure Stack-Operator heeft gemaakt de **Enterprise-HA** SKU voor de ondersteuning van hoge beschikbaarheid voor SQL AlwaysOn-beschikbaarheidsgroepen.

   ![Selecteer de SKU](./media/azure-stack-tutorial-sqlrp/createdb2.png)

4. Selecteer **aanmelding** > **Maak een nieuwe aanmelding** en geef vervolgens de referenties voor de SQL-verificatie moet worden gebruikt voor de nieuwe database. Wanneer u klaar bent, klikt u op **OK** en vervolgens **maken** om te beginnen met het proces van de database.

   ![Aanmelding maken](./media/azure-stack-tutorial-sqlrp/createdb3.png)

5. Wanneer de implementatie van de SQL-database voltooid is, Controleer de eigenschappen van de database voor het detecteren van de verbindingsreeks te gebruiken om verbinding te maken met de nieuwe database maximaal beschikbaar. 

   ![Verbindingsreeks weergeven](./media/azure-stack-tutorial-sqlrp/createdb4.png)




## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Een SQL Server AlwaysOn-beschikbaarheidsgroep maken met een sjabloon
> * Een Azure Stack-Server voor het hosten van SQL maken
> * Een maximaal beschikbare SQL-database maken

Ga verder met de volgende zelfstudie voor meer informatie over het:
> [!div class="nextstepaction"]
> [MySQL-databases met hoge beschikbaarheid maken](azure-stack-tutorial-mysql.md)