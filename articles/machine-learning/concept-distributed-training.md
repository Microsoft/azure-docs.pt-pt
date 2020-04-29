---
title: O que é treino distribuído?
titleSuffix: Azure Machine Learning
description: Aprenda sobre formação distribuída e como o Azure Machine Learning o suporta.
services: machine-learning
ms.service: machine-learning
author: nibaccam
ms.author: nibaccam
ms.subservice: core
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: a0d5bf795e4759a105b9a235770f37aa10bd6751
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80385548"
---
# <a name="distributed-training-with-azure-machine-learning"></a>Formação distribuída com Aprendizagem automática Azure

Neste artigo, aprende-se sobre formação distribuída e como o Azure Machine Learning o apoia para modelos de aprendizagem profunda. 

Na formação distribuída, a carga de trabalho para treinar um modelo é dividida e partilhada entre vários mini processadores, chamados nós operários. Estes nódosos operários trabalham paralelamente para acelerar a formação de modelos. A formação distribuída pode ser usada para modelos tradicionais de ML, mas é mais adequada para tarefas intensivas em computação e tempo, como [a aprendizagem profunda](concept-deep-learning-vs-machine-learning.md) para a formação de redes neuronais profundas.

## <a name="deep-learning-and-distributed-training"></a>Aprendizagem profunda e formação distribuída 

Existem dois tipos principais de formação distribuída: [paralelismo](#data-parallelism) de dados e [paralelismo de modelos.](#model-parallelism) Para formação distribuída em modelos de aprendizagem profunda, o [Azure Machine Learning SDK em Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) suporta integrações com estruturas populares, PyTorch e TensorFlow. Ambos os quadros empregam paralelismo de dados para a formação distribuída, e podem aproveitar o [horovod](https://horovod.readthedocs.io/en/latest/summary_include.html) para otimizar velocidades de computação. 

* [Treino distribuído com PyTorch](how-to-train-pytorch.md#distributed-training)

* [Formação distribuída com TensorFlow](how-to-train-tensorflow.md#distributed-training)

Para modelos ML que não requerem formação distribuída, consulte modelos de [comboio com Azure Machine Learning](concept-train-machine-learning-model.md#python-sdk) para as diferentes formas de treinar modelos usando o Python SDK.

## <a name="data-parallelism"></a>Paralelismo de dados

O paralelismo de dados é o mais fácil de implementar das duas abordagens de formação distribuídas, e é suficiente para a maioria dos casos de utilização.

Nesta abordagem, os dados são divididos em divisórias, onde o número de divisórias é igual ao número total de nós disponíveis, no cluster computacional. O modelo é copiado em cada um destes nós operários, e cada trabalhador opera no seu próprio subconjunto de dados. Tenha em mente que cada nó tem de ter a capacidade de suportar o modelo que está a ser treinado, ou seja, o modelo tem de se encaixar inteiramente em cada nó.

Cada nó calcula de forma independente os erros entre as suas previsões para as suas amostras de formação e as saídas rotuladas. Por sua vez, cada nó atualiza o seu modelo com base nos erros e deve comunicar todas as suas alterações aos outros nós para atualizar os seus modelos correspondentes. Isto significa que os nós dos trabalhadores precisam de sincronizar os parâmetros do modelo, ou gradientes, no final do computação do lote para garantir que estão a treinar um modelo consistente. 

## <a name="model-parallelism"></a>Paralelismo modelo

No paralelismo de modelos, também conhecido como paralelismo de rede, o modelo é segmentado em diferentes partes que podem ser executadas simultaneamente em diferentes nós, e cada um deles será executado com os mesmos dados. A escalabilidade deste método depende do grau de paraletização da tarefa do algoritmo, e é mais complexo implementar do que o paralelismo de dados. 

No paralelismo do modelo, os nós dos trabalhadores só precisam de sincronizar os parâmetros partilhados, geralmente uma vez para cada passo de propagação para a frente ou para trás. Além disso, os modelos maiores não são uma preocupação, uma vez que cada nó opera numa subsecção do modelo nos mesmos dados de formação.

## <a name="next-steps"></a>Passos seguintes

* Aprenda a [criar ambientes](how-to-set-up-training-targets.md) de treino com o Python SDK.
* Para um exemplo técnico, consulte o cenário de [arquitetura de referência.](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/training-deep-learning)
* [Treine os modelos ML com TensorFlow](how-to-train-tensorflow.md).
* [Treine os modelos ML com PyTorch](how-to-train-pytorch.md). 