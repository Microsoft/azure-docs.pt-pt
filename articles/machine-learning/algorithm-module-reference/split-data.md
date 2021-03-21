---
title: 'Dados divididos: referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo de Dados Divididos em Azure Machine Learning para dividir um conjunto de dados em dois conjuntos distintos.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: a4c93b12ad654e54a7f3c7ee0e75507d5cb45e90
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "90907817"
---
# <a name="split-data-module"></a>Módulo de dados divididos

Este artigo descreve um módulo no designer de Aprendizagem automática Azure.

Utilize o módulo de dados divididos para dividir um conjunto de dados em dois conjuntos distintos.

Este módulo é útil quando é necessário separar os dados em conjuntos de treino e teste. Também pode personalizar a forma como os dados são divididos. Algumas opções suportam a aleatoriedade de dados. Outros são adaptados para um determinado tipo de dados ou tipo de modelo.

## <a name="configure-the-module"></a>Configure o módulo

> [!TIP]
> Antes de escolher o modo de divisão, leia todas as opções para determinar o tipo de divisão de que necessita.
> Se alterar o modo de divisão, todas as outras opções poderão ser reiniciadas.

1. Adicione o módulo **de Dados Divididos** ao seu pipeline no designer. Pode encontrar este módulo na categoria **Data Transformation,** na categoria **Amostra e Divisão.**

1. **Modo de divisão**: Escolha um dos seguintes modos, dependendo do tipo de dados que tem e da forma como pretende dividi-lo. Cada modo de divisão tem diferentes opções.

   - **Linhas Divididas**: Utilize esta opção se quiser dividir os dados em duas partes. Pode especificar a percentagem de dados a colocar em cada divisão. Por predefinição, os dados são divididos 50/50.

     Também pode aleatoriamente a seleção de linhas em cada grupo e utilizar amostragem estratificada. Na amostragem estratificada, deve selecionar uma única coluna de dados para a qual pretende que os valores sejam repartidos igualmente entre os dois conjuntos de dados de resultados.  

   - **Divisão de Expressão Regular**: Escolha esta opção quando pretende dividir o conjunto de dados testando uma única coluna por um valor.

     Por exemplo, se estiver a analisar sentimentos, pode verificar a presença de um nome de produto em particular num campo de texto. Em seguida, pode dividir o conjunto de dados em linhas com o nome do produto-alvo e linhas sem o nome do produto alvo.

   - **Divisão de Expressão Relativa**: Utilize esta opção sempre que pretenda aplicar uma condição a uma coluna numéria. O número pode ser um campo de data/hora, uma coluna que contenha quantidades de idade ou dólar, ou mesmo uma percentagem. Por exemplo, pode querer dividir o seu conjunto de dados com base no custo dos itens, grupo de pessoas por faixas etárias ou dados separados por uma data de calendário.

### <a name="split-rows"></a>Linhas divididas

1. Adicione o módulo [de Dados Divididos](./split-data.md) ao seu pipeline no designer e ligue o conjunto de dados que pretende dividir.
  
1. Para **o modo de divisão**, selecione Split **Rows**. 

1. **Fração de linhas no primeiro conjunto de dados de saída**: Utilize esta opção para determinar quantas linhas irão para a primeira saída (do lado esquerdo). Todas as outras linhas irão para a segunda saída (lado direito).

   O rácio representa a percentagem de linhas enviadas para o primeiro conjunto de dados de saída, pelo que deve introduzir um número decimal entre 0 e 1.
     
   Por exemplo, se introduzir **0,75** como valor, o conjunto de dados será dividido 75/25. Nesta divisão, 75% das linhas serão enviadas para o primeiro conjunto de dados de saída. Os restantes 25% serão enviados para o segundo conjunto de dados de saída.
  
1. Selecione a opção **split randomized** se quiser aleatoriamente a seleção de dados para os dois grupos. Esta é a opção preferida quando se cria conjuntos de dados de treino e teste.

1. **Semente aleatória**: Introduza um valor inteiro não negativo para iniciar a sequência de pseudorandom de instâncias a utilizar. Esta semente predefinida é usada em todos os módulos que geram números aleatórios. 

   Especificar uma semente torna os resultados reprodutíveis. Se precisar de repetir os resultados de uma operação dividida, deve especificar uma semente para o gerador de números aleatórios. Caso contrário, a semente aleatória é definida por defeito a **0**, o que significa que o valor inicial das sementes é obtido a partir do relógio do sistema. Como resultado, a distribuição de dados pode ser ligeiramente diferente cada vez que executa uma divisão. 

1. **Divisão estratificada**: Defina esta opção para **True** para garantir que os dois conjuntos de dados de saída contenham uma amostra representativa dos valores na coluna de *estratos* ou *coluna-chave de estratificação*. 

   Com a amostragem estratificada, os dados são divididos de modo a que cada conjunto de dados de saída obtenha aproximadamente a mesma percentagem de cada valor-alvo. Por exemplo, é melhor garantir que os seus conjuntos de treino e testes são aproximadamente equilibrados no que diz respeito ao resultado ou a alguma outra coluna (como o género).

1. Envie o oleoduto.


## <a name="select-a-regular-expression"></a>Selecione uma expressão regular

1. Adicione o módulo [de Dados Divididos](./split-data.md) ao seu pipeline e conecte-o como entrada ao conjunto de dados que pretende dividir.  
  
1. Para **o modo de divisão**, selecione **'Repartição de expressão regular'.**

1. Na caixa **de expressão regular,** introduza uma expressão regular válida. 
  
   A expressão regular deve seguir a sintaxe Python para expressões regulares.

1. Envie o oleoduto.

   Com base na expressão regular que fornece, o conjunto de dados é dividido em dois conjuntos de linhas: linhas com valores que correspondem à expressão e todas as restantes linhas. 

Os exemplos a seguir demonstram como dividir um conjunto de dados utilizando a opção **de expressão regular.** 

### <a name="single-whole-word"></a>Única palavra inteira 

Este exemplo coloca no primeiro conjunto de dados todas as linhas que contêm o texto `Gryphon` na coluna `Text` . Coloca outras linhas na segunda saída de **Split Data**.

```text
    \"Text" Gryphon  
```

### <a name="substring"></a>Sub-corda

Este exemplo procura a cadeia especificada em qualquer posição dentro da segunda coluna do conjunto de dados. A posição é denotada aqui pelo valor de índice de 1. A partida é sensível a casos.

```text
(\1) ^[a-f]
```

O primeiro conjunto de dados de resultados contém todas as linhas onde a coluna de índice começa com um destes caracteres: `a` , , , , , . `b` `c` `d` `e` `f` . Todas as outras linhas são direcionadas para a segunda saída.

## <a name="select-a-relative-expression"></a>Selecione uma expressão relativa

1. Adicione o módulo [de Dados Divididos](./split-data.md) ao seu pipeline e conecte-o como entrada ao conjunto de dados que pretende dividir.
  
1. Para **o modo de divisão**, selecione Expressão **Relativa**.
  
1. Na caixa **de expressão relacional,** introduza uma expressão que execute uma operação de comparação numa única coluna.

   Para **coluna numérica:**
   - A coluna contém números de qualquer tipo de dados numéricos, incluindo tipos de dados de data e hora.
   - A expressão pode referenciar um máximo de um nome de coluna.
   - Utilize o carácter ampersand, `&` para o funcionamento e. Utilize o caractere do `|` tubo, para o funcionamento do BO.
   - Os seguintes operadores são apoiados: `<` , , , , `>` `<=` `>=` `==` . `!=`
   - Não se pode agrupar operações usando `(` e `)` .
   
   Para **a coluna string:**
   - São apoiados os seguintes operadores: `==` `!=` . .

1. Envie o oleoduto.

   A expressão divide o conjunto de dados em dois conjuntos de linhas: linhas com valores que vão ao encontro da condição, e todas as restantes linhas.

Os exemplos a seguir demonstram como dividir um conjunto de dados utilizando a opção **Expressão Relativa** no módulo **Dados Divididos.**  

### <a name="calendar-year"></a>Ano civil

Um cenário comum é dividir um conjunto de dados por anos. A expressão a seguir seleciona todas as linhas onde os valores na coluna `Year` são maiores do que `2010` .

```text
\"Year" > 2010
```

A expressão da data deve ter em conta todas as partes da data incluídas na coluna de dados. O formato das datas na coluna de dados deve ser consistente. 

Por exemplo, numa coluna de datas que utiliza o `mmddyyyy` formato, a expressão deve ser algo assim:

```text
\"Date" > 1/1/2010
```

### <a name="column-index"></a>Índice de coluna

A expressão a seguir demonstra como pode utilizar o índice da coluna para selecionar todas as linhas na primeira coluna do conjunto de dados que contenham valores inferiores ou iguais a 30, mas não iguais a 20.

```text
(\0)<=30 & !=20
```


## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 
