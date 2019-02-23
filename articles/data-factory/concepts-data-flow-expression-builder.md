---
title: Construtor de expressões de fluxo de mapeamento de dados do Azure Data Factory
description: O construtor de expressões para dados de mapeamento de fábrica de dados do Azure fluxos
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: b99311a7a8742cdc5995e53a6b0d3e6a2ddf1328
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56736877"
---
# <a name="mapping-data-flow-expression-builder"></a>Construtor de expressões de fluxo de dados de mapeamento

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

No Azure Data Factory mapeamento de fluxo de dados, encontrará caixas de expressão onde pode introduzir expressões para transformação de dados. Utilize estas caixas de colunas, campos, variáveis, parâmetros, as funções a partir do seu fluxo de dados. Para compilar a expressão, use o construtor de expressões, o que é iniciado ao clicar na caixa de texto expressão dentro da transformação. Verá também que, às vezes, as opções de "Coluna calculada" quando selecionar as colunas para transformação. Quando clico nele, também verá o construtor de expressões iniciado.

![Construtor de expressões](media/data-flow/expression.png "construtor de expressões")

A ferramenta construtor de expressões por predefinição, a opção de editor de texto. o recurso de preenchimento automático lê do modelo de objeto do fluxo de dados do Azure Data Factory inteiro com a verificação e realce de sintaxe.

![Preenchimento automático de construtor de expressões](media/data-flow/expb1.png "Expression Builder, preenchimento automático")

## <a name="currently-working-on-field"></a>Atualmente a trabalhar no campo

![Construtor de expressões](media/data-flow/exp3.png "trabalhando no momento")

Na parte superior esquerda da interface do Usuário do Expression Builder, verá um campo chamado "A trabalhar no momento" com o nome do campo que está atualmente a trabalhar num. A expressão que criar na interface do Usuário será aplicada apenas para o campo de trabalho que, atualmente. Se quiser transformar outro campo, guardar o trabalho atual e utilize esta lista pendente para selecionar outro campo e criar uma expressão para os outros campos.

## <a name="data-preview-in-debug-mode"></a>Pré-visualização de dados no modo de depuração

![Construtor de expressões](media/data-flow/exp4b.png "pré-visualização de dados de expressão")

Quando estiver a trabalhar em seu expressões, pode, opcionalmente, alternar no modo de depuração da superfície de design de fluxo de dados do Azure Data Factory, permitindo a visualização dinâmica em andamento de seus resultados de dados da expressão que estiver a criar. Depuração em direto em tempo real está ativada para as expressões.

![Modo de depuração](media/data-flow/debugbutton.png "botão de depuração")


![Construtor de expressões](media/data-flow/exp5.png "pré-visualização de dados de expressão")

## <a name="comments"></a>Comentários

Adicione comentários para as expressões usando a sintaxe de comentário com várias linhas e de linha única:

![Comentários](media/data-flow/comments.png "comentários")

## <a name="regular-expressions"></a>Expressões Regulares

A linguagem de expressão fluxo de dados do Azure Data Factory [documentação de referência completo aqui](http://aka.ms/dataflowexpressions), permite que incluam a sintaxe de expressão regular. Ao utilizar as funções de expressão regular, o construtor de expressões tentará interpretar barra invertida (\) como uma seqüência de caracteres de escape. Ao utilizar as barras invertidas sua expressão regular, ou coloque regex todo em tiques ` ` ou usar uma barra invertida dupla.

Exemplo de uso ticks

```
regex_replace('100 and 200', `(\d+)`, 'digits')
```

ou ao utilizar duas barras

```
regex_replace('100 and 200', '(\\d+)', 'digits')
```

## <a name="addressing-array-indexes"></a>Endereçamento de matriz de índices

Com as funções de expressão que retornam as matrizes, use parênteses Retos [-] para abordar índices específicos dentro desse objeto de retorno de matriz. A matriz é baseado em aplicações.

![Matriz do Expression Builder](media/data-flow/expb2.png "pré-visualização de dados de expressão")
