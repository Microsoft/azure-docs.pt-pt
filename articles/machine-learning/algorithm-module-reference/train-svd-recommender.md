---
title: 'Recomendador SVD do comboio: referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo de recomendador Train SVD em Azure Machine Learning para treinar um recomendador bayesiano utilizando o algoritmo SVD.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/17/2021
ms.openlocfilehash: 77407f253bb347160ea331bd7384d8085f21b040
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104654463"
---
# <a name="train-svd-recommender"></a>Preparar Recomendador SVD

Este artigo descreve como usar o módulo de recomendador SVD do comboio no designer de aprendizagem automática Azure. Utilize este módulo para treinar um modelo de recomendação baseado no algoritmo de decomposição de valor único (SVD).  

O módulo de recomendador SVD do comboio lê um conjunto de dados de triplos de classificação de artigos de utilizador. Devolve um recomendador SVD treinado. Em seguida, pode utilizar o modelo treinado para prever classificações ou gerar recomendações, ligando o módulo [De Recomendação Score SVD.](score-svd-recommender.md)  


  
## <a name="more-about-recommendation-models-and-the-svd-recommender"></a>Mais sobre modelos de recomendação e o recomendador SVD  

O principal objetivo de um sistema de recomendação é recomendar um ou mais *itens* aos *utilizadores* do sistema. Exemplos de um item podem ser um filme, restaurante, livro ou música. Um utilizador pode ser uma pessoa, um grupo de pessoas, ou outra entidade com preferências de item.  

Existem duas abordagens principais para sistemas de recomendação: 

+ Uma abordagem **baseada em conteúdo** utiliza funcionalidades tanto para utilizadores como para itens. Os utilizadores podem ser descritos por propriedades como idade e sexo. Os itens podem ser descritos por propriedades como autor e fabricante. Você pode encontrar exemplos típicos de sistemas de recomendação baseados em conteúdo em sites de matchmaking social. 
+ **A filtragem colaborativa** utiliza apenas identificadores dos utilizadores e dos itens. Obtém informação implícita sobre estas entidades a partir de uma matriz (escassa) de classificações dadas pelos utilizadores aos itens. Podemos aprender sobre um utilizador a partir dos itens que avaliaram e de outros utilizadores que avaliaram os mesmos itens.  

O recomendador SVD utiliza identificadores dos utilizadores e dos itens, e uma matriz de classificações dadas pelos utilizadores aos itens. É um *recomendador colaborativo.* 

Para obter mais informações sobre o recomendador SVD, consulte o documento de investigação relevante: [Técnicas de factorização de matriz para sistemas de recomendação](https://datajobs.com/data-science-repo/Recommender-Systems-[Netflix].pdf).


## <a name="how-to-configure-train-svd-recommender"></a>Como configurar o recomendador SVD do comboio  

### <a name="prepare-data"></a>Preparar dados

Antes de utilizar o módulo, os seus dados de entrada devem estar no formato que o modelo de recomendação espera. É necessário um conjunto de dados de formação de triplos de classificação de item do utilizador.

+ A primeira coluna contém identificadores de utilizador.
+ A segunda coluna contém identificadores de item.
+ A terceira coluna contém a classificação para o par de artigos de utilizador. Os valores de classificação devem ser do tipo numérico.  

O **conjunto de dados de classificações de** filmes no designer de machine learning do Azure (selecione **Datasets** e, em seguida, **Samples)** demonstra o formato esperado:

![Classificações de Filmes](media/module/movie-ratings-dataset.png)

A partir desta amostra, pode ver que um único utilizador classificou vários filmes. 

### <a name="train-the-model"></a>Preparar o modelo

1.  Adicione o módulo de recomendador SVD do comboio ao seu oleoduto no designer e conecte-o aos dados de treino.  
   
2.  Para **o número de fatores, especifique** o número de fatores a utilizar com o recomendador.  
    
    Cada fator mede o quanto o utilizador está relacionado com o item. O número de fatores é também a dimensionalidade do espaço do fator latente. Com o número de utilizadores e itens a aumentar, é melhor definir um maior número de fatores. Mas se o número for muito grande, o desempenho pode cair.
    
3.  **O número de iterações de algoritmo de recomendação** indica quantas vezes o algoritmo deve processar os dados de entrada. Quanto maior este número for, mais precisas são as previsões. No entanto, um número mais elevado significa um treino mais lento. O valor predefinido é 30.

4.  Para **a taxa de Aprendizagem,** insira um número entre 0,0 e 2.0 que define o tamanho do passo para a aprendizagem.

    A taxa de aprendizagem determina o tamanho do passo em cada iteração. Se o tamanho do passo for demasiado grande, poderá ultrapassar a solução ideal. Se o tamanho do degrau for muito pequeno, o treino demora mais tempo a encontrar a melhor solução. 
  
5.  Envie o oleoduto.  

## <a name="results"></a>Resultados

Após a execução do gasoduto, para utilizar o modelo de pontuação, ligue o [Recomendador SVD do comboio](train-svd-recommender.md) à [Pontuação SVD Recommender,](score-svd-recommender.md)para prever valores para novos exemplos de entrada.

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 
