---
title: 'Decision Forest Regression: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo Perceptron Médio de Duas Classes em Azure Machine Learning para criar um modelo de aprendizagem automática baseado no algoritmo perceptron médio.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 73e23dd7d350ea63e9fd8b933a525a9d8aad9e3e
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77920778"
---
# <a name="two-class-averaged-perceptron-module"></a>Módulo perceptron médio de duas classes

Este artigo descreve um módulo em Azure Machine Learning designer (pré-visualização).

Utilize este módulo para criar um modelo de aprendizagem automática baseado no algoritmo perceptron médio.  
  
Este algoritmo de classificação é um método de aprendizagem supervisionado, e requer um conjunto de *dados marcado,* que inclui uma coluna de etiquetas. Pode treinar o modelo fornecendo o modelo e o conjunto de dados marcadocomo entrada para [o Modelo de Comboio](./train-model.md). O modelo treinado pode então ser usado para prever valores para os novos exemplos de entrada.  

### <a name="about-averaged-perceptron-models"></a>Sobre modelos perceptron médios

O *método perceptron médio* é uma versão antiga e simples de uma rede neural. Nesta abordagem, as inputs são classificadas em várias saídas possíveis com base numa função linear, e depois combinadas com um conjunto de pesos que são derivados do vetor de características — daí o nome "perceptron".

Os modelos perceptron mais simples são adequados para aprender padrões liparáveis, enquanto redes neurais (especialmente redes neuronais profundas) podem modelar limites de classe mais complexos. No entanto, as perceptronas são mais rápidas, e como processam casos em série, as perceptronas podem ser usadas com treino contínuo.

## <a name="how-to-configure-two-class-averaged-perceptron"></a>Como configurar perceptron médio de duas classes

1.  Adicione o módulo **Perceptron médio de duas classes** ao seu pipeline.  

2.  Especifique como pretende que o modelo seja treinado, definindo a opção modo **Criar.**  
  
    -   **Parâmetro único**: Se souber como pretende configurar o modelo, forneça um conjunto específico de valores como argumentos.

    -   **Gama de parâmetros**: Selecione esta opção se não tiver a certeza dos melhores parâmetros e pretenda fazer uma varredura de parâmetros. Selecione uma gama de valores para iterar mais, e o [Modelo tune Hyperparâmetros](tune-model-hyperparameters.md) iterates sobre todas as combinações possíveis das definições que forneceu para determinar os hiperparâmetros que produzem os melhores resultados.  
  
3.  Para **a taxa de aprendizagem,** especifique um valor para a taxa de *aprendizagem*. Os valores da taxa de aprendizagem controlam o tamanho do passo que é usado na descida do gradiente estocástico cada vez que o modelo é testado e corrigido.
  
     Ao tornar a taxa mais pequena, testa-se o modelo com mais frequência, com o risco de ficar preso num planalto local. Ao fazer o passo maior, pode convergir mais rapidamente, com o risco de ultrapassar o verdadeiro minima.
  
4.  Para **o número máximo de iterações,** escreva o número de vezes que pretende que o algoritmo examine os dados de treino.  
  
     Parar cedo muitas vezes proporciona uma melhor generalização. Aumentar o número de iterações melhora a adaptação, com o risco de excesso de adaptação.
  
5.  Para **sementes aleatórias,** digite opcionalmente um valor inteiro para usar como semente. Recomenda-se a utilização de uma semente se pretender garantir a reprodutibilidade do gasoduto através de percursos.  
  
1.  Ligue um conjunto de dados de treino e um dos módulos de treino:
  
    -   Se definir o modo Criar modo **de treinador** para um parâmetro **único,** utilize o módulo Modelo de [Comboio.](train-model.md)




## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 