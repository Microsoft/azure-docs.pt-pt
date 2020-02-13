---
title: Azure Monitor vê designer para reconversão de livros de livros tarefas comuns
description: ''
author: austonli
ms.author: aul
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 444535f0fcc96685a9562a8f9536c0a2c2998946
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77171543"
---
# <a name="view-designer-to-workbooks-conversion-common-tasks"></a>Ver designer para trabalhos de conversão tarefas comuns
O designer de [visualização](view-designer.md) é uma funcionalidade do Azure Monitor que lhe permite criar vistas personalizadas para o ajudar a visualizar dados no seu espaço de trabalho Log Analytics, com gráficos, listas e cronologias. Estão a ser gradualmente eliminados e substituídos por livros de trabalho que fornecem funcionalidades adicionais. Este artigo detalha tarefas que são comuns na conversão de pontos de vista para livros de livros.


## <a name="quickstart-with-preset-view-designer-templates"></a>Quickstart com modelos de design de vista predefinida

Os livros de trabalho em espaços de trabalho de Log Analytics já têm modelos feitos para combinar com algumas das vistas do designer de visualização. Na categoria **Ver Designer Guides,** selecione Ver Guia de **Transição de Designers** para saber mais sobre as suas opções ou selecione um dos modelos predefinidos.

![Modelos de exemplo](media/view-designer-conversion-tasks/templates.png)

## <a name="enabling-time-range-filter"></a>Filtrar o intervalo de tempo de ativação
O designer de visualização tem um filtro de intervalo de tempo incorporado, no entanto, em livros de reposição esta definição não está ativada por padrão. Os livros de trabalho permitem que os utilizadores criem os seus filtros de intervalo de tempo próprios que possam ser mais aplicáveis aos seus registos de dados. Os passos para gerar o filtro estão listados abaixo:

Selecione a opção **Adicionar parâmetros.** O **estilo** padrão está definido para *Comprimidos*.

![Adicionar Param](media/view-designer-conversion-tasks/add-param.png)

 Selecione o botão **Adicionar Parâmetro.**

![Adicionar parâmetro](media/view-designer-conversion-tasks/add-parameter.png)

Do menu da barra lateral, na caixa de **texto do nome parametrómetro,** *digite TimeRange*. Definir **o tipo** de parâmetro como *picker*de intervalo de tempo . Selecione a caixa de verificação **Necessária?**

![Menu parâmetro](media/view-designer-conversion-tasks/parameter-menu.png)

Guarde o parâmetro no canto superior esquerdo do menu da barra lateral. Pode deixar a queda como *desdefinida* por padrão ou selecionar um valor predefinido do **TimeRange,** como *24 horas*. Selecione **Edição Feita .**

Os parâmetros podem ser usados em consultas adicionando aparelhos encaracolados {} em torno do nome do parâmetro. Mais detalhes sobre os parâmetros podem ser encontrados na documentação dos [Livros sobre parâmetros.](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Documentation/Parameters/Parameters.md)

## <a name="updating-queries-with-the-timerange-parameter"></a>Atualizar consultas com o parâmetro TimeRange

### <a name="option-1-select-timerange-from-the-time-range-dropdown"></a>Opção 1: Selecione TimeRange a partir do intervalo de tempo dropdown

![Parâmetro do tempo](media/view-designer-conversion-tasks/time-parameter.png)

### <a name="option-2-update-your-log-queries"></a>Opção 2: Atualizar as suas consultas de registo

Na sua consulta adicione a linha: `| where TimeGenerated {TimeRange}` como no seguinte exemplo:

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

## <a name="including-a-list"></a>Incluindo uma lista
A maioria das vistas do designer de visualização inclui uma lista, e você pode reproduzir esta lista padrão em um livro.

![Lista de azulejos](media/view-designer-conversion-tasks/tile-list.png)

Adicione uma visualização clicando **Em adicionar consulta** às opções celulares.

![Adicionar Param](media/view-designer-conversion-tasks/add-param.png)

Ver designer emprega uma consulta padrão que corresponde à sintaxe do exemplo original. Isto pode ser atualizado alterando a consulta para o formulário atualizado como no seguinte exemplo:

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

Isto gerará uma lista que se parece com a seguinte:

![Exemplo de lista](media/view-designer-conversion-tasks/list-example.png)

## <a name="enabling-sparklines"></a>Permitindo faíscas
Uma característica comum para grelhas é adicionar linhas de brilho para resumir vários padrões de dados ao longo do tempo. O designer de visualização oferece a funcionalidade **Enable Sparklines** para todas as listas, assim como os livros de trabalho. Para incluir linhas de brilho nos seus dados que combinam com o designer de visualização, junte os dados com a sua consulta original como no seguinte exemplo:

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

Selecione **Definições de Colunas**.
](media/view-designer-conversion-tasks/column-settings.png) de definições da coluna de ![

Atualize a queda do **renderizador** da Coluna para ser uma *área spark*.
![Sparklines](media/view-designer-conversion-tasks/sparkline.png)

Guarde as definições e volte a executar a consulta para atualizar a sua tabela para incluir uma linha de ignição.

A grelha resultante será semelhante à seguinte: ![exemplo Sparkline](media/view-designer-conversion-tasks/sparkline-example.png)

## <a name="advanced-cell-settings"></a>Definições celulares avançadas
Para espelhar o designer de visualização, pode executar tarefas como alterar o tamanho das células do livro ou adicionar pinos e ligações externas a registos.

Para aceder a **Definições Avançadas,** selecione o ícone de engrenagem na parte inferior de cada célula.

![Definições avançadas](media/view-designer-conversion-tasks/advanced-settings.png)

Isto apresentará um menu com várias opções:

![Definições avançadas](media/view-designer-conversion-tasks/advanced-settings-settings.png)

Para adicionar um pino e um link a uma consulta externa, selecione as caixas de verificação correspondentes. Para adicionar um título à sua célula, escreva o título desejado na secção **do título da Tabela.**

Por padrão, qualquer célula de livros de trabalho está definida para ocupar toda a largura da página, mas pode ajustá-lo escalando a célula sob o separador **Style** do menu **Definições Avançadas**

![Estilo de configurações avançadas](media/view-designer-conversion-tasks/advanced-settings-style.png)

 
## <a name="additional-parameters"></a>Parâmetros adicionais
**Selecione Adicionar parâmetro** para criar um novo parâmetro no seu livro. 

Para selecionar uma Subscrição, digite *a Subscrição* no campo de **nome parametrómetro** no menu lateral e selecione *Subscription Picker* do **tipo Parameter** dropdown

![Menu de subscrição](media/view-designer-conversion-tasks/subscription-filter.png)

Para selecionar um Recurso, digite *o Recurso* no campo de **nome parametrómetro** no menu lateral e selecione *Resource Picker* do **tipo Parameter** dropdown.

![Menu de Recursos](media/view-designer-conversion-tasks/resource-filter.png)

Isto irá inserir dropdowns para permitir o acesso às suas várias subscrições e recursos.

![Dropdown de recursos de subscrição](media/view-designer-conversion-tasks/subscription-resource.png)


## <a name="next-steps"></a>Passos seguintes
- [Conversões de azulejos](view-designer-conversion-tiles.md)
