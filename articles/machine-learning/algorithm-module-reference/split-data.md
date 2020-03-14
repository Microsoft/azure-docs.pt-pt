---
title: 'Dados divididos: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo Split Data em Azure Machine Learning para dividir um conjunto de dados em dois conjuntos distintos.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: b705b342708a038a7f3d8c15d6a4298e9fc17c75
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/14/2020
ms.locfileid: "79369816"
---
# <a name="split-data-module"></a>Módulo de Dados Divididos

Este artigo descreve um módulo em Azure Machine Learning designer (pré-visualização).

Utilize o módulo De dados divididos para dividir um conjunto de dados em dois conjuntos distintos.

Este módulo é útil quando precisa de separar os dados em conjuntos de treino e teste. Também pode personalizar a forma como os dados são divididos. Algumas opções suportam a aleatoriedade de dados. Outros são adaptados para um determinado tipo de tipo de dados ou modelo.

## <a name="configure-the-module"></a>Configure o módulo

> [!TIP]
> Antes de escolher o modo de divisão, leia todas as opções para determinar o tipo de divisão que necessita.
> Se alterar o modo de divisão, todas as outras opções poderão ser redefinidas.

1. Adicione o módulo **Split Data** ao seu pipeline no designer. Pode encontrar este módulo na categoria De recolha de **dados,** na categoria **Amostra e Divisão.**

1. **Modo de divisão**: Escolha um dos seguintes modos, dependendo do tipo de dados que tem e de como quer dividi-lo. Cada modo de divisão tem opções diferentes.

   - **Split Rows**: Utilize esta opção se pretender dividir os dados em duas partes. Pode especificar a percentagem de dados a colocar em cada divisão. Por padrão, os dados são divididos 50/50.

     Também pode aleatoriamente a seleção de linhas em cada grupo e utilizar amostras estratificadas. Na amostragem estratificada, deve selecionar uma única coluna de dados para os quais pretende que os valores sejam distribuídos igualmente entre os dois conjuntos de dados de resultados.  

   - **Divisão de Expressão Regular**: Escolha esta opção quando pretender dividir o seu conjunto de dados testando uma única coluna por um valor.

     Por exemplo, se estiver a analisar o sentimento, pode verificar a presença de um determinado nome de produto num campo de texto. Em seguida, pode dividir o conjunto de dados em linhas com o nome e linhas do produto-alvo sem o nome do produto-alvo.

   - **Divisão de Expressão Relativa**: Utilize esta opção sempre que pretender aplicar uma condição a uma coluna de números. O número pode ser um campo de data/hora, uma coluna que contenha valores de idade ou dólar, ou mesmo uma percentagem. Por exemplo, pode querer dividir o seu conjunto de dados com base no custo dos itens, emgrupo de pessoas por faixas etárias ou dados separados por uma data de calendário.

### <a name="split-rows"></a>Linhas divididas

1. Adicione o módulo [De dados divididos](./split-data.md) ao seu pipeline no designer e ligue o conjunto de dados que pretende dividir.
  
1. Para **o modo de divisão,** selecione Split **Rows**. 

1. **Fração de linhas no primeiro conjunto de dados**de saída: Utilize esta opção para determinar quantas linhas irão para a primeira saída (do lado esquerdo). Todas as outras linhas irão para a segunda saída (lado direito).

   O rácio representa a percentagem de linhas enviadas para o primeiro conjunto de dados de saída, pelo que deve introduzir um número decimal entre 0 e 1.
     
   Por exemplo, se introduzir **0,75** como valor, o conjunto de dados será dividido 75/25. Nesta divisão, 75% das linhas serão enviadas para o primeiro conjunto de dados de saída. Os restantes 25% serão enviados para o segundo conjunto de dados de saída.
  
1. Selecione a opção **de divisão aleatória** se pretender aleatoriamente a seleção de dados nos dois grupos. Esta é a opção preferida quando se está a criar conjuntos de dados de treino e de teste.

1. **Semente aleatória**: Introduza um valor inteiro não negativo para iniciar a sequência pseudoaleatória de instâncias a utilizar. Esta semente padrão é usada em todos os módulos que geram números aleatórios. 

   Especificar uma semente torna os resultados reprodutíveis. Se precisar repetir os resultados de uma operação dividida, deve especificar uma semente para o gerador de números aleatórios. Caso contrário, a semente aleatória é definida por predefinição a **0,** o que significa que o valor inicial das sementes é obtido a partir do relógio do sistema. Como resultado, a distribuição de dados pode ser ligeiramente diferente cada vez que executa uma divisão. 

1. **Divisão estratificada**: Desreserve esta opção **para** true para garantir que os dois conjuntos de dados de saída contenham uma amostra representativa dos valores na coluna de *estratos* ou na coluna da chave de *estratificação*. 

   Com amostragem estratificada, os dados são divididos de modo a que cada conjunto de dados de saída obtenha aproximadamente a mesma percentagem de cada valor-alvo. Por exemplo, é possível garantir que os seus conjuntos de treino e teste são aproximadamente equilibrados no que diz respeito ao resultado ou a outra coluna (como o género).

1. Executar o pipeline.


## <a name="select-a-regular-expression"></a>Selecione uma expressão regular

1. Adicione o módulo [De dados divididos](./split-data.md) ao seu pipeline e conectem-no como entrada ao conjunto de dados que pretende dividir.  
  
1. Para **o modo de divisão,** selecione a divisão de expressão **regular**.

1. Na caixa de **expressão regular,** introduza uma expressão regular válida. 
  
   A expressão regular deve seguir a sintaxe python para expressões regulares.

1. Executar o pipeline.

   Com base na expressão regular que fornece, o conjunto de dados é dividido em dois conjuntos de linhas: linhas com valores que correspondem à expressão e a todas as linhas restantes. 

Os exemplos seguintes demonstram como dividir um conjunto de dados utilizando a opção de **expressão regular.** 

### <a name="single-whole-word"></a>Palavra inteira única 

Este exemplo coloca no primeiro conjunto de dados todas as linhas que contêm o texto `Gryphon` na coluna `Text`. Coloca outras linhas na segunda saída de **Dados Divididos.**

```text
    \"Text" Gryphon  
```

### <a name="substring"></a>substring

Este exemplo procura a cadeia especificada em qualquer posição dentro da segunda coluna do conjunto de dados. A posição é denotada aqui pelo valor do índice de 1. A correspondência é sensível ao caso.

```text
(\1) ^[a-f]
```

O conjunto de dados do primeiro resultado contém todas as linhas onde a coluna de índice saem com um destes caracteres: `a`, `b`, `c`, `d`, `e`, `f`. Todas as outras linhas são direcionadas para a segunda saída.

## <a name="select-a-relative-expression"></a>Selecione uma expressão relativa

1. Adicione o módulo [De dados divididos](./split-data.md) ao seu pipeline e conectem-no como entrada ao conjunto de dados que pretende dividir.
  
1. Para **o modo de divisão,** selecione Expressão **Relativa**.
  
1. Na caixa de **expressão Relacional,** introduza uma expressão que execute uma operação de comparação numa única coluna.

   Para **coluna numérica:**
   - A coluna contém números de qualquer tipo de dados numéricos, incluindo tipos de dados de data e hora.
   - A expressão pode referenciar um máximo de um nome de coluna.
   - Utilize o caráter ampersand, `&`, para a operação E. Utilize o caracteres do tubo, `|`, para a operação OR.
   - São apoiados os seguintes operadores: `<`, `>`, `<=`, `>=`, `==`, `!=`.
   - Não se pode agrupar operações usando `(` e `)`.
   
   Para **a coluna String:**
   - São apoiados os seguintes operadores: `==`, `!=`.

1. Executar o pipeline.

   A expressão divide o conjunto de dados em dois conjuntos de linhas: linhas com valores que cumprem a condição, e todas as linhas restantes.

Os exemplos seguintes demonstram como dividir um conjunto de dados utilizando a opção **expressão relativa** no módulo De **dados divididos.**  

### <a name="calendar-year"></a>Ano civil

Um cenário comum é dividir um conjunto de dados por anos. A expressão seguinte seleciona todas as linhas onde os valores da coluna `Year` são superiores a `2010`.

```text
\"Year" > 2010
```

A expressão da data deve ser responsável por todas as partes da data incluídas na coluna de dados. O formato das datas na coluna de dados deve ser consistente. 

Por exemplo, numa coluna de data que usa o formato `mmddyyyy`, a expressão deve ser algo assim:

```text
\"Date" > 1/1/2010
```

### <a name="column-index"></a>Índice de colunas

A seguinte expressão demonstra como pode usar o índice de colunas para selecionar todas as linhas na primeira coluna do conjunto de dados que contenham valores inferiores ou iguais a 30, mas não iguais a 20.

```text
(\0)<=30 & !=20
```


## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 
