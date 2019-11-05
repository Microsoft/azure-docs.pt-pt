---
title: 'Avaliar Recomendador: referência de módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como usar o módulo avaliar Recomendador no Azure Machine Learning Service para avaliar a precisão das previsões do modelo de recomendação.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: dad11f0e03e55186dfee1e7a4f1f82fb44275693
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73517998"
---
# <a name="evaluate-recommender"></a>Avaliar Recomendador

Este artigo descreve como usar o módulo **avaliar recomendador** no Azure Machine Learning designer (versão prévia) para medir a precisão das previsões feitas por um modelo de recomendação. Usando esse módulo, você pode avaliar quatro tipos diferentes de recomendações:  
  
-   Classificações previstas para um determinado usuário e item  
  
-   Itens recomendados para um determinado usuário  
  
Quando você cria previsões usando um modelo de recomendação, resultados ligeiramente diferentes são retornados para cada um desses tipos de previsão com suporte. O módulo **avaliar recomendador** deduz o tipo de previsão do formato de coluna do conjunto de linha de DataSet. Por exemplo, o **conjunto de marcação** pode conter:

- usuários-item-processamentos de classificação
- usuários e seus itens recomendados

O módulo também aplica as métricas de desempenho apropriadas, com base no tipo de previsão que está sendo feita. 

  
## <a name="how-to-configure-evaluate-recommender"></a>Como configurar avaliar Recomendador

O módulo **avaliar recomendador** compara a saída de previsões por um modelo de recomendação com os dados de "terra terrestre" correspondentes. Por exemplo, o módulo de [recomendação SVD de Pontuação](score-svd-recommender.md) produz conjuntos de linhas pontuados que podem ser analisados com o **avaliar recomendador**.

### <a name="requirements"></a>Requisitos

**Avaliar recomendador** requer os seguintes conjuntos de dados como entrada. 
  
#### <a name="test-dataset"></a>Testar conjunto de teste

O **conjunto** de dados de teste contém o "princípio de verdade" na forma de **processamentos de classificação de usuário-item**.  

#### <a name="scored-dataset"></a>Conjunto de marcação

O **conjunto de marcação** contém as previsões que foram geradas pelo modelo de recomendação.  
  
As colunas neste segundo conjunto de mesmos dependem do tipo de previsão que você estava executando durante a pontuação. Por exemplo, o conjunto de marcação pode conter qualquer um dos seguintes:

- Usuários, itens e as classificações que o usuário provavelmente daria para o item
- Uma lista de usuários e itens recomendados para eles 

### <a name="metrics"></a>Métricas

As métricas de desempenho para o modelo são geradas com base no tipo de entrada. Para obter detalhes, consulte estas seções:

+ [Avaliar as classificações previstas](#evaluate-predicted-ratings)
+ [Avaliar recomendações de item](#evaluate-item-recommendations)

## <a name="evaluate-predicted-ratings"></a>Avaliar as classificações previstas  

Ao avaliar as classificações previstas, o conjunto de dados pontuado (a segunda entrada para **avaliar o recomendador**) deve conter **processamentos de classificação de usuário-item**, atendendo a estes requisitos:
  
-   A primeira coluna do conjunto de um contém identificadores de usuário.  
  
-   A segunda coluna contém os identificadores de item.  
  
-   A terceira coluna contém as classificações de usuário-item correspondentes.  
  
> [!IMPORTANT] 
> Para que a avaliação seja bem sucedida, os nomes das colunas devem ser `User`, `Item`e `Rating`, respectivamente.  
  
**Avaliar recomendador** compara as classificações no conjunto de valores de início da verdade com as classificações previstas do conjunto de valores de Pontuação e computa o **erro de média absoluta** (Mae) e o **erro ao quadrado da média raiz** (RMSE).



## <a name="evaluate-item-recommendations"></a>Avaliar recomendações de item

Ao avaliar a recomendação do item, use um conjunto de uma pontuação que inclua os itens recomendados para cada usuário:
  
-   A primeira coluna do conjunto de um deve conter o identificador de usuário.  
  
-   Todas as colunas subsequentes devem conter os identificadores de item recomendados correspondentes, ordenados pelo quão relevante um item é para o usuário. 

    Antes de conectar esse conjunto de resultados, recomendamos que você classifique o conjunto de resultados para que os itens mais relevantes sejam apresentados primeiro.  



> [!IMPORTANT] 
> Para **avaliar recomendador** para funcionar, os nomes de coluna devem ser `User`, `Item 1`, `Item 2`, `Item 3` e assim por diante.  
  
**Avaliar recomendador** computa o**NDCG**(lucro cumulativo médio normalizado) e o retorna no conjunto de saída.  
  
Como é impossível saber a verdadeira "verdadeira verdade" para os itens recomendados, **avaliar recomendador** usa as classificações de item de usuário no conjunto de dados de teste como ganhos no cálculo do NDCG. Para avaliar, o módulo de Pontuação de recomendação deve produzir apenas recomendações para itens com classificações de verdade de terra (no conjunto de testes).  
  

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning serviço. 
