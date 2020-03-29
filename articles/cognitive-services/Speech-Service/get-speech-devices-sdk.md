---
title: Obter o SDK de Dispositivos de Voz
titleSuffix: Azure Cognitive Services
description: O serviço Speech funciona com uma grande variedade de dispositivos e fontes de áudio. Agora, pode levar as suas aplicações de fala para o próximo nível com hardware e software combinados. Neste artigo, você vai aprender a ter acesso ao SDK de Dispositivos de Fala e começar a desenvolver- se.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: f8c1500dbbd9135a850e145199de8fea68cc4630
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220536"
---
# <a name="get-the-cognitive-services-speech-devices-sdk"></a>Obtenha os dispositivos de fala dos serviços cognitivos SDK

O SDK de Dispositivos de Fala é uma biblioteca pré-ajustada projetada para trabalhar com kits de desenvolvimento construídos de propósito e configurações variadas de matriz de microfone.

## <a name="choose-a-development-kit"></a>Escolha um kit de Desenvolvimento

|Dispositivos|Especificação|Descrição|Cenários|
|--|--|--|--|
|[Kit de Dev Dev Dev Dev Dev Dev Dev Da Roobo Smart](https://ddk.roobo.com)<br>[Configuração](speech-devices-sdk-roobo-v1.md) / [Quickstart](speech-devices-sdk-android-quickstart.md)![Roobo Smart Audio Dev Kit](media/speech-devices-sdk/device-roobo-v1.jpg)|7 Mic Array, ARM SOC, WIFI, Audio out, Io. <br>[Android](speech-devices-sdk-android-quickstart.md)|O primeiro SDK de Dispositivos de Fala para adaptar o Microsoft Mic Array e o processamento frontal SDK, para desenvolver cenários de transcrição e fala de alta qualidade|Transcrição de Conversação, Smart Speaker, Voice Agent, Wearable|
|[Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)<br>[Configuração](https://docs.microsoft.com/azure/Kinect-dk/set-up-azure-kinect-dk) / [Quickstart](speech-devices-sdk-windows-quickstart.md)![Azure Kinect DK](media/speech-devices-sdk/device-azure-kinect-dk.jpg)|7 Câmaras Mic Array RGB e Profundidade. <br>[Windows](speech-devices-sdk-windows-quickstart.md)/[Linux](speech-devices-sdk-linux-quickstart.md)|Um kit de desenvolvimento com sensores avançados de inteligência artificial (IA) para a construção de sofisticados modelos de visão computacional e fala. Combina uma matriz de microfone espacial de melhor classe e uma câmara de profundidade com uma câmara de vídeo e sensor de orientação — tudo num pequeno dispositivo com vários modos, opções e SDKs para acomodar uma gama de tipos de computação.|Transcrição de Conversas, Robótica, Edifício Inteligente|
|Kit Roobo Smart Audio Dev Kit 2<br>[Configuração](speech-devices-sdk-roobo-v2.md)<br>![Kit Roobo Smart Audio Dev Kit 2](media/speech-devices-sdk/device-roobo-v2.jpg)|7 Mic Array, ARM SOC, WIFI, Bluetooth, IO. <br>Linux|A 2ª geração de Dispositivos de Fala SDK que fornece sistemas operativos alternativos e mais funcionalidades num design de referência rentável.|Transcrição de Conversação, Smart Speaker, Voice Agent, Wearable|
|Conselho de Desenvolvimento URbetter T11![URbetter DDK](media/speech-devices-sdk/device-urbetter.jpg)|7 Mic Array, ARM SOC, WIFI, Ethernet, HDMI, CÂMARA USB. <br>Linux|Um SDK de nível da indústria que adapta a matriz do Microsoft Mic e suporta I/O estendido, como HDMI/Ethernet e mais periféricos USB|Transcrição de Conversações, Educação, Hospital, Robôs, OTT Box, Voice Agent, Drive Thru|

## <a name="download-the-speech-devices-sdk"></a>Descarregue o SDK dos Dispositivos de Fala

Descarregue o [SDK](https://aka.ms/sdsdk-download)dos Dispositivos de Fala .

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Começar com o SDK de Dispositivos de Fala](https://aka.ms/sdsdk-quickstart)
