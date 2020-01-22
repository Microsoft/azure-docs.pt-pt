---
title: 'Regressão logística multiclasse: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo regressão logística multiclasse no Azure Machine Learning para criar um modelo de regressão logística que pode ser usado para prever vários valores.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: 8d6140e97383fb07e65c87f74a555046f918ac41
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76314866"
---
# <a name="multiclass-logistic-regression-module"></a>Módulo de regressão logística multiclasse

Este artigo descreve um módulo no designer de Azure Machine Learning.

Use este módulo para criar um modelo de regressão logística que pode ser usado para prever vários valores.

A classificação usando a regressão logística é um método de aprendizado supervisionado e, portanto, requer um conjunto de informações rotulado. Você treina o modelo fornecendo o modelo e o conjunto de dados rotulado como uma entrada para um módulo, como o [modelo de treinamento](./train-model.md). O modelo treinado pode então ser usado para prever valores para novos exemplos de entrada.

Azure Machine Learning também fornece um módulo de [regressão logística de duas classes](./two-class-logistic-regression.md) , que é adequado para classificação de variáveis binárias ou dicotômicass.

## <a name="about-multiclass-logistic-regression"></a>Sobre a regressão logística multiclasse

A regressão logística é um método bem conhecido em estatísticas que é usado para prever a probabilidade de um resultado e é popular para tarefas de classificação. O algoritmo prevê a probabilidade de ocorrência de um evento ajustando dados a uma função logística. 

Em regressão logística multiclasse, o classificador pode ser usado para prever vários resultados.

## <a name="configure-a-multiclass-logistic-regression"></a>Configurar uma regressão logística multiclasse

1. Adicione o módulo **regressão logística multiclasse** ao pipeline.

2. Especifique como você deseja que o modelo seja treinado, definindo a opção **criar modo de instrutor** .

    + **Parâmetro único**: Use essa opção se você souber como deseja configurar o modelo e fornecer um conjunto específico de valores como argumentos.

    + **Intervalo de parâmetros**: Use essa opção se você não tiver certeza dos melhores parâmetros e quiser usar uma limpeza de parâmetro.

3. **Tolerância de otimização**, especifique o valor de limite para a convergência do otimizador. Se a melhoria entre as iterações for menor que o limite, o algoritmo parará e retornará o modelo atual.

4. **Peso de regularização de L1**, **peso de regularização L2**: digite um valor a ser usado para os parâmetros de regularização L1 e L2. Um valor diferente de zero é recomendado para ambos.

    A regularização é um método para evitar o superajuste por meio da penalização de modelos com valores de coeficiente extremo. A regularização funciona adicionando a penalidade associada aos valores de coeficiente ao erro da hipótese. Um modelo preciso com valores de coeficiente extremo seria penalizado mais, mas um modelo menos preciso com valores mais conservadores seria penalizado menos.

     A regularização L1 e L2 tem efeitos e usos diferentes. L1 pode ser aplicado a modelos esparsos, o que é útil ao trabalhar com dados altamente dimensionais. Por outro lado, a regularização L2 é preferível para dados que não são esparsos.  Este algoritmo dá suporte a uma combinação linear de valores de regularização L1 e L2: ou seja, se `x = L1` e `y = L2`, `ax + by = c` definirá o intervalo linear dos termos de regularização.

     Combinações lineares diferentes de termos de L1 e L2 foram desenvolvedas para modelos de regressão logística, como [regularização de rede elástica](https://wikipedia.org/wiki/Elastic_net_regularization).

6. **Semente de número aleatório**: digite um valor inteiro para usar como a semente do algoritmo se desejar que os resultados sejam repetidos em execuções. Caso contrário, um valor de relógio do sistema será usado como a semente, o que pode produzir resultados ligeiramente diferentes em execuções do mesmo pipeline.

8. Conecte um DataSet rotulado e um dos módulos de treinamento:

    + Se você definir **criar modo de instrutor** como **um único parâmetro**, use o módulo [treinar modelo](./train-model.md) .

9. Executar o pipeline.



## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 