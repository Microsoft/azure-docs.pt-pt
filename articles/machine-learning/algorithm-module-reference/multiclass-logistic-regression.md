---
title: 'Regressão Logística Multiclass: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo de regressão logística multiclasse no designer Azure Machine Learning para prever múltiplos valores.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: e2bbc28735bcbfd952c4941453956acd0568ea67
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "93420738"
---
# <a name="multiclass-logistic-regression-module"></a>Módulo de regressão logística multiclasse

Este artigo descreve um módulo no designer de Aprendizagem automática Azure.

Utilize este módulo para criar um modelo de regressão logística que pode ser usado para prever vários valores.

A classificação utilizando a regressão logística é um método de aprendizagem supervisionado, pelo que requer um conjunto de dados rotulado. Treina o modelo fornecendo o modelo e o conjunto de dados rotulados como uma entrada para um módulo como [o Train Model.](./train-model.md) O modelo treinado pode então ser usado para prever valores para novos exemplos de entrada.

A Azure Machine Learning também fornece um módulo [de regressão logística de duas classes,](./two-class-logistic-regression.md) que é adequado para a classificação de variáveis binárias ou dicotomias.

## <a name="about-multiclass-logistic-regression"></a>Sobre a regressão logística multiclasse

A regressão logística é um método bem conhecido nas estatísticas que é usado para prever a probabilidade de um resultado, e é popular para tarefas de classificação. O algoritmo prevê a probabilidade de ocorrência de um evento, adaptando dados a uma função logística. 

Na regressão logística multiclasse, o classificador pode ser usado para prever múltiplos resultados.

## <a name="configure-a-multiclass-logistic-regression"></a>Configurar uma regressão logística multiclasse

1. Adicione o módulo **de regressão logística multiclasse** ao oleoduto.

2. Especifique como pretende que o modelo seja treinado, definindo a opção **modo de formação Create.**

    + **Parâmetro único**: Utilize esta opção se souber como pretende configurar o modelo e fornecer um conjunto específico de valores como argumentos.

    + **Intervalo de parâmetros**: Selecione esta opção se não tiver a certeza dos melhores parâmetros e pretender fazer uma varredura de parâmetros. Selecione uma gama de valores para iterar e o [Tune Model Hyperparameters](tune-model-hyperparameters.md) itera sobre todas as combinações possíveis das configurações fornecidas para determinar os hiperparmetros que produzem os melhores resultados.  

3. **Tolerância à otimização,** especifique o valor limiar para a convergência optimizador. Se a melhoria entre iterações for inferior ao limiar, o algoritmo para e devolve o modelo atual.

4. **Peso de regularização L1**, **peso de regularização L2**: Digite um valor a utilizar para os parâmetros de regularização L1 e L2. Recomenda-se um valor não zero para ambos.

    A regularização é um método para prevenir a sobremontagem, penalizando modelos com valores de coeficiente extremos. A regularização funciona adicionando a penalidade que está associada aos valores do coeficiente ao erro da hipótese. Um modelo preciso com valores de coeficiente extremo seria penalizado mais, mas um modelo menos preciso com valores mais conservadores seria menos penalizado.

     A regularização L1 e L2 tem efeitos e utilizações diferentes. L1 pode ser aplicado em modelos escassos, o que é útil quando se trabalha com dados de alta dimensão. Em contrapartida, a regularização L2 é preferível para dados que não são escassos.  Este algoritmo suporta uma combinação linear de valores de regularização L1 e L2: isto é, se `x = L1` e , define o período linear dos termos de `y = L2` `ax + by = c` regularização.

     Diferentes combinações lineares de termos L1 e L2 foram concebidas para modelos de regressão logística, tais como [regularização líquida elástica.](https://wikipedia.org/wiki/Elastic_net_regularization)

6. **Semente de número aleatório**: Digite um valor inteiro para usar como semente para o algoritmo se quiser que os resultados sejam repetíveis ao longo de percursos. Caso contrário, um valor do relógio do sistema é usado como semente, que pode produzir resultados ligeiramente diferentes em corridas do mesmo oleoduto.

8. Ligue um conjunto de dados rotulado e treine o modelo:

    + Se definir **Criar o modo de treinador** para single **parâmetro,** conecte um conjunto de dados marcado e o módulo Modelo de [Comboio.](train-model.md)  
  
    + Se definir **Criar modo de treinador** para intervalo de **parâmetros,** ligue um conjunto de dados marcado e treine o modelo utilizando [hiperparmetros do modelo de sintonização](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Se passar uma gama de parâmetros para [o Modelo de Comboio,](train-model.md)utiliza apenas o valor predefinido na lista de parâmetros únicos.  
    > 
    > Se passar um único conjunto de valores de parâmetros para o módulo [Tune Model Hyperparameters,](tune-model-hyperparameters.md) quando espera uma gama de definições para cada parâmetro, ignora os valores e utiliza os valores predefinidos para o aluno.  
    > 
    > Se selecionar a opção De Alcance de **Parâmetros** e introduzir um único valor para qualquer parâmetro, esse valor único especificado é utilizado ao longo da varredura, mesmo que outros parâmetros se alterem numa gama de valores.

9. Envie o oleoduto.



## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 