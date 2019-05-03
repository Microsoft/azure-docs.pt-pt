---
title: 'Dividir os dados: Referência do módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como utilizar o módulo de dividir dados no serviço Azure Machine Learning para dividir um conjunto de dados em dois conjuntos distintos.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: a7395280ed9a2e9dcb94a081f0b3bf10a28da719
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029404"
---
# <a name="split-data-module"></a>Módulo split Data

Este artigo descreve um módulo da interface visual (pré-visualização) para o serviço Azure Machine Learning.

Utilize este módulo para dividir um conjunto de dados em dois conjuntos distintos.

Este módulo é particularmente útil quando precisa separar dados em preparação e teste conjuntos. Pode personalizar a forma que os dados são divididos também. Algumas opções de suportam a randomização de dados. outros são adaptados para um determinado tipo de dados ou tipo de modelo.

## <a name="how-to-configure"></a>Como configurar

> [!TIP]
> Antes de escolher o modo divisão, todas as opções para determinar o tipo de divisão de leitura, precisa.
> Se alterar o modo divisão, todas as outras opções foi repostas.

1. Adicionar a **Split Data** módulo à sua experimentação na interface. Pode encontrar este módulo sob **transformação de dados**, na **exemplo e divisão** categoria.

2. **A divisão de modo**: Escolha um dos seguintes modos, dependendo do tipo de dados que tiver e como deseja dividi-la. Cada modo divisão tem opções diferentes. Clique nos seguintes tópicos para obter instruções detalhadas e exemplos. 

    - **Dividir linhas**: Utilize esta opção se pretender dividir os dados em duas partes. Pode especificar a percentagem de dados para colocar em cada divisão, mas por predefinição, os dados são divididos 50 a 50.

        Também pode tornar aleatórios a seleção de linhas em cada grupo e usar stratified amostragem. Amostragem stratified, tem de selecionar uma única coluna de dados para o qual pretende que os valores a ser distribuídos de acordo com igualmente entre os conjuntos de dados de duas resultado.  

    - **Divisão de expressão regular** Escolha esta opção quando pretender dividir o conjunto de dados por uma única coluna para um valor de teste.

        Por exemplo, se analisar sentimentos, foi possível verificar a presença de um nome de produto em particular num campo de texto e, em seguida, dividir o conjunto de dados em linhas com o nome de produto de destino e pessoas sem.

    - **Divisão de expressão relativo**:  Utilize esta opção sempre que pretender aplicar uma condição para uma coluna do número. O número pode ser um campo de data/hora, um coluna que contém idade ou quantidades de dólar ou até mesmo uma percentagem. Por exemplo, talvez queira dividir seu conjunto de dados consoante o custo dos itens, grupo de pessoas por intervalos de idade ou dados separado por uma data de calendário.

### <a name="split-rows"></a>Linhas de divisão
1.  Adicionar a [Split Data](./split-data.md) módulo à sua experimentação na interface e ligue-se o conjunto de dados que pretende dividir.
  
2.  Para **modo de separação**, escolha **Dividir linhas**. 

3.  **Fração de linhas no primeiro conjunto de dados de saída**. Utilize esta opção para determinar o número de linhas vai para o primeiro resultado (lado esquerdo). Todas as outras linhas passará para a segunda saída (direita).

    O rácio representa a porcentagem das linhas enviada para o primeiro conjunto de dados de saída, tem de introduzir um número decimal entre 0 e 1.
     
     Por exemplo, se digitar 0,75 como o valor, o conjunto de dados poderia ser dividido utilizando um rácio de 75:25 com 75% das linhas enviadas para o primeiro conjunto de dados de saída e 25% enviados para o segundo conjunto de dados de saída.
  
4. Selecione o **Randomized split** opção se pretender tornar aleatórios a seleção de dados em dois grupos. Esta é a opção preferencial, durante a criação de conjuntos de dados de preparação e teste.

5.  **Seed Aleatório**: Escreva um valor de número inteiro não negativo para inicializar a sequência de pseudoaleatórios das instâncias para ser utilizado. Seed este padrão é usado em todos os módulos que geram números aleatórios. 

     Especificar uma semente torna os resultados geralmente reproduzível. Se precisar repetir os resultados de uma operação de divisão, deve especificar uma semente para o gerador de números aleatórios. Caso contrário, o seed aleatório é definido por predefinição como 0, o que significa que o valor semente inicial é obtido a partir do relógio do sistema. Como resultado, a distribuição de dados poderão ser ligeiramente diferentes sempre que executar uma divisão. 

6. **Divisão stratified**: Defina esta opção como **True** para garantir que os conjuntos de dados de dois saída contenham uma amostra representativa dos valores a *coluna strata* ou *coluna de chave stratification*. 

    Com a amostragem stratified, os dados são divididos de forma a que cada conjunto de dados de saída é aproximadamente o mesmo valor de percentagem de cada destino. Por exemplo, poderá querer garantir que seu treinamento e conjuntos de testes são balanceadas mais ou menos em relação ao resultado ou com considerar ou alguma outra coluna, como género.

7. Execute a experimentação.


## <a name="regular-expression-split"></a>Divisão de expressão regular

1.  Adicionar a [Split Data](./split-data.md) módulo à sua experimentação e ligue-o como entrada para o conjunto de dados que pretende dividir.  
  
2.  Para **modo de separação**, selecione **divisão da expressão Regular**.

3. Na **expressão Regular** , escreva uma expressão regular válida. 
  
   A expressão regular deve seguir a sintaxe de expressão regular de Python.


4. Execute a experimentação.

    Com base na expressão regular é fornecer, o conjunto de dados está dividido em dois conjuntos de linhas: linhas com valores que correspondem a expressão e todas as linhas restantes. 

## <a name="relative-expression-split"></a>Divisão de expressão relativo.

1. Adicionar a [Split Data](./split-data.md) módulo à sua experimentação e ligue-o como entrada para o conjunto de dados que pretende dividir.
  
2. Para **modo de separação**, selecione **divisão da expressão relativo**.
  
3. Na **expressão relacional** , digite uma expressão que realiza uma operação de comparação, numa única coluna:


 - Coluna numérica:
    - A coluna contenha números de qualquer tipo de dados numéricos, incluindo tipos de dados de data/hora.

    - A expressão pode fazer referência a um máximo de nome de uma coluna.

    - Utilizar o caráter ' e ' comercial (&) para a operação AND e a utilização do pipe de caracteres (|) para a operação OR.

    - São suportados os seguintes operadores: `<`, `>`, `<=`, `>=`, `==`, `!=`

    - Não é possível grupo operações usando `(` e `)`.

 - Coluna de cadeia de caracteres: 
    - São suportados os seguintes operadores: `==`, `!=`



4. Execute a experimentação.

    A expressão divide o conjunto de dados em dois conjuntos de linhas: linhas com valores que satisfazem a condição e todas as linhas restantes.

## <a name="next-steps"></a>Passos Seguintes

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço Azure Machine Learning. 