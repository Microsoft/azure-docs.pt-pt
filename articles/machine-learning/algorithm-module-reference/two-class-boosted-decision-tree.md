---
title: 'Two-Class Árvore de Decisão Reforçada: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo Two-Class Boosted Decision Tree no designer para criar um classificador binário.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 08/24/2020
ms.openlocfilehash: 810878956a89b6e7400be99692cf43925d1cd134
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94375932"
---
# <a name="two-class-boosted-decision-tree-module"></a>Two-Class módulo de árvore de decisão reforçada

Este artigo descreve um módulo no designer de Aprendizagem automática Azure.

Use este módulo para criar um modelo de aprendizagem automática que se baseia no algoritmo de árvores de decisão reforçada. 

Uma árvore de decisão reforçada é um método de aprendizagem conjunto em que a segunda árvore corrige para os erros da primeira árvore, a terceira árvore corrige para os erros da primeira e segunda árvores, e assim por diante. As previsões são baseadas em todo o conjunto de árvores juntas que faz a previsão.
  
Geralmente, quando devidamente configuradas, as árvores de decisão reforçadas são os métodos mais fáceis de obter o melhor desempenho em uma grande variedade de tarefas de aprendizagem automática. No entanto, são também um dos alunos mais intensivos de memória, e a implementação atual guarda tudo na memória. Portanto, um modelo de árvore de decisão reforçada pode não ser capaz de processar os grandes conjuntos de dados que alguns alunos lineares podem lidar.

Este módulo é baseado no algoritmo LightGBM.

## <a name="how-to-configure"></a>Como configurar

Este módulo cria um modelo de classificação destreinado. Como a classificação é um método de aprendizagem supervisionado, para treinar o modelo, é necessário um conjunto de *dados marcado* que inclua uma coluna de etiqueta com um valor para todas as linhas.

Pode treinar este tipo de modelo utilizando [o Modelo de Comboio.](././train-model.md) 

1.  Em Azure Machine Learning, adicione o módulo **De Decisão Reforçada** ao seu oleoduto.
  
2.  Especifique como pretende que o modelo seja treinado, definindo a opção **modo de formação Create.**
  
    + **Parâmetro único**: Se souber como pretende configurar o modelo, pode fornecer um conjunto específico de valores como argumentos.
  
    + **Intervalo de parâmetros**: Se não tiver a certeza dos melhores parâmetros, pode encontrar os parâmetros ideais utilizando o módulo [De Hiperparametros Tune Model.](tune-model-hyperparameters.md) Fornece alguma gama de valores, e o treinador itera sobre várias combinações das definições para determinar a combinação de valores que produz o melhor resultado.
  
3.  Para **o número máximo de folhas por árvore,** indique o número máximo de nós terminais (folhas) que podem ser criados em qualquer árvore.
  
     Ao aumentar este valor, você potencialmente aumenta o tamanho da árvore e obtém uma melhor precisão, correndo o risco de sobremontagem e tempo de treino mais longo.
  
4.  Para **o número mínimo de amostras por nó folha,** indique o número de casos necessários para criar qualquer nó terminal (folha) numa árvore.  
  
     Ao aumentar este valor, aumenta-se o limiar para a criação de novas regras. Por exemplo, com o valor padrão de 1, mesmo um único caso pode fazer com que uma nova regra seja criada. Se aumentar o valor para 5, os dados da formação terão de conter pelo menos cinco casos que satisfaçam as mesmas condições.
  
5.  Para **a taxa de aprendizagem,** digite um número entre 0 e 1 que define o tamanho do passo durante a aprendizagem.  
  
     A taxa de aprendizagem determina a rapidez ou a lentidão com que o aluno converge na solução ideal. Se o tamanho do passo for demasiado grande, poderá ultrapassar a solução ideal. Se o tamanho do degrau for muito pequeno, o treino demora mais tempo a convergir para a melhor solução.
  
6.  Para **o número de árvores construídas,** indicar o número total de árvores de decisão a criar no conjunto. Ao criar mais árvores de decisão, você pode potencialmente obter uma melhor cobertura, mas o tempo de treino vai aumentar.
  
     Se definir o valor para 1, apenas uma árvore é produzida (a árvore com o conjunto inicial de parâmetros) e não são realizadas mais iterações.
  
7.  Para **sementes de número aleatório,** digite opcionalmente um número inteiro não negativo para usar como valor de sementes aleatórias. Especificar uma semente garante a reprodutibilidade através de percursos que têm os mesmos dados e parâmetros.  
  
     A semente aleatória é definida por defeito a 0, o que significa que o valor inicial da semente é obtido a partir do relógio do sistema.  Corridas sucessivas usando uma semente aleatória podem ter resultados diferentes.
  

9. Treine o modelo:

    + Se definir **Criar o modo de treinador** para single **parâmetro,** conecte um conjunto de dados marcado e o módulo Modelo de [Comboio.](train-model.md)  
  
    + Se definir **Criar modo de treinador** para intervalo de **parâmetros,** ligue um conjunto de dados marcado e treine o modelo utilizando [hiperparmetros do modelo de sintonização](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Se passar uma gama de parâmetros para [o Modelo de Comboio,](train-model.md)utiliza apenas o valor predefinido na lista de parâmetros únicos.  
    > 
    > Se passar um único conjunto de valores de parâmetros para o módulo [Tune Model Hyperparameters,](tune-model-hyperparameters.md) quando espera uma gama de definições para cada parâmetro, ignora os valores e utiliza os valores predefinidos para o aluno.  
    > 
    > Se selecionar a opção De Alcance de **Parâmetros** e introduzir um único valor para qualquer parâmetro, esse valor único especificado é utilizado ao longo da varredura, mesmo que outros parâmetros se alterem numa gama de valores.  
   
## <a name="results"></a>Resultados

Após o treino estar completo:

+ Para guardar uma imagem do modelo treinado, selecione o **separador Saídas** no painel direito do módulo **modelo Train.** Selecione o ícone **do conjunto de dados registar** para guardar o modelo como um módulo reutilizável.

+ Para utilizar o modelo para pontuar, adicione o módulo **'Marcar',** a um pipeline.

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 