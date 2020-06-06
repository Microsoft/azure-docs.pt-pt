---
title: ResNet
titleSuffix: Azure Machine Learning
description: Saiba como criar um modelo de classificação de imagem utilizando o algoritmo ResNet.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: ea8c13e134eceeb27bd064e794d46d711092a867
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/05/2020
ms.locfileid: "84450732"
---
# <a name="resnet"></a>ResNet

Este artigo descreve como usar o módulo **ResNet** no designer de aprendizagem automática Azure (pré-visualização), para criar um modelo de classificação de imagem usando o algoritmo ResNet..  

Este algoritmo de classificação é um método de aprendizagem supervisionado, e requer um conjunto de dados rotulado. Consulte o módulo [De Conversão para Diretório de Imagem](convert-to-image-directory.md) para obter mais informações sobre como obter um diretório de imagem rotulado. Pode treinar o modelo fornecendo um modelo e um diretório de imagem rotulado como entradas para [o Modelo Train Pytorch.](train-pytorch-model.md) O modelo treinado pode então ser utilizado para prever valores para os novos exemplos de entrada utilizando o [Modelo de Imagem de Pontuação](score-image-model.md).

### <a name="more-about-resnet"></a>Mais sobre a ResNet

Consulte [este artigo](https://pytorch.org/docs/stable/torchvision/models.html?highlight=resnext101_32x8d#torchvision.models.resnext101_32x8d) para obter mais detalhes sobre a ResNet.

## <a name="how-to-configure-resnet"></a>Como configurar a ResNet

1.  Adicione o módulo **ResNet** ao seu oleoduto no designer.  

2.  Para **o nome do modelo**, especifique o nome de uma determinada estrutura resNet e pode selecionar a partir de resnet suportada: 'resnet18', 'resnet34', 'resnet50', 'resnet101', 'resnet152', 'resnet152', 'resnext50 \_ 32x4d', 'resnext101 \_ 32x8d', 'wide_resnet50 \_ 2', 'wide_resnet101 \_ 2'.

3.  Para **pré-treinado**, especifique se deve utilizar um modelo pré-treinado no ImageNet. Se selecionado, pode afinar o modelo com base no modelo pré-treinado selecionado; se desescolhido, pode treinar do zero.

4.  Ligue a saída do módulo **DenseNet,** o módulo de conjunto de dados de imagem de treino e validação ao [Modelo De Pytorch de Comboio.](train-pytorch-model.md) 

5. Envie o oleoduto.

## <a name="results"></a>Resultados

Após a execução do gasoduto, para utilizar o modelo para pontuação, ligue o [Modelo De Pytorch de Comboio](train-pytorch-model.md) ao Modelo de Imagem de [Pontuação,](score-image-model.md)para prever valores para novos exemplos de entrada.

## <a name="technical-notes"></a>Notas técnicas  

###  <a name="module-parameters"></a>Parâmetros do módulo  

| Nome       | Intervalo | Tipo    | Predefinição           | Descrição                              |
| ---------- | ----- | ------- | ----------------- | ---------------------------------------- |
| Nome do modelo | Qualquer   | Modo    | resnext101 \_ 32x8d | Nome de uma certa estrutura resNet       |
| Pré-treinado | Qualquer   | Booleano | Verdadeiro              | Se usar um modelo pré-treinado no ImageNet |
|            |       |         |                   |                                          |

###  <a name="output"></a>Saída  

| Nome            | Tipo                    | Descrição                              |
| --------------- | ----------------------- | ---------------------------------------- |
| Modelo destreinado | Não treinadoModelDirect | Um modelo resNet destreinado que pode ser ligado ao Modelo Train Pytorch. |

## <a name="next-steps"></a>Próximos passos

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 