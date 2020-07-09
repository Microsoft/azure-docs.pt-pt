---
title: Predefinições de tarefa para Media Encoder Standard (MES) / Microsoft Docs
description: O tópico dá e panorama geral das predefinições da amostra definida pelo serviço para Media Encoder Standard (MES).
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "61463408"
---
# <a name="sample-presets-for-media-encoder-standard-mes"></a>Predefinições de amostra para Media Encoder Standard (MES)

**A Media Encoder Standard** define um conjunto de predefinições pré-definidas de codificação do sistema que pode utilizar ao criar trabalhos de codificação. Recomenda-se a utilização da predefinição "Adaptive Streaming" se pretender codificar um vídeo para streaming com serviços de media. Quando especificar esta predefinição, a Media Encoder Standard [gerará automaticamente uma escada de bitrate](media-services-autogen-bitrate-ladder-with-mes.md). 

### <a name="creating-custom-presets-from-samples"></a>Criação de predefinições personalizadas a partir de amostras
Os Serviços de Comunicação Social suportam totalmente a personalização de todos os valores em predefinições para satisfazer as suas necessidades e requisitos específicos de codificação. Se precisar de personalizar uma predefinição codificante, deve começar com uma das predefinições do sistema abaixo que são fornecidas nesta secção como um modelo para a sua configuração personalizada. Para obter explicações sobre o que significa cada elemento nestas predefinições e os valores válidos para cada elemento, consulte o tópico [de esquema padrão Media Encoder.](media-services-mes-schema.md)  
  
> [!NOTE]
>  Ao utilizar uma pré-sintonia para codificações de 4k, deve obter o `S3` tipo de unidade reservada. Para mais informações, consulte [Como Escalar a Codificação.](https://azure.microsoft.com/documentation/articles/media-services-portal-encoding-units)  

#### <a name="video-rotation-default-setting-in-presets"></a>Definição padrão de rotação de vídeo em pré-sintonias:
Ao trabalhar com o Media Encoder Standard, a rotação de vídeo é ativada por padrão. Se o seu vídeo tiver sido gravado num dispositivo móvel no modo Retrato, então estas predefinições irão rodá-las para o modo Paisagem antes da codificação.
 
## <a name="available-presets"></a>Predefinições disponíveis: 

 [H264 Multiple Bitrate 1080p Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-1080p-Audio-5.1.md) produz um conjunto de 8 ficheiros MP4 alinhados com GOP, que variam de 6000 kbps a 400 kbps e áudio AAC 5.1.  
  
 [H264 Multiple Bitrate 1080p](media-services-mes-preset-H264-Multiple-Bitrate-1080p.md) produz um conjunto de 8 ficheiros MP4 alinhados com GOP, que variam de 6000 kbps a 400 kbps, e áudio AAC estéreo.  
  
 [H264 Multiple Bitrate 16x9 para iOS](media-services-mes-preset-H264-Multiple-Bitrate-16x9-for-iOS.md) produz um conjunto de 8 ficheiros MP4 alinhados com GOP, que variam de 8500 kbps a 200 kbps, e áudio AAC estéreo.  
  
 [H264 Multiple Bitrate 16x9 SD Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD-Audio-5.1.md) produz um conjunto de 5 ficheiros MP4 alinhados com GOP, que variam de 1900 kbps a 400 kbps e áudio AAC 5.1.  
  
 [H264 Multiple Bitrate 16x9 SD](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD.md) produz um conjunto de 5 ficheiros MP4 alinhados com GOP, que variam de 1900 kbps a 400 kbps, e áudio Estéreo AAC.  
  
 [H264 Multiple Bitrate 4K Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4K-Audio-5.1.md) produz um conjunto de 12 ficheiros MP4 alinhados com GOP, que variam entre 20000 kbps e 1000 kbps e áudio AAC 5.1.  
  
 [H264 Multiple Bitrate 4K](media-services-mes-preset-H264-Multiple-Bitrate-4K.md) produz um conjunto de 12 ficheiros MP4 alinhados com GOP, que variam de 20000 kbps a 1000 kbps, e áudio AAC estéreo.  
  
 [H264 Multiple Bitrate 4x3 para iOS](media-services-mes-preset-H264-Multiple-Bitrate-4x3-for-iOS.md) produz um conjunto de 8 ficheiros MP4 alinhados com GOP, que variam de 8500 kbps a 200 kbps, e áudio Estéreo AAC.  
  
 [H264 Multiple Bitrate 4x3 SD Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD-Audio-5.1.md) produz um conjunto de 5 ficheiros MP4 alinhados com GOP, que variam de 1600 kbps a 400 kbps e áudio AAC 5.1.  
  
 [H264 Multiple Bitrate 4x3 SD](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD.md) produz um conjunto de 5 ficheiros MP4 alinhados com GOP, que variam de 1600 kbps a 400 kbps, e áudio AAC estéreo.  
  
 [H264 Multiple Bitrate 720p Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-720p-Audio-5.1.md) produz um conjunto de 6 ficheiros MP4 alinhados com GOP, que variam de 3400 kbps a 400 kbps e áudio AAC 5.1.  
  
 [H264 Multiple Bitrate 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) produz um conjunto de 6 ficheiros MP4 alinhados com GOP, que variam de 3400 kbps a 400 kbps, e áudio AAC estéreo.  
  
 [H264 Single Bitrate 1080p Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-1080p-Audio-5.1.md) produz um único ficheiro MP4 com um bitrate de 6750 kbps e áudio AAC 5.1.  
  
 [H264 Single Bitrate 1080p](media-services-mes-preset-H264-Single-Bitrate-1080p.md) produz um único ficheiro MP4 com um bitrate de 6750 kbps, e áudio AAC estéreo.  
  
 [H264 Single Bitrate 4K Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-4K-Audio-5.1.md) produz um único ficheiro MP4 com um bitrate de 18000 kbps e áudio AAC 5.1.  
  
 [H264 Single Bitrate 4K](media-services-mes-preset-H264-Single-Bitrate-4K.md) produz um único ficheiro MP4 com um bitrate de 18000 kbps, e áudio AAC estéreo.  
  
 [H264 Single Bitrate 4x3 SD Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-4x3-SD-Audio-5.1.md) produz um único ficheiro MP4 com um bitrate de 1800 kbps e áudio AAC 5.1.  
  
 [H264 Single Bitrate 4x3 SD](media-services-mes-preset-H264-Single-Bitrate-4x3-SD.md) produz um único ficheiro MP4 com um bitrate de 1800 kbps, e áudio AAC estéreo.  
  
 [H264 Single Bitrate 16x9 SD Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-16x9-SD-Audio-5.1.md) produz um único ficheiro MP4 com um bitrate de 2200 kbps e áudio AAC 5.1.  
  
 [H264 Single Bitrate 16x9 SD](media-services-mes-preset-H264-Single-Bitrate-16x9-SD.md) produz um único ficheiro MP4 com um bitrate de 2200 kbps, e áudio AAC estéreo.  
  
 [H264 Single Bitrate 720p Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-720p-Audio-5.1.md) produz um único ficheiro MP4 com um bitrate de 4500 kbps e áudio AAC 5.1.  
  
 [H264 Single Bitrate 720p para o](media-services-mes-preset-H264-Single-Bitrate-720p-for-Android.md) predefinição Android produz um único ficheiro MP4 com um bitrate de 2000 kbps, e estéreo AAC.  
  
 [H264 Single Bitrate 720p](media-services-mes-preset-H264-Single-Bitrate-720p.md) produz um único ficheiro MP4 com um bitrate de 4500 kbps, e áudio AAC estéreo.  
  
 [H264 Single Bitrate High Quality SD para Android](media-services-mes-preset-H264-Single-Bitrate-High-Quality-SD-for-Android.md) produz um único ficheiro MP4 com um bitrate de 500 kbps, e som Estéreo AAC áudio..  
  
 [H264 Single Bitrate Low Quality SD para Android](media-services-mes-preset-H264-Single-Bitrate-Low-Quality-SD-for-Android.md) produz um único ficheiro MP4 com um bitrate de 56 kbps, e áudio AAC estéreo.  
  
 Para obter mais informações relacionadas com os codificadores dos Media Services, consulte [a Codificação a Pedido com a Azure Media Services](https://azure.microsoft.com/documentation/articles/media-services-encode-asset/).
