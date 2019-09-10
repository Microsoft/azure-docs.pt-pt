---
title: Introdução ao Gerenciador de métricas do Azure
description: Saiba como criar seu primeiro gráfico de métrica com o Azure Metrics Explorer.
author: vgorbenko
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: ceaefffaf04a3ab266cde300e8c4b93a5e804796
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70861090"
---
# <a name="getting-started-with-azure-metrics-explorer"></a>Introdução ao Metrics Explorer do Azure

## <a name="where-do-i-start"></a>Onde começo
Azure Monitor métricas Explorer é um componente do portal do Microsoft Azure que permite plotar gráficos, correlacionar visualmente tendências e investigar picos e quedas nos valores de métricas. Use o Metrics Explorer para investigar a integridade e a utilização de seus recursos. Iniciar na seguinte ordem:

1. [Escolha um recurso e uma métrica](#create-your-first-metric-chart) e você verá um gráfico básico. Em seguida, [Selecione um intervalo de tempo](#select-a-time-range) que seja relevante para sua investigação.

1. Tente [aplicar filtros de dimensão e divisão](#apply-dimension-filters-and-splitting). Os filtros e a divisão permitem que você analise quais segmentos da métrica contribuem para o valor de métrica geral e identificam possíveis exceções.

1. Use [as configurações avançadas](#advanced-chart-settings) para personalizar o gráfico antes de fixar os painéis. [Configure alertas](alerts-metric-overview.md) para receber notificações quando o valor da métrica exceder ou cair abaixo de um limite.

## <a name="create-your-first-metric-chart"></a>Criar seu primeiro gráfico de métrica

Para criar um gráfico de métricas, em seu recurso, grupo de recursos, assinatura ou Azure Monitor exibição, abra a guia **métricas** e siga estas etapas:

1. Usando o seletor de recursos, selecione o recurso para o qual você deseja ver as métricas. (O recurso será pré-selecionado se você tiver aberto as **métricas** no contexto de um recurso específico).

    > ![Selecione um recurso](./media/metrics-getting-started/resource-picker.png)

2. Para alguns recursos, você deve escolher um namespace. O namespace é apenas uma maneira de organizar métricas para que você possa encontrá-las facilmente. Por exemplo, contas de armazenamento têm namespaces separados para armazenar arquivos, tabelas, BLOBs e métricas de filas. Muitos tipos de recursos têm apenas um namespace.

3. Selecione uma métrica em uma lista de métricas disponíveis.

    > ![Selecionar uma métrica](./media/metrics-getting-started/metric-picker.png)

4. Opcionalmente, você pode alterar a agregação de métrica. Por exemplo, talvez você queira que o gráfico mostre os valores mínimo, máximo ou médio da métrica.

> [!NOTE]
> Use o botão **Adicionar métrica** e repita essas etapas se desejar ver várias métricas plotadas no mesmo gráfico. Para vários gráficos em uma exibição, selecione o botão **Adicionar gráfico** na parte superior.

## <a name="select-a-time-range"></a>Selecionar um intervalo de tempo

Por padrão, o gráfico mostra as 24 horas mais recentes de dados de métricas. Use o painel **seletor de tempo** para alterar o intervalo de tempo, ampliar ou reduzir seu gráfico. 

![Alterar painel de intervalo de tempo](./media/metrics-getting-started/time-picker.png)

> [!NOTE]
> Use o **pincel de tempo** para investigar uma área interessante do gráfico (pico ou DIP). Coloque o ponteiro do mouse no início da área, clique e mantenha o botão esquerdo do mouse, arraste para o outro lado da área e, em seguida, solte o botão. O gráfico será ampliado nesse intervalo de tempo. 

## <a name="apply-dimension-filters-and-splitting"></a>Aplicar filtros de dimensão e divisão

[Filtragem](metrics-charts.md#apply-filters-to-charts) e [divisão](metrics-charts.md#apply-splitting-to-a-chart) são ferramentas de diagnóstico poderosas para as métricas que têm dimensões. Esses recursos mostram como vários segmentos de métrica ("valores de dimensão") impactam o valor geral da métrica e permitem que você identifique possíveis exceções.

- A **filtragem** permite que você escolha quais valores de dimensão serão incluídos no gráfico. Por exemplo, talvez você queira mostrar solicitações bem-sucedidas ao representar o gráfico da métrica de *tempo de resposta do servidor* . Você precisaria aplicar o filtro no *sucesso da dimensão de solicitação* . 

- **Dividir** controles se o gráfico exibe linhas separadas para cada valor de uma dimensão ou agrega os valores em uma única linha. Por exemplo, você pode ver uma linha para um tempo médio de resposta em todas as instâncias de servidor ou ver linhas separadas para cada servidor. Você precisaria aplicar a divisão na dimensão de *instância do servidor* para ver linhas separadas.

Veja [exemplos dos gráficos](metric-chart-samples.md) que têm a filtragem e a divisão aplicada. O artigo mostra as etapas usadas para configurar os gráficos.

## <a name="advanced-chart-settings"></a>Configurações avançadas do gráfico

Você pode personalizar o estilo do gráfico, o título e modificar as configurações avançadas do gráfico. Quando terminar com a personalização, fixe-a em um painel para salvar seu trabalho. Você também pode configurar alertas de métricas. Siga a [documentação do produto](metrics-charts.md) para saber mais sobre esses e outros recursos avançados do Azure monitor métricas Explorer.

## <a name="next-steps"></a>Passos seguintes

* [Saiba mais sobre os recursos avançados do Metrics Explorer](metrics-charts.md)
* [Solução de problemas Metrics Explorer](metrics-troubleshoot.md)
* [See a list of available metrics for Azure services](metrics-supported.md) (Ver uma lista de métricas disponíveis para serviços do Azure)
* [See examples of configured charts](metric-chart-samples.md) (Ver exemplos de gráficos configurados)
