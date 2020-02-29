---
title: 'Decision Forest Regression: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo de regressão da Floresta de Decisão em Aprendizagem automática Azure para criar um modelo de regressão baseado num conjunto de árvores de decisão.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 6d326b3cb5a964e43ce77ee459533a4271d5ed73
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77919945"
---
# <a name="decision-forest-regression-module"></a>Módulo de Regressão Florestal de Decisão

Este artigo descreve um módulo em Azure Machine Learning designer (pré-visualização).

Utilize este módulo para criar um modelo de regressão baseado num conjunto de árvores de decisão.

Depois de configurar o modelo, deve treinar o modelo utilizando um conjunto de dados rotulado e o módulo [Modelo do Comboio.](./train-model.md) O modelo treinado pode então ser usado para fazer previsões. 

## <a name="how-it-works"></a>Como funciona

As árvores de decisão são modelos não paramétricos que realizam uma sequência de testes simples para cada caso, atravessando uma estrutura de dados de árvores binárias até que um nó de folha (decisão) seja alcançado.

As árvores de decisão têm estas vantagens:

- São eficientes tanto na computação como no uso da memória durante o treino e a previsão.

- Podem representar limites de decisão não lineares.

- Realizam seleção e classificação integradas de recursos e são resilientes na presença de características ruidosas.

Este modelo de regressão consiste num conjunto de árvores de decisão. Cada árvore numa decisão de regressão a floresta produz uma distribuição gaussiana como uma previsão. Uma agregação é realizada sobre o conjunto de árvores para encontrar uma distribuição gaussiana mais próxima da distribuição combinada para todas as árvores do modelo.

Para obter mais informações sobre o quadro teórico deste algoritmo e a sua implementação, consulte este artigo: [Decision Forests: A Unified Framework for Classification, Regression, Density Estimation, Manifold Learning e Semi-Supervised Learning](https://www.microsoft.com/en-us/research/publication/decision-forests-a-unified-framework-for-classification-regression-density-estimation-manifold-learning-and-semi-supervised-learning/?from=http%3A%2F%2Fresearch.microsoft.com%2Fapps%2Fpubs%2Fdefault.aspx%3Fid%3D158806#)

## <a name="how-to-configure-decision-forest-regression-model"></a>Como configurar o Modelo de Regressão da Floresta de Decisão

1. Adicione o módulo de **regressão** da Floresta de Decisão ao gasoduto. Pode encontrar o módulo no designer em **Machine Learning**, **Modelo Inicializar**e **Regressão.**

2. Abra as propriedades do módulo, e para **o método de reamostragem,** escolha o método utilizado para criar as árvores individuais.  Pode escolher entre **Bagging** ou **Replicate**.

    - **Bagging**: Bagging também é chamado de *agregação de botas.* Cada árvore numa decisão de regressão a floresta produz uma distribuição gaussiana como previsão. A agregação é encontrar um gaussiano cujos dois primeiros momentos correspondem aos momentos da mistura de distribuições gaussianas dadas combinando todas as distribuições devolvidas por árvores individuais.

         Para mais informações, consulte a entrada da Wikipedia para a [gregação de Bootstrap](https://wikipedia.org/wiki/Bootstrap_aggregating).

    - **Replicação**: Na replicação, cada árvore é treinada exatamente com os mesmos dados de entrada. A determinação de que o predicado dividido é usado para cada nó de árvore permanece aleatória e as árvores serão diversas.

         Para obter mais informações sobre o processo de formação com a opção **Replicate,** consulte [Decision Forests for Computer Vision e Medical Image Analysis. Criminisi e J. Shotton. Springer 2013. . .](https://research.microsoft.com/projects/decisionforests/)

3. Especifique como pretende que o modelo seja treinado, definindo a opção modo **Criar.**

    - **Parâmetro único**

      Se souber como pretende configurar o modelo, pode fornecer um conjunto específico de valores como argumentos. Pode ter aprendido estes valores por experimentação ou recebido como orientação.

    - **Gama de parâmetros**: Selecione esta opção se não tiver a certeza dos melhores parâmetros e pretenda fazer uma varredura de parâmetros. Selecione uma gama de valores para iterar mais, e o [Modelo tune Hyperparâmetros](tune-model-hyperparameters.md) iterates sobre todas as combinações possíveis das definições que forneceu para determinar os hiperparâmetros que produzem os melhores resultados. 



4. Para **o número de árvores de decisão,** indicar o número total de árvores de decisão a criar no conjunto. Ao criar mais árvores de decisão, pode potencialmente obter uma melhor cobertura, mas o tempo de treino aumentará.

    > [!TIP]
    > Este valor também controla o número de árvores exibidas ao visualizar o modelo treinado. se quiser ver ou imprimir uma única árvore, pode definir o valor para 1; no entanto, isto significa que apenas uma árvore será produzida (a árvore com o conjunto inicial de parâmetros) e não serão realizadas mais iterações.

5. Para **máxima profundidade das árvores de decisão,** digite um número para limitar a profundidade máxima de qualquer árvore de decisão. Aumentar a profundidade da árvore pode aumentar a precisão, com o risco de alguma sobremontagem e aumento do tempo de treino.

6. Para **o número de divisões aleatórias por nó,** digite o número de divisões a utilizar ao construir cada nó da árvore. Uma *divisão* significa que as características em cada nível da árvore (nó) são divididas aleatoriamente.

7. Para o **número mínimo de amostras por nó**de folha, indique o número mínimo de casos necessários para criar qualquer nó terminal (folha) numa árvore.

     Ao aumentar este valor, aumenta-se o limiar para a criação de novas regras. Por exemplo, com o valor padrão de 1, mesmo um único caso pode fazer com que uma nova regra seja criada. Se aumentar o valor para 5, os dados de formação teriam de conter pelo menos cinco casos que satisfaçam as mesmas condições.


9. Ligue um conjunto de dados rotulado, selecione uma única coluna de etiquetas que não contenha mais de dois resultados e ligue-se ao [Modelo de Comboio](./train-model.md).

    - Se definir a opção **de modo de treinador** para um parâmetro **único,** treine o modelo utilizando o módulo [Modelo de Comboio.](./train-model.md)

   

10. Executar o pipeline.

### <a name="results"></a>Resultados

Após o treino estar completo:

+ Para guardar uma imagem instantânea do modelo treinado, selecione o módulo de treino e, em seguida, mude para o separador **Saídas** no painel certo. Clique no **modelo de registo**de ícones .  Pode encontrar o modelo guardado como módulo na árvore do módulo. 

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 