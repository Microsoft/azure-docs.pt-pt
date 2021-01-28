---
title: Gerem miniaturas utilizando o Media Services Encoder Standard com .NET
description: Este artigo mostra como usar .NET para codificar um ativo e gerar miniaturas ao mesmo tempo usando o Media Encoder Standard.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 12/01/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: fe051ceec43ec919ddf92a4d7f7e4bfab0b351f5
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98938762"
---
# <a name="how-to-generate-thumbnails-using-encoder-standard-with-net"></a>Como gerar miniaturas usando o Encoder Standard com .NET

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Pode utilizar o Media Encoder Standard para gerar uma ou mais miniaturas a partir do seu vídeo de entrada em formatos de ficheiro de imagem [JPEG](https://en.wikipedia.org/wiki/JPEG) ou [PNG.](https://en.wikipedia.org/wiki/Portable_Network_Graphics)

## <a name="recommended-reading-and-practice"></a>Leitura e prática recomendadas

Recomenda-se que se familiarize com transformações personalizadas lendo [Como codificar com uma transformação personalizada - .NET](customize-encoder-presets-how-to.md).

## <a name="transform-code-example"></a>Transformar exemplo de código

O exemplo de código abaixo cria apenas uma miniatura.  Deve definir os seguintes parâmetros:

- **iniciar** - A posição no vídeo de entrada de onde começar a gerar miniaturas. O valor pode ser no formato ISO 8601 (Por exemplo, PT05S a começar a 5 segundos), ou uma contagem de quadros (por exemplo, 10 para começar no 10º quadro), ou um valor relativo à duração do fluxo (por exemplo, 10% para começar com 10% da duração do fluxo). Também suporta uma macro {Best}, que diz ao codificador para selecionar a melhor miniatura dos primeiros segundos do vídeo e produzirá apenas uma miniatura, independentemente das outras definições para Step e Range. O valor predefinido é macro {Best}.
- **passo** - Os intervalos em que as miniaturas são geradas. O valor pode ser no formato ISO 8601 (por exemplo, PT05S para uma imagem a cada 5 segundos), ou uma contagem de quadros (por exemplo, 30 para uma imagem a cada 30 fotogramas), ou um valor relativo à duração do fluxo (por exemplo, 10% para uma imagem a cada 10% da duração do fluxo). O valor do passo afetará a primeira miniatura gerada, que pode não ser exatamente a especificada na hora de início pré-sintonia de transformação. Isto deve-se ao codificador, que tenta selecionar a melhor miniatura entre a hora de início e a posição do passo a partir da hora de início como primeira saída. Como o valor padrão é de 10%, significa que se o fluxo tiver uma longa duração, a primeira miniatura gerada pode estar longe da especificada na hora de início. Tente selecionar um valor razoável para o passo se se espera que a primeira miniatura esteja perto da hora de início, ou desa um valor de intervalo para 1 se apenas uma miniatura for necessária no momento de início.
- **gama** - A posição relativa à transformação da hora de início predefinida no vídeo de entrada para parar de gerar miniaturas. O valor pode estar no formato ISO 8601 (Por exemplo, PT5M30S para parar a 5 minutos e 30 segundos da hora de início), ou uma contagem de quadros (por exemplo, 300 para parar no 300º quadro a partir do quadro na hora de início. Se este valor for 1, significa apenas produzir uma miniatura na hora de início), ou um valor relativo à duração do fluxo (Por exemplo, 50% para parar a metade da duração do fluxo desde a hora de início). O valor predefinido é de 100%, o que significa parar no final do fluxo.
- **camadas** - Uma coleção de camadas de imagem de saída a produzir pelo codificadora.

```csharp

private static Transform EnsureTransformExists(IAzureMediaServicesClient client, string resourceGroupName, string accountName, string transformName)
{
    // Does a Transform already exist with the desired name? Assume that an existing Transform with the desired name
    // also uses the same recipe or Preset for processing content.
    Transform transform = client.Transforms.Get(resourceGroupName, accountName, transformName);

    if (transform == null)
    {
        // Create a new Transform Outputs array - this defines the set of outputs for the Transform
        TransformOutput[] outputs = new TransformOutput[]
        {
            // Create a new TransformOutput with a custom Standard Encoder Preset
            // This demonstrates how to create custom codec and layer output settings

          new TransformOutput(
                new StandardEncoderPreset(
                    codecs: new Codec[]
                    {
                        // Generate a set of PNG thumbnails
                        new PngImage(
                            start: "25%",
                            step: "25%",
                            range: "80%",
                            layers: new PngLayer[]{
                                new PngLayer(
                                    width: "50%",
                                    height: "50%"
                                )
                            }
                        )
                    },
                    // Specify the format for the output files for the thumbnails
                    formats: new Format[]
                    {
                        new PngFormat(
                            filenamePattern:"Thumbnail-{Basename}-{Index}{Extension}"
                        )
                    }
                ),
                onError: OnErrorType.StopProcessingJob,
                relativePriority: Priority.Normal
            )
        };

        string description = "A transform that includes thumbnails.";
        // Create the custom Transform with the outputs defined above
        transform = client.Transforms.CreateOrUpdate(resourceGroupName, accountName, transformName, outputs, description);
    }

    return transform;
}
```

## <a name="next-steps"></a>Próximos passos
[Gerenas de miniaturas usando REST](media-services-generate-thumbnails-rest.md)
