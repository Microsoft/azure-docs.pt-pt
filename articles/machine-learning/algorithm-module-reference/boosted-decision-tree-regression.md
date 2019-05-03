---
title: 'Regressão da árvore de decisões elevada: Referência do módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como utilizar o módulo de regressão da árvore de decisão elevada no serviço Azure Machine Learning para criar um ensemble das árvores de regressão usando adaptativo.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: b4ebf1740ec2b0288d8052cb075a61b720b031a2
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028339"
---
# <a name="boosted-decision-tree-regression-module"></a>Módulo de regressão da árvore de decisão elevado

Este artigo descreve um módulo da interface visual (pré-visualização) para o serviço Azure Machine Learning.

Utilize este módulo para criar um ensemble das árvores de regressão usando adaptativo. *Adaptativo* significa que cada árvore está dependente de árvores anteriores. O algoritmo fica a saber por que se ajusta residuais das árvores de que precedido-lo. Assim, aumentar num ensemble da árvore de decisão tende a aumentar a exatidão com algumas pequenas risco de cobertura de menos.  
  
Este método de regressão é um método de aprendizagem supervisionada e, portanto, requer uma *rotulado como conjunto de dados*. A coluna de etiqueta tem de conter valores numéricos.  

> [!NOTE]
> Utilize este módulo apenas com conjuntos de dados que utilizam variáveis numéricas.  

Depois de definir o modelo, formá-lo utilizando o [modelo de formação](./train-model.md).

> [!TIP]
> Quer saber mais sobre as árvores que foram criadas? Depois do modelo foi treinado, clique com botão direito a saída do [Train Model](./train-model.md) módulo e selecione **Visualize** para ver a árvore que foi criada em cada iteração. Pode desagregar para as divisões para cada árvore e ver as regras para cada nó.  
  
## <a name="more-about-boosted-regression-trees"></a>Mais informações sobre as árvores de regressão elevada  

Adaptativo é um dos vários métodos de clássicos para a criação de modelos de ensemble, juntamente com bagging florestas aleatórias e assim por diante.  No Azure Machine Learning, árvores de decisões elevada utilizam uma implementação eficiente do gradiente MART adaptativo algoritmo. Aumentam a gradação é uma técnica para problemas de regressão de aprendizagem. Ele cria cada árvore de regressão de forma sequencial, usando uma função de perda predefinidos para medir o erro em cada etapa e corrija-la no próximo. Portanto, o modelo de predição é, na verdade, um ensemble de modelos de previsão mais fracos.  
  
Em problemas de regressão, adaptativo baseia-se uma série de árvores de forma sequencial e, em seguida, seleciona a árvore ideal usando uma função de perda de diferenciáveis arbitrário.  
  
Para obter mais informações, veja estes artigos:  
  
+ [https://wikipedia.org/wiki/Gradient_boosting#Gradient_tree_boosting](https://wikipedia.org/wiki/Gradient_boosting)

    Este artigo da Wikipedia sobre aumentam a gradação fornece algumas informações básicas sobre árvores elevadas. 
  
-  [http://research.microsoft.com/apps/pubs/default.aspx?id=132652](http://research.microsoft.com/apps/pubs/default.aspx?id=132652)  

    Microsoft Research: De RankNet para LambdaRank para LambdaMART: Uma visão geral. Por J.C. Burges.

O gradiente adaptativo método também pode ser utilizado para problemas de classificação, reduzindo-los para regressão com uma função de perda adequado. Para obter mais informações sobre a implementação de árvores elevada para tarefas de classificação, consulte [árvore de decisões elevada de duas classes](./two-class-boosted-decision-tree.md).  

## <a name="how-to-configure-boosted-decision-tree-regression"></a>Como configurar a regressão da árvore de decisão elevada

1.  Adicionar a **árvore de decisões elevada** módulo à sua experimentação. Pode encontrar este módulo sob **Machine Learning**, **inicializar**, sob o **regressão** categoria. 
  
2.  Especifique como pretende que o modelo de formação, definindo a **modo de instrutor de criação** opção.  
  
    -   **Único parâmetro**: Selecione esta opção se souber como pretende configurar o modelo e forneça um conjunto específico de valores como argumentos.  
   
  
3. **Número máximo de folhas por árvore**: Indica o número máximo de nós terminal (folhas) que podem ser criados em qualquer árvore.  

    Ao aumentar este valor, potencialmente aumentar o tamanho da árvore e obtenha uma melhor precisão, sob overfitting e mais tempo de treinamento.  

4. **Número mínimo de exemplos por nó folha**: Indica o número mínimo de cenários necessários para criar qualquer nó de terminal (folha) numa árvore.

    Ao aumentar este valor, aumentar o limiar para a criação de novas regras. Por exemplo, com o valor predefinido de 1, até mesmo um único caso pode fazer com que uma nova regra a ser criada. Se aumentar o valor para 5, os dados de treinamento seriam têm de conter, pelo menos, 5 casos que cumprem as mesmas condições.

5. **Taxa de aprendizagem**: Escreva um número entre 0 e 1 que define o tamanho de etapa ao aprendizado. A taxa de aprendizagem determina rápido ou devagar o aprendiz converge sobre a solução ideal. Se o tamanho de etapa é demasiado grande, poderá overshoot a solução ideal. Se o tamanho de etapa for demasiado pequeno, treinamento leva mais tempo para convergir sobre a melhor solução.

6. **Número de árvores construído**: Indica o número total de árvores de decisão para criar o ensemble. Ao criar árvores de decisões mais, pode potencialmente obter uma melhor cobertura, mas aumenta o tempo de treinamento.

    Este valor também controla o número de árvores apresentado quando visualizar o modelo preparado. Se pretender ver ou imprimir uma árvore de ingle, pode definir o valor como 1; No entanto, é produzido apenas uma árvore (a árvore com o conjunto inicial de parâmetros) e são efetuadas sem mais iterações.

7. **Propagação de números aleatórias**: Escreva um inteiro não negativo opcional para utilizar como o valor de seed aleatório. Especificar uma semente garante a capacidade de reprodução nas execuções que têm os mesmos dados e parâmetros.

    Por predefinição, o seed aleatório está definido como 0, o que significa que o valor semente inicial é obtido a partir do relógio do sistema.
  
8. **Permitir que os níveis de categóricos desconhecidos**: Selecione esta opção para criar um grupo para os valores de desconhecido em conjuntos de formação e validação. Se desmarcar esta opção, o modelo pode aceitar apenas os valores que estão contidos nos dados de treinamento. O modelo pode ser menos preciso para os valores conhecidos, mas ele pode fornecer a obter melhores predições para novos valores (desconhecidos).

9. Adicione um conjunto de dados de treinamento e um dos módulos de treinamento:

    - Se definir **modo de instrutor de criação** a opção de **único parâmetro**, utilize o [Train Model](train-model.md) módulo.  
  
    

10. Execute a experimentação.  
  
### <a name="results"></a>Resultados

Após a conclusão do treinamento:

+ Para ver a árvore que foi criada em cada iteração, com o botão direito a saída do [Train Model](train-model.md) módulo e selecione **Visualize**.
  
     Clique em cada árvore para desagregar as divisões e ver as regras para cada nó.  

+ Para utilizar o modelo para a classificação, ligá-la à [modelo de pontuação](./score-model.md), para prever valores para obter exemplos de entrada novo.

+ Para guardar um instantâneo do modelo preparado, com o botão direito a **modelo Trained** saída do módulo de treinamento e selecione **guardar como**. A cópia do modelo treinado que deve guardar não é atualizada em sucessivas execuções da experimentação.

## <a name="next-steps"></a>Passos Seguintes

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço Azure Machine Learning. 