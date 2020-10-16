---
title: DenseNet
titleSuffix: Azure Machine Learning
description: Aprenda a criar um modelo de classificação de imagem usando o algoritmo DenseNet.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/26/2020
ms.openlocfilehash: 2351012738f4cf5697fb29891c9459e4cc86cd3a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91536635"
---
# <a name="densenet"></a>DenseNet

Este artigo descreve como usar o módulo **DenseNet** no designer Azure Machine Learning, para criar um modelo de classificação de imagem usando o algoritmo Densenet.  

Este algoritmo de classificação é um método de aprendizagem supervisionado, e requer um diretório de imagem rotulado. 

> [!NOTE]
> Este módulo não suporta conjunto de dados rotulado gerado a partir da Rotulagem de *Dados* no estúdio, mas apenas suporta o diretório de imagem rotulado gerado a partir do módulo [Converte para Diretório de Imagem.](convert-to-image-directory.md) 

Pode treinar o modelo fornecendo o modelo e o diretório de imagem rotulado como entradas para [o Modelo Train Pytorch.](train-pytorch-model.md) O modelo treinado pode então ser utilizado para prever valores para os novos exemplos de entrada utilizando o [Modelo de Imagem de Pontuação](score-image-model.md).

### <a name="more-about-densenet"></a>Mais sobre a DenseNet

Para obter mais informações sobre a DenseNet, consulte o artigo de investigação, [Redes Convolutionais Densamente Ligadas](https://arxiv.org/abs/1608.06993).

## <a name="how-to-configure-densenet"></a>Como configurar a DenseNet

1.  Adicione o módulo **DenseNet** ao seu oleoduto no designer.  

2.  Para **o nome do modelo**, especifique o nome de uma determinada estrutura DenseNet e pode selecionar a partir de DenseNet suportado: 'densenet121', 'densenet161', 'densenet169', 'densenet201'.

3.  Para **pré-treinado**, especifique se deve utilizar um modelo pré-treinado no ImageNet. Se selecionado, pode afinar o modelo com base no modelo pré-treinado selecionado; se desescolhido, pode treinar do zero.

4.  Para **a memória eficiente,** especifique se deve utilizar o checkpointing, que é muito mais eficiente em termos de memória, mas mais lento. Para obter mais informações, consulte o trabalho de investigação, [Implementação eficiente da memória das Densanets.](https://arxiv.org/pdf/1707.06990.pdf)

5.  Ligue a saída do módulo **DenseNet,** formação e conjunto de dados de imagem de validação ao [Modelo De Pytorch de Comboio](train-pytorch-model.md). 

6. Envie o oleoduto.


## <a name="results"></a>Results

Após a execução do gasoduto, para utilizar o modelo para pontuação, ligue o [Modelo De Pytorch de Comboio](train-pytorch-model.md) ao Modelo de Imagem de [Pontuação,](score-image-model.md)para prever valores para novos exemplos de entrada.

## <a name="technical-notes"></a>Notas técnicas  

###  <a name="module-parameters"></a>Parâmetros do módulo  

| Nome             | Intervalo | Tipo    | Predefinição     | Descrição                              |
| ---------------- | ----- | ------- | ----------- | ---------------------------------------- |
| Nome do modelo       | Qualquer   | Modo    | densenet201 | Nome de uma certa estrutura DenseNet     |
| Pré-treinado       | Qualquer   | Booleano | Verdadeiro        | Se usar um modelo pré-treinado no ImageNet |
| Memória eficiente | Qualquer   | Booleano | Falso       | Se usar o checkpointing, que é muito mais eficiente na memória, mas mais lento |

###  <a name="output"></a>Saída  

| Nome            | Tipo                    | Descrição                              |
| --------------- | ----------------------- | ---------------------------------------- |
| Modelo destreinado | Não treinadoModelDirect | Um modelo de DenseNet destreinado que pode ser ligado ao Modelo Train Pytorch. |

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 
