---
title: 'Recomendador SVD do comboio: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo recomendador Train SVD em Azure Machine Learning para treinar um recomendador bayesiano utilizando o algoritmo SVD.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 417ef716c391126982809574fc16f6681115cac3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477413"
---
# <a name="train-svd-recommender"></a>Preparar Recomendador SVD

Este artigo descreve como utilizar o módulo recomendador Train SVD no designer de Machine Learning Azure (pré-visualização). Utilize este módulo para formar um modelo de recomendação baseado no algoritmo de decomposição de valor único (SVD).  

O módulo recomendador Do comboio SVD lê um conjunto de dados de triplos de classificação de itens de utilizador. Devolve um recomendador SVD treinado. Em seguida, pode utilizar o modelo treinado para prever classificações ou gerar recomendações, utilizando o módulo [recomendador Score SVD.](score-svd-recommender.md)  


  
## <a name="more-about-recommendation-models-and-the-svd-recommender"></a>Mais sobre modelos de recomendação e recomendação SVD  

O principal objetivo de um sistema de recomendação é recomendar um ou mais *itens* aos *utilizadores* do sistema. Exemplos de um item podem ser um filme, restaurante, livro ou música. Um utilizador pode ser uma pessoa, um grupo de pessoas ou outra entidade com preferências de item.  

Existem duas abordagens principais para sistemas de recomendação: 

+ Uma abordagem **baseada em conteúdo** faz uso de funcionalidades tanto para utilizadores como para itens. Os utilizadores podem ser descritos por propriedades como a idade e o género. Os itens podem ser descritos por propriedades como autor e fabricante. Pode encontrar exemplos típicos de sistemas de recomendação baseados em conteúdo em sites de matchmaking sociais. 
+ **A filtragem colaborativa** utiliza apenas identificadores dos utilizadores e dos itens. Obtém informações implícitas sobre estas entidades a partir de uma matriz (escassa) de classificações dadas pelos utilizadores aos itens. Podemos aprender sobre um utilizador a partir dos itens que avaliaram e de outros utilizadores que avaliaram os mesmos itens.  

O recomendador SVD utiliza identificadores dos utilizadores e dos itens, e uma matriz de classificações dadas pelos utilizadores aos itens. É um *recomendador colaborativo.* 

Para obter mais informações sobre o recomendador SVD, consulte o documento de investigação relevante: [Técnicas de factorização matriz para sistemas de recomendação](https://datajobs.com/data-science-repo/Recommender-Systems-[Netflix].pdf).


## <a name="how-to-configure-train-svd-recommender"></a>Como configurar o recomendador SVD do comboio  

### <a name="prepare-data"></a>Preparar dados

Antes de utilizar o módulo, os seus dados de entrada devem estar no formato que o modelo de recomendação espera. É necessário um conjunto de dados de formação de triplos de classificação de utilizador.

+ A primeira coluna contém identificadores de utilizador.
+ A segunda coluna contém identificadores de objetos.
+ A terceira coluna contém a classificação para o par de artigos de utilizador. Os valores de classificação devem ser do tipo numérico.  

O conjunto de dados **de Classificações** de Filmes em Azure Machine Learning designer (selecione **Datasets** e, em seguida, **Amostras)** demonstra o formato esperado:

![Classificações de filmes](media/module/movie-ratings-dataset.png)

A partir desta amostra, pode ver que um único utilizador classificou vários filmes. 

### <a name="train-the-model"></a>Dar formação sobre o modelo

1.  Adicione o módulo recomendador De Comboio SVD ao seu pipeline no designer e conecte-o aos dados de treino.  
   
2.  Para **o número de fatores,** especifique o número de fatores a utilizar com o recomendador.  
    
    Cada fator mede o quanto o utilizador está relacionado com o item. O número de fatores é também a dimensionalidade do espaço do fator latente. Com o número de utilizadores e itens a aumentar, é melhor definir um maior número de fatores. Mas se o número for muito grande, o desempenho pode cair.
    
3.  **O número de iterações de algoritmos de recomendação** indica quantas vezes o algoritmo deve processar os dados de entrada. Quanto maior for este número, mais precisas são as previsões. No entanto, um número mais elevado significa treino mais lento. O valor predefinido é 30.

4.  Para **a taxa de aprendizagem**, insira um número entre 0,0 e 2.0 que define o tamanho do passo para a aprendizagem.

    A taxa de aprendizagem determina o tamanho do passo em cada iteração. Se o tamanho do passo for demasiado grande, poderá ultrapassar a solução ideal. Se o tamanho do passo for muito pequeno, o treino demora mais tempo a encontrar a melhor solução. 
  
5.  Submeta o oleoduto.  


## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 
