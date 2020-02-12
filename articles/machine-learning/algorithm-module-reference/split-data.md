---
title: 'Dividir dados: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo dividir dados em Azure Machine Learning para dividir um conjunto de dado em dois conjuntos distintos.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 3e831e58b47d53e2924956cab13568c69bc1432e
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153745"
---
# <a name="split-data-module"></a>Módulo dividir dados

Este artigo descreve um módulo em Azure Machine Learning designer (pré-visualização).

Use este módulo para dividir um conjunto de módulos em dois conjuntos distintos.

Esse módulo é particularmente útil quando você precisa separar dados em conjuntos de treinamento e teste. Você pode personalizar a maneira como os dados são divididos também. Algumas opções dão suporte à randomização de dados; outras são adaptadas para um determinado tipo de dados ou tipo de modelo.

## <a name="how-to-configure"></a>Como configurar

> [!TIP]
> Antes de escolher o modo de divisão, Leia todas as opções para determinar o tipo de divisão de que você precisa.
> Se você alterar o modo de divisão, todas as outras opções poderão ser redefinidas.

1. Adicione o módulo **Split Data** ao seu pipeline no designer. Pode encontrar este módulo na categoria De recolha de **dados,** na categoria **Amostra e Divisão.**

2. **Modo de divisão**: Escolha um dos seguintes modos, dependendo do tipo de dados que tem e de como quer dividi-lo. Cada modo de divisão tem opções diferentes. Clique nos tópicos a seguir para obter instruções e exemplos detalhados. 

    - **Split Rows**: Utilize esta opção se pretender dividir os dados em duas partes. Você pode especificar a porcentagem de dados a serem colocados em cada divisão, mas por padrão, os dados são divididos 50-50.

        Você também pode tornar aleatória a seleção de linhas em cada grupo e usar a amostragem de sobreratificação. Em amostragem de sobreratificação, você deve selecionar uma única coluna de dados para a qual você deseja que os valores sejam distribuídos igualmente entre os dois conjuntos de dado de resultado.  

    - **Divisão de Expressão Regular**  Escolha esta opção quando pretender dividir o seu conjunto de dados testando uma única coluna por um valor.

        Por exemplo, se você estiver analisando sentimentos, poderá verificar a presença de um nome de produto específico em um campo de texto e, em seguida, dividir o conjunto de dado em linhas com o nome do produto de destino e aqueles sem.

    - **Divisão de Expressão Relativa**: Utilize esta opção sempre que pretender aplicar uma condição a uma coluna de números. O número pode ser um campo de data/hora, uma coluna que contém valores de idade ou moeda, ou até mesmo uma porcentagem. Por exemplo, talvez você queira dividir seu conjunto de dados dependendo do custo dos itens, agrupar pessoas por intervalos de idade ou separar dados por uma data de calendário.

### <a name="split-rows"></a>Linhas divididas

1.  Adicione o módulo [De dados divididos](./split-data.md) ao seu pipeline no designer e ligue o conjunto de dados que pretende dividir.
  
2.  Para o **modo de divisão,** escolha **as linhas split**. 

3.  **Fração de linhas no primeiro conjunto de dados de saída**. Use esta opção para determinar quantas linhas vão para a primeira saída (à esquerda). Todas as outras linhas vão para a segunda saída (à direita).

    A taxa representa a porcentagem de linhas enviadas para o primeiro conjunto de resultados de saída, portanto, você deve digitar um número decimal entre 0 e 1.
     
     Por exemplo, se escrever 0,75 como valor, o conjunto de dados seria dividido utilizando uma razão de 75:25, com 75% das linhas enviadas para o primeiro conjunto de dados de saída, e 25% enviadas para o segundo conjunto de dados de saída.
  
4. Selecione a opção **de divisão aleatória** se pretender aleatoriamente a seleção de dados nos dois grupos. Essa é a opção preferida ao criar conjuntos de testes de treinamento e de teste.

5.  **Semente aleatória**: Digite um valor inteiro não negativo para inicializar a sequência pseudoaleatória de instâncias a utilizar. Essa semente padrão é usada em todos os módulos que geram números aleatórios. 

     A especificação de uma semente torna os resultados geralmente reproduzíveis. Se você precisar repetir os resultados de uma operação de divisão, deverá especificar uma semente para o gerador de número aleatório. Caso contrário, a semente aleatória é definida por padrão como 0, o que significa que o valor de semente inicial é obtido do relógio do sistema. Como resultado, a distribuição de dados pode ser um pouco diferente cada vez que você executar uma divisão. 

6. **Divisão estratificada**: Desreserve esta opção **para** true para garantir que os dois conjuntos de dados de saída contenham uma amostra representativa dos valores na coluna de *estratos* ou na coluna da chave de *estratificação*. 

    Com a amostragem de sobreratificação, os dados são divididos de forma que cada conjunto de dado de saída Obtém aproximadamente a mesma porcentagem de cada valor de destino. Por exemplo, talvez você queira garantir que seus conjuntos de treinamento e teste sejam aproximadamente equilibrados em relação ao resultado, ou em relação a alguma outra coluna, como sexo.

7. Executar o pipeline.


## <a name="regular-expression-split"></a>Divisão de expressão regular

1.  Adicione o módulo [De dados divididos](./split-data.md) ao seu pipeline e conectem-no como entrada ao conjunto de dados que pretende dividir.  
  
2.  Para **o modo de divisão,** selecione a divisão de expressão **regular**.

3. Na caixa de **expressão regular,** digite uma expressão regular válida. 
  
   A expressão regular deve seguir a sintaxe de expressão regular do Python.


4. Executar o pipeline.

    Com base na expressão regular que você fornece, o conjunto de registros é dividido em dois conjuntos de linhas: linhas com valores que correspondem à expressão e todas as linhas restantes. 

## <a name="relative-expression-split"></a>Divisão de expressão relativa.

1. Adicione o módulo [De dados divididos](./split-data.md) ao seu pipeline e conectem-no como entrada ao conjunto de dados que pretende dividir.
  
2. Para **o modo de divisão,** selecione divisão de expressão **relativa**.
  
3. Na caixa de texto de **expressão relacional,** digite uma expressão que execute uma operação de comparação, numa única coluna:


 - Coluna numérica:
    - A coluna contém números de qualquer tipo de dados numéricos, incluindo tipos de dados de data/hora.

    - A expressão pode fazer referência a um máximo de um nome de coluna.

    - Use o caractere de e comercial (&) para a operação e e use o caractere de barra vertical (|) para a operação ou.

    - Os seguintes operadores são apoiados: `<`, `>`, `<=`, `>=`, `==`, `!=`

    - Não é possível agrupar operações utilizando `(` e `)`.

 - Coluna de cadeia de caracteres: 
    - São apoiados os seguintes operadores: `==`, `!=`



4. Executar o pipeline.

    A expressão divide o conjunto de um em dois conjuntos de linhas: linhas com valores que atendem à condição e todas as linhas restantes.

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 