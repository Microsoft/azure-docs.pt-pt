---
title: 'Two-Class Perceptron Médio: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo Perceptron averaged Two-Class no designer para criar um classificador binário.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: f1161dc99b84026bcc830c08b63b95d0bf3b0994
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93421146"
---
# <a name="two-class-averaged-perceptron-module"></a>Two-Class módulo perceptron médio

Este artigo descreve um módulo no designer de Aprendizagem automática Azure.

Utilize este módulo para criar um modelo de aprendizagem automática baseado no algoritmo perceptron médio.  
  
Este algoritmo de classificação é um método de aprendizagem supervisionado, e requer um *conjunto de dados marcado*, que inclui uma coluna de etiqueta. Pode treinar o modelo fornecendo o modelo e o conjunto de dados marcados como uma entrada para [o Modelo de Comboio.](./train-model.md) O modelo treinado pode então ser usado para prever valores para os novos exemplos de entrada.  

### <a name="about-averaged-perceptron-models"></a>Sobre modelos perceptron médios

O *método perceptron médio* é uma versão antiga e simples de uma rede neural. Nesta abordagem, as entradas são classificadas em várias saídas possíveis com base numa função linear, e depois combinadas com um conjunto de pesos que são derivados do vetor de recurso - daí o nome "perceptron".

Os modelos perceptron mais simples são adequados para aprender padrões linearmente separáveis, enquanto as redes neurais (especialmente redes neuronais profundas) podem modelar limites de classe mais complexos. No entanto, os perceptrons são mais rápidos, e como processam casos em série, os perceptrons podem ser usados com treino contínuo.

## <a name="how-to-configure-two-class-averaged-perceptron"></a>Como configurar Two-Class Perceptron Médio

1.  Adicione o módulo **Perceptron average de duas classes** ao seu oleoduto.  

2.  Especifique como pretende que o modelo seja treinado, definindo a opção **modo de formação Create.**  
  
    -   **Parâmetro único**: Se souber como pretende configurar o modelo, forneça um conjunto específico de valores como argumentos.

    -   **Intervalo de parâmetros**: Selecione esta opção se não tiver a certeza dos melhores parâmetros e pretender fazer uma varredura de parâmetros. Selecione uma gama de valores para iterar e o [Tune Model Hyperparameters](tune-model-hyperparameters.md) itera sobre todas as combinações possíveis das configurações fornecidas para determinar os hiperparmetros que produzem os melhores resultados.  
  
3.  Para **a taxa de aprendizagem,** especifique um valor para a taxa de *aprendizagem.* Os valores da taxa de aprendizagem controlam o tamanho do passo que é usado na descida do gradiente estocástico cada vez que o modelo é testado e corrigido.
  
     Ao tornar a taxa menor, testa o modelo com mais frequência, correndo o risco de ficar preso num planalto local. Ao aumentar o passo, pode convergir mais rápido, correndo o risco de ultrapassar o verdadeiro minima.
  
4.  Para **o número máximo de iterações,** digite o número de vezes que pretende que o algoritmo examine os dados de treino.  
  
     Parar cedo muitas vezes proporciona uma melhor generalização. Aumentar o número de iterações melhora a adaptação, correndo o risco de sobremontagem.
  
5.  Para **sementes de número aleatório,** digite opcionalmente um valor inteiro para usar como semente. Recomenda-se a utilização de uma semente se pretender garantir a reprodutibilidade do gasoduto através de percursos.  
  
1.  Ligue um conjunto de dados de treino e treine o modelo:

    + Se definir **Criar o modo de treinador** para single **parâmetro,** conecte um conjunto de dados marcado e o módulo Modelo de [Comboio.](train-model.md)  
  
    + Se definir **Criar modo de treinador** para intervalo de **parâmetros,** ligue um conjunto de dados marcado e treine o modelo utilizando [hiperparmetros do modelo de sintonização](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Se passar uma gama de parâmetros para [o Modelo de Comboio,](train-model.md)utiliza apenas o valor predefinido na lista de parâmetros únicos.  
    > 
    > Se passar um único conjunto de valores de parâmetros para o módulo [Tune Model Hyperparameters,](tune-model-hyperparameters.md) quando espera uma gama de definições para cada parâmetro, ignora os valores e utiliza os valores predefinidos para o aluno.  
    > 
    > Se selecionar a opção De Alcance de **Parâmetros** e introduzir um único valor para qualquer parâmetro, esse valor único especificado é utilizado ao longo da varredura, mesmo que outros parâmetros se alterem numa gama de valores.




## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 