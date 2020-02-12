---
title: 'Regressão logística de duas classes: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo regressão logística de duas classes no Azure Machine Learning para criar um modelo de regressão logística que pode ser usado para prever dois (e apenas dois) resultados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 541d1001f8b5881f2773f795d7bd849704cbd796
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153694"
---
# <a name="two-class-logistic-regression-module"></a>Módulo de regressão logística de duas classes

Este artigo descreve um módulo em Azure Machine Learning designer (pré-visualização).

Use este módulo para criar um modelo de regressão logística que pode ser usado para prever dois (e apenas dois) resultados. 

A regressão logística é uma técnica estatística conhecida que é usada para modelar muitos tipos de problemas. Este algoritmo é um método de *aprendizagem supervisionado;*  por isso, deve fornecer um conjunto de dados que já contenha os resultados para treinar o modelo.  

### <a name="about-logistic-regression"></a>Sobre a regressão logística  

A regressão logística é um método bem conhecido em estatísticas que é usado para prever a probabilidade de um resultado e é especialmente popular para tarefas de classificação. O algoritmo prevê a probabilidade de ocorrência de um evento ajustando dados a uma função logística.
  
Neste módulo, o algoritmo de classificação é otimizado para dicotômicas ou variáveis binárias. se precisar de classificar vários resultados, utilize o módulo [de regressão logística multiclasse.](./multiclass-logistic-regression.md)

##  <a name="how-to-configure"></a>Como configurar  

Para treinar esse modelo, você deve fornecer um conjunto de um DataSet que contenha um rótulo ou uma coluna de classe. Como esse módulo destina-se a problemas de duas classes, o rótulo ou a coluna de classe deve conter exatamente dois valores. 

Por exemplo, a coluna de etiquetas pode ser [Votada] com possíveis valores de "Sim" ou "Não". Ou, pode ser [Risco de Crédito], com valores possíveis de "Alto" ou "Baixo". 
  
1.  Adicione o módulo **de regressão logística de duas classes** ao seu oleoduto.  
  
2.  Especifique como pretende que o modelo seja treinado, definindo a opção modo **Criar.**  
  
    -   **Parâmetro único**: Se souber como pretende configurar o modelo, pode fornecer um conjunto específico de valores como argumentos.  
  
3.  Para **a tolerância à otimização,** especifique um valor-limiar a utilizar ao otimizar o modelo. Se a melhoria entre as iterações cair abaixo do limite especificado, o algoritmo será considerado convergido em uma solução e o treinamento será interrompido.  
  
4.  Para **o peso da regularização L1** e o peso da **regularização L2,** escreva um valor a utilizar para os parâmetros de regularização L1 e L2. Um valor diferente de zero é recomendado para ambos.  
     *A regularização* é um método para prevenir o excesso de montagem penalizando modelos com valores de coeficiente extremos. A regularização funciona adicionando a penalidade associada aos valores de coeficiente ao erro da hipótese. Assim, um modelo preciso com valores de coeficiente extremo seria penalizado mais, mas um modelo menos preciso com valores mais conservadores seria penalizado menos.  
  
     A regularização L1 e L2 tem efeitos e usos diferentes.  
  
    -   L1 pode ser aplicado a modelos esparsos, o que é útil ao trabalhar com dados altamente dimensionais.  
  
    -   Por outro lado, a regularização L2 é preferível para dados que não são esparsos.  
  
     Este algoritmo suporta uma combinação linear de valores de regularização L1 e L2: isto é, se <code>x = L1</code> e <code>y = L2</code>, então <code>ax + by = c</code> define o período linear dos termos de regularização.  
  
    > [!NOTE]
    >  Quer saber mais sobre a regularização de L1 e L2? O seguinte artigo fornece uma discussão sobre como a regularização L1 e L2 são diferentes e como afetam a adaptação do modelo, com amostras de código para a regressão logística e modelos de rede neural: [L1 e L2 Regularização para Machine Learning](https://msdn.microsoft.com/magazine/dn904675.aspx)  
    >
    > Foram concebidas diferentes combinações lineares de termos L1 e L2 para modelos de regressão logística: por exemplo, [regularização elástica da rede.](https://wikipedia.org/wiki/Elastic_net_regularization) Sugerimos que você referencie essas combinações para definir uma combinação linear que seja efetiva em seu modelo.
      
5.  Para o **tamanho da memória para L-BFGS,** especifique a quantidade de memória a utilizar para a otimização *L-BFGS.*  
  
     L-BFGS significa "memória limitada Broyden-Fletcher-Goldfarb-Shanno". É um algoritmo de otimização que é popular para a estimativa de parâmetro. Esse parâmetro indica o número de posições passadas e gradientes a serem armazenados para o cálculo da próxima etapa.  
  
     Esse parâmetro de otimização limita a quantidade de memória usada para calcular a próxima etapa e direção. Quando você especifica menos memória, o treinamento é mais rápido, mas menos preciso.  
  
6.  Para **sementes de número aleatórios,** digite um valor inteiro. Definir um valor de semente é importante se você quiser que os resultados sejam reproduzidos em várias execuções do mesmo pipeline.  
  
  
8. Adicione um conjunto de dados marcado ao gasoduto e ligue um dos [módulos](module-reference.md)de treino .  
  
    -   Se definir o modo Criar modo **de treinador** para um parâmetro **único,** utilize o módulo Modelo de [Comboio.](./train-model.md)  
  
9. Executar o pipeline.  
  
## <a name="results"></a>Resultados

Após a conclusão do treinamento:
 
  
+ Para fazer previsões sobre novos dados, utilize o modelo treinado e novos dados como entrada para o módulo ['Modelo de Pontuação'.](./score-model.md) 


## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 