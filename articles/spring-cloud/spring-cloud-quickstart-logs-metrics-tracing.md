---
title: Quickstart - Monitorização de aplicativos Azure Spring Cloud com registos, métricas e rastreio
description: Use o streaming de registos, a análise de registos, métricas e rastreio para monitorizar as aplicações de amostras da Piggymetrics na Azure Spring Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/04/2020
ms.custom: devx-track-java
ms.openlocfilehash: f9f03c355e1e619d004c8ec8c1cc2f91932db744
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89046838"
---
# <a name="quickstart-monitoring-azure-spring-cloud-apps-with-logs-metrics-and-tracing"></a>Quickstart: Monitorização de aplicativos Azure Spring Cloud com registos, métricas e rastreio

Com a capacidade de monitorização incorporada em Azure Spring Cloud, você pode depurar e monitorizar problemas complexos. A Azure Spring Cloud integra [a Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) com os Insights de [Aplicação](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)da Azure. Esta integração fornece registos, métricas e capacidade de rastreio distribuídos a partir do portal Azure. Os seguintes procedimentos explicam como usar o log streaming, log analytics, métricas e rastreio distribuído com aplicações PiggyMetrics implementadas.

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

1. Vá ao **serviço! Página geral** e selecione **Registos** na secção **De Monitorização.** Clique em **Executar** em uma das consultas de amostra para Azure Spring Cloud. 

   [![Entrada de Logs Analytics ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png)](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png#lightbox)
    
1. Então verá troncos filtrados. Consulte [os docs Azure Log Analytics](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries) para obter mais orientações sobre consultas de escrita.

   [![Logs Consulta de analítica ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query.png)](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query.png#lightbox)

## <a name="metrics"></a>Métricas

1. Vá ao **serviço! Página geral** e selecione **Métricas** na secção **de Monitorização.** Adicione a sua primeira métrica selecionando `system.cpu.usage` para **Metric** e `Avg` para **Agregação** para ver a linha do tempo para uma utilização geral do CPU.

   [![Entrada de métricas ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu.png)](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu.png#lightbox)
    
1. Clique em **Adicionar filtro** na barra de ferramentas acima, selecione para ver `App=Gateway` a utilização do CPU apenas para a aplicação **gateway.**

   [![Use o filtro em métricas ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter.png)](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter.png#lightbox)

1. Desprezem o filtro acima, clique em **Aplicar Dividir** e selecione `App` para **Valores** para ver a utilização do CPU por diferentes aplicações.

   [![Aplicar divisão em métricas ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split.png)](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split.png#lightbox)

## <a name="distributed-tracing"></a>Rastreio distribuído

1. Vá ao **serviço! Página de visão geral** e selecione **Rastreio distribuído** na secção **De Monitorização.** Em seguida, clique no separador mapa de **aplicação Ver** à direita.

   [![Entrada de rastreio ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png) distribuída](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png#lightbox)

1. Agora pode ver o estado das chamadas entre aplicações Piggymetrics. 

   [![Visão geral ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview.png) do rastreio distribuído](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview.png#lightbox)
    
1. Clique no link entre **gateway** e **serviço de conta** para ver mais detalhes como chamadas mais lentas por métodos HTTP.

   [![Rastreio ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call.png) distribuído](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call.png#lightbox)
    
1. Por fim, clique em **Investigar o Desempenho** para explorar uma análise de desempenho mais poderosa.

   [![Desempenho de ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance.png) rastreio distribuído](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance.png#lightbox)

## <a name="clean-up-resources"></a>Limpar os recursos

Nos passos anteriores, criou os recursos do Azure num grupo de recursos. Se não espera precisar destes recursos no futuro, elimine o grupo de recursos do portal ou executando o seguinte comando na Cloud Shell:

```azurecli
az group delete --name <your resource group name; for example: hellospring-1558400876966-rg> --yes
```

Nos passos anteriores, também definiu o nome do grupo de recursos predefinido. Para limpar o padrão, executar o seguinte comando na Cloud Shell:

```azurecli
az configure --defaults group=
```

## <a name="next-steps"></a>Passos seguintes

Para explorar mais sobre a capacidade de monitorização fora da caixa para Azure Spring Cloud, consulte:

> [!div class="nextstepaction"]
> Serviços de [diagnóstico](diagnostic-services.md) 
>  [Rastreio](spring-cloud-tutorial-distributed-tracing.md) 
>  distribuído [Registos de fluxo em tempo real](spring-cloud-howto-log-streaming.md)
