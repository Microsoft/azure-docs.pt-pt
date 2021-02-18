---
title: Estimativa dos custos do plano de consumo em Funções Azure
description: Saiba como estimar melhor os custos que poderá incorrer ao executar a sua aplicação de função num plano de Consumo em Azure.
ms.date: 9/20/2019
ms.topic: conceptual
ms.openlocfilehash: 4967e0ff79a638891da4f784cf2f5f1ca4ddfe51
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100578559"
---
# <a name="estimating-consumption-plan-costs"></a>Estimativa dos custos do plano de consumo

Existem atualmente três tipos de planos de hospedagem para uma app que funciona em Azure Functions, com cada plano tendo o seu próprio modelo de preços: 

| Planear | Description |
| ---- | ----------- |
| [**Consumo**](consumption-plan.md) | Só é cobrado pelo tempo que a sua aplicação de função for executado. Este plano inclui uma página de[preços] [de subvenção gratuita]por subscrição.|
| [**Premium**](functions-premium-plan.md) | Fornece-lhe as mesmas funcionalidades e mecanismo de escala que o plano de Consumo, mas com um desempenho melhorado e acesso VNET. O custo baseia-se no seu nível de preços escolhido. Para saber mais, consulte [o plano Azure Functions Premium.](functions-premium-plan.md) |
| [**Dedicado (Serviço de Aplicações)**](dedicated-plan.md) <br/>(nível básico ou superior) | Quando precisar de correr em VMs dedicados ou isoladamente, use imagens personalizadas ou queira utilizar a sua capacidade de plano de serviço de aplicações em excesso. Utiliza [faturação regular do plano de aplicações.](https://azure.microsoft.com/pricing/details/app-service/) O custo baseia-se no seu nível de preços escolhido.|

Escolheu o plano que melhor suporta o desempenho da sua função e os requisitos de custos. Para saber mais, consulte [a escala e hospedagem de Funções Azure.](functions-scale.md)

Este artigo trata apenas do plano de Consumo, uma vez que este plano resulta em custos variáveis. Este artigo substitui o plano de consumo custo de faturação do artigo [FAQ.](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ)

As funções duradouras também podem funcionar num plano de consumo. Para saber mais sobre as considerações de custos ao utilizar funções duradouras, consulte [a faturação de Funções Duradouras.](./durable/durable-functions-billing.md)

## <a name="consumption-plan-costs"></a>Custos do plano de consumo

O *custo* de execução de uma única função é medido em *GB-segundos*. O custo de execução é calculado combinando o seu uso de memória com o seu tempo de execução. Uma função que funciona por mais tempo custa mais, assim como uma função que consome mais memória. 

Considere um caso em que a quantidade de memória utilizada pela função permanece constante. Neste caso, calcular o custo é uma simples multiplicação. Por exemplo, digamos que a sua função consumiu 0,5 GB durante 3 segundos. Então o custo de execução `0.5GB * 3s = 1.5 GB-seconds` é. 

Uma vez que o uso da memória muda ao longo do tempo, o cálculo é essencialmente a parte integrante do uso da memória ao longo do tempo.  O sistema faz este cálculo através da amostragem do uso da memória do processo (juntamente com os processos infantis) em intervalos regulares. Como mencionado na [página de preços,]o uso da memória é arredondado para o balde de 128-MB mais próximo. Quando o seu processo está a usar 160 MB, é cobrado por 256 MB. O cálculo tem em conta a concordância, que é múltiplas execuções de funções simultâneas no mesmo processo.

> [!NOTE]
> Embora o uso do CPU não seja considerado diretamente no custo de execução, pode ter um impacto no custo quando afeta o tempo de execução da função.

Para uma função desencadeada por HTTP, quando ocorre um erro antes do seu código de função começar a executar, não é cobrado para uma execução. Isto significa que 401 respostas da plataforma devido à validação da chave API ou à funcionalidade de Autenticação/Autorização do Serviço de Aplicações não contam com o custo de execução. Da mesma forma, as respostas do código de estado 5xx não são contadas quando ocorrem na plataforma antes de uma função de processamento do pedido. Uma resposta de 5xx gerada pela plataforma após o início do seu código de função ainda é contada como uma execução, mesmo que o erro não seja levantado pelo seu código de função.

## <a name="other-related-costs"></a>Outros custos relacionados

Ao estimar o custo global de execução das suas funções em qualquer plano, lembre-se que o tempo de execução de Funções utiliza vários outros serviços Azure, que são faturados separadamente. Ao calcular os preços das aplicações de função, quaisquer gatilhos e encadernações que tenha que se integrem com outros serviços Azure exigem que crie e pague por esses serviços adicionais. 

Para funções em execução num plano de Consumo, o custo total é o custo de execução das suas funções, acrescido do custo da largura de banda e serviços adicionais. 

Ao estimar os custos globais da sua aplicação de função e serviços conexos, utilize a [calculadora de preços Azure](https://azure.microsoft.com/pricing/calculator/?service=functions). 

| Custo relacionado | Descrição |
| ------------ | ----------- |
| **Conta de armazenamento** | Cada aplicação de função requer que tenha uma conta de [Armazenamento Azure](../storage/common/storage-introduction.md#types-of-storage-accounts)para fins gerais associada, que é [faturada separadamente](https://azure.microsoft.com/pricing/details/storage/). Esta conta é utilizada internamente pelo tempo de execução de Funções, mas também pode usá-la para desímes de armazenamento e encadernações. Se não tiver uma conta de armazenamento, uma é criada para si quando a aplicação de função é criada. Para saber mais, consulte os [requisitos da conta de Armazenamento.](storage-considerations.md#storage-account-requirements)|
| **Application Insights** | As funções dependem do [Application Insights](../azure-monitor/app/app-insights-overview.md) para proporcionar uma experiência de monitorização de alto desempenho para as suas aplicações de função. Embora não seja necessário, deve [ativar a integração de Insights de Aplicação](configure-monitoring.md#enable-application-insights-integration). Todos os meses são incluídos dados de telemetria gratuitos. Para saber mais, consulte [a página de preços do Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/). |
| **Largura de banda de rede** | Não se paga a transferência de dados entre os serviços da Azure na mesma região. No entanto, pode incorrer em custos para transferências de dados de saída para outra região ou fora de Azure. Para saber mais, consulte [os detalhes dos preços da largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/). |

## <a name="behaviors-affecting-execution-time"></a>Comportamentos que afetam o tempo de execução

Os seguintes comportamentos das suas funções podem ter impacto no tempo de execução:

+ **Gatilhos e encadernações**: O tempo necessário para ler a entrada e escrever a saída para as [encadernações](functions-triggers-bindings.md) da sua função é contado como tempo de execução. Por exemplo, quando a sua função utiliza uma ligação de saída para escrever uma mensagem para uma fila de armazenamento Azure, o seu tempo de execução inclui o tempo necessário para escrever a mensagem para a fila, que está incluída no cálculo do custo da função. 

+ **Execução assíncrona**: O tempo que a sua função aguarda pelos resultados de um pedido de assínio `await` (em C#) é contado como tempo de execução. O cálculo gb-segundo baseia-se no tempo de início e fim da função e na utilização da memória durante esse período. O que está a acontecer ao longo desse tempo em termos de atividade da CPU não é contabilizado no cálculo. Poderá reduzir os custos durante as operações assíncronos utilizando [funções duradouras.](durable/durable-functions-overview.md) Não estás cobrado pelo tempo que passa nas esperas em funções de orquestrador.

## <a name="viewing-cost-related-data"></a>Visualização de dados relacionados com os custos

Na [sua fatura,](../cost-management-billing/understand/download-azure-invoice.md)pode consultar os dados relacionados com o custo das **Execuções Totais - Funções** e **Tempo de Execução - Funções,** juntamente com os custos reais cobrados. No entanto, estes dados da fatura são um agregado mensal para um período de faturação anterior. 

### <a name="function-app-level-metrics"></a>Métricas de nível de aplicação de função

Para entender melhor o impacto de custos das suas funções, pode utilizar o Azure Monitor para visualizar métricas relacionadas com os custos atualmente geradas pelas suas aplicações de função. Pode utilizar o [explorador de métricas Azure Monitor](../azure-monitor/essentials/metrics-getting-started.md) no portal [Azure] ou NAS DERES PARA obter estes dados.

#### <a name="monitor-metrics-explorer"></a>Explorador de métricas de monitorização

Utilize [o explorador de métricas Azure Monitor](../azure-monitor/essentials/metrics-getting-started.md) para visualizar dados relacionados com o custo para as aplicações de função do seu plano de consumo num formato gráfico. 

1. No topo do [portal Azure] em **serviços de pesquisa, recursos e docs**  procure `monitor` e selecione **Monitor** em **Serviços**.

1. À esquerda, selecione **Métricas**  >  **Selecione um recurso** e, em seguida, use as definições abaixo da imagem para escolher a sua aplicação de função.

    ![Selecione o seu recurso de aplicação de função](media/functions-consumption-costing/select-a-resource.png)

      
    |Definição  |Valor sugerido  |Descrição  |
    |---------|---------|---------|
    | Subscrição    |  A sua subscrição  | A subscrição com a sua aplicação de função.  |
    | Grupo de recursos     | O seu grupo de recursos  | O grupo de recursos que contém a sua aplicação de função.   |
    | Tipo de recurso     |  Serviços Aplicacionais | As aplicações de função são mostradas como instâncias de Serviços de Aplicação no Monitor. |
    | Recurso     |  O seu aplicativo de função  | A aplicação de função para monitorizar.        |

1. Selecione **Aplicar** para escolher a sua aplicação de função como o recurso a monitorizar.

1. A partir **de Metric,** escolha **Contagem de Execução de Função** e **Soma** para **Agregação**. Isto adiciona a soma das contagens de execução durante o período escolhido ao gráfico.

    ![Definir uma métrica de aplicação de funções para adicionar ao gráfico](media/functions-consumption-costing/monitor-metrics-add-metric.png)

1. Selecione Adicione os passos **métricos** e repita os passos 2-4 para adicionar **Unidades de Execução** de Função à tabela. 

O gráfico resultante contém os totais para ambas as métricas de execução no intervalo de tempo escolhido, que neste caso é de duas horas.

![Gráfico de contagem de execução de funções e unidades de execução](media/functions-consumption-costing/monitor-metrics-execution-sum.png)

Como o número de unidades de execução é muito maior do que a contagem de execução, o gráfico apenas mostra unidades de execução.

Este gráfico mostra um total de 1,11 mil milhões `Function Execution Units` consumidos num período de duas horas, medido em MB-milissegundos. Para converter em GB-segundos, divida por 1024000. Neste exemplo, a aplicação de função consumiu `1110000000 / 1024000 = 1083.98` GB-segundos. Pode pegar neste valor e multiplicar-se pelo preço atual do tempo de execução na página de preços da [página de preços das Funções][], o que lhe dá o custo destas duas horas, assumindo que já utilizou quaisquer subsídios gratuitos de tempo de execução. 

#### <a name="azure-cli"></a>CLI do Azure

O [CLI Azure](/cli/azure/) tem comandos para recuperar métricas. Pode utilizar o CLI a partir de um ambiente de comando local ou diretamente do portal utilizando [a Azure Cloud Shell](../cloud-shell/overview.md). Por exemplo, o comando [de lista de métricas az monitor aguis](/cli/azure/monitor/metrics#az-monitor-metrics-list) retorna os dados de hora em hora durante o mesmo período de tempo utilizado anteriormente.

Certifique-se de que substitui `<AZURE_SUBSCRIPTON_ID>` o ID de subscrição Azure que executa o comando.

```azurecli-interactive
az monitor metrics list --resource /subscriptions/<AZURE_SUBSCRIPTION_ID>/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption --metric FunctionExecutionUnits,FunctionExecutionCount --aggregation Total --interval PT1H --start-time 2019-09-11T21:46:00Z --end-time 2019-09-11T23:18:00Z
```

Este comando devolve uma carga JSON que se parece com o seguinte exemplo:

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
Esta resposta em particular mostra que, a partir `2019-09-11T21:46` `2019-09-11T23:18` de, a aplicação consumiu 11100000000 MB-milissegundos (1083,98 GB-segundos).

### <a name="function-level-metrics"></a>Métricas de nível de função

As unidades de execução de funções são uma combinação do tempo de execução e do uso da sua memória, o que torna difícil a compreensão do uso da memória. Os dados de memória não são uma métrica atualmente disponível através do Azure Monitor. No entanto, se pretender otimizar o uso da memória da sua app, pode utilizar os dados do contador de desempenho recolhidos pela App Insights.  

Se ainda não o fez, [ative o Application Insights na sua aplicação de função.](configure-monitoring.md#enable-application-insights-integration) Com esta integração ativada, pode [consultar estes dados de telemetria no portal](analyze-telemetry-data.md#query-telemetry-data). 

[!INCLUDE [functions-consumption-metrics-queries](../../includes/functions-consumption-metrics-queries.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre aplicações de função de monitorização](functions-monitoring.md)

[página de preços]:https://azure.microsoft.com/pricing/details/functions/
[Portal do Azure]: https://portal.azure.com
