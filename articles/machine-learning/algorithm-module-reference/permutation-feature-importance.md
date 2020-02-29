---
title: 'Importância da característica permutação: referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo de importância da característica permutação em Azure Machine Learning para calcular a característica de permutação pontuações de variáveis de características, dado um modelo treinado e um conjunto de dados de teste.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/24/2020
ms.openlocfilehash: 8611abd4e504e0378b744b12d0adb5fa22f5476b
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77920370"
---
# <a name="permutation-feature-importance"></a>Importância da funcionalidade de permutação

Este artigo descreve como usar o módulo de importância da funcionalidade de permutação no designer de machine learning azure (pré-visualização), para calcular um conjunto de pontuações de importância de funcionalidades para o seu conjunto de dados. Utiliza estas pontuações para o ajudar a determinar as melhores funcionalidades para utilizar num modelo.

Neste módulo, os valores da funcionalidade são baralhados aleatoriamente, uma coluna de cada vez. O desempenho do modelo é medido antes e depois. Pode escolher uma das métricas padrão para medir o desempenho.

As pontuações que o módulo devolve representam a *mudança* no desempenho de um modelo treinado, após permutação. Características importantes são geralmente mais sensíveis ao processo de baralhar, por isso resultarão em pontuações de maior importância. 

Este artigo fornece uma visão geral da funcionalidade de permutação, da sua base teórica e das suas aplicações em machine learning: [Permutação Feature Importance](https://blogs.technet.com/b/machinelearning/archive/2015/04/14/permutation-feature-importance.aspx).  

## <a name="how-to-use-permutation-feature-importance"></a>Como usar a importância da característica permutação

Gerar um conjunto de pontuações de funcionalidades requer que tenha um modelo já treinado, bem como um conjunto de dados de teste.  

1.  Adicione o módulo de importância da característica permutação ao seu pipeline. Pode encontrar este módulo na categoria **De Seleção** de Recursos. 

2.  Ligue um modelo treinado à entrada esquerda. O modelo deve ser um modelo de regressão ou um modelo de classificação.  

3.  Na entrada certa, ligue um conjunto de dados. De preferência, escolha um que seja diferente do conjunto de dados que usou para treinar o modelo. Este conjunto de dados é utilizado para pontuar com base no modelo treinado. Também é usado para avaliar o modelo após os valores da funcionalidade terem mudado.  

4.  Para **sementes aleatórias,** introduza um valor a utilizar como semente para aleatoriedade. Se especificar 0 (a predefinição), um número é gerado com base no relógio do sistema.

     Um valor de semente é opcional, mas você deve fornecer um valor se você quiser reprodutibilidade em corridas do mesmo pipeline.  

5.  Para métrica para medir o **desempenho,** selecione uma única métrica para utilizar quando estiver a calcular a qualidade do modelo após a permutação.  

     O designer de Machine Learning Azure suporta as seguintes métricas, dependendo se está a avaliar um modelo de classificação ou regressão:  

    -   **Classification** (Classificação)

        Precisão, Precisão, Recordação  

    -   **Regressão**

        Precisão, Recordação, Erro Absoluto Médio, Erro Quadrado médio da raiz, Erro Absoluto Relativo, Erro Quadrado Relativo, Coeficiente de Determinação  

     Para uma descrição mais detalhada destas métricas de avaliação e como são calculadas, consulte [Modelo de Avaliação](evaluate-model.md).  

6.  Executar o pipeline.  

7.  O módulo produz uma lista de colunas de funcionalidades e as pontuações associadas a elas. A lista está classificada na ordem descendente das pontuações.  


##  <a name="technical-notes"></a>Notas técnicas

Permutação Característica Importância funciona alterando aleatoriamente os valores de cada coluna de características, uma coluna de cada vez. Em seguida, avalia o modelo. 

Os rankings que o módulo fornece são muitas vezes diferentes dos que obtém da Seleção de [Funcionalidades Baseadas](filter-based-feature-selection.md)em Filtros . A seleção de funcionalidades baseada no filtro calcula as pontuações *antes* de um modelo ser criado. 

A razão para a diferença é que a Permutação Característica Importância não mede a associação entre uma característica e um valor-alvo. Em vez disso, captura a influência que cada característica tem nas previsões do modelo.
  
## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 
