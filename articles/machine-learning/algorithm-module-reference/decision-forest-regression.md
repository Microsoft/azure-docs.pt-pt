---
title: 'Regressão de floresta de decisão: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo de regressão de floresta de decisão em Azure Machine Learning para criar um modelo de regressão com base em uma Ensemble de árvores de decisão.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 3c412937e59927a9f3f3218b0da3382f0b67c230
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152385"
---
# <a name="decision-forest-regression-module"></a>Módulo de regressão de floresta de decisão

Este artigo descreve um módulo em Azure Machine Learning designer (pré-visualização).

Use este módulo para criar um modelo de regressão com base em uma Ensemble de árvores de decisão.

Depois de configurar o modelo, deve treinar o modelo utilizando um conjunto de dados rotulado e o módulo [Modelo do Comboio.](./train-model.md) O modelo treinado pode então ser usado para fazer previsões. 

## <a name="how-it-works"></a>Como funciona

As árvores de decisão são modelos não paramétricos que executam uma sequência de testes simples para cada instância, atravessando uma estrutura de dados de árvore binária até que um nó folha (decisão) seja atingido.

As árvores de decisão têm essas vantagens:

- Eles são eficientes no uso de memória e computação durante o treinamento e a previsão.

- Eles podem representar limites de decisão não lineares.

- Eles executam seleção e classificação de recursos integrados e são resilientes na presença de recursos ruidosas.

Esse modelo de regressão consiste em um Ensemble de árvores de decisão. Cada árvore em uma floresta de decisão de regressão gera uma distribuição gaussiana como uma previsão. Uma agregação é executada sobre o Ensemble de árvores para encontrar uma distribuição gaussiana mais próxima da distribuição combinada para todas as árvores no modelo.

Para obter mais informações sobre o quadro teórico deste algoritmo e a sua implementação, consulte este artigo: [Decision Forests: A Unified Framework for Classification, Regression, Density Estimation, Manifold Learning e Semi-Supervised Learning](https://www.microsoft.com/en-us/research/publication/decision-forests-a-unified-framework-for-classification-regression-density-estimation-manifold-learning-and-semi-supervised-learning/?from=http%3A%2F%2Fresearch.microsoft.com%2Fapps%2Fpubs%2Fdefault.aspx%3Fid%3D158806#)

## <a name="how-to-configure-decision-forest-regression-model"></a>Como configurar o modelo de regressão de floresta de decisão

1. Adicione o módulo de **regressão** da Floresta de Decisão ao gasoduto. Pode encontrar o módulo no designer em **Machine Learning**, **Modelo Inicializar**e **Regressão.**

2. Abra as propriedades do módulo, e para **o método de reamostragem,** escolha o método utilizado para criar as árvores individuais.  Pode escolher entre **Bagging** ou **Replicate**.

    - **Bagging**: Bagging também é chamado de *agregação de botas.* Cada árvore em uma floresta de decisão de regressão gera uma distribuição gaussiana por meio de previsão. A agregação é para localizar um gaussiano cujos primeiros dois momentos correspondem ao momento da mistura de distribuições gaussianas dadas pela combinação de todas as distribuições retornadas por árvores individuais.

         Para mais informações, consulte a entrada da Wikipedia para a [gregação de Bootstrap](https://wikipedia.org/wiki/Bootstrap_aggregating).

    - **Replicação**: Na replicação, cada árvore é treinada exatamente com os mesmos dados de entrada. A determinação de qual predicado de divisão é usado para cada nó de árvore permanece aleatório e as árvores serão diversificadas.

         Para obter mais informações sobre o processo de formação com a opção **Replicate,** consulte [Decision Forests for Computer Vision e Medical Image Analysis. Criminisi e J. Shotton. Springer 2013. . .](https://research.microsoft.com/projects/decisionforests/)

3. Especifique como pretende que o modelo seja treinado, definindo a opção modo **Criar.**

    - **Parâmetro único**

      Se você souber como deseja configurar o modelo, poderá fornecer um conjunto específico de valores como argumentos. Você pode ter aprendido esses valores por experimentação ou os recebeu como orientação.



4. Para **o número de árvores de decisão,** indicar o número total de árvores de decisão a criar no conjunto. Ao criar mais árvores de decisão, você pode potencialmente obter uma cobertura melhor, mas o tempo de treinamento aumentará.

    > [!TIP]
    > Esse valor também controla o número de árvores exibidas ao visualizar o modelo treinado. Se você quiser ver ou imprimir uma única árvore, poderá definir o valor como 1; no entanto, isso significa que apenas uma árvore será produzida (a árvore com o conjunto inicial de parâmetros) e nenhuma iteração adicional será executada.

5. Para **máxima profundidade das árvores de decisão,** digite um número para limitar a profundidade máxima de qualquer árvore de decisão. Aumentar a profundidade da árvore pode aumentar a precisão, com o risco de algum superajuste e um tempo de treinamento maior.

6. Para **o número de divisões aleatórias por nó,** digite o número de divisões a utilizar ao construir cada nó da árvore. Uma *divisão* significa que as características em cada nível da árvore (nó) são divididas aleatoriamente.

7. Para o **número mínimo de amostras por nó**de folha, indique o número mínimo de casos necessários para criar qualquer nó terminal (folha) numa árvore.

     Ao aumentar esse valor, você aumenta o limite para a criação de novas regras. Por exemplo, com o valor padrão de 1, mesmo um único caso pode fazer com que uma nova regra seja criada. Se você aumentar o valor para 5, os dados de treinamento precisarão conter pelo menos cinco casos que atendam às mesmas condições.


9. Ligue um conjunto de dados rotulado, selecione uma única coluna de etiquetas que não contenha mais de dois resultados e ligue-se ao [Modelo de Comboio](./train-model.md).

    - Se definir a opção **de modo de treinador** para um parâmetro **único,** treine o modelo utilizando o módulo [Modelo de Comboio.](./train-model.md)

   

10. Executar o pipeline.

### <a name="results"></a>Resultados

Após a conclusão do treinamento:

+ Para guardar uma imagem instantânea do modelo treinado, selecione o módulo de treino e, em seguida, mude para o separador **Saídas** no painel certo. Clique no **modelo de registo**de ícones .  Você pode encontrar o modelo salvo como um módulo na árvore de módulo. 

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 