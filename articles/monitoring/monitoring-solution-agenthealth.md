---
title: Oplossing status van agent in Azure | Microsoft Docs
description: In dit artikel is bedoeld om te begrijpen hoe u deze oplossing gebruikt voor het bewaken van de status van uw agents die rapporteren rechtstreeks naar Log Analytics of System Center Operations Manager.
services: operations-management-suite
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2017
ms.author: magoedte
ms.openlocfilehash: f0737c6a6ff228b92a030242faf7f4d634bdd9f2
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2018
ms.locfileid: "45733177"
---
#  <a name="agent-health-solution-in-azure"></a>Oplossing status van agent in Azure
De oplossing status van Agent in Azure kunt u meer informatie over voor alle van de agents die rapporteren rechtstreeks verbonden met Log Analytics, dat niet meer reageert zijn op de Log Analytics-werkruimte of vanuit een System Center Operations Manager-beheergroep en het verzenden van operationele de gegevens.  U kunt ook bijhouden hoeveel agents er zijn geïmplementeerd en waar deze zich geografisch gezien bevinden. Bovendien kunt u query's uitvoeren om op de hoogte te blijven van de verdeling van agents over Azure, andere cloudomgevingen of on-premises.    

## <a name="prerequisites"></a>Vereisten
Voordat u deze oplossing implementeert, Controleer of u beschikt over ondersteunde [Windows agents](../log-analytics/log-analytics-windows-agent.md) rapporteren aan de Log Analytics-werkruimte of aan een [Operations Manager-beheergroep](../log-analytics/log-analytics-om-agents.md) geïntegreerd met uw werkruimte.    

## <a name="solution-components"></a>Oplossingsonderdelen
Deze oplossing bestaat uit de volgende resources die worden toegevoegd aan uw werkruimte en rechtstreeks verbonden agents of verbonden Operations Manager-beheergroepen.

### <a name="management-packs"></a>Management packs
Als uw System Center Operations Manager-beheergroep is verbonden met een Log Analytics-werkruimte, worden de volgende management packs geïnstalleerd in Operations Manager.  Deze management packs worden na het toevoegen van deze oplossing ook op rechtstreeks verbonden Windows-computers geïnstalleerd. Met deze management packs hoeft u niets te configureren of te beheren.

* Microsoft System Center Advisor HealthAssessment Direct Channel Intelligence Pack  (Microsoft.IntelligencePacks.HealthAssessmentDirect)
* Microsoft System Center Advisor HealthAssessment Server Channel Intelligence Pack (Microsoft.IntelligencePacks.HealthAssessmentViaServer).  

Zie [Operations Manager koppelen aan Log Analytics](../log-analytics/log-analytics-om-agents.md) voor meer informatie over de manier waarop uw management packs voor oplossingen worden bijgewerkt.

## <a name="configuration"></a>Configuratie
De oplossing status van Agent toevoegen aan uw Log Analytics-werkruimte met behulp van de procedure beschreven in [oplossingen toevoegen](../log-analytics/log-analytics-add-solutions.md). Er is geen verdere configuratie nodig.


## <a name="data-collection"></a>Gegevensverzameling
### <a name="supported-agents"></a>Ondersteunde agents
De volgende tabel beschrijft de verbonden bronnen die worden ondersteund door deze oplossing.

| Verbonden bron | Ondersteund | Beschrijving |
| --- | --- | --- |
| Windows-agents | Ja | Er worden heartbeat-gebeurtenissen verzameld van direct verbonden Windows-agents.|
| Beheergroep System Center Operations Manager | Ja | Er worden elke 60 seconden heartbeat-gebeurtenissen verzameld van agents die rapporteren aan de beheergroep en deze worden vervolgens doorgestuurd naar Log Analytics. Er is geen directe verbinding tussen Operations Manager-agents en Log Analytics vereist. Gegevens van heartbeat-gebeurtenissen worden vanuit de beheergroep doorgestuurd naar de opslagplaats van Log Analytics.|

## <a name="using-the-solution"></a>De oplossing gebruiken
Wanneer u de oplossing aan uw Log Analytics-werkruimte toevoegt, de **agentstatus** tegel wordt toegevoegd aan uw dashboard. Op deze tegel ziet u het totale aantal agents en het aantal agents dat de afgelopen 24 uur niet heeft gereageerd.<br><br> ![De tegel Status van agent in het dashboard](./media/monitoring-solution-agenthealth/agenthealth-solution-tile-homepage.png)

Klik op de tegel **Status van agent** om het **gelijknamige** dashboard te openen.  Het dashboard bevat de kolommen in de volgende tabel. Elke kolom bevat de tien belangrijkste gebeurtenissen naar aantal die overeenkomen met de criteria van die kolom voor de opgegeven periode. U kunt de volledige lijst met gebeurtenissen weergeven door linksonder elke kolom **Alles weergeven** te selecteren. Dit kan overigens ook door op de kolomkop te klikken.

| Kolom | Beschrijving |
|--------|-------------|
| Agent count over time | Een trend van het aantal agents gedurende een periode van zeven dagen voor Linux- en Windows-agents.|
| Count of unresponsive agents | Een lijst met agents die in de afgelopen 24 uur geen heartbeat hebben verzonden.|
| Distribution by OS Type | Een visualisatie van het aantal Windows- en Linux-agents in uw omgeving.|
| Distribution by Agent Version | Een visualisatie van de verschillende agentversies die zijn geïnstalleerd in uw omgeving en het aantal van elke versie.|
| Distribution by Agent Category | Een visualisatie van de verschillende categorieën agents die heartbeat-gebeurtenissen verzenden: directe agents, OpsMgr-agents of OpsMgr Management Server.|
| Distribution by Management Group | Een visualisatie van de verschillende SCOM-beheergroepen in uw omgeving.|
| Geo-location of Agents | Een visualisatie van de verschillende landen waarin zich agents bevinden en het totale aantal agents dat in elk land is geïnstalleerd.|
| Count of Gateways Installed | Het aantal servers die de Log Analytics gateway is geïnstalleerd, en een lijst van deze servers.|

![Voorbeeld van het dashboard van de oplossing Status van agent](./media/monitoring-solution-agenthealth/agenthealth-solution-dashboard.png)  

## <a name="log-analytics-records"></a>Log Analytics-records
De oplossing voegt één type record in de Log Analytics-werkruimte.  

### <a name="heartbeat-records"></a>Heartbeat-records
Er wordt een record van het type **Heartbeat** gemaakt.  Deze records hebben de eigenschappen uit de volgende tabel.  

| Eigenschap | Beschrijving |
| --- | --- |
| Type | *Heartbeat*|
| Categorie | De waarde is *Direct Agent*, *SCOM Agent* of *SCOM Management Server*.|
| Computer | De naam van de computer.|
| OSType | Windows- of Linux-besturingssysteem.|
| OSMajorVersion | De primaire versie van het besturingssysteem.|
| OSMinorVersion | De secundaire versie van het besturingssysteem.|
| Versie | Log Analytics-Agent of Operations Manager-Agent-versie.|
| SCAgentChannel | De waarde is *Direct* en/of *SCManagementServer*.|
| IsGatewayInstalled | Als Log Analytics Gateway is geïnstalleerd, wordt de waarde is *waar*, anders wordt de waarde is *false*.|
| ComputerIP | Het IP-adres van de computer.|
| RemoteIPCountry | De geografische locatie waar de computer is geïmplementeerd.|
| ManagementGroupName | De naam van de beheergroep van Operations Manager.|
| SourceComputerId | De unieke ID van de computer.|
| RemoteIPLongitude | De lengtegraad van geografische locatie van de computer.|
| RemoteIPLatitude | De breedtegraad van de geografische locatie van de computer.|

Elke agent die rapporteert aan een Operations Manager-beheerserver stuurt twee heartbeats, en de waarde van de eigenschap SCAgentChannel bevat zowel **Direct** en **SCManagementServer** , afhankelijk van welke Log Analytics-gegevensbronnen en oplossingen die u hebt ingeschakeld in uw abonnement. Als u intrekt, gegevens van oplossingen verzonden rechtstreeks vanuit een Operations Manager-beheerserver naar Log Analytics of vanwege de hoeveelheid gegevens die worden verzameld op de agent, rechtstreeks vanuit de agent worden verzonden naar Log Analytics. Voor heartbeat-gebeurtenissen met de waarde **SCManagementServer** bestaat de waarde van ComputerIP uit het IP-adres van de beheerserver, aangezien de gegevens door deze server worden geüpload.  Voor heartbeats waarvan de eigenschap SCAgentChannel is ingesteld op **Direct**, is de waarde het openbare IP-adres van de agent.  

## <a name="sample-log-searches"></a>Voorbeeldzoekopdrachten in logboeken
De volgende tabel bevat voorbeelden van zoekopdrachten in logboeken voor records die zijn verzameld met deze oplossing.

| Query’s uitvoeren | Beschrijving |
|:---|:---|
| Heartbeat &#124; distinct Computer |Het totale aantal agents |
| Heartbeat &#124; summarize LastCall = max(TimeGenerated) by Computer &#124; where LastCall < ago(24h) |Het aantal agents dat de afgelopen 24 uur niet heeft gereageerd |
| Heartbeat &#124; summarize LastCall = max(TimeGenerated) by Computer &#124; where LastCall < ago(15m) |Het aantal agents dat de afgelopen 15 minuten niet heeft gereageerd |
| Heartbeat &#124; where TimeGenerated > ago(24h) and Computer in ((Heartbeat &#124; where TimeGenerated > ago(24h) &#124; distinct Computer)) &#124; summarize LastCall = max(TimeGenerated) by Computer |Computers die online waren (in de afgelopen 24 uur) |
| Heartbeat &#124; where TimeGenerated > ago(24h) and Computer !in ((Heartbeat &#124; where TimeGenerated > ago(30m) &#124; distinct Computer)) &#124; summarize LastCall = max(TimeGenerated) by Computer |Het totale aantal agents dat offline was in de afgelopen 30 minuten (voor de afgelopen 24 uur) |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by OSType |Een trend van het aantal agents in de tijd per type besturingssysteem|
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by OSType |Distribution by OS Type |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by Version |Verdeling naar agent-versie |
| Heartbeat &#124; summarize AggregatedValue = count() by Category |Verdeling naar agent-catgeorie |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by ManagementGroupName | Verdeling naar beheergroep |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by RemoteIPCountry |Geo-location of Agents |
| Heartbeat &#124; where iff(isnotnull(toint(IsGatewayInstalled)), IsGatewayInstalled == true, IsGatewayInstalled == "true") == true &#124; distinct Computer |Aantal Log Analytics Gateways geïnstalleerd |




## <a name="next-steps"></a>Volgende stappen

* Zie [Understanding alerts in Log Analytics](../log-analytics/log-analytics-alerts.md) voor meer informatie over het genereren van waarschuwingen van Log Analytics.
