---
title: Filtrar a azure Application Insights telemetria na sua aplicação web Java
description: Reduza o tráfego de telemetria filtrando os eventos que não precisa de monitorizar.
ms.topic: conceptual
ms.date: 3/14/2019
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 9a57874652fa5ee58f39483903be211305c683f2
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2020
ms.locfileid: "96601242"
---
# <a name="filter-telemetry-in-your-java-web-app"></a>Filtrar telemetria na sua aplicação web Java

> [!IMPORTANT]
> A abordagem recomendada para monitorizar as aplicações java é utilizar a auto-instrumentação sem alterar o código. Siga as diretrizes para o [agente Application Insights Java 3.0](./java-in-process-agent.md).

Os filtros fornecem uma forma de selecionar a telemetria que a sua [aplicação web Java envia para o Application Insights](java-get-started.md). Existem alguns filtros fora da caixa que pode usar, e também pode escrever os seus próprios filtros personalizados.

Os filtros fora da caixa incluem:

* Nível de severidade do rastreio
* URLs específicos, palavras-chave ou códigos de resposta
* Respostas rápidas - isto é, pedidos aos quais a sua app respondeu rapidamente
* Nomes específicos do evento

> [!NOTE]
> Os filtros distorcem as métricas da sua aplicação. Por exemplo, pode decidir que, para diagnosticar respostas lentas, irá definir um filtro para descartar tempos de resposta rápidos. Mas deve estar ciente de que os tempos médios de resposta reportados pela Application Insights serão então mais lentos do que a verdadeira velocidade, e a contagem de pedidos será menor do que a contagem real.
> Se isto é uma preocupação, utilize [a Amostragem.](./sampling.md)

## <a name="setting-filters"></a>Definição de filtros

Em ApplicationInsights.xml, adicione uma `TelemetryProcessors` secção como este exemplo:


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

[Inspecione o conjunto completo de processadores incorporados](https://github.com/Microsoft/ApplicationInsights-Java/tree/master/core/src/main/java/com/microsoft/applicationinsights/internal).

## <a name="built-in-filters"></a>Filtros embutidos

### <a name="metric-telemetry-filter"></a>Filtro de telemetria métrica

```XML

           <Processor type="MetricTelemetryFilter">
                  <Add name="NotNeeded" value="metric1,metric2"/>
           </Processor>
```

* `NotNeeded` - Lista separada por vírgulas de nomes métricos personalizados.


### <a name="page-view-telemetry-filter"></a>Filtro de telemetria de vista de página

```XML

           <Processor type="PageViewTelemetryFilter">
                  <Add name="DurationThresholdInMS" value="500"/>
                  <Add name="NotNeededNames" value="page1,page2"/>
                  <Add name="NotNeededUrls" value="url1,url2"/>
           </Processor>
```

* `DurationThresholdInMS` - Duração refere-se ao tempo de carregamento da página. Se isto estiver definido, as páginas que carregaram mais rapidamente do que desta vez não são reportadas.
* `NotNeededNames` - Lista separada por vírgulas de nomes de página.
* `NotNeededUrls` - Lista separada por vírgula de fragmentos de URL. Por exemplo, `"home"` filtra todas as páginas que têm "casa" na URL.


### <a name="request-telemetry-filter"></a>Solicitar filtro de telemetria


```XML

           <Processor type="RequestTelemetryFilter">
                  <Add name="MinimumDurationInMS" value="500"/>
                  <Add name="NotNeededResponseCodes" value="page1,page2"/>
                  <Add name="NotNeededUrls" value="url1,url2"/>
           </Processor>
```



### <a name="synthetic-source-filter"></a>Filtro de origem sintética

Filtra toda a telemetria que tem valores na propriedade SyntheticSource. Estes incluem pedidos de bots, aranhas e testes de disponibilidade.

Filtrar a telemetria para todos os pedidos sintéticos:


```XML

           <Processor type="SyntheticSourceFilter" />
```

Filtrar a telemetria para fontes sintéticas específicas:


```XML

           <Processor type="SyntheticSourceFilter" >
                  <Add name="NotNeeded" value="source1,source2"/>
           </Processor>
```

* `NotNeeded` - Lista separada por vírgulas de nomes de origem sintética.

### <a name="telemetry-event-filter"></a>Filtro de evento de telemetria

Filtra eventos personalizados (iniciados através do [TrackEvent).).](./api-custom-events-metrics.md#trackevent)


```XML

           <Processor type="TelemetryEventFilter" >
                  <Add name="NotNeededNames" value="event1, event2"/>
           </Processor>
```


* `NotNeededNames` - Lista separada por vírgulas de nomes de eventos.


### <a name="trace-telemetry-filter"></a>Trace Telemetria filtro

Filtra traços de registo (registados utilizando [trackTrace()](./api-custom-events-metrics.md#tracktrace) ou um [coletor de quadros de registo).](java-trace-logs.md)

```XML

           <Processor type="TraceTelemetryFilter">
                  <Add name="FromSeverityLevel" value="ERROR"/>
           </Processor>
```

* `FromSeverityLevel` os valores válidos são:
  *  OFF - Filtrar todos os vestígios
  *  TRACE - Sem filtragem. equivale ao nível de trace
  *  INFO - Filtrar o nível TRACE
  *  WARN - Filtrar TRACE e INFO
  *  ERRO - Filtrar WARN, INFO, TRACE
  *  CRITICAL - filtre tudo menos CRITICAL


## <a name="custom-filters"></a>Filtros personalizados

### <a name="1-code-your-filter"></a>1. Codificar o filtro

No seu código, crie uma classe que `TelemetryProcessor` implemente:

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


### <a name="2-invoke-your-filter-in-the-configuration-file"></a>2. Invoque o filtro no ficheiro de configuração

Em ApplicationInsights.xml:

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

### <a name="3-invoke-your-filter-java-spring"></a>3. Invoque o seu filtro (primavera de Java)

Para aplicações baseadas no quadro primavera, os processadores de telemetria personalizados devem ser registados na sua classe principal de aplicação como feijão. Serão então ligados automaticamente quando a aplicação começar.

```Java
@Bean
public TelemetryProcessor successFilter() {
      return new SuccessFilter();
}
```

Terá de criar os seus próprios parâmetros de filtro `application.properties` e aproveitar a estrutura de configuração externa do Spring Boot para passar esses parâmetros para o seu filtro personalizado. 


## <a name="troubleshooting"></a>Resolução de problemas

*O meu filtro não está a funcionar.*

* Verifique se forneceu valores de parâmetros válidos. Por exemplo, as durações devem ser inteiros. Valores inválidos farão com que o filtro seja ignorado. Se o filtro personalizado lançar uma exceção de um método de construção ou de conjunto, este será ignorado.

## <a name="next-steps"></a>Próximos passos

* [Amostragem](./sampling.md) - Considere a amostragem como uma alternativa que não distorce as suas métricas.

