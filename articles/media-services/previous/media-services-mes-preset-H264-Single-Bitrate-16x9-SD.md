---
title: H264 taxa de bits única 16x9 SD Media Encoder Standard configuração predefinida - Azure | Documentos da Microsoft
description: O tópico fornece uma visão geral do **h264 taxa de bits única 16x9 SD** predefinido de tarefa.
author: WenJason
manager: digimobile
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: ce0efc07-3461-44f6-a7bc-c4877bc09529
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 03/19/2019
ms.date: 04/01/2019
ms.author: v-jay
ms.openlocfilehash: fcc370afeca41a8d1d227db368eeaba789c56137
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61129530"
---
# <a name="h264-single-bitrate-16x9-sd"></a>H264 Taxa de Bits Única 16x9 SD
`Media Encoder Standard` Define um conjunto de codificação configurações predefinidas que pode utilizar durante a criação de tarefas de codificação. Pode utilizar um `preset name` para especificar em que formato gostaria de codificar o ficheiro de suporte de dados. Em alternativa, pode criar seu próprio JSON ou XML com base em suas configurações predefinidas (usando a codificação UTF-8 ou UTF-16. Em seguida, transmite o personalizado predefinido para o codificador. Para obter a lista de todos os nomes predefinidos suportados por este `Media Encoder Standard` codificador, consulte [predefinições de tarefas para o Media Encoder Standard](media-services-mes-presets-overview.md).  
  
 Este tópico mostra o `H264 Single Bitrate 16x9 SD` predefinidos no formato XML e JSON.  
  
 Este ficheiro predefinidas produz um único MP4 com velocidade de transmissão de 2200 kbps e áudio AAC estéreo. Para obter informações detalhadas sobre o perfil, a velocidade de transmissão, amostragem taxa, etc. isso configuração predefinida, examinar o XML ou JSON definido abaixo. Para obter explicações de que cada elemento desses meios de suas configurações predefinidas e os valores válidos para cada elemento, consulte a [esquema Media Encoder Standard](media-services-mes-schema.md) tópico.  
  
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
