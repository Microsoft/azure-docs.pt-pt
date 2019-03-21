---
title: Predefinições de tarefas para o Media Encoder Standard (MES) | Documentos da Microsoft
description: O tópico apresenta e descrição geral do exemplo definidas pelo serviço de predefinições para o Media Encoder Standard (MES).
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
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58260031"
---
# <a name="sample-presets-for-media-encoder-standard-mes"></a>Configurações predefinidas de exemplo para o Media Encoder Standard (MES)

**Codificador de multimédia Standard** define um conjunto de sistema predefinido de codificação configurações predefinidas que pode utilizar durante a criação de tarefas de codificação. Recomenda-se para utilizar o "transmissão em fluxo adaptável" configuração predefinida para codificar um vídeo para transmissão em fluxo com os serviços de multimédia. Se especificar esta predefinição, irá Media Encoder Standard [gerar automaticamente uma escala de bits](media-services-autogen-bitrate-ladder-with-mes.md). 

### <a name="creating-custom-presets-from-samples"></a>Criar configurações predefinidas personalizadas a partir de exemplos
Serviços de multimédia suporta totalmente a personalizar todos os valores nas predefinições para atender às suas necessidades específicas de codificação e requisitos. Se precisar de personalizar uma predefinição de codificação, deve começar com um do abaixo predefinições do sistema que são fornecidas nesta secção como um modelo para a sua configuração personalizada. Para obter explicações de que cada elemento desses meios de suas configurações predefinidas e os valores válidos para cada elemento, consulte a [esquema Media Encoder Standard](media-services-mes-schema.md) tópico.  
  
> [!NOTE]
>  Quando utilizar uma configuração predefinida para codifica 4K, obterá o `S3` reservados de tipo de unidade. Para obter mais informações, consulte [como a codificação de escala](https://azure.microsoft.com/documentation/articles/media-services-portal-encoding-units).  

#### <a name="video-rotation-default-setting-in-presets"></a>Configuração de padrão de rotação de vídeo em suas configurações predefinidas:
Ao trabalhar com o Media Encoder Standard, a rotação de vídeo está ativada por predefinição. Se o seu vídeo foi registrado num dispositivo móvel no modo retrato, em seguida, essas configurações predefinidas de irão girá-los para modo paisagem antes da codificação.
 
## <a name="available-presets"></a>Configurações de predefinidas disponíveis: 

 [H264 Multiple Bitrate 1080p Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-1080p-Audio-5.1.md) produz um conjunto de ficheiros MP4 de alinhado GOP de 8, desde 6000 kbps até 400 kbps e áudio AAC 5.1.  
  
 [H264 Multiple Bitrate 1080p](media-services-mes-preset-H264-Multiple-Bitrate-1080p.md) produz um conjunto de ficheiros MP4 de alinhado GOP de 8, desde 6000 kbps até 400 kbps e áudio AAC estéreo.  
  
 [H264 taxa de bits múltiplas 16x9 para iOS](media-services-mes-preset-H264-Multiple-Bitrate-16x9-for-iOS.md) produz um conjunto de ficheiros MP4 de alinhado GOP de 8, desde 8500 kbps até 200 kbps e áudio AAC estéreo.  
  
 [H264 taxa de bits múltiplas 16x9 SD Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD-Audio-5.1.md) produz um conjunto de ficheiros MP4 de alinhado GOP de 5, desde kbps de 1900 até 400 kbps e áudio AAC 5.1.  
  
 [H264 taxa de bits múltiplas 16x9 SD](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD.md) produz um conjunto de ficheiros MP4 de alinhado GOP de 5, desde kbps de 1900 até 400 kbps e áudio AAC estéreo.  
  
 [H264 taxa de bits múltiplas 4K Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4K-Audio-5.1.md) produz um conjunto de 12 ficheiros MP4 alinhado GOP, desde 20000 kbps até 1000 kbps e áudio AAC 5.1.  
  
 [H264 Multiple Bitrate 4K](media-services-mes-preset-H264-Multiple-Bitrate-4K.md) produz um conjunto de 12 ficheiros MP4 alinhado GOP, desde 20000 kbps até 1000 kbps e áudio AAC estéreo.  
  
 [H264 taxa de bits múltiplas 4x3 para iOS](media-services-mes-preset-H264-Multiple-Bitrate-4x3-for-iOS.md) produz um conjunto de ficheiros MP4 de alinhado GOP de 8, desde 8500 kbps até 200 kbps e áudio AAC estéreo.  
  
 [H264 taxa de bits múltiplas 4x3 SD Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD-Audio-5.1.md) produz um conjunto de ficheiros MP4 de alinhado GOP de 5, desde 1600 kbps até 400 kbps e áudio AAC 5.1.  
  
 [H264 taxa de bits múltiplas 4x3 SD](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD.md) produz um conjunto de ficheiros MP4 de alinhado GOP de 5, desde 1600 kbps até 400 kbps e áudio AAC estéreo.  
  
 [H264 taxa de bits múltiplas 720p Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-720p-Audio-5.1.md) produz um conjunto de 6 ficheiros MP4 GOP alinhados, desde 3400 kbps até 400 kbps e áudio AAC 5.1.  
  
 [H264 taxa de bits múltiplas 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) produz um conjunto de 6 ficheiros MP4 GOP alinhados, desde 3400 kbps até 400 kbps e áudio AAC estéreo.  
  
 [H264 taxa de bits única 1080p Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-1080p-Audio-5.1.md) produz um único ficheiro MP4 com velocidade de transmissão de 6750 kbps e áudio AAC 5.1.  
  
 [H264 taxa de bits única 1080p](media-services-mes-preset-H264-Single-Bitrate-1080p.md) produz um único ficheiro MP4 com velocidade de transmissão de 6750 kbps e áudio AAC estéreo.  
  
 [H264 taxa de bits única 4K Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-4K-Audio-5.1.md) produz um único ficheiro MP4 com velocidade de transmissão de 18000 kbps e áudio AAC 5.1.  
  
 [H264 taxa de bits única com velocidade de transmissão 4K](media-services-mes-preset-H264-Single-Bitrate-4K.md) produz um único ficheiro MP4 com velocidade de transmissão de 18000 kbps e áudio AAC estéreo.  
  
 [H264 taxa de bits única 4x3 SD Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-4x3-SD-Audio-5.1.md) produz um único ficheiro MP4 com velocidade de transmissão de 1800 kbps e áudio AAC 5.1.  
  
 [H264 taxa de bits única 4x3 SD](media-services-mes-preset-H264-Single-Bitrate-4x3-SD.md) produz um único ficheiro MP4 com velocidade de transmissão de 1800 kbps e áudio AAC estéreo.  
  
 [H264 taxa de bits única 16x9 SD Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-16x9-SD-Audio-5.1.md) produz um único ficheiro MP4 com velocidade de transmissão de 2200 kbps e áudio AAC 5.1.  
  
 [H264 taxa de bits única 16x9 SD](media-services-mes-preset-H264-Single-Bitrate-16x9-SD.md) produz um único ficheiro MP4 com velocidade de transmissão de 2200 kbps e áudio AAC estéreo.  
  
 [H264 taxa de bits única 720p Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-720p-Audio-5.1.md) produz um único ficheiro MP4 com velocidade de transmissão de 4500 kbps e áudio AAC 5.1.  
  
 [H264 taxa de bits única 720p para Android](media-services-mes-preset-H264-Single-Bitrate-720p-for-Android.md) predefinição produz um único ficheiro MP4 com velocidade de transmissão de 2000 kbps e AAC estéreo.  
  
 [H264 taxa de bits única 720p](media-services-mes-preset-H264-Single-Bitrate-720p.md) produz um único ficheiro MP4 com velocidade de transmissão de 4500 kbps e áudio AAC estéreo.  
  
 [H264 taxa de bits única de velocidade de transmissão de alta qualidade SD para Android](media-services-mes-preset-H264-Single-Bitrate-High-Quality-SD-for-Android.md) produz um único ficheiro MP4 com velocidade de transmissão de 500 kbps e áudio AAC estéreo....  
  
 [H264 taxa de bits única de velocidade de transmissão baixa qualidade SD para Android](media-services-mes-preset-H264-Single-Bitrate-Low-Quality-SD-for-Android.md) produz um único ficheiro MP4 com velocidade de transmissão de 56 kbps e áudio AAC estéreo.  
  
 Para obter mais informações relacionadas aos codificadores de serviços de multimédia, veja [codificação a pedido com os serviços de multimédia do Azure](https://azure.microsoft.com/documentation/articles/media-services-encode-asset/).
