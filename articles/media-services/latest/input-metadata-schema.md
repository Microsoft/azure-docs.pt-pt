---
title: Azure Media Services v3 input metadados esquema
description: Este artigo apresenta uma visão geral do esquema de metadados de entrada Azure Media Services v3.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 9ddfe3ea0d26a9032922423e7f2c2a2b6c3e411a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89295569"
---
# <a name="input-metadata"></a>Metadados de entrada

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Um trabalho de codificação está associado a um ativo de entrada (ou ativos) no qual pretende executar algumas tarefas de codificação.  Após a conclusão de uma tarefa, é produzido um ativo de saída. O ativo de saída contém vídeo, áudio, miniaturas, manifesto e outros ficheiros. 

O ativo de saída também contém um ficheiro com metadados sobre o ativo de entrada. O nome do ficheiro JSON dos metadados tem um ID aleatório, não o utilize para identificar o ativo de entrada a que o ativo de saída pertence. Para identificar o ativo de entrada a que pertence, utilize o `Uri` campo (para mais informações, consulte [Outros elementos infantis).](#other-child-elements)  

Os Serviços de Comunicação Social não verificam preventivamente os ativos de entrada para gerar metadados. Os metadados de entrada são gerados apenas como um artefacto quando um ativo de entrada é processado num Job. Daí que este artefacto seja escrito para o ativo de saída. Diferentes ferramentas são usadas para gerar metadados para ativos de entrada e ativos de saída. Portanto, os metadados de entrada têm um esquema ligeiramente diferente dos metadados de saída.

Este artigo discute os elementos e tipos do esquema JSON em que se baseia a entrada metada ( &lt; asset_id &gt;_metadata.jsem ). Para obter informações sobre o ficheiro que contém metadados sobre o ativo de saída, consulte [metadados de saída](output-metadata-schema.md).  

Pode encontrar o exemplo do esquema JSON no final deste artigo.  

## <a name="assetfile"></a>Ficheiro de Ativos  

Contém uma coleção de elementos AssetFile para o trabalho de codificação.  

> [!NOTE]
> Os quatro elementos infantis seguintes devem aparecer numa sequência.  
> 
> 

| Nome  | Descrição |
| --- | --- | 
| **VideoTracks**|Cada ficheiro de ativo físico pode conter faixas de vídeos zero ou mais intercaladas num formato de recipiente apropriado. Para mais informações, consulte [VideoTracks](#videotracks). |
| **AudioTracks**|Cada ficheiro de ativo físico pode conter zero ou mais faixas de áudio intercaladas num formato de recipiente apropriado. Para mais informações, consulte [AudioTracks](#audiotracks) |
| **Metadados**  |Os metadados do ficheiro do ativo representados como cordas de valor chave\valor. <br />Por exemplo: `<Metadata key="language" value="eng" />` |

### <a name="other-child-elements"></a>Outros elementos infantis

| Nome | Descrição |
| --- | --- |
| **Nome**<br />Necessário |Nome do ficheiro do ativo. <br /><br />Exemplo: `"Name": "Ignite-short.mp4"` |
| **Uri**<br />Necessário |O URL onde o ativo de entrada está localizado. Para identificar o ativo de entrada a que pertence o ativo de saída, utilize o `Uri` campo em vez de ID.|
| **Tamanho**<br />Necessário |Tamanho do ficheiro de ativos em bytes.  <br /><br />Exemplo: `"Size": 75739259`|
| **Duration**<br />Necessário |O conteúdo reproduz a duração. <br /><br />Exemplo: `"Duration": "PT1M10.304S"`. |
| **NúmeroOfStreams**<br />Necessário |Número de streams no ficheiro do ativo.  <br /><br />Exemplo: `"NumberOfStreams": 2`|
| **FormatoNames**<br />Necessário |Nomes de formato.  <br /><br />Exemplo: `"FormatNames": "mov,mp4,m4a,3gp,3g2,mj2"`|
| **Nome formatoVerboseName**<br /> Necessário |Formato verbose nomes. <br /><br />Exemplo: `"FormatVerboseName": "QuickTime / MOV"` |
| **Horário de início** |Hora de início de conteúdo.  <br /><br />Exemplo: `"StartTime": "PT0S"` |
| **OverallBitRate** |Bitrate médio do ficheiro do ativo em bits por segundo.  <br /><br />Exemplo: `"OverallBitRate": 8618539`|

## <a name="videotracks"></a>VideoTracks

| Nome |  | Descrição |
| --- | --- |
| **FourCC**<br />Necessário |Código de vídeo Código FourCC que é reportado por ffmpeg.<br /><br />Exemplo: `"FourCC": "avc1"` |
| **Perfil** |Perfil da pista de vídeo. <br /><br />Exemplo: `"Profile": "Main"`|
| **Nível** |Nível de pista de vídeo. <br /><br />Exemplo: `"Level": "3.2"`|
| **PixelFormat** |O formato pixel da pista de vídeo. <br /><br />Exemplo: `"PixelFormat": "yuv420p"`|
| **Largura**<br />Necessário |Largura de vídeo codificada em pixels. <br /><br />Exemplo: `"Width": "1280"`|
| **Altura**<br />Necessário |A altura de vídeo codificada em pixels.<br /><br />Exemplo: `"Height": "720"` |
| **DisplayAspectRatioNumerator**<br />Necessário |Numerador de aspeto de visualização de vídeo.<br /><br />Exemplo: `"DisplayAspectRatioNumerator": 16.0` |
| **DisplayAspectRatioDenominador**<br />Necessário |Denominador de rácio de aspeto do visualização de vídeo. <br /><br />Exemplo: `"DisplayAspectRatioDenominator": 9.0`|
| **SampleAspectRatioNumerator** |Numerador de aspeto da amostra de vídeo. <br /><br />Exemplo: `"SampleAspectRatioNumerator": 1.0`|
| **AmostraAspectRatioDenominador**|Exemplo: `"SampleAspectRatioDenominator": 1.0`|
| **FrameRate**<br />Necessário |Taxa de fotogramas de vídeo medida em formato .3f. <br /><br />Exemplo: `"FrameRate": 29.970`|
| **Bitrate** |Taxa média de bits de vídeo em bits por segundo, calculada a partir do ficheiro do ativo. Apenas a carga útil elementar é contada, e a sobrecarga da embalagem não está incluída. <br /><br />Exemplo: `"Bitrate": 8421583`|
| **HasBFrames** |Número de vídeo de quadros B. <br /><br />Exemplo: `"HasBFrames": 2`|
| **Metadados** |Cordas genéricas/valor que podem ser usadas para conter uma variedade de informações. <br />Veja o exemplo completo no final do artigo. |
| **ID**<br />Necessário |Índice baseado em zero desta faixa de áudio ou vídeo.<br /><br /> Este **Id** não é necessariamente o TrackID usado num ficheiro MP4. <br /><br />Exemplo: `"Id": 2`|
| **Codec** |Cadeia codec de pista de vídeo. <br /><br />Exemplo: `"Codec": "h264"`|
| **Nome codecLong** |Nome codec de linha de áudio ou vídeo. <br /><br />Exemplo: `"CodecLongName": "H.264 / AVC / MPEG-4 AVC / MPEG-4 part 10"`|
| **Codec** |Cadeia codec de pista de vídeo. <br /><br />Exemplo: `"Codec": "h264"`|
| **TimeBase**<br />Necessário |Base do tempo.<br /><br />Exemplo: `"TimeBase": "1/30000"`|
| **Números DeFrames** |Número de quadros (presente para faixas de vídeo). <br /><br />Exemplo: `"NumberOfFrames": 2107`|
| **Horário de início** |Hora de início da pista.<br /><br />Exemplo: `"StartTime": "PT0.033S"` |
| **Duration** |Duração da pista. <br /><br />Exemplo: `"Duration": "PT1M10.304S"`|

## <a name="audiotracks"></a>AudioTracks

| Nome  | Descrição |
| --- | --- | 
| **SampleFormat** |Formato de amostra. <br /><br />Exemplo: `"SampleFormat": "fltp"`|
| **ChannelLayout** |Disposição do canal. <br /><br />Exemplo: `"ChannelLayout": "stereo"`|
| **Channels** (Canais)<br />Necessário |Número (0 ou mais) de canais de áudio. <br /><br />Exemplo: `"Channels": 2`|
| **AmostragemRate**<br />Necessário |Taxa de amostragem de áudio em amostras/seg ou Hz. <br /><br />Exemplo: `"SamplingRate": 48000`|
| **Bitrate** |Taxa média de bits de áudio em bits por segundo, calculada a partir do ficheiro do ativo. Apenas a carga útil elementar é contada, e a sobrecarga da embalagem não está incluída nesta contagem. <br /><br />Exemplo: `"Bitrate": 192080`|
| **Metadados** |Cordas genéricas/valor que podem ser usadas para conter uma variedade de informações.  <br />Veja o exemplo completo no final do artigo. |
| **ID**<br />Necessário |Índice baseado em zero desta faixa de áudio ou vídeo.<br /><br /> Isto não é necessariamente o TrackID usado num ficheiro MP4. <br /><br />Exemplo: `"Id": 1`|
| **Codec** |Cadeia codec de pista de vídeo. <br /><br />Exemplo: `"Codec": "aac"`|
| **Nome codecLong** |Nome codec de linha de áudio ou vídeo. <br /><br />Exemplo: `"CodecLongName": "AAC (Advanced Audio Coding)"`|
| **TimeBase**<br />Necessário |Base do tempo.<br /><br />Exemplo: `"TimeBase": "1/48000"` |
| **Números DeFrames** |Número de quadros (presente para faixas de vídeo). <br /><br />Exemplo: `"NumberOfFrames": 3294`|
| **Horário de início** |Hora de início da pista. Para mais informações, consulte [ISO8601](https://www.iso.org/iso-8601-date-and-time-format.html). <br /><br />Exemplo: `"StartTime": "PT0S"` |
| **Duration** |Duração da pista. <br /><br />Exemplo: `"Duration": "PT1M10.272S"` |

## <a name="metadata"></a>Metadados

| Nome | Descrição |
| --- | --- |
| **chave**<br />Necessário |A chave no par chave/valor. |
| **value**<br /> Necessário |O valor no par chave/valor. |

## <a name="schema-example"></a>Exemplo de Schema

```json
{
  "AssetFile": [
    {
      "VideoTracks": [
        {
          "FourCC": "avc1",
          "Profile": "Main",
          "Level": "3.2",
          "PixelFormat": "yuv420p",
          "Width": "1280",
          "Height": "720",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "SampleAspectRatioNumerator": 1.0,
          "SampleAspectRatioNumeratorSpecified": true,
          "SampleAspectRatioDenominator": 1.0,
          "SampleAspectRatioDenominatorSpecified": true,
          "FrameRate": 29.970,
          "Bitrate": 8421583,
          "BitrateSpecified": true,
          "HasBFrames": 2,
          "HasBFramesSpecified": true,
          "Disposition": {
            "Default": 1
          },
          "Metadata": [
            {
              "key": "creation_time",
              "value": "2018-02-21T21:42:08.000000Z"
            },
            {
              "key": "language",
              "value": "eng"
            },
            {
              "key": "handler_name",
              "value": "Video Media Handler"
            },
            {
              "key": "encoder",
              "value": "AVC Coding"
            }
          ],
          "Id": 2,
          "Codec": "h264",
          "CodecLongName": "H.264 / AVC / MPEG-4 AVC / MPEG-4 part 10",
          "TimeBase": "1/30000",
          "NumberOfFrames": 2107,
          "NumberOfFramesSpecified": true,
          "StartTime": "PT0.033S",
          "Duration": "PT1M10.304S"
        }
      ],
      "AudioTracks": [
        {
          "SampleFormat": "fltp",
          "ChannelLayout": "stereo",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 192080,
          "BitrateSpecified": true,
          "BitsPerSampleSpecified": true,
          "Disposition": {
            "Default": 1
          },
          "Metadata": [
            {
              "key": "creation_time",
              "value": "2018-02-21T21:42:08.000000Z"
            },
            {
              "key": "language",
              "value": "eng"
            },
            {
              "key": "handler_name",
              "value": "Sound Media Handler"
            }
          ],
          "Id": 1,
          "Codec": "aac",
          "CodecLongName": "AAC (Advanced Audio Coding)",
          "TimeBase": "1/48000",
          "NumberOfFrames": 3294,
          "NumberOfFramesSpecified": true,
          "StartTime": "PT0S",
          "Duration": "PT1M10.272S"
        }
      ],
      "Metadata": [
        {
          "key": "major_brand",
          "value": "mp42"
        },
        {
          "key": "minor_version",
          "value": "19529854"
        },
        {
          "key": "compatible_brands",
          "value": "mp42isom"
        },
        {
          "key": "creation_time",
          "value": "2018-02-21T21:42:08.000000Z"
        }
      ],
      "Name": "Ignite-short.mp4",
      "Uri": "https://amsstorageacct.blob.core.windows.net/asset-00000000-0000-0000-000000000000/ignite.mp4",
      "Size": 75739259,
      "Duration": "PT1M10.304S",
      "NumberOfStreams": 2,
      "FormatNames": "mov,mp4,m4a,3gp,3g2,mj2",
      "FormatVerboseName": "QuickTime / MOV",
      "StartTime": "PT0S",
      "OverallBitRate": 8618539,
      "OverallBitRateSpecified": true
    }
  ]
}
```

## <a name="next-steps"></a>Passos seguintes

[Metadados de saída](output-metadata-schema.md)
