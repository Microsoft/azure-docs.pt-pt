---
title: H264 Bitrate Único 4K Áudio 5.1 | Microsoft Docs
description: O tópico apresenta uma visão geral da predefinição da tarefa **H264 Single Bitrate 4K Audio 5.1.**
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 72cb95ac-2cd6-4ef4-9938-8f22cea04920
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: f6c532a8c0e45fc1c9be035aa5ca0f247fe5e7b0
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103009067"
---
# <a name="h264-single-bitrate-4k-audio-51"></a>H264 Taxa de Bits Única 4K Audio 5.1

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

`Media Encoder Standard` define um conjunto de predefinições codificantes que pode usar ao criar trabalhos de codificação. Pode utilizar um `preset name` para especificar em que formato gostaria de codificar o seu ficheiro de mídia. Ou, pode criar as suas próprias predefinições baseadas em JSON ou XML (utilizando codificação UTF-8 ou UTF-16. Passaria então a predefinição personalizada para o codificar. Para a lista de todos os nomes predefinidos suportados por este `Media Encoder Standard` codificadores, consulte [Task Presets for Media Encoder Standard](media-services-mes-presets-overview.md).  
  
 Este tópico mostra a `H264 Single Bitrate 4K Audio 5.1` predefinição (em formato XML e JSON).  
  
 Esta predefinição produz um único ficheiro MP4 com um bitrate de 18000 kbps e áudio AAC 5.1. Para obter informações detalhadas sobre o perfil, o bitrate, a taxa de amostragem, etc. desta predefinição, examine o XML ou JSON definido abaixo. Para obter explicações sobre o que cada elemento significa e os valores válidos para cada elemento, consulte o [esquema padrão da Media Encoder](media-services-mes-schema.md).  
  
> [!NOTE]
>  Deve obter o tipo de unidade reservada Premium com codificações 4K. Para mais informações, consulte [Como Escalar a Codificação.](./media-services-scale-media-processing-overview.md)  
  
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
          <Bitrate>18000</Bitrate>  
          <Width>3840</Width>  
          <Height>2160</Height>  
          <FrameRate>0/1</FrameRate>  
          <Profile>Auto</Profile>  
          <Level>auto</Level>  
          <BFrames>3</BFrames>  
          <ReferenceFrames>3</ReferenceFrames>  
          <Slices>0</Slices>  
          <AdaptiveBFrame>true</AdaptiveBFrame>  
          <EntropyMode>Cabac</EntropyMode>  
          <BufferWindow>00:00:05</BufferWindow>  
          <MaxBitrate>18000</MaxBitrate>  
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
          "Bitrate": 18000,  
          "MaxBitrate": 18000,  
          "BufferWindow": "00:00:05",  
          "Width": 3840,  
          "Height": 2160,  
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
