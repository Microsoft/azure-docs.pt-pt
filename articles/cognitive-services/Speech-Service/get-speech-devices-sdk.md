---
title: Obter o SDK de Dispositivos de Voz
titleSuffix: Azure Cognitive Services
description: Os serviços de voz funcionam com uma ampla variedade de dispositivos e fontes de áudio. Agora, pode tirar seus aplicativos de fala para o próximo nível com correspondentes de hardware e software. Neste artigo, aprenderá como obter acesso para o SDK de dispositivos de voz e começar a desenvolver.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: b9a0890000cda0b3663ac29bee61fc1c702f6254
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65410694"
---
# <a name="get-the-cognitive-services-speech-devices-sdk"></a>Obter os dispositivos de voz dos serviços cognitivos SDK

O SDK de dispositivos de voz é uma biblioteca pretuned concebida para trabalhar com os SDKs e várias configurações de matriz de microfone.

## <a name="choose-a-development-kit"></a>Escolha um kit de desenvolvimento

|Dispositivos|Especificação|Descrição|Cenários|
|--|--|--|--|
|[Kit de programação de áudio de Roobo inteligente](https://ddk.roobo.com)</br>[Programa de configuração](speech-devices-sdk-roobo-v1.md) / [guia de início rápido](speech-devices-sdk-android-quickstart.md)![Kit de programação de áudio de Roobo inteligente](media/speech-devices-sdk/device-roobo-v1.jpg)|7 de matriz de mic, ARM SOC, de Wi-Fi, fora de áudio, e/s. </br>Android|O SDK de dispositivos de voz primeiro adaptar Array de Mic do Microsoft e front processamento SDK, para o desenvolvimento de cenários de transcrição e de voz de alta qualidade|Transcrição da conversação, o orador inteligente, agente, os de voz|
|[Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)![Azure Kinect DK](media/speech-devices-sdk/device-azure-kinect-dk.jpg)|7 câmeras RGB de matriz de mic e profundidade. </br>Windows/Linux|Um kit de programação com sensores de inteligência artificial (IA) para criar modelos de computador sofisticados e de voz. Ele combina uma câmera de matriz e a profundidade de melhor na classe de microfone espaciais com uma câmara de vídeo e um sensor de orientação — tudo num dispositivo pequeno com vários modos, opções e SDKs para acomodar uma gama de tipos de computação.|Construção de Smart de transcrição, Robotics, de conversação|
|Kit de programação de áudio de Roobo Smart 2![Kit de programação de áudio de Roobo Smart 2](media/speech-devices-sdk/device-roobo-v2.jpg)|7 de matriz de mic, ARM SOC, de Wi-Fi, Bluetooth, e/s. </br>Linux|2ª geração SDK de dispositivos de voz que fornece o sistema operacional alternativo e mais recursos numa estrutura de referência de custo-eficiência.|Transcrição da conversação, o orador inteligente, agente, os de voz|
|Placa de desenvolvimento URbetter T11![URbetter DDK](media/speech-devices-sdk/device-urbetter.jpg)|7 de matriz de mic, ARM SOC, de Wi-Fi, Ethernet, HDMI, câmara USB. </br>Linux|Um nível de setor SDK de dispositivos de voz que se adapta a matriz de Microsoft Mic e suporte estendido e/s, como HDMI/Ethernet e mais periféricos USB|Transcrição da conversação, Education, Hospital, robôs, OTT caixa, o agente, de voz unidade até|

## <a name="download-the-speech-devices-sdk"></a>Transferir os SDK de dispositivos de voz

Transfira o [dispositivos de voz SDK](https://aka.ms/sdsdk-download).

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Começar com o SDK de dispositivos de voz](https://aka.ms/sdsdk-quickstart)
