---
title: 'SVD Recomendador de Pontuação: referência de módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como usar o módulo de recomendação SVD de pontuação no serviço de Azure Machine Learning para pontuar previsões de recomendação para um conjunto de informações.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 25de69873857512a70d6417973d4a85883ac6455
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73517920"
---
# <a name="score-svd-recommender"></a>Pontuar Recomendador SVD

Este artigo descreve como usar o módulo **Score SVD recomendador** no designer de Azure Machine Learning (versão prévia). Use este módulo para criar previsões usando um modelo de recomendação treinado com base no algoritmo SVD (decomposição de valor único).

O recomendador SVD pode gerar dois tipos diferentes de previsões:

- [Prever classificações para um determinado usuário e item](#predict-ratings)

- [Itens recomendados a um determinado usuário](#recommend)

Ao criar o segundo tipo de previsões, você pode operar no modo de *produção* ou no *modo de avaliação*.

- O **modo de produção** considera todos os usuários ou itens e normalmente é usado em um serviço Web.

    Você pode criar pontuações para novos usuários, não apenas os usuários vistos durante o treinamento. Para obter mais informações, consulte [esta seção](#technical-notes). 

- O **modo de avaliação** opera em um conjunto reduzido de usuários ou itens que podem ser avaliados e normalmente é usado durante a pipeline.

Para obter mais informações sobre o algoritmo recomendador SVD, consulte o artigo de pesquisa: [técnicas de fatoração de matriz para sistemas de recomendação](https://datajobs.com/data-science-repo/Recommender-Systems-[Netflix].pdf).

                                                                                                                                    


## <a name="how-to-configure-score-svd-recommender"></a>Como configurar o SVD Recomendador de Pontuação

Esse módulo dá suporte a diferentes tipos de recomendações, cada um com requisitos diferentes. Clique no link para o tipo de dados que você tem e o tipo de recomendação que você deseja criar.

+ [Prever classificações](#predict-ratings)
+ [Itens recomendados](#recommend)

###  <a name="predict-ratings"></a>Prever classificações

Quando você prevê as classificações, o modelo calcula como um determinado usuário reagirá a um determinado item, considerando os dados de treinamento. Portanto, os dados de entrada para Pontuação devem fornecer um usuário e o item a serem classificados.

1. Adicione um modelo de recomendação treinado ao seu pipeline e conecte-o ao **recomendador SVD treinado**.  Você deve criar o modelo usando [treinar recomendador SVD](train-SVD-recommender.md).

2. **Tipo de previsão de recomendação**: selecione **previsão de classificação**. Nenhum outro parâmetro é necessário.

3. Adicione os dados para os quais você deseja fazer previsões e conecte-os ao **DataSet para pontuar**.

    Para prever as classificações, o conjunto de dados de entrada deve conter pares de item-usuário.

    O conjunto de linhas pode conter uma terceira coluna opcional de classificações para o par de item-usuário na primeira e segunda colunas, mas a terceira coluna será ignorada durante a previsão.

4. Executar o pipeline.

### <a name="results-for-rating-predictions"></a>Resultados para previsões de classificação 

O conjunto de dados de saída contém três colunas, contendo o usuário, o item e a classificação prevista para cada usuário de entrada e item.

###  <a name="recommend"></a>Recomendar 

Para recomendar itens para os usuários, você fornece uma lista de usuários e itens como entrada. A partir desses dados, o modelo usa seu conhecimento sobre itens e usuários existentes para gerar uma lista de itens com um apelo provável para cada usuário. Você pode personalizar o número de recomendações retornadas e definir um limite para o número de recomendações anteriores que são necessárias para gerar uma recomendação.

1. Adicione um modelo de recomendação treinado ao seu pipeline e conecte-o ao **recomendador SVD treinado**.  Você deve criar o modelo usando [treinar recomendador SVD](train-svd-recommender.md).

2. Para recomendar itens para uma determinada lista de usuários, defina **tipo de previsão de recomendado** como **recomendação de item**.

3. **Seleção de item recomendada**: indique se você está usando o módulo de pontuação em produção ou para avaliação de modelo, escolhendo um destes valores:

    - **De todos os itens**: Selecione esta opção se você estiver configurando um pipeline para usar em um serviço Web ou produção.  Essa opção habilita o **modo de produção**e o módulo faz recomendações de todos os itens vistos durante o treinamento.

    - **De itens classificados (para avaliação de modelo)** : Selecione esta opção se você estiver desenvolvendo ou testando um modelo. Essa opção habilita o **modo de avaliação**e o módulo faz recomendações apenas dos itens no conjunto de dados de entrada que foram classificados.
    
    - **De itens sem classificação (para sugerir novos itens para os usuários)** : Selecione essa opção e o módulo faz recomendações apenas dos itens no conjunto de módulos de treinamento que não foram classificados. 

4. Adicione o conjunto de um para o qual você deseja fazer previsões e conecte-o ao **conjunto de pontos para Pontuação**.

    - Para **todos os itens**, o conjunto de dados de entrada deve consistir em uma coluna, contendo os identificadores de usuários para os quais fazer recomendações.

        O conjunto de valores pode incluir duas colunas extras de identificadores e classificações de item, mas essas duas colunas são ignoradas. 

    - Para **itens classificados (para avaliação de modelo)** , o conjunto de dados de entrada deve consistir em **pares de item de usuário**. A primeira coluna deve conter o identificador de **usuário** . A segunda coluna deve conter os identificadores de **Item** correspondentes.

        O conjunto de valores pode incluir uma terceira coluna de classificações de usuário-item, mas essa coluna é ignorada.

    - Para **itens sem classificação (para sugerir novos itens para os usuários)** , o conjunto de dados de entrada deve consistir em **pares de item-usuário**. A primeira coluna deve conter o identificador de **usuário** . A segunda coluna deve conter os identificadores de **Item** correspondentes.

        O conjunto de valores pode incluir uma terceira coluna de classificações de usuário-item, mas essa coluna é ignorada.

5. **Número máximo de itens a serem recomendados para um usuário**: digite o número de itens a serem retornados para cada usuário. Por padrão, são recomendados cinco itens.

6. **Tamanho mínimo do pool de recomendações por usuário**: digite um valor que indique quantas recomendações anteriores são necessárias.  Por padrão, esse parâmetro é definido como 2, o que significa que o item deve ter sido recomendado por pelo menos dois outros usuários.

    Essa opção deve ser usada somente se você estiver pontuando no modo de avaliação. A opção não estará disponível se você selecionar **de todos os itens** ou **de itens sem classificação (para sugerir novos itens para os usuários)** .

7.  Para **itens sem classificação (para sugerir novos itens para os usuários)** , use a terceira porta de entrada, denominada **dados de treinamento**, para remover os itens que já foram classificados dos resultados da previsão.

    Para aplicar esse filtro, conecte o conjunto de dados de treinamento original à porta de entrada.

8. Executar o pipeline.

### <a name="results-of-item-recommendation"></a>Resultados da recomendação do item

O conjunto de pontos de Pontuação retornado por **Score SVD recomendador** lista os itens recomendados para cada usuário.

- A primeira coluna contém os identificadores de usuário.
- Várias colunas adicionais são geradas, dependendo do valor definido para o **número máximo de itens a ser recomendado para um usuário**. Cada coluna contém um item recomendado (por identificador). As recomendações são ordenadas por afinidade de usuário-item, com o item com a afinidade mais alta colocada na coluna, **Item 1**.

> [!WARNING]
> Este conjunto de classificação não pode ser avaliado usando o módulo [avaliar recomendador](evaluate-recommender.md) .


##  <a name="technical-notes"></a>Notas técnicas

Esta seção contém respostas para algumas perguntas comuns sobre como usar o recomendador para criar previsões.  

###  <a name="production-use-of-the-svd-recommender"></a>Uso de produção do recomendador SVD

Se você tiver um pipeline com o recomendador SVD e mover o modelo para produção, lembre-se dessas principais diferenças ao usar o recomendador no modo de avaliação e no modo de produção:

- A avaliação, por definição, requer previsões que podem ser verificadas em relação à *verdadeira verdade* em um conjunto de teste. Portanto, quando você avalia o recomendador, ele deve prever apenas os itens que foram classificados no conjunto de teste. Isso restringe, necessariamente, os valores possíveis que são previstos.

    No entanto, ao colocar o modelo em operação, você normalmente altera o modo de previsão para fazer recomendações com base em todos os itens possíveis, a fim de obter as melhores previsões. Para muitas dessas previsões, não há nenhuma verdade de terra correspondente, portanto, a precisão da recomendação não pode ser verificada da mesma maneira que durante o pipeline.


## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning serviço. 
