---
title: Azure Monitor vê designer para trabalhos de conversão de tarefas comuns
description: Tarefas comuns na transição de pontos de vista para livros de trabalho no Azure Monitor.
author: austonli
ms.author: aul
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: d479e64620519491ba4847349ea33820747fcea7
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102043478"
---
# <a name="view-designer-to-workbooks-conversion-common-tasks"></a>Ver designer para trabalhos de conversão de tarefas comuns
[O view designer](view-designer.md) é uma funcionalidade do Azure Monitor que lhe permite criar visualizações personalizadas para o ajudar a visualizar dados no seu espaço de trabalho Log Analytics, com gráficos, listas e linhas de tempo. Estão a ser eliminados e substituídos por livros que fornecem funcionalidades adicionais. Este artigo detalha tarefas que são comuns na conversão de visualizações para livros de trabalho.


## <a name="quickstart-with-preset-view-designer-templates"></a>Quickstart com modelos de design de vista predefinido

Os livros de trabalho em log analytics já têm modelos feitos para combinar com algumas das vistas do designer de visualização. Na categoria **'Guias de Visualização',** selecione **o Guia de Transição do Designer** para saber mais sobre as suas opções ou selecione um dos modelos pré-conjuntos.

![Modelos de exemplo](media/view-designer-conversion-tasks/templates.png)

## <a name="enabling-time-range-filter"></a>Filtragem de intervalo de tempo ativada
O view designer tem um filtro de intervalo de tempo incorporado, no entanto, nos livros de trabalho esta definição não é ativada por padrão. Os livros de trabalho permitem que os utilizadores criem os seus próprios filtros de intervalo de tempo que podem ser mais aplicáveis aos seus registos de dados. Os passos para gerar o filtro são listados abaixo:

Selecione a opção **de parâmetros Adicionar.** O **estilo** predefinido é definido para *Pills*.

![Adicionar Param](media/view-designer-conversion-tasks/add-param.png)

 Selecione o botão **Adicionar Parâmetro.**

![Adicionar Parâmetro](media/view-designer-conversion-tasks/add-parameter.png)

A partir do menu da barra lateral, na caixa de texto **do nome parâmetro,** *escreva TimeRange*. Definir **o tipo de parâmetro** como *picker de intervalo de tempo*. Selecione a caixa de verificação **requerida?**

![Menu de parâmetros](media/view-designer-conversion-tasks/parameter-menu.png)

Guarde o parâmetro no canto superior esquerdo do menu da barra lateral. Pode deixar a entrega como *desativação* por predefinição ou selecionar um valor **timeRange** predefinido, tal como *24 horas*. Selecione **'Editar' Feito.**

Os parâmetros podem ser usados em consultas adicionando chaves encaracoladas {} em torno do nome do seu parâmetro. Mais detalhes sobre os parâmetros podem ser encontrados na documentação dos [livros de trabalho sobre parâmetros.](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Documentation/Parameters/Parameters.md)

## <a name="updating-queries-with-the-timerange-parameter"></a>Atualização de consultas com o parâmetro TimeRange

### <a name="option-1-select-timerange-from-the-time-range-dropdown"></a>Opção 1: Selecione TimeRange a partir do dropdown do intervalo de tempo

![Parâmetro de tempo](media/view-designer-conversion-tasks/time-parameter.png)

### <a name="option-2-update-your-log-queries"></a>Opção 2: Atualizar as suas consultas de registo

Na sua consulta adicione a linha: `| where TimeGenerated {TimeRange}` como no exemplo seguinte:

Consulta original
```KQL
search * 
| summarize count() by Type
```

Consulta atualizada
```KQL
search * 
| where TimeGenerated {TimeRange} 
| summarize count() by Type
```

## <a name="including-a-list"></a>Incluindo uma Lista
A maioria das vistas do designer de visualizações inclui uma lista, e você pode reproduzir esta lista padrão em um livro.

![Lista de azulejos](media/view-designer-conversion-tasks/tile-list.png)

Adicione uma visualização clicando **Em Adicionar consulta** a partir das opções celulares.

![Adicionar Param](media/view-designer-conversion-tasks/add-param.png)

O designer de visualização emprega uma consulta padrão que corresponde à sintaxe do exemplo Original. Isto pode ser atualizado alterando a consulta para o formulário atualizado como no exemplo seguinte:

Consulta original
```KQL
search * 
| summarize AggregatedValue = count() by Type
```

Consulta atualizada
```KQL
search * 
| summarize Count = count() by Type
```

Isto gerará uma lista que se parece com o seguinte:

![Exemplo da lista](media/view-designer-conversion-tasks/list-example.png)

## <a name="enabling-sparklines"></a>Permitir linhas de faísca
Uma característica comum para as grelhas é adicionar linhas de faísca para resumir vários padrões de dados ao longo do tempo. O designer de visualização oferece a funcionalidade **Enable Sparklines** para todas as listas, assim como os livros de trabalho. Para incluir faíscas nos seus dados que correspondem ao designer de visualização, junte os dados com a sua consulta original como no exemplo seguinte:

Consulta original
```KQL
search *
| summarize AggregatedValue = count() by Type) on Type
```

Consulta atualizada
```KQL
search * 
| summarize AggregatedValue = count() by Type
| join kind = inner (search * 
    | make-series Trend = count() default=0 on TimeGenerated from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by Type) on Type
| project Type, AggregatedValue, Trend
```

Selecione **Definições de colunas**.
![Definições de colunas](media/view-designer-conversion-tasks/column-settings.png)

Atualize o dropdown **do renderizador da Coluna** para ser uma área de *faísca*.
![Linhas de faísca](media/view-designer-conversion-tasks/sparkline.png)

Guarde as definições e volte a executar a consulta para atualizar a sua tabela para incluir uma linha de faísca.

A grelha resultante será semelhante a seguinte: ![ Exemplo sparkline](media/view-designer-conversion-tasks/sparkline-example.png)

## <a name="advanced-cell-settings"></a>Configurações avançadas de células
Para espelhar o designer de visualização, pode executar tarefas como alterar o tamanho das células do livro ou adicionar pinos e links externos aos registos.

Para aceder **a Definições Avançadas** selecione o ícone de engrenagem na parte inferior de cada célula.

![Definições avançadas](media/view-designer-conversion-tasks/advanced-settings.png)

Isto irá exibir um menu com várias opções:

![Definições avançadas](media/view-designer-conversion-tasks/advanced-settings-settings.png)

Para adicionar um pino e um link a uma consulta externa selecione as caixas de verificação correspondentes. Para adicionar um título à sua célula, digite o título desejado na secção **de títulos** da Tabela.

Por predefinição, qualquer célula de livros de trabalho está definida para ocupar toda a largura da página, mas pode ajustá-la escalando a célula para baixo sob o **separador Estilo** do menu **Definições Avançadas**

![Estilo de configurações avançadas](media/view-designer-conversion-tasks/advanced-settings-style.png)

 
## <a name="additional-parameters"></a>Parâmetros adicionais
Selecione **Adicionar Parâmetro** para criar um novo parâmetro no seu livro. 

Para selecionar uma Subscrição, *digite a Subscrição* no campo **de nomes de parâmetros** no menu lateral e selecione o Picker de *subscrição* a partir do dropdown **do tipo parâmetro**

![Menu de Assinatura](media/view-designer-conversion-tasks/subscription-filter.png)

Para selecionar um recurso, *digite o recurso* no campo **de nomes parâmetros** no menu lateral e selecione o *Apanhador* de Recursos a partir do dropdown **do tipo parâmetro.**

![Menu de Recursos](media/view-designer-conversion-tasks/resource-filter.png)

Isto irá inserir dropdowns para que possa aceder às suas várias subscrições e recursos.

![Dropdown de recursos de subscrição](media/view-designer-conversion-tasks/subscription-resource.png)


## <a name="next-steps"></a>Passos seguintes
- [Conversões de mosaico](view-designer-conversion-tiles.md)
