---
title: Filtrar Aplicativo Azure telemetria de informações em seu aplicativo Web Java
description: Reduza o tráfego de telemetria filtrando os eventos que você não precisa monitorar.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 3/14/2019
ms.openlocfilehash: dd47ef68726f3f804dabbc9751e9c86dfe846559
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927287"
---
# <a name="filter-telemetry-in-your-java-web-app"></a>Filtrar telemetria em seu aplicativo Web Java

Os filtros fornecem uma maneira de selecionar a telemetria que seu [aplicativo Web Java envia para Application insights](java-get-started.md). Há alguns filtros prontos para uso que você pode usar, e você também pode escrever seus próprios filtros personalizados.

Os filtros prontos para uso incluem:

* Nível de severidade de rastreamento
* URLs específicas, palavras-chave ou códigos de resposta
* Respostas rápidas – ou seja, solicitações às quais seu aplicativo respondeu rapidamente
* Nomes de eventos específicos

> [!NOTE]
> Os filtros distorcem as métricas do seu aplicativo. Por exemplo, você pode decidir que, para diagnosticar respostas lentas, você definirá um filtro para descartar tempos de resposta rápidos. Mas você deve estar ciente de que os tempos de resposta médios relatados pelo Application Insights serão mais lentos do que a velocidade real e a contagem de solicitações será menor do que a contagem real.
> Se essa for uma preocupação, use a [amostragem](../../azure-monitor/app/sampling.md) em vez disso.

## <a name="setting-filters"></a>Definindo filtros

Em ApplicationInsights. xml, adicione uma seção `TelemetryProcessors` como neste exemplo:


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




[Inspecione o conjunto completo de processadores internos](https://github.com/Microsoft/ApplicationInsights-Java/tree/master/core/src/main/java/com/microsoft/applicationinsights/internal/processor).

## <a name="built-in-filters"></a>Filtros internos

### <a name="metric-telemetry-filter"></a>Filtro de telemetria de métrica

```XML

           <Processor type="MetricTelemetryFilter">
                  <Add name="NotNeeded" value="metric1,metric2"/>
           </Processor>
```

* `NotNeeded`-lista separada por vírgulas de nomes de métrica personalizados.


### <a name="page-view-telemetry-filter"></a>Filtro de telemetria de exibição de página

```XML

           <Processor type="PageViewTelemetryFilter">
                  <Add name="DurationThresholdInMS" value="500"/>
                  <Add name="NotNeededNames" value="page1,page2"/>
                  <Add name="NotNeededUrls" value="url1,url2"/>
           </Processor>
```

* `DurationThresholdInMS`-Duration refere-se ao tempo necessário para carregar a página. Se estiver definido, as páginas que carregarem mais rápido do que essa hora não serão relatadas.
* `NotNeededNames`-lista de nomes de página separados por vírgulas.
* `NotNeededUrls`-lista de fragmentos de URL separados por vírgulas. Por exemplo, `"home"` filtra todas as páginas que têm "Home" na URL.


### <a name="request-telemetry-filter"></a>Filtro de telemetria de solicitação


```XML

           <Processor type="RequestTelemetryFilter">
                  <Add name="MinimumDurationInMS" value="500"/>
                  <Add name="NotNeededResponseCodes" value="page1,page2"/>
                  <Add name="NotNeededUrls" value="url1,url2"/>
           </Processor>
```



### <a name="synthetic-source-filter"></a>Filtro de origem sintético

Filtra toda a telemetria que tem valores na propriedade sintética. Isso inclui solicitações de bots, aranhas e testes de disponibilidade.

Filtrar a telemetria de todas as solicitações sintéticas:


```XML

           <Processor type="SyntheticSourceFilter" />
```

Filtrar a telemetria para fontes sintéticas específicas:


```XML

           <Processor type="SyntheticSourceFilter" >
                  <Add name="NotNeeded" value="source1,source2"/>
           </Processor>
```

* `NotNeeded`-lista separada por vírgulas de nomes de origem sintético.

### <a name="telemetry-event-filter"></a>Filtro de eventos de telemetria

Filtra eventos personalizados (registrados usando [TrackEvent ()](../../azure-monitor/app/api-custom-events-metrics.md#trackevent)).


```XML

           <Processor type="TelemetryEventFilter" >
                  <Add name="NotNeededNames" value="event1, event2"/>
           </Processor>
```


* `NotNeededNames`-lista de nomes de eventos separados por vírgulas.


### <a name="trace-telemetry-filter"></a>Filtro de telemetria de rastreamento

Filtra rastreamentos de log (registrados usando [TrackTrace ()](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) ou um [coletor de estrutura de registro em log](java-trace-logs.md)).

```XML

           <Processor type="TraceTelemetryFilter">
                  <Add name="FromSeverityLevel" value="ERROR"/>
           </Processor>
```

* `FromSeverityLevel` os valores válidos são:
  *  FORA do filtro de todos os rastreamentos
  *  RASTREAMENTO-sem filtragem. igual ao nível de rastreamento
  *  INFORMAÇÕES-filtrar nível de rastreamento
  *  AVISO-filtrar rastreamento e informações
  *  ERRO-filtrar aviso, informações, rastreamento
  *  CRÍTICO-filtrar tudo menos crítico


## <a name="custom-filters"></a>Filtros personalizados

### <a name="1-code-your-filter"></a>1. codificar seu filtro

Em seu código, crie uma classe que implemente `TelemetryProcessor`:

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


### <a name="2-invoke-your-filter-in-the-configuration-file"></a>2. invocar seu filtro no arquivo de configuração

Em ApplicationInsights. xml:

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

### <a name="3-invoke-your-filter-java-spring"></a>3. invocar seu filtro (Spring do Java)

Para aplicativos baseados no Spring Framework, os processadores de telemetria personalizados devem ser registrados em sua classe de aplicativo principal como um Bean. Em seguida, eles serão transferidos por cabo quando o aplicativo for iniciado.

```Java
@Bean
public TelemetryProcessor successFilter() {
      return new SuccessFilter();
}
```

Você precisará criar seus próprios parâmetros de filtro no `application.properties` e aproveitar a estrutura de configuração externa do Spring boot para passar esses parâmetros para o filtro personalizado. 


## <a name="troubleshooting"></a>Resolução de problemas

*Meu filtro não está funcionando.*

* Verifique se você forneceu valores de parâmetro válidos. Por exemplo, as durações devem ser números inteiros. Valores inválidos farão com que o filtro seja ignorado. Se o filtro personalizado lançar uma exceção de um construtor ou de um método set, ele será ignorado.

## <a name="next-steps"></a>Passos seguintes

* [Amostragem](../../azure-monitor/app/sampling.md) -considere a amostragem como uma alternativa que não distorce suas métricas.
