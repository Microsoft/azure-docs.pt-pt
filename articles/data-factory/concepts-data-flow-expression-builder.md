---
title: Construtor de expressão no fluxo de dados de mapeamento
description: Construa expressões usando o Expression Builder no mapeamento de fluxos de dados na Fábrica de Dados Azure
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: dda2812b5e2cc79d53658d568ba0845d593f41d6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81605369"
---
# <a name="build-expressions-in-mapping-data-flow"></a>Construir expressões no fluxo de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

No mapeamento do fluxo de dados, muitas propriedades de transformação são introduzidas como expressões. Estas expressões são compostas por valores de coluna, parâmetros, funções, operadores e literais que avaliam um tipo de dados spark no tempo de execução.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tkur]

## <a name="open-expression-builder"></a>Construtor de expressão aberta

A interface de edição de expressão na experiência do utilizador da Azure Data Factory é conhecida como Expression Builder. À medida que introduz a sua lógica de expressão, data Factory utiliza a conclusão do código [IntelliSense](https://docs.microsoft.com/visualstudio/ide/using-intellisense?view=vs-2019) para realçar, verificação de sintaxe e conclusão automática.

![Construtor de Expressão](media/data-flow/xpb1.png "Construtor de Expressões")

Em transformações como a coluna derivada e o filtro, onde as expressões são obrigatórias, open Expression Builder selecionando a caixa de expressão azul.

![Caixa de expressão azul](media/data-flow/expressionbox.png "Construtor de Expressões")

Quando se refere colunas em condições correspondentes ou em grupo, uma expressão pode extrair valores das colunas. Para criar uma expressão, selecione **coluna Computed**.

![Opção de coluna computada](media/data-flow/computedcolumn.png "Construtor de Expressões")

Nos casos em que uma expressão ou um valor literal são inputs válidos, selecione **Adicionar conteúdo dinâmico** para construir uma expressão que avalie um valor literal.

![Adicionar opção de conteúdo dinâmico](media/data-flow/add-dynamic-content.png "Construtor de Expressões")

## <a name="expression-language-reference"></a>Referência da linguagem de expressão

O mapeamento de fluxos de dados tem funções incorporadas e operadores que podem ser usados em expressões. Para obter uma lista das funções disponíveis, consulte funções de [expressão no fluxo de dados de mapeamento](data-flow-expression-functions.md).

## <a name="column-names-with-special-characters"></a>Nomes de colunas com caracteres especiais

Quando tiver nomes de colunas que incluam caracteres ou espaços especiais, rodeo o nome com aparelhos encaracolados para os referir numa expressão.

```{[dbo].this_is my complex name$$$}```

## <a name="preview-expression-results"></a>Resultados da expressão de pré-visualização

Se o [modo de depuração](concepts-data-flow-debug-mode.md) estiver ligado, pode utilizar o cluster Spark ao vivo para ver uma pré-visualização em curso do que a sua expressão avalia. À medida que constróia a sua lógica, pode desincomodá-la em tempo real. 

![Pré-visualização em curso](media/data-flow/exp4b.png "Pré-visualização de dados de expressão")

Selecione **Refresh** para atualizar os resultados da sua expressão contra uma amostra ao vivo da sua fonte.

![Botão de atualização](media/data-flow/exp5.png "Pré-visualização de dados de expressão")

## <a name="string-interpolation"></a>Interpolação de cordas

Utilize aspas para encerrar texto de corda literal juntamente com expressões. Pode incluir funções de expressão, colunas e parâmetros. A interpolação das cordas é útil para evitar o uso extensivo da concatenação de cordas quando os parâmetros são incluídos em cordas de consulta. Para utilizar a sintaxe de expressão, encerre-a em aparelhos encaracolados,

Alguns exemplos de interpolação de cordas:

* ```"My favorite movie is {iif(instr(title,', The')>0,"The {split(title,', The')[1]}",title)}"```

* ```"select * from {$tablename} where orderyear > {$year}"```

* ```"Total cost with sales tax is {round(totalcost * 1.08,2)}"```

## <a name="comment-expressions"></a>Expressões de comentários

Adicione comentários às suas expressões utilizando uma sintaxe de comentários de linha única e multilinhas.

![Sintaxe de comentários de linha única e multiline](media/data-flow/comments.png "Comentários")

Os seguintes exemplos são válidos:

* ```/* This is my comment */```

* ```/* This is a```
*   ```multi-line comment */```
   
* ```// This is a single line comment```

Se colocar um comentário no topo da sua expressão, aparece na caixa de texto de transformação para documentar as suas expressões de transformação.

![Comentar na caixa de texto de transformação](media/data-flow/comments2.png "Comentários")

## <a name="regular-expressions"></a>Expressões regulares

Muitas funções linguísticas de expressão usam sintaxe de expressão regular. Quando se utilizam funções de expressão regulares,\\o Expression Builder tenta interpretar uma barra de backslash ( ) como uma sequência de caracteres de fuga. Quando utilizar as pestanas na sua expressão regular, encerre todo\`o regex em backticks ( ) ou utilize um backslash duplo.

Um exemplo que usa backticks:

```
regex_replace('100 and 200', `(\d+)`, 'digits')
```

Um exemplo que usa cortes duplos:

```
regex_replace('100 and 200', '(\\d+)', 'digits')
```

## <a name="address-array-indexes"></a>Índices de matriz de endereços

Com funções de expressão que devolvem matrizes, utilize parênteses ([]) para abordar índices específicos no interior que devolvem objetos matrizes. A matriz é baseada em umas.

![Array de construtor de expressão](media/data-flow/expb2.png "Pré-visualização de dados de expressão")

## <a name="keyboard-shortcuts"></a>Atalhos de teclado

* CTRL+K Ctrl+C: Comentar toda a linha.
* Ctrl+K Ctrl+U: Não comentar.
* F1: Fornecer comandos de ajuda ao editor.
* Tecla de seta Alt+Down: Desça a linha atual.
* Tecla de seta Alt+Up: Mova a linha atual.
* Ctrl+Barra espacial: Mostrar ajuda ao contexto.

## <a name="convert-to-dates-or-timestamps"></a>Converter em datas ou selos temporais

Para incluir os literais de cordas na ```toString()```saída da sua marca de tempo, envolva a sua conversão em .

```toString(toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss'), 'MM/dd /yyyy\'T\'HH:mm:ss')```

Para converter milissegundos da época para uma `toTimestamp(<number of milliseconds>)`data ou carimbo de tempo, utilize . Se o tempo chegar em segundos, multiplique por 1.000.

```toTimestamp(1574127407*1000l)```

O "l" de rasto no final da expressão anterior significa conversão para um tipo longo como sintaxe inline.

## <a name="next-steps"></a>Passos seguintes

[Comece a construir expressões de transformação de dados](data-flow-expression-functions.md)
