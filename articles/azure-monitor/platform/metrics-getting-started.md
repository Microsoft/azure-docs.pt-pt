---
title: Começando com o explorador de métricas Azure
description: Aprenda a criar o seu primeiro gráfico métrico com o explorador de métricas Azure.
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 920ff19b162843d1c2d631ad192ce1b3c4273a68
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84696988"
---
# <a name="getting-started-with-azure-metrics-explorer"></a>Getting started with Azure Metrics Explorer (Introdução ao Explorador de Métricas do Azure)

## <a name="where-do-i-start"></a>Por onde começo
O explorador de métricas Azure Monitor é um componente do portal Microsoft Azure que permite traçar gráficos, correlacionar visualmente tendências e investigar picos e mergulhos nos valores das métricas. Use o explorador de métricas para investigar a saúde e utilização dos seus recursos. Comece pela seguinte ordem:

1. [Escolha um recurso e uma métrica](#create-your-first-metric-chart) e verá um gráfico básico. Em [seguida, selecione um intervalo de tempo](#select-a-time-range) que seja relevante para a sua investigação.

1. Tente [aplicar filtros de dimensão e dividir](#apply-dimension-filters-and-splitting). Os filtros e a divisão permitem analisar quais os segmentos da métrica que contribuem para o valor métrico geral e identificar possíveis outliers.

1. Utilize [configurações avançadas](#advanced-chart-settings) para personalizar o gráfico antes de fixar nos painéis de instrumentos. [Configure alertas](alerts-metric-overview.md) para receber notificações quando o valor métrico exceder ou descer abaixo de um limiar.

## <a name="create-your-first-metric-chart"></a>Crie o seu primeiro gráfico métrico

Para criar um gráfico métrico, a partir do seu recurso, grupo de recursos, subscrição ou vista Azure Monitor, abra o **separador Métricas** e siga estes passos:

1. Utilizando o selecionador de recursos, selecione o recurso para o qual deseja ver métricas. (O recurso é pré-selecionado se abrir **métricas** no contexto de um recurso específico).

    > ![Selecionar um recurso](./media/metrics-getting-started/resource-picker.png)

2. Para alguns recursos, você deve escolher um espaço de nome. O espaço de nome é apenas uma forma de organizar métricas para que possa encontrá-las facilmente. Por exemplo, as contas de armazenamento têm espaços de nome separados para armazenar ficheiros, tabelas, blobs e métricas de filas. Muitos tipos de recursos têm apenas um espaço de nome.

3. Selecione uma métrica de uma lista de métricas disponíveis.

    > ![Selecione uma métrica](./media/metrics-getting-started/metric-picker.png)

4. Opcionalmente, pode alterar a agregação métrica. Por exemplo, pode querer que a sua tabela mostre valores mínimos, máximos ou médios da métrica.

> [!NOTE]
> Utilize o botão **métrico Adicionar** e repita estes passos se quiser ver várias métricas traçadas no mesmo gráfico. Para vários gráficos numa vista, selecione o **botão de gráfico Adicionar** em cima.

## <a name="select-a-time-range"></a>Selecione um intervalo de tempo

Por padrão, o gráfico mostra as 24 horas mais recentes de dados de métricas. Utilize o painel **de recolha de tempo** para alterar o intervalo de tempo, fazer zoom ou ampliar a sua tabela. 

![Alterar painel de intervalo de tempo](./media/metrics-getting-started/time-picker.png)

> [!NOTE]
> Utilize a **escova de tempo** para investigar uma área interessante do gráfico (pico ou um mergulho). Coloque o ponteiro do rato no início da área, clique e segure o botão do rato esquerdo, arraste para o outro lado da área e, em seguida, liberte o botão. O gráfico vai ampliar o intervalo de tempo. 

## <a name="apply-dimension-filters-and-splitting"></a>Aplicar filtros de dimensão e divisão

[Filtragem](metrics-charts.md#apply-filters-to-charts) e [divisão](metrics-charts.md#apply-splitting-to-a-chart) são poderosas ferramentas de diagnóstico para as métricas que têm dimensões. Estas características mostram como vários segmentos métricos ("valores de dimensão") impactam o valor global da métrica, e permitem identificar possíveis outliers.

- **A filtragem** permite-lhe escolher quais os valores de dimensão incluídos na tabela. Por exemplo, é melhor mostrar pedidos bem sucedidos ao cartografar a métrica do tempo de resposta do *servidor.* É necessário aplicar o filtro sobre o sucesso da dimensão *do pedido.* 

- **A divisão** controla se o gráfico apresenta linhas separadas para cada valor de uma dimensão ou agrega os valores numa única linha. Por exemplo, pode ver uma linha para um tempo médio de resposta em todas as instâncias do servidor ou ver linhas separadas para cada servidor. Seria necessário aplicar a divisão na dimensão do caso do *servidor* para ver linhas separadas.

Veja [exemplos dos gráficos](metric-chart-samples.md) que têm filtragem e divisão aplicadas. O artigo mostra que os passos foram usados para configurar as tabelas.

## <a name="advanced-chart-settings"></a>Definições avançadas do gráfico

Pode personalizar o estilo de gráfico, o título e modificar as definições avançadas do gráfico. Quando terminar com a personalização, coloque-o num painel de instrumentos para salvar o seu trabalho. Também pode configurar alertas de métricas. Siga a [documentação](metrics-charts.md) do produto para saber mais sobre estas e outras funcionalidades avançadas do explorador de métricas do Azure Monitor.

## <a name="next-steps"></a>Próximos passos

* [Conheça as funcionalidades avançadas do Metrics Explorer](metrics-charts.md)
* [Resolução de Problemas do Explorador de Métricas](metrics-troubleshoot.md)
* [See a list of available metrics for Azure services](metrics-supported.md) (Ver uma lista de métricas disponíveis para serviços do Azure)
* [See examples of configured charts](metric-chart-samples.md) (Ver exemplos de gráficos configurados)
