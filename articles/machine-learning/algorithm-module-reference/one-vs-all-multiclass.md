---
title: Multiclasse Um contra Todos
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo Multiclasse Um contra Todos no Azure Machine Learning para criar um modelo de classificação multiclasse a partir de um Ensemble de modelos de classificação binária.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/16/2019
ms.openlocfilehash: bf4f176eef502cdd1c68f19904a24bd2500e1302
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75428505"
---
# <a name="one-vs-all-multiclass"></a>Multiclasse Um contra Todos

Este artigo descreve como usar o módulo Multiclasse Um contra Todos no designer de Azure Machine Learning (versão prévia). O objetivo é criar um modelo de classificação que possa prever várias classes, usando a abordagem *um-versus-tudo* .

Esse módulo é útil para criar modelos que preveem três ou mais resultados possíveis, quando o resultado depende de variáveis de previsão contínuas ou categóricas. Esse método também permite que você use métodos de classificação binária para problemas que exigem várias classes de saída.

### <a name="more-about-one-versus-all-models"></a>Mais sobre modelos um-versus-todos

Alguns algoritmos de classificação permitem o uso de mais de duas classes por design. Outros restringem os resultados possíveis para um de dois valores (um modelo binário ou de duas classes). Mas mesmo algoritmos de classificação binária podem ser adaptados para tarefas de classificação de várias classes por meio de uma variedade de estratégias. 

Esse módulo implementa o método um-versus-todos, no qual um modelo binário é criado para cada uma das várias classes de saída. O módulo avalia cada um desses modelos binários para as classes individuais em relação a seu complemento (todas as outras classes no modelo) como se fosse um problema de classificação binária. Em seguida, o módulo executa a previsão executando esses classificadores binários e escolhendo a previsão com a pontuação de confiança mais alta.  

Em essência, o módulo cria um Ensemble de modelos individuais e, em seguida, mescla os resultados, para criar um modelo único que prevê todas as classes. Qualquer classificador binário pode ser usado como base para um modelo um-versus-todos.  

Por exemplo, digamos que você configure um modelo de [máquina de vetor de suporte de duas classes](two-class-support-vector-machine.md) e forneça isso como entrada para o módulo multiclasse um contra todos. O módulo criará modelos de máquina de vetor de suporte de duas classes para todos os membros da classe de saída. Em seguida, ele aplicaria o método um-versus-All para combinar os resultados de todas as classes.  

## <a name="how-to-configure-the-one-vs-all-multiclass-classifier"></a>Como configurar o classificador de Multiclasse Um contra Todos  

Esse módulo cria um Ensemble de modelos de classificação binária para analisar várias classes. Para usar este módulo, você precisa configurar e treinar primeiro um modelo de *classificação binária* . 

Você conecta o modelo binário ao módulo Multiclasse Um contra Todos. Em seguida, você treina o Ensemble de modelos usando o [modelo](train-model.md) de treinamento com um conjunto de acordo com o título.

Quando você combina os modelos, Multiclasse Um contra Todos cria vários modelos de classificação binária, otimiza o algoritmo para cada classe e, em seguida, mescla os modelos. O módulo executa essas tarefas, embora o conjunto de módulos de treinamento possa ter vários valores de classe.

1. Adicione o módulo Multiclasse Um contra Todos ao seu pipeline no designer. Você pode encontrar esse módulo em **Machine Learning-Initialize**, na categoria **classificação** .

   O classificador de Multiclasse Um contra Todos não tem nenhum parâmetro configurável. Todas as personalizações devem ser feitas no modelo de classificação binária que é fornecido como entrada.

2. Adicione um modelo de classificação binária ao pipeline e configure esse modelo. Por exemplo, você pode usar uma [máquina de vetor de suporte de duas classes](two-class-support-vector-machine.md) ou [árvore de decisão aumentada de duas classes](two-class-boosted-decision-tree.md).

3. Adicione o módulo [modelo de treinamento](train-model.md) ao seu pipeline. Conecte o classificador não treinado que é a saída de Multiclasse Um contra Todos.

4. Na outra entrada do [modelo de treinamento](train-model.md), conecte um conjunto de dados de treinamento rotulado que tenha vários valores de classe.

5. Executar o pipeline.

## <a name="results"></a>Resultados

Após a conclusão do treinamento, você pode usar o modelo para fazer previsões multiclasse.

Como alternativa, você pode passar o classificador não treinado para o modelo de validação [cruzada para validações](cross-validate-model.md) cruzadas em relação a um conjunto de uma validação rotulada.


## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 
