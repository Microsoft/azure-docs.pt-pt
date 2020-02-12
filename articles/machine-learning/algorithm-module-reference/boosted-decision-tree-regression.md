---
title: 'Regressão de árvore de decisão aumentada: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo regressão da árvore de decisão aumentada no Azure Machine Learning para criar um Ensemble de árvores de regressão usando o aumento.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 5298655437e04736e56193c443b8a770ea929606
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152419"
---
# <a name="boosted-decision-tree-regression-module"></a>Módulo de regressão da árvore de decisão aumentada

Este artigo descreve um módulo em Azure Machine Learning designer (pré-visualização).

Use este módulo para criar um Ensemble de árvores de regressão usando o aumento. *Impulsionar* significa que cada árvore depende de árvores anteriores. O algoritmo aprende ajustando o resíduo das árvores que o precedem. Portanto, o aumento em uma árvore de decisão Ensemble tende a melhorar a precisão com um pequeno risco de menos cobertura.  
  
Este método de regressão é um método de aprendizagem supervisionado e, portanto, requer um conjunto de *dados rotulado.* A coluna de rótulo deve conter valores numéricos.  

> [!NOTE]
> Use este módulo somente com conjuntos de valores que usam variáveis numéricas.  

Depois de definir o modelo, treine-o utilizando o [Modelo de Comboio](./train-model.md).

  
## <a name="more-about-boosted-regression-trees"></a>Mais sobre árvores de regressão aumentadas  

O aumento é um dos vários métodos clássicos para a criação de modelos Ensemble, juntamente com bagging, florestas aleatórias e assim por diante.  Em Azure Machine Learning, as árvores de decisão aumentadas usam uma implementação eficiente do algoritmo de aumento de gradiente do MART. O aumento de gradiente é uma técnica de aprendizado de máquina para problemas de regressão. Ele cria cada árvore de regressão de uma maneira em etapas, usando uma função de perda predefinida para medir o erro em cada etapa e corrigi-lo no próximo. Portanto, o modelo de previsão é, na verdade, um Ensemble de modelos de previsão mais fracos.  
  
Em problemas de regressão, o aumento cria uma série de árvores de uma maneira passo e, em seguida, seleciona a árvore ideal usando uma função de perda diferenciável arbitrária.  
  
Para obter informações adicionais, consulte estes artigos:  
  
+ [https://wikipedia.org/wiki/Gradient_boosting#Gradient_tree_boosting](https://wikipedia.org/wiki/Gradient_boosting)

    Este artigo da Wikipédia sobre o aumento de gradiente fornece alguma experiência em árvores aumentadas. 
  
-  [https://research.microsoft.com/apps/pubs/default.aspx?id=132652](https://research.microsoft.com/apps/pubs/default.aspx?id=132652)  

    Microsoft Research: de RankNet para LambdaRank para LambdaMART: uma visão geral. Por J.C. Burges.

O método de aumento de gradiente também pode ser usado para problemas de classificação, reduzindo-os para regressão com uma função de perda adequada. Para obter mais informações sobre a implementação de árvores reforçadas para tarefas de classificação, consulte árvore [de decisão impulsionada por duas classes](./two-class-boosted-decision-tree.md).  

## <a name="how-to-configure-boosted-decision-tree-regression"></a>Como configurar a regressão da árvore de decisão aumentada

1.  Adicione o módulo **Boosted Decision Tree** ao seu oleoduto. Pode encontrar este módulo na categoria **Machine Learning**, **Initialize,** na categoria **Regression.** 
  
2.  Especifique como pretende que o modelo seja treinado, definindo a opção modo **Criar.**  
  
    -   **Parâmetro único**: Selecione esta opção se souber como pretende configurar o modelo e forneça um conjunto específico de valores como argumentos.  
   
  
3. **Número máximo de folhas por árvore**: Indicar o número máximo de nódos os terminais (folhas) que podem ser criados em qualquer árvore.  

    Ao aumentar esse valor, você pode aumentar o tamanho da árvore e obter uma precisão melhor, com o risco de superajuste e tempo de treinamento mais longo.  

4. **Número mínimo de amostras por nó de folha**: Indicar o número mínimo de caixas necessárias para criar qualquer nó de terminais (folha) numa árvore.

    Ao aumentar esse valor, você aumenta o limite para a criação de novas regras. Por exemplo, com o valor padrão de 1, mesmo um único caso pode fazer com que uma nova regra seja criada. Se você aumentar o valor para 5, os dados de treinamento precisarão conter pelo menos 5 casos que atendam às mesmas condições.

5. **Taxa de aprendizagem**: Digite um número entre 0 e 1 que defina o tamanho do passo durante a aprendizagem. A taxa de aprendizagem determina o quão rápido ou lento o aprendiz convergi na solução ideal. Se o tamanho da etapa for muito grande, você poderá exceder a solução ideal. Se o tamanho da etapa for muito pequeno, o treinamento levará mais tempo para convergir na melhor solução.

6. **Número de árvores construídas**: Indicar o número total de árvores de decisão a criar no conjunto. Ao criar mais árvores de decisão, você pode potencialmente obter uma cobertura melhor, mas o tempo de treinamento aumenta.

    Esse valor também controla o número de árvores exibidas ao visualizar o modelo treinado. Se você quiser ver ou imprimir uma única árvore, poderá definir o valor como 1; no entanto, apenas uma árvore é produzida (a árvore com o conjunto inicial de parâmetros) e nenhuma iteração adicional é executada.

7. **Semente**de número aleatório: Digite um inteiro opcional não negativo para usar como valor de semente aleatória. A especificação de uma semente garante reprodução entre as execuções que têm os mesmos dados e parâmetros.

    Por padrão, a semente aleatória é definida como 0, o que significa que o valor de semente inicial é obtido do relógio do sistema.
  
8. **Permitir níveis categóricos desconhecidos**: Selecione esta opção para criar um grupo para valores desconhecidos nos conjuntos de formação e validação. Se você desmarcar essa opção, o modelo poderá aceitar somente os valores contidos nos dados de treinamento. O modelo pode ser menos preciso para valores conhecidos, mas pode fornecer previsões melhores para valores novos (desconhecidos).

9. Adicione um conjunto de uma de treinamento e um dos módulos de treinamento:

    - Se definir a opção **de modo de treinador criar** para parâmetro **único,** utilize o módulo [Modelo de Comboio.](train-model.md)  
  
    

10. Executar o pipeline.  
  
## <a name="results"></a>Resultados

Após a conclusão do treinamento:

+ Para utilizar o modelo para pontuar, conecte-o ao [Score Model,](./score-model.md)para prever valores para novos exemplos de entrada.

+ Para guardar uma imagem instantânea do modelo treinado, selecione o separador **Outputs** no painel direito do **modelo Treinado** e clique no ícone do conjunto de **dados Register.** A cópia do modelo treinado será salva como um módulo na árvore de módulo e não será atualizada em execuções sucessivas do pipeline.

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 
