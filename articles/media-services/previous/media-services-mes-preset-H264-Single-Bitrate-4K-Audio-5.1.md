---
title: H264 taxa de bits Única 4K Audio 5.1 | Documentos da Microsoft
description: O tópico fornece uma visão geral do **h264 taxa de bits única 4K Audio 5.1** predefinido de tarefa.
author: Juliako
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
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 9c4b133ae74438def30f4fcd9e49a5ca5e6f9cd4
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58199805"
---
# <a name="h264-single-bitrate-4k-audio-51"></a>H264 taxa de bits Única 4K Audio 5.1
`Media Encoder Standard` Define um conjunto de codificação configurações predefinidas que pode utilizar durante a criação de tarefas de codificação. Pode utilizar um `preset name` para especificar em que formato gostaria de codificar o ficheiro de suporte de dados. Em alternativa, pode criar seu próprio JSON ou XML com base em suas configurações predefinidas (usando a codificação UTF-8 ou UTF-16. Em seguida, transmite o personalizado predefinido para o codificador. Para obter a lista de todos os nomes predefinidos suportados por este `Media Encoder Standard` codificador, consulte [predefinições de tarefas para o Media Encoder Standard](media-services-mes-presets-overview.md).  
  
 Este tópico mostra o `H264 Single Bitrate 4K Audio 5.1` predefinido (no formato XML e JSON).  
  
 Esta predefinição produz um único ficheiro MP4 com velocidade de transmissão de 18000 kbps e áudio AAC 5.1. Para obter informações detalhadas sobre o perfil, a velocidade de transmissão, amostragem taxa, etc. isso configuração predefinida, examinar o XML ou JSON definido abaixo. Para obter explicações de significa que cada elemento e os valores válidos para cada elemento, consulte a [esquema Media Encoder Standard](media-services-mes-schema.md).  
  
> [!NOTE]
>  Deve obter a unidade reservada Premium codifica de tipo com 4K. Para obter mais informações, consulte [como a codificação de escala](https://azure.microsoft.com/documentation/articles/media-services-portal-encoding-units).  
  
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
