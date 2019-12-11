---
title: Construtor de expressões no fluxo de dados de mapeamento
description: Criar expressões usando o construtor de expressões no mapeamento de fluxos de dados no Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 12/9/2019
ms.openlocfilehash: 01aa2574ac6edd1ce5e1b209eac3e43bbed82fce
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74969391"
---
# <a name="building-expressions-in-mapping-data-flow"></a>Criando expressões no fluxo de dados de mapeamento

No fluxo de dados de mapeamento, muitas propriedades de transformação são inseridas como expressões. Essas expressões são compostas por valores de coluna, parâmetros, funções, operadores e literais que são avaliados como um tipo de dados do Spark em tempo de execução.

## <a name="opening-the-expression-builder"></a>Abrindo o construtor de expressões

A interface de edição de expressão no data factory UX é conhecida como o **Construtor de expressões**. Conforme você insere em sua lógica de expressão, data factory usa a conclusão de código [IntelliSense](https://docs.microsoft.com/visualstudio/ide/using-intellisense?view=vs-2019) para realce, verificação de sintaxe e preenchimento automático.

![Construtor de expressões](media/data-flow/xpb1.png "Construtor de Expressões")

Em transformações, como a coluna derivada e o filtro, em que as expressões são obrigatórias, abra o construtor de expressões clicando na caixa expressão azul.

![Construtor de expressões](media/data-flow/expressionbox.png "Construtor de Expressões")

Ao referenciar colunas em uma condição correspondente ou agrupar por, uma expressão pode extrair valores de colunas. Para criar uma expressão, selecione a opção "coluna computada".

![Construtor de expressões](media/data-flow/computedcolumn.png "Construtor de Expressões")

Nos casos em que uma expressão ou um valor literal são entradas válidas, ' adicionar conteúdo dinâmico ' permitirá que você crie uma expressão que seja avaliada como um literal.

![Construtor de expressões](media/data-flow/add-dynamic-content.png "Construtor de Expressões")

## <a name="expression-language-reference"></a>Referência de linguagem de expressão

O mapeamento de fluxos de dados tem funções e operadores internos que podem ser usados em expressões. Uma lista de funções disponíveis é encontrada na página referência de [linguagem de fluxo de dados de mapeamento](data-flow-expression-functions.md) .

## <a name="column-names-with-special-characters"></a>Nomes de coluna com caracteres especiais

Quando você tem nomes de coluna que incluem caracteres especiais ou espaços, coloque o nome entre chaves para fazer referência a eles em uma expressão.

```{[dbo].this_is my complex name$$$}```

## <a name="previewing-expression-results"></a>Visualizando resultados da expressão

Se o [modo de depuração](concepts-data-flow-debug-mode.md) for ativado, você poderá usar o cluster do Spark ao vivo para ver uma visualização em andamento do que sua expressão avalia. Como você está criando sua lógica, você pode depurar sua expressão em tempo real. 

![Construtor de expressões](media/data-flow/exp4b.png "Visualização de dados de expressão")

Clique no botão atualizar para atualizar os resultados da expressão em um exemplo ao vivo de sua origem.

![Construtor de expressões](media/data-flow/exp5.png "Visualização de dados de expressão")

## <a name="string-interpolation"></a>Interpolação de cadeia de caracteres

Use aspas duplas para incluir texto de cadeia de caracteres literal junto com expressões. Você pode incluir funções, colunas e parâmetros de expressão. A interpolação de cadeia de caracteres é útil para evitar o uso extensivo da concatenação de cadeia de caracteres ao incluir parâmetros em cadeias de consulta. Para usar a sintaxe de expressão, coloque-a entre chaves,

Alguns exemplos de interpolação de cadeia de caracteres:

* ```"My favorite movie is {iif(instr(title,', The')>0,"The {split(title,', The')[1]}",title)}"```

* ```"select * from {$tablename} where orderyear > {$year}"```

* ```"Total cost with sales tax is {round(totalcost * 1.08,2)}"```

## <a name="commenting-expressions"></a>Expressões de comentário

Adicione comentários às suas expressões usando a sintaxe de linha única e de comentário de várias linhas:

![Comentários](media/data-flow/comments.png "Comentários")

Abaixo estão exemplos de comentários válidos:

* ```/* This is my comment */```

* ```/* This is a```
*   ```multi-line comment */```
   
* ```// This is a single line comment```

Se você colocar um comentário na parte superior da expressão, ele será exibido na caixa de texto transformação para documentar suas expressões de transformação:

![Comentários](media/data-flow/comments2.png "Comentários")

## <a name="regular-expressions"></a>Expressões regulares

Muitas funções de linguagem de expressão usam sintaxe de expressão regular. Ao usar funções de expressão regular, o construtor de expressões tentará interpretar a barra invertida (\\) como uma sequência de caracteres de escape. Ao usar barras invertidas em sua expressão regular, coloque todo o Regex em tiques (\`) ou use uma barra invertida dupla.

Exemplo usando tiques

```
regex_replace('100 and 200', `(\d+)`, 'digits')
```

ou usar barra dupla

```
regex_replace('100 and 200', '(\\d+)', 'digits')
```

## <a name="addressing-array-indexes"></a>Endereçando índices de matriz

Com funções de expressão que retornam matrizes, use colchetes [] para endereçar índices específicos dentro desse objeto de matriz de retorno. A matriz é baseada em itens.

![Matriz do construtor de expressões](media/data-flow/expb2.png "Visualização de dados de expressão")

## <a name="keyboard-shortcuts"></a>Atalhos de teclado

* ```Ctrl-K Ctrl-C```: linha inteira de comentários
* ```Ctrl-K Ctrl-U```: remover marca de comentário
* ```F1```: fornecer comandos de ajuda do editor
* ```Alt-Down Arrow```: mover para baixo na linha atual
* ```Alt-Up Arrow```: mover para cima na linha atual
* ```Cntrl-Space```: mostrar ajuda de contexto

## <a name="convert-to-dates-or-timestamps"></a>Converter em datas ou carimbos de data/hora

Para incluir literais de cadeia de caracteres em sua saída de carimbo de data/hora, você precisa encapsular a conversão em ```toString()```.

```toString(toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss'), 'MM/dd /yyyy\'T\'HH:mm:ss')```

Para converter milissegundos de época em uma data ou timestamp, use `toTimestamp(<number of milliseconds>)`. Se o tempo for lançado em segundos, multiplique por 1000.

```toTimestamp(1574127407*1000l)```

O "l" à direita no final da expressão acima significa conversão para um tipo longo como sintaxe em linha.

## <a name="next-steps"></a>Passos seguintes

[Iniciar a criação de expressões de transformação de dados](data-flow-expression-functions.md)
