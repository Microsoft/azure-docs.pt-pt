---
title: 'Regressão logística de duas classes: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo de regressão logística de duas classes em Azure Machine Learning para criar um modelo de regressão logística que pode ser usado para prever dois (e apenas dois) resultados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 7eb1ad00c3c947c3ed6d4ca450bddc0956a08d71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455830"
---
# <a name="two-class-logistic-regression-module"></a>Módulo de regressão logística de duas classes

Este artigo descreve um módulo em Azure Machine Learning designer (pré-visualização).

Utilize este módulo para criar um modelo de regressão logística que pode ser usado para prever dois (e apenas dois) resultados. 

A regressão logística é uma técnica estatística bem conhecida que é usada para modelar muitos tipos de problemas. Este algoritmo é um método de *aprendizagem supervisionado;*  por isso, deve fornecer um conjunto de dados que já contenha os resultados para treinar o modelo.  

### <a name="about-logistic-regression"></a>Sobre a regressão logística  

A regressão logística é um método bem conhecido nas estatísticas que é usado para prever a probabilidade de um resultado, e é especialmente popular para tarefas de classificação. O algoritmo prevê a probabilidade de ocorrência de um evento, adaptando dados a uma função logística.
  
Neste módulo, o algoritmo de classificação é otimizado para dicotomias ou variáveis binárias. se precisar de classificar vários resultados, utilize o módulo [de regressão logística multiclasse.](./multiclass-logistic-regression.md)

##  <a name="how-to-configure"></a>Como configurar  

Para treinar este modelo, deve fornecer um conjunto de dados que contenha uma etiqueta ou coluna de classe. Como este módulo se destina a problemas de duas classes, a etiqueta ou coluna de classe deve conter exatamente dois valores. 

Por exemplo, a coluna de etiquetas pode ser [Votada] com possíveis valores de "Sim" ou "Não". Ou, pode ser [Risco de Crédito], com valores possíveis de "Alto" ou "Baixo". 
  
1.  Adicione o módulo **de regressão logística de duas classes** ao seu oleoduto.  
  
2.  Especifique como pretende que o modelo seja treinado, definindo a opção modo **Criar.**  
  
    -   **Parâmetro único**: Se souber como pretende configurar o modelo, pode fornecer um conjunto específico de valores como argumentos.  

    -   **Gama de parâmetros**: Se não tiver a certeza dos melhores parâmetros, pode encontrar os parâmetros ideais utilizando o módulo de [hiperparâmetros](tune-model-hyperparameters.md) do Modelo tune. Você fornece alguma gama de valores, e o treinador iterates sobre múltiplas combinações das configurações para determinar a combinação de valores que produz o melhor resultado.
  
3.  Para **a tolerância à otimização,** especifique um valor-limiar a utilizar ao otimizar o modelo. Se a melhoria entre iterações ficar abaixo do limiar especificado, o algoritmo é considerado como tendo convergido para uma solução, e o treino para.  
  
4.  Para **o peso da regularização L1** e o peso da **regularização L2,** escreva um valor a utilizar para os parâmetros de regularização L1 e L2. Recomenda-se um valor não-zero para ambos.  
     *A regularização* é um método para prevenir o excesso de montagem penalizando modelos com valores de coeficiente extremos. A regularização funciona adicionando a penalidade associada aos valores de coeficiente ao erro da hipótese. Assim, um modelo preciso com valores de coeficiente extremos seria penalizado mais, mas um modelo menos preciso com valores mais conservadores seria menos penalizado.  
  
     A regularização L1 e L2 tem efeitos e usos diferentes.  
  
    -   A L1 pode ser aplicada a modelos escassos, o que é útil quando se trabalha com dados de alta dimensão.  
  
    -   Em contraste, a regularização L2 é preferível para dados que não são escassos.  
  
     Este algoritmo suporta uma combinação linear de valores de regularização L1 e L2: isto é, se <code>x = L1</code> e, <code>y = L2</code>em seguida, <code>ax + by = c</code> define o período linear dos termos de regularização.  
  
    > [!NOTE]
    >  Quer saber mais sobre a regularização l1 e L2? O seguinte artigo fornece uma discussão sobre como a regularização L1 e L2 são diferentes e como afetam a adaptação do modelo, com amostras de código para a regressão logística e modelos de rede neural: [L1 e L2 Regularização para Machine Learning](https://msdn.microsoft.com/magazine/dn904675.aspx)  
    >
    > Foram concebidas diferentes combinações lineares de termos L1 e L2 para modelos de regressão logística: por exemplo, [regularização elástica da rede.](https://wikipedia.org/wiki/Elastic_net_regularization) Sugerimos que refira estas combinações para definir uma combinação linear que seja eficaz no seu modelo.
      
5.  Para o **tamanho da memória para L-BFGS,** especifique a quantidade de memória a utilizar para a otimização *L-BFGS.*  
  
     L-BFGS significa "memória limitada Broyden-Fletcher-Goldfarb-Shanno". É um algoritmo de otimização que é popular para a estimativa de parâmetros. Este parâmetro indica o número de posições e gradientes anteriores para armazenar para a computação do próximo passo.  
  
     Este parâmetro de otimização limita a quantidade de memória que é usada para calcular o próximo passo e direção. Quando se especifica menos memória, o treino é mais rápido, mas menos preciso.  
  
6.  Para **sementes de número aleatórios,** digite um valor inteiro. Definir um valor de semente é importante se quiser que os resultados sejam reprodutíveis ao longo de várias corridas do mesmo oleoduto.  
  
  
8. Adicione um conjunto de dados marcado ao gasoduto e ligue um dos [módulos](module-reference.md)de treino .  
  
    -   Se definir o modo Criar modo **de treinador** para um parâmetro **único,** utilize o módulo Modelo de [Comboio.](./train-model.md)  
  
9. Submeta o oleoduto.  
  
## <a name="results"></a>Resultados

Após o treino estar completo:
 
  
+ Para fazer previsões sobre novos dados, utilize o modelo treinado e novos dados como entrada para o módulo ['Modelo de Pontuação'.](./score-model.md) 


## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 