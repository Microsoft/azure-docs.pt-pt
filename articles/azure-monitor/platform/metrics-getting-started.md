---
title: Introdução ao Explorador de métricas do Azure
description: Saiba como criar seu primeiro gráfico de métricas com o Explorador de métricas do Azure.
author: vgorbenko
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 9d23d4b30ca4d394fb4afd0bb6620be6df179600
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57444944"
---
# <a name="getting-started-with-azure-metrics-explorer"></a>Introdução ao Explorador de métricas do Azure

## <a name="where-do-i-start"></a>Por onde começar

> [!NOTE]
> Este artigo abrange conceitos chave para o ajudar a começar a utilizar com o Explorador de métricas do Azure Monitor de novos utilizadores. Para obter mais documentação e informações sobre definições de gráfico avançado e métricas, veja [recursos avançados do Explorador de métricas do Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts).

Utilize o Explorador de métricas para investigar o estado de funcionamento e a utilização dos seus recursos. Comece pela seguinte ordem:

1. Comece por [escolher um recurso e uma métrica](#creating-your-first-metric-chart) e verá um gráfico básico. Em seguida, [selecionar um intervalo de tempo](#picking-time-range) que é relevante para a sua investigação.

1. Tente [aplicar os filtros de dimensão e divisão](#applying-dimension-filters-and-splitting). Os filtros e divisão permitem-lhe analisar quais segmentos da métrica contribuem para o valor da métrica geral e identificar valores atípicos possíveis.

1. Uso [definições avançadas](#advanced-chart-settings-and-next-steps) para personalizar o gráfico antes de afixar nos dashboards. [Configurar alertas](alerts-metric-overview.md) para receber notificações quando o valor da métrica excede ou cai abaixo de um limiar.

## <a name="create-your-first-metric-chart"></a>Crie seu primeiro gráfico de métricas

Para criar um gráfico de métricas, do recurso, grupo de recursos, subscrição ou exibição de Monitor do Azure, abra a **métricas** separador e siga estes passos:

1. Utilizando o selecionador de recursos, selecione o recurso para o qual pretende ver as métricas. (O recurso está pré-selecionada se abriu **métricas** no contexto de um recurso específico).

    > ![Selecionar um recurso](./media/metrics-getting-started/resource-picker.png)

2. Para alguns recursos, tem de escolher um espaço de nomes. O espaço de nomes é apenas uma forma de organizar as métricas para que pode encontrá-los facilmente. Por exemplo, contas de armazenamento têm espaços de nomes separados para armazenar as métricas de ficheiros, tabelas, Blobs e filas. Muitos tipos de recursos tem apenas um espaço de nomes.

3. Selecione uma métrica de uma lista de métricas disponíveis.

    > ![Selecionar uma métrica](./media/metrics-getting-started/metric-picker.png)

4. Opcionalmente, pode alterar a agregação de métricas. Por exemplo, pode desejar seu gráfico para mostrar os valores mínimo, máximos ou médios da métrica.

> [!NOTE]
> Utilize o **adicionar métrica** botão e repita estes passos, se quiser ver várias métricas desenhadas no mesmo gráfico. Para vários gráficos numa vista, selecione o **adicionar gráfico** botão na parte superior.

## <a name="pick-a-time-range"></a>Escolher um intervalo de tempo

Por predefinição, o gráfico mostra as mais recentes 24 horas de dados de métricas. Utilize o **Seletor de hora** painel para alterar o intervalo de tempo, ampliar ou reduzir o tempo limite no gráfico. 

![Painel de intervalo de tempo de alteração](./media/metrics-getting-started/time-picker.png)

## <a name="apply-dimension-filters-and-splitting"></a>Aplicar filtros de dimensão e divisão

[Filtragem](metrics-charts.md#apply-filters-to-charts) e [divisão](metrics-charts.md#apply-splitting-to-a-chart) são poderosas ferramentas de diagnóstico para as métricas que têm dimensões. Esses recursos mostram como vários segmentos de métrica ("valores de dimensão") afetam o valor geral da métrica e permitem-lhe identificar valores atípicos possíveis.

- **Filtragem** permite-lhe escolher quais valores de dimensão estão incluídos no gráfico. Por exemplo, pode querer mostrar pedidos com êxito quando a criação de gráficos a *tempo de resposta do servidor* métrica. Precisaria aplicar o filtro no *sucesso de pedido* dimensão. 

- **A divisão** controles se o gráfico exibe separado para cada valor de uma dimensão as linhas ou agrega os valores numa única linha. Por exemplo, pode ver uma linha para um tempo de resposta médio em todas as instâncias de servidor ou ver linhas separadas para cada servidor. Precisaria aplicam-se de que a divisão no *instância de servidor* dimensão para ver as linhas separadas.

Ver [exemplos dos gráficos](metric-chart-samples.md) que tem filtragem e a divisão de aplicadas. O artigo mostra-lhe que os passos foram utilizados para configurar os gráficos.

## <a name="advanced-chart-settings-and-next-steps"></a>Definições do gráfico avançado e os passos seguintes

Pode personalizar o estilo do gráfico, título e modificar as definições do gráfico avançado. Quando terminar com a personalização, afixá-la a um dashboard para guardar o seu trabalho. Também pode configurar alertas de métricas. Siga [documentação do produto](metrics-charts.md) saber mais sobre estes e outros recursos avançados do Explorador de métricas do Azure Monitor.

## <a name="next-steps"></a>Passos Seguintes

* [Ver uma lista de métricas disponíveis para serviços do Azure](metrics-supported.md)
* [Saiba mais sobre funcionalidades avançadas do Explorador de métricas](metrics-charts.md)
* [Veja exemplos de gráficos configurados](metric-chart-samples.md)
