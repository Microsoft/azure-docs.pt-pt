---
title: Começar com o explorador de métricas Azure
description: Aprenda a criar o seu primeiro gráfico métrico com o explorador de métricas Azure.
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 920ff19b162843d1c2d631ad192ce1b3c4273a68
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79248778"
---
# <a name="getting-started-with-azure-metrics-explorer"></a>Getting started with Azure Metrics Explorer (Introdução ao Explorador de Métricas do Azure)

## <a name="where-do-i-start"></a>Por onde devo começar?
O explorador de métricas Azure Monitor é um componente do portal Microsoft Azure que permite traçar gráficos, correlacionar visualmente tendências e investigar picos e mergulhos nos valores das métricas. Use o explorador de métricas para investigar a saúde e utilização dos seus recursos. Comece pela seguinte ordem:

1. [Escolha um recurso e uma métrica](#create-your-first-metric-chart) e verá um gráfico básico. Em seguida, [selecione um intervalo](#select-a-time-range) de tempo relevante para a sua investigação.

1. Tente [aplicar filtros de dimensão e divisão.](#apply-dimension-filters-and-splitting) Os filtros e a divisão permitem analisar quais os segmentos da métrica que contribuem para o valor métrico global e identificar possíveis outliers.

1. Utilize [configurações avançadas](#advanced-chart-settings) para personalizar o gráfico antes de fixar nos painéis. [Configure alertas](alerts-metric-overview.md) para receber notificações quando o valor métrico exceder ou descer abaixo de um limiar.

## <a name="create-your-first-metric-chart"></a>Crie o seu primeiro gráfico métrico

Para criar um gráfico métrico, a partir do seu recurso, grupo de recursos, subscrição ou visão do Monitor Azure, abra o separador Métricas e siga estes **passos:**

1. Utilizando o apanhador de recursos, selecione o recurso para o qual pretende ver métricas. (O recurso é pré-selecionado se abrir **Métricas** no contexto de um recurso específico).

    > ![Selecionar um recurso](./media/metrics-getting-started/resource-picker.png)

2. Para alguns recursos, deve escolher um espaço para o nome. O espaço de nome é apenas uma forma de organizar métricas para que possa encontrá-las facilmente. Por exemplo, as contas de armazenamento têm espaços de nome separados para armazenar métricas de Ficheiros, Tabelas, Blobs e Filas. Muitos tipos de recursos têm apenas um espaço de nome.

3. Selecione uma métrica de uma lista de métricas disponíveis.

    > ![Selecione uma métrica](./media/metrics-getting-started/metric-picker.png)

4. Opcionalmente, pode alterar a agregação métrica. Por exemplo, pode querer que o seu gráfico mostre valores mínimos, máximos ou médios da métrica.

> [!NOTE]
> Utilize o botão **Métrica Adicionar** e repita estes passos se quiser ver várias métricas traçadas no mesmo gráfico. Para várias tabelas numa vista, selecione o botão **'Adicionar'** por cima.

## <a name="select-a-time-range"></a>Selecione uma faixa de tempo

Por padrão, o gráfico mostra as 24 horas mais recentes de dados métricos. Utilize o painel de recolha de **tempo** para alterar o intervalo de tempo, fazer zoom ou ampliar a sua ficha. 

![Alterar painel de intervalo de tempo](./media/metrics-getting-started/time-picker.png)

> [!NOTE]
> Use a escova de **tempo** para investigar uma área interessante do gráfico (espigão ou um mergulho). Coloque o ponteiro do rato no início da área, clique e segure o botão do rato esquerdo, arraste para o outro lado da área e, em seguida, liberte o botão. O gráfico vai ampliar o intervalo de tempo. 

## <a name="apply-dimension-filters-and-splitting"></a>Aplicar filtros de dimensão e dividir

[Filtrar](metrics-charts.md#apply-filters-to-charts) e [dividir](metrics-charts.md#apply-splitting-to-a-chart) são poderosas ferramentas de diagnóstico para as métricas que têm dimensões. Estas características mostram como vários segmentos métricos ("valores de dimensão") impactam o valor global da métrica, e permitem identificar possíveis outliers.

- **A filtragem** permite-lhe escolher quais os valores de dimensão incluídos na tabela. Por exemplo, é melhor mostrar pedidos bem sucedidos ao traçar a métrica do tempo de resposta do *servidor.* Teria de aplicar o filtro sobre o sucesso da dimensão *do pedido.* 

- **A divisão** controla se o gráfico apresenta linhas separadas para cada valor de uma dimensão, ou agrega os valores numa única linha. Por exemplo, pode ver uma linha para um tempo médio de resposta em todas as instâncias do servidor, ou ver linhas separadas para cada servidor. Teria de aplicar a divisão na dimensão da instância do *servidor* para ver linhas separadas.

Veja [exemplos dos gráficos](metric-chart-samples.md) que têm filtragem e divisão aplicadas. O artigo mostra que os passos foram usados para configurar os gráficos.

## <a name="advanced-chart-settings"></a>Definições avançadas de gráficos

Pode personalizar o estilo, o título e modificar as definições avançadas de gráficos. Quando terminar com a personalização, coloque-o num painel para salvar o seu trabalho. Também pode configurar alertas de métricas. Siga a [documentação](metrics-charts.md) do produto para saber sobre estas e outras funcionalidades avançadas do explorador de métricas Do Monitor Azure.

## <a name="next-steps"></a>Passos seguintes

* [Saiba mais sobre as características avançadas do Metrics Explorer](metrics-charts.md)
* [Resolução de Problemas do Explorador de Métricas](metrics-troubleshoot.md)
* [See a list of available metrics for Azure services](metrics-supported.md) (Ver uma lista de métricas disponíveis para serviços do Azure)
* [See examples of configured charts](metric-chart-samples.md) (Ver exemplos de gráficos configurados)
