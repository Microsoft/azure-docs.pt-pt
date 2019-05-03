---
title: 'Regressão logística várias classes: Referência do módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como utilizar o módulo de várias classes de regressão logística no serviço Azure Machine Learning para criar um modelo de regressão logística, que pode ser utilizado para prever vários valores.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ac4310e851808d6e6d89d1a2b506975eea3b1d69
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029329"
---
# <a name="multiclass-logistic-regression-module"></a>Módulo de regressão logística várias classes

Este artigo descreve um módulo da interface visual (pré-visualização) para o serviço Azure Machine Learning.

Utilize este módulo para criar um modelo de regressão logística, que pode ser utilizado para prever vários valores.

Utilizar regressão logística de classificação é um método de aprendizagem supervisionada e, portanto, requer um conjunto de dados etiquetado. Preparar o modelo, fornecendo, tais como o modelo e o conjunto de dados etiquetado como entrada para um módulo [modelo de formação](./train-model.md). O modelo preparado, em seguida, pode ser utilizado para prever valores para obter exemplos de entrada novo.

O Azure Machine Learning também fornece um [regressão logística de duas classes](./two-class-logistic-regression.md) módulo, que é adequado para a classificação do binárias ou dichotomous variáveis.

## <a name="about-multiclass-logistic-regression"></a>Sobre várias classes regressão logística

Regressão logística é um método bem conhecido estatística que é utilizado para prever a probabilidade de um resultado e é popular para tarefas de classificação. O algoritmo prevê a probabilidade de ocorrência de um evento, pelo que se ajusta a dados para uma função de logística. 

Em várias classes regressão logística, o classificador pode ser utilizado para prever resultados de várias.

## <a name="configure-a-multiclass-logistic-regression"></a>Configurar um regressão logística várias classes

1. Adicionar a **regressão logística de várias classes** módulo para a experimentação.

2. Especifique como pretende que o modelo de formação, definindo a **modo de instrutor de criação** opção.

    + **Único parâmetro**: Utilize esta opção se souber como pretende configurar o modelo e forneça um conjunto específico de valores como argumentos.

    + **Intervalo de parâmetro**: Utilize esta opção se não estiver certo dos melhores parâmetros e pretende utilizar varrimentos.

3. **Tolerância de otimização**, especifique o valor de limiar para a convergência de otimizador. Se a melhoria entre as iterações for inferior ao limiar, o algoritmo interrompe e devolve o modelo atual.

4. **Peso de regularização de L1**, **peso de regularização de L2**: Escreva um valor a utilizar para os parâmetros de regularização L1 e L2. Para ambos, recomenda-se um valor diferente de zero.

    Regularização é um método para impedir a overfitting por modelos penalizing com valores de coeficiente extremo. Regularização funciona adicionando a penalidade que estão associada com valores de coeficiente para o erro da hipótese. Um modelo preciso com valores de coeficiente extreme poderia ser penalizado mais, mas um modelo de menos preciso com valores mais conservadoras poderia ser penalizado menos.

     L1 e L2 regularização ter efeitos diferentes e usa. L1 podem ser aplicadas a modelos dispersos, que é útil ao trabalhar com dados altamente dimensionais. Por outro lado, os erros de L2 regularização é preferível para dados que não estão dispersos.  Esse algoritmo suporta uma combinação de linear de valores de regularização de L1 e L2: ou seja, se `x = L1` e `y = L2`, `ax + by = c` define o intervalo linear dos termos de regularização.

     Diferentes combinações de linear dos termos de L1 e L2 ser pensadas para modelos de regressão logística, tal como [elástica regularização net](https://wikipedia.org/wiki/Elastic_net_regularization).

6. **Propagação de números aleatórias**: Escreva um valor inteiro para utilizar como a semente para o algoritmo, se pretender que os resultados para poder ser repetidos ao longo de execuções. Caso contrário, um valor de relógio do sistema é utilizado como a semente, o que pode produzir resultados ligeiramente diferentes nas execuções da experimentação mesmo.

8. Ligue-se de um conjunto de dados com nome e um dos módulos de treinamento:

    + Se definir **modo de instrutor de criação** para **único parâmetro**, utilize o [Train Model](./train-model.md) módulo.

9. Execute a experimentação.

## <a name="results"></a>Resultados

Após a conclusão do treinamento, pode ver um resumo dos parâmetros do modelo, juntamente com os pesos de funcionalidade aprendidos treinamento, com o botão direito a saída do [Train Model](./train-model.md) módulo e selecione **Visualize**.


## <a name="next-steps"></a>Passos Seguintes

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço Azure Machine Learning. 