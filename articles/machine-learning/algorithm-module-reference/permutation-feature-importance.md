---
title: 'Importância da característica da permutação: referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo De Importância de Recurso de Permutação no designer para calcular a permutação característica pontuações de variáveis de características.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/24/2020
ms.openlocfilehash: 8ae1e79922cc0f34e8b2d1f253fce5078df286d2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "93421248"
---
# <a name="permutation-feature-importance"></a>Importância da funcionalidade de permutação

Este artigo descreve como usar o módulo de Importância de Recurso de Permutação no designer Azure Machine Learning, para calcular um conjunto de pontuações de importância de recurso para o seu conjunto de dados. Utiliza estas pontuações para o ajudar a determinar as melhores funcionalidades para utilizar num modelo.

Neste módulo, os valores de características são baralhados aleatoriamente, uma coluna de cada vez. O desempenho do modelo é medido antes e depois. Pode escolher uma das métricas padrão para medir o desempenho.

As pontuações que o módulo devolve representam a *mudança* no desempenho de um modelo treinado, após permutação. Características importantes são geralmente mais sensíveis ao processo de baralhar, por isso resultarão em pontuações de maior importância. 

Este artigo fornece uma visão geral da característica de permutação, da sua base teórica, e das suas aplicações em machine learning: [Permutaation Feature Import](/archive/blogs/machinelearning/permutation-feature-importance).  

## <a name="how-to-use-permutation-feature-importance"></a>Como usar a importância da característica de permutação

Gerar um conjunto de pontuações de funcionalidades requer que tenha um modelo já treinado, bem como um conjunto de dados de teste.  

1.  Adicione o módulo de Importância de Recurso de Permutação ao seu oleoduto. Pode encontrar este módulo na categoria **Seleção de Recursos.** 

2.  Ligue um modelo treinado à entrada esquerda. O modelo deve ser um modelo de regressão ou um modelo de classificação.  

3.  Na entrada direita, ligue um conjunto de dados. De preferência, escolha um que seja diferente do conjunto de dados que usou para treinar o modelo. Este conjunto de dados é utilizado para pontuar com base no modelo treinado. Também é usado para avaliar o modelo depois de os valores de recurso terem mudado.  

4.  Para **sementes aleatórias,** insira um valor a utilizar como semente para aleatoriedade. Se especificar 0 (o padrão), um número é gerado com base no relógio do sistema.

     Um valor de sementes é opcional, mas você deve fornecer um valor se você quiser reprodutibilidade através de corridas do mesmo oleoduto.  

5.  Para **medir o desempenho,** selecione uma única métrica para usar quando estiver a calcular a qualidade do modelo após a permutação.  

     O designer de Aprendizagem automática Azure suporta as seguintes métricas, dependendo se está a avaliar um modelo de classificação ou regressão:  

    -   **Classificação**

        Precisão, Precisão, Recordação  

    -   **Regressão**

        Precisão, Recordação, Erro Absoluto Médio, Erro Quadrado Médio De Raiz, Erro Absoluto Relativo, Erro A quadrado relativo, Coeficiente de Determinação  

     Para uma descrição mais detalhada destas métricas de avaliação e como são calculadas, consulte [o Modelo de Avaliação.](evaluate-model.md)  

6.  Envie o oleoduto.  

7.  O módulo faz uma lista de colunas de funcionalidades e as pontuações associadas a elas. A lista está classificada na ordem descendente das pontuações.  


##  <a name="technical-notes"></a>Notas técnicas

Permutação Característica Importância funciona alterando aleatoriamente os valores de cada coluna de recurso, uma coluna de cada vez. Em seguida, avalia o modelo. 

Os rankings que o módulo fornece são muitas vezes diferentes dos que obtém da Seleção de [Recursos Baseados em Filtros.](filter-based-feature-selection.md) A Seleção de Recursos Baseados em Filtro calcula as pontuações *antes da* criação de um modelo. 

A razão para a diferença é que a Importância da Característica de Permutação não mede a associação entre uma característica e um valor-alvo. Em vez disso, captura a influência que cada recurso tem nas previsões do modelo.
  
## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning.