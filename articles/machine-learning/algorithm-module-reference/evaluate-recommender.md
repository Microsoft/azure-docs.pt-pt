---
title: 'Avaliar Recomendador: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo De Avaliação De Recomendação em Azure Machine Learning para avaliar a precisão das previsões do modelo recomendador.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 0890e13acbba8dae31de28d7c78a81bd9b516853
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "76312265"
---
# <a name="evaluate-recommender"></a>Avaliar Recomendador

Este artigo descreve como utilizar o módulo De Avaliação De Recomendação no designer de Aprendizagem automática Azure. O objetivo é medir a precisão das previsões que um modelo de recomendação fez. Ao utilizar este módulo, pode avaliar diferentes tipos de recomendações:  
  
-   Classificações previstas para um utilizador e um item    
-   Itens recomendados para um utilizador  
  
Quando cria previsões utilizando um modelo de recomendação, são devolvidos resultados ligeiramente diferentes para cada um destes tipos de previsão suportados. O módulo Deresendador de Avaliação deduz o tipo de previsão do formato de coluna do conjunto de dados pontuado. Por exemplo, o conjunto de dados marcado pode conter:

- Triplos da classificação de artigos de utilizador
- Utilizadores e seus itens recomendados

O módulo também aplica as métricas de desempenho apropriadas, com base no tipo de previsão que está sendo feita. 

  
## <a name="how-to-configure-evaluate-recommender"></a>Como configurar avaliar o Recomendador

O módulo 'Avaliador' compara a saída de previsão utilizando um modelo de recomendação com os dados correspondentes de "verdade do solo". Por exemplo, o módulo [Score SVD Recommender](score-svd-recommender.md) produz conjuntos de dados pontuados que pode analisar utilizando o Avaliador de Avaliação.

### <a name="requirements"></a>Requisitos

Avaliar o Recomendador requer os seguintes conjuntos de dados como entrada. 
  
#### <a name="test-dataset"></a>Conjunto de dados de teste

O conjunto de dados de teste contém os dados de "verdade no solo" sob a forma de triplos de classificação de artigos de utilizador.  

#### <a name="scored-dataset"></a>Conjunto de dados pontuado

O conjunto de dados pontuado contém as previsões que o modelo de recomendação gerou.  
  
As colunas deste segundo conjunto de dados dependem do tipo de previsão que realizou durante o processo de pontuação. Por exemplo, o conjunto de dados marcado pode conter qualquer um dos seguintes:

- Utilizadores, itens e as classificações que o utilizador provavelmente daria para o item
- Uma lista de utilizadores e itens recomendados para eles 

### <a name="metrics"></a>Métricas

As métricas de desempenho do modelo são geradas com base no tipo de entrada. As seguintes secções dão detalhes.

## <a name="evaluate-predicted-ratings"></a>Avaliar as classificações previstas  

Quando estiver a avaliar as classificações previstas, o conjunto de dados pontuado (a segunda entrada para avaliar o Recomendador) deve conter triplos de classificação de produto do utilizador que satisfaçam estes requisitos:
  
-   A primeira coluna do conjunto de dados contém os identificadores do utilizador.    
-   A segunda coluna contém os identificadores de item.  
-   A terceira coluna contém as classificações correspondentes de artigos de utilizador.  
  
> [!IMPORTANT] 
> Para que a avaliação tenha sucesso, os nomes das colunas devem ser `User` `Item` , `Rating` e, respectivamente.  
  
Avaliar o Recomendador compara as classificações no conjunto de dados "ground truth" com as classificações previstas do conjunto de dados pontuados. Em seguida, calcula o erro absoluto médio (MAE) e o erro quadrado médio da raiz (RMSE).



## <a name="evaluate-item-recommendations"></a>Avaliar recomendações de artigos

Ao avaliar as recomendações de artigos, utilize um conjunto de dados pontuado que inclua os itens recomendados para cada utilizador:
  
-   A primeira coluna do conjunto de dados deve conter o identificador do utilizador.    
-   Todas as colunas subsequentes devem conter os correspondentes identificadores de artigo recomendado, encomendados pela relevância de um artigo para o utilizador. 

Antes de ligar este conjunto de dados, recomendamos que serdene o conjunto de dados de modo a que os itens mais relevantes venham em primeiro lugar.  

> [!IMPORTANT] 
> Para avaliar o Recomendador a funcionar, os nomes das colunas devem ser `User` , , e assim por `Item 1` `Item 2` `Item 3` diante.  
  
Avaliar o Recomendador calcula o ganho acumulado médio normalizado (NDCG) e devolve-o no conjunto de dados de saída.  
  
Como é impossível saber a verdadeira "verdade básica" para os itens recomendados, o Assess Recommender utiliza as classificações de produto do utilizador no conjunto de dados de teste como ganhos no cálculo do NDCG. Para avaliar, o módulo de pontuação recomendador só deve produzir recomendações para itens com classificações de "verdade no solo" (no conjunto de dados de teste).  
  

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 
