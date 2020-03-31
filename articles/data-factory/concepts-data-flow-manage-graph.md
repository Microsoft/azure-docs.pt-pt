---
title: Gráficos de fluxo de dados
description: Como trabalhar com gráficos de fluxo de dados de fábrica de dados
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/04/2019
ms.openlocfilehash: da180bfb1aec29fa15b070fd73ba84d708ada927
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74928312"
---
# <a name="mapping-data-flow-graphs"></a>Mapeando gráficos de fluxo de dados

A superfície de design de fluxos de dados de mapeamento é uma superfície de "construção" onde se constroem fluxos de dados de cima para baixo, da esquerda para a direita. Existe uma caixa de ferramentas anexa a cada transformação com um símbolo mais (+). Concentre-se na sua lógica de negócio em vez de ligar nós através de bordas num ambiente DAG de forma livre.

Abaixo estão mecanismos incorporados para gerir o gráfico de fluxo de dados.

## <a name="move-nodes"></a>Mover nós

![Opções de transformação agregada](media/data-flow/agghead.png "cabeçalho agregador")

Sem um paradigma de arrastar e largar, a forma de "mover" um nó de transformação, é mudar o fluxo de entrada. Em vez disso, irá mover-se mudando ao mudar o "fluxo de entrada".

## <a name="streams-of-data-inside-of-data-flow"></a>Fluxos de dados dentro do fluxo de dados

No Fluxo de Dados da Fábrica de Dados de Dados da Azure, os fluxos representam o fluxo de dados. No painel de definições de transformação, verá um campo "Incoming Stream". Isto diz-lhe qual o fluxo de dados que está a chegar está a alimentar essa transformação. Pode alterar a localização física do seu nó de transformação no gráfico clicando no nome 'Entrada Stream' e selecionando outro fluxo de dados. A transformação atual, juntamente com todas as transformações subsequentes nesse fluxo, irão então mudar-se para o novo local.

Se estiver a mover uma transformação com uma ou mais transformações depois dela, então a nova localização no fluxo de dados será unida através de um novo ramo.

Se não tiver transformações subsequentes após o nó que selecionou, só essa transformação passará para o novo local.

## <a name="hide-graph-and-show-graph"></a>Ocultar gráfico e mostrar gráfico

Existe um botão na parte mais à direita do painel de configuração inferior onde pode expandir o painel inferior para o ecrã completo quando se trabalha em configurações de transformação. Isto permitir-lhe-á utilizar botões "anteriores" e "próximos" para navegar através das configurações do gráfico. Para voltar à visualização do gráfico, clique no botão para baixo e volte ao ecrã dividido.

## <a name="search-graph"></a>Gráfico de pesquisa

Pode pesquisar o gráfico com o botão de pesquisa na superfície de design.

![Procurar](media/data-flow/search001.png "Gráfico de pesquisa")

## <a name="next-steps"></a>Passos seguintes

Depois de concluir o design do Fluxo de Dados, ligue o botão de depuração e teste-o no modo de depuração, quer diretamente no designer de fluxo de [dados](concepts-data-flow-debug-mode.md) quer no [depurador do gasoduto](control-flow-execute-data-flow-activity.md).
