---
title: Azure Monitor vê designer para conversões de azulejos de livros
description: Detalhes para converter azulejos em livros de trabalho durante a transição de vistas no Azure Monitor.
author: austonli
ms.author: aul
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 9b8d1840c0b1cea4c03312d38ed892fde94faa4c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102043342"
---
# <a name="azure-monitor-view-designer-tile-conversions"></a>Azure Monitor visualiza conversões de azulejos de design
[O view designer](view-designer.md) é uma funcionalidade do Azure Monitor que lhe permite criar visualizações personalizadas para o ajudar a visualizar dados no seu espaço de trabalho Log Analytics, com gráficos, listas e linhas de tempo. Estão a ser eliminados e substituídos por livros que fornecem funcionalidades adicionais. Este artigo fornece detalhes para converter diferentes azulejos em livros de trabalho.

## <a name="donut--list-tile"></a>Azulejo da lista de & de donut

![Lista de Donuts](media/view-designer-conversion-tiles/donut-list.png)

Recriar o azulejo & donut nos livros envolve duas visualizações separadas. Para a porção de donut há duas opções.
Para ambos começar selecionando **Adicionar consulta** e colar a consulta original do designer de visualização para a célula.

**Opção 1:** Selecione Gráfico de **Torta** a partir do **dropdown visualização:** ![ menu de visualização de gráfico de torta](media/view-designer-conversion-tiles/pie-chart.png)

**Opção 2:** Selecione **Definir por consulta** a partir do dropdown **visualização** e adicione `| render piechart` à consulta:

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

Para criar uma lista e permitir faíscas, consulte o artigo sobre [tarefas comuns](view-designer-conversion-tasks.md).

Segue-se um exemplo de como o azulejo da lista de & donut pode ser reinterpretado em livros:

![Livros de lista de donuts](media/view-designer-conversion-tiles/donut-workbooks.png)

## <a name="line-chart--list-tile"></a>Azulejo de lista de & de linha
![Lista de gráficos de linha](media/view-designer-conversion-tiles/line-list.png) 

Para recriar a porção de gráfico de linha, a consulta é a seguinte:

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

**Opção 1:** Selecione gráfico de **linha** a partir do dropdown **visualização:**
 
 ![Menu de gráfico de linha](media/view-designer-conversion-tiles/line-visualization.png)

**Opção 2:** Selecione **Definir por consulta** a partir do dropdown **visualização** e adicione `| render linechart` à consulta:

 ![Menu de Visualização](media/view-designer-conversion-tiles/set-by-query.png)

**Exemplo**

```KQL
search * 
| make-series Count = count() default=0 on TimeGenerated from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by Type 
| render linechart_
```

Para criar uma lista e permitir faíscas, consulte o artigo sobre [tarefas comuns](view-designer-conversion-tasks.md).

Segue-se um exemplo de como o gráfico de linha & azulejo da lista pode ser reinterpretado em livros:

![Livros de lista de gráficos de linha](media/view-designer-conversion-tiles/line-workbooks.png)

## <a name="number--list-tile"></a>Azulejo da lista & número

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

Altere o dropdown de visualização para **Azulejos** e, em seguida, selecione **Definições de azulejos**.
 ![Visualização de azulejos](media/view-designer-conversion-tiles/tile-visualization.png)

Deixe a secção **título** em branco e selecione **Esquerda**. Alterar o valor da **coluna de utilização:** para **contar**, e **renderizador de colunas** para **grande número**:

![Definições de azulejos](media/view-designer-conversion-tiles/tile-settings.png)

 
Para criar uma lista e permitir faíscas, consulte o artigo sobre [tarefas comuns](view-designer-conversion-tasks.md).

Segue-se um exemplo de como o número & azulejo da lista pode ser reinterpretado em livros:

![Cadernos de Trabalho da Lista de Números](media/view-designer-conversion-tiles/number-workbooks.png)

## <a name="timeline--list"></a>Linha Cronológica e Lista

 ![Lista de linha do tempo](media/view-designer-conversion-tiles/time-list.png)

Para a atualização da linha do tempo, a sua consulta é a seguinte:

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

**Opção 1:** Selecione **gráfico** de barras a partir do **dropdown** ![ visualização: visualização de Barchart](media/view-designer-conversion-tiles/bar-visualization.png)
 
**Opção 2:** Selecione **Definir por consulta** a partir do dropdown **visualização** e adicione `| render barchart` à consulta:

 ![Menu de visualização](media/view-designer-conversion-tiles/set-by-query.png)

 
Para criar uma lista e permitir faíscas, consulte o artigo sobre [tarefas comuns](view-designer-conversion-tasks.md).

Segue-se um exemplo de como a linha do tempo & azulejo da lista pode ser reinterpretada em livros:

![Livros de listas de linha do tempo](media/view-designer-conversion-tiles/time-workbooks.png)

## <a name="next-steps"></a>Passos seguintes

- [Visão geral do designer de vista para a transição de livros de trabalho](view-designer-conversion-overview.md)
