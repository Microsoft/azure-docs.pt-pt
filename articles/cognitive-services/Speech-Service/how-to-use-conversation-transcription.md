---
title: Início rápido da transcrição de conversação em tempo real - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Aprenda a usar a transcrição de conversação em tempo real com o SDK do discurso. A Transcrição de Conversação permite-lhe transcrever reuniões e outras conversas com a capacidade de adicionar, remover e identificar vários participantes através do streaming de áudio para o serviço Discurso.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: trbye
zone_pivot_groups: acs-js-csharp
ms.openlocfilehash: 6cb338e8b7baa45e1c84f59a5730a9a500e71a79
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92486800"
---
# <a name="get-started-with-real-time-conversation-transcription"></a>Começar com transcrição de conversação em tempo real

A API de **ConversationTranscriber** da Speech SDK permite-lhe transcrever reuniões e outras conversas com a capacidade de adicionar, remover e identificar vários participantes através do streaming de áudio para o serviço de Discurso utilizando `PullStream` ou `PushStream` . Primeiro cria assinaturas de voz para cada participante usando a API REST e, em seguida, utiliza as assinaturas de voz com o SDK para transcrever conversas. Consulte a [visão geral](conversation-transcription.md) da Transcrição de Conversação para obter mais informações.

## <a name="limitations"></a>Limitações

* Disponível apenas nas seguintes regiões de subscrição: `centralus` `eastasia` , `eastus` , `westeurope`
* Requer uma matriz circular de microfone de 7 microfones com um fluxo de referência de reprodução. A matriz do microfone deve cumprir as [nossas especificações.](https://aka.ms/sdsdk-microphone)
* O [Speech Devices SDK](speech-devices-sdk.md) fornece dispositivos adequados e uma aplicação de amostra que demonstra a transcrição de conversação.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que tem uma conta Azure e subscrição do serviço de fala. Se não tiver uma conta e subscrição, [experimente gratuitamente o serviço Desemação](overview.md#try-the-speech-service-for-free).

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript Basics include](includes/how-to/conversation-transcription/real-time-javascript.md)]
::: zone-end

::: zone pivot="programming-language-csharp"
[!INCLUDE [C# Basics include](includes/how-to/conversation-transcription/real-time-csharp.md)]
::: zone-end

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> Transcrição de [conversa assíncronea](how-to-async-conversation-transcription.md) 
>  Código de [amostra de dispositivo ROOBO](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Java/Android/Speech%20Devices%20SDK%20Starter%20App/example/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/sdsdkstarterapp/ConversationTranscription.java) 
>  [Código de amostra do Azure Kinect Dev Kit](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Java/Windows_Linux/SampleDemo/src/com/microsoft/cognitiveservices/speech/samples/Cts.java)
