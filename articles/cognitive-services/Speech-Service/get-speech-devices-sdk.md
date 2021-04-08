---
title: Obter o SDK de Dispositivos de Voz
titleSuffix: Azure Cognitive Services
description: O serviço Speech funciona com uma grande variedade de dispositivos e fontes de áudio. Agora, pode levar as suas aplicações de discurso para o próximo nível com hardware e software combinados. Neste artigo, você vai aprender a ter acesso aos Dispositivos de Fala SDK e começar a desenvolver-se.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/14/2019
ms.author: erhopf
ms.openlocfilehash: 69898e64934c363a18a3ce2fa5e678116624bd24
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "95026374"
---
# <a name="get-the-cognitive-services-speech-devices-sdk"></a>Obtenha os dispositivos de fala de serviços cognitivos SDK

O Speech Devices SDK é uma biblioteca pré-sintonizada projetada para trabalhar com kits de desenvolvimento construídos de propósito e configurações variadas de matriz de microfone.

## <a name="choose-a-development-kit"></a>Escolha um kit de desenvolvimento

|Dispositivos|Especificação|Description|Cenários|
|--|--|--|--|
|[Urbetter Dev Kit](http://www.urbetter.com/products_56/278.html) ![ URbetter DDK](media/speech-devices-sdk/device-urbetter.jpg)|7 Matriz mic, ARM SOC, WIFI, Ethernet, HDMI, Câmara USB. <br>Linux|Um SDK de dispositivos de fala de nível industrial que adapta a matriz do Microsoft Mic e suporta erupções de E/S estendidas como HDMI/Ethernet e mais periféricos USB <br> [Entrar em contato urbetter](http://www.urbetter.com/products_56/278.html)|Transcrição de conversação, Educação, Hospital, Robôs, Caixa OTT, Agente de Voz, Drive Thru|
|[Roobo Smart Audio Dev Kit](http://ddk.roobo.com)<br>[Configuração](speech-devices-sdk-roobo-v1.md)  /  [Arranque rápido](./speech-devices-sdk-quickstart.md?pivots=platform-android%253fpivots%253dplatform-android) ![ Roobo Smart Audio Dev Kit](media/speech-devices-sdk/device-roobo-v1.jpg)|7 Matriz de microfone, ARM SOC, WIFI, Audio out, IO. <br>[Android](./speech-devices-sdk-quickstart.md?pivots=platform-android%253fpivots%253dplatform-android)|O primeiro SDK de dispositivos de fala para adaptar o Microsoft Mic Array e o processamento frontal SDK, para desenvolver cenários de transcrição e fala de alta qualidade|Transcrição de conversação, altifalante inteligente, agente de voz, wearable|
|[Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)<br>[Configuração](../../kinect-dk/set-up-azure-kinect-dk.md)  /  [Arranque rápido](./speech-devices-sdk-quickstart.md?pivots=platform-windows%253fpivots%253dplatform-windows) ![ Azure Kinect DK](media/speech-devices-sdk/device-azure-kinect-dk.jpg)|7 Mic Array RGB e Câmaras de Profundidade. <br>[Janelas](./speech-devices-sdk-quickstart.md?pivots=platform-windows%253fpivots%253dplatform-windows) / [Linux](./speech-devices-sdk-quickstart.md?pivots=platform-linux%253fpivots%253dplatform-linux)|Um kit de desenvolvedor com sensores avançados de inteligência artificial (IA) para a construção de modelos sofisticados de visão computacional e de fala. Combina uma matriz de microfone espacial de melhor classe e câmara de profundidade com uma câmara de vídeo e sensor de orientação - tudo num pequeno dispositivo com vários modos, opções e SDKs para acomodar uma gama de tipos de computação.|Transcrição de conversação, robótica, edifício inteligente|
|Roobo Smart Audio Dev Kit 2<br>[Configuração](speech-devices-sdk-roobo-v2.md)<br>![Roobo Smart Audio Dev Kit 2](media/speech-devices-sdk/device-roobo-v2.jpg)|7 Matriz mic, ARM SOC, WIFI, Bluetooth, IO. <br>Linux|A 2ª geração de Dispositivos de Fala SDK que fornece um SISTEMA alternativo e mais funcionalidades num design de referência rentável.|Transcrição de conversação, altifalante inteligente, agente de voz, wearable|


## <a name="download-the-speech-devices-sdk"></a>Descarregue os Dispositivos de Fala SDK

Descarregue os [Dispositivos de Fala SDK](./speech-devices-sdk.md).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Começar com os Dispositivos de Fala SDK](./speech-devices-sdk-quickstart.md?pivots=platform-android)