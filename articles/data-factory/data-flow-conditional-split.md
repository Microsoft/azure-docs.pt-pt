---
title: Transformação Divisão condicional do fluxo de dados de mapeamento de Azure Data Factory
description: Transformação Divisão condicional do fluxo de dados do Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: fec2b09b9dc471135d9cdd00ac2465728a47bdbf
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72026928"
---
# <a name="mapping-data-flow-conditional-split-transformation"></a>Mapeando a transformação de divisão condicional do fluxo de dados



(media/data-flow/conditionalsplit2.png "caixa") de ferramentas divisão condicional da ![caixa de ferramentas de divisão condicional]

A transformação Divisão condicional pode rotear linhas de dados para fluxos diferentes, dependendo do conteúdo dos dados. A implementação da transformação Divisão condicional é semelhante a uma estrutura de decisão de caso em uma linguagem de programação. A transformação avalia as expressões e, com base nos resultados, direciona a linha de dados para o fluxo especificado. Essa transformação também fornece uma saída padrão, de modo que se uma linha não corresponder a nenhuma expressão, ela será direcionada para a saída padrão.

(media/data-flow/conditionalsplit1.png "Opções de divisão condicional") de ![divisão condicional]

## <a name="multiple-paths"></a>Vários caminhos

Para adicionar outras condições, selecione "Adicionar fluxo" no painel de configuração inferior e clique na caixa de texto Construtor de expressões para criar sua expressão.

Divisão condicional ![múltipla](media/data-flow/conditionalsplit3.png "multicondicional") Split

## <a name="next-steps"></a>Passos seguintes

Transformações de fluxo de dados comuns usadas com divisão condicional: [Transformação de junção](data-flow-join.md), [transformação pesquisa](data-flow-lookup.md), [selecionar transformação](data-flow-select.md)
