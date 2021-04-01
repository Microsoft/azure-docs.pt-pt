---
title: Aplicar a Transformação da Imagem
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo De Transformação de Imagem Aplicar para aplicar uma transformação de imagem a um diretório de imagem.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: a64d5cebfd8e70e2f54a66193a7041c47887c54a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "90898906"
---
# <a name="apply-image-transformation"></a>Aplicar a Transformação da Imagem 

Este artigo descreve como usar o módulo de transformação de imagem aplicar no designer Azure Machine Learning, para modificar um diretório de imagem de entrada baseado numa transformação de imagem previamente especificada.  

É necessário ligar um módulo [de transformação de imagem Init](init-image-transformation.md) para especificar a transformação e, em seguida, pode aplicar tal transformação ao diretório de imagem de entrada do módulo de Transformação de Imagem Aplicada.

## <a name="how-to-use-apply-image-transformation"></a>Como usar a Transformação de Imagem Aplicada  

1. Adicione o módulo **de Transformação de Imagem Aplicar** ao seu oleoduto. Pode encontrar este módulo na categoria *Visão de Computador/Transformação de Dados de Imagem.* 

2. Ligue a saída da **Init Image Transformation** à entrada esquerda da **Apply Image Transformation**.

     > [!NOTE]
     > Apenas a transformação de imagem gerada pelo módulo [Init Image Transformation](init-image-transformation.md) é aceite neste módulo. Para outro tipo de transformação, por favor conecte-a à [Apply Transformation,](apply-transformation.md)caso contrário será lançado o 'InvalidTransformationDirectDirectorror'.


3. Ligue o diretório de imagens que pretende transformar.

4. Para **o Modo**, especifique para que finalidade utiliza a transformação da entrada: "Para treino" ou "Para inferência". 

   Se selecionar **Para o treino,** será aplicada toda a transformação que especificar na Transformação de Imagem init.

   Se selecionar **Para inferência,** a transformação como a criação de novas amostras aleatoriamente será excluída antes de ser aplicada. Isto porque as operações de transformação para criar novas amostras aleatoriamente como "flip horizontal aleatório" são utilizadas para o aumento de dados em formação, que devem ser removidas em inferência, porque as amostras de inferência precisam de ser fixadas para uma previsão e avaliação precisas.

   > [!NOTE]
   > Transformações que serão excluídas em modo **Para inferência** são: Cultura redimensionada aleatória, colheita aleatória, flip horizontal aleatório, flip vertical aleatório, rotação aleatória, afina aleatória, escala de cinza aleatória, perspetiva aleatória, apagamento aleatório.

5. Para aplicar uma transformação de imagem a um novo diretório de imagem, submeta o pipeline.  

### <a name="module-parameters"></a>Parâmetros do módulo

| Name | Intervalo | Tipo | Predefinição                   | Description                              |
| ---- | ----- | ---- | ------------------------- | ---------------------------------------- |
| Modo | Qualquer   | Modo | (Exigir que o utilizador especifique) | Para que fim usa a transformação de entrada. Deve excluir operações de transformação 'Random' em inferência, mas mantê-las em treino |

### <a name="expected-inputs"></a>Entradas esperadas  

| Nome                       | Tipo                    | Description                       |
| -------------------------- | ----------------------- | --------------------------------- |
| Transformação de imagem de entrada | Diretório de Transformação | Transformação de imagem de entrada        |
| Diretório de imagem de entrada      | ImageDirectory          | Diretório de imagem a ser transformado |

### <a name="outputs"></a>Saídas  

| Nome                   | Tipo           | Description            |
| ---------------------- | -------------- | ---------------------- |
| Diretório de imagem de saída | ImageDirectory | Diretório de imagem de saída |

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 
