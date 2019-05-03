---
title: 'Regressão de floresta de decisão: Referência do módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como utilizar o módulo de regressão de floresta de decisão no serviço Azure Machine Learning para criar um modelo de regressão com base num ensemble das árvores de decisão.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 67016582149824c8deb83b54102190a57bd19383
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028804"
---
# <a name="decision-forest-regression-module"></a>Módulo de regressão de floresta de decisão

Este artigo descreve um módulo da interface visual (pré-visualização) para o serviço Azure Machine Learning.

Utilize este módulo para criar um modelo de regressão com base num ensemble das árvores de decisão.

Depois de ter configurado o modelo, deve preparar o modelo utilizando um conjunto de dados com nome e o [modelo de formação](./train-model.md) módulo.  O modelo preparado, em seguida, pode ser utilizado para fazer previsões. 

## <a name="how-it-works"></a>Como funciona

Árvores de decisões são modelos não paramétrica que realizam uma seqüência de testes simples para cada instância, percorrer uma estrutura de dados de árvore binária até que um nó de folha (decisão) seja atingido.

Árvores de decisão de ter essas vantagens:

- Eles são eficientes em computação e o uso de memória durante a formação e predição.

- Podem representar os limites de decisão de não-lineares.

- Eles executam classificação e seleção de funcionalidades integradas e são resilientes na presença de funcionalidades ruidosos.

Este modelo de regressão consiste num ensemble das árvores de decisão. Cada árvore numa floresta de decisão de regressão produz uma distribuição Gaussiana como uma predição. Uma agregação é efetuada através de ensemble de árvores para encontrar uma distribuição Gaussiana mais próximo para a distribuição combinada para todas as árvores no modelo.

Para obter mais informações sobre a arquitetura teórica desse algoritmo e sua implementação, consulte este artigo: [Florestas de decisão: Uma arquitetura unificada para classificação, regressão, densidade estimativa, aprendizagem Manifold e aprendizagem supervisionada semiestruturados ](https://www.microsoft.com/en-us/research/publication/decision-forests-a-unified-framework-for-classification-regression-density-estimation-manifold-learning-and-semi-supervised-learning/?from=http%3A%2F%2Fresearch.microsoft.com%2Fapps%2Fpubs%2Fdefault.aspx%3Fid%3D158806#)

## <a name="how-to-configure-decision-forest-regression-model"></a>Como configurar o modelo de regressão de floresta de decisão

1. Adicionar a **regressão de floresta de decisão** módulo para a experimentação. Pode encontrar o módulo na interface do sob **Machine Learning**, **inicializar modelo**, e **regressão**.

2. Abra as propriedades do módulo e para **Resampling método**, escolha o método utilizado para criar as árvores individuais.  Pode escolher entre **Bagging** ou **replicar**.

    - **Bagging**: Também é denominado bagging *agregar bootstrap*. Cada árvore numa floresta de decisão de regressão produz uma distribuição Gaussiana por meio de predição. A agregação é encontrar uma Gaussiana cujos duas primeiras momentos correspondem os momentos de mistura de Gaussians tendo em conta ao combinar todos os Gaussians devolvidos pelo árvores individuais.

         Para obter mais informações, consulte a entrada da Wikipedia [agregar Bootstrap](https://wikipedia.org/wiki/Bootstrap_aggregating).

    - **Replicar**: Na replicação, cada árvore é preparado no exatamente os mesmos dados de entrada. A determinação de qual dividir o predicado é utilizado para cada nó de árvore permanece aleatória e as árvores será diversificadas.

         Para obter mais informações sobre o processo de treinamento com o **replicar** opção, consulte [florestas de decisão para imagem digitalizada e análise de imagens médicas. Criminisi e J. Shotton. Springer 2013.](http://research.microsoft.com/projects/decisionforests/).

3. Especifique como pretende que o modelo de formação, definindo a **modo de instrutor de criação** opção.

    - **Parâmetro único**

      Se souber como pretende configurar o modelo, pode fornecer um conjunto específico de valores como argumentos. Poderá ter aprendido estes valores por experimentação ou recebido-los como orientação.



4. Para **número de árvores de decisão**, indicar o número total de árvores de decisão para criar o ensemble. Ao criar árvores de decisões mais, pode potencialmente obter uma melhor cobertura, mas irá aumentar o tempo de treinamento.

    > [!TIP]
    > Este valor também controla o número de árvores apresentado quando visualizar o modelo preparado. Se pretender ver ou imprimir uma única árvore, pode definir o valor como 1; No entanto, isso significa que apenas uma árvore será produzido (a árvore com o conjunto inicial de parâmetros) e não mais iterações serão executadas.

5. Para **profundidade máximo das árvores de decisão**, escreva um número para limitar a profundidade máxima de qualquer árvore de decisão. Aumentando a profundidade da árvore pode aumentar a precisão, sob alguma overfitting e maior tempo de treinamento.

6. Para **número de divisões aleatórios por nó de**, escreva o número de divisões de usar para criar cada nó da árvore. R *dividir* significa que os recursos em cada nível da árvore (node) é divididas aleatoriamente.

7. Para **número mínimo de exemplos por nó folha**, indicar o número mínimo de cenários que são necessários para criar qualquer nó de terminal (folha) numa árvore.

     Ao aumentar este valor, aumentar o limiar para a criação de novas regras. Por exemplo, com o valor predefinido de 1, até mesmo um único caso pode fazer com que uma nova regra a ser criada. Se aumentar o valor para 5, os dados de treinamento seriam têm de conter, pelo menos, cinco casos que cumprem as mesmas condições.


9. Ligar um conjunto de dados com nome, selecione uma coluna de etiqueta única que contém os resultados de mais do que dois e ligar à [Train Model](./train-model.md).

    - Se definir **modo de instrutor de criação** a opção de **único parâmetro**, preparar o modelo utilizando o [modelo de formação](./train-model.md) módulo.

   

10. Execute a experimentação.

### <a name="results"></a>Resultados

Após a conclusão do treinamento:

+ Para ver a árvore que foi criada em cada iteração, a saída do módulo de treinamento com o botão direito e selecione **Visualize**.

+ Para ver as regras para cada nó, clique em cada árvore e desagregar para as divisões.

+ Para guardar um instantâneo do modelo preparado, com o botão direito a saída do módulo de treinamento e selecione **guardar como modelo preparado**. Esta cópia do modelo não é atualizada em sucessivas execuções da experimentação. 

## <a name="next-steps"></a>Passos Seguintes

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço Azure Machine Learning. 