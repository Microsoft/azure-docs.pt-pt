---
title: Filtrar telemetria do Application Insights do Azure na sua aplicação web Java | Documentos da Microsoft
description: Reduza o tráfego de telemetria ao filtrar os eventos que não precisa de monitorizar.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 11/23/2016
ms.author: mbullwin
ms.openlocfilehash: 692113257e483f67eaaee038c07d8702d95a7b31
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58116814"
---
# <a name="filter-telemetry-in-your-java-web-app"></a>Filtrar telemetria na sua aplicação web Java

Filtros fornecem uma forma para selecionar a telemetria que sua [aplicação web Java envia para o Application Insights](java-get-started.md). Existem alguns filtros de out-of-the-box que pode utilizar, e também pode escrever seus próprios filtros personalizados.

Os filtros de out-of-the-box incluem:

* Nível de gravidade de rastreio
* URLs específicos, palavras-chave ou códigos de resposta
* Respostas rápidas - ou seja, pedidos para o qual a aplicação respondeu a rapidamente
* Nomes de eventos específico

> [!NOTE]
> Filtros distorcer as métricas da sua aplicação. Por exemplo, poderá decidir que, para diagnosticar repostas lentas, defina um filtro para descartar os tempos de resposta rápidos. Mas deve estar ciente de que os tempos de resposta médio comunicados pelo Application Insights, em seguida, será mais lentos que a velocidade verdadeira e a contagem de pedidos irá ser menor do que a contagem real.
> Se isso for uma preocupação, utilize [amostragem](../../azure-monitor/app/sampling.md) em vez disso.

## <a name="setting-filters"></a>Filtros de definição

No applicationinsights. XML, adicione um `TelemetryProcessors` secção semelhante a este exemplo:


```XML

    <ApplicationInsights>
      <TelemetryProcessors>

        <BuiltInProcessors>
           <Processor type="TraceTelemetryFilter">
                  <Add name="FromSeverityLevel" value="ERROR"/>
           </Processor>

           <Processor type="RequestTelemetryFilter">
                  <Add name="MinimumDurationInMS" value="100"/>
                  <Add name="NotNeededResponseCodes" value="200-400"/>
           </Processor>

           <Processor type="PageViewTelemetryFilter">
                  <Add name="DurationThresholdInMS" value="100"/>
                  <Add name="NotNeededNames" value="home,index"/>
                  <Add name="NotNeededUrls" value=".jpg,.css"/>
           </Processor>

           <Processor type="TelemetryEventFilter">
                  <!-- Names of events we don't want to see -->
                  <Add name="NotNeededNames" value="Start,Stop,Pause"/>
           </Processor>

           <!-- Exclude telemetry from availability tests and bots -->
           <Processor type="SyntheticSourceFilter">
                <!-- Optional: specify which synthetic sources,
                     comma-separated
                     - default is all synthetics -->
                <Add name="NotNeededSources" value="Application Insights Availability Monitoring,BingPreview"
           </Processor>

        </BuiltInProcessors>

        <CustomProcessors>
          <Processor type="com.fabrikam.MyFilter">
            <Add name="Successful" value="false"/>
          </Processor>
        </CustomProcessors>

      </TelemetryProcessors>
    </ApplicationInsights>

```




[Inspecionar o conjunto completo de processadores incorporados](https://github.com/Microsoft/ApplicationInsights-Java/tree/master/core/src/main/java/com/microsoft/applicationinsights/internal/processor).

## <a name="built-in-filters"></a>Filtros incorporados

### <a name="metric-telemetry-filter"></a>Filtro da métrica telemetria

```XML

           <Processor type="MetricTelemetryFilter">
                  <Add name="NotNeeded" value="metric1,metric2"/>
           </Processor>
```

* `NotNeeded` – Lista separados por vírgulas de nomes de métricas personalizados.


### <a name="page-view-telemetry-filter"></a>Filtro de telemetria de vista de página

```XML

           <Processor type="PageViewTelemetryFilter">
                  <Add name="DurationThresholdInMS" value="500"/>
                  <Add name="NotNeededNames" value="page1,page2"/>
                  <Add name="NotNeededUrls" value="url1,url2"/>
           </Processor>
```

* `DurationThresholdInMS` -Duração refere-se para o tempo necessário para carregar a página. Se isto estiver definido, as páginas que carregado mais rapidamente do que desta vez não são reportadas.
* `NotNeededNames` – Lista separados por vírgulas de nomes de página.
* `NotNeededUrls` -Fragmentos de lista separados por vírgulas de URL. Por exemplo, `"home"` filtra todas as páginas que têm de "home" no URL.


### <a name="request-telemetry-filter"></a>Filtro de telemetria de pedido


```XML

           <Processor type="RequestTelemetryFilter">
                  <Add name="MinimumDurationInMS" value="500"/>
                  <Add name="NotNeededResponseCodes" value="page1,page2"/>
                  <Add name="NotNeededUrls" value="url1,url2"/>
           </Processor>
```



### <a name="synthetic-source-filter"></a>Filtro de origem sintético

Filtra as toda a telemetria que têm valores na propriedade SyntheticSource. Estes incluem pedidos de bots, spiders e testes de disponibilidade.

Filtre telemetria para todos os pedidos sintéticos:


```XML

           <Processor type="SyntheticSourceFilter" />
```

Filtre telemetria de fontes sintéticas específicas:


```XML

           <Processor type="SyntheticSourceFilter" >
                  <Add name="NotNeeded" value="source1,source2"/>
           </Processor>
```

* `NotNeeded` – Lista separados por vírgulas de nomes de origem sintético.

### <a name="telemetry-event-filter"></a>Filtro de eventos de telemetria

Filtra os eventos personalizados (conectado usando [trackevent ()](../../azure-monitor/app/api-custom-events-metrics.md#trackevent)).


```XML

           <Processor type="TelemetryEventFilter" >
                  <Add name="NotNeededNames" value="event1, event2"/>
           </Processor>
```


* `NotNeededNames` – Lista separados por vírgulas de nomes de eventos.


### <a name="trace-telemetry-filter"></a>Filtro de telemetria de rastreio

Rastreios de registo de filtros (conectado usando [tracktrace ()](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) ou uma [recoletor de estrutura de Registro em log](java-trace-logs.md)).

```XML

           <Processor type="TraceTelemetryFilter">
                  <Add name="FromSeverityLevel" value="ERROR"/>
           </Processor>
```

* `FromSeverityLevel` Valores válidos são:
  *  DESATIVAR - filtrar todos os rastreios
  *  RASTREIO - nenhuma filtragem. é igual ao nível de rastreio
  *  INFO - filtrar o nível de rastreio
  *  AVISO - filtro de rastreio e informações
  *  Erro - filtrar o rastreio de aviso, informações,
  *  CRÍTICO - filtrar todas, CRÍTICOS


## <a name="custom-filters"></a>Filtros personalizados

### <a name="1-code-your-filter"></a>1. O filtro de código

Em seu código, criar uma classe que implementa `TelemetryProcessor`:

```Java

    package com.fabrikam.MyFilter;
    import com.microsoft.applicationinsights.extensibility.TelemetryProcessor;
    import com.microsoft.applicationinsights.telemetry.Telemetry;

    public class SuccessFilter implements TelemetryProcessor {

       /* Any parameters that are required to support the filter.*/
       private final String successful;

       /* Initializers for the parameters, named "setParameterName" */
       public void setNotNeeded(String successful)
       {
          this.successful = successful;
       }

       /* This method is called for each item of telemetry to be sent.
          Return false to discard it.
          Return true to allow other processors to inspect it. */
       @Override
       public boolean process(Telemetry telemetry) {
        if (telemetry == null) { return true; }
        if (telemetry instanceof RequestTelemetry)
        {
            RequestTelemetry requestTelemetry = (RequestTelemetry)telemetry;
            return request.getSuccess() == successful;
        }
        return true;
       }
    }

```


### <a name="2-invoke-your-filter-in-the-configuration-file"></a>2. Invocar o filtro no ficheiro de configuração

In ApplicationInsights.xml:

```XML


    <ApplicationInsights>
      <TelemetryProcessors>
        <CustomProcessors>
          <Processor type="com.fabrikam.SuccessFilter">
            <Add name="Successful" value="false"/>
          </Processor>
        </CustomProcessors>
      </TelemetryProcessors>
    </ApplicationInsights>

```

## <a name="troubleshooting"></a>Resolução de problemas

*Meu filtro não está a funcionar.*

* Verifique que forneceu valores de parâmetro válido. Por exemplo, durações devem ser números inteiros. Valores inválidos fará com que o filtro que deverão ser ignorados. Se o seu filtro personalizado lança uma exceção de um construtor ou método set, ela será ignorada.

## <a name="next-steps"></a>Passos Seguintes

* [Amostragem](../../azure-monitor/app/sampling.md) -considere amostragem como uma alternativa que não distorcer as suas métricas.
