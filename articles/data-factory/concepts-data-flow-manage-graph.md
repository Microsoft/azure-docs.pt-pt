---
title: Azure Data Factory grafos de fluxo de dados
description: Como trabalhar com data factory gráficos de fluxo de dados
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 38f85dea554000daf89689a311c091bc9796a658
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74015902"
---
# <a name="mapping-data-flow-graphs"></a>Mapeando grafos de fluxo de dados

A superfície de design de fluxos de dados de mapeamento é uma superfície de "construção" onde você cria fluxos de dados de cima para baixo, da esquerda para a direita. Há uma caixa de ferramentas anexada a cada transformação com um símbolo de mais (+). Concentre-se em sua lógica de negócios em vez de conectar nós por meio de bordas em um ambiente de DAG de forma livre.

Abaixo estão os mecanismos internos para gerenciar o grafo de fluxo de dados.

## <a name="move-nodes"></a>Mover nós

![Opções de transformação Agregação](media/data-flow/agghead.png "cabeçalho do agregador")

Sem um paradigma de arrastar e soltar, a maneira de "mover" um nó de transformação é alterar o fluxo de entrada. Em vez disso, você moverá transformações ao alterar o "fluxo de entrada".

## <a name="streams-of-data-inside-of-data-flow"></a>Fluxos de dados dentro do fluxo de dados

No fluxo de dados Azure Data Factory, os fluxos representam o fluxo de dados. No painel configurações de transformação, você verá um campo "fluxo de entrada". Isso informa qual fluxo de dados de entrada está alimentando a transformação. Você pode alterar o local físico do nó de transformação no grafo clicando no nome do fluxo de entrada e selecionando outro fluxo de dados. A transformação atual, juntamente com todas as transformações subsequentes nesse fluxo, passará para o novo local.

Se você estiver movendo uma transformação com uma ou mais transformações depois dela, o novo local no fluxo de dados será ingressado por meio de uma nova ramificação.

Se você não tiver transformações subsequentes após o nó selecionado, somente essa transformação será movida para o novo local.

## <a name="hide-graph-and-show-graph"></a>Ocultar grafo e mostrar grafo

Há um botão na extrema direita do painel de configuração inferior, no qual você pode expandir o painel inferior para tela inteira ao trabalhar em configurações de transformação. Isso permitirá que você use os botões "anterior" e "Avançar" para navegar pelas configurações do grafo. Para voltar para a exibição de gráfico, clique no botão para baixo e retorne à tela de divisão.

## <a name="search-graph"></a>Grafo de pesquisa

Você pode pesquisar o grafo com o botão Pesquisar na superfície de design.

![Pesquisa](media/data-flow/search001.png "Grafo de pesquisa")

## <a name="next-steps"></a>Passos seguintes

Depois de concluir o design do fluxo de dados, ative o botão de depuração e teste-o no modo de depuração diretamente no [Designer de fluxo de dados](concepts-data-flow-debug-mode.md) ou na depuração de [pipeline](control-flow-execute-data-flow-activity.md).
