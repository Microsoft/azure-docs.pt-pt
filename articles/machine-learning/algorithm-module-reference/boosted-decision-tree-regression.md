---
title: 'Regressão da árvore de decisão aumentada: Referência de módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como usar o módulo regressão da árvore de decisão aumentada no serviço Azure Machine Learning para criar um Ensemble de árvores de regressão usando o aumento.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: de4135c2e516eb7f26e1b99a22a60501f4577cce
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128962"
---
# <a name="boosted-decision-tree-regression-module"></a>Módulo de regressão da árvore de decisão aumentada

Este artigo descreve um módulo da interface visual (visualização) para Azure Machine Learning serviço.

Use este módulo para criar um Ensemble de árvores de regressão usando o aumento. O *aumento* significa que cada árvore depende de árvores anteriores. O algoritmo aprende ajustando o resíduo das árvores que o precedem. Portanto, o aumento em uma árvore de decisão Ensemble tende a melhorar a precisão com um pequeno risco de menos cobertura.  
  
Esse método de regressão é um método de aprendizado supervisionado e, portanto, requer um *conjunto*de informações rotulado. A coluna de rótulo deve conter valores numéricos.  

> [!NOTE]
> Use este módulo somente com conjuntos de valores que usam variáveis numéricas.  

Depois de definir o modelo, treine-o usando o [modelo de treinamento](./train-model.md).

> [!TIP]
> Deseja saber mais sobre as árvores que foram criadas? Depois que o modelo tiver sido treinado, clique com o botão direito do mouse na saída do módulo [modelo de treinamento](./train-model.md) e selecione **Visualizar** para ver a árvore que foi criada em cada iteração. Você pode fazer uma busca detalhada nas divisões de cada árvore e ver as regras para cada nó.  
  
## <a name="more-about-boosted-regression-trees"></a>Mais sobre árvores de regressão aumentadas  

O aumento é um dos vários métodos clássicos para a criação de modelos Ensemble, juntamente com bagging, florestas aleatórias e assim por diante.  Em Azure Machine Learning, as árvores de decisão aumentadas usam uma implementação eficiente do algoritmo de aumento de gradiente do MART. O aumento de gradiente é uma técnica de aprendizado de máquina para problemas de regressão. Ele cria cada árvore de regressão de uma maneira em etapas, usando uma função de perda predefinida para medir o erro em cada etapa e corrigi-lo no próximo. Portanto, o modelo de previsão é, na verdade, um Ensemble de modelos de previsão mais fracos.  
  
Em problemas de regressão, o aumento cria uma série de árvores de uma maneira passo e, em seguida, seleciona a árvore ideal usando uma função de perda diferenciável arbitrária.  
  
Para obter informações adicionais, consulte estes artigos:  
  
+ [https://wikipedia.org/wiki/Gradient_boosting#Gradient_tree_boosting](https://wikipedia.org/wiki/Gradient_boosting)

    Este artigo da Wikipédia sobre o aumento de gradiente fornece alguma experiência em árvores aumentadas. 
  
-  [https://research.microsoft.com/apps/pubs/default.aspx?id=132652](https://research.microsoft.com/apps/pubs/default.aspx?id=132652)  

    Microsoft Research: De RankNet para LambdaRank para LambdaMART: Uma visão geral. Por J.C. Burges.

O método de aumento de gradiente também pode ser usado para problemas de classificação, reduzindo-os para regressão com uma função de perda adequada. Para obter mais informações sobre a implementação de árvores aumentadas para tarefas de classificação, consulte [árvore de decisão aumentada de duas classes](./two-class-boosted-decision-tree.md).  

## <a name="how-to-configure-boosted-decision-tree-regression"></a>Como configurar a regressão da árvore de decisão aumentada

1.  Adicione o módulo **árvore de decisão aumentada** ao seu experimento. Você pode encontrar esse módulo em **Machine Learning**, **inicializar**, na categoria **regressão** . 
  
2.  Especifique como você deseja que o modelo seja treinado, definindo a opção **criar modo de instrutor** .  
  
    -   **Parâmetro único**: Selecione esta opção se você souber como deseja configurar o modelo e fornecer um conjunto específico de valores como argumentos.  
   
  
3. **Número máximo de folhas por árvore**: Indique o número máximo de nós de terminal (folhas) que podem ser criados em qualquer árvore.  

    Ao aumentar esse valor, você pode aumentar o tamanho da árvore e obter uma precisão melhor, com o risco de superajuste e tempo de treinamento mais longo.  

4. **Número mínimo de amostras por nó folha**: Indica o número mínimo de casos necessários para criar qualquer nó de terminal (folha) em uma árvore.

    Ao aumentar esse valor, você aumenta o limite para a criação de novas regras. Por exemplo, com o valor padrão de 1, mesmo um único caso pode fazer com que uma nova regra seja criada. Se você aumentar o valor para 5, os dados de treinamento precisarão conter pelo menos 5 casos que atendam às mesmas condições.

5. **Taxa de aprendizagem**: Digite um número entre 0 e 1 que defina o tamanho da etapa durante o aprendizado. A taxa de aprendizagem determina o quão rápido ou lento o aprendiz convergi na solução ideal. Se o tamanho da etapa for muito grande, você poderá exceder a solução ideal. Se o tamanho da etapa for muito pequeno, o treinamento levará mais tempo para convergir na melhor solução.

6. **Número de árvores construídas**: Indique o número total de árvores de decisão a serem criadas no Ensemble. Ao criar mais árvores de decisão, você pode potencialmente obter uma cobertura melhor, mas o tempo de treinamento aumenta.

    Esse valor também controla o número de árvores exibidas ao visualizar o modelo treinado. Se você quiser ver ou imprimir uma árvore ingle, poderá definir o valor como 1; no entanto, apenas uma árvore é produzida (a árvore com o conjunto inicial de parâmetros) e nenhuma iteração adicional é executada.

7. **Semente de número aleatório**: Digite um inteiro não negativo opcional para usar como o valor de semente aleatória. A especificação de uma semente garante reprodução entre as execuções que têm os mesmos dados e parâmetros.

    Por padrão, a semente aleatória é definida como 0, o que significa que o valor de semente inicial é obtido do relógio do sistema.
  
8. **Permitir níveis categóricos**desconhecidos: Selecione esta opção para criar um grupo de valores desconhecidos nos conjuntos de treinamento e validação. Se você desmarcar essa opção, o modelo poderá aceitar somente os valores contidos nos dados de treinamento. O modelo pode ser menos preciso para valores conhecidos, mas pode fornecer previsões melhores para valores novos (desconhecidos).

9. Adicione um conjunto de uma de treinamento e um dos módulos de treinamento:

    - Se você definir a opção **criar modo de instrutor** como **parâmetro único**, use o módulo [treinar modelo](train-model.md) .  
  
    

10. Execute a experimentação.  
  
### <a name="results"></a>Resultados

Após a conclusão do treinamento:

+ Para ver a árvore que foi criada em cada iteração, clique com o botão direito do mouse na saída do módulo [modelo de treinamento](train-model.md) e selecione **Visualizar**.
  
     Clique em cada árvore para fazer uma busca detalhada nas divisões e ver as regras para cada nó.  

+ Para usar o modelo de pontuação, conecte-o ao [modelo de Pontuação](./score-model.md)para prever valores para novos exemplos de entrada.

+ Para salvar um instantâneo do modelo treinado, clique com o botão direito do mouse na saída do **modelo treinado** do módulo de treinamento e selecione **salvar como**. A cópia do modelo treinado que você salva não é atualizada em execuções sucessivas do experimento.

## <a name="next-steps"></a>Passos Seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning serviço. 