---
title: Um guia de referência para as partes do designer de exibição no Azure Monitor | Microsoft Docs
description: Usando o designer de exibição no Azure Monitor, você pode criar exibições personalizadas que são exibidas na portal do Azure e conter uma variedade de visualizações nos dados no espaço de trabalho Log Analytics. Este artigo é um guia de referência para as configurações para as partes de visualização que estão disponíveis em suas exibições personalizadas.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2018
ms.openlocfilehash: 853f0153283f31c9242b884babf5778f96cce141
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932000"
---
# <a name="reference-guide-to-view-designer-visualization-parts-in-azure-monitor"></a>Guia de referência para exibir partes de visualização do designer no Azure Monitor
Usando o designer de exibição no Azure Monitor, você pode criar uma variedade de exibições personalizadas na portal do Azure que podem ajudá-lo a Visualizar dados em seu espaço de trabalho do Log Analytics. Este artigo é um guia de referência para as configurações para as partes de visualização que estão disponíveis em suas exibições personalizadas.

Para obter mais informações sobre o designer de exibição, consulte:

* [Designer de exibição](view-designer.md): fornece uma visão geral do designer de exibição e dos procedimentos para criar e editar exibições personalizadas.
* [Referência de bloco](view-designer-tiles.md): fornece uma referência às configurações para cada bloco disponível em suas exibições personalizadas.


Os tipos de bloco do designer de exibição disponíveis são descritos na tabela a seguir:

| Tipo de exibição | Descrição |
|:--- |:--- |
| [Lista de consultas](#list-of-queries-part) |Exibe uma lista de consultas de log. Você pode selecionar cada consulta para exibir seus resultados. |
| [Número e lista](#number-and-list-part) |O cabeçalho exibe um único número que mostra uma contagem de registros de uma consulta de log. A lista exibe os dez primeiros resultados de uma consulta, com um grafo que indica o valor relativo de uma coluna numérica ou sua alteração ao longo do tempo. |
| [Dois números e lista](#two-numbers-and-list-part) |O cabeçalho exibe dois números que mostram contagens de registros de consultas de log separadas. A lista exibe os dez primeiros resultados de uma consulta, com um grafo que indica o valor relativo de uma coluna numérica ou sua alteração ao longo do tempo. |
| [Rosca e lista](#donut-and-list-part) |O cabeçalho exibe um único número que resume uma coluna de valor em uma consulta de log. A rosca exibe graficamente os resultados dos três principais registros. |
| [Duas linhas do tempo e lista](#two-timelines-and-list-part) |O cabeçalho exibe os resultados de duas consultas de log ao longo do tempo como gráficos de colunas, com um texto explicativo que exibe um único número que resume uma coluna de valor em uma consulta de log. A lista exibe os dez primeiros resultados de uma consulta, com um grafo que indica o valor relativo de uma coluna numérica ou sua alteração ao longo do tempo. |
| [Divulgação](#information-part) |O cabeçalho exibe texto estático e um link opcional. A lista exibe um ou mais itens com um título e texto estáticos. |
| [Gráfico de linhas, balão e lista](#line-chart-callout-and-list-part) |O cabeçalho exibe um gráfico de linhas, com várias séries de uma consulta de log ao longo do tempo e um texto explicativo com um valor resumido. A lista exibe os dez primeiros resultados de uma consulta, com um grafo que indica o valor relativo de uma coluna numérica ou sua alteração ao longo do tempo. |
| [Gráfico de linhas e lista](#line-chart-and-list-part) |O cabeçalho exibe um gráfico de linhas, com várias séries de uma consulta de log ao longo do tempo. A lista exibe os dez primeiros resultados de uma consulta, com um grafo que indica o valor relativo de uma coluna numérica ou sua alteração ao longo do tempo. |
| [Pilha de parte de gráficos de linhas](#stack-of-line-charts-part) |Exibe três gráficos de linhas separados, com várias séries de uma consulta de log ao longo do tempo. |

As seções a seguir descrevem os tipos de bloco e suas propriedades em detalhes.

> [!NOTE]
> As partes em exibições são baseadas em [consultas de log](../log-query/log-query-overview.md) em seu espaço de trabalho log Analytics. Atualmente, eles não dão suporte a [consultas entre recursos](../log-query/cross-workspace-query.md) para recuperar dados de Application insights.

## <a name="list-of-queries-part"></a>Lista de partes de consultas
A parte da lista de consultas exibe uma lista de consultas de log. Você pode selecionar cada consulta para exibir seus resultados. A exibição inclui uma única consulta por padrão, e você pode selecionar **+ consulta** para adicionar consultas adicionais.

![Exibição da lista de consultas](media/view-designer-parts/view-list-queries.png)

| Definição | Descrição |
|:--- |:--- |
| **Geral** | |
| Título |O texto que é exibido na parte superior da exibição. |
| Novo grupo |Selecione este link para criar um novo grupo na exibição, começando no modo de exibição atual. |
| Filtros previamente selecionados |Uma lista delimitada por vírgulas de propriedades a serem incluídas no painel de filtro esquerdo quando você seleciona uma consulta. |
| Modo de renderização |A exibição inicial que é exibida quando a consulta é selecionada. Você pode selecionar qualquer exibição disponível depois de abrir a consulta. |
| **Consultas** | |
| Consulta de pesquisa |A consulta a ser executada. |
| Nome amigável | O nome descritivo que é exibido. |

## <a name="number-and-list-part"></a>Parte de número e lista
O cabeçalho exibe um único número que mostra uma contagem de registros de uma consulta de log. A lista exibe os dez primeiros resultados de uma consulta, com um grafo que indica o valor relativo de uma coluna numérica ou sua alteração ao longo do tempo.

![Exibição da lista de consultas](media/view-designer-parts/view-number-list.png)

| Definição | Descrição |
|:--- |:--- |
| **Geral** | |
| Título do grupo |O texto que é exibido na parte superior da exibição. |
| Novo grupo |Selecione este link para criar um novo grupo na exibição, começando no modo de exibição atual. |
| Cone |O arquivo de imagem que é exibido ao lado do resultado no cabeçalho. |
| Usar ícone |Selecione este link para exibir o ícone. |
| **Title** (Título) | |
| Legenda |O texto que é exibido na parte superior do cabeçalho. |
| Consulta |A consulta a ser executada para o cabeçalho. A contagem dos registros retornados pela consulta é exibida. |
| Navegação por clique | Ação executada quando você clica no cabeçalho.  Para obter mais informações, consulte [configurações comuns](#click-through-navigation). |
| **Lista** | |
| Consulta |A consulta a ser executada para a lista. As duas primeiras propriedades para os dez primeiros registros nos resultados são exibidas. A primeira propriedade é um valor de texto e a segunda propriedade é um valor numérico. As barras são criadas automaticamente com base no valor relativo da coluna numérica.<br><br>Use o comando `Sort` na consulta para classificar os registros na lista. Para executar a consulta e retornar todos os registros, você pode selecionar **ver tudo**. |
| Ocultar grafo |Selecione este link para desabilitar o grafo à direita da coluna numérica. |
| Habilitar minigráficos |Selecione este link para exibir um minigráfico em vez de uma barra horizontal. Para obter mais informações, consulte [configurações comuns](#sparklines). |
| Cor |A cor das barras ou dos minigráficos. |
| Separador de nome e valor |O delimitador de caractere único a ser usado para analisar a propriedade Text em vários valores. Para obter mais informações, consulte [configurações comuns](#sparklines). |
| Navegação por clique | Ação executada quando você clica em um item na lista.  Para obter mais informações, consulte [configurações comuns](#click-through-navigation). |
| **Lista** |**> Títulos de coluna** |
| Nome |O texto que é exibido na parte superior da primeira coluna. |
| Valor |O texto que é exibido na parte superior da segunda coluna. |
| **Lista** |**Limites de >** |
| Habilitar limites |Selecione este link para habilitar os limites. Para obter mais informações, consulte [configurações comuns](#thresholds). |

## <a name="two-numbers-and-list-part"></a>Dois números e parte da lista
O cabeçalho tem dois números que exibem uma contagem de registros de consultas de log separadas. A lista exibe os dez primeiros resultados de uma consulta, com um grafo que indica o valor relativo de uma coluna numérica ou sua alteração ao longo do tempo.

![Dois números & exibição de lista](media/view-designer-parts/view-two-numbers-list.png)

| Definição | Descrição |
|:--- |:--- |
| **Geral** | |
| Título do grupo |O texto que é exibido na parte superior da exibição. |
| Novo grupo |Selecione este link para criar um novo grupo na exibição, começando no modo de exibição atual. |
| Cone |O arquivo de imagem que é exibido ao lado do resultado no cabeçalho. |
| Usar ícone |Selecione este link para exibir o ícone. |
| **Navegação de título** | |
| Navegação por clique | Ação executada quando você clica no cabeçalho.  Para obter mais informações, consulte [configurações comuns](#click-through-navigation). |
| **Title** (Título) | |
| Legenda |O texto que é exibido na parte superior do cabeçalho. |
| Consulta |A consulta a ser executada para o cabeçalho. A contagem dos registros retornados pela consulta é exibida. |
| **Lista** | |
| Consulta |A consulta a ser executada para a lista. As duas primeiras propriedades para os dez primeiros registros nos resultados são exibidas. A primeira propriedade é um valor de texto e a segunda propriedade é um valor numérico. As barras são criadas automaticamente com base no valor relativo da coluna numérica.<br><br>Use o comando `Sort` na consulta para classificar os registros na lista. Para executar a consulta e retornar todos os registros, você pode selecionar **ver tudo**. |
| Ocultar grafo |Selecione este link para desabilitar o grafo à direita da coluna numérica. |
| Habilitar minigráficos |Selecione este link para exibir um minigráfico em vez de uma barra horizontal. Para obter mais informações, consulte [configurações comuns](#sparklines). |
| Cor |A cor das barras ou dos minigráficos. |
| Operação |A operação a ser executada para o minigráfico. Para obter mais informações, consulte [configurações comuns](#sparklines). |
| Separador de nome e valor |O delimitador de caractere único a ser usado para analisar a propriedade Text em vários valores. Para obter mais informações, consulte [configurações comuns](#sparklines). |
| Navegação por clique | Ação executada quando você clica em um item na lista.  Para obter mais informações, consulte [configurações comuns](#click-through-navigation). |
| **Lista** |**> Títulos de coluna** |
| Nome |O texto que é exibido na parte superior da primeira coluna. |
| Valor |O texto que é exibido na parte superior da segunda coluna. |
| **Lista** |**Limites de >** |
| Habilitar limites |Selecione este link para habilitar os limites. Para obter mais informações, consulte [configurações comuns](#thresholds). |

## <a name="donut-and-list-part"></a>Rosca e parte da lista
O cabeçalho exibe um único número que resume uma coluna de valor em uma consulta de log. A rosca exibe graficamente os resultados dos três principais registros.

![Exibição de rosca e lista](media/view-designer-parts/view-donut-list.png)

| Definição | Descrição |
|:--- |:--- |
| **Geral** | |
| Título do grupo |O texto que é exibido na parte superior do bloco. |
| Novo grupo |Selecione este link para criar um novo grupo na exibição, começando no modo de exibição atual. |
| Cone |O arquivo de imagem que é exibido ao lado do resultado no cabeçalho. |
| Usar ícone |Selecione este link para exibir o ícone. |
| **Cabeçalho** | |
| Título |O texto que é exibido na parte superior do cabeçalho. |
| Subtítulo |O texto que é exibido sob o título na parte superior do cabeçalho. |
| **Rosca** | |
| Consulta |A consulta a ser executada para a rosca. A primeira propriedade é um valor de texto e a segunda propriedade é um valor numérico. |
| Navegação por clique | Ação executada quando você clica no cabeçalho.  Para obter mais informações, consulte [configurações comuns](#click-through-navigation). |
| **Rosca** |**Centro de >** |
| Texto |O texto que é exibido sob o valor dentro da rosca. |
| Operação |A operação a ser executada na propriedade Value para resumir como um único valor.<ul><li>Sum: Adiciona os valores de todos os registros.</li><li>Porcentagem: a taxa dos registros retornados pelos valores nos valores de **resultado usados na operação central** para o total de registros na consulta.</li></ul> |
| Valores de resultado usados na operação do centro |Opcionalmente, selecione o sinal de adição (+) para adicionar um ou mais valores. Os resultados da consulta são limitados a registros com os valores de propriedade que você especificar. Se nenhum valor for adicionado, todos os registros serão incluídos na consulta. |
| **Opções adicionais** |**Cores de >** |
| Cor 1<br>Cor 2<br>Cor 3 |Selecione a cor de cada um dos valores que são exibidos na rosca. |
| **Opções adicionais** |**Mapeamento de cores Avançado >** |
| Valor do campo |Digite o nome de um campo para exibi-lo como uma cor diferente se ele estiver incluído na rosca. |
| Cor |Selecione a cor do campo exclusivo. |
| **Lista** | |
| Consulta |A consulta a ser executada para a lista. A contagem dos registros retornados pela consulta é exibida. |
| Ocultar grafo |Selecione este link para desabilitar o grafo à direita da coluna numérica. |
| Habilitar minigráficos |Selecione este link para exibir um minigráfico em vez de uma barra horizontal. Para obter mais informações, consulte [configurações comuns](#sparklines). |
| Cor |A cor das barras ou dos minigráficos. |
| Operação |A operação a ser executada para o minigráfico. Para obter mais informações, consulte [configurações comuns](#sparklines). |
| Separador de nome e valor |O delimitador de caractere único a ser usado para analisar a propriedade Text em vários valores. Para obter mais informações, consulte [configurações comuns](#sparklines). |
| Navegação por clique | Ação executada quando você clica em um item na lista.  Para obter mais informações, consulte [configurações comuns](#click-through-navigation). |
| **Lista** |**> Títulos de coluna** |
| Nome |O texto que é exibido na parte superior da primeira coluna. |
| Valor |O texto que é exibido na parte superior da segunda coluna. |
| **Lista** |**Limites de >** |
| Habilitar limites |Selecione este link para habilitar os limites. Para obter mais informações, consulte [configurações comuns](#thresholds). |

## <a name="two-timelines-and-list-part"></a>Duas linhas do tempo e parte da lista
O cabeçalho exibe os resultados de duas consultas de log ao longo do tempo como gráficos de colunas, com um texto explicativo que exibe um único número que resume uma coluna de valor em uma consulta de log. A lista exibe os dez primeiros resultados de uma consulta, com um grafo que indica o valor relativo de uma coluna numérica ou sua alteração ao longo do tempo.

![Duas linhas do tempo e exibição de lista](media/view-designer-parts/view-two-timelines-list.png)

| Definição | Descrição |
|:--- |:--- |
| **Geral** | |
| Título do grupo |O texto que é exibido na parte superior do bloco. |
| Novo grupo |Selecione este link para criar um novo grupo na exibição, começando no modo de exibição atual. |
| Cone |O arquivo de imagem que é exibido ao lado do resultado no cabeçalho. |
| Usar ícone |Selecione este link para exibir o ícone. |
| **Navegação de título** | |
| Navegação por clique | Ação executada quando você clica no cabeçalho.  Para obter mais informações, consulte [configurações comuns](#click-through-navigation). |
| **Primeiro gráfico<br>segundo gráfico** | |
| Legenda |O texto que é exibido sob o texto explicativo para a primeira série. |
| Cor |A cor a ser usada para as colunas na série. |
| Consulta |A consulta a ser executada para a primeira série. A contagem de registros em cada intervalo de tempo é representada pelas colunas do gráfico. |
| Operação |A operação a ser executada na propriedade Value para resumir como um valor único para o texto explicativo.<ul><li>Sum: a soma dos valores de todos os registros.</li><li>Média: a média dos valores de todos os registros.</li><li>Última amostra: o valor do último intervalo que é incluído no gráfico.</li><li>Primeiro exemplo: o valor do primeiro intervalo que é incluído no gráfico.</li><li>Contagem: a contagem de todos os registros retornados pela consulta.</li></ul> |
| **Lista** | |
| Consulta |A consulta a ser executada para a lista. A contagem dos registros retornados pela consulta é exibida. |
| Ocultar grafo |Selecione este link para desabilitar o grafo à direita da coluna numérica. |
| Habilitar minigráficos |Selecione este link para exibir um minigráfico em vez de uma barra horizontal. Para obter mais informações, consulte [configurações comuns](#sparklines). |
| Cor |A cor das barras ou dos minigráficos. |
| Operação |A operação a ser executada para o minigráfico. Para obter mais informações, consulte [configurações comuns](#sparklines). |
| Navegação por clique | Ação executada quando você clica em um item na lista.  Para obter mais informações, consulte [configurações comuns](#click-through-navigation). |
| **Lista** |**> Títulos de coluna** |
| Nome |O texto que é exibido na parte superior da primeira coluna. |
| Valor |O texto que é exibido na parte superior da segunda coluna. |
| **Lista** |**Limites de >** |
| Habilitar limites |Selecione este link para habilitar os limites. Para obter mais informações, consulte [configurações comuns](#thresholds). |

## <a name="information-part"></a>Parte das informações
O cabeçalho exibe texto estático e um link opcional. A lista exibe um ou mais itens com um título e texto estáticos.

![Exibição de informações](media/view-designer-parts/view-information.png)

| Definição | Descrição |
|:--- |:--- |
| **Geral** | |
| Título do grupo |O texto que é exibido na parte superior do bloco. |
| Novo grupo |Selecione este link para criar um novo grupo na exibição, começando no modo de exibição atual. |
| Cor |A cor do plano de fundo do cabeçalho. |
| **Cabeçalho** | |
| Imagem |O arquivo de imagem que é exibido no cabeçalho. |
| Label |O texto que é exibido no cabeçalho. |
| **Cabeçalho** |**> Link** |
| Label |O texto do link. |
| Url |A URL do link. |
| **Itens de informações** | |
| Título |O texto que é exibido para o título de cada item. |
| Conteúdo |O texto que é exibido para cada item. |

## <a name="line-chart-callout-and-list-part"></a>Gráfico de linhas, balão e parte da lista
O cabeçalho exibe um gráfico de linhas com várias séries de uma consulta de log ao longo do tempo e um texto explicativo com um valor resumido. A lista exibe os dez primeiros resultados de uma consulta, com um grafo que indica o valor relativo de uma coluna numérica ou sua alteração ao longo do tempo.

![Gráfico de linhas, balão e exibição de lista](media/view-designer-parts/view-line-chart-callout-list.png)

| Definição | Descrição |
|:--- |:--- |
| **Geral** | |
| Título do grupo |O texto que é exibido na parte superior do bloco. |
| Novo grupo |Selecione este link para criar um novo grupo na exibição, começando no modo de exibição atual. |
| Cone |O arquivo de imagem que é exibido ao lado do resultado no cabeçalho. |
| Usar ícone |Selecione este link para exibir o ícone. |
| **Cabeçalho** | |
| Título |O texto que é exibido na parte superior do cabeçalho. |
| Subtítulo |O texto que é exibido sob o título na parte superior do cabeçalho. |
| **Gráfico de linhas** | |
| Consulta |A consulta a ser executada para o gráfico de linhas. A primeira propriedade é um valor de texto e a segunda propriedade é um valor numérico. Essa consulta normalmente usa a palavra-chave *Measure* para resumir os resultados. Se a consulta usar a palavra-chave *Interval* , o eixo x do gráfico usará esse intervalo de tempo. Se a consulta não incluir a palavra-chave *Interval* , o eixo x usará intervalos por hora. |
| Navegação por clique | Ação executada quando você clica no cabeçalho.  Para obter mais informações, consulte [configurações comuns](#click-through-navigation). |
| **Gráfico de linhas** |**> Texto explicativo** |
| Título do balão |O texto que é exibido acima do valor do texto explicativo. |
| Nome da série |Valor da propriedade da série a ser usada para o valor do texto explicativo. Se nenhuma série for fornecida, todos os registros da consulta serão usados. |
| Operação |A operação a ser executada na propriedade Value para resumir como um valor único para o texto explicativo.<ul><li>Média: a média dos valores de todos os registros.</li><li>Contagem: a contagem de todos os registros retornados pela consulta.</li><li>Última amostra: o valor do último intervalo que é incluído no gráfico.</li><li>Max: o valor máximo dos intervalos que são incluídos no gráfico.</li><li>Min: o valor mínimo dos intervalos que são incluídos no gráfico.</li><li>Sum: a soma dos valores de todos os registros.</li></ul> |
| **Gráfico de linhas** |**Eixo Y >** |
| Usar escala logarítmica |Selecione este link para usar uma escala logarítmica para o eixo y. |
| Unidades |Especifique as unidades para os valores a serem retornados pela consulta. Essas informações são usadas para exibir rótulos de gráfico que indicam os tipos de valor e, opcionalmente, para converter os valores. O tipo de *unidade* especifica a categoria da unidade e define os valores de tipo de *unidade atual* disponíveis. Se você selecionar um valor em *converter para*, os valores numéricos serão convertidos do tipo de *unidade atual* para o tipo *converter para* . |
| Rótulo personalizado |O texto que é exibido para o eixo y ao lado do rótulo do tipo de *unidade* . Se nenhum rótulo for especificado, somente o tipo de *unidade* será exibido. |
| **Lista** | |
| Consulta |A consulta a ser executada para a lista. A contagem dos registros retornados pela consulta é exibida. |
| Ocultar grafo |Selecione este link para desabilitar o grafo à direita da coluna numérica. |
| Habilitar minigráficos |Selecione este link para exibir um minigráfico em vez de uma barra horizontal. Para obter mais informações, consulte [configurações comuns](#sparklines). |
| Cor |A cor das barras ou dos minigráficos. |
| Operação |A operação a ser executada para o minigráfico. Para obter mais informações, consulte [configurações comuns](#sparklines). |
| Separador de nome e valor |O delimitador de caractere único a ser usado para analisar a propriedade Text em vários valores. Para obter mais informações, consulte [configurações comuns](#sparklines). |
| Navegação por clique | Ação executada quando você clica em um item na lista.  Para obter mais informações, consulte [configurações comuns](#click-through-navigation). |
| **Lista** |**> Títulos de coluna** |
| Nome |O texto que é exibido na parte superior da primeira coluna. |
| Valor |O texto que é exibido na parte superior da segunda coluna. |
| **Lista** |**Limites de >** |
| Habilitar limites |Selecione este link para habilitar os limites. Para obter mais informações, consulte [configurações comuns](#thresholds). |

## <a name="line-chart-and-list-part"></a>Gráfico de linhas e parte da lista
O cabeçalho exibe um gráfico de linhas com várias séries de uma consulta de log ao longo do tempo. A lista exibe os dez primeiros resultados de uma consulta, com um grafo que indica o valor relativo de uma coluna numérica ou sua alteração ao longo do tempo.

![Gráfico de linhas e exibição de lista](media/view-designer-parts/view-line-chart-callout-list.png)

| Definição | Descrição |
|:--- |:--- |
| **Geral** | |
| Título do grupo |O texto que é exibido na parte superior do bloco. |
| Novo grupo |Selecione este link para criar um novo grupo na exibição, começando no modo de exibição atual. |
| Cone |O arquivo de imagem que é exibido ao lado do resultado no cabeçalho. |
| Usar ícone |Selecione este link para exibir o ícone. |
| **Cabeçalho** | |
| Título |O texto que é exibido na parte superior do cabeçalho. |
| Subtítulo |O texto que é exibido sob o título na parte superior do cabeçalho. |
| **Gráfico de linhas** | |
| Consulta |A consulta a ser executada para o gráfico de linhas. A primeira propriedade é um valor de texto e a segunda propriedade é um valor numérico. Essa consulta normalmente usa a palavra-chave *Measure* para resumir os resultados. Se a consulta usar a palavra-chave *Interval* , o eixo x do gráfico usará esse intervalo de tempo. Se a consulta não incluir a palavra-chave *Interval* , o eixo x usará intervalos por hora. |
| Navegação por clique | Ação executada quando você clica no cabeçalho.  Para obter mais informações, consulte [configurações comuns](#click-through-navigation). |
| **Gráfico de linhas** |**Eixo Y >** |
| Usar escala logarítmica |Selecione este link para usar uma escala logarítmica para o eixo y. |
| Unidades |Especifique as unidades para os valores a serem retornados pela consulta. Essas informações são usadas para exibir rótulos de gráfico que indicam os tipos de valor e, opcionalmente, para converter os valores. O tipo de *unidade* especifica a categoria da unidade e define os valores de tipo de *unidade atual* disponíveis. Se você selecionar um valor em *converter para*, os valores numéricos serão convertidos do tipo de *unidade atual* para o tipo *converter para* . |
| Rótulo personalizado |O texto que é exibido para o eixo y ao lado do rótulo do tipo de *unidade* . Se nenhum rótulo for especificado, somente o tipo de *unidade* será exibido. |
| **Lista** | |
| Consulta |A consulta a ser executada para a lista. A contagem dos registros retornados pela consulta é exibida. |
| Ocultar grafo |Selecione este link para desabilitar o grafo à direita da coluna numérica. |
| Habilitar minigráficos |Selecione este link para exibir um minigráfico em vez de uma barra horizontal. Para obter mais informações, consulte [configurações comuns](#sparklines). |
| Cor |A cor das barras ou dos minigráficos. |
| Operação |A operação a ser executada para o minigráfico. Para obter mais informações, consulte [configurações comuns](#sparklines). |
| Separador de nome e valor |O delimitador de caractere único a ser usado para analisar a propriedade Text em vários valores. Para obter mais informações, consulte [configurações comuns](#sparklines). |
| Navegação por clique | Ação executada quando você clica em um item na lista.  Para obter mais informações, consulte [configurações comuns](#click-through-navigation). |
| **Lista** |**> Títulos de coluna** |
| Nome |O texto que é exibido na parte superior da primeira coluna. |
| Valor |O texto que é exibido na parte superior da segunda coluna. |
| **Lista** |**Limites de >** |
| Habilitar limites |Selecione este link para habilitar os limites. Para obter mais informações, consulte [configurações comuns](#thresholds). |

## <a name="stack-of-line-charts-part"></a>Pilha de parte de gráficos de linhas
A pilha do gráfico de linhas exibe três gráficos de linhas separados, com várias séries de uma consulta de log ao longo do tempo, conforme mostrado aqui:

![Pilha de gráficos de linhas](media/view-designer-parts/view-stack-line-charts.png)

| Definição | Descrição |
|:--- |:--- |
| **Geral** | |
| Título do grupo |O texto que é exibido na parte superior do bloco. |
| Novo grupo |Selecione este link para criar um novo grupo na exibição, começando no modo de exibição atual. |
| Cone |O arquivo de imagem que é exibido ao lado do resultado no cabeçalho. |
| **Gráfico 1<br>gráfico 2<br>gráfico 3** |**Cabeçalho de >** |
| Título |O texto que é exibido na parte superior do gráfico. |
| Subtítulo |O texto que é exibido sob o título na parte superior do gráfico. |
| **Gráfico 1<br>gráfico 2<br>gráfico 3** |**Gráfico de linhas** |
| Consulta |A consulta a ser executada para o gráfico de linhas. A primeira propriedade é um valor de texto e a segunda propriedade é um valor numérico. Essa consulta normalmente usa a palavra-chave *Measure* para resumir os resultados. Se a consulta usar a palavra-chave *Interval* , o eixo x do gráfico usará esse intervalo de tempo. Se a consulta não incluir a palavra-chave *Interval* , o eixo x usará intervalos por hora. |
| Navegação por clique | Ação executada quando você clica no cabeçalho.  Para obter mais informações, consulte [configurações comuns](#click-through-navigation). |
| **Tipo** |**Eixo Y >** |
| Usar escala logarítmica |Selecione este link para usar uma escala logarítmica para o eixo y. |
| Unidades |Especifique as unidades para os valores a serem retornados pela consulta. Essas informações são usadas para exibir rótulos de gráfico que indicam os tipos de valor e, opcionalmente, para converter os valores. O tipo de *unidade* especifica a categoria da unidade e define os valores de tipo de *unidade atual* disponíveis. Se você selecionar um valor em *converter para*, os valores numéricos serão convertidos do tipo de *unidade atual* para o tipo *converter para* . |
| Rótulo personalizado |O texto que é exibido para o eixo y ao lado do rótulo do tipo de *unidade* . Se nenhum rótulo for especificado, somente o tipo de *unidade* será exibido. |

## <a name="common-settings"></a>Configurações comuns
As seções a seguir descrevem as configurações que são comuns a várias partes de visualização.

### <a name="name-value-separator"></a>Separador de nome e valor
O separador de nome e valor é o delimitador de caractere único a ser usado para analisar a propriedade Text de uma consulta de lista em vários valores. Se você especificar um delimitador, poderá fornecer nomes para cada campo, separados pelo mesmo delimitador na caixa **nome** .

Por exemplo, considere uma propriedade chamada *Location* que incluía valores como *Redmond-Building 41* e *Bellevue-Building 12*. Você pode especificar um traço (-) para o separador de nome e valor e *cidade-prédio* para o nome. Essa abordagem analisa cada valor em duas propriedades chamadas *cidade* e *construção*.

### <a name="click-through-navigation"></a>Navegação por clique
A navegação por clique define qual ação será tomada quando você clicar em um cabeçalho ou item de lista em uma exibição.  Isso abrirá uma consulta no [log Analytics](../../azure-monitor/log-query/portals.md) ou iniciará outra exibição.

A tabela a seguir descreve as configurações de navegação de clique.

| Definição           | Descrição |
|:--|:--|
| Pesquisa de logs (automática) | Consulta de log a ser executada quando você seleciona um item de cabeçalho.  Essa é a mesma consulta de log na qual o item se baseia.
| Pesquisa de Registos        | Consulta de log a ser executada quando você seleciona um item em uma lista.  Digite a consulta na caixa **consulta de navegação** .   Use *{Selected item}* para incluir a sintaxe do item selecionado pelo usuário.  Por exemplo, se a consulta tiver uma coluna chamada *computador* e a consulta de navegação for *{Selected item}* , uma consulta como *Computer = "MyComputer"* será executada quando você selecionar um computador. Se a consulta de navegação for *Type = evento {Selected item}* , a consulta *Type = Event Computer = "MyComputer"* será executada. |
| Ver              | Exibição para abrir quando você seleciona um item de cabeçalho ou um item em uma lista.  Selecione o nome de uma exibição em seu espaço de trabalho na caixa **nome da exibição** . |



### <a name="sparklines"></a>Minigráficos
Um minigráfico é um pequeno gráfico de linhas que ilustra o valor de uma entrada de lista ao longo do tempo. Para partes de visualização com uma lista, você pode selecionar se deseja exibir uma barra horizontal, que indica o valor relativo de uma coluna numérica ou um minigráfico, que indica seu valor ao longo do tempo.

A tabela a seguir descreve as configurações para minigráficos:

| Definição | Descrição |
|:--- |:--- |
| Habilitar minigráficos |Selecione este link para exibir um minigráfico em vez de uma barra horizontal. |
| Operação |Se os minigráficos estiverem habilitados, essa será a operação a ser executada em cada propriedade na lista para calcular os valores para o minigráfico.<ul><li>Última amostra: o último valor para a série ao longo do intervalo de tempo.</li><li>Max: o valor máximo para a série durante o intervalo de tempo.</li><li>Min: o valor mínimo para a série ao longo do intervalo de tempo.</li><li>Sum: a soma dos valores para a série ao longo do intervalo de tempo.</li><li>Resumo: usa o mesmo comando `measure` que a consulta no cabeçalho.</li></ul> |

### <a name="thresholds"></a>Limites
Usando limites, você pode exibir um ícone colorido ao lado de cada item em uma lista. Os limites fornecem um indicador visual rápido de itens que excedem um valor específico ou caem dentro de um intervalo específico. Por exemplo, você pode exibir um ícone verde para itens com um valor aceitável, amarelo se o valor estiver dentro de um intervalo que indica um aviso e vermelho se ele exceder um valor de erro.

Ao habilitar os limites de uma parte, você deve especificar um ou mais limites. Se o valor de um item for maior que um valor de limite e menor que o próximo valor de limite, a cor desse valor será usada. Se o item for maior que o valor de limite mais alto, outra cor será usada. 

Cada conjunto de limites tem um limite com um valor de **padrão**. Essa é a cor que será definida se nenhum outro valor for excedido. Você pode adicionar ou remover limites selecionando o botão **Adicionar** (+) ou **excluir** (x).

A tabela a seguir descreve as configurações para limites:

| Definição | Descrição |
|:--- |:--- |
| Habilitar limites |Selecione este link para exibir um ícone de cor à esquerda de cada valor. O ícone indica a integridade do valor em relação aos limites especificados. |
| Nome |O nome do valor do limite. |
| Os |O valor do limite. A cor de integridade de cada item de lista é definida com a cor do valor de limite mais alto que é excedido pelo valor do item. Se nenhum valor de limite for excedido, uma cor padrão será usada. |
| Cor |A cor que indica o valor do limite. |

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [consultas de log](../log-query/log-query-overview.md) para dar suporte às consultas em partes de visualização.
