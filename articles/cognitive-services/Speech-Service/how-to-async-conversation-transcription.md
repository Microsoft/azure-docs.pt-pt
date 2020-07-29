---
title: Transcrição de Conversa assíncroa - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Aprenda a usar a transcrição de conversação assíncronea utilizando o serviço De Discurso. Disponível apenas para Java e C#.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: amishu
zone_pivot_groups: programming-languages-set-twenty-one
ms.openlocfilehash: 65a6fd7e0e1e64a459a46c0f97a2afdbc03573a9
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87284229"
---
# <a name="asynchronous-conversation-transcription"></a>Transcrição de Conversas Assíncrona

Neste artigo, a Transcrição de Conversação Assíncrono é demonstrada usando a **API de Climatização RemotaConversationTranscriptionClient.** Se tiver configurado Transcrição de Conversação para fazer transcrição assíncrono e tiver uma `conversationId` , pode obter a transcrição associada a isso utilizando a `conversationId` API de **Climaclíscription RemoteConversationTranscription.**

## <a name="asynchronous-vs-real-time--asynchronous"></a>Assíncronos vs. em tempo real + assíncronos

Com a transcrição assíncrona, transmite-se o áudio da conversa, mas não precisa de uma transcrição devolvida em tempo real. Em vez disso, depois de o áudio ser enviado, use o `conversationId` de consulta para o estado da `Conversation` transcrição assíncronea. Quando a transcrição assíncronea estiver pronta, terás `RemoteConversationTranscriptionResult` um.

Com tempo real mais assíncronos, obtém-se a transcrição em tempo real, mas também obtém-se a transcrição consultando com o `conversationId` (cenário semelhante ao assíncronos).

São necessários dois passos para realizar a transcrição assíncronea. O primeiro passo é carregar o áudio, escolhendo apenas assíncronos ou em tempo real mais assíncronos. O segundo passo é obter os resultados da transcrição.

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/remote-conversation/csharp/examples.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/remote-conversation/java/examples.md)]
::: zone-end


## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Explore as nossas amostras no GitHub](https://aka.ms/csspeech/samples)
