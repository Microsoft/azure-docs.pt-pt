---
title: 'Árvore de decisão reforçada multiclasse: referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo Multiclass Boosted Decision Tree em Azure Machine Learning para criar um classificador utilizando dados rotulados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/19/2020
ms.openlocfilehash: 1a1cb7661ae01dd89d45afe004978813ac90eaff
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "90905303"
---
# <a name="multiclass-boosted-decision-tree"></a>Árvore de Decisões Elevada Multiclasse

Este artigo descreve um módulo no designer de Aprendizagem automática Azure.

Use este módulo para criar um modelo de aprendizagem automática que se baseia no algoritmo de árvores de decisão reforçada.

Uma árvore de decisão reforçada é um método de aprendizagem conjunto em que a segunda árvore corrige para os erros da primeira árvore, a terceira árvore corrige para os erros da primeira e segunda árvores, e assim por diante. As previsões baseiam-se no conjunto de árvores juntas.

## <a name="how-to-configure"></a>Como configurar 

Este módulo cria um modelo de classificação destreinado. Como a classificação é um método de aprendizagem supervisionado, você precisa de um *conjunto de dados rotulado* que inclua uma coluna de etiqueta com um valor para todas as linhas.

Pode treinar este tipo de modelo utilizando o [Modelo de Comboio.](././train-model.md) 

1.  Adicione o módulo **Multiclass Boosted Decision Tree** ao seu oleoduto.

1.  Especifique como pretende que o modelo seja treinado definindo a opção **modo de formação Create.**

    + **Parâmetro único**: Se souber como pretende configurar o modelo, pode fornecer um conjunto específico de valores como argumentos.
    
    + **Intervalo de parâmetros**: Selecione esta opção se não tiver a certeza dos melhores parâmetros e pretender fazer uma varredura de parâmetros. Selecione uma gama de valores para iterar e o [Tune Model Hyperparameters](tune-model-hyperparameters.md) itera sobre todas as combinações possíveis das configurações fornecidas para determinar os hiperparmetros que produzem os melhores resultados.  

1. **O número máximo de folhas por árvore** limita o número máximo de nós terminais (folhas) que podem ser criados em qualquer árvore.
    
    Ao aumentar este valor, você potencialmente aumenta o tamanho da árvore e obtém uma maior precisão, correndo o risco de sobremontagem e tempo de treino mais longo.
  
1. **O número mínimo de amostras por nó folha** indica o número de casos necessários para criar qualquer nó terminal (folha) numa árvore.  

    Ao aumentar este valor, aumenta-se o limiar para a criação de novas regras. Por exemplo, com o valor padrão de 1, mesmo um único caso pode fazer com que uma nova regra seja criada. Se aumentar o valor para 5, os dados da formação terão de conter pelo menos cinco casos que satisfaçam as mesmas condições.

1. **A taxa de aprendizagem** define o tamanho do passo durante a aprendizagem. Insira um número entre 0 e 1.

    A taxa de aprendizagem determina a rapidez ou a lentidão com que o aluno converge numa solução ideal. Se o tamanho do passo for demasiado grande, poderá ultrapassar a solução ideal. Se o tamanho do degrau for muito pequeno, o treino demora mais tempo a convergir para a melhor solução.

1. **O número de árvores construídas** indica o número total de árvores de decisão a criar no conjunto. Ao criar mais árvores de decisão, você pode potencialmente obter uma melhor cobertura, mas o tempo de treino vai aumentar.

1. **A semente de número aleatório** define opcionalmente um número inteiro não negativo para usar como valor de sementes aleatórias. Especificar uma semente garante a reprodutibilidade através de percursos que têm os mesmos dados e parâmetros.  

    A semente aleatória é definida por defeito para 42. Corridas sucessivas usando diferentes sementes aleatórias podem ter resultados diferentes.

1. Treine o modelo:

    + Se definir **Criar o modo de treinador** para single **parâmetro,** conecte um conjunto de dados marcado e o módulo Modelo de [Comboio.](train-model.md)  
  
    + Se definir **Criar modo de treinador** para intervalo de **parâmetros,** ligue um conjunto de dados marcado e treine o modelo utilizando [hiperparmetros do modelo de sintonização](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Se passar uma gama de parâmetros para [o Modelo de Comboio,](train-model.md)utiliza apenas o valor predefinido na lista de parâmetros únicos.  
    > 
    > Se passar um único conjunto de valores de parâmetros para o módulo [Tune Model Hyperparameters,](tune-model-hyperparameters.md) quando espera uma gama de definições para cada parâmetro, ignora os valores e utiliza os valores predefinidos para o aluno.  
    > 
    > Se selecionar a opção De Alcance de **Parâmetros** e introduzir um único valor para qualquer parâmetro, esse valor único especificado é utilizado ao longo da varredura, mesmo que outros parâmetros se alterem numa gama de valores.

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 
