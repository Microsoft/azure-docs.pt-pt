---
title: H264 Bitrate Único 16x9 SD Audio 5.1 Microsoft Docs
description: O tópico apresenta uma visão geral do **bitrate único H264 16x9 SD Audio 5.1** predefinido.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: d57cd39f-60e8-4248-a21d-c5b9f096b0ce
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 584d36779c8a7ed0641a191a911986c737273587
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89265750"
---
# <a name="h264-single-bitrate-16x9-sd-audio-51"></a>H264 Taxa de Bits Única 16x9 SD Audio 5.1

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

`Media Encoder Standard` define um conjunto de predefinições codificantes que pode usar ao criar trabalhos de codificação. Pode utilizar um `preset name` para especificar em que formato gostaria de codificar o seu ficheiro de mídia. Ou, pode criar as suas próprias predefinições baseadas em JSON ou XML (utilizando codificação UTF-8 ou UTF-16. Passaria então a predefinição personalizada para o codificar. Para a lista de todos os nomes predefinidos suportados por este `Media Encoder Standard` codificadores, consulte [Task Presets for Media Encoder Standard](media-services-mes-presets-overview.md).  
  
 Este tópico mostra a `H264 Single Bitrate 16x9 SD Audio 5.1` predefinição no formato XML e JSON.  
  
 Esta predefinição produz um único ficheiro MP4 com um bitrate de 2200 kbps e áudio AAC 5.1. Para obter informações detalhadas sobre o perfil, o bitrate, a taxa de amostragem, etc. desta predefinição, examine o XML ou JSON definido abaixo. Para obter explicações sobre o que cada elemento significa e os valores válidos para cada elemento, consulte o [esquema padrão da Media Encoder](media-services-mes-schema.md).  
  
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
          <Bitrate>2200</Bitrate>  
          <Width>848</Width>  
          <Height>480</Height>  
          <FrameRate>0/1</FrameRate>  
          <Profile>Auto</Profile>  
          <Level>auto</Level>  
          <BFrames>3</BFrames>  
          <ReferenceFrames>3</ReferenceFrames>  
          <Slices>0</Slices>  
          <AdaptiveBFrame>true</AdaptiveBFrame>  
          <EntropyMode>Cabac</EntropyMode>  
          <BufferWindow>00:00:05</BufferWindow>  
          <MaxBitrate>2200</MaxBitrate>  
        </H264Layer>  
      </H264Layers>  
      <Chapters />  
    </H264Video>  
    <AACAudio>  
      <Profile>AACLC</Profile>  
      <Channels>6</Channels>  
      <SamplingRate>48000</SamplingRate>  
      <Bitrate>384</Bitrate>  
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
          "Bitrate": 2200,  
          "MaxBitrate": 2200,  
          "BufferWindow": "00:00:05",  
          "Width": 848,  
          "Height": 480,  
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
      "Channels": 6,  
      "SamplingRate": 48000,  
      "Bitrate": 384,  
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
