---
title: Gere um sprite de miniatura com a Azure Media Services [ Azure Media Services] Microsoft Docs
description: Este tópico mostra como gerar um sprite de miniatura com a Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: e5d32d1bc3bd704b03e58c62251a323ed3f4662c
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/26/2020
ms.locfileid: "61229055"
---
# <a name="generate-a-thumbnail-sprite"></a>Gerar um sprite de miniatura  

Pode utilizar o Media Encoder Standard para gerar um sprite de miniatura, que é um ficheiro JPEG que contém várias miniaturas de pequena resolução costuradas juntas numa única (grande) imagem, juntamente com um ficheiro VTT. Este ficheiro VTT especifica o intervalo de tempo no vídeo de entrada que cada miniatura representa, juntamente com o tamanho e as coordenadas dessa miniatura dentro do grande ficheiro JPEG. Os jogadores de vídeo usam o ficheiro VTT e a imagem sprite para mostrar uma barra de busca 'visual', fornecendo ao espectador feedback visual ao esfregar para trás e para a frente ao longo da linha do tempo de vídeo.

Para utilizar o Media Encoder Standard para gerar Miniatura Sprite, o predefinido:

1. Deve usar o formato de imagem da miniatura JPG
2. Deve especificar os valores de início/passo/gama como selos temporais, ou valores % (e não contagens de quadros) 
    
    1. Não faz mal misturar selos temporais e valores %

3. Deve ter o valor SpriteColumn, como um número não negativo maior ou igual a 1

    1. Se o SpriteColumn estiver definido para M >= 1, a imagem de saída é um retângulo com colunas M. Se o número de miniaturas geradas através de #2 não for um múltiplo exato de M, a última linha estará incompleta e ficará com pixels pretos.  

Segue-se um exemplo:

```json
{
    "Version": 1.0,
    "Codecs": [
    {
      "Start": "00:00:01",
      "Type": "JpgImage",
      "Step": "5%",
      "Range": "100%",
      "JpgLayers": [
        {
          "Type": "JpgLayer",
          "Width": "10%",
          "Height": "10%",
          "Quality": 90
        }
      ],
      "SpriteColumn": 10
    }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
          }
        }
   ]
}
```

## <a name="known-issues"></a>Problemas Conhecidos

1.  Não é possível gerar uma imagem sprite com uma única linha de imagens (SpriteColumn = 1 resulta numa imagem com uma única coluna).
2.  A chunking das imagens sprite em imagens JPEG de tamanho moderado ainda não é suportada. Por conseguinte, deve ser tomado o cuidado de limitar o número de miniaturas e o seu tamanho, de modo que o consequente Sprite miniatura costurado seja de cerca de 8M pixels ou menos.
3.  O Azure Media Player suporta sprites nos navegadores Microsoft Edge, Chrome e Firefox. A análise vTT não é suportada no IE11.

## <a name="next-steps"></a>Passos seguintes

[Codificar conteúdo](media-services-encode-asset.md)
