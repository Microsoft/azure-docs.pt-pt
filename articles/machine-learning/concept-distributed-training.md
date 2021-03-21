---
title: O que é treino distribuído?
titleSuffix: Azure Machine Learning
description: Saiba que tipo de formação distribuída a Azure Machine Learning suporta e as integrações de enquadramento de código aberto disponíveis para formação distribuída.
services: machine-learning
ms.service: machine-learning
author: nibaccam
ms.author: nibaccam
ms.subservice: core
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: f87175500fcf5bdbcf9a5c2f499f6bab96b37b63
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102498970"
---
# <a name="distributed-training-with-azure-machine-learning"></a>Treino distribuído com Azure Machine Learning

Neste artigo, aprende-se sobre o treino distribuído e como a Azure Machine Learning o suporta para modelos de aprendizagem profunda. 

Na formação distribuída, a carga de trabalho para formar um modelo é dividida e partilhada entre vários mini-processadores, chamados nós de trabalhadores. Estes nós operários trabalham em paralelo para acelerar a formação de modelos. O treino distribuído pode ser usado para modelos tradicionais de ML, mas é mais adequado para tarefas de computação e tempo intensivo, como [aprendizagem profunda](concept-deep-learning-vs-machine-learning.md) para treinar redes neuronais profundas. 

## <a name="deep-learning-and-distributed-training"></a>Aprendizagem profunda e formação distribuída 

Existem dois tipos principais de formação distribuída: [paralelismo de dados](#data-parallelism) e [paralelismo de modelo.](#model-parallelism) Para formação distribuída em modelos de deep learning, o [Azure Machine Learning SDK em Python](/python/api/overview/azure/ml/intro) suporta integrações com quadros populares, PyTorch e TensorFlow. Ambos os quadros empregam paralelismo de dados para formação distribuída, e podem aproveitar [horovod](https://horovod.readthedocs.io/en/latest/summary_include.html) para otimizar velocidades de computação. 

* [Preparação distribuída com o PyTorch](how-to-train-pytorch.md#distributed-training)

* [Treino distribuído com TensorFlow](how-to-train-tensorflow.md#distributed-training)

Para modelos ML que não necessitam de formação distribuída, consulte [modelos de comboio com Azure Machine Learning](concept-train-machine-learning-model.md#python-sdk) para as diferentes formas de treinar modelos usando o Python SDK.

## <a name="data-parallelism"></a>Paralelismo de dados

O paralelismo de dados é o mais fácil de implementar das duas abordagens de formação distribuídas, e é suficiente para a maioria dos casos de utilização.

Nesta abordagem, os dados são divididos em divisórias, onde o número de divisórias é igual ao número total de nós disponíveis, no cluster compute. O modelo é copiado em cada um destes nós de trabalhadores, e cada trabalhador opera no seu próprio subconjunto dos dados. Tenha em mente que cada nó tem de ter a capacidade de suportar o modelo que está a ser treinado, ou seja, o modelo tem de se encaixar inteiramente em cada nó. O diagrama seguinte proporciona uma demonstração visual desta abordagem.

![Data-paralelismo-diagrama de conceito](./media/concept-distributed-training/distributed-training.svg)

Cada nó calcula independentemente os erros entre as suas previsões para as suas amostras de treino e as saídas rotuladas. Por sua vez, cada nó atualiza o seu modelo com base nos erros e deve comunicar todas as suas alterações aos outros nós para atualizar os seus modelos correspondentes. Isto significa que os nós dos trabalhadores precisam de sincronizar os parâmetros do modelo, ou gradientes, no final do cálculo do lote para garantir que estão a treinar um modelo consistente. 

## <a name="model-parallelism"></a>Paralelismo modelo

No paralelismo modelo, também conhecido como paralelismo de rede, o modelo é segmentado em diferentes partes que podem ser executadas simultaneamente em diferentes nós, e cada um será executado nos mesmos dados. A escalabilidade deste método depende do grau de paralelização de tarefas do algoritmo, e é mais complexo implementar do que o paralelismo de dados. 

No paralelismo modelo, os nós dos trabalhadores só precisam de sincronizar os parâmetros partilhados, geralmente uma vez para cada passo de propagação para a frente ou para trás. Além disso, modelos maiores não são uma preocupação, uma vez que cada nó opera numa subsecção do modelo nos mesmos dados de treino.

## <a name="next-steps"></a>Passos seguintes

* Aprenda a [usar alvos de computação para o treino de modelo](how-to-set-up-training-targets.md) com o Python SDK.
* Para um exemplo técnico, consulte o [cenário de arquitetura de referência.](/azure/architecture/reference-architectures/ai/training-deep-learning)
* [Treinar modelos ML com TensorFlow](how-to-train-tensorflow.md).
* [Treinar modelos ML com PyTorch.](how-to-train-pytorch.md)