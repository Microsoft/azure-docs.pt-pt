---
title: Esquema de metadados de metadados de saída do Azure Media Services
description: Este artigo apresenta uma visão geral do esquema de metadados de saída V3 da Azure Media Services.
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
ms.openlocfilehash: f26905366949c2c198e52f78bc7adb734cbb7f90
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102612461"
---
# <a name="output-metadata"></a>Metadados de saída

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Um trabalho de codificação está associado a um ativo de entrada (ou ativos) no qual pretende executar algumas tarefas de codificação. Por exemplo, codificar um ficheiro MP4 para conjuntos de bitrate adaptativos H.264 MP4; criar uma miniatura; criar sobreposições. Após a conclusão de uma tarefa, é produzido um ativo de saída.  O ativo de saída contém vídeo, áudio, miniaturas e outros ficheiros. O ativo de saída também contém um ficheiro com metadados sobre o ativo de saída. O nome do ficheiro JSON dos metadados tem o seguinte formato: `<source_file_name>_manifest.json` (por exemplo, `BigBuckBunny_manifest.json` ). Deve procurar qualquer *_metadata.jse consultar a cadeia de ficheiros no interior para encontrar o nome de ficheiro de origem (sem truncação).

Os Serviços de Comunicação Social não verificam preventivamente os ativos de entrada para gerar metadados. Os metadados de entrada são gerados apenas como um artefacto quando um ativo de entrada é processado num trabalho. Daí que este artefacto seja escrito para o ativo de saída. Diferentes ferramentas são usadas para gerar metadados para ativos de entrada e ativos de saída. Portanto, os metadados de entrada têm um esquema ligeiramente diferente dos metadados de saída.

Este artigo discute os elementos e tipos do esquema JSON no qual se baseiam os metadados de saída &lt; (source_file_name &gt;_manifest.js). <!--For information about the file that contains metadata about the input asset, see [Input metadata](input-metadata-schema.md).  -->

Pode encontrar o código de esquema completo e o exemplo JSON no final deste artigo.  

## <a name="assetfile"></a>Ficheiro de Ativos

Cobrança de entradas do AssetFile para o trabalho de codificação.  

| Nome | Descrição |
| --- | --- |
| **Origens** |Recolha de ficheiros de meios de entrada/fonte, que foram processados para produzir este AssetFile.<br />Exemplo: `"Sources": [{"Name": "Ignite-short_1280x720_AACAudio_3551.mp4"}]`|
| **VideoTracks**|Cada AssetFile físico pode conter nele zero ou mais vídeos faixas intercaladas num formato de recipiente apropriado. <br />Ver [VideoTracks](#videotracks). |
| **AudioTracks**|Cada ActivoFile físico pode conter nele zero ou mais faixas de áudio intercaladas num formato de recipiente apropriado. Esta é a coleção de todas aquelas faixas de áudio.<br /> Para mais informações, consulte [AudioTracks](#audiotracks). |
| **Nome**<br />Necessário |O nome do ficheiro de ativos da comunicação social. <br /><br />Exemplo: `"Name": "Ignite-short_1280x720_AACAudio_3551.mp4"`|
| **Tamanho**<br />Necessário |Tamanho do ficheiro de ativos em bytes. <br /><br />Exemplo: `"Size": 32414631`|
| **Duration**<br />Necessário |O conteúdo reproduz a duração. Para mais informações, consulte o formato [ISO8601.](https://www.iso.org/iso-8601-date-and-time-format.html) <br /><br />Exemplo: `"Duration": "PT1M10.315S"`|

## <a name="videotracks"></a>VideoTracks 

Cada AssetFile físico pode conter nele zero ou mais vídeos faixas intercaladas num formato de recipiente apropriado. O elemento **VideoTracks** representa uma coleção de todas as faixas de vídeo.  

| Nome | Descrição |
| --- | --- |
| **ID**<br /> Necessário |Índice baseado em zero desta pista de vídeo. **Nota:**  Este **Id** não é necessariamente o TrackID usado num ficheiro MP4. <br /><br />Exemplo: `"Id": 1`|
| **FourCC**<br />Necessário | Código de vídeo Código FourCC que é reportado por ffmpeg.  <br /><br />Exemplo: `"FourCC": "avc1" | "hev1" | "hvc1"`|
| **Perfil** |Perfil H264 (apenas aplicável ao código H264)  <br /><br />Exemplo: `"Profile": "High"` |
| **Nível** |Nível H264 (apenas aplicável ao código H264).  <br /><br />Exemplo: `"Level": "3.2"`|
| **Width**<br />Necessário |Largura de vídeo codificada em pixels.  <br /><br />Exemplo: `"Width": "1280"`|
| **Height**<br />Necessário |A altura de vídeo codificada em pixels.  <br /><br />Exemplo: `"Height": "720"`|
| **DisplayAspectRatioNumerator**<br />Necessário|Numerador de aspeto de visualização de vídeo.  <br /><br />Exemplo: `"DisplayAspectRatioNumerator": 16.0`|
| **DisplayAspectRatioDenominador**<br />Necessário |Denominador de rácio de aspeto do visualização de vídeo.  <br /><br />Exemplo: `"DisplayAspectRatioDenominator": 9.0`|
| **Taxa de fotogramas**<br />Necessário |Taxa de fotogramas de vídeo medida em formato .3f.  <br /><br />Exemplo: `"Framerate": 29.970`|
| **Bitrate**<br />Necessário |Taxa média de bits de vídeo em bits por segundo, calculada a partir do AssetFile. Conta apenas a carga de fluxo elementar, e não inclui a sobrecarga da embalagem.  <br /><br />Exemplo: `"Bitrate": 3551567`|
| **TargetBitrate**<br />Necessário |Bitrate médio do alvo para esta pista de vídeo, conforme solicitado através da predefinição codificante, em bits por segundo. <br /><br />Exemplo: `"TargetBitrate": 3520000` |

## <a name="audiotracks"></a>AudioTracks 

Cada ActivoFile físico pode conter nele zero ou mais faixas de áudio intercaladas num formato de recipiente apropriado. O elemento **AudioTracks** representa uma coleção de todas aquelas faixas de áudio.  

| Nome  | Descrição |
| --- | --- |
| **ID**<br />Necessário  |Índice baseado em zero desta faixa de áudio. **Nota:**  Este não é necessariamente o TrackID usado num ficheiro MP4.  <br /><br />Exemplo: `"Id": 2`|
| **Codec**  |Cadeia de codec de faixa de áudio.  <br /><br />Exemplo: `"Codec": "aac"`|
| **Linguagem**|Exemplo: `"Language": "eng"`|
| **Channels** (Canais)<br />Necessário|Número de canais de áudio.  <br /><br />Exemplo: `"Channels": 2`|
| **AmostragemRate**<br />Necessário |Taxa de amostragem de áudio em amostras/seg ou Hz.  <br /><br />Exemplo: `"SamplingRate": 48000`|
| **Bitrate**<br />Necessário |Taxa média de bits de áudio em bits por segundo, calculada a partir do AssetFile. Conta apenas a carga de fluxo elementar, e não inclui a sobrecarga da embalagem.  <br /><br />Exemplo: `"Bitrate": 128041`|

## <a name="json-schema-example"></a>Exemplo de esquema JSON

```json
{
  "AssetFile": [
    {
      "Sources": [
        {
          "Name": "Ignite-short_1280x720_AACAudio_3551.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "3.2",
          "Width": "1280",
          "Height": "720",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 3551567,
          "TargetBitrate": 3520000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }
      ],
      "Name": "Ignite-short_1280x720_AACAudio_3551.mp4",
      "Size": 32414631,
      "Duration": "PT1M10.315S"
    },
    {
      "Sources": [
        {
          "Name": "Ignite-short_960x540_AACAudio_2216.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "3.1",
          "Width": "960",
          "Height": "540",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 2216326,
          "TargetBitrate": 2210000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }
      ],
      "Name": "Ignite-short_960x540_AACAudio_2216.mp4",
      "Size": 20680897,
      "Duration": "PT1M10.315S"
    },
    {
      "Sources": [
        {
          "Name": "Ignite-short_640x360_AACAudio_1150.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "3.0",
          "Width": "640",
          "Height": "360",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 1150440,
          "TargetBitrate": 1150000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }
      ],
      "Name": "Ignite-short_640x360_AACAudio_1150.mp4",
      "Size": 11313920,
      "Duration": "PT1M10.315S"
    },
    {
      "Sources": [
        {
          "Name": "Ignite-short_480x270_AACAudio_722.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "2.1",
          "Width": "480",
          "Height": "270",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 722682,
          "TargetBitrate": 720000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }
      ],
      "Name": "Ignite-short_480x270_AACAudio_722.mp4",
      "Size": 7554708,
      "Duration": "PT1M10.315S"
    },
    {
      "Sources": [
        {
          "Name": "Ignite-short_320x180_AACAudio_380.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "1.3",
          "Width": "320",
          "Height": "180",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 380655,
          "TargetBitrate": 380000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }`
      ],
      "Name": "Ignite-short_320x180_AACAudio_380.mp4",
      "Size": 4548932,
      "Duration": "PT1M10.315S"
    }
  ]
}
```

## <a name="next-steps"></a>Passos seguintes

[Criar uma entrada de trabalho a partir de um URL HTTPS](job-input-from-http-how-to.md)
