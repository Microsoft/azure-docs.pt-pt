---
title: Construtor de expressões de fluxo de dados de mapeamento
description: O construtor de expressões para mapear Azure Data Factory fluxos de dados
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/06/2019
ms.openlocfilehash: 7d8f02647224c971c44bff51f09315c53c53e9a3
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928344"
---
# <a name="mapping-data-flow-expression-builder"></a>Construtor de expressões de fluxo de dados de mapeamento



No fluxo de dados de mapeamento Azure Data Factory, você encontrará caixas de expressão nas quais é possível inserir expressões para transformação de dados. Use colunas, campos, variáveis, parâmetros, funções do fluxo de dados nessas caixas. Para criar a expressão, use o construtor de expressões, que é iniciado clicando na caixa de texto expressão dentro da transformação. Às vezes, você verá opções de "coluna computada" ao selecionar colunas para transformação. Ao clicar nele, você também verá o construtor de expressões iniciado.

![Construtor de expressões](media/data-flow/xpb1.png "Construtor de Expressões")

A ferramenta Construtor de expressões usa como padrão a opção editor de texto. o recurso de preenchimento automático lê todo o modelo de objeto de fluxo de dados Azure Data Factory com verificação de sintaxe e realce.

![Preenchimento automático do construtor de expressões](media/data-flow/expb1.png "Preenchimento automático do construtor de expressões")

## <a name="build-schemas-in-output-schema-pane"></a>Criar esquemas no painel de esquema de saída

![Adicionar coluna complexa](media/data-flow/complexcolumn.png "Adicionar colunas")

No painel esquema de saída à esquerda, você verá as colunas que está modificando e adicionando ao seu esquema. Você pode criar interativamente estruturas de dados simples e complexas aqui. Adicione campos adicionais usando "adicionar coluna" e crie hierarquias usando "Adicionar subcoluna".

![Adicionar subcoluna](media/data-flow/addsubcolumn.png "Adicionar subcoluna")

## <a name="data-preview-in-debug-mode"></a>Visualização de dados no modo de depuração

![Construtor de expressões](media/data-flow/exp4b.png "Visualização de dados de expressão")

Quando você estiver trabalhando em suas expressões de fluxo de dados, ative o modo de depuração da superfície de design Azure Data Factory fluxo de dados, habilitando a visualização em andamento ao vivo dos resultados de seus dados da expressão que você está criando. A depuração dinâmica em tempo real está habilitada para suas expressões.

![Modo de depuração](media/data-flow/debugbutton.png "Botão de depuração")

Clique no botão atualizar para atualizar os resultados da expressão em um exemplo ao vivo de sua fonte em tempo real.

![Construtor de expressões](media/data-flow/exp5.png "Visualização de dados de expressão")

## <a name="comments"></a>Comentários

Adicione comentários às suas expressões usando a sintaxe de linha única e de comentário de várias linhas:

![Comentários](media/data-flow/comments.png "Comentários")

## <a name="string-interpolation"></a>Interpolação de cadeia de caracteres

Use aspas duplas para incluir texto de cadeia de caracteres literal junto com expressões. Você pode incluir funções, colunas e parâmetros de expressão. Isso é muito útil para evitar o uso extensivo da concatenação de cadeia de caracteres ao incluir parâmetros em cadeias de consulta.

* ```"My favorite movie is {iif(instr(title,', The')>0,"The {split(title,', The')[1]}",title)}"```

* ```"select * from {$tablename} where orderyear > {$year}"```

* ```"Total cost with sales tax is {round(totalcost * 1.08,2)}"```

## <a name="regular-expressions"></a>Expressões regulares

O Azure Data Factory linguagem de expressão de fluxo de dados, [documentação de referência completa aqui](https://aka.ms/dataflowexpressions), habilita funções que incluem sintaxe de expressão regular. Ao usar funções de expressão regular, o construtor de expressões tentará interpretar a barra invertida (\\) como uma sequência de caracteres de escape. Ao usar barras invertidas em sua expressão regular, coloque todo o Regex em tiques (\`) ou use uma barra invertida dupla.

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

## <a name="handling-names-with-special-characters"></a>Manipulando nomes com caracteres especiais

Quando você tem nomes de coluna que incluem caracteres especiais ou espaços, coloque o nome entre chaves.
* ```{[dbo].this_is my complex name$$$}```

## <a name="keyboard-shortcuts"></a>Atalhos de teclado

* ```Ctrl-K Ctrl-C```: linha inteira de comentários
* ```Ctrl-K Ctrl-U```: remover marca de comentário
* ```F1```: fornecer comandos de ajuda do editor
* ```Alt-Down Arrow```: mover a linha atual para baixo
* ```Alt-Up Arrow```: mover a linha atual para cima
* ```Cntrl-Space```: mostrar ajuda de contexto

## <a name="manual-comments"></a>Comentários manuais

* ```/* This is my comment */```

* ```/* This is a```
*   ```multi-line comment */```
   
* ```// This is a single line comment```

Se você colocar um comentário na parte superior da expressão, ele será exibido na caixa de texto transformação para documentar suas expressões de transformação:

![Comentários](media/data-flow/comments2.png "Comentários")

## <a name="convert-to-dates-or-timestamps"></a>Converter em datas ou carimbos de data/hora

```toString(toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss'), 'MM/dd /yyyy\'T\'HH:mm:ss')```

Observe que, para incluir literais de cadeia de caracteres em sua saída de carimbo de data/hora, você precisa encapsular a conversão dentro de ```toString()```.

Aqui está como converter segundos de época em uma data ou carimbo de hora:

```toTimestamp(1574127407*1000l)```

Observe o "l" à direita no final da expressão acima. Isso significa conversão para tempo como sintaxe em linha.

## <a name="handling-column-names-with-special-characters"></a>Manipulando nomes de coluna com caracteres especiais

Quando você tem nomes de coluna que incluem caracteres especiais ou espaços, coloque o nome entre chaves.

```{[dbo].this_is my complex name$$$}```

## <a name="next-steps"></a>Passos seguintes

[Iniciar a criação de expressões de transformação de dados](data-flow-expression-functions.md)
