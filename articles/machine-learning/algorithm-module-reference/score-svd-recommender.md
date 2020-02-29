---
title: 'Recomendador SVD: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Saiba como utilizar o módulo recomendador Score SVD em Azure Machine Learning para marcar previsões de recomendação para um conjunto de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/10/2020
ms.openlocfilehash: a3eafc28dc6d0f44a1f1019cb3393259aa2a698a
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77920353"
---
# <a name="score-svd-recommender"></a>Classificar Recomendador SVD

Este artigo descreve como utilizar o módulo recomendador Score SVD no designer de Machine Learning Azure (pré-visualização). Utilize este módulo para criar previsões utilizando um modelo de recomendação treinado baseado no algoritmo de decomposição de valor único (SVD).

O recomendador SVD pode gerar dois tipos diferentes de previsões:

- [Prever classificações para um determinado utilizador e item](#prediction-of-ratings)
- [Recomendar itens a um utilizador](#recommendations-for-users)

Quando está a criar o segundo tipo de previsões, pode operar num destes modos:

- **O modo de produção** considera todos os utilizadores ou itens. É normalmente usado num serviço web.

  Pode criar pontuações para novos utilizadores, e não apenas para utilizadores vistos durante o treino. Para mais informações, consulte as [notas técnicas.](#technical-notes) 

- O modo de **avaliação** funciona num conjunto reduzido de utilizadores ou itens que podem ser avaliados. É normalmente usado durante as operações do oleoduto.

Para obter mais informações sobre o algoritmo recomendador SVD, consulte as técnicas de factorização matrix do artigo de investigação [para sistemas de recomendação](https://datajobs.com/data-science-repo/Recommender-Systems-[Netflix].pdf).

## <a name="how-to-configure-score-svd-recommender"></a>Como configurar o recomendador SVD de pontuação

Este módulo suporta dois tipos de previsões, cada uma com requisitos diferentes. 

###  <a name="prediction-of-ratings"></a>Previsão das classificações

Quando prevê classificações, o modelo calcula como um utilizador irá reagir a um determinado item, tendo em conta os dados de formação. Os dados de entrada para pontuação devem fornecer ao utilizador e ao item uma classificação.

1. Adicione um modelo de recomendação treinado ao seu pipeline e ligue-o ao **recomendador SVD treinado**. Tem de criar o modelo utilizando o módulo [recomendador Train SVD.](train-SVD-recommender.md)

2. Para o tipo de **previsão do recomendador,** selecione **Previsão de Classificação**. Não são necessários outros parâmetros.

3. Adicione os dados para os quais pretende fazer previsões e conecte-os ao **Dataset para marcar**.

   Para que o modelo preveja classificações, o conjunto de dados de entrada deve conter pares de itens de utilizador.

   O conjunto de dados pode conter uma terceira coluna opcional de classificações para o par de artigos de utilizador na primeira e segunda colunas. Mas a terceira coluna será ignorada durante a previsão.

4. Executar o pipeline.

### <a name="results-for-rating-predictions"></a>Resultados das previsões de classificação 

O conjunto de dados de saída contém três colunas: utilizadores, itens e a classificação prevista para cada utilizador e item de entrada.

###  <a name="recommendations-for-users"></a>Recomendações para utilizadores 

Para recomendar itens para utilizadores, fornece uma lista de utilizadores e itens como entrada. A partir destes dados, o modelo utiliza os seus conhecimentos sobre itens e utilizadores existentes para gerar uma lista de itens com provável apelo a cada utilizador. Pode personalizar o número de recomendações devolvidas. E pode estabelecer um limiar para o número de recomendações anteriores que são necessárias para gerar uma recomendação.

1. Adicione um modelo de recomendação treinado ao seu pipeline e ligue-o ao **recomendador SVD treinado**.  Tem de criar o modelo utilizando o módulo [recomendador Train SVD.](train-svd-recommender.md)

2. Para recomendar itens para uma lista de utilizadores, detete o tipo de **previsão do Recomendador** para **a Recomendação**do Ponto .

3. Para **a seleção recomendada**de itens, indique se está a utilizar o módulo de pontuação em produção ou para avaliação do modelo. Escolha um destes valores:

    - **De Todos os itens**: Selecione esta opção se estiver a configurar um pipeline para utilizar num serviço web ou em produção.  Esta opção permite o modo de *produção.* O módulo faz recomendações de todos os itens vistos durante o treino.

    - **A partir de Itens Avaliados (para avaliação**do modelo) : Selecione esta opção se estiver a desenvolver ou a testar um modelo. Esta opção permite o modo de *avaliação*. O módulo faz recomendações apenas a partir dos itens no conjunto de dados de entrada que foram avaliados.
    
    - **A partir de Itens Não Classificados (para sugerir novos itens aos utilizadores)** : Selecione esta opção se quiser que o módulo faça recomendações apenas a partir desses itens no conjunto de dados de formação que não tenham sido avaliados. 

4. Adicione o conjunto de dados para o qual pretende fazer previsões e conecte-o ao **Dataset para marcar**.

    - Para **From All Items,** o conjunto de dados de entrada deve ser composto por uma coluna. Contém os identificadores dos utilizadores para os quais podem fazer recomendações.

      O conjunto de dados pode incluir duas colunas extra de identificadores e classificações de itens, mas estas duas colunas são ignoradas. 

    - Para **a partir de itens classificados (para avaliação**do modelo), o conjunto de dados de entrada deve consistir em pares de itens de utilizador. A primeira coluna deve conter o identificador do utilizador. A segunda coluna deve conter os identificadores de objectocorrespondentes.

      O conjunto de dados pode incluir uma terceira coluna de classificações de itens de utilizador, mas esta coluna é ignorada.

    - Para **a partir de itens não avaliados (para sugerir novos itens para os utilizadores)** , o conjunto de dados de entrada deve consistir em pares de itens de utilizador. A primeira coluna deve conter o identificador do utilizador. A segunda coluna deve conter os identificadores de objectocorrespondentes.

     O conjunto de dados pode incluir uma terceira coluna de classificações de itens de utilizador, mas esta coluna é ignorada.

5. **Número máximo de itens a recomendar a um utilizador**: Insira o número de itens a devolver para cada utilizador. Por defeito, o módulo recomenda cinco itens.

6. **Tamanho mínimo do pool de recomendação por utilizador**: Introduza um valor que indique quantas recomendações anteriores são necessárias. Por padrão, este parâmetro está definido para 2, o que significa que pelo menos dois outros utilizadores recomendaram o item.

   Utilize esta opção apenas se estiver a marcar em modo de avaliação. A opção não está disponível se selecionar **De Todos os Itens** ou de Itens Não **Avaliados (para sugerir novos itens aos utilizadores)** .

7.  Para **itens não avaliados (para sugerir novos itens para os utilizadores)** , utilize a terceira porta de entrada, denominada Dados de **Formação,** para remover itens que já tenham sido avaliados a partir dos resultados da previsão.

    Para aplicar este filtro, ligue o conjunto de dados de treino original à porta de entrada.

8. Executar o pipeline.

### <a name="results-of-item-recommendation"></a>Resultados da recomendação do item

O conjunto de dados pontuado devolvido pelo Recomendante Score SVD lista os itens recomendados para cada utilizador:

- A primeira coluna contém os identificadores do utilizador.
- São geradas várias colunas adicionais, dependendo do valor que definiu para o **número máximo de itens a recomendar a um utilizador**. Cada coluna contém um item recomendado (por identificador). As recomendações são encomendadas por afinidade de produto de utilizador. O item com maior afinidade é colocado na **coluna Item 1**.

> [!WARNING]
> Não é possível avaliar este conjunto de dados pontuado utilizando o módulo ['Avaliar recomendador'.](evaluate-recommender.md)


##  <a name="technical-notes"></a>Notas técnicas

Se tiver um pipeline com o recomendador SVD e mover o modelo para a produção, esteja ciente de que existem diferenças fundamentais entre utilizar o recomendador no modo de avaliação e utilizá-lo no modo de produção.

A avaliação, por definição, requer previsões que podem ser verificadas contra a *verdade no solo* num conjunto de testes. Quando avalia o recomendador, deve prever apenas itens que tenham sido classificados no conjunto de testes. Isto restringe os valores possíveis que estão previstos.

Ao operacionalizar o modelo, normalmente muda o modo de previsão para fazer recomendações com base em todos os itens possíveis, de forma a obter as melhores previsões. Para muitas destas previsões, não há verdade no terreno correspondente. Assim, a exatidão da recomendação não pode ser verificada da mesma forma que durante as operações do gasoduto.


## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 
