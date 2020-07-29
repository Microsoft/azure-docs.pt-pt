---
title: Gráficos de fluxo de dados
description: Como trabalhar com gráficos de fluxo de dados de fábrica de dados
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/04/2019
ms.openlocfilehash: 0d357c4c671070a5c5e9d4587e2f90b6628996f4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81605360"
---
# <a name="mapping-data-flow-graphs"></a>Gráficos de fluxo de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A superfície de design de dados de mapeamento é uma superfície de "construção" onde se constrói fluxos de dados de cima para baixo, da esquerda para a direita. Há uma caixa de ferramentas anexada a cada transformação com um símbolo mais (+). Concentre-se na sua lógica de negócio em vez de ligar nós através de bordas em um ambiente DAG de forma livre.

Abaixo estão os mecanismos incorporados para gerir o gráfico de fluxo de dados.

## <a name="move-nodes"></a>Mover nós

![Opções de Transformação Agregada](media/data-flow/agghead.png "cabeçalho agregador")

Sem um paradigma de arrastar e largar, a forma de "mover" um nó de transformação, é mudar o fluxo de entrada. Em vez disso, move-se-á transformando-se mudando o "fluxo de entrada".

## <a name="streams-of-data-inside-of-data-flow"></a>Fluxos de dados dentro do fluxo de dados

No Fluxo de Dados da Fábrica de Dados Azure, os fluxos representam o fluxo de dados. No painel de definições de transformação, verá um campo "Incoming Stream". Isto diz-lhe qual o fluxo de dados que está a alimentar essa transformação. Pode alterar a localização física do seu nó de transformação no gráfico clicando no nome Incoming Stream e selecionando outro fluxo de dados. A transformação atual, juntamente com todas as transformações subsequentes naquele fluxo, irá então mudar-se para o novo local.

Se estiver a mover uma transformação com uma ou mais transformações depois dela, então a nova localização no fluxo de dados será unida através de um novo ramo.

Se não tiver transformações subsequentes após o nó que selecionou, só essa transformação se deslocará para o novo local.

## <a name="hide-graph-and-show-graph"></a>Esconda gráfico e mostre gráfico

Existe um botão na extrema-direita do painel de configuração inferior onde pode expandir o painel inferior para o ecrã completo ao trabalhar em configurações de transformação. Isto permitir-lhe-á utilizar botões "anteriores" e "seguintes" para navegar através das configurações do gráfico. Para voltar à vista do gráfico, clique no botão para baixo e volte ao ecrã dividido.

## <a name="search-graph"></a>Gráfico de pesquisa

Pode pesquisar o gráfico com o botão de pesquisa na superfície de design.

![Pesquisa](media/data-flow/search001.png "Gráfico de pesquisa")

## <a name="next-steps"></a>Próximos passos

Depois de completar o seu design de Fluxo de Dados, ligue o botão de depuração e teste-o no modo de depuração, quer diretamente no designer de fluxo de [dados,](concepts-data-flow-debug-mode.md) quer no [depurar do gasoduto](control-flow-execute-data-flow-activity.md).
