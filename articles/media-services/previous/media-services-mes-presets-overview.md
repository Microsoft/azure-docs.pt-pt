---
title: Predefinições de tarefas para media codificador standard (MES) [ Microsoft Docs
description: O tópico dá e visão geral das predefinições de amostra definidas pelo serviço para Media Encoder Standard (MES).
author: Juliako
manager: femila
editor: johndeu
services: media-services
documentationcenter: ''
ms.assetid: f243ed1c-ac9c-4300-a5f7-f092cf9853b9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 50c52369a5a957a4dd6279cac5079e2dea023106
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "61463408"
---
# <a name="sample-presets-for-media-encoder-standard-mes"></a>Predefinições de amostra para media codificador standard (MES)

**Media Encoder Standard** define um conjunto de predefinições de codificação de sistemas pré-definidos que pode usar na criação de trabalhos de codificação. Recomenda-se utilizar o predefinido "Adaptive Streaming" se pretender codificar um vídeo para streaming com os Media Services. Quando especificar este predefinido, o Media Encoder Standard [gerará automaticamente uma escada bitrate](media-services-autogen-bitrate-ladder-with-mes.md). 

### <a name="creating-custom-presets-from-samples"></a>Criação de predefinições personalizadas a partir de amostras
A Media Services suporta totalmente a personalização de todos os valores em predefinições para satisfazer as suas necessidades e requisitos específicos de codificação. Se precisar de personalizar um preset de codificação, deve começar com uma das predefinições do sistema abaixo que são fornecidas nesta secção como um modelo para a sua configuração personalizada. Para explicações sobre o que cada elemento nestas predefinições significa, e os valores válidos para cada elemento, consulte o tópico [de esquema sema media Encoder Standard.](media-services-mes-schema.md)  
  
> [!NOTE]
>  Ao utilizar um predefinido para codificações de `S3` 4k, deve obter o tipo de unidade reservado. Para mais informações, consulte [Como Escalar a Codificação](https://azure.microsoft.com/documentation/articles/media-services-portal-encoding-units).  

#### <a name="video-rotation-default-setting-in-presets"></a>Definição predefinida de rotação de vídeo em predefinições:
Ao trabalhar com o Media Encoder Standard, a rotação de vídeo é ativada por padrão. Se o seu vídeo tiver sido gravado num dispositivo móvel no modo Retrato, estas predefinições irão rodá-los para o modo Paisagem antes de codificar.
 
## <a name="available-presets"></a>Presets disponíveis: 

 [H264 Multiple Bitrate 1080p Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-1080p-Audio-5.1.md) produz um conjunto de 8 ficheiros MP4 alinhados com GOP, que variam entre 6000 kbps e 400 kbps e áudio AAC 5.1.  
  
 [H264 Multiple Bitrate 1080p](media-services-mes-preset-H264-Multiple-Bitrate-1080p.md) produz um conjunto de 8 ficheiros MP4 alinhados com GOP, que variam entre 6000 kbps e 400 kbps, e áudio AAC estéreo.  
  
 [H264 Multiple Bitrate 16x9 para iOS](media-services-mes-preset-H264-Multiple-Bitrate-16x9-for-iOS.md) produz um conjunto de 8 ficheiros MP4 alinhados com GOP, que variam entre 8500 kbps e 200 kbps, e áudio AAC estéreo.  
  
 [H264 Multiple Bitrate 16x9 SD Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD-Audio-5.1.md) produz um conjunto de 5 ficheiros MP4 alinhados com GOP, que variam entre 1900 kbps e 400 kbps e áudio AAC 5.1.  
  
 [H264 Multiple Bitrate 16x9 SD](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD.md) produz um conjunto de 5 ficheiros MP4 alinhados com GOP, que variam entre 1900 kbps e 400 kbps, e áudio AAC estéreo.  
  
 [H264 Multiple Bitrate 4K Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4K-Audio-5.1.md) produz um conjunto de 12 ficheiros MP4 alinhados com GOP, que variam entre 20000 kbps e 1000 kbps e áudio AAC 5.1.  
  
 [H264 Multiple Bitrate 4K](media-services-mes-preset-H264-Multiple-Bitrate-4K.md) produz um conjunto de 12 ficheiros MP4 alinhados com GOP, que variam de 20000 kbps a 1000 kbps, e áudio AAC estéreo.  
  
 [H264 Multiple Bitrate 4x3 para iOS](media-services-mes-preset-H264-Multiple-Bitrate-4x3-for-iOS.md) produz um conjunto de 8 ficheiros MP4 alinhados com GOP, que variam entre 8500 kbps e 200 kbps, e áudio AAC estéreo.  
  
 [H264 Multiple Bitrate 4x3 SD Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD-Audio-5.1.md) produz um conjunto de 5 ficheiros MP4 alinhados com GOP, que variam entre 1600 kbps e 400 kbps e áudio AAC 5.1.  
  
 [H264 Multiple Bitrate 4x3 SD](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD.md) produz um conjunto de 5 ficheiros MP4 alinhados com GOP, que variam entre 1600 kbps e 400 kbps, e áudio AAC estéreo.  
  
 [H264 Multiple Bitrate 720p Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-720p-Audio-5.1.md) produz um conjunto de 6 ficheiros MP4 alinhados com GOP, que variam entre 3400 kbps e 400 kbps e áudio AAC 5.1.  
  
 [H264 Multiple Bitrate 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) produz um conjunto de 6 ficheiros MP4 alinhados com GOP, que variam entre 3400 kbps e 400 kbps, e áudio AAC estéreo.  
  
 [H264 Single Bitrate 1080p Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-1080p-Audio-5.1.md) produz um único ficheiro MP4 com uma bitrate de 6750 kbps e áudio AAC 5.1.  
  
 [H264 Single Bitrate 1080p](media-services-mes-preset-H264-Single-Bitrate-1080p.md) produz um único ficheiro MP4 com uma bitrate de 6750 kbps e áudio AAC estéreo.  
  
 [H264 Single Bitrate 4K Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-4K-Audio-5.1.md) produz um único ficheiro MP4 com uma bitrate de 18000 kbps e áudio AAC 5.1.  
  
 [H264 Single Bitrate 4K](media-services-mes-preset-H264-Single-Bitrate-4K.md) produz um único ficheiro MP4 com uma bitrate de 18000 kbps e áudio AAC estéreo.  
  
 [H264 Single Bitrate 4x3 SD Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-4x3-SD-Audio-5.1.md) produz um único ficheiro MP4 com uma bitrate de 1800 kbps e áudio AAC 5.1.  
  
 [H264 Single Bitrate 4x3 SD](media-services-mes-preset-H264-Single-Bitrate-4x3-SD.md) produz um único ficheiro MP4 com uma bitrate de 1800 kbps e áudio AAC estéreo.  
  
 [H264 Single Bitrate 16x9 SD Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-16x9-SD-Audio-5.1.md) produz um único ficheiro MP4 com uma bitrate de 2200 kbps e áudio AAC 5.1.  
  
 [H264 Single Bitrate 16x9 SD](media-services-mes-preset-H264-Single-Bitrate-16x9-SD.md) produz um único ficheiro MP4 com uma bitrate de 2200 kbps e áudio AAC estéreo.  
  
 [H264 Single Bitrate 720p Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-720p-Audio-5.1.md) produz um único ficheiro MP4 com uma bitrate de 4500 kbps e áudio AAC 5.1.  
  
 [H264 Single Bitrate 720p para pré-definido Android](media-services-mes-preset-H264-Single-Bitrate-720p-for-Android.md) produz um único ficheiro MP4 com um bitrate de 2000 kbps, e a aparelhagem AAC.  
  
 [H264 Single Bitrate 720p](media-services-mes-preset-H264-Single-Bitrate-720p.md) produz um único ficheiro MP4 com uma bitrate de 4500 kbps e áudio AAC estéreo.  
  
 [H264 Single Bitrate High Quality SD for Android](media-services-mes-preset-H264-Single-Bitrate-High-Quality-SD-for-Android.md) produz um único ficheiro MP4 com uma bitrate de 500 kbps e áudio AAC estéreo.  
  
 [H264 Single Bitrate Low Quality SD for Android](media-services-mes-preset-H264-Single-Bitrate-Low-Quality-SD-for-Android.md) produz um único ficheiro MP4 com um bitrate de 56 kbps e áudio AAC estéreo.  
  
 Para obter mais informações relacionadas com os media services, consulte [A Codificação On-Demand com](https://azure.microsoft.com/documentation/articles/media-services-encode-asset/)a Azure Media Services .
