---
title: 'Avaliar Recomendador: referência de módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como usar o módulo avaliar Recomendador no serviço de Azure Machine Learning para avaliar a precisão das previsões de modelo de recomendação.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 9a80fce04aa939895d1dc9572714046d9203bad7
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73717172"
---
# <a name="evaluate-recommender"></a>Avaliar Recomendador

Este artigo descreve como usar o módulo avaliar Recomendador no Azure Machine Learning designer (versão prévia). O objetivo é medir a precisão das previsões que um modelo de recomendação fez. Usando esse módulo, você pode avaliar diferentes tipos de recomendações:  
  
-   Classificações previstas para um usuário e um item    
-   Itens recomendados para um usuário  
  
Quando você cria previsões usando um modelo de recomendação, resultados ligeiramente diferentes são retornados para cada um desses tipos de previsão com suporte. O módulo avaliar Recomendador deduz o tipo de previsão do formato de coluna do conjunto de linha de DataSet. Por exemplo, o conjunto de marcação pode conter:

- usuários-item-processamentos de classificação
- usuários e seus itens recomendados

O módulo também aplica as métricas de desempenho apropriadas, com base no tipo de previsão que está sendo feita. 

  
## <a name="how-to-configure-evaluate-recommender"></a>Como configurar avaliar Recomendador

O módulo avaliar Recomendador compara a saída de previsão usando um modelo de recomendação com os dados de "terra terrestre" correspondentes. Por exemplo, o módulo de [recomendação do SVD de Pontuação](score-svd-recommender.md) produz conjuntos de valores de conjunto de linhas que você pode analisar usando avaliar recomendador.

### <a name="requirements"></a>Requisitos

Avaliar Recomendador requer os seguintes conjuntos de dados como entrada. 
  
#### <a name="test-dataset"></a>Testar conjunto de teste

O conjunto de dados de teste contém o "princípio de verdade" na forma de processamentos de classificação de usuário-item.  

#### <a name="scored-dataset"></a>Conjunto de marcação

O conjunto de marcação contém as previsões que o modelo de recomendação gerou.  
  
As colunas nesse segundo conjunto de DataSet dependem do tipo de previsão que você realizou durante o processo de pontuação. Por exemplo, o conjunto de pontos de Pontuação pode conter um dos seguintes:

- Usuários, itens e as classificações que o usuário provavelmente daria para o item
- Uma lista de usuários e itens recomendados para eles 

### <a name="metrics"></a>Métricas

As métricas de desempenho para o modelo são geradas com base no tipo de entrada. As seções a seguir fornecem detalhes.

## <a name="evaluate-predicted-ratings"></a>Avaliar as classificações previstas  

Quando você estiver avaliando as classificações previstas, o conjunto de dados pontuado (a segunda entrada para avaliar o Recomendador) deve conter processamentos de classificação de usuário-item que atendam a estes requisitos:
  
-   A primeira coluna do conjunto de um contém os identificadores de usuário.    
-   A segunda coluna contém os identificadores de item.  
-   A terceira coluna contém as classificações de usuário-item correspondentes.  
  
> [!IMPORTANT] 
> Para que a avaliação seja bem sucedida, os nomes das colunas devem ser `User`, `Item`e `Rating`, respectivamente.  
  
Avaliar Recomendador compara as classificações no conjunto de "verdadeiro verdade" com as classificações previstas do conjunto de valores de pontuação. Em seguida, ele computa o erro de média absoluta (MAE) e o erro de raiz quadrada da média (RMSE).



## <a name="evaluate-item-recommendations"></a>Avaliar recomendações de item

Quando você estiver avaliando as recomendações de item, use um conjunto de uma pontuação que inclua os itens recomendados para cada usuário:
  
-   A primeira coluna do conjunto de um deve conter o identificador de usuário.    
-   Todas as colunas subsequentes devem conter os identificadores de item recomendados correspondentes, ordenados pelo quão relevante um item é para o usuário. 

Antes de conectar esse conjunto de resultados, recomendamos que você classifique o conjunto de resultados para que os itens mais relevantes sejam apresentados primeiro.  

> [!IMPORTANT] 
> Para avaliar Recomendador para funcionar, os nomes de coluna devem ser `User`, `Item 1`, `Item 2`, `Item 3` e assim por diante.  
  
Avaliar Recomendador computa o NDCG (lucro cumulativo médio normalizado) e o retorna no conjunto de saída.  
  
Como é impossível saber a verdadeira "verdadeira verdade" para os itens recomendados, avaliar Recomendador usa as classificações de item de usuário no conjunto de dados de teste como ganhos no cálculo do NDCG. Para avaliar, o módulo de Pontuação de recomendação deve produzir apenas recomendações para itens com classificações de "terra terrestre" (no conjunto de testes).  
  

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o serviço de Azure Machine Learning. 
