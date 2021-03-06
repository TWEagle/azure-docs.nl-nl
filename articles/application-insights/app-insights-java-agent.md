---
title: Prestatiebewaking voor Java-web-apps in Azure Application Insights | Microsoft Docs
description: Uitgebreide bewaking van prestaties en gebruik van uw Java-website met Application Insights.
services: application-insights
documentationcenter: java
author: harelbr
manager: carmonm
ms.assetid: 84017a48-1cb3-40c8-aab1-ff68d65e2128
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 08/24/2016
ms.author: mbullwin
ms.openlocfilehash: b327e7f062cdf3e6b1b34a9540461dcb18caf21c
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2018
---
# <a name="monitor-dependencies-caught-exceptions-and-method-execution-times-in-java-web-apps"></a>Afhankelijkheden, bijgewerkt uitzonderingen en uitvoeringstijden methode in Java-web-apps bewaken


Als u hebt [uw Java-web-app met Application Insights geïnstrumenteerd][java], kunt u de Java-Agent voor dieper inzicht, zonder codewijzigingen:

* **Afhankelijkheden:** gegevens over aanroepen waarmee uw toepassing op andere onderdelen, met inbegrip van:
  * **REST-aanroepen** gedaan via HttpClient, OkHttp en RestTemplate (Spring) worden vastgelegd.
  * **Redis** aanroepen via de client-Jedis worden vastgelegd.
  * **[JDBC aanroepen](http://docs.oracle.com/javase/7/docs/technotes/guides/jdbc/)**  -MySQL, SQL Server en Oracle DB opdrachten die automatisch worden geregistreerd. Als de oproep langer dan per 10 duurt, rapporteert de agent voor MySQL, het queryplan.
* **Uitzonderingen wordt onderschept:** informatie over uitzonderingen die worden verwerkt door uw code.
* **Uitvoeringstijd voor methode:** informatie over de tijd die nodig is voor specifieke methoden uitvoeren.

Voor het gebruik van de Java-agent moet installeren u deze op uw server. Uw web-apps moeten zijn uitgerust met de [Application Insights-SDK voor Java][java]. 

## <a name="install-the-application-insights-agent-for-java"></a>Installeer de Application Insights-agent voor Java
1. Op de computer waarop u uw server Java [de agent downloaden](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest). Zorg ervoor dat voor het downloaden van de dezelfde verson van Java-Agent als de pakketten core en web Application Insights-SDK voor Java.
2. Het opstartscript van application server bewerken en de volgende JVM toevoegen:
   
    `javaagent:`*volledig pad naar het JAR-bestand van agent*
   
    Bijvoorbeeld in Tomcat op een Linux-machine:
   
    `export JAVA_OPTS="$JAVA_OPTS -javaagent:<full path to agent JAR file>"`
3. Start de toepassingsserver van uw opnieuw.

## <a name="configure-the-agent"></a>De agent configureren
Maak een bestand met de naam `AI-Agent.xml` en plaats deze in dezelfde map als de agent JAR-bestand.

Stel de inhoud van het xml-bestand. Het volgende voorbeeld als u wilt opnemen in of uitsluiten van de functies die u wilt bewerken.

```XML

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsightsAgent>
      <Instrumentation>

        <!-- Collect remote dependency data -->
        <BuiltIn enabled="true">
           <!-- Disable Redis or alter threshold call duration above which arguments are sent.
               Defaults: enabled, 10000 ms -->
           <Jedis enabled="true" thresholdInMS="1000"/>

           <!-- Set SQL query duration above which query plan is reported (MySQL, PostgreSQL). Default is 10000 ms. -->
           <MaxStatementQueryLimitInMS>1000</MaxStatementQueryLimitInMS>
        </BuiltIn>

        <!-- Collect data about caught exceptions
             and method execution times -->

        <Class name="com.myCompany.MyClass">
           <Method name="methodOne"
               reportCaughtExceptions="true"
               reportExecutionTime="true"
               />

           <!-- Report on the particular signature
                void methodTwo(String, int) -->
           <Method name="methodTwo"
              reportExecutionTime="true"
              signature="(Ljava/lang/String;I)V" />
        </Class>

      </Instrumentation>
    </ApplicationInsightsAgent>

```

U moet rapporten uitzondering en timing van de methode voor afzonderlijke methoden inschakelen.

Standaard `reportExecutionTime` is ingesteld op true en `reportCaughtExceptions` is ingesteld op false.

## <a name="view-the-data"></a>De gegevens weergeven
In de Application Insights-resource geaggregeerde externe afhankelijkheid en methode uitvoeringstijden weergegeven [onder de tegel prestaties][metrics].

Als u wilt zoeken naar afzonderlijke exemplaren van afhankelijkheid en uitzondering methode rapporten, open [Search][diagnostic].

[Diagnose afhankelijkheidsproblemen - meer](app-insights-asp-net-dependencies.md#diagnosis).

## <a name="questions-problems"></a>Vragen? Problemen?
* Zijn er geen gegevens? [Set firewall-uitzonderingen](app-insights-ip-addresses.md)
* [Problemen met Java oplossen](app-insights-java-troubleshoot.md)

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiexceptions]: app-insights-api-custom-events-metrics.md#track-exception
[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md
