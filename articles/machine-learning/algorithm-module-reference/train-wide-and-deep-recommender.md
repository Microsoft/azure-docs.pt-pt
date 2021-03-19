---
title: Utilize o módulo de recomendador & profundo do comboio
titleSuffix: Azure Machine Learning
description: Saiba como utilizar o módulo Train Wide & Deep Recommender no Azure Machine Learning designer para treinar um modelo de recomendação.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 06/12/2020
ms.openlocfilehash: d7dd7105ddb0d6503faefb996b84c0e53a62ce49
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104655381"
---
# <a name="train-wide--deep-recommender"></a>Train Wide & Recomendador Profundo
Este artigo descreve como usar o módulo **Train Wide & Deep Recommender** no Azure Machine Learning designer, para treinar um modelo de recomendação. Este módulo é baseado em Wide & Deep learning, que é proposto pela Google.

O módulo **Train Wide & Deep Recommender** lê um conjunto de dados de triplos de classificação de artigos de utilizador e, opcionalmente, algumas funcionalidades de utilizador e item. Devolve um recomendador de wide & Deep treinado.  Em seguida, pode utilizar o modelo treinado para gerar previsões ou recomendações de classificação utilizando o módulo [Score Wide e Deep Recommender.](score-wide-and-deep-recommender.md)  

## <a name="more-about-recommendation-models-and-the-wide--deep-recommender"></a>Mais sobre modelos de recomendação e o grande recomendador & Deep  

O principal objetivo de um sistema de recomendação é recomendar um ou mais *itens* aos *utilizadores* do sistema. Exemplos de um item podem ser um filme, restaurante, livro ou música. Um utilizador pode ser uma pessoa, um grupo de pessoas ou outra entidade com preferências de item.  

Existem duas abordagens principais para os sistemas de recomendação. 

+ A primeira é a abordagem **baseada em conteúdo,** que faz uso de funcionalidades tanto para utilizadores como para itens. Os utilizadores podem ser descritos por propriedades como idade e sexo, e itens podem ser descritos por propriedades como autor e fabricante. Exemplos típicos de sistemas de recomendação baseados em conteúdo podem ser encontrados em sites de matchmaking sociais. 
+ A segunda abordagem é **a filtragem colaborativa,** que utiliza apenas identificadores dos utilizadores e dos itens e obtém informações implícitas sobre estas entidades a partir de uma matriz (escassa) de classificações dadas pelos utilizadores aos itens. Podemos aprender sobre um utilizador a partir dos itens que avaliaram e de outros utilizadores que avaliaram os mesmos itens.  

O recomendador Wide & Deep combina estas abordagens, utilizando filtragem colaborativa com uma abordagem baseada em conteúdo. É, portanto, considerado um **recomendador híbrido.** 

Como isto funciona: Quando um utilizador é relativamente novo no sistema, as previsões são melhoradas utilizando a informação da funcionalidade sobre o utilizador, abordando assim o conhecido problema do "arranque a frio". No entanto, uma vez recolhido um número suficiente de classificações de um determinado utilizador, é possível fazer previsões totalmente personalizadas para eles com base nas suas classificações específicas e não apenas nas suas funcionalidades. Assim, há uma transição suave das recomendações baseadas em conteúdo para recomendações baseadas na filtragem colaborativa. Mesmo que as funcionalidades de utilizador ou item não estejam disponíveis, o recomendador Wide & Deep continuará a funcionar no seu modo de filtragem colaborativo.  

Mais detalhes sobre o Grande & Recomendador Profundo e o seu algoritmo probabilístico subjacente podem ser encontrados no artigo de investigação relevante: [Wide & Deep Learning for Recommender Systems](https://arxiv.org/pdf/1606.07792.pdf).  

## <a name="how-to-configure-train-wide--deep-recommender"></a>Como configurar o comboio wide & recomendador profundo  

+ [Preparar os dados de formação](#prepare-data)
+ [Preparar o modelo](#train-the-model)

### <a name="prepare-data"></a>Preparar dados

Antes de tentar utilizar o módulo, é essencial que os seus dados estejam no formato esperado pelo modelo de recomendação. É necessário um conjunto de dados de formação de **triplos de classificação de item do utilizador,** mas também pode incluir funcionalidades do utilizador e funcionalidades de item (se disponível), em conjuntos de dados separados.

#### <a name="required-dataset-of-user-item-ratings"></a>Conjunto de dados necessário das classificações de artigos de utilizador

Os dados de entrada utilizados para a formação devem conter o tipo certo de dados no formato correto: 

+ A primeira coluna deve conter identificadores de utilizador.
+ A segunda coluna deve conter identificadores de item.
+ A terceira coluna contém a classificação para o par de artigos de utilizador. Os valores de classificação devem ser do tipo numérico. 

Por exemplo, um conjunto típico de classificações de artigos de utilizador pode ser assim:

|IDUtilizador|MovieId|Classificação|
|------------|-------------|------------|
|1|68646|10|
|223|31381|10|

#### <a name="user-features-dataset-optional"></a>Conjunto de dados de recursos do utilizador (opcional)

O conjunto de dados das **funcionalidades** do utilizador deve conter identificadores para os utilizadores e utilizar os mesmos identificadores que foram fornecidos na primeira coluna do conjunto de dados de classificações de itens de utilizadores. As colunas restantes podem conter qualquer número de funcionalidades que descrevam os utilizadores.  

Por exemplo, um conjunto típico de funcionalidades do utilizador pode ser assim: 

|IDUtilizador|Idade|Sexo|Juros|Localização|
|------------|--------------|-----------------------|---------------|------------|
|1|25|masculino| Drama    |Europa|
|223|40|feminino|Romance|Ásia|

#### <a name="item-features-dataset-optional"></a>O conjunto de dados (opcional)

O conjunto de dados das características do item deve conter identificadores de item na sua primeira coluna. As colunas restantes podem conter qualquer número de funcionalidades descritivas para os itens.  

Por exemplo, um conjunto típico de funcionalidades de item pode ser assim:  

|MovieId|Título|Língua original|Géneros|Anual|
|-------------|-------------|-------------------|-----------|---------------|
|68646|O Padrinho|Inglês|Drama|1972|
|31381|Foi-se com o Vento|Inglês|Histórico|1939|

### <a name="train-the-model"></a>Preparar o modelo

1.  Adicione o módulo **Train Wide e Deep Recommender** à sua experiência no designer e conecte-o ao conjunto de dados de treino.  
  
2. Se tiver um conjunto de dados separado de funcionalidades do utilizador e/ou funcionalidades de produto, conecte-os ao módulo **Train Wide e Deep Recommender.**  
  
    - **Conjunto de dados de funcionalidades do utilizador**: Ligue o conjunto de dados que descreve os utilizadores à segunda entrada.
    - **Conjunto de dados de recursos** do item : Ligue o conjunto de dados que descreve os itens à terceira entrada.  
    
3.  **Épocas**: indicar quantas vezes o algoritmo deve processar todos os dados de treino. 

    Quanto maior este número, mais adequado é a formação; no entanto, a formação custa mais tempo e pode causar uma sobremontagem.

4. **Tamanho do lote**: digite o número de exemplos de treino utilizados num único passo de treino. 

     Este hiperparmetro pode influenciar a velocidade de treino. Um tamanho de lote mais alto leva a uma época de custos de tempo menos tempo, mas pode aumentar o tempo de convergência. E se o lote for demasiado grande para caber GPU/CPU, um erro de memória pode ser levantado.

5.  **Otimizador de partes largas**: selecione um optimizador para aplicar gradientes à parte larga do modelo.

6.  **Taxa de aprendizagem otimizadora larga**: insira um número entre 0.0 e 2.0 que define a taxa de aprendizagem do otimizador de larga parte.

    Este hiperparmetro determina o tamanho do passo em cada passo de treino enquanto se move em direção a uma função mínima de perda. Uma taxa de aprendizagem demasiado grande pode causar um salto de aprendizagem sobre o minima, enquanto uma taxa de aprendizagem muito pequena pode causar problemas de convergência.

7.  **Dimensão de característica cruzada**: digite a dimensão introduzindo as ids do utilizador pretendido e as funções de id do item. 

    O recomendador Wide & Deep executa a transformação entre produtos em relação às funcionalidades de id do utilizador e id de item por padrão. O resultado cruzado será hashed de acordo com este número para garantir a dimensão.

8.  **Otimizador de partes profundas**: selecione um optimizador para aplicar gradientes na parte profunda do modelo.

9.  **Taxa de aprendizagem de otimizador** profundo : insira um número entre 0.0 e 2.0 que define a taxa de aprendizagem do otimizador de partes profundas.

10.  **Dimensão de incorporação do utilizador:** digite um inteiro para especificar a dimensão da incorporação de id do utilizador.

     O recomendador Wide & Deep cria as incorporações de id do utilizador partilhadas e incorporações de id de item para a parte larga e profunda.

11.  **Dimensão de incorporação de item:** digite um inteiro para especificar a dimensão da incorporação do id do item.

12.  **Características categóricas em dimensão incorporada:** introduza um inteiro para especificar as dimensões das incorporações categóricas.

     Em componente profundo do recomendador Deep &, aprende-se um vetor incorporado para cada característica categórica. E estes vetores incorporados partilham a mesma dimensão.

13.  **Unidades ocultas:** digite o número de nós ocultos de componente profundo. O número de nós em cada camada é separado por vírgulas. Por exemplo, pelo tipo "1000.500.100", especifica-se que o componente profundo tem três camadas, com a primeira camada à última, respectivamente, tem 1000 nós, 500 nós e 100 nós.

14.  **Função de ativação**: selecione uma função de ativação aplicada a cada camada, o padrão é ReLU.

15.  **Abandono**: introduza um número entre 0,0 e 1.0 para determinar a probabilidade de as saídas serem deixadas cair em cada camada durante o treino.

     O abandono é um método de regularização para evitar que as redes neurais se desajustem. Uma decisão comum para este valor é começar com 0.5, que parece estar perto do ideal para um vasto leque de redes e tarefas.

16.  **Normalização do lote**: selecione esta opção para utilizar a normalização do lote após cada camada escondida no componente profundo.

     A normalização do lote é uma técnica para combater o problema interno da mudança de covariate durante o treino das redes. Em geral, pode ajudar a melhorar a velocidade, o desempenho e a estabilidade das redes. 

17.  Executar o pipeline.

## <a name="results"></a>Resultados

Após a execução do gasoduto, para utilizar o modelo de pontuação, ligue o [Train Wide e o Deep Recommender](train-wide-and-deep-recommender.md) à [Pontuação Larga e Profunda,](score-wide-and-deep-recommender.md)para prever valores para novos exemplos de entrada.

##  <a name="technical-notes"></a>Notas técnicas

O Wide & Deep treina em conjunto modelos lineares largos e redes neuronais profundas para combinar os pontos fortes da memorização e generalização. O componente amplo aceita um conjunto de funcionalidades brutas e transformações de recursos para memorizar interações de recursos. E com menos engenharia de recursos, o componente profundo generaliza-se a combinações de recursos invisíveis através de incorporações de características densos de baixa dimensão. 

Na implementação do recomendador Wide & Deep, o módulo utiliza uma estrutura de modelo padrão. O componente largo leva incorporações de utilizador, incorporações de item e a transformação transversal de ids de utilizador e ids de item como entrada. Para a parte profunda do modelo, um vetor incorporado é aprendido para cada característica categórica. Juntamente com outros vetores de recurso numérico, estes vetores são então alimentados na rede neural de feed-forward profundo. A parte larga e profunda são combinadas resumindo as suas odds finais de registo de saída como a previsão, que finalmente vai para uma função comum de perda para o treino conjunto.


## <a name="next-steps"></a>Passos seguintes

Veja o [conjunto de módulos disponíveis](module-reference.md) no Azure Machine Learning. 
