---
title: Como cortar ficheiros de vídeo com serviços de mídia - .NET | Microsoft Docs
description: Cortar é o processo de selecionar uma janela retangular dentro da moldura de vídeo, e codificar apenas os pixels dentro dessa janela. Este tópico mostra como cortar ficheiros de vídeo com os Media Services utilizando .NET.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 03/24/2021
ms.author: inhenkel
ms.openlocfilehash: 43d0e1e3b8c0eaa37a3b09557b333c3abafe8b63
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105572439"
---
# <a name="how-to-crop-video-files-with-media-services---net"></a>Como cortar ficheiros de vídeo com serviços de mídia - .NET

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Você pode usar os Media Services para obter um vídeo de entrada. Cortar é o processo de selecionar uma janela retangular dentro da moldura de vídeo, e codificar apenas os pixels dentro dessa janela. O diagrama a seguir ajuda a ilustrar o processo.

## <a name="pre-processing-stage"></a>Fase de pré-processamento

A cultura é uma fase de pré-processamento, pelo que os *parâmetros de corte* na predefinição codificante aplicam-se ao vídeo *de entrada.* A codificação é uma fase subsequente, e as definições de largura/altura aplicam-se ao vídeo *pré-processado* e não ao vídeo original. Ao desenhar a sua predefinição, faça o seguinte:

1. Selecione os parâmetros de cultura com base no vídeo de entrada original
1. Selecione as definições de codificação com base no vídeo cortado.

> [!WARNING]
> Se não corresponder às definições de codificação com o vídeo cortado, a saída não será como se espera.

Por exemplo, o seu vídeo de entrada tem uma resolução de 1920x1080 pixels (relação de aspeto 16:9), mas tem barras pretas (caixas de pilares) à esquerda e à direita, de modo que apenas uma janela de 4:3 ou 1440x1080 pixels contém vídeo ativo. Você pode cortar as barras pretas, e codificar a área 1440x1080.

## <a name="transform-code"></a>Código de transformação

O seguinte código snippet ilustra como escrever uma transformação em .NET para vídeos de colheita.  O código pressupõe que tem um ficheiro local para trabalhar.

- Esquerda é o local mais à esquerda da colheita.
- Top é a localização mais alta da colheita.
- A largura é a largura final da colheita.
- Altura é a altura final da colheita.

```dotnet
var preset = new StandardEncoderPreset

    {

        Filters = new Filters

        {                   

            Crop = new Rectangle

            {

                Left = "200",

                Top = "200",

                Width = "1280",

                Height = "720"

            }

        },

        Codecs =

        {

            new AacAudio(),

            new H264Video()

            {

                Layers =

                {                           

                    new H264Layer

                    {

                        Bitrate = 1000000,

                        Width = "1280",

                        Height = "720"

                    }

                }

            }

        },

        Formats =

        {

            new Mp4Format

            {

                FilenamePattern = "{Basename}_{Bitrate}{Extension}"

            }

        }

    }

```
