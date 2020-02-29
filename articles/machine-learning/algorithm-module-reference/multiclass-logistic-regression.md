---
title: 'Regressão Logística Multiclasse: Referência do Módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo de regressão logística multiclasse em Azure Machine Learning para criar um modelo de regressão logística que pode ser usado para prever múltiplos valores.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 40193a2547959b44c5753cfcc6ccad9344ab9486
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77920438"
---
# <a name="multiclass-logistic-regression-module"></a>Módulo de Regressão Logística Multiclasse

Este artigo descreve um módulo em Azure Machine Learning designer (pré-visualização).

Utilize este módulo para criar um modelo de regressão logística que pode ser usado para prever múltiplos valores.

A classificação utilizando a regressão logística é um método de aprendizagem supervisionado, pelo que requer um conjunto de dados rotulado. Treina o modelo fornecendo o modelo e o conjunto de dados rotulado como uma entrada para um módulo como [o Modelo de Comboio](./train-model.md). O modelo treinado pode então ser usado para prever valores para novos exemplos de entrada.

O Azure Machine Learning também fornece um módulo [de regressão logística de duas classes,](./two-class-logistic-regression.md) adequado para a classificação de variáveis binárias ou dichotos.

## <a name="about-multiclass-logistic-regression"></a>Sobre a regressão logística multiclasse

A regressão logística é um método bem conhecido nas estatísticas que é usado para prever a probabilidade de um resultado, e é popular para tarefas de classificação. O algoritmo prevê a probabilidade de ocorrência de um evento, adaptando dados a uma função logística. 

Em regressão logística multiclasse, o classificador pode ser usado para prever múltiplos resultados.

## <a name="configure-a-multiclass-logistic-regression"></a>Configure uma regressão logística multiclasse

1. Adicione o módulo **de regressão logística multiclasse** ao oleoduto.

2. Especifique como pretende que o modelo seja treinado, definindo a opção modo **Criar.**

    + **Parâmetro único**: Utilize esta opção se souber como pretende configurar o modelo e fornecer um conjunto específico de valores como argumentos.

    + **Gama de parâmetros**: Selecione esta opção se não tiver a certeza dos melhores parâmetros e pretenda fazer uma varredura de parâmetros. Selecione uma gama de valores para iterar mais, e o [Modelo tune Hyperparâmetros](tune-model-hyperparameters.md) iterates sobre todas as combinações possíveis das definições que forneceu para determinar os hiperparâmetros que produzem os melhores resultados.  

3. **Tolerância à otimização,** especifique o valor limiar para a convergência do optimizador. Se a melhoria entre iterações for inferior ao limiar, o algoritmo para e devolve o modelo atual.

4. **Peso de regularização L1**, **Peso de regularização L2**: Digite um valor a utilizar para os parâmetros de regularização L1 e L2. Recomenda-se um valor não-zero para ambos.

    A regularização é um método para prevenir o excesso de montagem penalizando modelos com valores de coeficiente extremos. A regularização funciona adicionando a penalidade associada aos valores de coeficiente ao erro da hipótese. Um modelo preciso com valores de coeficiente extremos seria penalizado mais, mas um modelo menos preciso com valores mais conservadores seria menos penalizado.

     A regularização L1 e L2 tem efeitos e usos diferentes. A L1 pode ser aplicada a modelos escassos, o que é útil quando se trabalha com dados de alta dimensão. Em contraste, a regularização L2 é preferível para dados que não são escassos.  Este algoritmo suporta uma combinação linear de valores de regularização L1 e L2: isto é, se `x = L1` e `y = L2`, `ax + by = c` define o período linear dos termos de regularização.

     Foram concebidas diferentes combinações lineares de termos L1 e L2 para modelos de regressão logística, como a [regularização elástica da rede.](https://wikipedia.org/wiki/Elastic_net_regularization)

6. **Semente**de número aleatório: Digite um valor inteiro para usar como semente para o algoritmo se quiser que os resultados sejam repetíveis ao longo de runs. Caso contrário, é utilizado um valor do relógio do sistema como semente, que pode produzir resultados ligeiramente diferentes em corridas do mesmo gasoduto.

8. Ligue um conjunto de dados rotulado e um dos módulos do comboio:

    + Se definir o modo Criar modo **de treinador** para um parâmetro **único,** utilize o módulo Modelo de [Comboio.](./train-model.md)

9. Executar o pipeline.



## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 