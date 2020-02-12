---
title: 'Regressão de floresta de decisão: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo perceptron médio de duas classes no Azure Machine Learning para criar um modelo de aprendizado de máquina com base no algoritmo de média de perceptron.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 352881d2fb4ddd8ce438f1bca713513b65f50f40
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153762"
---
# <a name="two-class-averaged-perceptron-module"></a>Módulo perceptron médio de duas classes

Este artigo descreve um módulo em Azure Machine Learning designer (pré-visualização).

Use este módulo para criar um modelo de aprendizado de máquina com base no algoritmo médio de perceptron.  
  
Este algoritmo de classificação é um método de aprendizagem supervisionado, e requer um conjunto de *dados marcado,* que inclui uma coluna de etiquetas. Pode treinar o modelo fornecendo o modelo e o conjunto de dados marcadocomo entrada para [o Modelo de Comboio](./train-model.md). O modelo treinado pode então ser usado para prever valores para os novos exemplos de entrada.  

### <a name="about-averaged-perceptron-models"></a>Sobre os modelos de média de perceptron

O *método perceptron médio* é uma versão antiga e simples de uma rede neural. Nessa abordagem, as entradas são classificadas em várias saídas possíveis com base em uma função linear e, em seguida, combinadas com um conjunto de pesos que são derivados do vetor de recurso — por isso o nome "perceptron".

Os modelos perceptron mais simples são adequados para aprender padrões separáveis lineares, enquanto as redes neurais (especialmente redes neurais profundas) podem modelar limites de classe mais complexos. No entanto, o perceptrons é mais rápido e, como eles processam casos em série, o perceptrons pode ser usado com treinamento contínuo.

## <a name="how-to-configure-two-class-averaged-perceptron"></a>Como configurar o perceptron médio de duas classes

1.  Adicione o módulo **Perceptron médio de duas classes** ao seu pipeline.  

2.  Especifique como pretende que o modelo seja treinado, definindo a opção modo **Criar.**  
  
    -   **Parâmetro único**: Se souber como pretende configurar o modelo, forneça um conjunto específico de valores como argumentos.
  
3.  Para **a taxa de aprendizagem,** especifique um valor para a taxa de *aprendizagem*. Os valores de taxa de aprendizagem controlam o tamanho da etapa usada em estocástico Grad descendente cada vez que o modelo é testado e corrigido.
  
     Ao tornar a taxa menor, você testa o modelo com mais frequência, com o risco que você pode ficar preso em um limite local. Ao aumentar a etapa, você pode convergir mais rapidamente, com o risco de sobretornar o verdadeiro mínimo.
  
4.  Para **o número máximo de iterações,** escreva o número de vezes que pretende que o algoritmo examine os dados de treino.  
  
     A interrupção inicial geralmente fornece melhor generalização. Aumentar o número de iterações melhora o ajuste, com o risco de superajuste.
  
5.  Para **sementes aleatórias,** digite opcionalmente um valor inteiro para usar como semente. Usar uma semente é recomendado se você quiser garantir reprodução do pipeline entre execuções.  
  
1.  Conecte um conjunto de e um dos módulos de treinamento:
  
    -   Se definir o modo Criar modo **de treinador** para um parâmetro **único,** utilize o módulo Modelo de [Comboio.](train-model.md)




## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 