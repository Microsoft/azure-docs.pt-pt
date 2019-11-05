---
title: Multiclasse Um contra Todos
titleSuffix: Azure Machine Learning service
description: Saiba como usar o módulo Multiclasse Um contra Todos no serviço Azure Machine Learning para criar um modelo de classificação multiclasse a partir de um Ensemble de modelos de classificação binária.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/16/2019
ms.openlocfilehash: 9ded83dc5b8b8b98af66c8858214e8f82a4aa166
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73518167"
---
# <a name="one-vs-all-multiclass"></a>Multiclasse Um contra Todos

Este artigo descreve como usar o módulo **multiclasse um contra todos** o designer de Machine Learning do Azure (versão prévia) para criar um modelo de classificação que pode prever várias classes, usando a abordagem "um vs. todos".

Esse módulo é útil para criar modelos que preveem três ou mais resultados possíveis, quando o resultado depende de variáveis de previsão contínuas ou categóricas. Esse método também permite que você use métodos de classificação binária para problemas que exigem várias classes de saída.

### <a name="more-about-one-vs-all-models"></a>Mais sobre modelos de um-vs-todos

Embora alguns algoritmos de classificação permitam o uso de mais de duas classes por design, outras restringem os resultados possíveis a um dos dois valores (um modelo binário ou de duas classes). No entanto, até mesmo os algoritmos de classificação binária podem ser adaptados para tarefas de classificação de várias classes usando uma variedade de estratégias. 

Esse módulo implementa o *método um versus todos*, no qual um modelo binário é criado para cada uma das várias classes de saída. Cada um desses modelos binários para as classes individuais é avaliado em relação ao seu complemento (todas as outras classes no modelo) como se fosse um problema de classificação binária. Em seguida, a previsão é executada executando esses classificadores binários e escolhendo a previsão com a pontuação de confiança mais alta.  

Em essência, um Ensemble de modelos individuais é criado e os resultados são mesclados, para criar um único modelo que prevê todas as classes. Assim, qualquer classificador binário pode ser usado como base para um modelo de um-vs-todos.  

 Por exemplo, digamos que você configure um modelo de [máquina de vetor de suporte de duas classes](two-class-support-vector-machine.md) e forneça isso como entrada para o módulo **multiclasse um contra todos** . O módulo criará modelos de máquina de vetor de suporte de duas classes para todos os membros da classe de saída e, em seguida, aplicaria o método One-vs-All para combinar os resultados de todas as classes.  

## <a name="how-to-configure-the-one-vs-all-classifier"></a>Como configurar o classificador de um-vs-todos  

Esse módulo cria um Ensemble de modelos de classificação binária para analisar várias classes. Portanto, para usar esse módulo, você precisa configurar e treinar primeiro um modelo de **classificação binária** . 

Em seguida, você conecta o modelo binário ao módulo **multiclasse um contra todos** e treina o Ensemble de modelos usando [treinar modelo](train-model.md) com um conjunto de módulos de treinamento com rótulo.

Quando você combina os modelos, embora o conjunto de os de treinamento possa ter vários valores de classe, o **multiclasse um contra todos** cria vários modelos de classificação binária, otimiza o algoritmo para cada classe e, em seguida, mescla os modelos.

1. Adicione o **multiclasse um contra todos** ao seu pipeline no designer. Você pode encontrar esse módulo em Machine Learning-Initialize, na categoria **classificação** .

    O classificador de **multiclasse um contra todos** não tem nenhum parâmetro configurável. Todas as personalizações devem ser feitas no modelo de classificação binária que é fornecido como entrada.

2. Adicione um modelo de classificação binária ao pipeline e configure esse modelo. Por exemplo, você pode usar uma [máquina de vetor de suporte de duas classes](two-class-support-vector-machine.md) ou uma [árvore de decisão aumentada de duas classes](two-class-boosted-decision-tree.md).

3. Adicione o módulo [modelo de treinamento](train-model.md) ao seu pipeline e conecte o classificador não treinado que é a saída de **multiclasse um contra todos**.

4. Na outra entrada do [modelo de treinamento](train-model.md), conecte um conjunto de dados de treinamento rotulado que tenha vários valores de classe.

5. Executar o pipeline.

## <a name="results"></a>Resultados

Após a conclusão do treinamento, você pode usar o modelo para fazer previsões multiclasse.

Como alternativa, você pode passar o classificador não treinado para o modelo de validação [cruzada para validações](cross-validate-model.md) cruzadas em relação a um conjunto de dados de validação rotulado.


## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning serviço. 
