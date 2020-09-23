---
title: DenseNet
titleSuffix: Azure Machine Learning
description: Aprenda a criar um modelo de classificação de imagem usando o algoritmo densenet.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: 69c18c24ae9a8eb4c1fd54c1f8530e126a40b004
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90898519"
---
# <a name="densenet"></a>DenseNet

Este artigo descreve como usar o módulo **DenseNet** no designer Azure Machine Learning, para criar um modelo de classificação de imagem usando o algoritmo Densenet.  

Este algoritmo de classificação é um método de aprendizagem supervisionado, e requer um conjunto de dados rotulado. Consulte o módulo [de Conversão para Diretório de Imagem](convert-to-image-directory.md) para obter mais instruções sobre como obter um diretório de imagem rotulado. Pode treinar o modelo fornecendo o modelo e o diretório de imagem rotulado como entradas para [o Modelo Train Pytorch.](train-pytorch-model.md) O modelo treinado pode então ser utilizado para prever valores para os novos exemplos de entrada utilizando o [Modelo de Imagem de Pontuação](score-image-model.md).

### <a name="more-about-densenet"></a>Mais sobre a DenseNet

Consulte [redes convolutionais densamente ligadas](https://arxiv.org/abs/1608.06993) para obter mais detalhes.

## <a name="how-to-configure-densenet"></a>Como configurar a DenseNet

1.  Adicione o módulo **DenseNet** ao seu oleoduto no designer.  

2.  Para **o nome do modelo**, especifique o nome de uma determinada estrutura de denso e pode selecionar a partir de denso suportado: 'densenet121', 'densenet161', 'densenet169', 'densenet201'.

3.  Para **pré-treinado**, especifique se deve utilizar um modelo pré-treinado no ImageNet. Se selecionado, pode afinar o modelo com base no modelo pré-treinado selecionado; se desescolhido, pode treinar do zero.

4.  Para **a memória eficiente,** especifique se deve utilizar o checkpointing, que é muito mais eficiente em termos de memória, mas mais lento. Consulte https://arxiv.org/pdf/1707.06990.pdf para obter mais informações.

5.  Ligue a saída do módulo **DenseNet,** o módulo de conjunto de dados de imagem de treino e validação ao [Modelo De Pytorch de Comboio.](train-pytorch-model.md) 

6. Envie o oleoduto.


## <a name="results"></a>Resultados

Após a execução do gasoduto, para utilizar o modelo para pontuação, ligue o [Modelo De Pytorch de Comboio](train-pytorch-model.md) ao Modelo de Imagem de [Pontuação,](score-image-model.md)para prever valores para novos exemplos de entrada.

## <a name="technical-notes"></a>Notas técnicas  

###  <a name="module-parameters"></a>Parâmetros do módulo  

| Name             | Intervalo | Tipo    | Predefinição     | Descrição                              |
| ---------------- | ----- | ------- | ----------- | ---------------------------------------- |
| Nome do modelo       | Qualquer   | Modo    | densenet201 | Nome de uma certa estrutura de densenet     |
| Pré-treinado       | Qualquer   | Booleano | Verdadeiro        | Se usar um modelo pré-treinado no ImageNet |
| Memória eficiente | Qualquer   | Booleano | Falso       | Se usar o checkpointing, que é muito mais eficiente na memória, mas mais lento |

###  <a name="output"></a>Saída  

| Nome            | Tipo                    | Descrição                              |
| --------------- | ----------------------- | ---------------------------------------- |
| Modelo destreinado | Não treinadoModelDirect | Um modelo de densenet destreinado que pode ser ligado ao Modelo Train Pytorch. |

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 
