---
title: 'Regressão logística de duas classes: Referência do módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como utilizar o módulo de duas classes de regressão logística no serviço Azure Machine Learning para criar um modelo de regressão logística, que pode ser utilizado para prever resultados de dois (e somente dois).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: aacaf6c64ef77d0e694f97e3675060eca33794ed
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65029254"
---
# <a name="two-class-logistic-regression-module"></a>Módulo de regressão logística de duas classes

Este artigo descreve um módulo da interface visual (pré-visualização) para o serviço Azure Machine Learning.

Utilize este módulo para criar um modelo de regressão logística que pode ser utilizado para prever resultados de dois (e somente dois). 

Regressão logística é uma técnica de estatística bem conhecida que é utilizada para muitos tipos de problemas de modelagem. Esse algoritmo é uma *aprendizagem supervisionada* método;  Por conseguinte, tem de fornecer um conjunto de dados já contém os resultados para preparar o modelo.  

### <a name="about-logistic-regression"></a>Sobre regressão logística  

Regressão logística é um método bem conhecido estatística que é utilizado para prever a probabilidade de um resultado e é especialmente populares para tarefas de classificação. O algoritmo prevê a probabilidade de ocorrência de um evento, pelo que se ajusta a dados para uma função de logística.
  
Neste módulo, o algoritmo de classificação é otimizado para variáveis dichotomous ou binárias. Se precisar de classificar os resultados de várias, utilize o [regressão logística de várias classes](./multiclass-logistic-regression.md) módulo.

##  <a name="how-to-configure"></a>Como configurar  

Para preparar este modelo, tem de fornecer um conjunto de dados que contém uma coluna de etiqueta ou a classe. Uma vez que este módulo destina-se a problemas de classe dois, a coluna de etiqueta ou a classe tem de conter exatamente dois valores. 

Por exemplo, a etiqueta coluna poderá ser [votado] com valores possíveis dos "Sim" ou "Não". Em alternativa, poderá ser [risco de crédito,] com os valores possíveis de "Alta" ou "Baixa". 
  
1.  Adicionar a **regressão logística de duas classes** módulo à sua experimentação.  
  
2.  Especifique como pretende que o modelo de formação, definindo a **modo de instrutor de criação** opção.  
  
    -   **Único parâmetro**: Se souber como pretende configurar o modelo, pode fornecer um conjunto específico de valores como argumentos.  
  
3.  Para **tolerância de otimização**, especifique um valor de limiar para utilizar ao otimizar o modelo. Se a melhoria entre as iterações cai abaixo do limiar especificado, o algoritmo é considerado como tenham convergido numa solução e deixa de treinamento.  
  
4.  Para **peso de regularização de L1** e **peso de regularização de L2**, escreva um valor a utilizar para os parâmetros de regularização L1 e L2. Para ambos, recomenda-se um valor diferente de zero.  
  
     *Regularização* é um método para impedir overfitting por penalizing modelos com valores de coeficiente extremo. Regularização funciona adicionando a penalidade que estão associada com valores de coeficiente para o erro da hipótese. Portanto, um modelo preciso com valores de coeficiente extreme poderia ser penalizado mais, mas um modelo de menos preciso com valores mais conservadoras poderia ser penalizado menos.  
  
     L1 e L2 regularização ter efeitos diferentes e usa.  
  
    -   L1 podem ser aplicadas a modelos dispersos, que é útil ao trabalhar com dados altamente dimensionais.  
  
    -   Por outro lado, os erros de L2 regularização é preferível para dados que não estão dispersos.  
  
     Esse algoritmo suporta uma combinação de linear de valores de regularização de L1 e L2: ou seja, se <code>x = L1</code> e <code>y = L2</code>, em seguida, <code>ax + by = c</code> define o intervalo linear dos termos de regularização.  
  
    > [!NOTE]
    >  Quer saber mais sobre regularização L1 e L2? O artigo seguinte disponibiliza uma descrição de como regularização L1 e L2 são diferentes e como elas afetam o ajuste de modelo, com exemplos de código para regressão logística e modelos de rede neural:  [L1 e L2 regularização para Machine Learning](https://msdn.microsoft.com/magazine/dn904675.aspx)  
    >
    > Diferentes combinações de linear dos termos de L1 e L2 ser pensadas para modelos de regressão logística: por exemplo, [elástica regularização net](https://wikipedia.org/wiki/Elastic_net_regularization). Sugerimos que faz referência essas combinações para definir uma combinação de linear é eficaz no seu modelo.
      
5.  Para **tamanho de memória para L-BFGS**, especifique a quantidade de memória a utilizar para *L BFGS* otimização.  
  
     L BFGS significa "memória limitada Broyden-Fletcher-Goldfarb-Shanno". É um algoritmo de otimização que é popular para a estimativa de parâmetro. Esse parâmetro indica o número de posições anteriores e gradientes para armazenar de computação do passo seguinte.  
  
     Este parâmetro de otimização limita a quantidade de memória que é usada para computar o próximo passo e direção. Quando especificar menos memória, o treinamento é mais rápido, mas menos precisas.  
  
6.  Para **seed número aleatório**, escreva um valor inteiro. Definindo um valor de semente é importante se pretender que os resultados estejam reproduzível ao longo de várias execuções da experimentação mesmo.  
  
  
8. Adicionar um conjunto de dados marcado para a experimentação e ligue-se de um da [módulos de treinamento](module-reference.md).  
  
    -   Se definir **modo de instrutor de criação** para **único parâmetro**, utilize o [Train Model](./train-model.md) módulo.  
  
9. Execute a experimentação.  
  
## <a name="results"></a>Resultados

Após a conclusão do treinamento:

+ Para ver um resumo dos parâmetros do modelo, juntamente com os pesos de funcionalidade aprendidos treinamento, com o botão direito a saída de [Train Model](./train-model.md) e selecione **Visualize**.   
  
+ Para fazer previsões sobre novos dados, utilize o modelo preparado e os novos dados como entrada para o [modelo de pontuação](./score-model.md) módulo. 


## <a name="next-steps"></a>Passos Seguintes

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço Azure Machine Learning. 