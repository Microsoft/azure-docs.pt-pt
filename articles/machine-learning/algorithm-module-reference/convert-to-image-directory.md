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
ms.date: 09/28/2020
ms.openlocfilehash: 9f5f4b2b069ebc65430fba4bc31a9891ed61fedf
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91450117"
---
# <a name="convert-to-image-directory"></a>Converter para o Diretório da Imagem

Este artigo descreve como usar o módulo Converte para Diretório de Imagem para ajudar a converter o conjunto de dados de imagem para o tipo de dados 'Image Directory', que é o formato de dados padronizado em tarefas relacionadas com a imagem, como a classificação de imagem no designer de Aprendizagem automática Azure.

## <a name="how-to-use-convert-to-image-directory"></a>Como usar Converter para Diretório de Imagem  

1.  Adicione o módulo **Converte-imagem ao Diretório de Imagem** à tela. Pode encontrar este módulo na categoria 'Visão de Computador/Transformação de Dados de Imagem' na lista de módulos. 

2.  A entrada do módulo **Converte para o Diretório de Imagem** deve ser um conjunto de dados de ficheiros. [Registe um conjunto de dados de imagem](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets) e conecte-o à porta de entrada do módulo. Certifique-se de que há imagem no conjunto de dados de entrada. Atualmente o Designer não suporta visualizar o conjunto de dados de imagem.
 
    Os seguintes formatos de conjunto de dados são suportados:

    - Ficheiro comprimido nestas extensões: ".zip", ".tar", ".gz", ".bz2".
    - Pasta contendo imagens. **Recomendo vivamente a compressão desta pasta primeiro e depois utilize o ficheiro comprimido como conjunto de dados**.

    > [!WARNING]
    > Não **é possível** utilizar o módulo **de Dados de Importação** para importar conjunto de dados de imagem, porque o tipo de saída do módulo de **dados de importação** é o DataFrame Directory, que contém apenas a cadeia de trajetória de ficheiro.
    

    > [!NOTE]
    > - Se utilizar o conjunto de dados de imagem na aprendizagem supervisionada, tem de especificar o rótulo do conjunto de dados de treino.
    > - Para a tarefa de classificação de imagem, o rótulo pode ser gerado como 'categoria' de imagem na saída do módulo se este conjunto de dados de imagem estiver organizado no formato Torchvision ImageFolder. Caso contrário, apenas as imagens são guardadas sem etiqueta. Segue-se um exemplo de como poderia organizar o conjunto de dados de imagem para obter etiqueta, usar a categoria de imagem como nome de sub-classificação. 
    > - Não é necessário carregar a mesma contagem de imagens em cada pasta de categoria.
    > - As imagens com estas extensões (em minúsculas) são suportadas: '.jpg', '.jpeg', '.png', '.ppm', 'bmp', 'pgm', '.tif', '.tiff', '.webp'. Também pode ter vários tipos de imagens numa pasta.    
    > - Consulte os [conjuntos de dados da torchvision](https://pytorch.org/docs/stable/torchvision/datasets.html#imagefolder) para obter mais informações.
    >
    > ```
    > Your_image_folder_name/Category_1/xxx.png
    > Your_image_folder_name/Category_1/xxy.jpg
    > Your_image_folder_name/Category_1/xxz.jpeg
    >
    > Your_image_folder_name/Category_2/123.png
    > Your_image_folder_name/Category_2/nsdf3.png
    > Your_image_folder_name/Category_2/asd932_.png
    > ```
    > - Se utilizar o conjunto de dados de imagem para pontuação, o conjunto de dados do ficheiro de entrada deste módulo deve conter imagens não classificadas.
    
3.  Envie o oleoduto. Este módulo pode ser executado em GPU ou CPU.

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
