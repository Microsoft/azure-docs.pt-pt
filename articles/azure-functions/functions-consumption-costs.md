---
title: Estimando os custos do plano de consumo em Azure Functions
description: Saiba como estimar melhor os custos que você pode incorrer ao executar seu aplicativo de funções em um plano de consumo no Azure.
ms.date: 9/20/2019
ms.topic: conceptual
ms.openlocfilehash: 0e3177d7c65eb1624441427f123e6f95095bdbbd
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963993"
---
# <a name="estimating-consumption-plan-costs"></a>Estimando os custos do plano de consumo

Atualmente, há três tipos de planos de hospedagem para um aplicativo que é executado no Azure Functions, com cada plano com seu próprio modelo de preços: 

| Plano | Descrição |
| ---- | ----------- |
| [**Utilização**](functions-scale.md#consumption-plan) | Você é cobrado apenas pelo tempo em que seu aplicativo de funções é executado. Esse plano inclui uma[página de preços] de [concessão gratuita]em uma base por assinatura.|
| [**Especiais**](functions-scale.md#premium-plan) | Fornece os mesmos recursos e mecanismo de dimensionamento que o plano de consumo, mas com o desempenho aprimorado e o acesso à VNET. O custo é baseado no tipo de preço escolhido. Para saber mais, confira [Azure Functions plano Premium](functions-premium-plan.md). |
| [**Dedicado (serviço de aplicativo)** ](functions-scale.md#app-service-plan) <br/>(camada básica ou superior) | Quando você precisa executar em VMs dedicadas ou isoladamente, use imagens personalizadas ou deseja usar o excesso de capacidade do plano do serviço de aplicativo. Usa a [cobrança regular do plano do serviço de aplicativo](https://azure.microsoft.com/pricing/details/app-service/). O custo é baseado no tipo de preço escolhido.|

Você escolheu o plano que melhor dá suporte aos requisitos de desempenho e custo de sua função. Para saber mais, consulte [Azure Functions escala e hospedagem](functions-scale.md).

Este artigo lida apenas com o plano de consumo, pois esse plano resulta em custos variáveis. Este artigo substitui o artigo [perguntas frequentes sobre cobrança de custos do plano de consumo](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ) .

Durable Functions também pode ser executado em um plano de consumo. Para saber mais sobre as considerações de custo ao usar Durable Functions, consulte [Durable Functions cobrança](./durable/durable-functions-billing.md).

## <a name="consumption-plan-costs"></a>Custos do plano de consumo

O *custo* de execução de uma única execução de função é medido em *GB-segundos*. O custo de execução é calculado pela combinação de seu uso de memória com o tempo de execução. Uma função que é executada por mais custa mais, como uma função que consome mais memória. 

Considere um caso em que a quantidade de memória usada pela função permaneça constante. Nesse caso, o cálculo do custo é uma multiplicação simples. Por exemplo, digamos que sua função consumiu 0,5 GB por 3 segundos. Em seguida, o custo de execução é `0.5GB * 3s = 1.5 GB-seconds`. 

Como o uso de memória muda ao longo do tempo, o cálculo é basicamente o integral do uso de memória ao longo do tempo.  O sistema faz esse cálculo por meio da amostragem do uso de memória do processo (juntamente com processos filho) em intervalos regulares. Conforme mencionado na [página de preços], o uso de memória é arredondado para o bucket de 128 MB mais próximo. Quando seu processo estiver usando 160 MB, você será cobrado por 256 MB. O cálculo leva em conta a simultaneidade, que é várias execuções de função simultâneas no mesmo processo.

> [!NOTE]
> Embora o uso da CPU não seja considerado diretamente no custo de execução, ele pode ter um impacto no custo quando ele afeta o tempo de execução da função.

## <a name="other-related-costs"></a>Outros custos relacionados

Ao estimar o custo geral da execução de suas funções em qualquer plano, lembre-se de que o tempo de execução do Functions usa vários outros serviços do Azure, que são cobrados separadamente. Ao calcular os preços dos aplicativos de funções, os gatilhos e associações que você tem que se integram a outros serviços do Azure exigem que você crie e pague por esses serviços adicionais. 

Para funções em execução em um plano de consumo, o custo total é o custo de execução de suas funções, além do custo da largura de banda e de serviços adicionais. 

Ao estimar os custos gerais do seu aplicativo de funções e dos serviços relacionados, use a [calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/?service=functions). 

| Custo relacionado | Descrição |
| ------------ | ----------- |
| **Conta de armazenamento** | Cada aplicativo de funções requer que você tenha um associado Uso Geral [conta de armazenamento do Azure](../storage/common/storage-introduction.md#types-of-storage-accounts), que é [cobrado separadamente](https://azure.microsoft.com/pricing/details/storage/). Essa conta é usada internamente pelo tempo de execução do functions, mas você também pode usá-la para gatilhos e associações de armazenamento. Se você não tiver uma conta de armazenamento, uma será criada para você quando o aplicativo de funções for criado. Para saber mais, consulte [requisitos da conta de armazenamento](storage-considerations.md#storage-account-requirements).|
| **Application Insights** | As funções dependem de [Application insights](../azure-monitor/app/app-insights-overview.md) para fornecer uma experiência de monitoramento de alto desempenho para seus aplicativos de funções. Embora não seja necessário, você deve [habilitar a integração de Application insights](functions-monitoring.md#enable-application-insights-integration). Uma concessão gratuita de dados de telemetria é incluída todos os meses. Para saber mais, consulte [a página de preços do Azure monitor](https://azure.microsoft.com/pricing/details/monitor/). |
| **Largura de banda da rede** | Você não paga pela transferência de dados entre os serviços do Azure na mesma região. No entanto, você pode incorrer em custos para transferências de dados de saída para outra região ou fora do Azure. Para saber mais, consulte [detalhes de preços de largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/). |

## <a name="behaviors-affecting-execution-time"></a>Comportamentos que afetam o tempo de execução

Os seguintes comportamentos de suas funções podem afetar o tempo de execução:

+ **Gatilhos e associações**: o tempo necessário para ler a entrada e gravar a saída em suas [associações de função](functions-triggers-bindings.md) é contado como tempo de execução. Por exemplo, quando sua função usa uma associação de saída para gravar uma mensagem em uma fila de armazenamento do Azure, o tempo de execução inclui o tempo necessário para gravar a mensagem na fila, que está incluída no cálculo do custo da função. 

+ **Execução assíncrona**: o tempo que sua função aguarda para os resultados de uma solicitação assíncrona (`await` C#em) é contado como tempo de execução. O cálculo de GB por segundo é baseado na hora de início e de término da função e no uso de memória nesse período. O que está acontecendo nesse tempo em termos de atividade de CPU não é acrescentado ao cálculo. Talvez seja possível reduzir os custos durante operações assíncronas usando [Durable Functions](durable/durable-functions-overview.md). Você não é cobrado pelo tempo gasto em Awaits em funções de orquestrador.

## <a name="view-execution-data"></a>Exibir dados de execução

Em [sua fatura](/azure/billing/billing-download-azure-invoice), você pode exibir os dados relacionados ao custo das funções de **execuções totais** e funções de **tempo de execução**, juntamente com os custos de cobrança reais. No entanto, esses dados da fatura são uma agregação mensal para um período de nota fiscal passada. 

Para entender melhor o impacto de custos de suas funções, você pode usar Azure Monitor para exibir as métricas de custo que estão sendo geradas atualmente por seus aplicativos de funções. Você pode usar o [Azure monitor métricas Explorer](../azure-monitor/platform/metrics-getting-started.md) no [portal do Azure] ou APIs REST para obter esses dados.

### <a name="monitor-metrics-explorer"></a>Monitorar métricas Explorer

Use [Azure monitor métricas Explorer](../azure-monitor/platform/metrics-getting-started.md) para exibir dados relacionados ao custo para seus aplicativos de função de plano de consumo em um formato gráfico. 

1. Na parte superior da [portal do Azure] em Pesquisar **serviços, recursos e** pesquisa de documentos `monitor` e selecione **monitorar** em **Serviços**.

1. À esquerda, selecione **métricas** > **Selecione um recurso**e, em seguida, use as configurações abaixo da imagem para escolher seu aplicativo de funções.

    ![Selecione o recurso de aplicativo de funções](media/functions-consumption-costing/select-a-resource.png)

      
    |Definição  |Valor sugerido  |Descrição  |
    |---------|---------|---------|
    | Subscrição    |  A sua subscrição  | A assinatura com seu aplicativo de funções.  |
    | Grupo de recursos     | Seu grupo de recursos  | O grupo de recursos que contém seu aplicativo de funções.   |
    | Tipo de recurso     |  Serviços Aplicacionais | Os aplicativos de funções são mostrados como instâncias de serviços de aplicativos no monitor. |
    | Recurso     |  Seu aplicativo de funções  | O aplicativo de funções a ser monitorado.        |

1. Selecione **aplicar** para escolher o aplicativo de funções como o recurso a ser monitorado.

1. Em **métrica**, escolha **contagem de execução de função** e **soma** para **agregação**. Isso adiciona a soma das contagens de execução durante o período escolhido ao gráfico.

    ![Definir uma métrica de aplicativo de funções para adicionar ao gráfico](media/functions-consumption-costing/monitor-metrics-add-metric.png)

1. Selecione **Adicionar métrica** e repita as etapas 2-4 para adicionar **unidades de execução de função** ao gráfico. 

O gráfico resultante contém os totais para ambas as métricas de execução no intervalo de tempo escolhido, que nesse caso é de duas horas.

![Grafo de contagens de execução de função e unidades de execução](media/functions-consumption-costing/monitor-metrics-execution-sum.png)

Como o número de unidades de execução é muito maior do que a contagem de execução, o gráfico apenas mostra as unidades de execução.

Este gráfico mostra um total de 1.110.000.000 `Function Execution Units` consumidos em um período de duas horas, medido em MB-milissegundos. Para converter em GB-segundos, divida por 1024000. Neste exemplo, o aplicativo de funções consumiu `1110000000 / 1024000 = 1083.98` GB-segundos. Você pode usar esse valor e multiplicar pelo preço atual do tempo de execução na[página de preços]da [página de preços do Functions], que oferece o custo dessas duas horas, supondo que você já tenha usado qualquer concessão gratuita de tempo de execução. 

### <a name="azure-cli"></a>CLI do Azure

O [CLI do Azure](/cli/azure/) tem comandos para recuperar métricas. Você pode usar a CLI de um ambiente de comando local ou diretamente do portal usando [Azure cloud Shell](../cloud-shell/overview.md). Por exemplo, o seguinte comando [AZ monitor de lista de métricas](/cli/azure/monitor/metrics#az-monitor-metrics-list) retorna dados por hora no mesmo período de tempo usado antes.

Certifique-se de que substitui `<AZURE_SUBSCRIPTON_ID>` com o seu ID de subscrição Azure que executa o comando.

```azurecli-interactive
az monitor metrics list --resource /subscriptions/<AZURE_SUBSCRIPTION_ID>/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption --metric FunctionExecutionUnits,FunctionExecutionCount --aggregation Total --interval PT1H --start-time 2019-09-11T21:46:00Z --end-time 2019-09-11T23:18:00Z
```

Esse comando retorna uma carga JSON semelhante ao exemplo a seguir:

```json
{
  "cost": 0.0,
  "interval": "1:00:00",
  "namespace": "Microsoft.Web/sites",
  "resourceregion": "centralus",
  "timespan": "2019-09-11T21:46:00Z/2019-09-11T23:18:00Z",
  "value": [
    {
      "id": "/subscriptions/XXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXX/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption/providers/Microsoft.Insights/metrics/FunctionExecutionUnits",
      "name": {
        "localizedValue": "Function Execution Units",
        "value": "FunctionExecutionUnits"
      },
      "resourceGroup": "metrics-testing-consumption",
      "timeseries": [
        {
          "data": [
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T21:46:00+00:00",
              "total": 793294592.0
            },
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T22:46:00+00:00",
              "total": 316576256.0
            }
          ],
          "metadatavalues": []
        }
      ],
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    },
    {
      "id": "/subscriptions/XXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXX/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption/providers/Microsoft.Insights/metrics/FunctionExecutionCount",
      "name": {
        "localizedValue": "Function Execution Count",
        "value": "FunctionExecutionCount"
      },
      "resourceGroup": "metrics-testing-consumption",
      "timeseries": [
        {
          "data": [
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T21:46:00+00:00",
              "total": 33538.0
            },
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T22:46:00+00:00",
              "total": 13040.0
            }
          ],
          "metadatavalues": []
        }
      ],
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    }
  ]
}
```
Essa resposta específica mostra que de `2019-09-11T21:46` para `2019-09-11T23:18`, o aplicativo consumiu 1110000000 MB-milissegundos (1083,98 GB-segundos).

## <a name="determine-memory-usage"></a>Determinar o uso de memória

As unidades de execução de função são uma combinação de tempo de execução e seu uso de memória, o que torna uma métrica difícil para entender o uso de memória. Os dados de memória não são uma métrica atualmente disponível por meio de Azure Monitor. No entanto, se você quiser otimizar o uso de memória do seu aplicativo, o poderá usar os dados do contador de desempenho coletados pelo Application Insights.  

Se você ainda não tiver feito isso, [habilite Application insights em seu aplicativo de funções](functions-monitoring.md#enable-application-insights-integration). Com essa integração habilitada, você pode [consultar esses dados de telemetria no portal](functions-monitoring.md#query-telemetry-data).  

Em **monitoramento**, selecione **logs (análise)** , em seguida, copie a seguinte consulta de telemetria e cole-a na janela de consulta e selecione **executar**. Essa consulta retorna o uso de memória total em cada tempo de amostra.

```
performanceCounters
| where name == "Private Bytes"
| project timestamp, name, value
```

Os resultados são semelhantes ao exemplo a seguir:

| carimbo de tempo \[\] UTC          | nome          | valor       |
|----------------------------|---------------|-------------|
| 9/12/2019, 1:05:14\.947 AM | Bytes Privados | 209.932.288 |
| 9/12/2019, 1:06:14\.994 AM | Bytes Privados | 212.189.184 |
| 9/12/2019, 1:06:30\.010 | Bytes Privados | 231.714.816 |
| 9/12/2019, 1:07:15\.040 AM | Bytes Privados | 210.591.744 |
| 9/12/2019, 1:12:16\.285 AM | Bytes Privados | 216.285.184 |
| 9/12/2019, 1:12:31\.376 AM | Bytes Privados | 235.806.720 |

## <a name="function-level-metrics"></a>Métricas de nível de função

Azure Monitor acompanha as métricas no nível de recurso, que para funções é o aplicativo de funções. A integração do Application Insights emite métricas de acordo com a função. Aqui está um exemplo de consulta de análise para obter a duração média de uma função:

```
customMetrics
| where name contains "Duration"
| extend averageDuration = valueSum / valueCount
| summarize averageDurationMilliseconds=avg(averageDuration) by name
```

| nome                       | averageDurationMilliseconds |
|----------------------------|-----------------------------|
| QueueTrigger AvgDurationMs | 16\.087                     |
| QueueTrigger MaxDurationMs | 90\.249                     |
| QueueTrigger MinDurationMs | 8\.522                      |

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre como monitorar aplicativos de funções](functions-monitoring.md)

[página de preços]: https://azure.microsoft.com/pricing/details/functions/
[Portal do Azure]: https://portal.azure.com
