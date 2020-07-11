---
title: Converter para o Diretório da Imagem
titleSuffix: Azure Machine Learning
description: Saiba como utilizar o módulo Converte-imagem diretório para converter conjunto de dados para formato de diretório de imagem.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: b29b5fa1beb19bc055f94c56b064ae2c0ae175b5
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86171147"
---
# <a name="convert-to-image-directory"></a>Converter para o Diretório da Imagem

Este artigo descreve como utilizar o módulo Converte para Diretório de Imagem para ajudar a converter o conjunto de dados de imagem para o tipo de dados 'Image Directory', que é o formato de dados padronizado em tarefas relacionadas com a imagem, como a classificação de imagem no designer de Aprendizagem de Máquinas Azure (pré-visualização).

## <a name="how-to-use-convert-to-image-directory"></a>Como usar Converter para Diretório de Imagem  

1.  Adicione o módulo **Converte-imagem ao Diretório de Imagens** à sua experiência. Pode encontrar este módulo na categoria 'Visão de Computador/Transformação de Dados de Imagem' na lista de módulos. 

2.  [Registe um conjunto de dados de imagem](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets) e conecte-o à porta de entrada do módulo. Certifique-se de que há imagem no conjunto de dados de entrada. 
    Os seguintes formatos de conjunto de dados são suportados:

    - Ficheiro comprimido nestas extensões: ".zip", ".tar", ".gz", ".bz2".
    - Pasta contendo imagens. **Recomendo vivamente a compressão desta pasta primeiro e depois utilize o ficheiro comprimido como conjunto de dados**.

    > [!WARNING]
    > Não **é possível** utilizar o módulo **de Dados de Importação** para importar conjunto de dados de imagem, porque o tipo de saída do módulo de **dados de importação** é o DataFrame Directory, que contém apenas a cadeia de trajetória de ficheiro.
    

    > [!NOTE]
    > Se utilizar o conjunto de dados de imagem na aprendizagem supervisionada, é necessário o rótulo.
    > Para a tarefa de classificação de imagem, o rótulo pode ser gerado como 'categoria' de imagem na saída do módulo se este conjunto de dados de imagem estiver organizado no formato Torchvision ImageFolder. Caso contrário, apenas as imagens são guardadas sem etiqueta. Aqui está um exemplo de como você poderia organizar conjunto de dados de imagem para obter etiqueta, usar a categoria de imagem como nome de sub-classificação. Consulte os [conjuntos de dados da torchvision](https://pytorch.org/docs/stable/torchvision/datasets.html#imagefolder) para obter mais informações.
    >
    > ```
    > root/dog/xxx.png
    > root/dog/xxy.png
    > root/dog/xxz.png
    >
    > root/cat/123.png
    > root/cat/nsdf3.png
    > root/cat/asd932_.png
    > ```

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

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 
