---
title: Tutorial - Criar um gráfico de métricas no Monitor Azure
description: Aprenda a criar o seu primeiro gráfico métrico com o explorador de métricas Azure.
author: bwren
ms.author: bwren
ms.subservice: metrics
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: d8f72471dd3109bf584d18f032ec2f4d949a4993
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "79082817"
---
# <a name="tutorial-create-a-metrics-chart-in-azure-monitor"></a>Tutorial: Criar um gráfico de métricas no Monitor Azure
O explorador de métricas é uma característica do Azure Monitor no portal Azure que permite criar gráficos a partir de valores métricos, correlacionar visualmente tendências e investigar picos e mergulhos nos valores métricos. Utilize o explorador de métricas para investigar a saúde e utilização dos seus recursos Azure ou para traçar gráficos a partir de métricas personalizadas. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Selecione uma métrica para a qual pretende traçar um gráfico
> * Realizar diferentes agregados de valores métricos
> * Modificar o intervalo de tempo e a granularidade para o gráfico

Segue-se um vídeo que mostra um cenário mais extenso do que o procedimento descrito neste artigo. Se você é novo em métricas, sugerimos que leia este artigo primeiro e depois veja o vídeo para ver mais detalhes. 

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4qO59]

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial é necessário um recurso Azure para monitorizar. Pode utilizar qualquer recurso na sua subscrição Azure que suporte métricas. Para determinar se um recurso suporta métricas, vá ao seu menu no portal Azure e verifique se há uma opção **Métricas** na secção **de Monitorização** do menu.


## <a name="log-in-to-azure"></a>Iniciar sessão no Azure
Faça login no portal [https://portal.azure.com](https://portal.azure.com)Azure em .

## <a name="open-metrics-explorer-and-select-a-scope"></a>Abrir o explorador de métricas e selecionar um âmbito
Pode abrir métricas exploradora, quer a partir do menu Do Monitor Azure, quer a partir do menu de um recurso no portal Azure. As métricas de todos os recursos estão disponíveis independentemente da opção que utilizar. 

1. Selecione **Métricas** do menu **Do Monitor Azure** ou da secção **de Monitorização** do menu de um recurso.

1. Selecione o **Scope**, que é o recurso para o qual pretende ver métricas. O âmbito já está povoado se abrir métricas explorador a partir do menu de um recurso.

    ![Selecione um âmbito](media/tutorial-metrics-explorer/scope-picker.png)

2. Selecione um **Espaço nome** se o âmbito tiver mais de um. O espaço de nome é apenas uma forma de organizar métricas para que possa encontrá-las facilmente. Por exemplo, as contas de armazenamento têm espaços de nome separados para armazenar métricas de Ficheiros, Tabelas, Blobs e Filas. Muitos tipos de recursos têm apenas um espaço de nome.

3. Selecione uma métrica de uma lista de métricas disponíveis para o âmbito e espaço de nome selecionados.

    ![Selecione uma métrica](media/tutorial-metrics-explorer/metric-picker.png)

4. Opcionalmente, altere a **agregação**métrica . Isto define como os valores métricos serão agregados ao longo do tempo granularidade para o gráfico. Por exemplo, se a granularidade do tempo for fixada para 15 minutos e a agregação estiver definida para a soma, então cada ponto do gráfico será a soma de todos os valores recolhidos em cada segmento de 15 minutos.

    ![Gráfico](media/tutorial-metrics-explorer/chart.png)

5. Utilize o botão **Métrica Adicionar** e repita estes passos se quiser ver várias métricas traçadas no mesmo gráfico. Para várias tabelas numa vista, selecione o botão **New chart.**

## <a name="select-a-time-range-and-granularity"></a>Selecione uma gama de tempo e granularidade

Por padrão, o gráfico mostra as 24 horas mais recentes de dados métricos. Utilize o marcador de tempo para alterar a gama de **tempo** para o gráfico ou a **granularidade** do tempo que define o intervalo de tempo para cada ponto de dados. O gráfico utiliza a agregação especificada para calcular todos os valores amostrados ao longo do tempo especificado.

![Alterar painel de intervalo de tempo](media/tutorial-metrics-explorer/time-picker.png)


Use a escova de **tempo** para investigar uma área interessante da tabela, como um espigão ou um mergulho. Coloque o ponteiro do rato no início da área, clique e segure o botão do rato esquerdo, arraste para o outro lado da área e liberte o botão. O gráfico vai ampliar o intervalo de tempo. 

![Escova do tempo](media/tutorial-metrics-explorer/time-brush.png)

## <a name="apply-dimension-filters-and-splitting"></a>Aplicar filtros de dimensão e dividir
Consulte as seguintes referências para funcionalidades avançadas que lhe permitem realizar análises adicionais nas suas métricas e identificar potenciais outliers nos seus dados.

- [A filtragem](../platform/metrics-charts.md#apply-filters-to-charts) permite-lhe escolher quais os valores de dimensão incluídos na tabela. Por exemplo, é melhor mostrar apenas pedidos bem sucedidos ao traçar uma métrica de tempo de resposta do *servidor.* 

- [A divisão](../platform/metrics-charts.md#apply-splitting-to-a-chart) controla se o gráfico apresenta linhas separadas para cada valor de uma dimensão, ou agrega os valores numa única linha. Por exemplo, pode querer ver uma linha para um tempo médio de resposta em todas as instâncias do servidor ou pode querer linhas separadas para cada servidor. 

Veja [exemplos dos gráficos](../platform/metric-chart-samples.md) que têm filtragem e divisão aplicadas.

## <a name="advanced-chart-settings"></a>Definições avançadas de gráficos

Pode personalizar o estilo, o título e modificar as definições avançadas de gráficos. Quando terminar com a personalização, coloque-o num painel para salvar o seu trabalho. Também pode configurar alertas de métricas. Consulte [as características avançadas do Azure Metrics Explorer](../platform/metrics-charts.md#lock-boundaries-of-chart-y-axis) para conhecer estas e outras funcionalidades avançadas do explorador de métricas Do Monitor Azure.


## <a name="next-steps"></a>Passos seguintes
Agora que aprendeu a trabalhar com métricas no Monitor Azure, aprenda a usar métricas para enviar alertas pró-ativos.

> [!div class="nextstepaction"]
> [Criar, ver e gerir alertas de métricas com o Azure Monitor](../platform/alerts-metric.md)

