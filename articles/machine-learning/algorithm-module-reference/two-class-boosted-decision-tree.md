---
title: 'Árvore de decisões elevada de duas classes: Referência do módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como utilizar o módulo de árvore de decisões elevada de duas classes no serviço Azure Machine Learning para criar um modelo de machine learning que se baseia o algoritmo de árvores de decisões elevada.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 09ea530cac5bdbd62208f134177e5ceaccb545e2
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027949"
---
# <a name="two-class-boosted-decision-tree-module"></a>Módulo de árvore de decisões elevada de duas classes

Este artigo descreve um módulo da interface visual (pré-visualização) para o serviço Azure Machine Learning.

Utilize este módulo para criar um modelo de machine learning que se baseia o algoritmo de árvores de decisões elevada. 

Uma árvore de decisões elevada é um método em que a árvore de segundo corrige os erros da árvore primeiro, de aprendizagem de ensemble a árvore de terceiro corrige os erros de árvores de primeiros e segunda e assim por diante.  Previsões baseiam-se a toda ensemble das árvores em conjunto, que faz a predição.
  
Em geral, quando devidamente configurada, árvores de decisões elevada são os métodos mais fácil com o qual pretende obter um desempenho superior numa grande variedade de tarefas de machine learning. No entanto, também são um dos aprendizes a mais intensiva da memória e a implementação atual contém tudo o que, na memória. Por conseguinte, um modelo de árvore de decisões elevada poderá não conseguir processar grandes conjuntos de dados que podem processar alguns aprendizes lineares.

## <a name="how-to-configure"></a>Como configurar

Este módulo cria um modelo de classificação inoportuno. Uma vez que a classificação é um método de aprendizagem supervisionada, para preparar o modelo, precisa de um *etiquetados de conjunto de dados* que inclui uma coluna de etiqueta com um valor para todas as linhas.

Pode preparar este tipo de modelo usando [modelo de formação](././train-model.md). 

1.  No Azure Machine Learning, adicione a **árvore de decisões elevada** módulo à sua experimentação.
  
2.  Especifique como pretende que o modelo de formação, definindo a **modo de instrutor de criação** opção.
  
    + **Único parâmetro**: Se souber como pretende configurar o modelo, pode fornecer um conjunto específico de valores como argumentos.
  
  
3.  Para **número máximo de folhas por árvore**, indicar o número máximo de nós terminal (folhas) que podem ser criados em qualquer árvore.
  
     Ao aumentar este valor, potencialmente aumentar o tamanho da árvore e obtenha uma melhor precisão, sob overfitting e mais tempo de treinamento.
  
4.  Para **número mínimo de exemplos por nó folha**, indicar o número de cenários necessários para criar qualquer nó de terminal (folha) numa árvore.  
  
     Ao aumentar este valor, aumentar o limiar para a criação de novas regras. Por exemplo, com o valor predefinido de 1, até mesmo um único caso pode fazer com que uma nova regra a ser criada. Se aumentar o valor para 5, os dados de treinamento seriam têm de conter, pelo menos, cinco casos que cumprem as mesmas condições.
  
5.  Para **taxa de aprendizagem**, escreva um número entre 0 e 1 que define o tamanho de etapa ao aprendizado.  
  
     A taxa de aprendizagem determina rápido ou devagar o aprendiz converge sobre a solução ideal. Se o tamanho de etapa é demasiado grande, poderá overshoot a solução ideal. Se o tamanho de etapa for demasiado pequeno, treinamento leva mais tempo para convergir sobre a melhor solução.
  
6.  Para **número de árvores construído**, indicar o número total de árvores de decisão para criar o ensemble. Ao criar árvores de decisões mais, pode potencialmente obter uma melhor cobertura, mas irá aumentar o tempo de treinamento.
  
     Este valor também controla o número de árvores apresentado quando visualizar o modelo preparado. Se pretender ver ou uma única árvore de impressão, defina o valor como 1. No entanto, ao fazê-lo, apenas uma árvore é produzido (a árvore com o conjunto inicial de parâmetros) e são efetuadas sem mais iterações.
  
7.  Para **seed número aleatório**, opcionalmente, escreva um inteiro não negativo para utilizar como o valor de seed aleatório. Especificar uma semente garante a capacidade de reprodução nas execuções que têm os mesmos dados e parâmetros.  
  
     O seed aleatório é definido por predefinição como 0, o que significa que o valor semente inicial é obtido a partir do relógio do sistema.  Execuções de sucessivas, com um seed aleatório podem ter resultados diferentes.
  

9. Prepare o modelo.
  
    + Se definir **modo de instrutor de criação** para **único parâmetro**, ligar um conjunto de dados marcado e o [Train Model](./train-model.md) módulo.  
  
   
## <a name="results"></a>Resultados

Após a conclusão da preparação de modelos, clique com botão direito a saída de [Train Model](./train-model.md) para ver os resultados:

+ Para ver a árvore que foi criada em cada iteração, selecione **Visualize**. 
+ Para desagregar as divisões e ver as regras para cada nó, clique em cada árvore.


## <a name="next-steps"></a>Passos Seguintes

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço Azure Machine Learning. 