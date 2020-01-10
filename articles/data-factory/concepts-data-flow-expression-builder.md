---
title: Construtor de expressões no fluxo de dados de mapeamento
description: Criar expressões usando o construtor de expressões no mapeamento de fluxos de dados no Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 12/9/2019
ms.openlocfilehash: 1dd782092ce91f7b71a3a2a6f2ed1646ee39a7e0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75444541"
---
# <a name="build-expressions-in-mapping-data-flow"></a>Criar expressões no fluxo de dados de mapeamento

No fluxo de dados de mapeamento, muitas propriedades de transformação são inseridas como expressões. Essas expressões são compostas por valores de coluna, parâmetros, funções, operadores e literais que são avaliados como um tipo de dados do Spark em tempo de execução.

## <a name="open-expression-builder"></a>Abrir o construtor de expressões

A interface de edição de expressão no Azure Data Factory experiência do usuário é conhecida como Construtor de expressões. À medida que você insere a lógica de expressão, Data Factory usa a conclusão de código [IntelliSense](https://docs.microsoft.com/visualstudio/ide/using-intellisense?view=vs-2019) para realce, verificação de sintaxe e preenchimento automático.

![Construtor de expressões](media/data-flow/xpb1.png "Construtor de Expressões")

Em transformações, como a coluna derivada e o filtro, em que as expressões são obrigatórias, abra o construtor de expressões selecionando a caixa expressão azul.

![Caixa de expressão azul](media/data-flow/expressionbox.png "Construtor de Expressões")

Quando você faz referência a colunas em uma condição de correspondência ou agrupamento, uma expressão pode extrair valores de colunas. Para criar uma expressão, selecione **coluna computada**.

![Opção de coluna computada](media/data-flow/computedcolumn.png "Construtor de Expressões")

Nos casos em que uma expressão ou um valor literal são entradas válidas, selecione **adicionar conteúdo dinâmico** para criar uma expressão que seja avaliada como um valor literal.

![Opção adicionar conteúdo dinâmico](media/data-flow/add-dynamic-content.png "Construtor de Expressões")

## <a name="expression-language-reference"></a>Referência de linguagem de expressão

O mapeamento de fluxos de dados tem funções e operadores internos que podem ser usados em expressões. Para obter uma lista de funções disponíveis, consulte [funções de expressão no fluxo de dados de mapeamento](data-flow-expression-functions.md).

## <a name="column-names-with-special-characters"></a>Nomes de coluna com caracteres especiais

Quando você tem nomes de coluna que incluem caracteres especiais ou espaços, coloque o nome entre chaves para fazer referência a eles em uma expressão.

```{[dbo].this_is my complex name$$$}```

## <a name="preview-expression-results"></a>Resultados da expressão de visualização

Se o [modo de depuração](concepts-data-flow-debug-mode.md) estiver ativado, você poderá usar o cluster Spark ao vivo para ver uma visualização em andamento do que sua expressão avalia. Ao criar sua lógica, você pode depurar sua expressão em tempo real. 

![Visualização em andamento](media/data-flow/exp4b.png "Visualização de dados de expressão")

Selecione **Atualizar** para atualizar os resultados da expressão em um exemplo ao vivo de sua origem.

![Botão atualizar](media/data-flow/exp5.png "Visualização de dados de expressão")

## <a name="string-interpolation"></a>Interpolação de cadeia de caracteres

Use aspas para colocar o texto da cadeia de caracteres literal junto com expressões. Você pode incluir funções, colunas e parâmetros de expressão. A interpolação de cadeia de caracteres é útil para evitar o uso extensivo da concatenação de cadeia de caracteres quando parâmetros são incluídos em cadeias de consulta. Para usar a sintaxe de expressão, coloque-a entre chaves,

Alguns exemplos de interpolação de cadeia de caracteres:

* ```"My favorite movie is {iif(instr(title,', The')>0,"The {split(title,', The')[1]}",title)}"```

* ```"select * from {$tablename} where orderyear > {$year}"```

* ```"Total cost with sales tax is {round(totalcost * 1.08,2)}"```

## <a name="comment-expressions"></a>Expressões de comentário

Adicione comentários às suas expressões usando a sintaxe de linha única e de comentário multilinha.

![Sintaxe de linha única e comentário multilinha](media/data-flow/comments.png "Comentários")

Os exemplos a seguir são comentários válidos:

* ```/* This is my comment */```

* ```/* This is a```
*   ```multi-line comment */```
   
* ```// This is a single line comment```

Se você colocar um comentário na parte superior da expressão, ele aparecerá na caixa de texto transformação para documentar suas expressões de transformação.

![Comentário na caixa de texto transformação](media/data-flow/comments2.png "Comentários")

## <a name="regular-expressions"></a>Expressões regulares

Muitas funções de linguagem de expressão usam sintaxe de expressão regular. Quando você usa funções de expressão regular, o construtor de expressões tenta interpretar uma barra invertida (\\) como uma sequência de caracteres de escape. Quando você usa barras invertidas em sua expressão regular, coloque o Regex inteiro em totiques (\`) ou use uma barra invertida dupla.

Um exemplo que usa marcas de escala:

```
regex_replace('100 and 200', `(\d+)`, 'digits')
```

Um exemplo que usa barras duplas:

```
regex_replace('100 and 200', '(\\d+)', 'digits')
```

## <a name="address-array-indexes"></a>Endereçar índices de matriz

Com funções de expressão que retornam matrizes, use colchetes ([]) para endereçar índices específicos dentro de objetos de matriz de retorno. A matriz se baseia nessas partes.

![Matriz do construtor de expressões](media/data-flow/expb2.png "Visualização de dados de expressão")

## <a name="keyboard-shortcuts"></a>Atalhos de teclado

* CTRL + K CTRL + C: comentar linha inteira.
* CTRL + K Ctrl + U: remover marca de comentário.
* F1: fornecer comandos de ajuda do editor.
* ALT + tecla de seta para baixo: mover para baixo na linha atual.
* ALT + tecla de seta para cima: mover para cima na linha atual.
* CTRL + barra de espaços: mostra a ajuda de contexto.

## <a name="convert-to-dates-or-timestamps"></a>Converter em datas ou carimbos de data/hora

Para incluir literais de cadeia de caracteres em sua saída de carimbo de data/hora, coloque a conversão em ```toString()```.

```toString(toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss'), 'MM/dd /yyyy\'T\'HH:mm:ss')```

Para converter milissegundos de época em uma data ou timestamp, use `toTimestamp(<number of milliseconds>)`. Se o tempo for lançado em segundos, multiplique por 1.000.

```toTimestamp(1574127407*1000l)```

O "l" à direita no final da expressão anterior significa conversão para um tipo longo como sintaxe embutida.

## <a name="next-steps"></a>Passos seguintes

[Iniciar a criação de expressões de transformação de dados](data-flow-expression-functions.md)
