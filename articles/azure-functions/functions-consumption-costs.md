---
title: Estimativa dos custos do plano de consumo em funções azure
description: Saiba como estimar melhor os custos que pode incorrer ao executar a sua aplicação de funções num plano de consumo em Azure.
ms.date: 9/20/2019
ms.topic: conceptual
ms.openlocfilehash: 0e3177d7c65eb1624441427f123e6f95095bdbbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76963993"
---
# <a name="estimating-consumption-plan-costs"></a>Estimativa dos custos do plano de consumo

Existem atualmente três tipos de planos de hospedagem para uma app que funciona em Funções Azure, com cada plano a ter o seu próprio modelo de preços: 

| Planear | Descrição |
| ---- | ----------- |
| [**Consumo**](functions-scale.md#consumption-plan) | Só é cobrado pelo tempo em que a sua aplicação de função funciona. Este plano inclui uma página de[preços] de [subvenção gratuita]numa base de subscrição.|
| [**Premium**](functions-scale.md#premium-plan) | Fornece-lhe as mesmas funcionalidades e mecanismo de escala que o plano de consumo, mas com um desempenho melhorado e acesso VNET. O custo baseia-se no seu nível de preços escolhido. Para saber mais, consulte o [plano Azure Functions Premium.](functions-premium-plan.md) |
| [**Dedicado (Serviço de Aplicações)**](functions-scale.md#app-service-plan) <br/>(nível básico ou superior) | Quando precisar de correr em VMs dedicados ou isoladamente, use imagens personalizadas ou queira utilizar a sua capacidade de plano de serviço de aplicações em excesso. Utiliza a faturação regular do plano de serviço de [aplicações.](https://azure.microsoft.com/pricing/details/app-service/) O custo baseia-se no seu nível de preços escolhido.|

Escolheu o plano que melhor suporta o desempenho da sua função e os requisitos de custos. Para saber mais, consulte a escala e o alojamento das [Funções Azure.](functions-scale.md)

Este artigo trata apenas do plano de Consumo, uma vez que este plano resulta em custos variáveis. Este artigo substitui o [plano de consumo a cobrar artigo seletiva faQ.](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ)

As Funções Duráveis também podem ser executadas num plano de consumo. Para saber mais sobre as considerações de custos ao utilizar Funções Duráveis, consulte a faturação das [Funções Duráveis](./durable/durable-functions-billing.md).

## <a name="consumption-plan-costs"></a>Custos do plano de consumo

O *custo* de execução de uma única função é medido em *segundos de GB*. O custo de execução é calculado combinando o seu uso de memória com o seu tempo de execução. Uma função que funciona por mais tempo custa mais, assim como uma função que consome mais memória. 

Considere um caso em que a quantidade de memória utilizada pela função permanece constante. Neste caso, calcular o custo é uma simples multiplicação. Por exemplo, diga que a sua função consumiu 0,5 GB durante 3 segundos. Então o `0.5GB * 3s = 1.5 GB-seconds`custo de execução é. 

Uma vez que o uso da memória muda ao longo do tempo, o cálculo é essencialmente a integral do uso da memória ao longo do tempo.  O sistema faz este cálculo através da amostragem da utilização da memória do processo (juntamente com os processos infantis) em intervalos regulares. Como mencionado na página de [preços,]o uso da memória é arredondado para o balde de 128 MB mais próximo. Quando o seu processo está a usar 160 MB, é cobrado por 256 MB. O cálculo tem em conta a conmoeda, que é múltiplas execuções de funções simultâneas no mesmo processo.

> [!NOTE]
> Embora o uso do CPU não seja diretamente considerado no custo de execução, pode ter um impacto no custo quando afeta o tempo de execução da função.

## <a name="other-related-costs"></a>Outros custos conexos

Ao estimar o custo global de funcionamento das suas funções em qualquer plano, lembre-se que o tempo de funcionamento das Funções utiliza vários outros serviços Azure, que são faturados separadamente. Ao calcular os preços das aplicações de funções, quaisquer gatilhos e encadernações que se integram com outros serviços Do Azure exigem que crie e pague por esses serviços adicionais. 

Para funções em execução num plano de consumo, o custo total é o custo de execução das suas funções, além do custo da largura de banda e serviços adicionais. 

Ao estimar os custos globais da sua app de funções e serviços conexos, utilize a calculadora de [preços Do Azure.](https://azure.microsoft.com/pricing/calculator/?service=functions) 

| Custo relacionado | Descrição |
| ------------ | ----------- |
| **Conta de armazenamento** | Cada aplicação de função requer que tenha uma conta de [Armazenamento Azure](../storage/common/storage-introduction.md#types-of-storage-accounts)de Propósito Geral associada , que é [faturada separadamente](https://azure.microsoft.com/pricing/details/storage/). Esta conta é utilizada internamente pelo tempo de funcionamento das Funções, mas também pode utilizá-la para gatilhos e encadernações de armazenamento. Se não tiver uma conta de armazenamento, é criada uma para si quando a aplicação de funções for criada. Para saber mais, consulte os requisitos da [conta de armazenamento.](storage-considerations.md#storage-account-requirements)|
| **Application Insights** | As funções dependem de Insights de [Aplicação](../azure-monitor/app/app-insights-overview.md) para proporcionar uma experiência de monitorização de alto desempenho para as suas aplicações de função. Embora não seja necessário, deve ativar a integração de Insights de [Aplicação.](functions-monitoring.md#enable-application-insights-integration) Todos os meses é incluída uma concessão gratuita de dados de telemetria. Para saber mais, consulte [a página de preços do Monitor Azure](https://azure.microsoft.com/pricing/details/monitor/). |
| **Largura de banda de rede** | Não paga a transferência de dados entre os serviços do Azure na mesma região. No entanto, pode incorrer em custos para transferências de dados de saída para outra região ou fora de Azure. Para saber mais, consulte os detalhes dos [preços da Largura de Banda](https://azure.microsoft.com/pricing/details/bandwidth/). |

## <a name="behaviors-affecting-execution-time"></a>Comportamentos que afetam o tempo de execução

Os seguintes comportamentos das suas funções podem afetar o tempo de execução:

+ **Gatilhos e encadernações**: O tempo de leitura da entrada e de escrever a saída para as [suas encadernações](functions-triggers-bindings.md) de função é contado como tempo de execução. Por exemplo, quando a sua função utiliza uma ligação de saída para escrever uma mensagem a uma fila de armazenamento Azure, o seu tempo de execução inclui o tempo de execução necessário para escrever a mensagem para a fila, que está incluída no cálculo do custo da função. 

+ **Execução assíncrona**: O tempo que a sua função`await` aguarda pelos resultados de um pedido de asincronização (em C#) é contado como tempo de execução. O cálculo de gb-segundo baseia-se no tempo de início e fim da função e no uso da memória durante esse período. O que está a acontecer ao longo desse tempo em termos de atividade da CPU não é contabilizado no cálculo. Poderá reduzir os custos durante operações assíncronas utilizando [funções duráveis.](durable/durable-functions-overview.md) Não é cobrado pelo tempo gasto em funções orquestradoras.

## <a name="view-execution-data"></a>Ver dados de execução

Na [sua fatura,](/azure/billing/billing-download-azure-invoice)pode ver os dados relacionados com os custos das **Execuções Totais - Funções** e Tempo de **Execução - Funções**, juntamente com os custos reais faturados. No entanto, estes dados de fatura são um agregado mensal para um período de faturaanterior anterior. 

Para melhor compreender o impacto de custos das suas funções, pode utilizar o Monitor Azure para ver as métricas relacionadas com os custos atualmente geradas pelas suas aplicações de função. Pode utilizar o explorador de [métricas Azure Monitor](../azure-monitor/platform/metrics-getting-started.md) no [portal Azure] ou NAS APIs REST para obter estes dados.

### <a name="monitor-metrics-explorer"></a>Monitorde o explorador de métricas

Utilize o explorador de [métricas Do Monitor Azure](../azure-monitor/platform/metrics-getting-started.md) para visualizar dados relacionados com o custo para as aplicações de função do seu plano de consumo num formato gráfico. 

1. No topo do [portal Azure] em **serviços de pesquisa, recursos e docs** pesquisa `monitor` e selecione **Monitor** ao abrigo de **Serviços.**

1. À esquerda, selecione **Métricas** > **Selecione um recurso**e, em seguida, use as definições abaixo da imagem para escolher a sua aplicação de função.

    ![Selecione o recurso da sua aplicação de função](media/functions-consumption-costing/select-a-resource.png)

      
    |Definição  |Valor sugerido  |Descrição  |
    |---------|---------|---------|
    | Subscrição    |  A sua subscrição  | A subscrição com a sua app de funções.  |
    | Grupo de recursos     | O seu grupo de recursos  | O grupo de recursos que contém a sua aplicação de função.   |
    | Tipo de recurso     |  Serviços Aplicacionais | As aplicações de função são mostradas como instâncias de Serviços de Aplicação no Monitor. |
    | Recurso     |  A sua aplicação de funções  | A aplicação de função para monitorizar.        |

1. Selecione **Aplicar** para escolher a sua aplicação de função como recurso a monitorizar.

1. De **Métrica**, escolha contagem de **execução** de funções e **soma** para **agregação**. Isto adiciona a soma das contagens de execução durante o período escolhido ao gráfico.

    ![Defina uma métrica de aplicação de funções para adicionar ao gráfico](media/functions-consumption-costing/monitor-metrics-add-metric.png)

1. **Selecione Adicionar passos métricos** e repetir passos 2-4 para adicionar unidades de execução de **função** ao gráfico. 

O gráfico resultante contém os totais para ambas as métricas de execução na faixa horente escolhida, que neste caso é de duas horas.

![Gráfico de contagem de execução de funções e unidades de execução](media/functions-consumption-costing/monitor-metrics-execution-sum.png)

Como o número de unidades de execução é muito maior do que a contagem de execução, o gráfico apenas mostra unidades de execução.

Este gráfico mostra um total de `Function Execution Units` 1,11 mil milhões consumidos num período de duas horas, medido em MB-milissegundos. Para converter em GB-segundos, divida por 1024000. Neste exemplo, a aplicação `1110000000 / 1024000 = 1083.98` de funções consumiu gb-segundos. Pode pegar neste valor e multiplicar-se pelo preço atual do tempo de execução na página de[preços]da página de preços [funções], o que lhe dá o custo destas duas horas, assumindo que já utilizou quaisquer subsídios gratuitos de tempo de execução. 

### <a name="azure-cli"></a>CLI do Azure

O [Azure CLI](/cli/azure/) tem comandos para recuperar métricas. Pode utilizar o CLI a partir de um ambiente de comando local ou diretamente a partir do portal utilizando [a Azure Cloud Shell](../cloud-shell/overview.md). Por exemplo, o comando de métricas de [monitorização az](/cli/azure/monitor/metrics#az-monitor-metrics-list) seguinte retorna os dados horários durante o mesmo período de tempo utilizado anteriormente.

Certifique-se `<AZURE_SUBSCRIPTON_ID>` de substituir pelo seu ID de subscrição Azure que executa o comando.

```azurecli-interactive
az monitor metrics list --resource /subscriptions/<AZURE_SUBSCRIPTION_ID>/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption --metric FunctionExecutionUnits,FunctionExecutionCount --aggregation Total --interval PT1H --start-time 2019-09-11T21:46:00Z --end-time 2019-09-11T23:18:00Z
```

Este comando devolve uma carga útil JSON que se parece com o seguinte exemplo:

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
Esta resposta em `2019-09-11T21:46` particular `2019-09-11T23:18`mostra que, de até à aplicação, a app consumiu 111000000000 MB-milissegundos (1083,98 GB-segundos).

## <a name="determine-memory-usage"></a>Determinar o uso da memória

As unidades de execução de funções são uma combinação do tempo de execução e do uso da sua memória, o que torna uma métrica difícil para entender o uso da memória. Os dados da memória não são uma métrica atualmente disponível através do Monitor Azure. No entanto, se pretender otimizar o uso da memória da sua aplicação, pode utilizar os dados do contador de desempenho recolhidos pela Application Insights.  

Se ainda não o fez, ative os Insights de [Aplicação na sua aplicação de funções](functions-monitoring.md#enable-application-insights-integration). Com esta integração ativada, pode consultar estes dados de [telemetria no portal](functions-monitoring.md#query-telemetry-data).  

Em **Monitorização,** selecione **Registos (Analytics)** e, em seguida, copie a seguinte consulta de telemetria e cole-a na janela de consulta e selecione **Executar**. Esta consulta devolve o uso total da memória em cada momento da amostra.

```
performanceCounters
| where name == "Private Bytes"
| project timestamp, name, value
```

Os resultados parecem ser o seguinte exemplo:

| carimbo \[de tempo UTC\]          | nome          | valor       |
|----------------------------|---------------|-------------|
| 9/12/2019, 1:05:14\.947 | Bytes Privados | 209,932,288 |
| 9/12/2019, 1:06:14\.994 | Bytes Privados | 212,189,184 |
| 9/12/2019, 1:06:30\.010 | Bytes Privados | 231,714,816 |
| 9/12/2019, 1:07:15\.040 | Bytes Privados | 210,591,744 |
| 9/12/2019, 1:12:16\.285 | Bytes Privados | 216,285,184 |
| 9/12/2019, 1:12:31\.376 | Bytes Privados | 235,806,720 |

## <a name="function-level-metrics"></a>Métricas de nível de função

O Azure Monitor rastreia as métricas ao nível dos recursos, que para funções é a aplicação de função. A integração de Insights de Aplicação emite métricas numa base por função. Aqui está uma consulta de análise de exemplo para obter a duração média de uma função:

```
customMetrics
| where name contains "Duration"
| extend averageDuration = valueSum / valueCount
| summarize averageDurationMilliseconds=avg(averageDuration) by name
```

| nome                       | médiaDuraÇãoMillisegundos |
|----------------------------|-----------------------------|
| AvgDurationMs De Gatilho de Fila | 16\.087                     |
| QueueTrigger MaxDurationMs | 90\.249                     |
| QueueTrigger MinDurationMs | 8\.522                      |

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre aplicações de função de monitorização](functions-monitoring.md)

[página de preços]:https://azure.microsoft.com/pricing/details/functions/
[Portal Azure]: https://portal.azure.com
