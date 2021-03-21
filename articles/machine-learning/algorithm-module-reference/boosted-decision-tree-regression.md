---
title: 'Regressão da árvore da decisão reforçada: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo de regressão da árvore de decisão reforçada no Azure Machine Learning para criar um conjunto de árvores de regressão usando o reforço.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 08/24/2020
ms.openlocfilehash: 664943fc5535883b3df77b2795383e5c0586a71c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94375334"
---
# <a name="boosted-decision-tree-regression-module"></a>Módulo de regressão da árvore da decisão reforçada

Este artigo descreve um módulo no designer de Aprendizagem automática Azure.

Utilize este módulo para criar um conjunto de árvores de regressão utilizando o reforço. *Aumentar* significa que cada árvore depende de árvores anteriores. O algoritmo aprende encaixando o residual das árvores que o precederam. Assim, o aumento de um conjunto de árvores de decisão tende a melhorar a precisão com algum pequeno risco de menor cobertura.  

Este módulo é baseado no algoritmo LightGBM.
  
Este método de regressão é um método de aprendizagem supervisionado e, portanto, requer um *conjunto de dados rotulado*. A coluna da etiqueta deve conter valores numéricos.  

> [!NOTE]
> Utilize este módulo apenas com conjuntos de dados que utilizam variáveis numéricas.  

Depois de definir o modelo, treine-o utilizando o [Modelo de Comboio.](./train-model.md)

  
## <a name="more-about-boosted-regression-trees"></a>Mais sobre árvores de regressão reforçada  

Impulsionar é um dos vários métodos clássicos para criar modelos de conjunto, juntamente com o embalamento, florestas aleatórias, e assim por diante.  Em Azure Machine Learning, as árvores de decisão reforçadas usam uma implementação eficiente do algoritmo de aumento do gradiente MART. O aumento do gradiente é uma técnica de aprendizagem automática para problemas de regressão. Constrói cada árvore de regressão de forma passo a passo, utilizando uma função de perda predefinida para medir o erro em cada passo e corrigir para ele no próximo. Assim, o modelo de previsão é na verdade um conjunto de modelos de previsão mais fracos.  
  
Em problemas de regressão, impulsionar constrói uma série de árvores de forma passo a passo, e em seguida, seleciona a árvore ideal usando uma função de perda arbitrária diferenciada.  
  
Para obter informações adicionais, consulte estes artigos:  
  
+ [https://wikipedia.org/wiki/Gradient_boosting#Gradient_tree_boosting](https://wikipedia.org/wiki/Gradient_boosting)

    Este artigo da Wikipedia sobre o aumento do gradiente fornece algum fundo em árvores impulsionadas. 
  
-  [https://research.microsoft.com/apps/pubs/default.aspx?id=132652](https://research.microsoft.com/apps/pubs/default.aspx?id=132652)  

    Microsoft Research: De RankNet a LambdaRank a LambdaMART: Uma visão geral. Por J.C. Os Burges.

O método de aumento do gradiente também pode ser usado para problemas de classificação, reduzindo-os à regressão com uma função de perda adequada. Para obter mais informações sobre a implementação das árvores reforçadas para tarefas de classificação, consulte [a Árvore de Decisão Reforçada de Duas Classes.](./two-class-boosted-decision-tree.md)  

## <a name="how-to-configure-boosted-decision-tree-regression"></a>Como configurar regressão da árvore de decisão reforçada

1.  Adicione o módulo **Árvore de Decisão Reforçada** ao seu oleoduto. Pode encontrar este módulo na categoria **Machine Learning**, **Initialize,** na categoria **Regressão.** 
  
2.  Especifique como pretende que o modelo seja treinado, definindo a opção **modo de formação Create.**  
  
    -   **Parâmetro único**: Selecione esta opção se souber como pretende configurar o modelo e fornecer um conjunto específico de valores como argumentos. 
     
    -   **Intervalo de parâmetros**: Selecione esta opção se não tiver a certeza dos melhores parâmetros e pretender fazer uma varredura de parâmetros. Selecione uma gama de valores para iterar e o [Tune Model Hyperparameters](tune-model-hyperparameters.md) itera sobre todas as combinações possíveis das configurações fornecidas para determinar os hiperparmetros que produzem os melhores resultados.    
   
  
3. **Número máximo de folhas por árvore**: Indicar o número máximo de nós terminais (folhas) que podem ser criados em qualquer árvore.  

    Ao aumentar este valor, você potencialmente aumenta o tamanho da árvore e obtém uma melhor precisão, correndo o risco de sobremontagem e tempo de treino mais longo.  

4. **Número mínimo de amostras por nó folha**: Indique o número mínimo de casos necessários para criar qualquer nó terminal (folha) numa árvore.

    Ao aumentar este valor, aumenta-se o limiar para a criação de novas regras. Por exemplo, com o valor padrão de 1, mesmo um único caso pode fazer com que uma nova regra seja criada. Se aumentar o valor para 5, os dados da formação terão de conter pelo menos 5 casos que satisfaçam as mesmas condições.

5. **Taxa de aprendizagem**: Digite um número entre 0 e 1 que define o tamanho do passo durante a aprendizagem. A taxa de aprendizagem determina a rapidez ou a lentidão com que o aluno converge na solução ideal. Se o tamanho do passo for demasiado grande, poderá ultrapassar a solução ideal. Se o tamanho do degrau for muito pequeno, o treino demora mais tempo a convergir para a melhor solução.

6. **Número de árvores construídas**: Indicar o número total de árvores de decisão a criar no conjunto. Ao criar mais árvores de decisão, você pode potencialmente obter uma melhor cobertura, mas o tempo de treino aumenta.

    Se definir o valor para 1; no entanto, apenas uma árvore é produzida (a árvore com o conjunto inicial de parâmetros) e não são realizadas mais iterações.

7. **Semente de número aleatório**: Digite um número inteiro opcional não negativo para usar como valor de sementes aleatórias. Especificar uma semente garante a reprodutibilidade através de percursos que têm os mesmos dados e parâmetros.

    Por predefinição, a semente aleatória é definida para 0, o que significa que o valor inicial da semente é obtido a partir do relógio do sistema.
  

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
    

10. Envie o oleoduto.  
  
## <a name="results"></a>Resultados

Após o treino estar completo:

+ Para utilizar o modelo para pontuar, ligue o [Modelo de Comboio](train-model.md) ao Modelo de [Pontuação,](./score-model.md)para prever valores para novos exemplos de entrada.

+ Para guardar uma imagem do modelo treinado, selecione o separador **Saídas** no painel direito do **modelo treinado** e clique no ícone do conjunto de **dados do Registo.** A cópia do modelo treinado será guardada como módulo na árvore do módulo e não será atualizada em sucessivas execuções do gasoduto.

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 
