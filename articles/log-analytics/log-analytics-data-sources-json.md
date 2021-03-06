---
title: Aangepaste JSON-gegevens in OMS Log Analytics verzamelt | Microsoft Docs
description: Aangepaste JSON-gegevensbronnen kunnen worden verzameld in Log Analytics met behulp van de OMS-Agent voor Linux.  Deze aangepaste gegevensbronnen kunnen eenvoudige scripts JSON retourneert, zoals curl of een van de FluentD meer dan 300 invoegtoepassingen zijn. Dit artikel beschrijft de vereiste configuratie voor het verzamelen van deze gegevens.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/04/2017
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: 9725a3df04ef28fc3a076c3c6ca6663e36b186a8
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/02/2018
ms.locfileid: "48040265"
---
# <a name="collecting-custom-json-data-sources-with-the-oms-agent-for-linux-in-log-analytics"></a>Verzamelen van aangepaste JSON-gegevensbronnen met de OMS-Agent voor Linux in Log Analytics
Aangepaste JSON-gegevensbronnen kunnen worden verzameld in Log Analytics met behulp van de OMS-Agent voor Linux.  Deze aangepaste gegevensbronnen kunnen worden eenvoudige scripts, zoals JSON retourneert [curl](https://curl.haxx.se/) of een van [FluentD van meer dan 300 invoegtoepassingen](http://www.fluentd.org/plugins/all). Dit artikel beschrijft de vereiste configuratie voor het verzamelen van deze gegevens.

> [!NOTE]
> OMS-Agent voor Linux v1.1.0-217 + is vereist voor de aangepaste JSON-gegevens

## <a name="configuration"></a>Configuratie

### <a name="configure-input-plugin"></a>Invoer-invoegtoepassing configureren

Voor het verzamelen van JSON-gegevens in Log Analytics toevoegen `oms.api.` aan het begin van een FluentD tag op in een invoer-invoegtoepassing.

Volgende is bijvoorbeeld een afzonderlijk configuratiebestand `exec-json.conf` in `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/`.  Dit maakt gebruik van de invoegtoepassing FluentD `exec` een curl-opdracht uitvoeren elke 30 seconden.  De uitvoer van deze opdracht worden verzameld door de JSON-uitvoer-invoegtoepassing.

```
<source>
  type exec
  command 'curl localhost/json.output'
  format json
  tag oms.api.httpresponse
  run_interval 30s
</source>

<match oms.api.httpresponse>
  type out_oms_api
  log_level info

  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms_api_httpresponse*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
```
Het configuratiebestand toegevoegd onder `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/` moet de eigenaar gewijzigd met de volgende opdracht.

`sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/exec-json.conf`

### <a name="configure-output-plugin"></a>Uitvoer-invoegtoepassing configureren 
De configuratie van de volgende uitvoer-invoegtoepassing toevoegen aan de configuratie van de belangrijkste in `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` of als een afzonderlijk configuratiebestand in geplaatst `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/`

```
<match oms.api.**>
  type out_oms_api
  log_level info

  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms_api*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
```

### <a name="restart-oms-agent-for-linux"></a>OMS-Agent voor Linux opnieuw starten
Start opnieuw op de OMS-Agent voor Linux-service met de volgende opdracht.

    sudo /opt/microsoft/omsagent/bin/service_control restart 

## <a name="output"></a>Uitvoer
De gegevens worden verzameld in Log Analytics met een recordtype van `<FLUENTD_TAG>_CL`.

Bijvoorbeeld, het aangepaste label `tag oms.api.tomcat` in Log Analytics met een recordtype van `tomcat_CL`.  U kunt alle records van dit type met de volgende zoeken in Logboeken kan ophalen.

    Type=tomcat_CL

Geneste JSON-gegevens bronnen worden ondersteund, maar worden geïndexeerd, op basis van bovenliggende veld. Bijvoorbeeld, de volgende JSON-gegevens wordt geretourneerd van een zoekopdracht in Log Analytics als `tag_s : "[{ "a":"1", "b":"2" }]`.

```
{
    "tag": [{
        "a":"1",
        "b":"2"
    }]
}
```


## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [zoekopdrachten](log-analytics-log-searches.md) om de gegevens die worden verzameld van gegevensbronnen en oplossingen te analyseren. 
 