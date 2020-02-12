---
title: 'Árvore de decisão aumentada de duas classes: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo árvore de decisão aumentada de duas classes no Azure Machine Learning para criar um modelo de aprendizado de máquina baseado no algoritmo árvores de decisão aumentada.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 985417200737e00ddbda4194cf7596b11c74ac10
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153728"
---
# <a name="two-class-boosted-decision-tree-module"></a>Módulo de árvore de decisão aumentada de duas classes

Este artigo descreve um módulo em Azure Machine Learning designer (pré-visualização).

Use este módulo para criar um modelo de aprendizado de máquina baseado no algoritmo árvores de decisão aumentada. 

Uma árvore de decisão aumentada é um método de aprendizado Ensemble no qual a segunda árvore corrige os erros da primeira árvore, a terceira árvore corrige os erros da primeira e segunda árvores e assim por diante.  As previsões são baseadas em toda a Ensemble de árvores juntas que faz a previsão.
  
Geralmente, quando configuradas corretamente, as árvores de decisão aumentadas são os métodos mais fáceis com os quais obter o melhor desempenho em uma ampla variedade de tarefas de aprendizado de máquina. No entanto, eles também são um dos mais aprendizes com uso intensivo de memória e a implementação atual contém tudo na memória. Portanto, um modelo de árvore de decisão impulsionado pode não ser capaz de processar os grandes conjuntos de altos que alguns aprendizes lineares podem manipular.

## <a name="how-to-configure"></a>Como configurar

Este módulo cria um modelo de classificação não treinado. Como a classificação é um método de aprendizagem supervisionado, para treinar o modelo, você precisa de um conjunto de *dados marcado* que inclua uma coluna de etiqueta com um valor para todas as linhas.

Pode treinar este tipo de modelo utilizando [o Modelo de Comboio](././train-model.md). 

1.  No Azure Machine Learning, adicione o módulo **Boosted Decision Tree** ao seu pipeline.
  
2.  Especifique como pretende que o modelo seja treinado, definindo a opção modo **Criar.**
  
    + **Parâmetro único**: Se souber como pretende configurar o modelo, pode fornecer um conjunto específico de valores como argumentos.
  
  
3.  Para o **número máximo de folhas por árvore,** indique o número máximo de nódos os terminais (folhas) que podem ser criados em qualquer árvore.
  
     Ao aumentar esse valor, você pode aumentar o tamanho da árvore e obter uma precisão melhor, com o risco de superajuste e tempo de treinamento mais longo.
  
4.  Para o **número mínimo de amostras por nó**de folha, indique o número de caixas necessárias para criar qualquer nó terminal (folha) numa árvore.  
  
     Ao aumentar esse valor, você aumenta o limite para a criação de novas regras. Por exemplo, com o valor padrão de 1, mesmo um único caso pode fazer com que uma nova regra seja criada. Se você aumentar o valor para 5, os dados de treinamento precisarão conter pelo menos cinco casos que atendam às mesmas condições.
  
5.  Para **a taxa de aprendizagem**, digite um número entre 0 e 1 que defina o tamanho do passo durante a aprendizagem.  
  
     A taxa de aprendizagem determina o quão rápido ou lento o aprendiz convergi na solução ideal. Se o tamanho do passo for demasiado grande, poderá ultrapassar a solução ideal. Se o tamanho da etapa for muito pequeno, o treinamento levará mais tempo para convergir na melhor solução.
  
6.  Para **o número de árvores construídas,** indicar o número total de árvores de decisão a criar no conjunto. Ao criar mais árvores de decisão, você pode potencialmente obter uma cobertura melhor, mas o tempo de treinamento aumentará.
  
     Esse valor também controla o número de árvores exibidas ao visualizar o modelo treinado. Se você quiser ver ou imprimir uma única árvore, defina o valor como 1. No entanto, quando você faz isso, apenas uma árvore é produzida (a árvore com o conjunto inicial de parâmetros) e nenhuma iteração adicional é executada.
  
7.  Para **sementes aleatórias,** digite opcionalmente um inteiro não negativo para usar como o valor de sementes aleatórias. A especificação de uma semente garante reprodução entre as execuções que têm os mesmos dados e parâmetros.  
  
     A semente aleatória é definida por padrão como 0, o que significa que o valor de semente inicial é obtido do relógio do sistema.  As execuções sucessivas usando uma semente aleatória podem ter resultados diferentes.
  

9. Treine o modelo.
  
    + Se definir o modo Criar o **modo de formação** para **um parâmetro único,** ligue um conjunto de dados marcado e o módulo [Modelo de Comboio.](./train-model.md)  
   
## <a name="results"></a>Resultados

Após a conclusão do treinamento:

+ Para guardar uma imagem do modelo treinado, selecione o separador **Saídas** no painel direito do módulo **modelo Train.** Selecione o ícone do conjunto de **dados Register** para salvar o modelo como um módulo reutilizável.

+ Para utilizar o modelo para a pontuação, adicione o módulo **Score Model** a um pipeline.

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 