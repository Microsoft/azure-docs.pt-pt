---
title: One-vs-All Multiclass
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo One-vs-All Multiclass em Azure Machine Learning para criar um modelo de classificação multiclasse a partir de um conjunto de modelos de classificação binária.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/16/2019
ms.openlocfilehash: dfe01e16b55325db03e4150a33ae5c4aa5822ae2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90898502"
---
# <a name="one-vs-all-multiclass"></a>One-vs-All Multiclass

Este artigo descreve como usar o módulo One-vs-All Multiclass no designer Azure Machine Learning. O objetivo é criar um modelo de classificação que possa prever várias classes, utilizando a abordagem *one-versus-all.*

Este módulo é útil para a criação de modelos que prevejam três ou mais resultados possíveis, quando o resultado depende de variáveis preditores contínuas ou categóricas. Este método também permite usar métodos de classificação binários para problemas que requerem várias classes de saída.

### <a name="more-about-one-versus-all-models"></a>Mais sobre modelos one-versus-all

Alguns algoritmos de classificação permitem o uso de mais de duas classes por design. Outros restringem os resultados possíveis a um de dois valores (um modelo binário ou de duas classes). Mas mesmo algoritmos binários de classificação podem ser adaptados para tarefas de classificação multi-classes através de uma variedade de estratégias. 

Este módulo implementa o método one-versus-all, no qual é criado um modelo binário para cada uma das classes de saída múltiplas. O módulo avalia cada um destes modelos binários para as classes individuais contra o seu complemento (todas as outras classes do modelo) como se fosse uma questão de classificação binária. Em seguida, o módulo executa a previsão executando estes classificadores binários e escolhendo a previsão com a pontuação de confiança mais alta.  

No essencial, o módulo cria um conjunto de modelos individuais e depois funde os resultados, para criar um único modelo que preveja todas as classes. Qualquer classificador binário pode ser usado como base para um modelo one-versus-all.  

Por exemplo, digamos que configura um modelo [de máquina de vetor de suporte de duas classes e fornece-o](two-class-support-vector-machine.md) como entrada para o módulo Multiclasse One-vs-All. O módulo criaria modelos de máquinas de vetor de dois classes para todos os membros da classe de saída. Em seguida, aplicaria o método one-versus-all para combinar os resultados para todas as classes.  

## <a name="how-to-configure-the-one-vs-all-multiclass-classifier"></a>Como configurar o classificador One-vs-All Multiclass  

Este módulo cria um conjunto de modelos de classificação binária para analisar várias classes. Para utilizar este módulo, é necessário configurar e treinar primeiro um modelo *de classificação binária.* 

Ligue o modelo binário ao módulo One-vs-All Multiclass. Em seguida, treina o conjunto de modelos utilizando [o Train Model](train-model.md) com um conjunto de dados de treino rotulado.

Ao combinar os modelos, o One-vs-All Multiclass cria vários modelos de classificação binária, otimiza o algoritmo para cada classe e, em seguida, funde os modelos. O módulo faz estas tarefas mesmo que o conjunto de dados de treino possa ter vários valores de classe.

1. Adicione o módulo One-vs-All Multiclass ao seu pipeline no designer. Pode encontrar este módulo na categoria **Machine Learning - Initialize,** na categoria **Classificação.**

   O classificador One-vs-All Multiclass não tem parâmetros configuráveis por si só. Quaisquer personalizações devem ser feitas no modelo de classificação binária que é fornecido como entrada.

2. Adicione um modelo de classificação binária ao oleoduto e configuure este modelo. Por exemplo, você pode usar [a máquina de vetor de apoio de duas classes](two-class-support-vector-machine.md) ou a árvore de [decisão reforçada de duas classes.](two-class-boosted-decision-tree.md)

3. Adicione o módulo [Modelo de Comboio](train-model.md) ao seu oleoduto. Ligue o classificador não treinado que é a saída de One-vs-All Multiclass.

4. Na outra entrada do Modelo de [Comboio,](train-model.md)ligue um conjunto de dados de formação rotulado que tem vários valores de classe.

5. Envie o oleoduto.

## <a name="results"></a>Results

Após o treino estar completo, pode usar o modelo para fazer previsões multiclasse.

Em alternativa, pode passar o classificador não treinado para [Modelo De Validação Cruzada](cross-validate-model.md) para validação cruzada contra um conjunto de dados de validação rotulado.


## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 
