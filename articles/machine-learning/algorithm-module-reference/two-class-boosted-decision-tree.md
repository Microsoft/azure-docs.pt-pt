---
title: 'Árvore de decisão reforçada de duas classes: referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo de árvore de decisão impulsionada por duas classes em Azure Machine Learning para criar um modelo de aprendizagem automática que se baseie no algoritmo de árvores de decisão impulsionada.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 1d144a48f79e59b35c88c5b338747d3186ebceda
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920744"
---
# <a name="two-class-boosted-decision-tree-module"></a>Módulo de árvore de decisão impulsionado por duas classes

Este artigo descreve um módulo em Azure Machine Learning designer (pré-visualização).

Use este módulo para criar um modelo de aprendizagem automática que se baseie no algoritmo de árvores de decisão impulsionada. 

Uma árvore de decisão reforçada é um método de aprendizagem conjunto em que a segunda árvore corrige os erros da primeira árvore, a terceira árvore corrige para os erros da primeira e segunda árvores, e assim por diante.  As previsões baseiam-se em todo o conjunto de árvores que fazem a previsão.
  
Geralmente, quando devidamente configuradas, as árvores de decisão impulsionadas são os métodos mais fáceis para obter o melhor desempenho numa grande variedade de tarefas de aprendizagem automática. No entanto, são também um dos alunos mais intensivos da memória, e a implementação atual guarda tudo na memória. Portanto, um modelo de árvore de decisão reforçado pode não ser capaz de processar os grandes conjuntos de dados que alguns alunos lineares podem lidar.

## <a name="how-to-configure"></a>Como configurar

Este módulo cria um modelo de classificação destreinado. Como a classificação é um método de aprendizagem supervisionado, para treinar o modelo, você precisa de um conjunto de *dados marcado* que inclua uma coluna de etiqueta com um valor para todas as linhas.

Pode treinar este tipo de modelo utilizando [o Modelo de Comboio](././train-model.md). 

1.  No Azure Machine Learning, adicione o módulo **Boosted Decision Tree** ao seu pipeline.
  
2.  Especifique como pretende que o modelo seja treinado, definindo a opção modo **Criar.**
  
    + **Parâmetro único**: Se souber como pretende configurar o modelo, pode fornecer um conjunto específico de valores como argumentos.
  
    + **Gama de parâmetros**: Se não tiver a certeza dos melhores parâmetros, pode encontrar os parâmetros ideais utilizando o módulo de [hiperparâmetros](tune-model-hyperparameters.md) do Modelo tune. Você fornece alguma gama de valores, e o treinador iterates sobre múltiplas combinações das configurações para determinar a combinação de valores que produz o melhor resultado.
  
3.  Para o **número máximo de folhas por árvore,** indique o número máximo de nódos os terminais (folhas) que podem ser criados em qualquer árvore.
  
     Ao aumentar este valor, você potencialmente aumenta o tamanho da árvore e obtém uma melhor precisão, com o risco de excesso de montagem e tempo de treino mais longo.
  
4.  Para o **número mínimo de amostras por nó**de folha, indique o número de caixas necessárias para criar qualquer nó terminal (folha) numa árvore.  
  
     Ao aumentar este valor, aumenta-se o limiar para a criação de novas regras. Por exemplo, com o valor padrão de 1, mesmo um único caso pode fazer com que uma nova regra seja criada. Se aumentar o valor para 5, os dados de formação teriam de conter pelo menos cinco casos que satisfaçam as mesmas condições.
  
5.  Para **a taxa de aprendizagem**, digite um número entre 0 e 1 que defina o tamanho do passo durante a aprendizagem.  
  
     A taxa de aprendizagem determina a rapidez ou a lentidão do aluno converge na solução ideal. Se o tamanho do passo for demasiado grande, poderá ultrapassar a solução ideal. Se o tamanho do passo for muito pequeno, o treino demora mais tempo a convergir para a melhor solução.
  
6.  Para **o número de árvores construídas,** indicar o número total de árvores de decisão a criar no conjunto. Ao criar mais árvores de decisão, pode potencialmente obter uma melhor cobertura, mas o tempo de treino aumentará.
  
     Este valor também controla o número de árvores exibidas ao visualizar o modelo treinado. se quiser ver ou imprimir uma única árvore, deite o valor para 1. No entanto, quando o fizer, apenas uma árvore é produzida (a árvore com o conjunto inicial de parâmetros) e não são realizadas mais iterações.
  
7.  Para **sementes aleatórias,** digite opcionalmente um inteiro não negativo para usar como o valor de sementes aleatórias. Especificar uma semente garante a reprodutibilidade em percursos que tenham os mesmos dados e parâmetros.  
  
     A semente aleatória é definida por padrão para 0, o que significa que o valor inicial da semente é obtido a partir do relógio do sistema.  As corridas sucessivas com uma semente aleatória podem ter resultados diferentes.
  

9. Treine o modelo.
  
    + Se definir o modo Criar o **modo de formação** para **um parâmetro único,** ligue um conjunto de dados marcado e o módulo [Modelo de Comboio.](./train-model.md)  
   
## <a name="results"></a>Resultados

Após o treino estar completo:

+ Para guardar uma imagem do modelo treinado, selecione o separador **Saídas** no painel direito do módulo **modelo Train.** Selecione o ícone do conjunto de **dados Register** para salvar o modelo como um módulo reutilizável.

+ Para utilizar o modelo para a pontuação, adicione o módulo **Score Model** a um pipeline.

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 