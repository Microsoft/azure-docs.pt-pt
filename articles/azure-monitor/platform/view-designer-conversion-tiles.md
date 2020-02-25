---
title: Azure Monitor vê designer para conversões de azulejos de livros
description: ''
author: austonli
ms.author: aul
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: b1cce6d1aafabda62b71ecd38ff5ac7df1033b26
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2020
ms.locfileid: "77561991"
---
# <a name="azure-monitor-view-designer-tile-conversions"></a>Azure Monitor ver conversões de azulejos designer
O designer de [visualização](view-designer.md) é uma funcionalidade do Azure Monitor que lhe permite criar vistas personalizadas para o ajudar a visualizar dados no seu espaço de trabalho Log Analytics, com gráficos, listas e cronologias. Estão a ser gradualmente eliminados e substituídos por livros de trabalho que fornecem funcionalidades adicionais. Este artigo fornece detalhes para converter diferentes azulejos em livros de trabalho.

## <a name="donut--list-tile"></a>Donut e azulejo sintetizar

![Lista de Donut](media/view-designer-conversion-tiles/donut-list.png)

A recriação do azulejo donut e lista nos livros de trabalho envolve duas visualizações separadas. Para a porção de donuts existem duas opções.
Para ambos começam por selecionar **Adicionar consulta** e colar a consulta original do designer de visualização para a célula.

**Opção 1:** Selecione **Pie Chart** do dropdown de **visualização:** menu de visualização de gráficos de ![Pie](media/view-designer-conversion-tiles/pie-chart.png)

**Opção 2:** Selecione **set por consulta** a partir do dropdown de **visualização** e adicione `| render piechart` à consulta:

 ![Menu de Visualização](media/view-designer-conversion-tiles/set-by-query.png)

**Exemplo**

Consulta original
```KQL
search * 
| summarize AggregatedValue = count() by Type 
| order by AggregatedValue desc
```

Consulta atualizada
```KQL
search * 
| summarize AggregatedValue = count() by Type 
| order by AggregatedValue desc 
| render piechart
```

Para criar uma lista e permitir faíscas, consulte o artigo sobre [tarefas comuns.](view-designer-conversion-tasks.md)

Segue-se um exemplo de como o azulejo donut & list pode ser reinterpretado em livros de trabalho:

![Livros de lista de donuts](media/view-designer-conversion-tiles/donut-workbooks.png)

## <a name="line-chart--list-tile"></a>Gráfico de linha e azulejo de lista
![Lista de gráficos de linha](media/view-designer-conversion-tiles/line-list.png) 

Para recriar a porção de gráfico de linha atualizar a consulta da seguinte forma:

Consulta original
```KQL
search * 
| summarize AggregatedValue = count() by Type
```

Consulta atualizada
```KQL
search * 
| make-series Count = count() default=0 on TimeGenerated from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by Type
```

Existem duas opções para visualizar o gráfico de linha

**Opção 1:** Selecione gráfico de **linha** a partir do dropdown de **visualização:**
 
 ![Menu de gráfico de linha](media/view-designer-conversion-tiles/line-visualization.png)

**Opção 2:** Selecione **set por consulta** a partir do dropdown de **visualização** e adicione `| render linechart` à consulta:

 ![Menu de Visualização](media/view-designer-conversion-tiles/set-by-query.png)

**Exemplo**

```KQL
search * 
| make-series Count = count() default=0 on TimeGenerated from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by Type 
| render linechart_
```

Para criar uma lista e permitir faíscas, consulte o artigo sobre [tarefas comuns.](view-designer-conversion-tasks.md)

Segue-se um exemplo de como o gráfico de linha e o azulejo da lista podem ser reinterpretados em livros de trabalho:

![Livros de lista de gráficos de linha](media/view-designer-conversion-tiles/line-workbooks.png)

## <a name="number--list-tile"></a>Número e azulejo de lista

 ![Lista de azulejos](media/view-designer-conversion-tiles/tile-list-example.png)

Para o azulejo número, atualize a consulta da seguinte forma:

Consulta original
```KQL
search * 
| summarize AggregatedValue = count() by Computer | count
```

Consulta atualizada
```KQL
search *
| summarize AggregatedValue = count() by Computer 
| summarize Count = count()
```

Mude a descida da visualização para **Azulejos** e, em seguida, selecione **Definições de azulejos**.
 ![](media/view-designer-conversion-tiles/tile-visualization.png) de visualização de azulejos

Deixe a secção **Título** em branco e selecione **Esquerda**. Alterar o valor da **coluna utilização:** para **contar,** e column **renderer** para **grande número:**

![Definições de azulejos](media/view-designer-conversion-tiles/tile-settings.png)

 
Para criar uma lista e permitir faíscas, consulte o artigo sobre [tarefas comuns.](view-designer-conversion-tasks.md)

Segue-se um exemplo de como o número e lista de azulejos pode ser reinterpretado em livros de trabalho:

![Lista de Números Livros](media/view-designer-conversion-tiles/number-workbooks.png)

## <a name="timeline--list"></a>Linha Cronológica e Lista

 ![Lista de cronologias](media/view-designer-conversion-tiles/time-list.png)

Para atualizar a linha do tempo a sua consulta da seguinte forma:

Consulta original
```KQL
search * 
| sort by TimeGenerated desc
```

Consulta atualizada
```KQL
search * 
| summarize Count = count() by Computer, bin(TimeGenerated,{TimeRange:grain})
```

Existem duas opções para visualizar a consulta como um gráfico de barras:

**Opção 1:** Selecione gráfico de **barras** a partir do dropdown de **visualização:** ![visualização de Barchart](media/view-designer-conversion-tiles/bar-visualization.png)
 
**Opção 2:** Selecione **set por consulta** a partir do dropdown de **visualização** e adicione `| render barchart` à consulta:

 ![Menu de visualização](media/view-designer-conversion-tiles/set-by-query.png)

 
Para criar uma lista e permitir faíscas, consulte o artigo sobre [tarefas comuns.](view-designer-conversion-tasks.md)

Segue-se um exemplo de como a linha do tempo e a lista de azulejos podem ser reinterpretadas em livros de trabalho:

![Lista de CronologiaS Livros](media/view-designer-conversion-tiles/time-workbooks.png)

## <a name="next-steps"></a>Passos seguintes

- [Visão geral do designer de visão para a transição de livros](view-designer-conversion-overview.md)
