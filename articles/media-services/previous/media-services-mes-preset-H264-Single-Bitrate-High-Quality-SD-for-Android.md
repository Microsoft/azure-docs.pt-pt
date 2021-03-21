---
title: H264 Single Bitrate High Quality SD para Android | Microsoft Docs
description: O tópico apresenta uma visão geral do **SD de alta qualidade de Bitrate Single Bitrate H264 para** a predefinição da tarefa Android.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 4a190f24-ec6a-47e7-8bca-6294e064b15b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 6787323a0923a9a13b3d22079b0e958a7633b995
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103011736"
---
# <a name="h264-single-bitrate-high-quality-sd-for-android"></a>H264 Taxa de Bits Única de Alta Qualidade SD para Android

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

`Media Encoder Standard` define um conjunto de predefinições codificantes que pode usar ao criar trabalhos de codificação. Pode utilizar um `preset name` para especificar em que formato gostaria de codificar o seu ficheiro de mídia. Ou, pode criar as suas próprias predefinições baseadas em JSON ou XML (utilizando codificação UTF-8 ou UTF-16. Passaria então a predefinição personalizada para o codificar. Para a lista de todos os nomes predefinidos suportados por este `Media Encoder Standard` codificadores, consulte [Task Presets for Media Encoder Standard](media-services-mes-presets-overview.md).  
  
 Este tópico mostra a `H264 Single Bitrate High Quality SD for Android` predefinição no formato XML e JSON.  
  
 Esta predefinição produz um único ficheiro MP4 com um bitrate de 500 kbps, e áudio AAC estéreo. Para obter informações detalhadas sobre o perfil, o bitrate, a taxa de amostragem, etc. desta predefinição, examine o XML ou JSON definido abaixo. Para obter explicações sobre o que significa cada elemento nestas predefinições e os valores válidos para cada elemento, consulte o tópico [de esquema padrão Media Encoder.](media-services-mes-schema.md)  
  
 XML  
  
```  
<?xml version="1.0" encoding="utf-16"?>  
<Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">  
  <Encoding>  
    <H264Video>  
      <KeyFrameInterval>00:00:05</KeyFrameInterval>  
      <SceneChangeDetection>true</SceneChangeDetection>  
      <H264Layers>  
        <H264Layer>  
          <Bitrate>500</Bitrate>  
          <Width>480</Width>  
          <Height>360</Height>  
          <FrameRate>0/1</FrameRate>  
          <Profile>Baseline</Profile>  
          <Level>3</Level>  
          <BFrames>0</BFrames>  
          <ReferenceFrames>3</ReferenceFrames>  
          <Slices>0</Slices>  
          <AdaptiveBFrame>false</AdaptiveBFrame>  
          <EntropyMode>Cavlc</EntropyMode>  
          <BufferWindow>00:00:05</BufferWindow>  
          <MaxBitrate>500</MaxBitrate>  
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
      "KeyFrameInterval": "00:00:05",  
      "SceneChangeDetection": true,  
      "H264Layers": [  
        {  
          "Profile": "Baseline",  
          "Level": "3",  
          "Bitrate": 500,  
          "MaxBitrate": 500,  
          "BufferWindow": "00:00:05",  
          "Width": 480,  
          "Height": 360,  
          "ReferenceFrames": 3,  
          "EntropyMode": "Cavlc",  
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
