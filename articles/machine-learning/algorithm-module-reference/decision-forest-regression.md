---
title: 'Regressão de floresta de decisão: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo de regressão de floresta de decisão em Azure Machine Learning para criar um modelo de regressão com base em uma Ensemble de árvores de decisão.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: d930a6d856c6608e7792ce8ef3204b39aba0028a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497965"
---
# <a name="decision-forest-regression-module"></a>Módulo de regressão de floresta de decisão

Este artigo descreve um módulo no designer de Azure Machine Learning (versão prévia).

Use este módulo para criar um modelo de regressão com base em uma Ensemble de árvores de decisão.

Depois de configurar o modelo, você deve treinar o modelo usando um DataSet rotulado e o módulo [treinar modelo](./train-model.md) . O modelo treinado pode então ser usado para fazer previsões. 

## <a name="how-it-works"></a>Como funciona

As árvores de decisão são modelos não paramétricos que executam uma sequência de testes simples para cada instância, atravessando uma estrutura de dados de árvore binária até que um nó folha (decisão) seja atingido.

As árvores de decisão têm essas vantagens:

- Eles são eficientes no uso de memória e computação durante o treinamento e a previsão.

- Eles podem representar limites de decisão não lineares.

- Eles executam seleção e classificação de recursos integrados e são resilientes na presença de recursos ruidosas.

Esse modelo de regressão consiste em um Ensemble de árvores de decisão. Cada árvore em uma floresta de decisão de regressão gera uma distribuição gaussiana como uma previsão. Uma agregação é executada sobre o Ensemble de árvores para encontrar uma distribuição gaussiana mais próxima da distribuição combinada para todas as árvores no modelo.

Para obter mais informações sobre a estrutura teórica para este algoritmo e sua implementação, consulte este artigo: [florestas de decisão: uma estrutura unificada para classificação, regressão, estimativa de densidade, aprendizado de diversa e aprendizado Semisupervisionado ](https://www.microsoft.com/en-us/research/publication/decision-forests-a-unified-framework-for-classification-regression-density-estimation-manifold-learning-and-semi-supervised-learning/?from=http%3A%2F%2Fresearch.microsoft.com%2Fapps%2Fpubs%2Fdefault.aspx%3Fid%3D158806#)

## <a name="how-to-configure-decision-forest-regression-model"></a>Como configurar o modelo de regressão de floresta de decisão

1. Adicione o módulo **regressão de floresta de decisão** ao pipeline. Você pode encontrar o módulo no designer em **Machine Learning**, **inicializar modelo**e **regressão**.

2. Abra as propriedades do módulo e, para o **método de reamostragem**, escolha o método usado para criar as árvores individuais.  Você pode escolher entre **bagging** ou **replicar**.

    - **Bagging**: bagging também é chamado de *agregação de Bootstrap*. Cada árvore em uma floresta de decisão de regressão gera uma distribuição gaussiana por meio de previsão. A agregação é para localizar um gaussiano cujos primeiros dois momentos correspondem ao momento da mistura de distribuições gaussianas dadas pela combinação de todas as distribuições retornadas por árvores individuais.

         Para obter mais informações, consulte a entrada da Wikipédia para [agregação de Bootstrap](https://wikipedia.org/wiki/Bootstrap_aggregating).

    - **Replicate**: na replicação, cada árvore é treinada exatamente com os mesmos dados de entrada. A determinação de qual predicado de divisão é usado para cada nó de árvore permanece aleatório e as árvores serão diversificadas.

         Para obter mais informações sobre o processo de treinamento com a opção **replicar** , consulte [florestas de decisão para pesquisa Visual computacional e análise de imagem médica. Criminisi e J. Shotton. Springer 2013.](https://research.microsoft.com/projects/decisionforests/).

3. Especifique como você deseja que o modelo seja treinado, definindo a opção **criar modo de instrutor** .

    - **Parâmetro único**

      Se você souber como deseja configurar o modelo, poderá fornecer um conjunto específico de valores como argumentos. Você pode ter aprendido esses valores por experimentação ou os recebeu como orientação.



4. Para o **número de árvores de decisão**, indique o número total de árvores de decisão a serem criadas no Ensemble. Ao criar mais árvores de decisão, você pode potencialmente obter uma cobertura melhor, mas o tempo de treinamento aumentará.

    > [!TIP]
    > Esse valor também controla o número de árvores exibidas ao visualizar o modelo treinado. Se você quiser ver ou imprimir uma única árvore, poderá definir o valor como 1; no entanto, isso significa que apenas uma árvore será produzida (a árvore com o conjunto inicial de parâmetros) e nenhuma iteração adicional será executada.

5. Para obter a **profundidade máxima das árvores de decisão**, digite um número para limitar a profundidade máxima de qualquer árvore de decisão. Aumentar a profundidade da árvore pode aumentar a precisão, com o risco de algum superajuste e um tempo de treinamento maior.

6. Para o **número de divisões aleatórias por nó**, digite o número de divisões a serem usadas ao compilar cada nó da árvore. Uma *divisão* significa que os recursos em cada nível da árvore (nó) são divididos aleatoriamente.

7. Para o **número mínimo de amostras por nó folha**, indique o número mínimo de casos necessários para criar qualquer nó de terminal (folha) em uma árvore.

     Ao aumentar esse valor, você aumenta o limite para a criação de novas regras. Por exemplo, com o valor padrão de 1, mesmo um único caso pode fazer com que uma nova regra seja criada. Se você aumentar o valor para 5, os dados de treinamento precisarão conter pelo menos cinco casos que atendam às mesmas condições.


9. Conecte um conjunto de informações rotulado, selecione uma única coluna de rótulo contendo no máximo dois resultados e conecte-se ao [modelo de treinamento](./train-model.md).

    - Se você definir a opção **criar modo de instrutor** como **parâmetro único**, treine o modelo usando o módulo [treinar modelo](./train-model.md) .

   

10. Executar o pipeline.

### <a name="results"></a>Resultados

Após a conclusão do treinamento:

+ Para ver a árvore que foi criada em cada iteração, clique com o botão direito do mouse na saída do módulo de treinamento e selecione **Visualizar**.

+ Para ver as regras para cada nó, clique em cada árvore e faça uma busca detalhada nas divisões.

+ Para salvar um instantâneo do modelo treinado, clique com o botão direito do mouse na saída do módulo de treinamento e selecione **salvar como modelo treinado**. Essa cópia do modelo não é atualizada em execuções sucessivas do pipeline. 

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 