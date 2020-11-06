---
title: Preparar um Modelo do PyTorch
titleSuffix: Azure Machine Learning
description: Utilize o módulo Train Pytorch Models no designer Azure Machine Learning para treinar modelos de raiz ou afinar os modelos existentes.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/26/2020
ms.openlocfilehash: 2d88069f33995bdbe9dd479afe9a4e72ab9939b6
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93420670"
---
# <a name="train-pytorch-model"></a>Preparar um Modelo do PyTorch

Este artigo descreve como usar o módulo **Train Pytorch Model** em Azure Machine Learning designer para treinar modelos de pytorch como o DenseNet. A formação ocorre depois de definir um modelo e definir os seus parâmetros, e requer dados rotulados. 

## <a name="how-to-use-train-pytorch-model"></a>Como usar o modelo train pytorch 

1. Adicione o módulo [DenseNet](densenet.md) ou [ResNet](resnet.md) ao seu projeto de pipeline no designer.

2. Adicione o módulo **modelo Train Pytorch** ao oleoduto. Pode encontrar este módulo na categoria **Model Training.** Expanda **o Comboio** e, em seguida, arraste o módulo **Modelo Train Pytorch** para o seu oleoduto.

   > [!NOTE]
   > O módulo **modelo train Pytorch** é melhor executado no cálculo do tipo **GPU** para um conjunto de dados grande, caso contrário o seu pipeline falhará. Pode selecionar o cálculo para módulo específico no painel direito do módulo, definindo **Utilizar outro alvo de computação**.

3.  Na entrada esquerda, fixe um modelo destreinado. Anexar o conjunto de dados de formação e o conjunto de dados de validação à entrada média e direita do **Modelo Train Pytorch**.

    Para um modelo destreinado, deve ser um modelo de pytorch como o DenseNet; caso contrário, será lançado um 'InvalidModelDirectoryError'.

    Para o conjunto de dados, o conjunto de dados de formação deve ser um diretório de imagem rotulado. Consulte o **Convert to Image Directory** para saber como obter um diretório de imagem rotulado. Se não estiver rotulado, será lançado um 'NotLabeledDatasetError'.

    O conjunto de dados de formação e o conjunto de dados de validação têm as mesmas categorias de etiquetas, caso contrário será lançado um InvalidDatasetError.

4.  Para **épocas** , especifique quantas épocas gostaria de treinar. Todo o conjunto de dados será iterado em todas as épocas, por defeito 5.

5.  Para **o tamanho do lote** , especifique quantas instâncias treinar num lote, por defeito 16.

6.  Para **a taxa de aprendizagem,** especifique um valor para a taxa de *aprendizagem.* Os valores da taxa de aprendizagem controlam o tamanho do passo que é usado no optimizador como sgd cada vez que o modelo é testado e corrigido.

    Ao tornar a taxa menor, testa o modelo com mais frequência, correndo o risco de ficar preso num planalto local. Ao aumentar o passo, pode convergir mais rápido, correndo o risco de ultrapassar o verdadeiro minima. por defeito 0.001.

7.  Para **sementes aleatórias,** digite opcionalmente um valor inteiro para usar como semente. Recomenda-se a utilização de uma semente se pretender garantir a reprodutibilidade da experiência através de corridas.

8.  Para **Patience** , especifique quantas épocas para parar cedo o treino se a perda de validação não diminuir consecutivamente. por defeito 3.

9.  Envie o oleoduto. Se o seu conjunto de dados tiver um tamanho maior, levará algum tempo e recomenda-se a computação gpu.

## <a name="results"></a>Resultados

Após a execução do gasoduto, para utilizar o modelo para pontuação, ligue o [Modelo De Pytorch de Comboio](train-pytorch-model.md) ao Modelo de Imagem de [Pontuação,](score-image-model.md)para prever valores para novos exemplos de entrada.

## <a name="technical-notes"></a>Notas técnicas
###  <a name="expected-inputs"></a>Entradas esperadas  

| Nome               | Tipo                    | Description                              |
| ------------------ | ----------------------- | ---------------------------------------- |
| Modelo destreinado    | Não treinadoModelDirect | Modelo destreinado, requer pytorch         |
| Conjunto de dados de formação   | ImageDirectory          | Conjunto de dados de formação                         |
| Conjunto de dados de validação | ImageDirectory          | Conjunto de dados de validação para avaliação em cada época |

###  <a name="module-parameters"></a>Parâmetros do módulo  

| Name          | Intervalo            | Tipo    | Predefinição | Description                              |
| ------------- | ---------------- | ------- | ------- | ---------------------------------------- |
| Épocas        | >0               | Número inteiro | 5       | Selecione a coluna que contém a etiqueta ou coluna de resultados |
| Tamanho do lote    | >0               | Número inteiro | 16      | Quantas instâncias para treinar num lote   |
| Taxa de aprendizagem | >=duplo. Epsilon | Float   | 0.001   | A taxa de aprendizagem inicial para o otimizador de descida de gradiente estocástico. |
| Sementes aleatórias   | Qualquer              | Número inteiro | 1       | A semente para o gerador de números aleatórios usado pelo modelo. |
| Paciência      | >0               | Número inteiro | 3       | Quantas épocas para parar o treino precoce   |

###  <a name="outputs"></a>Saídas  

| Nome          | Tipo           | Description   |
| ------------- | -------------- | ------------- |
| Modelo treinado | ModelDirectory | Modelo treinado |

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 



