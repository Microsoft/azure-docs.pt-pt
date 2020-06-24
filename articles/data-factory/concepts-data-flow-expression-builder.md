---
title: Construtor de expressão no fluxo de dados de mapeamento
description: Construa expressões utilizando o Expression Builder em fluxos de dados de mapeamento na Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/20/2020
ms.openlocfilehash: 7e2b655b344af90c4555beb0af85fa11cbc6d1c8
ms.sourcegitcommit: 398fecceba133d90aa8f6f1f2af58899f613d1e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/21/2020
ms.locfileid: "85126166"
---
# <a name="build-expressions-in-mapping-data-flow"></a>Construa expressões no fluxo de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

No fluxo de dados de mapeamento, muitas propriedades de transformação são inseridas como expressões. Estas expressões são compostas por valores de coluna, parâmetros, funções, operadores e literais que avaliam um tipo de dados Spark no tempo de execução.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tkur]

## <a name="open-expression-builder"></a>Construtor de expressão aberta

A interface de edição de expressão na experiência do utilizador da Azure Data Factory é conhecida como Expression Builder. Ao introduzir a sua lógica de expressão, a Data Factory utiliza a conclusão do código [IntelliSense](https://docs.microsoft.com/visualstudio/ide/using-intellisense?view=vs-2019) para realçar, fazer verificação de sintaxe e completar automaticamente.

![Construtor de Expressão](media/data-flow/xpb1.png "Construtor de Expressões")

Em transformações como a coluna derivada e o filtro, onde as expressões são obrigatórias, abra o Expression Builder selecionando a caixa de expressão azul.

![Caixa de expressão azul](media/data-flow/expressionbox.png "Construtor de Expressões")

Quando se referem colunas numa condição correspondente ou em grupo, uma expressão pode extrair valores de colunas. Para criar uma expressão, **selecione coluna Computada**.

![Opção de coluna computada](media/data-flow/computedcolumn.png "Construtor de Expressões")

Nos casos em que uma expressão ou um valor literal são entradas válidas, **selecione Adicionar conteúdo dinâmico** para construir uma expressão que avalie para um valor literal.

![Adicionar opção de conteúdo dinâmico](media/data-flow/add-dynamic-content.png "Construtor de Expressões")

## <a name="expression-language-reference"></a>Referência da linguagem de expressão

Mapear fluxos de dados tem funções incorporadas e operadores que podem ser usados em expressões. Para obter uma lista das funções disponíveis, consulte [as funções de expressão no fluxo de dados de mapeamento](data-flow-expression-functions.md).

## <a name="column-names-with-special-characters"></a>Nomes de colunas com caracteres especiais

Quando tiver nomes de colunas que incluam caracteres ou espaços especiais, rodeie o nome com aparelhos encaracolados para os referenciar numa expressão.

```{[dbo].this_is my complex name$$$}```

## <a name="preview-expression-results"></a>Resultados da expressão de pré-visualização

Se [o modo de depuração](concepts-data-flow-debug-mode.md) estiver ligado, pode utilizar o cluster De spark ao vivo para ver uma pré-visualização em curso do que a sua expressão avalia. À medida que constrói a sua lógica, pode desormes a tua expressão em tempo real. 

![Pré-visualização em curso](media/data-flow/exp4b.png "Visualização de dados de expressão")

Selecione **Refresh** para atualizar os resultados da sua expressão contra uma amostra ao vivo da sua fonte.

![Botão de atualização](media/data-flow/exp5.png "Visualização de dados de expressão")

## <a name="string-interpolation"></a>Interpolação de cordas

Utilize aspas para incluir textos de cordas literais juntamente com expressões. Pode incluir funções de expressão, colunas e parâmetros. A interpolação de cordas é útil para evitar o uso extensivo da concatenação de cordas quando os parâmetros são incluídos em cadeias de consulta. Para usar a sintaxe de expressão, enrola-a em aparelhos encaracolados,

Alguns exemplos de interpolação de cordas:

* ```"My favorite movie is {iif(instr(title,', The')>0,"The {split(title,', The')[1]}",title)}"```

* ```"select * from {$tablename} where orderyear > {$year}"```

* ```"Total cost with sales tax is {round(totalcost * 1.08,2)}"```

## <a name="comment-expressions"></a>Expressões de comentário

Adicione comentários às suas expressões utilizando sintaxe de comentário de linha única e multiline.

Os seguintes exemplos são os comentários válidos:

* ```/* This is my comment */```

* ```/* This is a```
*   ```multi-line comment */```

Se colocar um comentário no topo da sua expressão, aparece na caixa de texto de transformação para documentar as suas expressões de transformação.

![Comentar na caixa de texto de transformação](media/data-flow/comments2.png "Comentários")

## <a name="regular-expressions"></a>Expressões regulares

Muitas funções linguísticas de expressão usam sintaxe de expressão regular. Quando utiliza funções de expressão regular, o Expression Builder tenta interpretar um retrocesso \\ () como uma sequência de caracteres de fuga. Quando utilizar as costas na sua expressão regular, ou encerra todo o regex em patas traseiras \` () ou utiliza uma pestana dupla.

Um exemplo que usa backticks:

```
regex_replace('100 and 200', `(\d+)`, 'digits')
```

Um exemplo que usa duplos cortes:

```
regex_replace('100 and 200', '(\\d+)', 'digits')
```

## <a name="address-array-indexes"></a>Índices de matriz de endereços

Com funções de expressão que devolvem matrizes, utilize suportes ([]) para endereçar índices específicos no interior dos objetos de matriz de retorno. A matriz é baseada em umas.

![Matriz de construtor de expressão](media/data-flow/expb2.png "Visualização de dados de expressão")

## <a name="keyboard-shortcuts"></a>Atalhos de teclado

* Ctrl+K Ctrl+C: Comentar toda a linha.
* Ctrl+K Ctrl+U: Descomprometment.
* F1: Fornecer comandos de ajuda ao editor.
* Tecla de seta Alt+Down: Desloque-se para baixo da linha de corrente.
* Tecla de seta Alt+Up: Avance a linha de corrente.
* Ctrl+Spacebar: Mostrar ajuda ao contexto.

## <a name="convert-to-dates-or-timestamps"></a>Converter em datas ou prazos

Para incluir as cordas literais na sua saída de timetamp, embrulhe a sua conversão em ```toString()``` .

```toString(toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss'), 'MM/dd /yyyy\'T\'HH:mm:ss')```

Para converter milissegundos da época para uma data ou hora marcada, utilize `toTimestamp(<number of milliseconds>)` . Se o tempo chegar em segundos, multiplique por 1.000.

```toTimestamp(1574127407*1000l)```

O "l" de fuga no final da expressão anterior significa conversão para um tipo longo como sintaxe inline.

## <a name="find-time-from-epoch-or-unix-time"></a>Encontre tempo da época ou tempo unix

toLong( actualTimestamp() - toTimestamp('1970-01-01 00:00:00.000', 'yyyy-MM-dd HH:mm:mm:ss. SSS') ) * 1000l

## <a name="next-steps"></a>Passos seguintes

[Começar a construir expressões de transformação de dados](data-flow-expression-functions.md)
