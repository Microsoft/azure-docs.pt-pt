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
ms.date: 11/12/2020
ms.openlocfilehash: 1489ce74da2ecff5212feb5a1a2e3c9151b73424
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94555588"
---
# <a name="convert-to-image-directory"></a>Converter para o Diretório da Imagem

Este artigo descreve como usar o módulo Converte para Diretório de Imagem para ajudar a converter o conjunto de dados de imagem para o tipo de dados *do Image Directory,* que é o formato de dados padronizado em tarefas relacionadas com a imagem, como a classificação de imagem no designer de Aprendizagem automática Azure.

## <a name="how-to-use-convert-to-image-directory"></a>Como usar Converter para Diretório de Imagem  

1. Prepare primeiro o conjunto de dados de imagem. 

    Para uma aprendizagem supervisionada, é necessário especificar o rótulo do conjunto de dados de treino. O ficheiro do conjunto de dados de imagem deve estar na seguinte estrutura:
    
    ```
    Your_image_folder_name/Category_1/xxx.png
    Your_image_folder_name/Category_1/xxy.jpg
    Your_image_folder_name/Category_1/xxz.jpeg
    
    Your_image_folder_name/Category_2/123.png
    Your_image_folder_name/Category_2/nsdf3.png
    Your_image_folder_name/Category_2/asd932_.png
    ```
    
    Na pasta do conjunto de dados de imagem, existem várias sub-dobradeiras. Cada sub-dobradeira contém imagens de uma categoria, respectivamente. Os nomes dos sub-12 são considerados os rótulos para tarefas como a classificação de imagem. Consulte os [conjuntos de dados da torchvision](https://pytorch.org/docs/stable/torchvision/datasets.html#imagefolder) para obter mais informações.

    > [!WARNING]
    > Os conjuntos de dados atualmente rotulados exportados da Data Labeling não são suportados no designer.

    As imagens com estas extensões (em minúsculas) são apoiadas: '.jpg', '.jpeg', '.png', '.ppm', '.bmp', 'pgm', '.tif', '.tiff', '.webp'. Também pode ter vários tipos de imagens numa pasta. Não é necessário conter a mesma contagem de imagens em cada pasta de categoria.

    Pode utilizar a pasta ou o ficheiro comprimido com extensão '.zip', '.tar', '.gz', e '.bz2'. **Os ficheiros comprimidos são recomendados para um melhor desempenho.** 
    
    ![Conjunto de dados de amostra de imagem](./media/module/image-sample-dataset.png)

    > [!NOTE]
    > Para inferência, a pasta do conjunto de dados de imagem só precisa de conter imagens não classificadas.

1. [Registe o conjunto de dados de imagem como um conjunto](../how-to-create-register-datasets.md) de dados de ficheiros no seu espaço de trabalho, uma vez que a entrada do módulo Converte para o Diretório de Imagem deve ser um conjunto de **dados de ficheiros**.

1. Adicione o conjunto de dados de imagem registado à tela. Pode encontrar o seu conjunto de dados registado na categoria **Datasets** na lista de módulos à esquerda da tela. Atualmente o Designer não suporta visualizar o conjunto de dados de imagem.

    > [!WARNING]
    > Não **é possível** utilizar o módulo **de Dados de Importação** para importar conjunto de dados de imagem, porque o tipo de saída do módulo de **dados de importação** é o DataFrame Directory, que contém apenas a cadeia de trajetória de ficheiro.

1. Adicione o módulo **Converte-imagem ao Diretório de Imagem** à tela. Pode encontrar este módulo na categoria 'Visão de Computador/Transformação de Dados de Imagem' na lista de módulos. Conecte-o ao conjunto de dados de imagem.
    
3.  Envie o oleoduto. Este módulo pode ser executado em GPU ou CPU.

## <a name="results"></a>Resultados

A saída do módulo **Converte para Diretório de Imagem** está no formato de **Diretório de Imagem,** e pode ser ligada a outros módulos relacionados com a imagem dos quais o formato porta de entrada é também o Diretório de Imagem.

![Converter para saída de Diretório de Imagem](./media/module/convert-to-image-directory-output.png)

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