---
title: 'Regressão logística multiclasse: Referência de módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como usar o módulo regressão logística multiclasse no serviço Azure Machine Learning para criar um modelo de regressão logística que pode ser usado para prever vários valores.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: d51bc48944204b4c7c50790949927849869f26fc
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128640"
---
# <a name="multiclass-logistic-regression-module"></a>Módulo de regressão logística multiclasse

Este artigo descreve um módulo da interface visual (visualização) para Azure Machine Learning serviço.

Use este módulo para criar um modelo de regressão logística que pode ser usado para prever vários valores.

A classificação usando a regressão logística é um método de aprendizado supervisionado e, portanto, requer um conjunto de informações rotulado. Você treina o modelo fornecendo o modelo e o conjunto de dados rotulado como uma entrada para um módulo, como o [modelo de treinamento](./train-model.md). O modelo treinado pode então ser usado para prever valores para novos exemplos de entrada.

Azure Machine Learning também fornece um módulo de [regressão logística de duas classes](./two-class-logistic-regression.md) , que é adequado para classificação de variáveis binárias ou dicotômicass.

## <a name="about-multiclass-logistic-regression"></a>Sobre a regressão logística multiclasse

A regressão logística é um método bem conhecido em estatísticas que é usado para prever a probabilidade de um resultado e é popular para tarefas de classificação. O algoritmo prevê a probabilidade de ocorrência de um evento ajustando dados a uma função logística. 

Em regressão logística multiclasse, o classificador pode ser usado para prever vários resultados.

## <a name="configure-a-multiclass-logistic-regression"></a>Configurar uma regressão logística multiclasse

1. Adicione o módulo **regressão logística multiclasse** ao experimento.

2. Especifique como você deseja que o modelo seja treinado, definindo a opção **criar modo de instrutor** .

    + **Parâmetro único**: Use esta opção se você souber como deseja configurar o modelo e fornecer um conjunto específico de valores como argumentos.

    + **Intervalo de parâmetros**: Use esta opção se você não tiver certeza dos melhores parâmetros e quiser usar uma varredura de parâmetro.

3. **Tolerância de otimização**, especifique o valor de limite para a convergência do otimizador. Se a melhoria entre as iterações for menor que o limite, o algoritmo parará e retornará o modelo atual.

4. **Peso**de regularização de L1, **peso de regularização L2**: Digite um valor a ser usado para os parâmetros de regularização L1 e L2. Um valor diferente de zero é recomendado para ambos.

    A regularização é um método para evitar o superajuste por meio da penalização de modelos com valores de coeficiente extremo. A regularização funciona adicionando a penalidade associada aos valores de coeficiente ao erro da hipótese. Um modelo preciso com valores de coeficiente extremo seria penalizado mais, mas um modelo menos preciso com valores mais conservadores seria penalizado menos.

     A regularização L1 e L2 tem efeitos e usos diferentes. L1 pode ser aplicado a modelos esparsos, o que é útil ao trabalhar com dados altamente dimensionais. Por outro lado, a regularização L2 é preferível para dados que não são esparsos.  Este algoritmo dá suporte a uma combinação linear de valores de regularização L1 e L2: ou `x = L1` seja `y = L2`, `ax + by = c` se e, define o intervalo linear dos termos de regularização.

     Combinações lineares diferentes de termos de L1 e L2 foram desenvolvedas para modelos de regressão logística, como regularização de [rede elástica](https://wikipedia.org/wiki/Elastic_net_regularization).

6. **Semente de número aleatório**: Digite um valor inteiro para usar como a semente do algoritmo se você quiser que os resultados sejam repetidos em execuções. Caso contrário, um valor de relógio do sistema será usado como semente, o que pode produzir resultados ligeiramente diferentes em execuções do mesmo experimento.

8. Conecte um DataSet rotulado e um dos módulos de treinamento:

    + Se você definir **criar modo de instrutor** como **um único parâmetro**, use o módulo [treinar modelo](./train-model.md) .

9. Execute a experimentação.

## <a name="results"></a>Resultados

Após a conclusão do treinamento, você pode ver um resumo dos parâmetros do modelo, junto com os pesos do recurso aprendidos do treinamento, clique com o botão direito do mouse na saída do módulo [modelo](./train-model.md) de treinamento e selecione **Visualizar**.


## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning serviço. 