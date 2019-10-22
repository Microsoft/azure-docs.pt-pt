---
title: 'Regressão logística de duas classes: referência de módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como usar o módulo regressão logística de duas classes no serviço Azure Machine Learning para criar um modelo de regressão logística que pode ser usado para prever dois (e apenas dois) resultados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 7020bf529fa635d74959a9dac071aa6e2b134c5b
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72692614"
---
# <a name="two-class-logistic-regression-module"></a>Módulo de regressão logística de duas classes

Este artigo descreve um módulo da interface visual (visualização) para Azure Machine Learning serviço.

Use este módulo para criar um modelo de regressão logística que pode ser usado para prever dois (e apenas dois) resultados. 

A regressão logística é uma técnica estatística conhecida que é usada para modelar muitos tipos de problemas. Esse algoritmo é um método de *aprendizado supervisionado* ;  Portanto, você deve fornecer um conjunto de um DataSet que já contém os resultados para treinar o modelo.  

### <a name="about-logistic-regression"></a>Sobre a regressão logística  

A regressão logística é um método bem conhecido em estatísticas que é usado para prever a probabilidade de um resultado e é especialmente popular para tarefas de classificação. O algoritmo prevê a probabilidade de ocorrência de um evento ajustando dados a uma função logística.
  
Neste módulo, o algoritmo de classificação é otimizado para dicotômicas ou variáveis binárias. Se você precisar classificar vários resultados, use o módulo [regressão logística multiclasse](./multiclass-logistic-regression.md) .

##  <a name="how-to-configure"></a>Como configurar  

Para treinar esse modelo, você deve fornecer um conjunto de um DataSet que contenha um rótulo ou uma coluna de classe. Como esse módulo destina-se a problemas de duas classes, o rótulo ou a coluna de classe deve conter exatamente dois valores. 

Por exemplo, a coluna de rótulo pode ser [voto] com os valores possíveis de "Sim" ou "não". Ou, pode ser [risco de crédito], com os valores possíveis de "alto" ou "baixo". 
  
1.  Adicione o módulo **regressão logística de duas classes** ao seu pipeline.  
  
2.  Especifique como você deseja que o modelo seja treinado, definindo a opção **criar modo de instrutor** .  
  
    -   **Parâmetro único**: se você souber como deseja configurar o modelo, poderá fornecer um conjunto específico de valores como argumentos.  
  
3.  Para a **tolerância de otimização**, especifique um valor de limite a ser usado ao otimizar o modelo. Se a melhoria entre as iterações cair abaixo do limite especificado, o algoritmo será considerado convergido em uma solução e o treinamento será interrompido.  
  
4.  Para peso de **regularização L1** e **peso de regularização L2**, digite um valor a ser usado para os parâmetros de regularização L1 e L2. Um valor diferente de zero é recomendado para ambos.  
  
     A *regularização* é um método para evitar o superajuste por meio da penalização de modelos com valores de coeficiente extremo. A regularização funciona adicionando a penalidade associada aos valores de coeficiente ao erro da hipótese. Assim, um modelo preciso com valores de coeficiente extremo seria penalizado mais, mas um modelo menos preciso com valores mais conservadores seria penalizado menos.  
  
     A regularização L1 e L2 tem efeitos e usos diferentes.  
  
    -   L1 pode ser aplicado a modelos esparsos, o que é útil ao trabalhar com dados altamente dimensionais.  
  
    -   Por outro lado, a regularização L2 é preferível para dados que não são esparsos.  
  
     Esse algoritmo dá suporte a uma combinação linear de valores de regularização L1 e L2: ou seja, se <code>x = L1</code> e <code>y = L2</code>, <code>ax + by = c</code> definirá o intervalo linear dos termos de regularização.  
  
    > [!NOTE]
    >  Quer saber mais sobre a regularização de L1 e L2? O artigo a seguir fornece uma discussão de como a regularização L1 e L2 é diferente e como elas afetam o ajuste de modelo, com exemplos de código para a regressão logística e modelos de rede neural: a [regularização L1 e L2 para Machine Learning](https://msdn.microsoft.com/magazine/dn904675.aspx)  
    >
    > Combinações lineares diferentes de termos de L1 e L2 foram desenvolvedas para modelos de regressão logística: por exemplo, [regularização de rede elástica](https://wikipedia.org/wiki/Elastic_net_regularization). Sugerimos que você referencie essas combinações para definir uma combinação linear que seja efetiva em seu modelo.
      
5.  Para o **tamanho da memória para l-BFGS**, especifique a quantidade de memória a ser usada para a otimização de *l-BFGS* .  
  
     L-BFGS significa "memória limitada Broyden-Fletcher-Goldfarb-Shanno". É um algoritmo de otimização que é popular para a estimativa de parâmetro. Esse parâmetro indica o número de posições passadas e gradientes a serem armazenados para o cálculo da próxima etapa.  
  
     Esse parâmetro de otimização limita a quantidade de memória usada para calcular a próxima etapa e direção. Quando você especifica menos memória, o treinamento é mais rápido, mas menos preciso.  
  
6.  Para **semente de número aleatório**, digite um valor inteiro. Definir um valor de semente é importante se você quiser que os resultados sejam reproduzidos em várias execuções do mesmo pipeline.  
  
  
8. Adicione um conjunto de um DataSet marcado ao pipeline e conecte um dos [módulos de treinamento](module-reference.md).  
  
    -   Se você definir **criar modo de instrutor** como **um único parâmetro**, use o módulo [treinar modelo](./train-model.md) .  
  
9. Executar o pipeline.  
  
## <a name="results"></a>Resultados

Após a conclusão do treinamento:

+ Para ver um resumo dos parâmetros do modelo, junto com os pesos do recurso aprendidos do treinamento, clique com o botão direito do mouse na saída do [modelo](./train-model.md) de treinamento e selecione **Visualizar**.   
  
+ Para fazer previsões sobre novos dados, use o modelo treinado e novos dados como entrada para o módulo [modelo de Pontuação](./score-model.md) . 


## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning serviço. 