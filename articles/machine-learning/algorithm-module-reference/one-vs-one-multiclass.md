---
title: Multiclasse One-vs-One
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo One-vs-One Multiclass em Azure Machine Learning para criar um modelo de classificação multiclasse a partir de um conjunto de modelos de classificação binária.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/16/2019
ms.openlocfilehash: 1c08ba51b815386783c3412e9238d2e96da03ff9
ms.sourcegitcommit: 051908e18ce42b3b5d09822f8cfcac094e1f93c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2020
ms.locfileid: "94377695"
---
# <a name="one-vs-one-multiclass"></a>Multiclasse One-vs-One

Este artigo descreve como usar o módulo One-vs-One Multiclass no designer Azure Machine Learning. O objetivo é criar um modelo de classificação que possa prever várias classes, utilizando a abordagem *um-contra-um.*

Este módulo é útil para a criação de modelos que prevejam três ou mais resultados possíveis, quando o resultado depende de variáveis preditores contínuas ou categóricas. Este método também permite usar métodos de classificação binários para problemas que requerem várias classes de saída.

### <a name="more-about-one-versus-one-models"></a>Mais sobre modelos um-versus-um

Alguns algoritmos de classificação permitem o uso de mais de duas classes por design. Outros restringem os resultados possíveis a um de dois valores (um modelo binário ou de duas classes). Mas mesmo algoritmos binários de classificação podem ser adaptados para tarefas de classificação multi-classes através de uma variedade de estratégias. 

Este módulo implementa o método one-versus-one, no qual um modelo binário é criado por par de classes. Na hora da previsão, a classe que recebeu mais votos é selecionada. Uma vez que requer a adaptação dos `n_classes * (n_classes - 1) / 2` classificadores, este método é geralmente mais lento do que um contra todos, devido à sua complexidade O(n_classes^2). No entanto, este método pode ser vantajoso para algoritmos como algoritmos de kernel que não escalam bem com `n_samples` . Isto porque cada problema de aprendizagem individual envolve apenas um pequeno subconjunto dos dados, enquanto que, com um-versus-todos, o conjunto de dados completo é usado `n_classes` tempos.

No essencial, o módulo cria um conjunto de modelos individuais e depois funde os resultados, para criar um único modelo que preveja todas as classes. Qualquer classificador binário pode ser usado como base para um modelo um contra um.  

Por exemplo, digamos que configura um modelo [de máquina de vetor de suporte de duas classes e fornece-o](two-class-support-vector-machine.md) como entrada para o módulo Multiclasse One-vs-One. O módulo criaria modelos de máquinas de vetor de dois classes para todos os membros da classe de saída. Aplicaria então o método um-contra um para combinar os resultados para todas as classes.  

## <a name="how-to-configure-the-one-vs-one-multiclass-classifier"></a>Como configurar o classificador One-vs-One Multiclass  

Este módulo cria um conjunto de modelos de classificação binária para analisar várias classes. Para utilizar este módulo, é necessário configurar e treinar primeiro um modelo *de classificação binária.* 

Ligue o modelo binário ao módulo One-vs-One Multiclass. Em seguida, treina o conjunto de modelos utilizando [o Train Model](train-model.md) com um conjunto de dados de treino rotulado.

Ao combinar os modelos, o One-vs-One Multiclass cria vários modelos de classificação binária, otimiza o algoritmo para cada classe e, em seguida, funde os modelos. O módulo faz estas tarefas mesmo que o conjunto de dados de treino possa ter vários valores de classe.

1. Adicione o módulo One-vs-One Multiclass ao seu pipeline no designer. Pode encontrar este módulo na categoria **Machine Learning - Initialize,** na categoria **Classificação.**

   O classificador One-vs-One Multiclasser não tem parâmetros configuráveis por si só. Quaisquer personalizações devem ser feitas no modelo de classificação binária que é fornecido como entrada.

2. Adicione um modelo de classificação binária ao oleoduto e configuure este modelo. Por exemplo, você pode usar [a máquina de vetor de apoio de duas classes](two-class-support-vector-machine.md) ou a árvore de [decisão reforçada de duas classes.](two-class-boosted-decision-tree.md)

3. Adicione o módulo [Modelo de Comboio](train-model.md) ao seu oleoduto. Ligue o classificador não treinado que é a saída de One-vs-One Multiclasse.

4. Na outra entrada do Modelo de [Comboio,](train-model.md)ligue um conjunto de dados de formação rotulado que tem vários valores de classe.

5. Envie o oleoduto.

## <a name="results"></a>Resultados

Após o treino estar completo, pode usar o modelo para fazer previsões multiclasse.

Em alternativa, pode passar o classificador não treinado para [Modelo De Validação Cruzada](cross-validate-model.md) para validação cruzada contra um conjunto de dados de validação rotulado.


## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 
