---
title: Um guia de referência para as partes do estruturador de vistas no Azure Monitor | Documentos da Microsoft
description: Ao utilizar o estruturador de vistas no Azure Monitor, pode criar vistas personalizadas que são apresentadas no portal do Azure e contêm uma variedade de visualizações nos dados na área de trabalho do Log Analytics. Este artigo é um guia de referência para as definições para as partes de visualização que estão disponíveis em suas vistas personalizadas.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: 5718d620-b96e-4d33-8616-e127ee9379c4
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: bwren
ms.openlocfilehash: 53323e70884e61b4643f7950a1a6333f08dbbb6f
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56889907"
---
# <a name="reference-guide-to-view-designer-visualization-parts-in-azure-monitor"></a>Guia de referência a partes de visualização do estruturador de vistas no Azure Monitor
Ao utilizar o estruturador de vistas no Azure Monitor, pode criar diversas exibições personalizadas no portal do Azure pode ajudá-lo a visualizar os dados na sua área de trabalho do Log Analytics. Este artigo é um guia de referência para as definições para as partes de visualização que estão disponíveis em suas vistas personalizadas.

Para obter mais informações sobre o estruturador de vistas, consulte:

* [Exibir Designer](view-designer.md): Fornece uma visão geral do estruturador de vistas e procedimentos para criação e edição de exibições personalizadas.
* [Referência de mosaico](view-designer-tiles.md): Fornece uma referência para as definições para cada mosaico disponível nas suas vistas personalizadas.


Os tipos de mosaico do estruturador de vistas disponíveis são descritos na tabela a seguir:

| Tipo de vista | Descrição |
|:--- |:--- |
| [Lista de consultas](#list-of-queries-part) |Apresenta uma lista de consultas de registo. Pode selecionar cada consulta para exibir os resultados. |
| [Número e lista](#number-and-list-part) |O cabeçalho apresenta um único número, que mostra uma contagem de registos de uma consulta de registo. A lista apresenta os resultados de dez principais de uma consulta, com um gráfico que indica o valor relativo de uma coluna numérica ou a alteração ao longo do tempo. |
| [Dois números e lista](#two-numbers-and-list-part) |O cabeçalho apresenta dois números que mostram as contagens dos registos de consultas de registo separado. A lista apresenta os resultados de dez principais de uma consulta, com um gráfico que indica o valor relativo de uma coluna numérica ou a alteração ao longo do tempo. |
| [Anel e lista](#donut-and-list-part) |O cabeçalho apresenta um único número que resume uma coluna de valor numa consulta de registo. Anel apresenta graficamente os resultados dos registos de três principais. |
| [Duas linhas cronológicas e lista](#two-timelines-and-list-part) |O cabeçalho apresenta os resultados de duas consultas de registo ao longo do tempo, como gráficos de colunas, com uma nota de aviso que exibe um único número que resume uma coluna de valor numa consulta de registo. A lista apresenta os resultados de dez principais de uma consulta, com um gráfico que indica o valor relativo de uma coluna numérica ou a alteração ao longo do tempo. |
| [Informações](#information-part) |O cabeçalho exibe texto estático e uma ligação opcional. A lista apresenta um ou mais itens com um título estático e o texto. |
| [Gráfico de linhas, nota de aviso e lista](#line-chart-callout-and-list-part) |O cabeçalho apresenta um gráfico de linhas, com várias séries de uma consulta de registo ao longo do tempo e uma nota de aviso com um valor resumido. A lista apresenta os resultados de dez principais de uma consulta, com um gráfico que indica o valor relativo de uma coluna numérica ou a alteração ao longo do tempo. |
| [Gráfico de linhas e lista](#line-chart-and-list-part) |O cabeçalho apresenta um gráfico de linhas, com várias séries de uma consulta de registo ao longo do tempo. A lista apresenta os resultados de dez principais de uma consulta, com um gráfico que indica o valor relativo de uma coluna numérica ou a alteração ao longo do tempo. |
| [Pilha de parte de gráficos de linha](#stack-of-line-charts-part) |Apresenta três gráficos de linha separado, com várias séries de uma consulta de registo ao longo do tempo. |

As secções seguintes descrevem os tipos de mosaico e as respetivas propriedades em detalhes.

## <a name="list-of-queries-part"></a>Lista de parte de consultas
A lista de parte de consultas apresenta uma lista de consultas de registo. Pode selecionar cada consulta para exibir os resultados. A vista inclui uma única consulta, por predefinição e pode selecionar **+ consulta** adicionar consultas adicionais.

![Lista de vista de consultas](media/view-designer-parts/view-list-queries.png)

| Definição | Descrição |
|:--- |:--- |
| **Geral** | |
| Título |O texto que é apresentado na parte superior da vista. |
| Novo Grupo |Selecione esta ligação para criar um novo grupo na exibição, começando a exibição atual. |
| Filtros pré-selecionados |Uma lista delimitada por vírgulas de propriedades para incluir no painel de filtro à esquerda, quando seleciona uma consulta. |
| Modo de composição |A vista inicial que é apresentada quando a consulta estiver selecionada. Pode selecionar todas as vistas disponíveis depois de abrir a consulta. |
| **Consultas** | |
| Consulta de pesquisa |A consulta seja executada. |
| Nome amigável | O nome descritivo, que é apresentado. |

## <a name="number-and-list-part"></a>Parte do número e lista
O cabeçalho apresenta um único número, que mostra uma contagem de registos de uma consulta de registo. A lista apresenta os resultados de dez principais de uma consulta, com um gráfico que indica o valor relativo de uma coluna numérica ou a alteração ao longo do tempo.

![Lista de vista de consultas](media/view-designer-parts/view-number-list.png)

| Definição | Descrição |
|:--- |:--- |
| **Geral** | |
| Título do grupo |O texto que é apresentado na parte superior da vista. |
| Novo Grupo |Selecione esta ligação para criar um novo grupo na exibição, começando a exibição atual. |
| Ícone |O ficheiro de imagem que é apresentado junto ao resultado no cabeçalho. |
| Utilizar Ícone |Selecione esta ligação para ver o ícone. |
| **Title** (Título) | |
| Legenda |O texto que é apresentado na parte superior do cabeçalho. |
| Consulta |A consulta seja executada para o cabeçalho. É apresentada a contagem dos registos que são devolvidos pela consulta. |
| Navegação por clique | Ação tomada quando clicar no cabeçalho.  Para obter mais informações, consulte [configurações comuns](#click-through-navigation). |
| **Lista** | |
| Consulta |A consulta seja executada para obter a lista. As duas primeiras propriedades os dez primeiros registos os resultados são apresentadas. A primeira propriedade é um valor de texto e a segunda propriedade é um valor numérico. Barras são criadas automaticamente que baseiam-se do valor relativo da coluna numérica.<br><br>Utilize o `Sort` comando na consulta para ordenar os registos na lista. Para executar a consulta e retornar todos os registos, pode selecionar **ver todas as**. |
| Ocultar gráfico |Selecione esta ligação para desativar o gráfico à direita da coluna numérica. |
| Ativar gráficos sparkline |Selecione esta ligação para ver um gráfico sparkline em vez de uma barra horizontal. Para obter mais informações, consulte [configurações comuns](#sparklines). |
| Cor |A cor de barras ou minigráficos. |
| Separador de nome e valor |O delimitador de caráter a utilizar para analisar a propriedade de texto em vários valores. Para obter mais informações, consulte [configurações comuns](#sparklines). |
| Navegação por clique | Ação efetuada quando clica num item na lista.  Para obter mais informações, consulte [configurações comuns](#click-through-navigation). |
| **Lista** |**> Títulos de coluna** |
| Name |O texto que é apresentado na parte superior da primeira coluna. |
| Value |O texto que é apresentado na parte superior da segunda coluna. |
| **Lista** |**> Limiares de** |
| Ativar Limiares |Selecione esta ligação para ativar limiares. Para obter mais informações, consulte [configurações comuns](#thresholds). |

## <a name="two-numbers-and-list-part"></a>Dois números e parte da lista
O cabeçalho tem dois números que mostra uma contagem de registos de consultas de registo separado. A lista apresenta os resultados de dez principais de uma consulta, com um gráfico que indica o valor relativo de uma coluna numérica ou a alteração ao longo do tempo.

![Ver os dois números & lista](media/view-designer-parts/view-two-numbers-list.png)

| Definição | Descrição |
|:--- |:--- |
| **Geral** | |
| Título do grupo |O texto que é apresentado na parte superior da vista. |
| Novo Grupo |Selecione esta ligação para criar um novo grupo na exibição, começando a exibição atual. |
| Ícone |O ficheiro de imagem que é apresentado junto ao resultado no cabeçalho. |
| Utilizar Ícone |Selecione esta ligação para ver o ícone. |
| **Navegação de título** | |
| Navegação por clique | Ação tomada quando clicar no cabeçalho.  Para obter mais informações, consulte [configurações comuns](#click-through-navigation). |
| **Title** (Título) | |
| Legenda |O texto que é apresentado na parte superior do cabeçalho. |
| Consulta |A consulta seja executada para o cabeçalho. É apresentada a contagem dos registos que são devolvidos pela consulta. |
| **Lista** | |
| Consulta |A consulta seja executada para obter a lista. As duas primeiras propriedades os dez primeiros registos os resultados são apresentadas. A primeira propriedade é um valor de texto e a segunda propriedade é um valor numérico. Barras são criadas automaticamente com base no valor relativo da coluna numérica.<br><br>Utilize o `Sort` comando na consulta para ordenar os registos na lista. Para executar a consulta e retornar todos os registos, pode selecionar **ver todas as**. |
| Ocultar gráfico |Selecione esta ligação para desativar o gráfico à direita da coluna numérica. |
| Ativar gráficos sparkline |Selecione esta ligação para ver um gráfico sparkline em vez de uma barra horizontal. Para obter mais informações, consulte [configurações comuns](#sparklines). |
| Cor |A cor de barras ou minigráficos. |
| Operação |A operação a efetuar para o gráfico sparkline. Para obter mais informações, consulte [configurações comuns](#sparklines). |
| Separador de nome e valor |O delimitador de caráter a utilizar para analisar a propriedade de texto em vários valores. Para obter mais informações, consulte [configurações comuns](#sparklines). |
| Navegação por clique | Ação efetuada quando clica num item na lista.  Para obter mais informações, consulte [configurações comuns](#click-through-navigation). |
| **Lista** |**> Títulos de coluna** |
| Name |O texto que é apresentado na parte superior da primeira coluna. |
| Value |O texto que é apresentado na parte superior da segunda coluna. |
| **Lista** |**> Limiares de** |
| Ativar Limiares |Selecione esta ligação para ativar limiares. Para obter mais informações, consulte [configurações comuns](#thresholds). |

## <a name="donut-and-list-part"></a>Parte do anel e lista
O cabeçalho apresenta um único número que resume uma coluna de valor numa consulta de registo. Anel apresenta graficamente os resultados dos registos de três principais.

![Vista de anel e lista](media/view-designer-parts/view-donut-list.png)

| Definição | Descrição |
|:--- |:--- |
| **Geral** | |
| Título do grupo |O texto que é apresentado na parte superior do mosaico. |
| Novo Grupo |Selecione esta ligação para criar um novo grupo na exibição, começando a exibição atual. |
| Ícone |O ficheiro de imagem que é apresentado junto ao resultado no cabeçalho. |
| Utilizar Ícone |Selecione esta ligação para ver o ícone. |
| **Cabeçalho** | |
| Título |O texto que é apresentado na parte superior do cabeçalho. |
| Subtítulo |O texto que é apresentado sob o título na parte superior do cabeçalho. |
| **Anel** | |
| Consulta |A consulta seja executada para o anel. A primeira propriedade é um valor de texto e a segunda propriedade é um valor numérico. |
| Navegação por clique | Ação tomada quando clicar no cabeçalho.  Para obter mais informações, consulte [configurações comuns](#click-through-navigation). |
| **Anel** |**> Center** |
| Texto |O texto que é apresentado no valor dentro do anel. |
| Operação |A operação para executar na propriedade de valor para resumi-lo como um único valor.<ul><li>Soma: Adiciona os valores de todos os registos.</li><li>Percentagem de: A proporção de registros retornado pelos valores na **resultar valores utilizados na operação do centro** para o total de registos na consulta.</li></ul> |
| Valores dos resultados utilizados na operação do centro |Opcionalmente, selecione o sinal de adição (+) para adicionar um ou mais valores. Os resultados da consulta são limitados aos registros com os valores de propriedade que especificar. Se não os valores são adicionados, todos os registos estão incluídos na consulta. |
| **Opções adicionais** |**> Cores** |
| Cor 1<br>Cor 2<br>Cor 3 |Selecione a cor para cada um dos valores que são apresentados no anel. |
| **Opções adicionais** |**> Mapeamento de cores avançadas** |
| Valor do campo |Escreva o nome de um campo para exibi-lo como uma cor diferente, se ele está incluído no anel. |
| Cor |Selecione a cor para o campo exclusivo. |
| **Lista** | |
| Consulta |A consulta seja executada para obter a lista. É apresentada a contagem dos registos que são devolvidos pela consulta. |
| Ocultar gráfico |Selecione esta ligação para desativar o gráfico à direita da coluna numérica. |
| Ativar gráficos sparkline |Selecione esta ligação para ver um gráfico sparkline em vez de uma barra horizontal. Para obter mais informações, consulte [configurações comuns](#sparklines). |
| Cor |A cor de barras ou minigráficos. |
| Operação |A operação a efetuar para o gráfico sparkline. Para obter mais informações, consulte [configurações comuns](#sparklines). |
| Separador de nome e valor |O delimitador de caráter a utilizar para analisar a propriedade de texto em vários valores. Para obter mais informações, consulte [configurações comuns](#sparklines). |
| Navegação por clique | Ação efetuada quando clica num item na lista.  Para obter mais informações, consulte [configurações comuns](#click-through-navigation). |
| **Lista** |**> Títulos de coluna** |
| Name |O texto que é apresentado na parte superior da primeira coluna. |
| Value |O texto que é apresentado na parte superior da segunda coluna. |
| **Lista** |**> Limiares de** |
| Ativar Limiares |Selecione esta ligação para ativar limiares. Para obter mais informações, consulte [configurações comuns](#thresholds). |

## <a name="two-timelines-and-list-part"></a>Parte dois de linhas cronológicas e lista
O cabeçalho apresenta os resultados de duas consultas de registo ao longo do tempo, como gráficos de colunas, com uma nota de aviso que exibe um único número que resume uma coluna de valor numa consulta de registo. A lista apresenta os resultados de dez principais de uma consulta, com um gráfico que indica o valor relativo de uma coluna numérica ou a alteração ao longo do tempo.

![Ver duas linhas cronológicas e lista](media/view-designer-parts/view-two-timelines-list.png)

| Definição | Descrição |
|:--- |:--- |
| **Geral** | |
| Título do grupo |O texto que é apresentado na parte superior do mosaico. |
| Novo Grupo |Selecione esta ligação para criar um novo grupo na exibição, começando a exibição atual. |
| Ícone |O ficheiro de imagem que é apresentado junto ao resultado no cabeçalho. |
| Utilizar Ícone |Selecione esta ligação para ver o ícone. |
| **Navegação de título** | |
| Navegação por clique | Ação tomada quando clicar no cabeçalho.  Para obter mais informações, consulte [configurações comuns](#click-through-navigation). |
| **Primeiro gráfico<br>segundo gráfico** | |
| Legenda |O texto que é apresentado em nota de aviso para a primeira série. |
| Cor |A cor a utilizar para as colunas da série. |
| Consulta |A consulta seja executada para a primeira série. A contagem de registos ao longo de cada intervalo de tempo é representada pelas colunas do gráfico. |
| Operação |A operação para executar na propriedade de valor para resumi-lo como um valor único para a nota de aviso.<ul><li>Soma: A soma dos valores de todos os registos.</li><li>Média: A média dos valores de todos os registos.</li><li>Último exemplo: O valor no último intervalo de que está incluído no gráfico.</li><li>Primeiro exemplo: O valor do primeiro intervalo de que está incluído no gráfico.</li><li>Contagem de: Contagem de todos os registos que são devolvidos pela consulta.</li></ul> |
| **Lista** | |
| Consulta |A consulta seja executada para obter a lista. É apresentada a contagem dos registos que são devolvidos pela consulta. |
| Ocultar gráfico |Selecione esta ligação para desativar o gráfico à direita da coluna numérica. |
| Ativar gráficos sparkline |Selecione esta ligação para ver um gráfico sparkline em vez de uma barra horizontal. Para obter mais informações, consulte [configurações comuns](#sparklines). |
| Cor |A cor de barras ou minigráficos. |
| Operação |A operação a efetuar para o gráfico sparkline. Para obter mais informações, consulte [configurações comuns](#sparklines). |
| Navegação por clique | Ação efetuada quando clica num item na lista.  Para obter mais informações, consulte [configurações comuns](#click-through-navigation). |
| **Lista** |**> Títulos de coluna** |
| Name |O texto que é apresentado na parte superior da primeira coluna. |
| Value |O texto que é apresentado na parte superior da segunda coluna. |
| **Lista** |**> Limiares de** |
| Ativar Limiares |Selecione esta ligação para ativar limiares. Para obter mais informações, consulte [configurações comuns](#thresholds). |

## <a name="information-part"></a>Parte da informação
O cabeçalho exibe texto estático e uma ligação opcional. A lista apresenta um ou mais itens com um título estático e o texto.

![Vista de informações](media/view-designer-parts/view-information.png)

| Definição | Descrição |
|:--- |:--- |
| **Geral** | |
| Título do grupo |O texto que é apresentado na parte superior do mosaico. |
| Novo Grupo |Selecione esta ligação para criar um novo grupo na exibição, começando a exibição atual. |
| Cor |A cor de fundo para o cabeçalho. |
| **Cabeçalho** | |
| Imagem |O ficheiro de imagem que é apresentado no cabeçalho. |
| Label |O texto que é apresentado no cabeçalho. |
| **Cabeçalho** |**> Link** |
| Label |O texto da ligação. |
| Url |O Url para a ligação. |
| **Itens de informações** | |
| Título |O texto que é apresentado para o título de cada item. |
| Conteúdo |O texto que é apresentado para cada item. |

## <a name="line-chart-callout-and-list-part"></a>Gráfico de linhas, nota de aviso e parte da lista
O cabeçalho apresenta um gráfico de linhas com várias séries de uma consulta de registo ao longo do tempo e uma nota de aviso com um valor resumido. A lista apresenta os resultados de dez principais de uma consulta, com um gráfico que indica o valor relativo de uma coluna numérica ou a alteração ao longo do tempo.

![Gráfico de linhas, nota de aviso e vista de lista](media/view-designer-parts/view-line-chart-callout-list.png)

| Definição | Descrição |
|:--- |:--- |
| **Geral** | |
| Título do grupo |O texto que é apresentado na parte superior do mosaico. |
| Novo Grupo |Selecione esta ligação para criar um novo grupo na exibição, começando a exibição atual. |
| Ícone |O ficheiro de imagem que é apresentado junto ao resultado no cabeçalho. |
| Utilizar Ícone |Selecione esta ligação para ver o ícone. |
| **Cabeçalho** | |
| Título |O texto que é apresentado na parte superior do cabeçalho. |
| Subtítulo |O texto que é apresentado sob o título na parte superior do cabeçalho. |
| **Gráfico de linhas** | |
| Consulta |A consulta seja executada para o gráfico de linhas. A primeira propriedade é um valor de texto e a segunda propriedade é um valor numérico. Esta consulta normalmente utiliza a *medida* palavra-chave para resumir os resultados. Se a consulta utiliza a *intervalo* palavra-chave, o eixo x do gráfico utiliza este intervalo de tempo. Se a consulta não inclui a *intervalo* palavra-chave, os intervalos de hora a hora do eixo x utiliza. |
| Navegação por clique | Ação tomada quando clicar no cabeçalho.  Para obter mais informações, consulte [configurações comuns](#click-through-navigation). |
| **Gráfico de linhas** |**> Texto explicativo** |
| Título da nota de aviso |O texto que é apresentado acima do valor da nota de aviso. |
| Nome da Série |Valor da propriedade para a série a utilizar para o valor da nota de aviso. Se não for fornecido nenhum série, todos os registos da consulta são utilizados. |
| Operação |A operação para executar na propriedade de valor para resumi-lo como um valor único para a nota de aviso.<ul><li>Média: A média dos valores de todos os registos.</li><li>Contagem de: Contagem de todos os registos que são devolvidos pela consulta.</li><li>Último exemplo: O valor no último intervalo de que está incluído no gráfico.</li><li>Máx.: O valor máximo de intervalos de que estão incluídos no gráfico.</li><li>Mínimo: O valor mínimo dos intervalos que estão incluídos no gráfico.</li><li>Soma: A soma dos valores de todos os registos.</li></ul> |
| **Gráfico de linhas** |**> Eixo y** |
| Utilizar Escala Logarítmica |Selecione esta ligação para utilizar escala logarítmica para o eixo y. |
| Unidades |Especifique as unidades para os valores a serem retornados pela consulta. Estas informações são utilizadas para rótulos de gráficos de apresentação que indicam os tipos de valor e, opcionalmente, para converter os valores. O *unidade* tipo Especifica a categoria da unidade e define o disponíveis *unidade atual* tipo de valores. Se selecionar um valor na *converter*, os valores numéricos são convertidos da *unidade atual* escreva para o *converter* tipo. |
| Etiqueta personalizada |O texto que é apresentado para o eixo y junto à etiqueta para o *unidade* tipo. Se nenhuma etiqueta for especificada, apenas os *unidade* tipo é apresentado. |
| **Lista** | |
| Consulta |A consulta seja executada para obter a lista. É apresentada a contagem dos registos que são devolvidos pela consulta. |
| Ocultar gráfico |Selecione esta ligação para desativar o gráfico à direita da coluna numérica. |
| Ativar gráficos sparkline |Selecione esta ligação para ver um gráfico sparkline em vez de uma barra horizontal. Para obter mais informações, consulte [configurações comuns](#sparklines). |
| Cor |A cor de barras ou minigráficos. |
| Operação |A operação a efetuar para o gráfico sparkline. Para obter mais informações, consulte [configurações comuns](#sparklines). |
| Separador de nome e valor |O delimitador de caráter a utilizar para analisar a propriedade de texto em vários valores. Para obter mais informações, consulte [configurações comuns](#sparklines). |
| Navegação por clique | Ação efetuada quando clica num item na lista.  Para obter mais informações, consulte [configurações comuns](#click-through-navigation). |
| **Lista** |**> Títulos de coluna** |
| Name |O texto que é apresentado na parte superior da primeira coluna. |
| Value |O texto que é apresentado na parte superior da segunda coluna. |
| **Lista** |**> Limiares de** |
| Ativar Limiares |Selecione esta ligação para ativar limiares. Para obter mais informações, consulte [configurações comuns](#thresholds). |

## <a name="line-chart-and-list-part"></a>Parte de gráfico e a lista de linha
O cabeçalho apresenta um gráfico de linhas com várias séries de uma consulta de registo ao longo do tempo. A lista apresenta os resultados de dez principais de uma consulta, com um gráfico que indica o valor relativo de uma coluna numérica ou a alteração ao longo do tempo.

![Vista de lista e gráfico de linha](media/view-designer-parts/view-line-chart-callout-list.png)

| Definição | Descrição |
|:--- |:--- |
| **Geral** | |
| Título do grupo |O texto que é apresentado na parte superior do mosaico. |
| Novo Grupo |Selecione esta ligação para criar um novo grupo na exibição, começando a exibição atual. |
| Ícone |O ficheiro de imagem que é apresentado junto ao resultado no cabeçalho. |
| Utilizar Ícone |Selecione esta ligação para ver o ícone. |
| **Cabeçalho** | |
| Título |O texto que é apresentado na parte superior do cabeçalho. |
| Subtítulo |O texto que é apresentado sob o título na parte superior do cabeçalho. |
| **Gráfico de linhas** | |
| Consulta |A consulta seja executada para o gráfico de linhas. A primeira propriedade é um valor de texto e a segunda propriedade é um valor numérico. Esta consulta normalmente utiliza a *medida* palavra-chave para resumir os resultados. Se a consulta utiliza a *intervalo* palavra-chave, o eixo x do gráfico utiliza este intervalo de tempo. Se a consulta não inclui a *intervalo* palavra-chave, os intervalos de hora a hora do eixo x utiliza. |
| Navegação por clique | Ação tomada quando clicar no cabeçalho.  Para obter mais informações, consulte [configurações comuns](#click-through-navigation). |
| **Gráfico de linhas** |**> Eixo y** |
| Utilizar Escala Logarítmica |Selecione esta ligação para utilizar escala logarítmica para o eixo y. |
| Unidades |Especifique as unidades para os valores a serem retornados pela consulta. Estas informações são utilizadas para rótulos de gráficos de apresentação que indicam os tipos de valor e, opcionalmente, para converter os valores. O *unidade* tipo Especifica a categoria da unidade e define o disponíveis *unidade atual* tipo de valores. Se selecionar um valor na *converter*, os valores numéricos são convertidos da *unidade atual* escreva para o *converter* tipo. |
| Etiqueta personalizada |O texto que é apresentado para o eixo y junto à etiqueta para o *unidade* tipo. Se nenhuma etiqueta for especificada, apenas os *unidade* tipo é apresentado. |
| **Lista** | |
| Consulta |A consulta seja executada para obter a lista. É apresentada a contagem dos registos que são devolvidos pela consulta. |
| Ocultar gráfico |Selecione esta ligação para desativar o gráfico à direita da coluna numérica. |
| Ativar gráficos sparkline |Selecione esta ligação para ver um gráfico sparkline em vez de uma barra horizontal. Para obter mais informações, consulte [configurações comuns](#sparklines). |
| Cor |A cor de barras ou minigráficos. |
| Operação |A operação a efetuar para o gráfico sparkline. Para obter mais informações, consulte [configurações comuns](#sparklines). |
| Separador de nome e valor |O delimitador de caráter a utilizar para analisar a propriedade de texto em vários valores. Para obter mais informações, consulte [configurações comuns](#sparklines). |
| Navegação por clique | Ação efetuada quando clica num item na lista.  Para obter mais informações, consulte [configurações comuns](#click-through-navigation). |
| **Lista** |**> Títulos de coluna** |
| Name |O texto que é apresentado na parte superior da primeira coluna. |
| Value |O texto que é apresentado na parte superior da segunda coluna. |
| **Lista** |**> Limiares de** |
| Ativar Limiares |Selecione esta ligação para ativar limiares. Para obter mais informações, consulte [configurações comuns](#thresholds). |

## <a name="stack-of-line-charts-part"></a>Pilha de parte de gráficos de linha
A pilha do gráfico de linhas apresenta três gráficos de linha separado, com várias séries de uma consulta de registo ao longo do tempo, conforme mostrado aqui:

![Pilha de gráficos de linhas](media/view-designer-parts/view-stack-line-charts.png)

| Definição | Descrição |
|:--- |:--- |
| **Geral** | |
| Título do grupo |O texto que é apresentado na parte superior do mosaico. |
| Novo Grupo |Selecione esta ligação para criar um novo grupo na exibição, começando a exibição atual. |
| Ícone |O ficheiro de imagem que é apresentado junto ao resultado no cabeçalho. |
| **1 de gráfico<br>gráfico 2<br>3 do gráfico** |**> Cabeçalho** |
| Título |O texto que é apresentado na parte superior do gráfico. |
| Subtítulo |O texto que é apresentado sob o título na parte superior do gráfico. |
| **1 de gráfico<br>gráfico 2<br>3 do gráfico** |**Gráfico de linhas** |
| Consulta |A consulta seja executada para o gráfico de linhas. A primeira propriedade é um valor de texto e a segunda propriedade é um valor numérico. Esta consulta normalmente utiliza a *medida* palavra-chave para resumir os resultados. Se a consulta utiliza a *intervalo* palavra-chave, o eixo x do gráfico utiliza este intervalo de tempo. Se a consulta não inclui a *intervalo* palavra-chave, os intervalos de hora a hora do eixo x utiliza. |
| Navegação por clique | Ação tomada quando clicar no cabeçalho.  Para obter mais informações, consulte [configurações comuns](#click-through-navigation). |
| **Chart** |**> Eixo y** |
| Utilizar Escala Logarítmica |Selecione esta ligação para utilizar escala logarítmica para o eixo y. |
| Unidades |Especifique as unidades para os valores a serem retornados pela consulta. Estas informações são utilizadas para rótulos de gráficos de apresentação que indicam os tipos de valor e, opcionalmente, para converter os valores. O *unidade* tipo Especifica a categoria da unidade e define o disponíveis *unidade atual* tipo de valores. Se selecionar um valor na *converter*, os valores numéricos são convertidos da *unidade atual* escreva para o *converter* tipo. |
| Etiqueta personalizada |O texto que é apresentado para o eixo y junto à etiqueta para o *unidade* tipo. Se nenhuma etiqueta for especificada, apenas os *unidade* tipo é apresentado. |

## <a name="common-settings"></a>Definições comuns
As secções seguintes descrevem as definições que são comuns a várias partes de visualização.

### <a name="name-value-separator"></a>Separador de nome e valor
O separador de nome e o valor é o delimitador de caráter a utilizar para analisar a propriedade text de uma consulta de lista em vários valores. Se especificar um delimitador, pode fornecer nomes para cada campo, separado pelo delimitador mesmo no **nome** caixa.

Por exemplo, considere uma propriedade chamada *localização* que incluídos como valores *41 de criação de Redmond* e *12 de criação de Bellevue*. Pode especificar um travessão (-) para o separador de nome e valor e *criação de cidade* para o nome. Essa abordagem analisa cada valor em duas propriedades chamadas *Cidade* e *modulares*.

### <a name="click-through-navigation"></a>Navegação por clique
Navegação por clique define que ação será direcionada quando clica num cabeçalho ou item de lista numa vista.  Optar por esta ação irá abrir uma consulta no [Log Analyticsl](../../azure-monitor/log-query/portals.md) ou iniciar outra vista.

A tabela seguinte descreve as definições para a navegação por clique.

| Definição           | Descrição |
|:--|:--|
| Pesquisa de registos (Automático) | Consulta de registo para ser executada quando seleciona um item de cabeçalho.  Esta é a mesma consulta de registo que o item se baseia.
| Pesquisa de Registos        | Consulta de registo para ser executada quando seleciona um item numa lista.  Escreva a consulta para o **consulta de navegação** caixa.   Uso *{item selecionado}* para incluir a sintaxe para o item que o utilizador selecionou.  Por exemplo, se a consulta tem uma coluna chamada *computador* e a consulta de navegação *{item selecionado}*, uma consulta como *computador = "MyComputer"* é executado quando seleciona um computador. Se estiver a consulta de navegação *tipo = evento {item selecionado}*, a consulta *tipo = evento computador = "MyComputer"* é executado. |
| Vista              | Vista para abrir quando seleciona um item de cabeçalho ou um item numa lista.  Selecione o nome de uma vista na sua área de trabalho do **nome da vista** caixa. |



### <a name="sparklines"></a>Gráficos Sparkline
Um gráfico sparkline é um gráfico de linhas pequeno que ilustra o valor de uma entrada de lista ao longo do tempo. Para partes de visualização com uma lista, pode selecionar se pretende apresentar uma barra horizontal, o que indica o valor relativo de uma coluna numérica, ou um gráfico sparkline, que indica o respetivo valor ao longo do tempo.

A tabela seguinte descreve as definições para os minigráficos:

| Definição | Descrição |
|:--- |:--- |
| Ativar Gráficos Sparkline |Selecione esta ligação para ver um gráfico sparkline em vez de uma barra horizontal. |
| Operação |Se os minigráficos são ativados, esta é a operação para executar cada propriedade na lista para calcular os valores para o gráfico sparkline.<ul><li>Último exemplo: O último valor para a série ao longo do intervalo de tempo.</li><li>Máx.: O valor máximo para a série ao longo do intervalo de tempo.</li><li>Mínimo: O valor mínimo para a série ao longo do intervalo de tempo.</li><li>Soma: A soma dos valores para a série ao longo do intervalo de tempo.</li><li>Resumo: Utiliza as mesmas `measure` comando como a consulta no cabeçalho.</li></ul> |

### <a name="thresholds"></a>Limiares
Ao utilizar limiares, pode apresentar um ícone colorido ao lado de cada item numa lista. Limiares dão-lhe um indicador visual rápido de itens que exceder um determinado valor ou enquadram-se dentro de um intervalo específico. Por exemplo, pode apresentar um ícone verde para itens com um valor aceitável, amarelo, se o valor for dentro de um intervalo que indica um aviso e vermelho, se ela exceder um valor de erro.

Quando ativar os limites de uma parte, tem de especificar um ou mais limiares. Se o valor de um item for maior do que um valor de limiar e inferior ao valor de limiar seguinte, a cor para esse valor é utilizada. Se o item for superior ao valor de limiar mais elevado, é utilizada noutra cor. 

Cada conjunto de limiar tem um limiar com um valor de **predefinido**. Esta é a cor que é definida se não existem outros valores são atingidos. Pode adicionar ou remover limites, selecionando o **Add** (+) ou **eliminar** (botão x).

A tabela seguinte descreve as definições de limiares:

| Definição | Descrição |
|:--- |:--- |
| Ativar Limiares |Selecione esta ligação para apresentar um ícone cor à esquerda de cada valor. O ícone indica o estado de funcionamento do valor relativo ao limites especificados. |
| Name |O nome do valor de limiar. |
| Limiar |O valor para o limiar. A cor de estado de funcionamento para cada item da lista é definida para a cor do valor de limiar mais elevado, que é excedido pelo valor do item. Se não existem valores de limiares forem ultrapassados, é utilizada uma cor predefinida. |
| Cor |A cor que indica o valor de limiar. |

## <a name="next-steps"></a>Passos Seguintes
* Saiba mais sobre [registar as consultas](../log-query/log-query-overview.md) para suportar as consultas em partes de visualização.
