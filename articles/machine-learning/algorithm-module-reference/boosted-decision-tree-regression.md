---
title: 'Regressão da árvore de decisão reforçada: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo de regressão da árvore de decisão impulsionada em Azure Machine Learning para criar um conjunto de árvores de regressão usando o boosting.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 10821639fb26af935326bda0bff7895105da675c
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77919962"
---
# <a name="boosted-decision-tree-regression-module"></a>Módulo de regressão da árvore de decisão reforçado

Este artigo descreve um módulo em Azure Machine Learning designer (pré-visualização).

Use este módulo para criar um conjunto de árvores de regressão usando o reforço. *Impulsionar* significa que cada árvore depende de árvores anteriores. O algoritmo aprende encaixando o residual das árvores que o precederam. Assim, o reforço de um conjunto de árvores de decisão tende a melhorar a precisão com algum pequeno risco de menor cobertura.  
  
Este método de regressão é um método de aprendizagem supervisionado e, portanto, requer um conjunto de *dados rotulado.* A coluna de etiquetas deve conter valores numéricos.  

> [!NOTE]
> Utilize este módulo apenas com conjuntos de dados que utilizem variáveis numéricas.  

Depois de definir o modelo, treine-o utilizando o [Modelo de Comboio](./train-model.md).

  
## <a name="more-about-boosted-regression-trees"></a>Mais sobre árvores de regressão reforçadas  

O boosting é um dos vários métodos clássicos para criar modelos conjuntos, juntamente com o empacotamento, florestas aleatórias, e assim por diante.  Em Azure Machine Learning, árvores de decisão impulsionadas usam uma implementação eficiente do algoritmo de reforço do gradiente DA MART. O reforço do gradiente é uma técnica de aprendizagem automática para problemas de regressão. Constrói cada árvore de regressão de forma passo a passo, utilizando uma função de perda predefinida para medir o erro em cada passo e corrigir para ele no próximo. Assim, o modelo de previsão é na verdade um conjunto de modelos de previsão mais fracos.  
  
Em problemas de regressão, o aumento constrói uma série de árvores de forma passo a passo, e depois seleciona a árvore ideal usando uma função arbitrária de perda diferenciadora.  
  
Para obter informações adicionais, consulte estes artigos:  
  
+ [https://wikipedia.org/wiki/Gradient_boosting#Gradient_tree_boosting](https://wikipedia.org/wiki/Gradient_boosting)

    Este artigo da Wikipedia sobre o reforço do gradiente fornece algum fundo sobre árvores impulsionadas. 
  
-  [https://research.microsoft.com/apps/pubs/default.aspx?id=132652](https://research.microsoft.com/apps/pubs/default.aspx?id=132652)  

    Microsoft Research: From RankNet to LambdaRank to LambdaMART: An Overview. Por J.C. Burges, burges.

O método de reforço do gradiente também pode ser usado para problemas de classificação, reduzindo-os à regressão com uma função de perda adequada. Para obter mais informações sobre a implementação de árvores reforçadas para tarefas de classificação, consulte árvore [de decisão impulsionada por duas classes](./two-class-boosted-decision-tree.md).  

## <a name="how-to-configure-boosted-decision-tree-regression"></a>Como configurar a regressão da árvore de decisão reforçada

1.  Adicione o módulo **Boosted Decision Tree** ao seu oleoduto. Pode encontrar este módulo na categoria **Machine Learning**, **Initialize,** na categoria **Regression.** 
  
2.  Especifique como pretende que o modelo seja treinado, definindo a opção modo **Criar.**  
  
    -   **Parâmetro único**: Selecione esta opção se souber como pretende configurar o modelo e forneça um conjunto específico de valores como argumentos. 
     
    -   **Gama de parâmetros**: Selecione esta opção se não tiver a certeza dos melhores parâmetros e pretenda fazer uma varredura de parâmetros. Selecione uma gama de valores para iterar mais, e o [Modelo tune Hyperparâmetros](tune-model-hyperparameters.md) iterates sobre todas as combinações possíveis das definições que forneceu para determinar os hiperparâmetros que produzem os melhores resultados.    
   
  
3. **Número máximo de folhas por árvore**: Indicar o número máximo de nódos os terminais (folhas) que podem ser criados em qualquer árvore.  

    Ao aumentar este valor, você potencialmente aumenta o tamanho da árvore e obtém uma melhor precisão, com o risco de excesso de montagem e tempo de treino mais longo.  

4. **Número mínimo de amostras por nó de folha**: Indicar o número mínimo de caixas necessárias para criar qualquer nó de terminais (folha) numa árvore.

    Ao aumentar este valor, aumenta-se o limiar para a criação de novas regras. Por exemplo, com o valor padrão de 1, mesmo um único caso pode fazer com que uma nova regra seja criada. Se aumentar o valor para 5, os dados de formação teriam de conter pelo menos 5 casos que satisfaçam as mesmas condições.

5. **Taxa de aprendizagem**: Digite um número entre 0 e 1 que defina o tamanho do passo durante a aprendizagem. A taxa de aprendizagem determina a rapidez ou a lentidão do aluno converge na solução ideal. Se o tamanho do passo for muito grande, poderá ultrapassar a solução ideal. Se o tamanho do passo for muito pequeno, o treino demora mais tempo a convergir para a melhor solução.

6. **Número de árvores construídas**: Indicar o número total de árvores de decisão a criar no conjunto. Ao criar mais árvores de decisão, você pode potencialmente obter uma melhor cobertura, mas o tempo de treino aumenta.

    Este valor também controla o número de árvores exibidas ao visualizar o modelo treinado. se quiser ver ou imprimir uma única árvore, pode definir o valor para 1; no entanto, apenas uma árvore é produzida (a árvore com o conjunto inicial de parâmetros) e não são realizadas mais iterações.

7. **Semente**de número aleatório: Digite um inteiro opcional não negativo para usar como valor de semente aleatória. Especificar uma semente garante a reprodutibilidade em percursos que tenham os mesmos dados e parâmetros.

    Por predefinição, a semente aleatória está definida para 0, o que significa que o valor inicial da semente é obtido a partir do relógio do sistema.
  

9. Adicione um conjunto de dados de treino e um dos módulos de treino:

    - Se definir a opção **de modo de treinador criar** para parâmetro **único,** utilize o módulo [Modelo de Comboio.](train-model.md)  
  
    

10. Executar o pipeline.  
  
## <a name="results"></a>Resultados

Após o treino estar completo:

+ Para utilizar o modelo para pontuar, conecte-o ao [Score Model,](./score-model.md)para prever valores para novos exemplos de entrada.

+ Para guardar uma imagem instantânea do modelo treinado, selecione o separador **Outputs** no painel direito do **modelo Treinado** e clique no ícone do conjunto de **dados Register.** A cópia do modelo treinado será guardada como um módulo na árvore do módulo e não será atualizada em sucessivas corridas do pipeline.

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 
