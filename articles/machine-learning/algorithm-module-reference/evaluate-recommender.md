---
title: 'Avaliar recomendador: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo Recomendador de Avaliação em Aprendizagem automática Azure para avaliar a precisão das previsões do modelo recomendador.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 38144d5df04427a82989b78843466ecd55386196
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76312265"
---
# <a name="evaluate-recommender"></a>Avaliar Recomendador

Este artigo descreve como utilizar o módulo Recomendador de Avaliação em Azure Machine Learning designer (pré-visualização). O objetivo é medir a precisão das previsões que um modelo de recomendação fez. Ao utilizar este módulo, pode avaliar diferentes tipos de recomendações:  
  
-   Classificações previstas para um utilizador e um item    
-   Itens recomendados para um utilizador  
  
Quando se criam previsões utilizando um modelo de recomendação, são devolvidos resultados ligeiramente diferentes para cada um destes tipos de previsão suportados. O módulo 'Recomendar avaliação' deduz o tipo de previsão do formato da coluna do conjunto de dados pontuado. Por exemplo, o conjunto de dados pontuado pode conter:

- Triplos de classificação de utilizador
- Utilizadores e seus itens recomendados

O módulo também aplica as métricas de desempenho apropriadas, com base no tipo de previsão que está a ser feita. 

  
## <a name="how-to-configure-evaluate-recommender"></a>Como configurar o Recomendador de Avaliação

O módulo 'Avaliar recomendador' compara a saída de previsão utilizando um modelo de recomendação com os dados correspondentes de "verdade no solo". Por exemplo, o módulo [recomendador Score SVD](score-svd-recommender.md) produz conjuntos de dados pontuados que pode analisar utilizando o Recomendador de Avaliação.

### <a name="requirements"></a>Requisitos

Avaliar o Recomendador requer os seguintes conjuntos de dados como entrada. 
  
#### <a name="test-dataset"></a>Conjunto de dados de teste

O conjunto de dados do teste contém os dados da "verdade no solo" sob a forma de triplos de classificação de itens de utilizador.  

#### <a name="scored-dataset"></a>Conjunto de dados pontuado

O conjunto de dados pontuado contém as previsões que o modelo de recomendação gerou.  
  
As colunas neste segundo conjunto de dados dependem do tipo de previsão que realizou durante o processo de pontuação. Por exemplo, o conjunto de dados pontuado pode conter qualquer um dos seguintes dados:

- Utilizadores, itens e as classificações que o utilizador provavelmente daria para o item
- Uma lista de utilizadores e itens recomendados para eles 

### <a name="metrics"></a>Métricas

As métricas de desempenho do modelo são geradas com base no tipo de entrada. As seguintes secções dão detalhes.

## <a name="evaluate-predicted-ratings"></a>Avaliar classificações previstas  

Quando estiver a avaliar as classificações previstas, o conjunto de dados pontuado (a segunda entrada para avaliar o recomendador) deve conter triplos de classificação de utilizador que satisfaçam estes requisitos:
  
-   A primeira coluna do conjunto de dados contém os identificadores do utilizador.    
-   A segunda coluna contém os identificadores de objetos.  
-   A terceira coluna contém as classificações correspondentes de artigos de utilizador.  
  
> [!IMPORTANT] 
> Para que a avaliação tenha `User` `Item`sucesso, `Rating`os nomes das colunas devem ser, e, respectivamente.  
  
Avaliar o Recomendador compara as classificações no conjunto de dados da "verdade no solo" com as classificações previstas do conjunto de dados pontuado. Em seguida, calcula o erro absoluto médio (MAE) e o erro quadrado da média raiz (RMSE).



## <a name="evaluate-item-recommendations"></a>Avaliar recomendações de itens

Quando estiver a avaliar as recomendações do item, utilize um conjunto de dados pontuado que inclua os itens recomendados para cada utilizador:
  
-   A primeira coluna do conjunto de dados deve conter o identificador do utilizador.    
-   Todas as colunas subsequentes devem conter os identificadores de itens recomendados correspondentes, ordenados pela relevância de um item para o utilizador. 

Antes de ligar este conjunto de dados, recomendamos que separe o conjunto de dados de modo a que os itens mais relevantes venham em primeiro lugar.  

> [!IMPORTANT] 
> Para avaliar o recomendor para funcionar, `Item 1` `Item 2`os `Item 3` nomes das colunas devem ser, `User`e assim por diante.  
  
Avaliar o Recomendador calcula o ganho acumulado normalizado médio normalizado (NDCG) e devolve-o no conjunto de dados de saída.  
  
Como é impossível saber a verdadeira "verdade no solo" para os itens recomendados, avaliar o Recomendor utiliza as classificações de produto de utilizador no conjunto de dados de teste como ganhos na computação do NDCG. Para avaliar, o módulo de pontuação recomendador só deve produzir recomendações para itens com classificações de "verdade no solo" (no conjunto de dados de teste).  
  

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 
