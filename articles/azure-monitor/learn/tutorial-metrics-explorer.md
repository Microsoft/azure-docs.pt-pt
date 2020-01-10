---
title: Tutorial – criar um gráfico de métricas no Azure Monitor
description: Saiba como criar seu primeiro gráfico de métrica com o Azure Metrics Explorer.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.subservice: metrics
ms.topic: tutorial
ms.date: 12/16/2019
ms.author: bwren
ms.openlocfilehash: 7d9360840912614b6ae89d958d90de962b36506d
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/07/2020
ms.locfileid: "75689963"
---
# <a name="tutorial-create-a-metrics-chart-in-azure-monitor"></a>Tutorial: criar um gráfico de métricas no Azure Monitor
O Metrics Explorer é um recurso de Azure Monitor no portal do Azure que permite que você crie gráficos com base em valores de métrica, correlacione tendências visualmente e investigue picos e quedas em valores de métricas. Use o Metrics Explorer para investigar a integridade e a utilização de seus recursos do Azure ou para plotar gráficos de métricas personalizadas. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Selecione uma métrica para a qual você deseja plotar um gráfico
> * Executar agregações diferentes de valores de métrica
> * Modificar o intervalo de tempo e a granularidade do gráfico

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisa de um recurso do Azure para monitorar. Você pode usar qualquer recurso em sua assinatura do Azure que dê suporte a métricas. Para determinar se um recurso dá suporte a métricas, acesse seu menu na portal do Azure e verifique se há uma opção de **métrica** na seção **monitoramento** do menu.


## <a name="log-in-to-azure"></a>Iniciar sessão no Azure
Inicie sessão no portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="open-metrics-explorer-and-select-a-scope"></a>Abrir o Metrics Explorer e selecionar um escopo
Você pode abrir o Metrics Explorer no menu Azure Monitor ou no menu de um recurso no portal do Azure. As métricas de todos os recursos estão disponíveis independentemente da opção que você usar. 

1. Selecione **métricas** no menu **Azure monitor** ou na seção **monitoramento** do menu de um recurso.

1. Selecione o **escopo**, que é o recurso para o qual você deseja ver as métricas. O escopo já estará preenchido se você tiver aberto o Metrics Explorer no menu de um recurso.

    ![Selecionar um escopo](media/tutorial-metrics-explorer/scope-picker.png)

2. Selecione um **namespace** se o escopo tiver mais de um. O namespace é apenas uma maneira de organizar métricas para que você possa encontrá-las facilmente. Por exemplo, contas de armazenamento têm namespaces separados para armazenar arquivos, tabelas, BLOBs e métricas de filas. Muitos tipos de recursos têm apenas um namespace.

3. Selecione uma métrica em uma lista de métricas disponíveis para o escopo e o namespace selecionados.

    ![Selecionar uma métrica](media/tutorial-metrics-explorer/metric-picker.png)

4. Opcionalmente, altere a **agregação**de métrica. Isso define como os valores de métrica serão agregados na granularidade de tempo para o grafo. Por exemplo, se a granularidade de tempo for definida como 15 minutos e a agregação for definida como Sum, cada ponto no grafo será a soma de todos os valores coletados em cada segmento de 15 minutos.

    ![Gráfico](media/tutorial-metrics-explorer/chart.png)

5. Use o botão **Adicionar métrica** e repita essas etapas se desejar ver várias métricas plotadas no mesmo gráfico. Para vários gráficos em uma exibição, selecione o botão **novo gráfico** .

## <a name="select-a-time-range-and-granularity"></a>Selecione um intervalo de tempo e granularidade

Por padrão, o gráfico mostra as 24 horas mais recentes de dados de métricas. Use o seletor de tempo para alterar o **intervalo de tempo** para o gráfico ou a **granularidade de tempo** que define o intervalo de tempo para cada ponto de dados. O gráfico usa a agregação especificada para calcular todos os valores de amostra ao longo da granularidade de tempo especificada.

![Alterar painel de intervalo de tempo](media/tutorial-metrics-explorer/time-picker.png)


Use o **pincel de tempo** para investigar uma área interessante do gráfico, como um pico ou um DIP. Coloque o ponteiro do mouse no início da área, clique e mantenha o botão esquerdo do mouse, arraste para o outro lado da área e solte o botão. O gráfico será ampliado nesse intervalo de tempo. 

![Pincel de tempo](media/tutorial-metrics-explorer/time-brush.png)

## <a name="apply-dimension-filters-and-splitting"></a>Aplicar filtros de dimensão e divisão
Consulte as referências a seguir para recursos avançados que permitem executar análises adicionais em suas métricas e identificar possíveis exceções em seus dados.

- A [filtragem](../platform/metrics-charts.md#apply-filters-to-charts) permite que você escolha quais valores de dimensão serão incluídos no gráfico. Por exemplo, talvez você queira mostrar apenas solicitações bem-sucedidas ao representar graficamente uma métrica de *tempo de resposta do servidor* . 

- [Dividir](../platform/metrics-charts.md#apply-splitting-to-a-chart) controles se o gráfico exibe linhas separadas para cada valor de uma dimensão ou agrega os valores em uma única linha. Por exemplo, talvez você queira ver uma linha para um tempo médio de resposta em todas as instâncias de servidor ou pode desejar linhas separadas para cada servidor. 

Veja [exemplos dos gráficos](../platform/metric-chart-samples.md) que têm a filtragem e a divisão aplicada.

## <a name="advanced-chart-settings"></a>Configurações avançadas do gráfico

Você pode personalizar o estilo do gráfico, o título e modificar as configurações avançadas do gráfico. Quando terminar com a personalização, fixe-a em um painel para salvar seu trabalho. Você também pode configurar alertas de métricas. Consulte [recursos avançados do Azure Metrics Explorer](../platform/metrics-charts.md#lock-boundaries-of-chart-y-axis) para saber mais sobre esses e outros recursos avançados do Azure monitor Metrics Explorer.


## <a name="next-steps"></a>Passos seguintes
Agora que você aprendeu como trabalhar com métricas em Azure Monitor, saiba como usar métricas para enviar alertas proativos.

> [!div class="nextstepaction"]
> [Criar, ver e gerir alertas de métricas com o Azure Monitor](../platform/alerts-metric.md)

