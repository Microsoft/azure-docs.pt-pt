---
title: 'Árvore de decisão aumentada multiclasse: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo árvore de decisão aumentada multiclasse no Azure Machine Learning para criar um classificador usando dados rotulados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: 0bcca16bd89781428773eda168e6ee3c2f5784ef
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152181"
---
# <a name="multiclass-boosted-decision-tree"></a>Árvore de Decisões Elevada Multiclasse

Este artigo descreve um módulo em Azure Machine Learning designer (pré-visualização).

Use este módulo para criar um modelo de aprendizado de máquina baseado no algoritmo árvores de decisão aumentada.

Uma árvore de decisão aumentada é um método de aprendizado Ensemble no qual a segunda árvore corrige os erros da primeira árvore, a terceira árvore corrige os erros da primeira e segunda árvores e assim por diante. As previsões são baseadas na Ensemble de árvores juntas.

## <a name="how-to-configure"></a>Como configurar 

Este módulo cria um modelo de classificação não treinado. Como a classificação é um método de aprendizagem supervisionado, você precisa de um conjunto de *dados rotulado* que inclua uma coluna de etiqueta com um valor para todas as linhas.

Pode treinar este tipo de modelo utilizando o [Modelo de Comboio](././train-model.md). 

1.  Adicione o módulo **Multiclass Boosted Decision Tree** ao seu pipeline.

1.  Especifique como pretende que o modelo seja treinado definindo a opção **modo de modo Criar.**

    + **Parâmetro único**: Se souber como pretende configurar o modelo, pode fornecer um conjunto específico de valores como argumentos.


    *  **O número máximo de folhas por árvore** limita o número máximo de nós terminais (folhas) que podem ser criados em qualquer árvore.
    
        Ao aumentar esse valor, você pode aumentar o tamanho da árvore e obter uma precisão maior, com o risco de superajuste e tempo de treinamento mais longo.
  
    * **O número mínimo de amostras por nó de folha** indica o número de caixas necessárias para criar qualquer nó terminal (folha) numa árvore.  

         Ao aumentar esse valor, você aumenta o limite para a criação de novas regras. Por exemplo, com o valor padrão de 1, mesmo um único caso pode fazer com que uma nova regra seja criada. Se você aumentar o valor para 5, os dados de treinamento precisarão conter pelo menos cinco casos que atendam às mesmas condições.

    * **A taxa de aprendizagem** define o tamanho do passo durante a aprendizagem. Insira um número entre 0 e 1.

         A taxa de aprendizagem determina o quão rápido ou lento o aprendiz convergi em uma solução ideal. Se o tamanho do passo for demasiado grande, poderá ultrapassar a solução ideal. Se o tamanho da etapa for muito pequeno, o treinamento levará mais tempo para convergir na melhor solução.

    * **O número de árvores construídas** indica o número total de árvores de decisão a criar no conjunto. Ao criar mais árvores de decisão, você pode potencialmente obter uma cobertura melhor, mas o tempo de treinamento aumentará.

    *  **A semente de número aleatório** define opcionalmente um inteiro não negativo para usar como o valor de sementes aleatórias. A especificação de uma semente garante reprodução entre as execuções que têm os mesmos dados e parâmetros.  

         A semente aleatória é definida por padrão como 42. As execuções sucessivas usando sementes aleatórias diferentes podem ter resultados diferentes.

> [!Note]
> Se definir o modo Criar o **modo de formação** para **um parâmetro único,** ligue um conjunto de dados marcado e o módulo [Modelo de Comboio.](./train-model.md)

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 
