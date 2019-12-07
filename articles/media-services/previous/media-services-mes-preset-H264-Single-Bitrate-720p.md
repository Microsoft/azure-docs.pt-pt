---
title: H264 taxa de bits única 720p Media Encoder Standard predefinida – Azure | Microsoft Docs
description: Este artigo fornece uma visão geral da predefinição de tarefa Media Encoder Standard "H264 taxa de bits única 720p".
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: f66da66c-9f21-441d-8038-51e3094e9307
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: e48f0c614ba7a7b4199e6ddf0050de0ae7db063f
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74886834"
---
# <a name="h264-single-bitrate-720p"></a>H264 Taxa de Bits Única 720p
`Media Encoder Standard` define um conjunto de predefinições de codificação que você pode usar ao criar trabalhos de codificação. Você pode usar um `preset name` para especificar em qual formato deseja codificar o arquivo de mídia. Ou você pode criar suas próprias predefinições de JSON ou baseadas em XML (usando a codificação UTF-8 ou UTF-16. Em seguida, você passaria a predefinição personalizada para o codificador. Para obter a lista de todos os nomes predefinidos com suporte por este codificador de `Media Encoder Standard`, consulte [predefinições de tarefa para Media Encoder Standard](media-services-mes-presets-overview.md).  
  
 Este tópico mostra a predefinição de `H264 Single Bitrate 720p` no formato XML e JSON.  
  
 Essa predefinição produz um único arquivo MP4 com uma taxa de bits de 4500 kbps e áudio AAC estéreo. Para obter informações detalhadas sobre perfil, taxa de bits, taxa de amostragem, etc. dessa predefinição, examine o XML ou JSON definido abaixo. Para obter explicações sobre o que cada elemento nessas predefinições significa e os valores válidos para cada elemento, consulte o tópico [Media Encoder Standard Schema](media-services-mes-schema.md) .  
  
 XML  
  
```  
<?xml version="1.0" encoding="utf-16"?>  
<Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">  
  <Encoding>  
    <H264Video>  
      <KeyFrameInterval>00:00:02</KeyFrameInterval>  
      <SceneChangeDetection>true</SceneChangeDetection>  
      <H264Layers>  
        <H264Layer>  
          <Bitrate>4500</Bitrate>  
          <Width>1280</Width>  
          <Height>720</Height>  
          <FrameRate>0/1</FrameRate>  
          <Profile>Auto</Profile>  
          <Level>auto</Level>  
          <BFrames>3</BFrames>  
          <ReferenceFrames>3</ReferenceFrames>  
          <Slices>0</Slices>  
          <AdaptiveBFrame>true</AdaptiveBFrame>  
          <EntropyMode>Cabac</EntropyMode>  
          <BufferWindow>00:00:05</BufferWindow>  
          <MaxBitrate>4500</MaxBitrate>  
        </H264Layer>  
      </H264Layers>  
      <Chapters />  
    </H264Video>  
    <AACAudio>  
      <Profile>AACLC</Profile>  
      <Channels>2</Channels>  
      <SamplingRate>48000</SamplingRate>  
      <Bitrate>128</Bitrate>  
    </AACAudio>  
  </Encoding>  
  <Outputs>  
    <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">  
      <MP4Format />  
    </Output>  
  </Outputs>  
</Preset>  
```  
  
 JSON  
  
```  
{  
  "Version": 1.0,  
  "Codecs": [  
    {  
      "KeyFrameInterval": "00:00:02",  
      "SceneChangeDetection": true,  
      "H264Layers": [  
        {  
          "Profile": "Auto",  
          "Level": "auto",  
          "Bitrate": 4500,  
          "MaxBitrate": 4500,  
          "BufferWindow": "00:00:05",  
          "Width": 1280,  
          "Height": 720,  
          "BFrames": 3,  
          "ReferenceFrames": 3,  
          "AdaptiveBFrame": true,  
          "Type": "H264Layer",  
          "FrameRate": "0/1"  
        }  
      ],  
      "Type": "H264Video"  
    },  
    {  
      "Profile": "AACLC",  
      "Channels": 2,  
      "SamplingRate": 48000,  
      "Bitrate": 128,  
      "Type": "AACAudio"  
    }  
  ],  
  "Outputs": [  
    {  
      "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",  
      "Format": {  
        "Type": "MP4Format"  
      }  
    }  
  ]  
}  
```
