---
title: Multiclasse One-vs-All
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo Multiclasse One-vs-All em Azure Machine Learning para criar um modelo de classificação multiclasse a partir de um conjunto de modelos de classificação binária.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/16/2019
ms.openlocfilehash: 29934758ab729e0fb888c10b7f834da3d0bf7fb0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456085"
---
# <a name="one-vs-all-multiclass"></a>Multiclasse One-vs-All

Este artigo descreve como usar o módulo Multiclasse One-vs-All no designer de Machine Learning Azure (pré-visualização). O objetivo é criar um modelo de classificação que possa prever várias classes, utilizando a abordagem *um-contra-tudo.*

Este módulo é útil para criar modelos que prevejam três ou mais resultados possíveis, quando o resultado depende de variáveis de previsão contínua ou categórica. Este método também permite usar métodos de classificação binárias para problemas que requerem várias classes de saída.

### <a name="more-about-one-versus-all-models"></a>Mais sobre modelos um contra todos

Alguns algoritmos de classificação permitem o uso de mais de duas classes por design. Outros restringem os resultados possíveis a um de dois valores (um modelo binário, ou de duas classes). Mas mesmo algoritmos de classificação binários podem ser adaptados para tarefas de classificação multi-classe através de uma variedade de estratégias. 

Este módulo implementa o método one-versus-all, no qual é criado um modelo binário para cada uma das classes de saída múltiplas. O módulo avalia cada um destes modelos binários para as classes individuais contra o seu complemento (todas as outras classes do modelo) como se fosse uma questão de classificação binária. Em seguida, o módulo executa a previsão executando estes classificadores binários e escolhendo a previsão com a pontuação de confiança mais alta.  

No essencial, o módulo cria um conjunto de modelos individuais e depois funde os resultados, para criar um único modelo que preveja todas as classes. Qualquer classificador binário pode ser usado como base para um modelo único contra todos.  

Por exemplo, digamos que configura um modelo de máquina de [vetores de suporte de duas classes](two-class-support-vector-machine.md) e fornece-o como entrada para o módulo Multiclasse One-vs-All. O módulo criaria modelos de máquinas vetoriais de suporte de duas classes para todos os membros da classe de saída. Aplicaria então o método um contra todos para combinar os resultados de todas as classes.  

## <a name="how-to-configure-the-one-vs-all-multiclass-classifier"></a>Como configurar o classificador One-vs-All Multiclass  

Este módulo cria um conjunto de modelos de classificação binária para analisar várias classes. Para utilizar este módulo, é necessário configurar e treinar primeiro um modelo *de classificação binário.* 

Liga o modelo binário ao módulo Multiclasse One-vs-All. Em seguida, treina o conjunto de modelos utilizando [o Modelo de Comboio](train-model.md) com um conjunto de dados de treino marcado.

Ao combinar os modelos, a One-vs-All Multiclass cria vários modelos de classificação binária, otimiza o algoritmo para cada classe e, em seguida, funde os modelos. O módulo faz estas tarefas mesmo que o conjunto de dados de treino possa ter vários valores de classe.

1. Adicione o módulo Multiclasse One-vs-All ao seu pipeline no designer. Pode encontrar este módulo em **Machine Learning - Inicializar,** na categoria **Classificação.**

   O classificador One-vs-All Multiclass não tem parâmetros configuráveis por si só. Quaisquer personalizações devem ser feitas no modelo de classificação binária que é fornecido como entrada.

2. Adicione um modelo de classificação binário ao pipeline e configure este modelo. Por exemplo, você pode usar [máquina de vetor de suporte de duas classes](two-class-support-vector-machine.md) ou árvore de decisão impulsionada de duas [classes](two-class-boosted-decision-tree.md).

3. Adicione o módulo [Modelo de Comboio](train-model.md) ao seu pipeline. Ligue o classificador destreinado que é a saída de One-vs-All Multiclass.

4. Na outra entrada do [Modelo de Comboio,](train-model.md)ligue um conjunto de dados de treino marcado com vários valores de classe.

5. Submeta o oleoduto.

## <a name="results"></a>Resultados

Após o treino estar completo, pode usar o modelo para fazer previsões multi-classe.

Em alternativa, pode passar o classificador não treinado para [o Modelo De Validação Cruzada](cross-validate-model.md) para validação cruzada contra um conjunto de dados de validação rotulado.


## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 
