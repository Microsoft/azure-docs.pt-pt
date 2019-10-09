---
title: Azure Data Factory nós de movimentação de fluxo de dados
description: Como mover nós em um diagrama de fluxo de dados de mapeamento de Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: 853fff955a953052b94ad4e7e1dd55371069c30b
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030185"
---
# <a name="mapping-data-flow-move-nodes"></a>Mapeando nós de movimentação de fluxo de dados



(media/data-flow/agghead.png "Cabeçalho do agregador") de ![Opções de transformação Agregação]

A superfície de design do fluxo de dados Azure Data Factory é uma superfície de "construção" onde você cria fluxos de dados de cima para baixo, da esquerda para a direita. Há uma caixa de ferramentas anexada a cada transformação com um símbolo de mais (+). Concentre-se em sua lógica de negócios em vez de conectar nós por meio de bordas em um ambiente de DAG de forma livre.

Portanto, sem um paradigma de arrastar e soltar, a maneira de "mover" um nó de transformação é alterar o fluxo de entrada. Em vez disso, você moverá transformações ao alterar o "fluxo de entrada".

## <a name="streams-of-data-inside-of-data-flow"></a>Fluxos de dados dentro do fluxo de dados

No fluxo de dados Azure Data Factory, os fluxos representam o fluxo de dados. No painel configurações de transformação, você verá um campo "fluxo de entrada". Isso informa qual fluxo de dados de entrada está alimentando a transformação. Você pode alterar o local físico do nó de transformação no grafo clicando no nome do fluxo de entrada e selecionando outro fluxo de dados. A transformação atual, juntamente com todas as transformações subsequentes nesse fluxo, passará para o novo local.

Se você estiver movendo uma transformação com uma ou mais transformações depois dela, o novo local no fluxo de dados será ingressado por meio de uma nova ramificação.

Se você não tiver transformações subsequentes após o nó selecionado, somente essa transformação será movida para o novo local.

## <a name="next-steps"></a>Passos seguintes

Depois de concluir o design do fluxo de dados, ative o botão de depuração e teste-o no modo de depuração diretamente no [Designer de fluxo de dados](concepts-data-flow-debug-mode.md) ou na depuração de [pipeline](control-flow-execute-data-flow-activity.md).
