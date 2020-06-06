---
title: Converter para Diretório de Imagem
titleSuffix: Azure Machine Learning
description: Saiba como utilizar o módulo Converte-imagem diretório para converter conjunto de dados para formato de diretório de imagem.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: dc40e0a644f692b397b1f2107b27b1d940d2b284
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/05/2020
ms.locfileid: "84450634"
---
# <a name="convert-to-image-directory"></a>Converter para Diretório de Imagem

Este artigo descreve como utilizar o módulo Converte para Diretório de Imagem para ajudar a converter o conjunto de dados de imagem para o tipo de dados 'Image Directory', que é o formato de dados padronizado em tarefas relacionadas com a imagem, como a classificação de imagem no designer de Aprendizagem de Máquinas Azure (pré-visualização).

## <a name="how-to-use-convert-to-image-directory"></a>Como usar Converter para Diretório de Imagem  

1.  Adicione o módulo **Converte-imagem ao Diretório de Imagens** à sua experiência. Pode encontrar este módulo na categoria 'Visão de Computador/Transformação de Dados de Imagem' na lista de módulos. 

2.  Conecte um conjunto de dados de imagem como entrada. Certifique-se de que há imagem no conjunto de dados de entrada.
    Os seguintes formatos de conjunto de dados são suportados:

    - Ficheiro comprimido nestas extensões: ".zip", ".tar", ".gz", ".bz2".
    - Pasta contendo 1 ficheiro comprimido em extensões válidas. 
    - Pasta contendo imagens.

    > [!NOTE]
    > A categoria de imagem pode ser gravada na saída do módulo se este conjunto de dados de imagem estiver organizado no formato Torchvision ImageFolder, consulte os [conjuntos de dados da torchvision](https://pytorch.org/docs/stable/torchvision/datasets.html#imagefolder) para obter mais informações. Caso contrário, apenas as imagens são guardadas.

3.  Envie o oleoduto.

## <a name="results"></a>Resultados

A saída do módulo **Converte para Diretório de Imagem** está no formato de Diretório de Imagem, e pode ser ligada a outros módulos relacionados com a imagem dos quais o formato porta de entrada é também o Diretório de Imagem.

## <a name="technical-notes"></a>Notas técnicas 

###  <a name="expected-inputs"></a>Entradas esperadas  

| Nome          | Tipo                  | Descrição   |
| ------------- | --------------------- | ------------- |
| Conjunto de dados de entrada | AnyDirectory, ZipFile | Conjunto de dados de entrada |

###  <a name="output"></a>Saída  

| Nome                   | Tipo           | Descrição            |
| ---------------------- | -------------- | ---------------------- |
| Diretório de imagem de saída | ImageDirectory | Diretório de imagem de saída |

## <a name="next-steps"></a>Próximos passos

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 
