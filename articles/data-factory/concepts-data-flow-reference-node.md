---
title: Nó de referência do fluxo de dados de mapeamento de Azure Data Factory
description: Data Factory fluxo de dados adicionará um nó de referência para junções, pesquisas, uniões
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 4ed17114cc0ce586c68c5b3e087acffdb82ea96c
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030490"
---
# <a name="mapping-data-flow-reference-node"></a>Nó de referência do fluxo de dados de mapeamento



![](media/data-flow/referencenode.png "Nó de referência") do nó de referência

Um nó de referência é adicionado automaticamente à tela para significar que o nó ao qual ele está anexado faz referência a outro nó existente na tela. Imagine um nó de referência como um ponteiro ou uma referência a outra transformação de fluxo de dados.

Por exemplo: Quando você ingressa ou União de mais de um fluxo de dados, a tela de fluxo de dados pode adicionar um nó de referência que reflete o nome e as configurações do fluxo de entrada não primário.

O nó de referência não pode ser movido ou excluído. No entanto, você pode clicar no nó para modificar as configurações de transformação de origem.

As regras de interface do usuário que regem quando o fluxo de dados adiciona o nó de referência baseiam-se no espaço disponível e no espaçamento vertical entre as linhas.
