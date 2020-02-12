---
title: 'Regressão logística multiclasse: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo regressão logística multiclasse no Azure Machine Learning para criar um modelo de regressão logística que pode ser usado para prever vários valores.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: a697c1e43ccd486f8c98399ea9065902bd247f7d
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152147"
---
# <a name="multiclass-logistic-regression-module"></a>Módulo de regressão logística multiclasse

Este artigo descreve um módulo em Azure Machine Learning designer (pré-visualização).

Use este módulo para criar um modelo de regressão logística que pode ser usado para prever vários valores.

A classificação usando a regressão logística é um método de aprendizado supervisionado e, portanto, requer um conjunto de informações rotulado. Treina o modelo fornecendo o modelo e o conjunto de dados rotulado como uma entrada para um módulo como [o Modelo de Comboio](./train-model.md). O modelo treinado pode então ser usado para prever valores para novos exemplos de entrada.

O Azure Machine Learning também fornece um módulo [de regressão logística de duas classes,](./two-class-logistic-regression.md) adequado para a classificação de variáveis binárias ou dichotos.

## <a name="about-multiclass-logistic-regression"></a>Sobre a regressão logística multiclasse

A regressão logística é um método bem conhecido em estatísticas que é usado para prever a probabilidade de um resultado e é popular para tarefas de classificação. O algoritmo prevê a probabilidade de ocorrência de um evento ajustando dados a uma função logística. 

Em regressão logística multiclasse, o classificador pode ser usado para prever vários resultados.

## <a name="configure-a-multiclass-logistic-regression"></a>Configurar uma regressão logística multiclasse

1. Adicione o módulo **de regressão logística multiclasse** ao oleoduto.

2. Especifique como pretende que o modelo seja treinado, definindo a opção modo **Criar.**

    + **Parâmetro único**: Utilize esta opção se souber como pretende configurar o modelo e fornecer um conjunto específico de valores como argumentos.

    + **Gama de parâmetros**: Utilize esta opção se não tiver a certeza dos melhores parâmetros e pretenda utilizar uma varredura de parâmetros.

3. **Tolerância à otimização,** especifique o valor limiar para a convergência do optimizador. Se a melhoria entre as iterações for menor que o limite, o algoritmo parará e retornará o modelo atual.

4. **Peso de regularização L1**, **Peso de regularização L2**: Digite um valor a utilizar para os parâmetros de regularização L1 e L2. Um valor diferente de zero é recomendado para ambos.

    A regularização é um método para evitar o superajuste por meio da penalização de modelos com valores de coeficiente extremo. A regularização funciona adicionando a penalidade associada aos valores de coeficiente ao erro da hipótese. Um modelo preciso com valores de coeficiente extremo seria penalizado mais, mas um modelo menos preciso com valores mais conservadores seria penalizado menos.

     A regularização L1 e L2 tem efeitos e usos diferentes. L1 pode ser aplicado a modelos esparsos, o que é útil ao trabalhar com dados altamente dimensionais. Por outro lado, a regularização L2 é preferível para dados que não são esparsos.  Este algoritmo suporta uma combinação linear de valores de regularização L1 e L2: isto é, se `x = L1` e `y = L2`, `ax + by = c` define o período linear dos termos de regularização.

     Foram concebidas diferentes combinações lineares de termos L1 e L2 para modelos de regressão logística, como a [regularização elástica da rede.](https://wikipedia.org/wiki/Elastic_net_regularization)

6. **Semente**de número aleatório: Digite um valor inteiro para usar como semente para o algoritmo se quiser que os resultados sejam repetíveis ao longo de runs. Caso contrário, um valor de relógio do sistema será usado como a semente, o que pode produzir resultados ligeiramente diferentes em execuções do mesmo pipeline.

8. Conecte um DataSet rotulado e um dos módulos de treinamento:

    + Se definir o modo Criar modo **de treinador** para um parâmetro **único,** utilize o módulo Modelo de [Comboio.](./train-model.md)

9. Executar o pipeline.



## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 