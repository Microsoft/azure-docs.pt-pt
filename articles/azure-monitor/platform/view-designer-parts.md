---
title: Um guia de referência para as peças do Designer de Visualização no Monitor Azure [ Microsoft Docs
description: Ao utilizar o View Designer no Monitor Azure, pode criar vistas personalizadas que são exibidas no portal Azure e conter uma variedade de visualizações sobre dados no espaço de trabalho log Analytics. Este artigo é um guia de referência para as definições para as peças de visualização que estão disponíveis nas suas vistas personalizadas.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2018
ms.openlocfilehash: 7dc4164cac1998a02ae62d7682f9630aa4faa619
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77658562"
---
# <a name="reference-guide-to-view-designer-visualization-parts-in-azure-monitor"></a>Guia de referência para ver peças de visualização do Designer no Monitor Azure
Ao utilizar o View Designer no Monitor Azure, pode criar uma variedade de vistas personalizadas no portal Azure que podem ajudá-lo a visualizar dados no seu espaço de trabalho Log Analytics. Este artigo é um guia de referência para as definições para as peças de visualização que estão disponíveis nas suas vistas personalizadas.

Para mais informações sobre o View Designer, consulte:

* [Ver Designer](view-designer.md): Fornece uma visão geral do View Designer e procedimentos para criar e editar vistas personalizadas.
* [Referência de azulejos](view-designer-tiles.md): Fornece uma referência às definições de cada azulejo disponível nas suas vistas personalizadas.


Os tipos de azulejos view designer disponíveis são descritos na tabela seguinte:

| Tipo de visualização | Descrição |
|:--- |:--- |
| [Lista de consultas](#list-of-queries-part) |Apresenta uma lista de consultas de registo. Pode selecionar cada consulta para apresentar os seus resultados. |
| [Número e lista](#number-and-list-part) |O cabeçalho apresenta um único número que mostra uma contagem de registos de uma consulta de registo. A lista apresenta os dez melhores resultados de uma consulta, com um gráfico que indica o valor relativo de uma coluna numérica ou a sua mudança ao longo do tempo. |
| [Dois números e lista](#two-numbers-and-list-part) |O cabeçalho apresenta dois números que mostram contagens de registos de consultas de registo separadas. A lista apresenta os dez melhores resultados de uma consulta, com um gráfico que indica o valor relativo de uma coluna numérica ou a sua mudança ao longo do tempo. |
| [Donut e lista](#donut-and-list-part) |O cabeçalho apresenta um único número que resume uma coluna de valor numa consulta de registo. O donut apresenta graficamente os resultados dos três primeiros registos. |
| [Duas cronologias e lista](#two-timelines-and-list-part) |O cabeçalho exibe os resultados de duas consultas de registo ao longo do tempo como gráficos de colunas, com uma chamada que apresenta um único número que resume uma coluna de valor numa consulta de registo. A lista apresenta os dez melhores resultados de uma consulta, com um gráfico que indica o valor relativo de uma coluna numérica ou a sua mudança ao longo do tempo. |
| [Informações](#information-part) |O cabeçalho apresenta texto estático e um link opcional. A lista apresenta um ou mais itens com um título e texto estáticos. |
| [Gráfico de linha, chamada e lista](#line-chart-callout-and-list-part) |O cabeçalho exibe um gráfico de linha, com várias séries de uma consulta de log ao longo do tempo e uma chamada com um valor resumido. A lista apresenta os dez melhores resultados de uma consulta, com um gráfico que indica o valor relativo de uma coluna numérica ou a sua mudança ao longo do tempo. |
| [Gráfico de linha e lista](#line-chart-and-list-part) |O cabeçalho exibe um gráfico de linha, com várias séries de uma consulta de log ao longo do tempo. A lista apresenta os dez melhores resultados de uma consulta, com um gráfico que indica o valor relativo de uma coluna numérica ou a sua mudança ao longo do tempo. |
| [Pilha de gráficos de linha parte](#stack-of-line-charts-part) |Exibe três gráficos de linha separados, com várias séries de uma consulta de log ao longo do tempo. |

As secções seguintes descrevem os tipos de azulejos e as suas propriedades em detalhe.

> [!NOTE]
> As peças em vistas baseiam-se em consultas de [registo](../log-query/log-query-overview.md) no seu espaço de trabalho Log Analytics. Atualmente, não suportam [consultas de recursos cruzados](../log-query/cross-workspace-query.md) para obter dados de Insights de Aplicação.

## <a name="list-of-queries-part"></a>Parte da lista de consultas
A parte da lista de consultas apresenta uma lista de consultas de registo. Pode selecionar cada consulta para apresentar os seus resultados. A vista inclui uma única consulta por padrão, e pode selecionar **+ Consulta** para adicionar consultas adicionais.

![Vista de consulta](media/view-designer-parts/view-list-queries.png)

| Definição | Descrição |
|:--- |:--- |
| **General** | |
| Título |O texto que está exposto no topo da vista. |
| Novo Grupo |Selecione este link para criar um novo grupo na vista, a partir da vista atual. |
| Filtros pré-selecionados |Uma lista de propriedades delimitada sela a incluir no painel do filtro esquerdo quando seleciona uma consulta. |
| Modo render |A vista inicial que é exibida quando a consulta é selecionada. Pode selecionar quaisquer vistas disponíveis depois de abrirem a consulta. |
| **Consultas** | |
| Consulta de pesquisa |A consulta para correr. |
| Nome amigável | O nome descritivo que é exibido. |

## <a name="number-and-list-part"></a>Número e parte da lista
O cabeçalho apresenta um único número que mostra uma contagem de registos de uma consulta de registo. A lista apresenta os dez melhores resultados de uma consulta, com um gráfico que indica o valor relativo de uma coluna numérica ou a sua mudança ao longo do tempo.

![Vista de consulta](media/view-designer-parts/view-number-list.png)

| Definição | Descrição |
|:--- |:--- |
| **General** | |
| Título do grupo |O texto que está exposto no topo da vista. |
| Novo Grupo |Selecione este link para criar um novo grupo na vista, a partir da vista atual. |
| Ícone |O ficheiro de imagem que é apresentado ao lado do resultado no cabeçalho. |
| Ícone de uso |Selecione este link para visualizar o ícone. |
| **Título** | |
| Legenda |O texto que está exposto no topo do cabeceamento. |
| Consulta |A consulta para correr para o cabeceamento. A contagem dos registos que são devolvidos pela consulta é exibida. |
| Navegação por click-through | Ação tomada quando clica no cabeçalho.  Para mais informações, consulte [Definições Comuns](#click-through-navigation). |
| **Lista** | |
| Consulta |A consulta para correr para a lista. As duas primeiras propriedades para os primeiros dez registos dos resultados são apresentadas. A primeira propriedade é um valor de texto, e a segunda propriedade é um valor numérico. As barras são automaticamente criadas que são baseadas no valor relativo da coluna numérica.<br><br>Use `Sort` o comando na consulta para classificar os registos da lista. Para executar a consulta e devolver todos os registos, pode selecionar **Ver tudo**. |
| Ocultar gráfico |Selecione este link para desativar o gráfico à direita da coluna numérica. |
| Ativar as linhas de faísca |Selecione este link para exibir uma linha de faísca em vez de uma barra horizontal. Para mais informações, consulte [Definições Comuns](#sparklines). |
| Cor |A cor das barras ou das faíscas. |
| Separador de nome e valor |O delimitador de caracteres únicos para usar para analisar a propriedade de texto em vários valores. Para mais informações, consulte [Definições Comuns](#sparklines). |
| Navegação por click-through | Ação tomada quando clica num item na lista.  Para mais informações, consulte [Definições Comuns](#click-through-navigation). |
| **Lista** |**títulos da Coluna>** |
| Nome |O texto que é exibido no topo da primeira coluna. |
| Valor |O texto que está exposto no topo da segunda coluna. |
| **Lista** |**limiares de>** |
| Limiares de ativação |Selecione este link para ativar os limiares. Para mais informações, consulte [Definições Comuns](#thresholds). |

## <a name="two-numbers-and-list-part"></a>Dois números e parte da lista
O cabeçalho tem dois números que mostram uma contagem de registos de consultas de registo separadas. A lista apresenta os dez melhores resultados de uma consulta, com um gráfico que indica o valor relativo de uma coluna numérica ou a sua mudança ao longo do tempo.

![Dois números & vista de lista](media/view-designer-parts/view-two-numbers-list.png)

| Definição | Descrição |
|:--- |:--- |
| **General** | |
| Título do grupo |O texto que está exposto no topo da vista. |
| Novo Grupo |Selecione este link para criar um novo grupo na vista, a partir da vista atual. |
| Ícone |O ficheiro de imagem que é apresentado ao lado do resultado no cabeçalho. |
| Ícone de uso |Selecione este link para visualizar o ícone. |
| **Navegação de Título** | |
| Navegação por click-through | Ação tomada quando clica no cabeçalho.  Para mais informações, consulte [Definições Comuns](#click-through-navigation). |
| **Título** | |
| Legenda |O texto que está exposto no topo do cabeceamento. |
| Consulta |A consulta para correr para o cabeceamento. A contagem dos registos que são devolvidos pela consulta é exibida. |
| **Lista** | |
| Consulta |A consulta para correr para a lista. As duas primeiras propriedades para os primeiros dez registos dos resultados são apresentadas. A primeira propriedade é um valor de texto, e a segunda propriedade é um valor numérico. As barras são automaticamente criadas com base no valor relativo da coluna numérica.<br><br>Use `Sort` o comando na consulta para classificar os registos da lista. Para executar a consulta e devolver todos os registos, pode selecionar **Ver tudo**. |
| Ocultar gráfico |Selecione este link para desativar o gráfico à direita da coluna numérica. |
| Ativar as linhas de faísca |Selecione este link para exibir uma linha de faísca em vez de uma barra horizontal. Para mais informações, consulte [Definições Comuns](#sparklines). |
| Cor |A cor das barras ou das faíscas. |
| Operação |A operação a executar para a linha de faísca. Para mais informações, consulte [Definições Comuns](#sparklines). |
| Separador de nome e valor |O delimitador de caracteres únicos para usar para analisar a propriedade de texto em vários valores. Para mais informações, consulte [Definições Comuns](#sparklines). |
| Navegação por click-through | Ação tomada quando clica num item na lista.  Para mais informações, consulte [Definições Comuns](#click-through-navigation). |
| **Lista** |**títulos da Coluna>** |
| Nome |O texto que é exibido no topo da primeira coluna. |
| Valor |O texto que está exposto no topo da segunda coluna. |
| **Lista** |**limiares de>** |
| Limiares de ativação |Selecione este link para ativar os limiares. Para mais informações, consulte [Definições Comuns](#thresholds). |

## <a name="donut-and-list-part"></a>Donut e parte da lista
O cabeçalho apresenta um único número que resume uma coluna de valor numa consulta de registo. O donut apresenta graficamente os resultados dos três primeiros registos.

![Donut e vista da lista](media/view-designer-parts/view-donut-list.png)

| Definição | Descrição |
|:--- |:--- |
| **General** | |
| Título do grupo |O texto que está exposto no topo do azulejo. |
| Novo Grupo |Selecione este link para criar um novo grupo na vista, a partir da vista atual. |
| Ícone |O ficheiro de imagem que é apresentado ao lado do resultado no cabeçalho. |
| Ícone de uso |Selecione este link para visualizar o ícone. |
| **Cabeçalho** | |
| Título |O texto que está exposto no topo do cabeceamento. |
| Subtítulo |O texto que é exibido sob o título no topo do cabeçalho. |
| **Donut** | |
| Consulta |A consulta para correr para o donut. A primeira propriedade é um valor de texto, e a segunda propriedade é um valor numérico. |
| Navegação por click-through | Ação tomada quando clica no cabeçalho.  Para mais informações, consulte [Definições Comuns](#click-through-navigation). |
| **Donut** |**Centro>** |
| Texto |O texto que é exibido sob o valor dentro do donut. |
| Operação |A operação para executar sobre a propriedade de valor para resumir como um único valor.<ul><li>Soma: Adiciona os valores de todos os registos.</li><li>Percentagem: O rácio dos registos devolvidos pelos valores dos valores do **resultado utilizados no funcionamento** central aos registos totais da consulta.</li></ul> |
| Valores de resultadoutilizados no funcionamento do centro |Opcionalmente, selecione o sinal de mais (+) para adicionar um ou mais valores. Os resultados da consulta limitam-se a registos com os valores de propriedade que especifica. Se não forem adicionados valores, todos os registos estão incluídos na consulta. |
| **Opções adicionais** |**cores>** |
| Cor 1<br>Cor 2<br>Cor 3 |Selecione a cor para cada um dos valores que são apresentados no donut. |
| **Opções adicionais** |**> Mapeamento de cores avançados** |
| Valor do campo |Digite o nome de um campo para exibi-lo como uma cor diferente se estiver incluído no donut. |
| Cor |Selecione a cor para o campo único. |
| **Lista** | |
| Consulta |A consulta para correr para a lista. A contagem dos registos que são devolvidos pela consulta é exibida. |
| Ocultar gráfico |Selecione este link para desativar o gráfico à direita da coluna numérica. |
| Ativar as linhas de faísca |Selecione este link para exibir uma linha de faísca em vez de uma barra horizontal. Para mais informações, consulte [Definições Comuns](#sparklines). |
| Cor |A cor das barras ou das faíscas. |
| Operação |A operação a executar para a linha de faísca. Para mais informações, consulte [Definições Comuns](#sparklines). |
| Separador de nome e valor |O delimitador de caracteres únicos para usar para analisar a propriedade de texto em vários valores. Para mais informações, consulte [Definições Comuns](#sparklines). |
| Navegação por click-through | Ação tomada quando clica num item na lista.  Para mais informações, consulte [Definições Comuns](#click-through-navigation). |
| **Lista** |**títulos da Coluna>** |
| Nome |O texto que é exibido no topo da primeira coluna. |
| Valor |O texto que está exposto no topo da segunda coluna. |
| **Lista** |**limiares de>** |
| Limiares de ativação |Selecione este link para ativar os limiares. Para mais informações, consulte [Definições Comuns](#thresholds). |

## <a name="two-timelines-and-list-part"></a>Duas cronologias e parte da lista
O cabeçalho exibe os resultados de duas consultas de registo ao longo do tempo como gráficos de colunas, com uma chamada que apresenta um único número que resume uma coluna de valor numa consulta de registo. A lista apresenta os dez melhores resultados de uma consulta, com um gráfico que indica o valor relativo de uma coluna numérica ou a sua mudança ao longo do tempo.

![Duas cronologias e vista para a lista](media/view-designer-parts/view-two-timelines-list.png)

| Definição | Descrição |
|:--- |:--- |
| **General** | |
| Título do grupo |O texto que está exposto no topo do azulejo. |
| Novo Grupo |Selecione este link para criar um novo grupo na vista, a partir da vista atual. |
| Ícone |O ficheiro de imagem que é apresentado ao lado do resultado no cabeçalho. |
| Ícone de uso |Selecione este link para visualizar o ícone. |
| **Navegação de Título** | |
| Navegação por click-through | Ação tomada quando clica no cabeçalho.  Para mais informações, consulte [Definições Comuns](#click-through-navigation). |
| **Primeiro<br>gráfico Segundo gráfico** | |
| Legenda |O texto que é exibido sob o callout para a primeira série. |
| Cor |A cor a usar para as colunas da série. |
| Consulta |A consulta para correr para a primeira série. A contagem de registos em cada intervalo de tempo é representada pelas colunas do gráfico. |
| Operação |A operação para executar na propriedade de valor para resumir como um único valor para a chamada.<ul><li>Soma: A soma dos valores de todos os registos.</li><li>Média: A média dos valores de todos os registos.</li><li>Última amostra: O valor do último intervalo incluído na tabela.</li><li>Primeira amostra: O valor do primeiro intervalo incluído na tabela.</li><li>Contagem: A contagem de todos os registos que são devolvidos pela consulta.</li></ul> |
| **Lista** | |
| Consulta |A consulta para correr para a lista. A contagem dos registos que são devolvidos pela consulta é exibida. |
| Ocultar gráfico |Selecione este link para desativar o gráfico à direita da coluna numérica. |
| Ativar as linhas de faísca |Selecione este link para exibir uma linha de faísca em vez de uma barra horizontal. Para mais informações, consulte [Definições Comuns](#sparklines). |
| Cor |A cor das barras ou das faíscas. |
| Operação |A operação a executar para a linha de faísca. Para mais informações, consulte [Definições Comuns](#sparklines). |
| Navegação por click-through | Ação tomada quando clica num item na lista.  Para mais informações, consulte [Definições Comuns](#click-through-navigation). |
| **Lista** |**títulos da Coluna>** |
| Nome |O texto que é exibido no topo da primeira coluna. |
| Valor |O texto que está exposto no topo da segunda coluna. |
| **Lista** |**limiares de>** |
| Limiares de ativação |Selecione este link para ativar os limiares. Para mais informações, consulte [Definições Comuns](#thresholds). |

## <a name="information-part"></a>Parte da informação
O cabeçalho apresenta texto estático e um link opcional. A lista apresenta um ou mais itens com um título e texto estáticos.

![Vista da informação](media/view-designer-parts/view-information.png)

| Definição | Descrição |
|:--- |:--- |
| **General** | |
| Título do grupo |O texto que está exposto no topo do azulejo. |
| Novo Grupo |Selecione este link para criar um novo grupo na vista, a partir da vista atual. |
| Cor |A cor de fundo para o cabeçalho. |
| **Cabeçalho** | |
| Imagem |O ficheiro de imagem que está exposto no cabeçalho. |
| Etiqueta |O texto que está exposto no cabeçalho. |
| **Cabeçalho** |**Link>** |
| Etiqueta |O texto de ligação. |
| Url |A Url para o link. |
| **Itens de informação** | |
| Título |O texto que é exibido para o título de cada item. |
| Conteúdo |O texto que é exibido para cada item. |

## <a name="line-chart-callout-and-list-part"></a>Gráfico de linha, chamada e parte da lista
O cabeçalho exibe um gráfico de linha com várias séries de uma consulta de log ao longo do tempo e uma chamada com um valor resumido. A lista apresenta os dez melhores resultados de uma consulta, com um gráfico que indica o valor relativo de uma coluna numérica ou a sua mudança ao longo do tempo.

![Gráfico de linha, chamada e vista da lista](media/view-designer-parts/view-line-chart-callout-list.png)

| Definição | Descrição |
|:--- |:--- |
| **General** | |
| Título do grupo |O texto que está exposto no topo do azulejo. |
| Novo Grupo |Selecione este link para criar um novo grupo na vista, a partir da vista atual. |
| Ícone |O ficheiro de imagem que é apresentado ao lado do resultado no cabeçalho. |
| Ícone de uso |Selecione este link para visualizar o ícone. |
| **Cabeçalho** | |
| Título |O texto que está exposto no topo do cabeceamento. |
| Subtítulo |O texto que é exibido sob o título no topo do cabeçalho. |
| **Gráfico de linhas** | |
| Consulta |A consulta para correr para o gráfico de linha. A primeira propriedade é um valor de texto, e a segunda propriedade é um valor numérico. Esta consulta usa normalmente a palavra-chave de *medida* para resumir resultados. Se a consulta utilizar a palavra-chave de *intervalo,* o eixo x do gráfico utiliza este intervalo de tempo. Se a consulta não incluir a palavra-chave de *intervalo,* o eixo x utiliza intervalos de hora. |
| Navegação por click-through | Ação tomada quando clica no cabeçalho.  Para mais informações, consulte [Definições Comuns](#click-through-navigation). |
| **Gráfico de linhas** |**> Callout** |
| Título de callout |O texto que é exibido acima do valor de chamada. |
| Nome da série |Valor patrimonial para a série a utilizar para o valor de chamada. Se não for fornecida nenhuma série, todos os registos da consulta são utilizados. |
| Operação |A operação para executar na propriedade de valor para resumir como um único valor para a chamada.<ul><li>Média: A média dos valores de todos os registos.</li><li>Contagem: A contagem de todos os registos que são devolvidos pela consulta.</li><li>Última amostra: O valor do último intervalo incluído na tabela.</li><li>Max: O valor máximo dos intervalos incluídos na tabela.</li><li>Min: O valor mínimo dos intervalos incluídos na tabela.</li><li>Soma: A soma dos valores de todos os registos.</li></ul> |
| **Gráfico de linhas** |**> eixo Y** |
| Utilizar escala logarítmica |Selecione este link para utilizar uma escala logarítmica para o eixo y. |
| Unidades |Especifique as unidades para que os valores sejam devolvidos pela consulta. Esta informação é utilizada para exibir etiquetas de gráficos que indicam os tipos de valor e, opcionalmente, para converter os valores. O tipo *Unidade* especifica a categoria da unidade e define os valores do tipo *Unidade Atual* disponíveis. Se selecionar um valor em *Converter para*, os valores numéricos são convertidos do tipo *Unidade Atual* para o Converter *para* escrever. |
| Etiqueta personalizada |O texto apresentado para o eixo y ao lado da etiqueta para o tipo *Unidade.* Se não for especificada a etiqueta, apenas o tipo *Unidade* é apresentado. |
| **Lista** | |
| Consulta |A consulta para correr para a lista. A contagem dos registos que são devolvidos pela consulta é exibida. |
| Ocultar gráfico |Selecione este link para desativar o gráfico à direita da coluna numérica. |
| Ativar as linhas de faísca |Selecione este link para exibir uma linha de faísca em vez de uma barra horizontal. Para mais informações, consulte [Definições Comuns](#sparklines). |
| Cor |A cor das barras ou das faíscas. |
| Operação |A operação a executar para a linha de faísca. Para mais informações, consulte [Definições Comuns](#sparklines). |
| Separador de nome e valor |O delimitador de caracteres únicos para usar para analisar a propriedade de texto em vários valores. Para mais informações, consulte [Definições Comuns](#sparklines). |
| Navegação por click-through | Ação tomada quando clica num item na lista.  Para mais informações, consulte [Definições Comuns](#click-through-navigation). |
| **Lista** |**títulos da Coluna>** |
| Nome |O texto que é exibido no topo da primeira coluna. |
| Valor |O texto que está exposto no topo da segunda coluna. |
| **Lista** |**limiares de>** |
| Limiares de ativação |Selecione este link para ativar os limiares. Para mais informações, consulte [Definições Comuns](#thresholds). |

## <a name="line-chart-and-list-part"></a>Gráfico de linha e parte da lista
O cabeçalho exibe um gráfico de linha com várias séries de uma consulta de log ao longo do tempo. A lista apresenta os dez melhores resultados de uma consulta, com um gráfico que indica o valor relativo de uma coluna numérica ou a sua mudança ao longo do tempo.

![Gráfico de linha e vista de lista](media/view-designer-parts/view-line-chart-callout-list.png)

| Definição | Descrição |
|:--- |:--- |
| **General** | |
| Título do grupo |O texto que está exposto no topo do azulejo. |
| Novo Grupo |Selecione este link para criar um novo grupo na vista, a partir da vista atual. |
| Ícone |O ficheiro de imagem que é apresentado ao lado do resultado no cabeçalho. |
| Ícone de uso |Selecione este link para visualizar o ícone. |
| **Cabeçalho** | |
| Título |O texto que está exposto no topo do cabeceamento. |
| Subtítulo |O texto que é exibido sob o título no topo do cabeçalho. |
| **Gráfico de linhas** | |
| Consulta |A consulta para correr para o gráfico de linha. A primeira propriedade é um valor de texto, e a segunda propriedade é um valor numérico. Esta consulta usa normalmente a palavra-chave de *medida* para resumir resultados. Se a consulta utilizar a palavra-chave de *intervalo,* o eixo x do gráfico utiliza este intervalo de tempo. Se a consulta não incluir a palavra-chave de *intervalo,* o eixo x utiliza intervalos de hora. |
| Navegação por click-through | Ação tomada quando clica no cabeçalho.  Para mais informações, consulte [Definições Comuns](#click-through-navigation). |
| **Gráfico de linhas** |**> eixo Y** |
| Utilizar escala logarítmica |Selecione este link para utilizar uma escala logarítmica para o eixo y. |
| Unidades |Especifique as unidades para que os valores sejam devolvidos pela consulta. Esta informação é utilizada para exibir etiquetas de gráficos que indicam os tipos de valor e, opcionalmente, para converter os valores. O tipo *Unidade* especifica a categoria da unidade e define os valores do tipo *Unidade Atual* disponíveis. Se selecionar um valor em *Converter para*, os valores numéricos são convertidos do tipo *Unidade Atual* para o Converter *para* escrever. |
| Etiqueta personalizada |O texto apresentado para o eixo y ao lado da etiqueta para o tipo *Unidade.* Se não for especificada a etiqueta, apenas o tipo *Unidade* é apresentado. |
| **Lista** | |
| Consulta |A consulta para correr para a lista. A contagem dos registos que são devolvidos pela consulta é exibida. |
| Ocultar gráfico |Selecione este link para desativar o gráfico à direita da coluna numérica. |
| Ativar as linhas de faísca |Selecione este link para exibir uma linha de faísca em vez de uma barra horizontal. Para mais informações, consulte [Definições Comuns](#sparklines). |
| Cor |A cor das barras ou das faíscas. |
| Operação |A operação a executar para a linha de faísca. Para mais informações, consulte [Definições Comuns](#sparklines). |
| Separador de nome e valor |O delimitador de caracteres únicos para usar para analisar a propriedade de texto em vários valores. Para mais informações, consulte [Definições Comuns](#sparklines). |
| Navegação por click-through | Ação tomada quando clica num item na lista.  Para mais informações, consulte [Definições Comuns](#click-through-navigation). |
| **Lista** |**títulos da Coluna>** |
| Nome |O texto que é exibido no topo da primeira coluna. |
| Valor |O texto que está exposto no topo da segunda coluna. |
| **Lista** |**limiares de>** |
| Limiares de ativação |Selecione este link para ativar os limiares. Para mais informações, consulte [Definições Comuns](#thresholds). |

## <a name="stack-of-line-charts-part"></a>Pilha de gráficos de linha parte
A pilha de gráficos de linha apresenta três gráficos de linha separados, com várias séries de uma consulta de log ao longo do tempo, como mostrado aqui:

![Pilha de gráficos de linha](media/view-designer-parts/view-stack-line-charts.png)

| Definição | Descrição |
|:--- |:--- |
| **General** | |
| Título do grupo |O texto que está exposto no topo do azulejo. |
| Novo Grupo |Selecione este link para criar um novo grupo na vista, a partir da vista atual. |
| Ícone |O ficheiro de imagem que é apresentado ao lado do resultado no cabeçalho. |
| **Gráfico<br>1<br>Gráfico 2 Gráfico 3** |**cabeçalho>** |
| Título |O texto que está exposto no topo da tabela. |
| Subtítulo |O texto que é exibido sob o título no topo da tabela. |
| **Gráfico<br>1<br>Gráfico 2 Gráfico 3** |**Gráfico de linhas** |
| Consulta |A consulta para correr para o gráfico de linha. A primeira propriedade é um valor de texto, e a segunda propriedade é um valor numérico. Esta consulta usa normalmente a palavra-chave de *medida* para resumir resultados. Se a consulta utilizar a palavra-chave de *intervalo,* o eixo x do gráfico utiliza este intervalo de tempo. Se a consulta não incluir a palavra-chave de *intervalo,* o eixo x utiliza intervalos de hora. |
| Navegação por click-through | Ação tomada quando clica no cabeçalho.  Para mais informações, consulte [Definições Comuns](#click-through-navigation). |
| **Gráfico** |**> eixo Y** |
| Utilizar escala logarítmica |Selecione este link para utilizar uma escala logarítmica para o eixo y. |
| Unidades |Especifique as unidades para que os valores sejam devolvidos pela consulta. Esta informação é utilizada para exibir etiquetas de gráficos que indicam os tipos de valor e, opcionalmente, para converter os valores. O tipo *Unidade* especifica a categoria da unidade e define os valores do tipo *Unidade Atual* disponíveis. Se selecionar um valor em *Converter para*, os valores numéricos são convertidos do tipo *Unidade Atual* para o Converter *para* escrever. |
| Etiqueta personalizada |O texto apresentado para o eixo y ao lado da etiqueta para o tipo *Unidade.* Se não for especificada a etiqueta, apenas o tipo *Unidade* é apresentado. |

## <a name="common-settings"></a>Definições comuns
As seguintes secções descrevem definições comuns a várias partes de visualização.

### <a name="name-and-value-separator"></a><a name="name-value-separator"></a>Separador de nome e valor
O separador de nome e valor é o delimitador de caracteres únicos a usar para analisar a propriedade de texto de uma consulta de lista em vários valores. Se especificar um delimitador, pode fornecer nomes para cada campo, separados pelo mesmo delimitador na caixa **nome.**

Por exemplo, considere uma propriedade chamada *Localização* que incluía valores como *Redmond-Building 41* e *Bellevue-Building 12*. Pode especificar um traço (-) para o separador de nome e valor e *construção da cidade* para o nome. Esta abordagem analisa cada valor em duas propriedades chamadas *City* and *Building.*

### <a name="click-through-navigation"></a><a name="click-through-navigation"></a>Navegação clique-através
A navegação por clique define que medidas serão tomadas quando clicar num cabeçalho ou num item de lista numa vista.  Isto abrirá uma consulta no [Log Analytics](../../azure-monitor/log-query/portals.md) ou lançará outra vista.

A tabela seguinte descreve as definições para a navegação por click-through.

| Definição           | Descrição |
|:--|:--|
| Pesquisa de Registo (Auto) | Faça consulta de log para executar quando selecionar um item de cabeçalho.  Esta é a mesma consulta de registo em que o item é baseado.
| Pesquisa de Registos        | Faça log consulta para executar quando selecionar um item numa lista.  Digite a consulta na caixa de **consulta de navegação.**   Utilize *{artigo selecionado}* para incluir a sintaxe para o item que o utilizador selecionou.  Por exemplo, se a consulta tiver uma coluna chamada *Computador* e a consulta de navegação for *{item selecionado}*, uma consulta como *Computer="MyComputer"* é executada quando seleciona um computador. Se a consulta de navegação for *Type=Event {item selecionado}*, a consulta *Type=Event Computer="MyComputer"* é executada. |
| Vista              | Veja a abertura quando selecionar um item de cabeçalho ou um item numa lista.  Selecione o nome de uma vista no seu espaço de trabalho na caixa **'Ver Nome'.** |



### <a name="sparklines"></a><a name="sparklines"></a>Linhas de faíscas
Uma linha de brilho é um pequeno gráfico de linha que ilustra o valor de uma entrada de lista ao longo do tempo. Para peças de visualização com uma lista, pode selecionar se deve apresentar uma barra horizontal, que indica o valor relativo de uma coluna numérica, ou uma linha de brilho, que indica o seu valor ao longo do tempo.

A tabela seguinte descreve as definições para as linhas de faísca:

| Definição | Descrição |
|:--- |:--- |
| Ativar sparklines |Selecione este link para exibir uma linha de faísca em vez de uma barra horizontal. |
| Operação |Se as linhas de faísca estiverem ativadas, esta é a operação a realizar em cada imóvel da lista para calcular os valores para a linha de brilho.<ul><li>Última amostra: O último valor para a série ao longo do intervalo de tempo.</li><li>Max: O valor máximo para a série ao longo do intervalo de tempo.</li><li>Min: O valor mínimo para a série ao longo do intervalo de tempo.</li><li>Soma: A soma dos valores da série ao longo do intervalo de tempo.</li><li>Resumo: Usa o `measure` mesmo comando que a consulta no cabeçalho.</li></ul> |

### <a name="thresholds"></a><a name="thresholds"></a>Limiares
Ao utilizar limiares, pode apresentar um ícone colorido ao lado de cada item numa lista. Os limiares dão-lhe um indicador visual rápido de itens que excedem um valor específico ou que se enquadram numa determinada gama. Por exemplo, pode apresentar um ícone verde para itens com um valor aceitável, amarelo se o valor estiver dentro de um intervalo que indique um aviso, e vermelho se exceder um valor de erro.

Quando ativa limiares para uma parte, deve especificar um ou mais limiares. Se o valor de um item for superior a um valor limiar e inferior ao valor limiar seguinte, a cor para esse valor é utilizada. Se o item for maior do que o valor limiar mais elevado, outra cor é usada. 

Cada limiar definido tem um limiar com um valor de **Padrão**. Esta é a cor que é definida se nenhum outro valor for ultrapassado. Pode adicionar ou remover limiares selecionando o botão **Adicionar** (+) ou **Eliminar** (x).

O quadro seguinte descreve as definições para os limiares:

| Definição | Descrição |
|:--- |:--- |
| Limiares de ativação |Selecione este link para exibir um ícone de cor à esquerda de cada valor. O ícone indica a saúde do valor em relação aos limiares especificados. |
| Nome |O nome do valor limiar. |
| Limiar |O valor para o limiar. A cor de saúde para cada item da lista é definida para a cor do valor limiar mais elevado que é ultrapassado pelo valor do item. Se não forem ultrapassados os valores-limiar, é utilizada uma cor predefinida. |
| Cor |A cor que indica o valor limiar. |

## <a name="next-steps"></a>Passos seguintes
* Aprenda sobre consultas de [log](../log-query/log-query-overview.md) para suportar as consultas em peças de visualização.
