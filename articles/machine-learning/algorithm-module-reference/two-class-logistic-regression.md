---
title: 'Two-Class Regressão Logística: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo de regressão logística Two-Class em Azure Machine Learning para criar um classificador binário.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: 2e29a666f4d478e11986f834cff94d9743223f22
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96012651"
---
# <a name="two-class-logistic-regression-module"></a>Two-Class módulo de regressão logística

Este artigo descreve um módulo no designer de Aprendizagem automática Azure.

Utilize este módulo para criar um modelo de regressão logística que pode ser usado para prever dois (e apenas dois) resultados. 

A regressão logística é uma técnica estatística bem conhecida que é usada para modelar muitos tipos de problemas. Este algoritmo é um método *de aprendizagem supervisionado;*  portanto, deve fornecer um conjunto de dados que já contenha os resultados para treinar o modelo.  

### <a name="about-logistic-regression"></a>Sobre a regressão logística  

A regressão logística é um método bem conhecido nas estatísticas que é usado para prever a probabilidade de um resultado, e é especialmente popular para tarefas de classificação. O algoritmo prevê a probabilidade de ocorrência de um evento, adaptando dados a uma função logística.
  
Neste módulo, o algoritmo de classificação é otimizado para variáveis dicotomias ou binárias. se precisar de classificar vários resultados, utilize o módulo [de regressão logística multiclasse.](./multiclass-logistic-regression.md)

##  <a name="how-to-configure"></a>Como configurar  

Para treinar este modelo, tem de fornecer um conjunto de dados que contenha uma etiqueta ou coluna de classe. Uma vez que este módulo se destina a problemas de duas classes, o rótulo ou coluna de classe deve conter exatamente dois valores. 

Por exemplo, a coluna do rótulo pode ser [Votada] com possíveis valores de "Sim" ou "Não". Ou, pode ser [Risco de Crédito], com possíveis valores de "Alto" ou "Baixo". 
  
1.  Adicione o módulo **de regressão logística de duas classes** ao seu oleoduto.  
  
2.  Especifique como pretende que o modelo seja treinado, definindo a opção **modo de formação Create.**  
  
    -   **Parâmetro único**: Se souber como pretende configurar o modelo, pode fornecer um conjunto específico de valores como argumentos.  

    -   **Intervalo de parâmetros**: Se não tiver a certeza dos melhores parâmetros, pode encontrar os parâmetros ideais utilizando o módulo [De Hiperparametros Tune Model.](tune-model-hyperparameters.md) Fornece alguma gama de valores, e o treinador itera sobre várias combinações das definições para determinar a combinação de valores que produz o melhor resultado.
  
3.  Para **a tolerância à otimização,** especifique um valor-limiar a utilizar ao otimizar o modelo. Se a melhoria entre iterações ficar abaixo do limiar especificado, considera-se que o algoritmo convergiu numa solução e o treino para.  
  
4.  Para **o peso de regularização L1** e o peso de **regularização L2,** escreva um valor a utilizar para os parâmetros de regularização L1 e L2. Recomenda-se um valor não zero para ambos.  
     *A regularização* é um método para prevenir a sobremontagem, penalizando modelos com valores de coeficiente extremos. A regularização funciona adicionando a penalidade que está associada aos valores do coeficiente ao erro da hipótese. Assim, um modelo preciso com valores de coeficiente extremo seria penalizado mais, mas um modelo menos preciso com valores mais conservadores seria menos penalizado.  
  
     A regularização L1 e L2 tem efeitos e utilizações diferentes.  
  
    -   L1 pode ser aplicado em modelos escassos, o que é útil quando se trabalha com dados de alta dimensão.  
  
    -   Em contrapartida, a regularização L2 é preferível para dados que não são escassos.  
  
     Este algoritmo suporta uma combinação linear de valores de regularização L1 e L2: isto é, se <code>x = L1</code> e <code>y = L2</code> , em seguida, define o período <code>ax + by = c</code> linear dos termos de regularização.  
  
    > [!NOTE]
    >  Quer saber mais sobre a regularização L1 e L2? O seguinte artigo fornece uma discussão sobre como a regularização L1 e L2 são diferentes e como afetam a montagem do modelo, com amostras de código para regressão logística e modelos de rede neural:  [Regularização L1 e L2 para Machine Learning](/archive/msdn-magazine/2015/february/test-run-l1-and-l2-regularization-for-machine-learning)  
    >
    > Foram concebidas diferentes combinações lineares de termos L1 e L2 para modelos de regressão logística: por exemplo, [regularização líquida elástica](https://wikipedia.org/wiki/Elastic_net_regularization). Sugerimos que faça referência a estas combinações para definir uma combinação linear que seja eficaz no seu modelo.
      
5.  Para **o tamanho da memória para L-BFGS,** especifique a quantidade de memória a utilizar para a otimização *L-BFGS.*  
  
     L-BFGS significa "memória limitada Broyden-Fletcher-Goldfarb-Shanno". É um algoritmo de otimização que é popular para a estimativa de parâmetros. Este parâmetro indica o número de posições e gradientes passados para armazenar para a computação do passo seguinte.  
  
     Este parâmetro de otimização limita a quantidade de memória que é usada para calcular o próximo passo e direção. Quando se especifica menos memória, o treino é mais rápido, mas menos preciso.  
  
6.  Para **sementes de número aleatório,** digite um valor inteiro. A definição de um valor de sementes é importante se pretender que os resultados sejam reprodutíveis ao longo de várias corridas do mesmo oleoduto.  
  
  
8. Adicione um conjunto de dados rotulado ao oleoduto e treine o modelo:

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
  
## <a name="results"></a>Resultados

Após o treino estar completo:
 
  
+ Para fazer previsões sobre novos dados, utilize o modelo treinado e novos dados como entrada para o módulo ['Modelo de Pontuação'.](./score-model.md) 


## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning.