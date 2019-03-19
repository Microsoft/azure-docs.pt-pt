---
title: Gerar um sprite miniaturas com os serviços de multimédia do Azure | Documentos da Microsoft
description: Este tópico mostra como gerar um sprite miniaturas com os serviços de multimédia do Azure.
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
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57991659"
---
# <a name="generate-a-thumbnail-sprite"></a>Gerar um sprite em miniatura  

Pode utilizar o Media Encoder Standard para gerar um sprite em miniatura, que é um arquivo JPEG, que contém vários miniaturas de resolução pequeno Unidas numa única imagem (grande), juntamente com um ficheiro VTT. Este ficheiro VTT Especifica o intervalo de tempo no vídeo de entrada que representa cada miniatura, juntamente com o tamanho e as coordenadas dessa miniatura dentro do arquivo JPEG grandes. Leitores de vídeo utilizar a imagem de ficheiro e o sprite VTT para mostrar um seekbar "visual", fornecendo um visualizador com comentários visuais quando voltar a limpeza e reencaminhar juntamente com a linha cronológica de vídeo.

Para poder utilizar o Media Encoder Standard para gerar a miniatura Sprite, a configuração predefinida:

1. Tem de utilizar o formato de imagem em miniatura JPG
2. Tem de especificar valores de início/passo/intervalo como qualquer um dos carimbos de data /, ou valores de % (e não as contagens de quadro) 
    
    1. Há problema em combinar valores carimbos e %

3. Tem de ter o valor de SpriteColumn, como um número não negativo maior que ou igual a 1

    1. Se SpriteColumn estiver definido como M > = 1, a imagem de saída é um retângulo com colunas de M. Se o número de miniaturas gerado através de #2 não é um múltiplo exato de M, a última linha será incompleta e esquerda com pixels pretas.  

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

1.  Não é possível gerar uma imagem do sprite com uma única linha de imagens (SpriteColumn = 1 resultados numa imagem com uma única coluna).
2.  Segmentação de imagens do sprite nas imagens JPEG moderadamente tamanho ainda não é suportado. Por este motivo, deve ter cuidado para limitar o número de miniaturas e seu tamanho, para que o Sprite de miniatura stitched resultante é em torno de 8 pixels de M ou menos.
3.  O Azure Media Player suporta sprites nos browsers Microsoft Edge, Chrome e Firefox. Análise de VTT não é suportada no IE11.

## <a name="next-steps"></a>Passos Seguintes

[Codificar conteúdo](media-services-encode-asset.md)
