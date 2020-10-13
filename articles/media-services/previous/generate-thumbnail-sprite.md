---
title: Gere um sprite de miniatura com a Azure Media Services Microsoft Docs
description: Este tópico mostra como gerar um sprite de miniatura com a Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/31/2020
ms.author: juliako
ms.openlocfilehash: 1d05e73bc9aa51bf20121790de94b1b6d0e26615
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89268575"
---
# <a name="generate-a-thumbnail-sprite"></a>Gerar um sprite de miniatura

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Pode utilizar o Media Encoder Standard para gerar um sprite de miniatura, que é um ficheiro JPEG que contém múltiplas miniaturas de resolução pequenas cosidas numa única (grande) imagem, juntamente com um ficheiro VTT. Este ficheiro VTT especifica o intervalo de tempo no vídeo de entrada que cada miniatura representa, juntamente com o tamanho e coordenadas dessa miniatura dentro do grande ficheiro JPEG. Os jogadores de vídeo usam o ficheiro VTT e a imagem sprite para mostrar uma barra de busca 'visual', proporcionando ao espectador feedback visual ao esfregar para trás e para a frente ao longo da linha de tempo do vídeo.

Para utilizar o Media Encoder Standard para gerar o Thumbnail Sprite, a predefinição:

1. Deve usar o formato de imagem da miniatura JPG
2. Deve especificar os valores de início/passo/gama como se os valores de tempo, ou % valores (e não a contagem de quadros) 
    
    1. É normal misturar os tempos e valores %

3. Deve ter o valor SpriteColumn, como um número não negativo maior ou igual a 1

    1. Se o SpriteColumn estiver definido para M >= 1, a imagem de saída é um retângulo com colunas M. Se o número de miniaturas geradas através de #2 não for um múltiplo exato de M, a última linha ficará incompleta e deixada com pixels pretos.  

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
2.  O estotomamento das imagens do sprite em imagens JPEG de tamanho moderado ainda não está suportado. Por isso, deve-se ter cuidado para limitar o número de miniaturas e o seu tamanho, de modo a que o sprite de miniatura costurado resultante seja de cerca de 8M pixels ou menos.
3.  O Azure Media Player suporta sprites nos navegadores Microsoft Edge, Chrome e Firefox. A análise de VTT não é suportada no IE11.

## <a name="next-steps"></a>Passos seguintes

[Codificar conteúdo](media-services-encode-asset.md)
