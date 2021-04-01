---
title: 'Pontuação SVD Recomendadora: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo de recomendador score SVD em Azure Machine Learning para marcar previsões de recomendação para um conjunto de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 08/10/2020
ms.openlocfilehash: bf62fa995724b8e1fff757e89945cc39db3d9842
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "90893717"
---
# <a name="score-svd-recommender"></a>Classificar Recomendador SVD

Este artigo descreve como usar o módulo de recomendador Score SVD no designer de aprendizagem automática Azure. Utilize este módulo para criar previsões utilizando um modelo de recomendação treinado baseado no algoritmo de decomposição de valor único (SVD).

O recomendador SVD pode gerar dois tipos diferentes de previsões:

- [Prever classificações para um determinado utilizador e item](#prediction-of-ratings)
- [Recomendar itens a um utilizador](#recommendations-for-users)

Quando se está a criar o segundo tipo de previsões, pode operar num destes modos:

- **O modo de produção** considera todos os utilizadores ou itens. É normalmente usado num serviço web.

  Pode criar pontuações para novos utilizadores, e não apenas para os utilizadores vistos durante o treino. Para mais informações, consulte as [notas técnicas.](#technical-notes) 

- **O modo de avaliação** funciona num conjunto reduzido de utilizadores ou itens que podem ser avaliados. É normalmente usado durante as operações do oleoduto.

Para obter mais informações sobre o algoritmo de recomendação SVD, consulte o artigo de investigação [Técnicas de factorização matrix para sistemas de recomendação](https://datajobs.com/data-science-repo/Recommender-Systems-[Netflix].pdf).

## <a name="how-to-configure-score-svd-recommender"></a>Como configurar o Recomendador SVD de pontuação

Este módulo suporta dois tipos de previsões, cada uma com requisitos diferentes. 

###  <a name="prediction-of-ratings"></a>Previsão das classificações

Quando prevê classificações, o modelo calcula como um utilizador irá reagir a um determinado item, dados os dados de formação. Os dados de entrada para a pontuação devem fornecer tanto um utilizador como o item para avaliar.

1. Adicione um modelo de recomendação treinado ao seu oleoduto e conecte-o ao **recomendador SVD treinado.** Tem de criar o modelo utilizando o módulo [de recomendador SVD train.](train-SVD-recommender.md)

2. Para **o tipo de previsão de recomendação**, selecione Previsão de **Classificação**. Não são necessários outros parâmetros.

3. Adicione os dados para os quais deseja fazer previsões e conecte-os ao **Dataset para obter**.

   Para que o modelo preveja classificações, o conjunto de dados de entrada deve conter pares de artigos de utilizador.

   O conjunto de dados pode conter uma terceira coluna opcional de classificações para o par de artigos de utilizador nas primeira e segunda colunas. Mas a terceira coluna será ignorada durante a previsão.

4. Envie o oleoduto.

### <a name="results-for-rating-predictions"></a>Resultados das previsões de rating 

O conjunto de dados de saída contém três colunas: utilizadores, itens e a classificação prevista para cada utilizador e item de entrada.

###  <a name="recommendations-for-users"></a>Recomendações para utilizadores 

Para recomendar itens para os utilizadores, fornece uma lista de utilizadores e itens como entrada. A partir destes dados, o modelo utiliza o seu conhecimento sobre itens e utilizadores existentes para gerar uma lista de itens com provável apelo a cada utilizador. Pode personalizar o número de recomendações devolvidas. E pode definir um limiar para o número de recomendações anteriores que são necessárias para gerar uma recomendação.

1. Adicione um modelo de recomendação treinado ao seu oleoduto e conecte-o ao **recomendador SVD treinado.**  Tem de criar o modelo utilizando o módulo [de recomendador SVD train.](train-svd-recommender.md)

2. Para recomendar itens para uma lista de utilizadores, desa um **tipo de previsão de recomendação** de **recomendação** para o item .

3. Para **a seleção recomendada de artigos,** indique se está a utilizar o módulo de pontuação em produção ou para avaliação do modelo. Escolha um destes valores:

    - **De todos os itens**: Selecione esta opção se estiver a configurar um oleoduto para utilizar num serviço web ou em produção.  Esta opção permite o *modo de produção.* O módulo faz recomendações de todos os itens vistos durante o treino.

    - **A partir de Itens Classificados (para avaliação de modelos)**: Selecione esta opção se estiver a desenvolver ou testar um modelo. Esta opção permite o *modo de avaliação*. O módulo faz recomendações apenas a partir dos itens do conjunto de dados de entrada que foram avaliados.
    
    - **A partir de Itens Não Classificados (para sugerir novos itens aos utilizadores)**: Selecione esta opção se quiser que o módulo faça recomendações apenas a partir dos itens do conjunto de dados de formação que não foram avaliados. 

4. Adicione o conjunto de dados para o qual pretende fazer previsões e conecte-o ao **Dataset para obter**.

    - Para **todos os itens,** o conjunto de dados de entrada deve ser composto por uma coluna. Contém os identificadores dos utilizadores para os quais fazer recomendações.

      O conjunto de dados pode incluir duas colunas extra de identificadores de item e classificações, mas estas duas colunas são ignoradas. 

    - Para **itens classificados (para avaliação de modelos)**, o conjunto de dados de entrada deve ser composto por pares de artigos de utilizador. A primeira coluna deve conter o identificador do utilizador. A segunda coluna deve conter os identificadores de item correspondentes.

      O conjunto de dados pode incluir uma terceira coluna de classificações de item de utilizador, mas esta coluna é ignorada.

    - Para **itens não classificados (para sugerir novos itens aos utilizadores)**, o conjunto de dados de entrada deve ser composto por pares de artigos de utilizador. A primeira coluna deve conter o identificador do utilizador. A segunda coluna deve conter os identificadores de item correspondentes.

     O conjunto de dados pode incluir uma terceira coluna de classificações de item de utilizador, mas esta coluna é ignorada.

5. **Número máximo de itens a recomendar a um utilizador**: Introduza o número de itens a devolver para cada utilizador. Por predefinição, o módulo recomenda cinco itens.

6. **Tamanho mínimo do conjunto de recomendações por utilizador**: Introduza um valor que indique quantas recomendações prévias são necessárias. Por predefinição, este parâmetro é definido para 2, o que significa que pelo menos dois outros utilizadores recomendaram o item.

   Utilize esta opção apenas se estiver a pontuar no modo de avaliação. A opção não está disponível se selecionar **de todos os itens** ou **de itens não classificados (para sugerir novos itens aos utilizadores)**.

7.  Para **itens não avaliados (para sugerir novos itens aos utilizadores)**, utilize a terceira porta de entrada, denominada **Dados de Formação,** para remover itens que já tenham sido classificados a partir dos resultados da previsão.

    Para aplicar este filtro, ligue o conjunto de dados de treino original à porta de entrada.

8. Envie o oleoduto.

### <a name="results-of-item-recommendation"></a>Resultados da recomendação do artigo

O conjunto de dados pontuado devolvido pela Score SVD Recommender lista os itens recomendados para cada utilizador:

- A primeira coluna contém os identificadores do utilizador.
- São geradas várias colunas adicionais, dependendo do valor que definiu para o **número máximo de itens a recomendar a um utilizador**. Cada coluna contém um item recomendado (por identificador). As recomendações são ordenadas pela afinidade do artigo-utilizador. O item com maior afinidade é colocado na coluna **Ponto 1**.


##  <a name="technical-notes"></a>Notas técnicas

Se tiver um oleoduto com o recomendador SVD e mover o modelo para a produção, esteja ciente de que existem diferenças fundamentais entre a utilização do recomendador em modo de avaliação e a sua utilização no modo de produção.

A avaliação, por definição, requer previsões que podem ser verificadas contra a *verdade no terreno* num conjunto de testes. Quando avaliar o recomendador, deve prever apenas itens que tenham sido classificados no conjunto de testes. Isto restringe os valores possíveis que estão previstos.

Quando operacionaliza o modelo, normalmente muda-se o modo de previsão para fazer recomendações com base em todos os itens possíveis, de forma a obter as melhores previsões. Para muitas destas previsões, não há verdade básica correspondente. Assim, a precisão da recomendação não pode ser verificada da mesma forma que durante as operações de gasoduto.


## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 
