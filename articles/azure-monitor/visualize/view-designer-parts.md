---
title: Um guia de referência para as peças do View Designer no Azure Monitor | Microsoft Docs
description: Ao utilizar o View Designer no Azure Monitor, pode criar vistas personalizadas que são exibidas no portal Azure e conter uma variedade de visualizações de dados no espaço de trabalho Log Analytics. Este artigo é um guia de referência para as definições das peças de visualização que estão disponíveis nas suas vistas personalizadas.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2018
ms.openlocfilehash: 6e971db01322fc031dd0fa8abe82f76a5b45d256
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102045263"
---
# <a name="reference-guide-to-view-designer-visualization-parts-in-azure-monitor"></a>Guia de referência para ver peças de visualização do designer no Azure Monitor
Ao utilizar o View Designer no Azure Monitor, pode criar uma variedade de visualizações personalizadas no portal Azure que podem ajudá-lo a visualizar dados no seu espaço de trabalho Log Analytics. Este artigo é um guia de referência para as definições das peças de visualização que estão disponíveis nas suas vistas personalizadas.

Para mais informações sobre o View Designer, consulte:

* [Ver Designer](view-designer.md): Fornece uma visão geral do View Designer e procedimentos para criar e editar vistas personalizadas.
* [Referência de azulejos](view-designer-tiles.md): Fornece uma referência às definições de cada azulejo disponível nas suas vistas personalizadas.


Os tipos de azulejos disponíveis do View Designer são descritos na tabela seguinte:

| Tipo de visualização | Description |
|:--- |:--- |
| [Lista de consultas](#list-of-queries-part) |Apresenta uma lista de consultas de registo. Pode selecionar cada consulta para mostrar os seus resultados. |
| [Número e lista](#number-and-list-part) |O cabeçalho apresenta um único número que mostra uma contagem de registos de uma consulta de registo. A lista apresenta os dez primeiros resultados de uma consulta, com um gráfico que indica o valor relativo de uma coluna numérica ou a sua alteração ao longo do tempo. |
| [Dois números e lista](#two-numbers-and-list-part) |O cabeçalho apresenta dois números que mostram registos de consultas de registo separadas. A lista apresenta os dez primeiros resultados de uma consulta, com um gráfico que indica o valor relativo de uma coluna numérica ou a sua alteração ao longo do tempo. |
| [Donut e lista](#donut-and-list-part) |O cabeçalho apresenta um único número que resume uma coluna de valor numa consulta de registo. O donut apresenta graficamente os resultados dos três melhores registos. |
| [Duas cronologias e lista](#two-timelines-and-list-part) |O cabeçalho apresenta os resultados de duas consultas de registo ao longo do tempo como gráficos de colunas, com uma chamada que apresenta um único número que resume uma coluna de valor numa consulta de registo. A lista apresenta os dez primeiros resultados de uma consulta, com um gráfico que indica o valor relativo de uma coluna numérica ou a sua alteração ao longo do tempo. |
| [Informações](#information-part) |O cabeçalho apresenta texto estático e um link opcional. A lista apresenta um ou mais itens com um título e texto estáticos. |
| [Gráfico de linha, chamada e lista](#line-chart-callout-and-list-part) |O cabeçalho apresenta um gráfico de linha, com várias séries de uma consulta de registo ao longo do tempo e uma chamada com um valor resumido. A lista apresenta os dez primeiros resultados de uma consulta, com um gráfico que indica o valor relativo de uma coluna numérica ou a sua alteração ao longo do tempo. |
| [Gráfico de linha e lista](#line-chart-and-list-part) |O cabeçalho apresenta um gráfico de linha, com várias séries a partir de uma consulta de log ao longo do tempo. A lista apresenta os dez primeiros resultados de uma consulta, com um gráfico que indica o valor relativo de uma coluna numérica ou a sua alteração ao longo do tempo. |
| [Pilha de fichas de linha parte](#stack-of-line-charts-part) |Apresenta três gráficos de linha separados, com várias séries de uma consulta de registo ao longo do tempo. |

As secções seguintes descrevem os tipos de azulejos e as suas propriedades em detalhe.

> [!NOTE]
> As partes nas vistas baseiam-se em [consultas de registo](../logs/log-query-overview.md) no seu espaço de trabalho Log Analytics. Atualmente, não suportam [consultas de recursos cruzados](../logs/cross-workspace-query.md) para obter dados da Application Insights.

## <a name="list-of-queries-part"></a>Parte da parte das consultas
A lista de consultas por parte apresenta uma lista de consultas de registo. Pode selecionar cada consulta para mostrar os seus resultados. A vista inclui uma única consulta por predefinição, e pode selecionar **+ Consulta** para adicionar consultas adicionais.

![Screenshot da lista de consultas de visualização parte no Azure Monitor View Designer.](media/view-designer-parts/view-list-queries.png)

| Definições | Descrição |
|:--- |:--- |
| **Geral** | |
| Título |O texto que é exibido na parte superior da vista. |
| Novo Grupo |Selecione este link para criar um novo grupo na vista, começando pela vista atual. |
| Filtros pré-selecionados |Uma lista de propriedades delimitadas em vírgula para incluir no painel de filtro esquerdo quando selecionar uma consulta. |
| Modo de renderização |A vista inicial que é exibida quando a consulta é selecionada. Pode selecionar quaisquer visualizações disponíveis depois de abrirem a consulta. |
| **Consultas** | |
| Consulta de pesquisa |A consulta a correr. |
| Nome amigável | O nome descritivo que é mostrado. |

## <a name="number-and-list-part"></a>Número e peça de lista
O cabeçalho apresenta um único número que mostra uma contagem de registos de uma consulta de registo. A lista apresenta os dez primeiros resultados de uma consulta, com um gráfico que indica o valor relativo de uma coluna numérica ou a sua alteração ao longo do tempo.

![Screenshot do número e listar peças de visualização no Azure Monitor View Designer.](media/view-designer-parts/view-number-list.png)

| Definições | Descrição |
|:--- |:--- |
| **Geral** | |
| Título de grupo |O texto que é exibido na parte superior da vista. |
| Novo Grupo |Selecione este link para criar um novo grupo na vista, começando pela vista atual. |
| Ícone |O ficheiro de imagem que é apresentado ao lado do resultado no cabeçalho. |
| Use ícone |Selecione este link para visualizar o ícone. |
| **Título** | |
| Legenda |O texto que é mostrado na parte superior do cabeçalho. |
| Consulta |A consulta para correr para o cabeçalho. É apresentada a contagem dos registos que são devolvidos pela consulta. |
| Navegação por clique | Ação tomada quando clica no cabeçalho.  Para obter mais informações, consulte [As Definições Comuns](#click-through-navigation). |
| **Lista** | |
| Consulta |A consulta para concorrer à lista. As duas primeiras propriedades para os primeiros dez registos dos resultados são apresentadas. A primeira propriedade é um valor de texto, e a segunda propriedade é um valor numérico. As barras são criadas automaticamente com base no valor relativo da coluna numérica.<br><br>Use o `Sort` comando na consulta para classificar os registos da lista. Para executar a consulta e devolver todos os registos, pode selecionar **Ver tudo.** |
| Ocultar gráfico |Selecione este link para desativar o gráfico à direita da coluna numérica. |
| Ativar linhas de faísca |Selecione este link para mostrar uma linha de faísca em vez de uma barra horizontal. Para obter mais informações, consulte [As Definições Comuns](#sparklines). |
| Cor |A cor das barras ou das linhas de faísca. |
| Separador de nome e valor |O delimiter de caracteres únicos para usar para analisar a propriedade do texto em múltiplos valores. Para obter mais informações, consulte [As Definições Comuns](#sparklines). |
| Navegação por clique | Ação tomada quando clica num item da lista.  Para obter mais informações, consulte [As Definições Comuns](#click-through-navigation). |
| **Lista** |**Títulos da Coluna>** |
| Name |O texto que é exibido no topo da primeira coluna. |
| Valor |O texto que é exibido no topo da segunda coluna. |
| **Lista** |**Limiares de>** |
| Ativar limiares |Selecione este link para ativar os limiares. Para obter mais informações, consulte [As Definições Comuns](#thresholds). |

## <a name="two-numbers-and-list-part"></a>Dois números e parte da lista
O cabeçalho tem dois números que mostram uma contagem de registos de consultas de registo separadas. A lista apresenta os dez primeiros resultados de uma consulta, com um gráfico que indica o valor relativo de uma coluna numérica ou a sua alteração ao longo do tempo.

![Dois números & vista da lista](media/view-designer-parts/view-two-numbers-list.png)

| Definições | Descrição |
|:--- |:--- |
| **Geral** | |
| Título de grupo |O texto que é exibido na parte superior da vista. |
| Novo Grupo |Selecione este link para criar um novo grupo na vista, começando pela vista atual. |
| Ícone |O ficheiro de imagem que é apresentado ao lado do resultado no cabeçalho. |
| Use ícone |Selecione este link para visualizar o ícone. |
| **Navegação de Títulos** | |
| Navegação por clique | Ação tomada quando clica no cabeçalho.  Para obter mais informações, consulte [As Definições Comuns](#click-through-navigation). |
| **Título** | |
| Legenda |O texto que é mostrado na parte superior do cabeçalho. |
| Consulta |A consulta para correr para o cabeçalho. É apresentada a contagem dos registos que são devolvidos pela consulta. |
| **Lista** | |
| Consulta |A consulta para concorrer à lista. As duas primeiras propriedades para os primeiros dez registos dos resultados são apresentadas. A primeira propriedade é um valor de texto, e a segunda propriedade é um valor numérico. As barras são criadas automaticamente com base no valor relativo da coluna numérica.<br><br>Use o `Sort` comando na consulta para classificar os registos da lista. Para executar a consulta e devolver todos os registos, pode selecionar **Ver tudo.** |
| Ocultar gráfico |Selecione este link para desativar o gráfico à direita da coluna numérica. |
| Ativar linhas de faísca |Selecione este link para mostrar uma linha de faísca em vez de uma barra horizontal. Para obter mais informações, consulte [As Definições Comuns](#sparklines). |
| Cor |A cor das barras ou das linhas de faísca. |
| Operação |A operação para executar para a linha de faísca. Para obter mais informações, consulte [As Definições Comuns](#sparklines). |
| Separador de nome e valor |O delimiter de caracteres únicos para usar para analisar a propriedade do texto em múltiplos valores. Para obter mais informações, consulte [As Definições Comuns](#sparklines). |
| Navegação por clique | Ação tomada quando clica num item da lista.  Para obter mais informações, consulte [As Definições Comuns](#click-through-navigation). |
| **Lista** |**Títulos da Coluna>** |
| Name |O texto que é exibido no topo da primeira coluna. |
| Valor |O texto que é exibido no topo da segunda coluna. |
| **Lista** |**Limiares de>** |
| Ativar limiares |Selecione este link para ativar os limiares. Para obter mais informações, consulte [As Definições Comuns](#thresholds). |

## <a name="donut-and-list-part"></a>Donut e parte da lista
O cabeçalho apresenta um único número que resume uma coluna de valor numa consulta de registo. O donut apresenta graficamente os resultados dos três melhores registos.

![Vista de donut e lista](media/view-designer-parts/view-donut-list.png)

| Definições | Descrição |
|:--- |:--- |
| **Geral** | |
| Título de grupo |O texto que é exibido no topo do azulejo. |
| Novo Grupo |Selecione este link para criar um novo grupo na vista, começando pela vista atual. |
| Ícone |O ficheiro de imagem que é apresentado ao lado do resultado no cabeçalho. |
| Use ícone |Selecione este link para visualizar o ícone. |
| **Cabeçalho** | |
| Título |O texto que é mostrado na parte superior do cabeçalho. |
| Subtítulo |O texto que é exibido sob o título no topo do cabeçalho. |
| **Anel** | |
| Consulta |A consulta para correr para o donut. A primeira propriedade é um valor de texto, e a segunda propriedade é um valor numérico. |
| Navegação por clique | Ação tomada quando clica no cabeçalho.  Para obter mais informações, consulte [As Definições Comuns](#click-through-navigation). |
| **Anel** |**Centro>** |
| Texto |O texto que é mostrado sob o valor dentro do donut. |
| Operação |A operação para executar na propriedade de valor para resumi-la como um único valor.<ul><li>Soma: Acrescenta os valores de todos os registos.</li><li>Percentagem: O rácio dos registos devolvidos pelos valores em **Valores de Resultado utilizados na operação central** para o total de registos na consulta.</li></ul> |
| Valores de resultados utilizados na operação central |Opcionalmente, selecione o sinal de mais (+) para adicionar um ou mais valores. Os resultados da consulta estão limitados a registos com os valores de propriedade que especifica. Se não forem adicionados valores, todos os registos estão incluídos na consulta. |
| **Opções adicionais** |**> cores** |
| Cor 1<br>Cor 2<br>Cor 3 |Selecione a cor para cada um dos valores que são apresentados no donut. |
| **Opções adicionais** |**> Mapeamento de cores avançadas** |
| Valor do campo |Digite o nome de um campo para exibi-lo como uma cor diferente se estiver incluído no donut. |
| Cor |Selecione a cor para o campo único. |
| **Lista** | |
| Consulta |A consulta para concorrer à lista. É apresentada a contagem dos registos que são devolvidos pela consulta. |
| Ocultar gráfico |Selecione este link para desativar o gráfico à direita da coluna numérica. |
| Ativar linhas de faísca |Selecione este link para mostrar uma linha de faísca em vez de uma barra horizontal. Para obter mais informações, consulte [As Definições Comuns](#sparklines). |
| Cor |A cor das barras ou das linhas de faísca. |
| Operação |A operação para executar para a linha de faísca. Para obter mais informações, consulte [As Definições Comuns](#sparklines). |
| Separador de nome e valor |O delimiter de caracteres únicos para usar para analisar a propriedade do texto em múltiplos valores. Para obter mais informações, consulte [As Definições Comuns](#sparklines). |
| Navegação por clique | Ação tomada quando clica num item da lista.  Para obter mais informações, consulte [As Definições Comuns](#click-through-navigation). |
| **Lista** |**Títulos da Coluna>** |
| Name |O texto que é exibido no topo da primeira coluna. |
| Valor |O texto que é exibido no topo da segunda coluna. |
| **Lista** |**Limiares de>** |
| Ativar limiares |Selecione este link para ativar os limiares. Para obter mais informações, consulte [As Definições Comuns](#thresholds). |

## <a name="two-timelines-and-list-part"></a>Duas linhas de tempo e parte da lista
O cabeçalho apresenta os resultados de duas consultas de registo ao longo do tempo como gráficos de colunas, com uma chamada que apresenta um único número que resume uma coluna de valor numa consulta de registo. A lista apresenta os dez primeiros resultados de uma consulta, com um gráfico que indica o valor relativo de uma coluna numérica ou a sua alteração ao longo do tempo.

![Duas linhas do tempo e vista de lista](media/view-designer-parts/view-two-timelines-list.png)

| Definições | Descrição |
|:--- |:--- |
| **Geral** | |
| Título de grupo |O texto que é exibido no topo do azulejo. |
| Novo Grupo |Selecione este link para criar um novo grupo na vista, começando pela vista atual. |
| Ícone |O ficheiro de imagem que é apresentado ao lado do resultado no cabeçalho. |
| Use ícone |Selecione este link para visualizar o ícone. |
| **Navegação de Títulos** | |
| Navegação por clique | Ação tomada quando clica no cabeçalho.  Para obter mais informações, consulte [As Definições Comuns](#click-through-navigation). |
| **Primeira tabela <br> Segundo gráfico** | |
| Legenda |O texto que é exibido sob a chamada para a primeira série. |
| Cor |A cor a usar para as colunas da série. |
| Consulta |A consulta para correr para a primeira série. A contagem de registos ao longo de cada intervalo de tempo é representada pelas colunas de gráficos. |
| Operação |A operação para executar na propriedade de valor para resumi-la como um único valor para a chamada.<ul><li>Soma: A soma dos valores de todos os registos.</li><li>Média: A média dos valores de todos os registos.</li><li>Última amostra: o valor do último intervalo incluído na tabela.</li><li>Primeira amostra: o valor do primeiro intervalo incluído na tabela.</li><li>Contagem: A contagem de todos os registos que são devolvidos pela consulta.</li></ul> |
| **Lista** | |
| Consulta |A consulta para concorrer à lista. É apresentada a contagem dos registos que são devolvidos pela consulta. |
| Ocultar gráfico |Selecione este link para desativar o gráfico à direita da coluna numérica. |
| Ativar linhas de faísca |Selecione este link para mostrar uma linha de faísca em vez de uma barra horizontal. Para obter mais informações, consulte [As Definições Comuns](#sparklines). |
| Cor |A cor das barras ou das linhas de faísca. |
| Operação |A operação para executar para a linha de faísca. Para obter mais informações, consulte [As Definições Comuns](#sparklines). |
| Navegação por clique | Ação tomada quando clica num item da lista.  Para obter mais informações, consulte [As Definições Comuns](#click-through-navigation). |
| **Lista** |**Títulos da Coluna>** |
| Name |O texto que é exibido no topo da primeira coluna. |
| Valor |O texto que é exibido no topo da segunda coluna. |
| **Lista** |**Limiares de>** |
| Ativar limiares |Selecione este link para ativar os limiares. Para obter mais informações, consulte [As Definições Comuns](#thresholds). |

## <a name="information-part"></a>Parte da informação
O cabeçalho apresenta texto estático e um link opcional. A lista apresenta um ou mais itens com um título e texto estáticos.

![Vista de informação](media/view-designer-parts/view-information.png)

| Definições | Descrição |
|:--- |:--- |
| **Geral** | |
| Título de grupo |O texto que é exibido no topo do azulejo. |
| Novo Grupo |Selecione este link para criar um novo grupo na vista, começando pela vista atual. |
| Cor |A cor de fundo para o cabeçalho. |
| **Cabeçalho** | |
| Imagem |O ficheiro de imagem que é apresentado no cabeçalho. |
| Etiqueta |O texto que é mostrado no cabeçalho. |
| **Cabeçalho** |**> Link** |
| Etiqueta |O texto do link. |
| Url |A url para a ligação. |
| **Artigos de informação** | |
| Título |O texto que é mostrado para o título de cada item. |
| Conteúdo |O texto que é mostrado para cada item. |

## <a name="line-chart-callout-and-list-part"></a>Gráfico de linha, chamada e parte da lista
O cabeçalho apresenta um gráfico de linha com várias séries a partir de uma consulta de registo ao longo do tempo e uma chamada com um valor resumido. A lista apresenta os dez primeiros resultados de uma consulta, com um gráfico que indica o valor relativo de uma coluna numérica ou a sua alteração ao longo do tempo.

![Gráfico de linha, chamada e vista de lista](media/view-designer-parts/view-line-chart-callout-list.png)

| Definições | Descrição |
|:--- |:--- |
| **Geral** | |
| Título de grupo |O texto que é exibido no topo do azulejo. |
| Novo Grupo |Selecione este link para criar um novo grupo na vista, começando pela vista atual. |
| Ícone |O ficheiro de imagem que é apresentado ao lado do resultado no cabeçalho. |
| Use ícone |Selecione este link para visualizar o ícone. |
| **Cabeçalho** | |
| Título |O texto que é mostrado na parte superior do cabeçalho. |
| Subtítulo |O texto que é exibido sob o título no topo do cabeçalho. |
| **Gráfico de linha** | |
| Consulta |A consulta para correr para o gráfico de linha. A primeira propriedade é um valor de texto, e a segunda propriedade é um valor numérico. Esta consulta normalmente utiliza a palavra-chave da *medida* para resumir os resultados. Se a consulta utilizar a palavra-chave de *intervalo,* o eixo x do gráfico utiliza este intervalo de tempo. Se a consulta não incluir a palavra-chave de *intervalo,* o eixo x utiliza intervalos de hora. |
| Navegação por clique | Ação tomada quando clica no cabeçalho.  Para obter mais informações, consulte [As Definições Comuns](#click-through-navigation). |
| **Gráfico de linha** |**> Callout** |
| Título de chamada |O texto que é apresentado acima do valor de chamada. |
| Nome da série |Valor da propriedade para a série a usar para o valor de chamada. Se não forem fornecidas séries, todos os registos da consulta são utilizados. |
| Operação |A operação para executar na propriedade de valor para resumi-la como um único valor para a chamada.<ul><li>Média: A média dos valores de todos os registos.</li><li>Contagem: A contagem de todos os registos que são devolvidos pela consulta.</li><li>Última amostra: o valor do último intervalo incluído na tabela.</li><li>Max: O valor máximo dos intervalos incluídos na tabela.</li><li>Min: O valor mínimo dos intervalos incluídos na tabela.</li><li>Soma: A soma dos valores de todos os registos.</li></ul> |
| **Gráfico de linha** |**> eixo Y** |
| Use a escala logarítmica |Selecione este link para utilizar uma escala logarítmica para o eixo y. |
| Unidades |Especifique as unidades para que os valores sejam devolvidos pela consulta. Estas informações são utilizadas para exibir etiquetas de gráficos que indicam os tipos de valor e, opcionalmente, para converter os valores. O tipo *de Unidade* especifica a categoria da unidade e define os valores de tipo Unidade *De Corrente* disponíveis. Se selecionar um valor em *Converter para,* os valores numéricos são convertidos do tipo *Unidade Atual* para o *Converse para* o tipo. |
| Etiqueta personalizada |O texto que é apresentado para o eixo y ao lado da etiqueta para o tipo *Unidade.* Se não for especificada nenhuma etiqueta, apenas é apresentado o tipo *Unidade.* |
| **Lista** | |
| Consulta |A consulta para concorrer à lista. É apresentada a contagem dos registos que são devolvidos pela consulta. |
| Ocultar gráfico |Selecione este link para desativar o gráfico à direita da coluna numérica. |
| Ativar linhas de faísca |Selecione este link para mostrar uma linha de faísca em vez de uma barra horizontal. Para obter mais informações, consulte [As Definições Comuns](#sparklines). |
| Cor |A cor das barras ou das linhas de faísca. |
| Operação |A operação para executar para a linha de faísca. Para obter mais informações, consulte [As Definições Comuns](#sparklines). |
| Separador de nome e valor |O delimiter de caracteres únicos para usar para analisar a propriedade do texto em múltiplos valores. Para obter mais informações, consulte [As Definições Comuns](#sparklines). |
| Navegação por clique | Ação tomada quando clica num item da lista.  Para obter mais informações, consulte [As Definições Comuns](#click-through-navigation). |
| **Lista** |**Títulos da Coluna>** |
| Name |O texto que é exibido no topo da primeira coluna. |
| Valor |O texto que é exibido no topo da segunda coluna. |
| **Lista** |**Limiares de>** |
| Ativar limiares |Selecione este link para ativar os limiares. Para obter mais informações, consulte [As Definições Comuns](#thresholds). |

## <a name="line-chart-and-list-part"></a>Gráfico de linha e parte da lista
O cabeçalho apresenta um gráfico de linha com várias séries a partir de uma consulta de registo ao longo do tempo. A lista apresenta os dez primeiros resultados de uma consulta, com um gráfico que indica o valor relativo de uma coluna numérica ou a sua alteração ao longo do tempo.

![Gráfico de linha e vista de lista](media/view-designer-parts/view-line-chart-callout-list.png)

| Definições | Descrição |
|:--- |:--- |
| **Geral** | |
| Título de grupo |O texto que é exibido no topo do azulejo. |
| Novo Grupo |Selecione este link para criar um novo grupo na vista, começando pela vista atual. |
| Ícone |O ficheiro de imagem que é apresentado ao lado do resultado no cabeçalho. |
| Use ícone |Selecione este link para visualizar o ícone. |
| **Cabeçalho** | |
| Título |O texto que é mostrado na parte superior do cabeçalho. |
| Subtítulo |O texto que é exibido sob o título no topo do cabeçalho. |
| **Gráfico de linha** | |
| Consulta |A consulta para correr para o gráfico de linha. A primeira propriedade é um valor de texto, e a segunda propriedade é um valor numérico. Esta consulta normalmente utiliza a palavra-chave da *medida* para resumir os resultados. Se a consulta utilizar a palavra-chave de *intervalo,* o eixo x do gráfico utiliza este intervalo de tempo. Se a consulta não incluir a palavra-chave de *intervalo,* o eixo x utiliza intervalos de hora. |
| Navegação por clique | Ação tomada quando clica no cabeçalho.  Para obter mais informações, consulte [As Definições Comuns](#click-through-navigation). |
| **Gráfico de linha** |**> eixo Y** |
| Use a escala logarítmica |Selecione este link para utilizar uma escala logarítmica para o eixo y. |
| Unidades |Especifique as unidades para que os valores sejam devolvidos pela consulta. Estas informações são utilizadas para exibir etiquetas de gráficos que indicam os tipos de valor e, opcionalmente, para converter os valores. O tipo *de Unidade* especifica a categoria da unidade e define os valores de tipo Unidade *De Corrente* disponíveis. Se selecionar um valor em *Converter para,* os valores numéricos são convertidos do tipo *Unidade Atual* para o *Converse para* o tipo. |
| Etiqueta personalizada |O texto que é apresentado para o eixo y ao lado da etiqueta para o tipo *Unidade.* Se não for especificada nenhuma etiqueta, apenas é apresentado o tipo *Unidade.* |
| **Lista** | |
| Consulta |A consulta para concorrer à lista. É apresentada a contagem dos registos que são devolvidos pela consulta. |
| Ocultar gráfico |Selecione este link para desativar o gráfico à direita da coluna numérica. |
| Ativar linhas de faísca |Selecione este link para mostrar uma linha de faísca em vez de uma barra horizontal. Para obter mais informações, consulte [As Definições Comuns](#sparklines). |
| Cor |A cor das barras ou das linhas de faísca. |
| Operação |A operação para executar para a linha de faísca. Para obter mais informações, consulte [As Definições Comuns](#sparklines). |
| Separador de nome e valor |O delimiter de caracteres únicos para usar para analisar a propriedade do texto em múltiplos valores. Para obter mais informações, consulte [As Definições Comuns](#sparklines). |
| Navegação por clique | Ação tomada quando clica num item da lista.  Para obter mais informações, consulte [As Definições Comuns](#click-through-navigation). |
| **Lista** |**Títulos da Coluna>** |
| Name |O texto que é exibido no topo da primeira coluna. |
| Valor |O texto que é exibido no topo da segunda coluna. |
| **Lista** |**Limiares de>** |
| Ativar limiares |Selecione este link para ativar os limiares. Para obter mais informações, consulte [As Definições Comuns](#thresholds). |

## <a name="stack-of-line-charts-part"></a>Pilha de fichas de linha parte
A pilha de gráficos de linha apresenta três gráficos de linha separados, com várias séries de uma consulta de log ao longo do tempo, como mostrado aqui:

![Pilha de gráficos de linha](media/view-designer-parts/view-stack-line-charts.png)

| Definições | Descrição |
|:--- |:--- |
| **Geral** | |
| Título de grupo |O texto que é exibido no topo do azulejo. |
| Novo Grupo |Selecione este link para criar um novo grupo na vista, começando pela vista atual. |
| Ícone |O ficheiro de imagem que é apresentado ao lado do resultado no cabeçalho. |
| **Gráfico 1 <br> Gráfico 2 <br> Gráfico 3** |**Cabeçalho>** |
| Título |O texto que é exibido no topo da tabela. |
| Subtítulo |O texto que é exibido sob o título no topo da tabela. |
| **Gráfico 1 <br> Gráfico 2 <br> Gráfico 3** |**Gráfico de linha** |
| Consulta |A consulta para correr para o gráfico de linha. A primeira propriedade é um valor de texto, e a segunda propriedade é um valor numérico. Esta consulta normalmente utiliza a palavra-chave da *medida* para resumir os resultados. Se a consulta utilizar a palavra-chave de *intervalo,* o eixo x do gráfico utiliza este intervalo de tempo. Se a consulta não incluir a palavra-chave de *intervalo,* o eixo x utiliza intervalos de hora. |
| Navegação por clique | Ação tomada quando clica no cabeçalho.  Para obter mais informações, consulte [As Definições Comuns](#click-through-navigation). |
| **Gráfico** |**> eixo Y** |
| Use a escala logarítmica |Selecione este link para utilizar uma escala logarítmica para o eixo y. |
| Unidades |Especifique as unidades para que os valores sejam devolvidos pela consulta. Estas informações são utilizadas para exibir etiquetas de gráficos que indicam os tipos de valor e, opcionalmente, para converter os valores. O tipo *de Unidade* especifica a categoria da unidade e define os valores de tipo Unidade *De Corrente* disponíveis. Se selecionar um valor em *Converter para,* os valores numéricos são convertidos do tipo *Unidade Atual* para o *Converse para* o tipo. |
| Etiqueta personalizada |O texto que é apresentado para o eixo y ao lado da etiqueta para o tipo *Unidade.* Se não for especificada nenhuma etiqueta, apenas é apresentado o tipo *Unidade.* |

## <a name="common-settings"></a>Configurações comuns
As secções seguintes descrevem definições comuns a várias partes de visualização.

### <a name="name-and-value-separator"></a><a name="name-value-separator"></a>Separador de nome e valor
O separador de nome e valor é o delimiter de caracteres únicos para usar para analisar a propriedade de texto de uma consulta de lista em vários valores. Se especificar um delimiter, pode fornecer nomes para cada campo, separados pelo mesmo delimitador na caixa **Nome.**

Por exemplo, considere uma propriedade chamada *Localização* que incluiu valores como *Redmond-Building 41* e *Bellevue-Building 12*. Pode especificar um traço (-) para o nome e separador de valor e *city-building* para o nome. Esta abordagem analisa cada valor em duas propriedades chamadas *Cidade* e *Edifício.*

### <a name="click-through-navigation"></a><a name="click-through-navigation"></a>Navegação click-through
A navegação por clique define que ação será tomada quando clicar num cabeçalho ou listar um item numa vista.  Isto abrirá uma consulta no [Log Analytics](../logs/log-query-overview.md) ou lançará outra vista.

A tabela seguinte descreve as definições para a navegação por clique.

| Definições           | Descrição |
|:--|:--|
| Pesquisa de registo (Auto) | Faça a consulta de registo a executar quando selecionar um item de cabeçalho.  Esta é a mesma consulta de registo em que o item é baseado.
| Pesquisa de Registos        | Faça a consulta de registo a ser executada quando selecionar um item numa lista.  Digite a consulta na caixa **de consulta de navegação.**   Utilize *{item selecionado}* para incluir a sintaxe para o item que o utilizador selecionou.  Por exemplo, se a consulta tiver uma coluna chamada *Computador* e a consulta de navegação for *{item selecionado}*, uma consulta como *Computer="MyComputer"* é executada quando seleciona um computador. Se a consulta de navegação for *Type=Event {item selecionado}*, é executada a consulta *Type=Event Computer="MyComputer".* |
| Vista              | Ver para abrir quando selecionar um item do cabeçalho ou um item numa lista.  Selecione o nome de uma vista no seu espaço de trabalho na caixa **'Ver Nome'.** |



### <a name="sparklines"></a><a name="sparklines"></a>Linhas de faísca
Uma linha de faísca é um pequeno gráfico de linha que ilustra o valor de uma entrada de lista ao longo do tempo. Para visualizar peças com uma lista, pode selecionar se deve apresentar uma barra horizontal, que indica o valor relativo de uma coluna numérica, ou uma linha de faísca, que indica o seu valor ao longo do tempo.

A tabela a seguir descreve as definições para as linhas de faísca:

| Definições | Descrição |
|:--- |:--- |
| Ativar linhas de faísca |Selecione este link para mostrar uma linha de faísca em vez de uma barra horizontal. |
| Operação |Se as linhas de faísca estiverem ativadas, esta é a operação a executar em cada propriedade da lista para calcular os valores para a linha de faísca.<ul><li>Última amostra: O último valor da série durante o intervalo de tempo.</li><li>Max: O valor máximo da série durante o intervalo de tempo.</li><li>Min: O valor mínimo para a série durante o intervalo de tempo.</li><li>Soma: A soma dos valores da série ao longo do intervalo de tempo.</li><li>Resumo: Utiliza o mesmo `measure` comando que a consulta no cabeçalho.</li></ul> |

### <a name="thresholds"></a><a name="thresholds"></a>Limiares
Ao utilizar limiares, pode exibir um ícone colorido ao lado de cada item numa lista. Os limiares dão-lhe um indicador visual rápido de itens que excedam um determinado valor ou se enquadram num determinado intervalo. Por exemplo, pode apresentar um ícone verde para itens com um valor aceitável, amarelo se o valor estiver dentro de um intervalo que indique um aviso, e vermelho se exceder um valor de erro.

Quando ativa limiares para uma parte, deve especificar um ou mais limiares. Se o valor de um item for maior do que um valor limiar e inferior ao valor limiar seguinte, a cor desse valor é utilizada. Se o item for maior do que o valor limiar mais elevado, é utilizada outra cor. 

Cada conjunto de limiares tem um limiar com um valor de **Padrão**. Esta é a cor que é definida se nenhum outro valor for ultrapassado. Pode adicionar ou remover limiares selecionando o botão **Adicionar** (+) ou **Eliminar** (x).

O quadro que se segue descreve as definições dos limiares:

| Definições | Descrição |
|:--- |:--- |
| Ativar limiares |Selecione este link para exibir um ícone de cor à esquerda de cada valor. O ícone indica a saúde do valor em relação aos limiares especificados. |
| Name |O nome do valor limiar. |
| Limite |O valor para o limiar. A cor de saúde de cada item da lista é definida para a cor do valor limiar mais elevado que é ultrapassado pelo valor do item. Se não forem ultrapassados os valores limiares, é utilizada uma cor padrão. |
| Cor |A cor que indica o valor limiar. |

## <a name="next-steps"></a>Passos seguintes
* Saiba mais [sobre consultas de registo](../logs/log-query-overview.md) para suportar as consultas em peças de visualização.