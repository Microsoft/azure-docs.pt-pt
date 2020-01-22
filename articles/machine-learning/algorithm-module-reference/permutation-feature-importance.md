---
title: 'Importância do recurso de permuta: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo importância do recurso de permuta no Azure Machine Learning para computar as pontuações de importância do recurso de permuta das variáveis de recurso, dado um modelo treinado e um conjunto de dados de teste.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 9ef859cce68e13f5207d77d8396bc69ddbbce339
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76314951"
---
# <a name="permutation-feature-importance"></a>Importância da funcionalidade de permutação

Este artigo descreve como usar o módulo de importância do recurso de permuta no Azure Machine Learning designer, para computar um conjunto de pontuações de importância do recurso para o conjunto de seus conjuntos de resultados. Você usa essas pontuações para ajudá-lo a determinar os melhores recursos a serem usados em um modelo.

Neste módulo, os valores de recurso são aleatoriamente aleatórios, uma coluna de cada vez. O desempenho do modelo é medido antes e depois. Você pode escolher uma das métricas padrão para medir o desempenho.

As pontuações que o módulo retorna representam a *alteração* no desempenho de um modelo treinado, após a permutação. Normalmente, os recursos importantes são mais sensíveis ao processo de embaralhamento, portanto, eles resultarão em pontuações de importância mais alta. 

Este artigo fornece uma visão geral do recurso de permuta, sua base teórica e seus aplicativos no aprendizado de máquina: [importância do recurso de permutação](https://blogs.technet.com/b/machinelearning/archive/2015/04/14/permutation-feature-importance.aspx).  

## <a name="how-to-use-permutation-feature-importance"></a>Como usar a importância do recurso de permuta

A geração de um conjunto de pontuações de recursos requer que você tenha um modelo já treinado, bem como um conjunto de testes.  

1.  Adicione o módulo importância do recurso de permuta ao seu pipeline. Você pode encontrar esse módulo na categoria **seleção de recursos** . 

2.  Conecte um modelo treinado à entrada à esquerda. O modelo deve ser um modelo de regressão ou um modelo de classificação.  

3.  Na entrada à direita, conecte um conjunto de dados. Preferencialmente, escolha uma que seja diferente do conjunto de um que você usou para treinar o modelo. Esse DataSet é usado para pontuação com base no modelo treinado. Ele também é usado para avaliar o modelo após a alteração dos valores de recurso.  

4.  Para **semente aleatória**, insira um valor a ser usado como semente para randomização. Se você especificar 0 (o padrão), um número será gerado com base no relógio do sistema.

     Um valor de semente é opcional, mas você deve fornecer um valor se quiser reprodução em execuções do mesmo pipeline.  

5.  Para **métrica para medir o desempenho**, selecione uma única métrica a ser usada quando você estiver computando a qualidade do modelo após a permuta.  

     O Azure Machine Learning designer dá suporte às seguintes métricas, dependendo se você está avaliando um modelo de classificação ou regressão:  

    -   **Classification** (Classificação)

        Precisão, precisão, recall, perda de log média  

    -   **Regressão**

        Precisão, cancelamento, erro absoluto médio, erro de raiz quadrada média, erro absoluto relativo, erro de quadrado relativo, coeficiente de determinação  

     Para obter uma descrição mais detalhada dessas métricas de avaliação e como elas são calculadas, consulte [avaliar modelo](evaluate-model.md).  

6.  Executar o pipeline.  

7.  O módulo gera uma lista de colunas de recursos e as pontuações associadas a elas. A lista é classificada em ordem decrescente das pontuações.  


##  <a name="technical-notes"></a>Notas técnicas

A importância do recurso de permuta funciona alterando aleatoriamente os valores de cada coluna de recurso, uma coluna de cada vez. Em seguida, ele avalia o modelo. 

As classificações que o módulo fornece são muitas vezes diferentes daquelas que você obtém da [seleção de recursos baseada em filtro](filter-based-feature-selection.md). A seleção de recursos baseada em filtro calcula pontuações *antes* de um modelo ser criado. 

O motivo da diferença é que a importância do recurso de permuta não mede a associação entre um recurso e um valor de destino. Em vez disso, ele captura quanto influência cada recurso tem em previsões do modelo.
  
## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 
