---
title: Web-app-analyse voor ASP.NET instellen met Azure Application Insights | Microsoft Docs
description: Configureer prestaties, beschikbaarheid en gebruiker gedrag analysehulpprogramma's voor uw ASP.NET-website, on-premises gehost of in Azure.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: d0eee3c0-b328-448f-8123-f478052751db
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: mbullwin
ms.openlocfilehash: 86c0343a3492bf91eedda9303e3c6ac9cf86c4c3
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46947316"
---
# <a name="set-up-application-insights-for-your-aspnet-website"></a>Application Insights instellen voor uw ASP.NET-website

Met deze procedure wordt uw ASP.NET web-app zo geconfigureerd dat telemetrie wordt verzonden naar de [Azure Application Insights](app-insights-overview.md)-service. Dit werkt voor ASP.NET-apps die worden gehost op uw eigen on-premises IIS-server of in de cloud. U beschikt over grafieken en een krachtige querytaal waarmee u meer inzicht krijgt in de prestaties van uw app en hoe mensen deze gebruiken. Daarnaast ontvangt u meldingen over fouten of prestatieproblemen. Veel ontwikkelaars gebruiken deze functies graag, maar u kunt de telemetrie desgewenst uitbreiden en aanpassen.

Voor de Setup zijn maar een paar klikken nodig in Visual Studio. U kunt kosten besparen door de hoeveelheid telemetrie te beperken. Hiermee kunt u experimenteren en fouten opsporen of een site met weinig gebruikers bewaken. Wanneer u besluit dat u de productiesite wilt bewaken, kunt u de limiet later eenvoudig verhogen.

## <a name="prerequisites"></a>Vereisten
Als u Application Insights wilt toevoegen aan uw ASP.NET-website, moet u het volgende doen:

- [Installeer Visual Studio 2017 voor Windows ](https://www.visualstudio.com/downloads/) met de volgende workloads:
    - ASP.NET-ontwikkeling en webontwikkeling
    - Azure-ontwikkeling

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="ide"></a> Stap 1: de Application Insights-SDK toevoegen

> [!IMPORTANT]
> Het proces voor het toevoegen van Application Insights hangt af van het type ASP.NET-sjabloon. Als u de sjabloon **Leeg** of **Azure mobile app** gebruikt, selecteer dan **Project** > **Application Insights Telemetry toevoegen**. Raadpleeg voor alle andere ASP.NET-sjablonen de onderstaande instructies. 

Klik met de rechtermuisknop op de naam van de web-app in Solution Explorer en kies **Application Insights configureren**

![Schermopname van Solution Explorer waarin Application Insights is gemarkeerd](./media/app-insights-asp-net/0001-configure-application-insights.png)

(Afhankelijk van de Application Insights-SDK-versie wordt u mogelijk gevraagd om een upgrade uit te voeren naar de meest recente SDK-versie. Als u hierom wordt gevraagd, selecteert u **SDK bijwerken**.)

![Schermopname: Er is een nieuwe versie van Microsoft Application Insights-SDK beschikbaar. SDK bijwerken is gemarkeerd](./media/app-insights-asp-net/0002-update-sdk.png)

Application Insights-configuratiescherm:

Selecteer **Gratis beginnen**.

![Schermopname van de pagina Uw app registreren bij Application Insights](./media/app-insights-asp-net/0004-start-free.png)

Als u een resourcegroep of locatie wilt instellen voor het opslaan van uw gegevens, klikt u op **Instellingen configureren**. Resourcegroepen worden gebruikt om toegang tot gegevens te beheren. Als u verschillende apps hebt die deel uitmaken van hetzelfde systeem, kunt u hun Application Insights-gegevens in dezelfde resourcegroep plaatsen.

 Selecteer **Registreren**. 

![Schermopname van de pagina Uw app registreren bij Application Insights](./media/app-insights-asp-net/0005-register-ed.png)

 Er wordt telemetrie verzonden naar [Azure Portal](https://portal.azure.com), zowel tijdens de foutopsporing als na het publiceren van de app.
> [!NOTE]
> Als u geen telemetrie naar de portal wilt verzenden tijdens de foutopsporing, voegt u de Application Insights SDK toe aan de app, maar configureert u geen resource in de portal. U kunt de telemetrie in Visual Studio bekijken tijdens de foutopsporing. U kunt later terugkeren naar deze configuratiepagina, of u kunt wachten tot de app is geïmplementeerd en [telemetrie inschakelen tijdens runtime](app-insights-monitor-performance-live-website-now.md).

## <a name="run"></a> Stap 2: uw app uitvoeren
Voer uw app uit met F5. Open verschillende pagina's om telemetrie te genereren.

In Visual Studio ziet u het aantal gebeurtenissen dat is vastgelegd in een logboek.

![Schermopname van Visual Studio. Tijdens het opsporen van fouten wordt de knop Application Insights weergegeven.](./media/app-insights-asp-net/0006-Events.png)

## <a name="step-3-see-your-telemetry"></a>Stap 3: uw telemetrie weergeven
U ziet uw telemetrie in Visual Studio of in de Application Insights-webportal. Zoek telemetrie in Visual Studio om fouten in de app op te sporen. Bewaak de prestaties en het gebruik in de webportal wanneer het systeem live is. 

### <a name="see-your-telemetry-in-visual-studio"></a>De telemetrie bekijken in Visual Studio

In Visual Studio om de Application Insights-gegevens te bekijken.  Selecteer **Solution Explorer** > **Verbonden services** > klik met de rechtermuisknop op **Application Insights**, en klik vervolgens op **Zoeken in live telemetrie**.

In het zoekvenster van Visual Studio Application Insights ziet u de telemetriegegevens van de toepassing die zijn gegenereerd aan de serverzijde van de app. Experimenteer met de filters en klik op een gebeurtenis voor meer details.

![Schermopname van de weergave Gegevens van foutopsporingssessie in het venster Application Insights.](./media/app-insights-asp-net/55.png)

> [!Tip]
> Als u geen gegevens ziet, controleert u of het tijdsbereik correct is en klikt u op het pictogram Zoeken.

[Meer informatie over Application Insights-hulpprogramma's in Visual Studio](app-insights-visual-studio.md).

<a name="monitor"></a>
### <a name="see-telemetry-in-web-portal"></a>Telemetrie bekijken in de webportal

U kunt de telemetrie ook in de Application Insights-webportal bekijken (tenzij u ervoor hebt gekozen alleen de SDK te installeren). De portal bevat meer grafieken, analysefuncties en weergaven met meerdere onderdelen dan Visual Studio. De portal bevat ook waarschuwingen.

Open uw Application Insights-resource. Meld u aan bij [Azure Portal](https://portal.azure.com/) om de telemetrie te bekijken, of selecteer hiervoor **Solution Explorer** > **Verbonden services** > klik met de rechtermuisknop op **Application Insights** > **Application Insights-portal openen**.

De portal wordt geopend met een weergave van de telemetrie van uw app.

![Schermopname van Application Insights-overzichtspagina](./media/app-insights-asp-net/66.png)

Klik in de portal op een tegel of grafiek om meer details te bekijken.

[Meer informatie over het gebruik van Application Insights in Azure Portal](app-insights-dashboards.md).

## <a name="step-4-publish-your-app"></a>Stap 4: uw app publiceren
Publiceer uw app op de IIS-server of op Azure. Bekijk de livestream met metrische gegevens in [Live Metrics Stream](app-insights-metrics-explorer.md#live-metrics-stream) om te controleren of alles goed werkt.

Uw telemetrie wordt opgebouwd in de Application Insights-portal, waar u metrische gegevens kunt controleren, uw telemetrie kunt doorzoeken en [dashboards](app-insights-dashboards.md) kunt instellen. U kunt ook de krachtige [querytaal van Log Analytics](https://aka.ms/LogAnalyticsLanguage) gebruiken om gebruik en prestaties te analyseren of om specifieke gebeurtenissen te zoeken.

U kunt uw telemetrie ook blijven analyseren in [Visual Studio](app-insights-visual-studio.md) met hulpprogramma's voor diagnostisch zoeken en [Trends](app-insights-visual-studio-trends.md).

> [!NOTE]
> Als uw app zoveel telemetrie verzendt dat de[beperkingslimieten](app-insights-pricing.md#limits-summary) worden benaderd, worden automatisch [steekproeven](app-insights-sampling.md) ingeschakeld. Met steekproeven vermindert u de hoeveelheid telemetrie die vanuit uw app wordt verzonden, maar behoudt u gecorreleerde gegevens voor diagnostische doeleinden.
>
>

## <a name="land"></a> U bent nu klaar

Gefeliciteerd! U hebt het pakket Application Insights geïnstalleerd in uw app en dit zo geconfigureerd dat telemetrie wordt verzonden naar de Application Insights-service in Azure.

![Diagram van verplaatsing van telemetrie](./media/app-insights-asp-net/01-scheme.png)

De Azure-resource die de telemetrie van uw app ontvangt, wordt aangeduid met een *instrumentatiesleutel*. U vindt deze sleutel in het bestand ApplicationInsights.config.


## <a name="upgrade-to-future-sdk-versions"></a>Upgraden naar toekomstige SDK-versies
Als u wilt upgraden naar een [nieuwe release van de SDK](https://github.com/Microsoft/ApplicationInsights-dotnet-server/releases), opent u **NuGet-pakketbeheer** opnieuw en filtert u op geïnstalleerde pakketten. Selecteer **Microsoft.ApplicationInsights.Web** en kies **Upgraden**.

Als u aanpassingen in ApplicationInsights.config hebt aangebracht, slaat u hiervan een kopie op voordat u de upgrade uitvoert. Voeg de wijzigingen vervolgens samen in de nieuwe versie.

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Volgende stappen

Er zijn ook andere onderwerpen die u kunt bekijken als u geïnteresseerd bent in:

* [Een web-app instrumenteren tijdens runtime](app-insights-monitor-performance-live-website-now.md)
* [Azure Cloud Services](app-insights-cloudservices.md)

### <a name="more-telemetry"></a>Meer telemetrie

* **[Laadgegevens voor browser en pagina](app-insights-javascript.md)**: voeg een codefragment in uw webpagina's in.
* **[Gedetailleerde bewaking van afhankelijkheid en uitzonderingen](app-insights-monitor-performance-live-website-now.md)**: installeer Status Monitor op uw server.
* **[Codeer aangepaste gebeurtenissen](app-insights-api-custom-events-metrics.md)** om gebruikersacties te tellen, te meten of de tijdsduur hiervan te bepalen.
* **[Haal logboekgegevens op](app-insights-asp-net-trace-logs.md)**: koppel logboekgegevens aan uw telemetrie.

### <a name="analysis"></a>Analyse

* **[Met Application Insights werken in Visual Studio](app-insights-visual-studio.md)**<br/>Bevat informatie over foutopsporing met telemetrie, het doorzoeken van diagnostische gegevens en het in detail analyseren van code.
* **[Werken met de Application Insights-portal](app-insights-dashboards.md)**<br/> Bevat informatie over dashboards, krachtige hulpprogramma's voor diagnose en analyse, waarschuwingen, een live afhankelijkheidskaart van uw toepassing en exportmogelijkheden voor telemetrie.
* **[Analyse](app-insights-analytics-tour.md)**: de krachtige querytaal.

### <a name="alerts"></a>Waarschuwingen

* [Beschikbaarheidstests](app-insights-monitor-web-app-availability.md): maak tests om ervoor te zorgen dat uw site zichtbaar is op internet.
* [Slimme diagnostische gegevens](app-insights-proactive-diagnostics.md): deze tests worden automatisch uitgevoerd, zodat u niets hoeft te doen om ze in te stellen. Deze geeft aan of een app een ongebruikelijk aantal mislukte aanvragen heeft.
* [Metrische waarschuwingen](app-insights-alerts.md): stel deze in om u te waarschuwen als een metriek een drempelwaarde overschrijdt. U kunt deze instellen op aangepaste metrische gegevens die u in uw app codeert.

### <a name="automation"></a>Automation

* [Het maken van een Application Insights-resource automatiseren](app-insights-powershell.md)
