---
title: 'Importância do recurso de permuta: referência de módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como usar o módulo de importância do recurso de permuta no serviço de Azure Machine Learning para computar as pontuações de importância do recurso de permuta das variáveis de recurso, considerando um modelo treinado e um conjunto de dados de teste.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 8a2ddb92101957a3dcadebb17dffa39113825e4b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73517946"
---
# <a name="permutation-feature-importance"></a>Importância do recurso de permuta

Este artigo descreve como usar o módulo de [importância do recurso de permuta](permutation-feature-importance.md) no designer de Azure Machine Learning (versão prévia), para computar um conjunto de pontuações de importância do recurso para seu conjunto de seus. Você usa essas pontuações para ajudá-lo a determinar os melhores recursos a serem usados em um modelo.

Nesse módulo, os valores de recurso são aleatoriamente aleatórios, uma coluna por vez e o desempenho do modelo é medido antes e depois. Você pode escolher uma das métricas padrão fornecidas para medir o desempenho.

As pontuações que o módulo retorna representam a **alteração** no desempenho de um modelo treinado, após a permutação. Os recursos importantes geralmente são mais sensíveis ao processo de embaralhamento e, portanto, resultarão em pontuações de importância mais alta. 

Este artigo fornece uma boa visão geral da importância do recurso de permuta, sua base teórica e seus aplicativos no aprendizado de máquina: [importância do recurso de permuta](http://blogs.technet.com/b/machinelearning/archive/2015/04/14/permutation-feature-importance.aspx)  

## <a name="how-to-use-permutation-feature-importance"></a>Como usar a importância do recurso de permuta

Para gerar um conjunto de pontuações de recursos, é necessário que você tenha um modelo já treinado, bem como um conjunto de teste.  

1.  Adicione o módulo **importância do recurso de permuta** ao seu pipeline. Você pode encontrar esse módulo na categoria **seleção de recursos** . 

2.  Conecte um modelo treinado à entrada à esquerda. O modelo deve ser um modelo de regressão ou modelo de classificação.  

3.  Na entrada correta, conecte um conjunto de dados, preferencialmente um que seja diferente do conjunto de dados usado para treinar o modelo. Esse conjunto de recursos é usado para pontuação com base no modelo treinado e para avaliar o modelo após a alteração dos valores de recurso.  

4.  Para **semente aleatória**, digite um valor a ser usado como semente para randomização. Se você especificar 0 (o padrão), um número será gerado com base no relógio do sistema.

     Um valor de semente é opcional, mas você deve fornecer um valor se quiser reprodução em execuções do mesmo pipeline.  

5.  Para **métrica para medir o desempenho**, selecione uma única métrica a ser usada ao computar a qualidade do modelo após a permuta.  

     O Azure Machine Learning designer dá suporte às seguintes métricas, dependendo se você está avaliando um modelo de classificação ou regressão:  

    -   **Classification** (Classificação)

        Precisão, precisão, recall, perda de log média  

    -   **Regressão**

        Precisão, cancelamento, erro absoluto médio, erro de raiz quadrada média, erro absoluto relativo, erro de quadrado relativo, coeficiente de determinação  

     Para obter uma descrição mais detalhada dessas métricas de avaliação e como elas são calculadas, consulte [avaliar modelo](evaluate-model.md).  

6.  Executar o pipeline.  

7.  O módulo gera uma lista de colunas de recursos e as pontuações associadas a elas, classificadas em ordem de pontuação, decrescente.  


##  <a name="technical-notes"></a>Notas técnicas

Esta seção fornece detalhes de implementação, dicas e respostas para perguntas frequentes.

### <a name="how-does-this-compare-to-other-feature-selection-methods"></a>Como isso se compara a outros métodos de seleção de recursos?

A importância do recurso de permuta funciona alterando aleatoriamente os valores de cada coluna de recursos, uma coluna de cada vez e, em seguida, avaliando o modelo. 

As classificações fornecidas pela importância do recurso de permuta são muitas vezes diferentes das que você obtém da [seleção de recursos baseada em filtro](filter-based-feature-selection.md), que calcula as pontuações **antes** de um modelo ser criado. 

Isso ocorre porque a importância do recurso de permuta não mede a associação entre um recurso e um valor de destino, mas, em vez disso, captura quanto influência cada recurso tem em previsões do modelo.
  
## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning serviço. 
