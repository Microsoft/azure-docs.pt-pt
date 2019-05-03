---
title: 'Regressão de floresta de decisão: Referência do módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como utilizar o módulo de Perceptron apresentou uma média de duas classes no serviço Azure Machine Learning para criar um modelo de machine learning com base no algoritmo perceptron média.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: f0fec525ed87f91cf102053383b2934aac4b71c0
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029239"
---
# <a name="two-class-averaged-perceptron-module"></a>Módulo de Perceptron apresentou uma média de duas classes

Este artigo descreve um módulo da interface visual (pré-visualização) para o serviço Azure Machine Learning.

Utilize este módulo para criar um modelo de machine learning com base no algoritmo perceptron média.  
  
Esse algoritmo de classificação é um método de aprendizagem supervisionada e requer uma *etiquetados de conjunto de dados*, que inclui uma coluna de etiqueta. Pode preparar o modelo, fornecendo o modelo e o conjunto de dados marcado como entrada para [modelo de formação](./train-model.md). O modelo preparado, em seguida, pode ser utilizado para prever valores para os exemplos de entrada novo.  

### <a name="about-averaged-perceptron-models"></a>Acerca dos modelos de perceptron média

O *apresentou uma média de método perceptron* é uma versão cedo e simple de uma rede neural. Nesta abordagem, as entradas são classificadas em várias saídas possíveis com base numa função linear e, em seguida, combinados com um conjunto de pesos que são derivados do vetor de funcionalidade —, por conseguinte, o nome "perceptron."

Os modelos de perceptron mais simples são adequados para aprender os padrões de maneira linear separados, ao passo que as redes neurais (especialmente redes neurais profundas) pode modelar os limites de classe mais complexos. No entanto, perceptrons são mais rápidos e porque eles processam casos em série, perceptrons pode ser utilizado com a formação contínua.

## <a name="how-to-configure-two-class-averaged-perceptron"></a>Como configurar Perceptron apresentou uma média de duas classes

1.  Adicionar a **Perceptron apresentou uma média de duas classes** módulo à sua experimentação.  

2.  Especifique como pretende que o modelo de formação, definindo a **modo de instrutor de criação** opção.  
  
    -   **Único parâmetro**: Se souber como pretende configurar o modelo, fornecem um conjunto específico de valores como argumentos.
  
3.  Para **taxa de aprendizagem**, especifique um valor para o *taxa de aprendizagem*. A taxa de aprendizagem os valores de controle o tamanho do passo de utilizado no stochastic gradient descendente gradação sempre que o modelo é testado e corrigido.
  
     Ao fazer a taxa de menor, testar o modelo com mais freqüência, com o risco de que pode ficar preso num patamar local. Ao fazer o passo maior, pode convergir com mais rapidez, sob as verdadeiras mínimas errar o alvo.
  
4.  Para **número máximo de iterações**, escreva o número de vezes que pretende que o algoritmo para examinar os dados de treinamento.  
  
     A parar no início, muitas vezes, fornece o melhor generalização. Aumentar o número de iterações melhora o ajuste, sob overfitting.
  
5.  Para **seed número aleatório**, opcionalmente, escreva um valor inteiro para utilizar como a semente. É recomendado utilizar uma semente se quiser garantir a capacidade de reprodução da experimentação em execução.  
  
1.  Ligue-se de um conjunto de dados de treinamento e um dos módulos de treinamento:
  
    -   Se definir **modo de instrutor de criação** para **único parâmetro**, utilize o [Train Model](train-model.md) módulo.

## <a name="results"></a>Resultados

Após a conclusão do treinamento:

+ Para ver um resumo dos parâmetros do modelo, juntamente com os pesos de funcionalidade aprendidos treinamento, com o botão direito a saída de [Train Model](./train-model.md).


## <a name="next-steps"></a>Passos Seguintes

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço Azure Machine Learning. 