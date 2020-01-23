---
title: 'Árvore de decisão aumentada de duas classes: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo árvore de decisão aumentada de duas classes no Azure Machine Learning para criar um modelo de aprendizado de máquina baseado no algoritmo árvores de decisão aumentada.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: 1c51ff1e653ac3fbbf6042cb6fb631b05ef94041
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548361"
---
# <a name="two-class-boosted-decision-tree-module"></a>Módulo de árvore de decisão aumentada de duas classes

Este artigo descreve um módulo no designer de Azure Machine Learning (versão prévia).

Use este módulo para criar um modelo de aprendizado de máquina baseado no algoritmo árvores de decisão aumentada. 

Uma árvore de decisão aumentada é um método de aprendizado Ensemble no qual a segunda árvore corrige os erros da primeira árvore, a terceira árvore corrige os erros da primeira e segunda árvores e assim por diante.  As previsões são baseadas em toda a Ensemble de árvores juntas que faz a previsão.
  
Geralmente, quando configuradas corretamente, as árvores de decisão aumentadas são os métodos mais fáceis com os quais obter o melhor desempenho em uma ampla variedade de tarefas de aprendizado de máquina. No entanto, eles também são um dos mais aprendizes com uso intensivo de memória e a implementação atual contém tudo na memória. Portanto, um modelo de árvore de decisão impulsionado pode não ser capaz de processar os grandes conjuntos de altos que alguns aprendizes lineares podem manipular.

## <a name="how-to-configure"></a>Como configurar

Este módulo cria um modelo de classificação não treinado. Como a classificação é um método de aprendizado supervisionado, para treinar o modelo, você precisa de um conjunto de informações *marcado* que inclua uma coluna de rótulo com um valor para todas as linhas.

Você pode treinar esse tipo de modelo usando o [modelo de treinamento](././train-model.md). 

1.  Em Azure Machine Learning, adicione o módulo **árvore de decisão aumentada** ao seu pipeline.
  
2.  Especifique como você deseja que o modelo seja treinado, definindo a opção **criar modo de instrutor** .
  
    + **Parâmetro único**: se você souber como deseja configurar o modelo, poderá fornecer um conjunto específico de valores como argumentos.
  
  
3.  Para o **número máximo de folhas por árvore**, indique o número máximo de nós de terminal (folhas) que podem ser criados em qualquer árvore.
  
     Ao aumentar esse valor, você pode aumentar o tamanho da árvore e obter uma precisão melhor, com o risco de superajuste e tempo de treinamento mais longo.
  
4.  Para o **número mínimo de amostras por nó folha**, indique o número de casos necessários para criar qualquer nó de terminal (folha) em uma árvore.  
  
     Ao aumentar esse valor, você aumenta o limite para a criação de novas regras. Por exemplo, com o valor padrão de 1, mesmo um único caso pode fazer com que uma nova regra seja criada. Se você aumentar o valor para 5, os dados de treinamento precisarão conter pelo menos cinco casos que atendam às mesmas condições.
  
5.  Para **taxa de aprendizagem**, digite um número entre 0 e 1 que defina o tamanho da etapa durante o aprendizado.  
  
     A taxa de aprendizagem determina o quão rápido ou lento o aprendiz convergi na solução ideal. Se o tamanho da etapa for muito grande, você poderá exceder a solução ideal. Se o tamanho da etapa for muito pequeno, o treinamento levará mais tempo para convergir na melhor solução.
  
6.  Para o **número de árvores construídas**, indique o número total de árvores de decisão a serem criadas no Ensemble. Ao criar mais árvores de decisão, você pode potencialmente obter uma cobertura melhor, mas o tempo de treinamento aumentará.
  
     Esse valor também controla o número de árvores exibidas ao visualizar o modelo treinado. Se você quiser ver ou imprimir uma única árvore, defina o valor como 1. No entanto, quando você faz isso, apenas uma árvore é produzida (a árvore com o conjunto inicial de parâmetros) e nenhuma iteração adicional é executada.
  
7.  Para **semente de número aleatório**, opcionalmente, digite um inteiro não negativo para usar como o valor de semente aleatória. A especificação de uma semente garante reprodução entre as execuções que têm os mesmos dados e parâmetros.  
  
     A semente aleatória é definida por padrão como 0, o que significa que o valor de semente inicial é obtido do relógio do sistema.  As execuções sucessivas usando uma semente aleatória podem ter resultados diferentes.
  

9. Treine o modelo.
  
    + Se você definir **criar modo de instrutor** como um **único parâmetro**, conecte um conjunto de um DataSet marcado e o módulo [treinar modelo](./train-model.md) .  
   
## <a name="results"></a>Resultados

Após a conclusão do treinamento:

+ Para salvar um instantâneo do modelo treinado, selecione a guia **saídas** no painel direito do módulo modelo de **treinamento** . Selecione o ícone **registrar conjunto de registros** para salvar o modelo como um módulo reutilizável.

+ Para usar o modelo de pontuação, adicione o módulo **modelo de Pontuação** a um pipeline.

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 