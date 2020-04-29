---
title: 'Árvore de decisão impulsionada multiclasse: referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo Multiclass Boosted Decision Tree em Azure Machine Learning para criar um classificador utilizando dados rotulados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/19/2020
ms.openlocfilehash: cfe35f81526a729092edf522f693ccd18494d1ec
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82137829"
---
# <a name="multiclass-boosted-decision-tree"></a>Árvore de Decisões Elevada Multiclasse

Este artigo descreve um módulo em Azure Machine Learning designer (pré-visualização).

Use este módulo para criar um modelo de aprendizagem automática que se baseie no algoritmo de árvores de decisão impulsionada.

Uma árvore de decisão reforçada é um método de aprendizagem conjunto em que a segunda árvore corrige os erros da primeira árvore, a terceira árvore corrige para os erros da primeira e segunda árvores, e assim por diante. As previsões baseiam-se no conjunto de árvores juntas.

## <a name="how-to-configure"></a>Como configurar 

Este módulo cria um modelo de classificação destreinado. Como a classificação é um método de aprendizagem supervisionado, você precisa de um conjunto de *dados rotulado* que inclua uma coluna de etiqueta com um valor para todas as linhas.

Pode treinar este tipo de modelo utilizando o [Modelo de Comboio](././train-model.md). 

1.  Adicione o módulo **Multiclass Boosted Decision Tree** ao seu pipeline.

1.  Especifique como pretende que o modelo seja treinado definindo a opção **modo de modo Criar.**

    + **Parâmetro único**: Se souber como pretende configurar o modelo, pode fornecer um conjunto específico de valores como argumentos.
    
    + **Gama de parâmetros**: Selecione esta opção se não tiver a certeza dos melhores parâmetros e pretenda fazer uma varredura de parâmetros. Selecione uma gama de valores para iterar mais, e o [Modelo tune Hyperparâmetros](tune-model-hyperparameters.md) iterates sobre todas as combinações possíveis das definições que forneceu para determinar os hiperparâmetros que produzem os melhores resultados.  

1. **O número máximo de folhas por árvore** limita o número máximo de nós terminais (folhas) que podem ser criados em qualquer árvore.
    
        By increasing this value, you potentially increase the size of the tree and achieve higher precision, at the risk of overfitting and longer training time.
  
1. **O número mínimo de amostras por nó de folha** indica o número de caixas necessárias para criar qualquer nó terminal (folha) numa árvore.  

         By increasing this value, you increase the threshold for creating new rules. For example, with the default value of 1, even a single case can cause a new rule to be created. If you increase the value to 5, the training data would have to contain at least five cases that meet the same conditions.

1. **A taxa de aprendizagem** define o tamanho do passo durante a aprendizagem. Insira um número entre 0 e 1.

         The learning rate determines how fast or slow the learner converges on an optimal solution. If the step size is too large, you might overshoot the optimal solution. If the step size is too small, training takes longer to converge on the best solution.

1. **O número de árvores construídas** indica o número total de árvores de decisão a criar no conjunto. Ao criar mais árvores de decisão, pode potencialmente obter uma melhor cobertura, mas o tempo de treino aumentará.

1. **A semente de número aleatório** define opcionalmente um inteiro não negativo para usar como o valor de sementes aleatórias. Especificar uma semente garante a reprodutibilidade em percursos que tenham os mesmos dados e parâmetros.  

         The random seed is set by default to 42. Successive runs using different random seeds can have different results.

1. Treine o modelo:

    + Se definir o modo Criar o **modo de formação** para **um parâmetro único,** ligue um conjunto de dados marcado e o módulo [Modelo de Comboio.](train-model.md)  
  
    + Se definir O modo Criar o **modo de formação** para o intervalo do **parâmetro,** ligue um conjunto de dados marcado e treine o modelo utilizando [hiperparâmetros do Modelo tune](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Se passar uma gama de parâmetros para [o Modelo de Comboio,](train-model.md)utiliza apenas o valor predefinido na lista de parâmetros individuais.  
    > 
    > Se passar um único conjunto de valores de parâmetros para o módulo de [hiperparâmetros](tune-model-hyperparameters.md) do Modelo tune, quando espera uma gama de definições para cada parâmetro, ignora os valores e utiliza os valores predefinidos para o aluno.  
    > 
    > Se selecionar a opção Gama de **Parâmetros** e introduzir um único valor para qualquer parâmetro, esse valor único especificado é utilizado ao longo da varredura, mesmo que outros parâmetros alterem uma gama de valores.

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 
