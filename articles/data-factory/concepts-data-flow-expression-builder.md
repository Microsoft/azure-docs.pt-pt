---
title: Construtor de expressão no fluxo de dados de mapeamento
description: Construa expressões utilizando o Expression Builder em fluxos de dados de mapeamento na Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/04/2021
ms.openlocfilehash: 753f201fbde5d9e7100b6e257f8dc79e4462d7b6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99584928"
---
# <a name="build-expressions-in-mapping-data-flow"></a>Construa expressões no fluxo de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

No fluxo de dados de mapeamento, muitas propriedades de transformação são inseridas como expressões. Estas expressões são compostas por valores de coluna, parâmetros, funções, operadores e literais que avaliam um tipo de dados Spark no tempo de execução. Mapear fluxos de dados tem uma experiência dedicada que o ajuda na construção destas expressões chamadas **Dedômes.** Utilizando a conclusão do código  [IntelliSense](/visualstudio/ide/using-intellisense) para realçar, fazer verificação de sintaxe e auto-conclusão, o construtor de expressão foi concebido para facilitar os fluxos de dados de construção. Este artigo explica como usar o construtor de expressão para construir eficazmente a sua lógica de negócio.

![Construtor de Expressão](media/data-flow/expresion-builder.png "Construtor de Expressões")

## <a name="open-expression-builder"></a>Construtor de expressão aberta

Existem vários pontos de entrada para abrir o construtor de expressão. Todos estes dependem do contexto específico da transformação do fluxo de dados. O caso de uso mais comum é em transformações como [coluna derivada](data-flow-derived-column.md) e [agregado](data-flow-aggregate.md) onde os utilizadores criam ou atualizam colunas usando a linguagem de expressão de fluxo de dados. O construtor de expressão pode ser aberto selecionando O construtor de **expressão aberta** acima da lista de colunas. Também pode clicar num contexto de coluna e abrir o construtor de expressão diretamente para essa expressão.

![Open Expression Builder deriva](media/data-flow/open-expression-builder-derive.png "Open Expression Builder deriva")

Em algumas transformações como [o filtro,](data-flow-filter.md)clicar numa caixa de texto de expressão azul abrirá o construtor de expressão. 

![Caixa de expressão azul](media/data-flow/expressionbox.png "Caixa de expressão azul")

Quando se referem colunas numa condição correspondente ou em grupo, uma expressão pode extrair valores de colunas. Para criar uma expressão, **selecione coluna Computada**.

![Opção de coluna computada](media/data-flow/computedcolumn.png "Opção de coluna computada")

Nos casos em que uma expressão ou um valor literal são entradas válidas, **selecione Adicionar conteúdo dinâmico** para construir uma expressão que avalie para um valor literal.

![Adicionar opção de conteúdo dinâmico](media/data-flow/add-dynamic-content.png "Adicionar opção de conteúdo dinâmico")

## <a name="expression-elements"></a>Elementos de expressão

No mapeamento dos fluxos de dados, as expressões podem ser compostas por valores de coluna, parâmetros, funções, variáveis locais, operadores e literais. Estas expressões devem avaliar a um tipo de dados Spark, como cordas, boolean ou inteiro.

![Elementos de expressão](media/data-flow/expression-elements.png "Elementos de expressão")

### <a name="functions"></a>Funções

Mapear fluxos de dados tem funções incorporadas e operadores que podem ser usados em expressões. Para obter uma lista de funções disponíveis, consulte a [referência do fluxo de dados](data-flow-expression-functions.md)de mapeamento .

#### <a name="address-array-indexes"></a>Índices de matriz de endereços

Ao lidar com colunas ou funções que devolvam tipos de matrizes, utilize suportes ([]) para aceder a um elemento específico. Se o índice não existir, a expressão avalia em NU.

![Matriz de construtor de expressão](media/data-flow/expression-array.png "Visualização de dados de expressão")

> [!IMPORTANT]
> No mapeamento dos fluxos de dados, as matrizes são baseadas em um significado que o primeiro elemento é referenciado pelo índice um. Por exemplo, o meuArray[1] acederá ao primeiro elemento de uma matriz chamada 'myArray'.

### <a name="input-schema"></a>Esquema de entrada

Se o fluxo de dados utilizar um esquema definido em qualquer uma das suas fontes, pode referenciar uma coluna pelo nome em muitas expressões. Se estiver a utilizar a deriva de esquema, pode referenciar colunas explicitamente utilizando as `byName()` funções ou `byNames()` funções ou corresponder utilizando padrões de coluna.

#### <a name="column-names-with-special-characters"></a>Nomes de colunas com caracteres especiais

Quando tiver nomes de colunas que incluam caracteres ou espaços especiais, rodeie o nome com aparelhos encaracolados para os referenciar numa expressão.

```{[dbo].this_is my complex name$$$}```

### <a name="parameters"></a>Parâmetros

Os parâmetros são valores que são passados para um fluxo de dados no tempo de execução a partir de um oleoduto. Para fazer referência a um parâmetro, clique no parâmetro a partir da visualização de **elementos de expressão** ou referencia-lo com um sinal de dólar na frente do seu nome. Por exemplo, um parâmetro chamado parâmetro1 seria referenciado por `$parameter1` . Para saber mais, consulte [os fluxos de dados de mapeamento parametrizante.](parameters-data-flow.md)

### <a name="cached-lookup"></a>Lookup em cache

Uma procura em cache permite-lhe fazer uma procura inline da saída de um lavatório em cache. Existem duas funções disponíveis para usar em cada pia, `lookup()` e `outputs()` . A sintaxe para referência a estas funções é `cacheSinkName#functionName()` . Para mais informações, consulte [os lavatórios de cache.](data-flow-sink.md#cache-sink)

`lookup()` toma as colunas correspondentes na transformação atual como parâmetros e devolve uma coluna complexa igual à linha correspondente às colunas-chave na pia de cache. A coluna complexa devolvida contém um subcolumn para cada coluna mapeada na pia da cache. Por exemplo, se tivesse uma pia de cache de código de erro `errorCodeCache` que tivesse uma coluna-chave correspondente no código e uma coluna chamada `Message` . A chamada `errorCodeCache#lookup(errorCode).Message` devolveria a mensagem correspondente ao código passado. 

`outputs()` não tem parâmetros e devolve toda a pia de cache como uma matriz de colunas complexas. Isto não pode ser chamado se as colunas-chave são especificadas na pia e só devem ser usadas se houver um pequeno número de linhas na pia de cache. Um caso de uso comum está a anexar o valor máximo de uma chave de incremento. Se uma única linha agregada em cache `CacheMaxKey` contiver uma `MaxKey` coluna, pode fazer referência ao primeiro valor chamando `CacheMaxKey#outputs()[1].MaxKey` .

![Lookup em cache](media/data-flow/cached-lookup-example.png "Lookup em cache")

### <a name="locals"></a>Locais

Se estiver a partilhar lógicas em várias colunas ou quiser compartimentar a sua lógica, pode criar um local dentro de uma coluna derivada\. Para fazer referência a um local, clique no local a partir da vista de **elementos de expressão** ou referencia-lo com um cólon na frente do seu nome. Por exemplo, um local chamado local1 seria referenciado por `:local1` . Saiba mais sobre os locais na [documentação da coluna derivada.](data-flow-derived-column.md#locals)

## <a name="preview-expression-results"></a>Resultados da expressão de pré-visualização

Se [o modo de depuração](concepts-data-flow-debug-mode.md) estiver ligado, pode utilizar interativamente o cluster de depuração para visualizar o que a sua expressão avalia. Selecione **Refresh** ao lado da pré-visualização de dados para atualizar os resultados da pré-visualização de dados. Pode ver a saída de cada linha dadas as colunas de entrada.

![Pré-visualização em curso](media/data-flow/preview-expression.png "Visualização de dados de expressão")

## <a name="string-interpolation"></a>Interpolação de cordas

Ao criar cordas longas que usam elementos de expressão, use a interpolação de cordas para facilmente construir uma lógica de cordas complexa. A interpolação de cordas evita o uso extensivo da concatenação de cordas quando os parâmetros são incluídos em cadeias de consulta. Utilize aspas duplas para incluir texto de corda literal juntamente com expressões. Pode incluir funções de expressão, colunas e parâmetros. Para usar a sintaxe de expressão, enrola-a em aparelhos encaracolados,

Alguns exemplos de interpolação de cordas:

* ```"My favorite movie is {iif(instr(title,', The')>0,"The {split(title,', The')[1]}",title)}"```

* ```"select * from {$tablename} where orderyear > {$year}"```

* ```"Total cost with sales tax is {round(totalcost * 1.08,2)}"```

* ```"{:playerName} is a {:playerRating} player"```

> [!NOTE]
> Ao utilizar a sintaxe de interpolação de cordas em consultas de origem SQL, a cadeia de consulta deve estar numa única linha, sem '/n'.

## <a name="commenting-expressions"></a>Comentando expressões

Adicione comentários às suas expressões utilizando sintaxe de comentário de linha única e multiline.

Os seguintes exemplos são os comentários válidos:

* ```/* This is my comment */```

* ```/* This is a```
* ```multi-line comment */```

Se colocar um comentário no topo da sua expressão, aparece na caixa de texto de transformação para documentar as suas expressões de transformação.

![Comentar na caixa de texto de transformação](media/data-flow/comment-expression.png "Comentários")

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

## <a name="keyboard-shortcuts"></a>Atalhos de teclado

Abaixo estão uma lista de atalhos disponíveis no construtor de expressão. A maioria dos atalhos intellisense estão disponíveis na criação de expressões.

* Ctrl+K Ctrl+C: Comentar toda a linha.
* Ctrl+K Ctrl+U: Descomprometment.
* F1: Fornecer comandos de ajuda ao editor.
* Tecla de seta Alt+Down: Desloque-se para baixo da linha de corrente.
* Tecla de seta Alt+Up: Avance a linha de corrente.
* Ctrl+Spacebar: Mostrar ajuda ao contexto.

## <a name="commonly-used-expressions"></a>Expressões comumente usadas

### <a name="convert-to-dates-or-timestamps"></a>Converter em datas ou prazos

Para incluir as cordas literais na sua saída de timetamp, embrulhe a sua conversão em ```toString()``` .

```toString(toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss'), 'MM/dd /yyyy\'T\'HH:mm:ss')```

Para converter milissegundos da época para uma data ou hora marcada, utilize `toTimestamp(<number of milliseconds>)` . Se o tempo chegar em segundos, multiplique por 1.000.

```toTimestamp(1574127407*1000l)```

O "l" de fuga no final da expressão anterior significa conversão para um tipo longo como sintaxe inline.

### <a name="find-time-from-epoch-or-unix-time"></a>Encontre tempo da época ou tempo unix

toLong( actualTimestamp() - toTimestamp('1970-01-01 00:00:00.000', 'yyyy-MM-dd HH:mm:mm:ss. SSS') ) * 1000l

## <a name="next-steps"></a>Passos seguintes

[Começar a construir expressões de transformação de dados](data-flow-expression-functions.md)
