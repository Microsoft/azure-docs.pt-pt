---
title: H264 taxa de bits única 1080p predefinir Media Encoder Standard - Azure | Documentos da Microsoft
description: O tópico fornece uma visão geral do **h264 taxa de bits única 1080p** predefinido de tarefa.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 0951fea8-15af-420b-9648-8c5c1abf8173
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 031718ce7c3e728de967d2e3502fc56baf3acde6
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57849895"
---
# <a name="h264-single-bitrate-1080p"></a>H264 taxa de bits Única 1080p
`Media Encoder Standard` Define um conjunto de codificação configurações predefinidas que pode utilizar durante a criação de tarefas de codificação. Pode utilizar um `preset name` para especificar em que formato gostaria de codificar o ficheiro de suporte de dados. Em alternativa, pode criar seu próprio JSON ou XML com base em suas configurações predefinidas (usando a codificação UTF-8 ou UTF-16. Em seguida, transmite o personalizado predefinido para o codificador. Para obter a lista de todos os nomes predefinidos suportados por este `Media Encoder Standard` codificador, consulte [predefinições de tarefas para o Media Encoder Standard](media-services-mes-presets-overview.md).  
  
 Este tópico mostra o `H264 Single Bitrate 1080p` predefinidos no formato XML e JSON.  
  
 Este ficheiro predefinidas produz um único MP4 com velocidade de transmissão de 6750 kbps e áudio AAC estéreo. Para obter informações detalhadas sobre o perfil, a velocidade de transmissão, amostragem taxa, etc. isso configuração predefinida, examinar o XML ou JSON definido abaixo. Para obter explicações de que cada elemento desses meios de suas configurações predefinidas e os valores válidos para cada elemento, consulte a [esquema Media Encoder Standard](media-services-mes-schema.md) tópico.  
  
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
          <Bitrate>6750</Bitrate>  
          <Width>1920</Width>  
          <Height>1080</Height>  
          <FrameRate>0/1</FrameRate>  
          <Profile>Auto</Profile>  
          <Level>auto</Level>  
          <BFrames>3</BFrames>  
          <ReferenceFrames>3</ReferenceFrames>  
          <Slices>0</Slices>  
          <AdaptiveBFrame>true</AdaptiveBFrame>  
          <EntropyMode>Cabac</EntropyMode>  
          <BufferWindow>00:00:05</BufferWindow>  
          <MaxBitrate>6750</MaxBitrate>  
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
          "Bitrate": 6750,  
          "MaxBitrate": 6750,  
          "BufferWindow": "00:00:05",  
          "Width": 1920,  
          "Height": 1080,  
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
