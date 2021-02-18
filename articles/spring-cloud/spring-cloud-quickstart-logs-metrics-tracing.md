---
title: Quickstart - Monitorização de aplicativos Azure Spring Cloud com registos, métricas e rastreio
description: Use o streaming de registos, a análise de registos, métricas e rastreio para monitorizar as aplicações de amostras da Piggymetrics na Azure Spring Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/04/2020
ms.custom: devx-track-java, devx-track-azurecli
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: a935b34c4fcebe9128d8af0316658072e20ddfbe
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100596218"
---
# <a name="quickstart-monitoring-azure-spring-cloud-apps-with-logs-metrics-and-tracing"></a>Quickstart: Monitorização de aplicativos Azure Spring Cloud com registos, métricas e rastreio

::: zone pivot="programming-language-csharp"
Com a capacidade de monitorização incorporada em Azure Spring Cloud, você pode depurar e monitorizar problemas complexos. A Azure Spring Cloud integra [o rastreio distribuído](https://steeltoe.io/docs/3/tracing/distributed-tracing) de Steeltoe com os Insights de [Aplicação](../azure-monitor/app/app-insights-overview.md)da Azure. Esta integração fornece registos, métricas e capacidade de rastreio distribuídos a partir do portal Azure.

Os seguintes procedimentos explicam como utilizar o Log Streaming, Log Analytics, Metrics e Distributed Tracing com a aplicação de amostra que implementou nos quickstarts anteriores.

## <a name="prerequisites"></a>Pré-requisitos

* Complete os arranques rápidos anteriores desta série:

  * [Serviço Azure Spring Cloud](spring-cloud-quickstart-provision-service-instance.md).
  * [Configurar o servidor de configuração Azure Spring Cloud](spring-cloud-quickstart-setup-config-server.md).
  * [Construa e implemente aplicativos.](spring-cloud-quickstart-deploy-apps.md)

## <a name="logs"></a>Registos

Existem duas formas de ver registos na Nuvem de primavera do Azure: **Registar streaming** de registos em tempo real por instância de aplicação ou **Log Analytics** para registos agregados com capacidade de consulta avançada.

### <a name="log-streaming"></a>Streaming de registos

Pode utilizar o streaming de registo no CLI Azure com o seguinte comando.

```azurecli
az spring-cloud app logs -n solar-system-weather -f
```

Verá uma saída semelhante ao seguinte exemplo:

```output
=> ConnectionId:0HM2HOMHT82UK => RequestPath:/weatherforecast RequestId:0HM2HOMHT82UK:00000003, SpanId:|e8c1682e-46518cc0202c5fd9., TraceId:e8c1682e-46518cc0202c5fd9, ParentId: => Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController.Get (Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather)
Executing action method Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController.Get (Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather) - Validation state: Valid
←[40m←[32minfo←[39m←[22m←[49m: Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController[0]

=> ConnectionId:0HM2HOMHT82UK => RequestPath:/weatherforecast RequestId:0HM2HOMHT82UK:00000003, SpanId:|e8c1682e-46518cc0202c5fd9., TraceId:e8c1682e-46518cc0202c5fd9, ParentId: => Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController.Get (Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather)
Retrieved weather data from 4 planets
←[40m←[32minfo←[39m←[22m←[49m: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[2]

=> ConnectionId:0HM2HOMHT82UK => RequestPath:/weatherforecast RequestId:0HM2HOMHT82UK:00000003, SpanId:|e8c1682e-46518cc0202c5fd9., TraceId:e8c1682e-46518cc0202c5fd9, ParentId: => Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController.Get (Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather)
Executing ObjectResult, writing value of type 'System.Collections.Generic.KeyValuePair`2[[System.String, System.Private.CoreLib, Version=4.0.0.0, Culture=neutral, PublicKeyToken=7cec85d7bea7798e],[System.String, System.Private.CoreLib, Version=4.0.0.0, Culture=neutral, PublicKeyToken=7cec85d7bea7798e]][]'.
←[40m←[32minfo←[39m←[22m←[49m: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[2]
```

> [!TIP]
> Use `az spring-cloud app logs -h` para explorar mais parâmetros e funcionalidade de fluxo de log.

### <a name="log-analytics"></a>Log Analytics

1. No portal Azure, vá ao **serviço | Página geral** e selecione **Registos** na secção **De Monitorização.** Selecione **Executar** numa das consultas de amostra para Azure Spring Cloud.

   [![Entrada de Logs Analytics ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png)](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png#lightbox)
    
1. Edite a consulta para remover as cláusulas Onde que limitam o visor a registos de aviso e erro.

1. Em `Run` seguida, selecione , e verá registos. Consulte [os docs Azure Log Analytics](../azure-monitor/logs/get-started-queries.md) para obter mais orientações sobre consultas de escrita.

   [![Logs Analítica consulta - Steeltoe ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query-steeltoe.png)](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query-steeltoe.png#lightbox)

## <a name="metrics"></a>Métricas

1. No portal Azure, vá ao **serviço | Página geral** e selecione **Métricas** na secção **de Monitorização.** Adicione a sua primeira métrica selecionando uma das métricas .NET em **Performance (.NET)** ou **Request (.NET)** na **métrica** e `Avg` para a **agregação** ver a linha do tempo para esta métrica.

   [![Entrada métrica - Steeltoe ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu-steeltoe.png)](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu-steeltoe.png#lightbox)
    
1. Clique em **Adicionar filtro** na barra de ferramentas, selecione para ver `App=solar-system-weather` a utilização do CPU apenas para a aplicação **de clima do sistema solar.**

   [![Use filtro em métricas - Steeltoe ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter-steeltoe.png)](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter-steeltoe.png#lightbox)

1. Desprezem o filtro criado no passo anterior, selecione **Apply Splitting** e selecione `App` para **Valores** para ver a utilização do CPU por diferentes aplicações.

   [![Aplicar divisão em métricas - Steeltoe ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split-steeltoe.png)](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split-steeltoe.png#lightbox)

## <a name="distributed-tracing"></a>Rastreio distribuído

1. No portal Azure, vá ao **serviço | Página de visão geral** e selecione **Rastreio distribuído** na secção **De Monitorização.** Em seguida, selecione o separador mapa de **aplicação 'Ver'** à direita.

   [![Entrada de rastreio distribuída - Steeltoe ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png)](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png#lightbox)

1. Agora pode ver o estado das chamadas entre apps. 

   [![Visão geral de rastreio distribuído - Steeltoe ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview-steeltoe.png)](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview-steeltoe.png#lightbox)
    
1. Selecione a ligação entre o **sistema solar-clima** e **o fornecedor de clima planetário** para ver mais detalhes como chamadas mais lentas por métodos HTTP.

   [![Rastreio distribuído - Steeltoe ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call-steeltoe.png)](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call-steeltoe.png#lightbox)
    
1. Por fim, **selecione Investigate Performance** para explorar uma análise de desempenho mais poderosa.

   [![Desempenho de rastreio distribuído - Steeltoe ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance-steeltoe.png)](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance-steeltoe.png#lightbox)
::: zone-end

::: zone pivot="programming-language-java"
Com a capacidade de monitorização incorporada em Azure Spring Cloud, você pode depurar e monitorizar problemas complexos. A Azure Spring Cloud integra [a Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) com os Insights de [Aplicação](../azure-monitor/app/app-insights-overview.md)da Azure. Esta integração fornece registos, métricas e capacidade de rastreio distribuídos a partir do portal Azure. Os seguintes procedimentos explicam como usar o log streaming, log analytics, métricas e rastreio distribuído com aplicações PiggyMetrics implementadas.

## <a name="prerequisites"></a>Pré-requisitos

Complete os passos anteriores: 

* [Provisionar um exemplo de Azure Spring Cloud](spring-cloud-quickstart-provision-service-instance.md)
* [Configurar o servidor config](spring-cloud-quickstart-setup-config-server.md)
* [Construa e implemente aplicativos.](spring-cloud-quickstart-deploy-apps.md)

## <a name="logs"></a>Registos

Existem duas formas de ver registos na Nuvem de primavera do Azure: **Registar streaming** de registos em tempo real por instância de aplicação ou **Log Analytics** para registos agregados com capacidade de consulta avançada.

### <a name="log-streaming"></a>Streaming de registos

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

Pode utilizar o streaming de registo no CLI Azure com o seguinte comando.

```azurecli
az spring-cloud app logs -s <service instance name> -g <resource group name> -n gateway -f
```

Verá troncos como este:

[![Streaming de registo a partir de Azure CLI ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-streaming-cli.png)](media/spring-cloud-quickstart-logs-metrics-tracing/logs-streaming-cli.png#lightbox)

> [!TIP]
> Use `az spring-cloud app logs -h` para explorar mais parâmetros e funcionalidades de fluxo de log.

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

Para obter os registos usando O Kit de Ferramentas Azure para IntelliJ:

1. Selecione **Azure Explorer,** em **seguida, Cloud primavera**.

1. Clique com o botão direito na aplicação de execução.

1. Selecione **'Registos** de streaming' da lista de drop-down.

   ![Selecione registos de streaming](media/spring-cloud-intellij-howto/streaming-logs.png)
    
1. Selecione **Exemplo**.

   ![Selecione a instância](media/spring-cloud-intellij-howto/select-instance.png)
    
1. O registo de streaming será visível na janela de saída.

   ![Saída de registo de streaming](media/spring-cloud-intellij-howto/streaming-log-output.png)

---
### <a name="log-analytics"></a>Log Analytics

1. Vá ao **serviço | Página geral** e selecione **Registos** na secção **De Monitorização.** Clique em **Executar** em uma das consultas de amostra para Azure Spring Cloud. 

   [![Entrada de Logs Analytics ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png)](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png#lightbox)
    
1. Então verá troncos filtrados. Consulte [os docs Azure Log Analytics](../azure-monitor/logs/get-started-queries.md) para obter mais orientações sobre consultas de escrita.

   [![Logs Consulta de analítica ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query.png)](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query.png#lightbox)

## <a name="metrics"></a>Métricas

1. Vá ao **serviço | Página geral** e selecione **Métricas** na secção **de Monitorização.** Adicione a sua primeira métrica selecionando `system.cpu.usage` para **Metric** e `Avg` para **Agregação** para ver a linha do tempo para uma utilização geral do CPU.

   [![Entrada de métricas ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu.png)](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu.png#lightbox)
    
1. Clique em **Adicionar filtro** na barra de ferramentas acima, selecione para ver `App=Gateway` a utilização do CPU apenas para a aplicação **gateway.**

   [![Use o filtro em métricas ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter.png)](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter.png#lightbox)

1. Desprezem o filtro acima, clique em **Aplicar Dividir** e selecione `App` para **Valores** para ver a utilização do CPU por diferentes aplicações.

   [![Aplicar divisão em métricas ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split.png)](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split.png#lightbox)

## <a name="distributed-tracing"></a>Rastreio distribuído

1. Vá ao **serviço | Página de visão geral** e selecione **Rastreio distribuído** na secção **De Monitorização.** Em seguida, clique no separador mapa de **aplicação Ver** à direita.

   [![Entrada de rastreio ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png) distribuída](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png#lightbox)

1. Agora pode ver o estado das chamadas entre aplicações Piggymetrics. 

   [![Visão geral ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview.png) do rastreio distribuído](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview.png#lightbox)
    
1. Clique no link entre **gateway** e **serviço de conta** para ver mais detalhes como chamadas mais lentas por métodos HTTP.

   [![Rastreio ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call.png) distribuído](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call.png#lightbox)
    
1. Por fim, clique em **Investigar o Desempenho** para explorar uma análise de desempenho mais poderosa.

   [![Desempenho de ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance.png) rastreio distribuído](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance.png#lightbox)

::: zone-end

## <a name="clean-up-resources"></a>Limpar os recursos

Nestes quickstarts, criou recursos Azure que continuarão a acumular encargos se permanecerem na sua subscrição. Se não espera precisar destes recursos no futuro, elimine o grupo de recursos utilizando o portal ou executando o seguinte comando na Cloud Shell:

```azurecli
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

Num início rápido anterior, também definiu o nome do grupo de recursos predefinido. Se não pretender continuar até ao próximo arranque rápido, limpe o padrão executando o seguinte comando CLI:

```azurecli
az configure --defaults group=
```

## <a name="next-steps"></a>Passos seguintes

Para explorar mais capacidades de monitorização da Azure Spring Cloud, consulte:

> [!div class="nextstepaction"]
> [Serviços de diagnóstico](diagnostic-services.md)
>
> [Rastreio distribuído](spring-cloud-tutorial-distributed-tracing.md)
>
> [Transmitir em fluxo registos em tempo real](spring-cloud-howto-log-streaming.md)
