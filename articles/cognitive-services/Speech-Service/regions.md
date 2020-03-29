---
title: Regiões - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Uma lista das regiões disponíveis e pontos finais para o serviço da Fala, incluindo o discurso a texto, o texto-a-fala e a tradução da fala.
services: cognitive-services
author: mahilleb-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 560575ca7f51218e472abecb4319f4a3db69b1ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220470"
---
# <a name="speech-service-supported-regions"></a>Serviço de fala apoiado regiões

O serviço De Fala permite que a sua aplicação converta áudio em texto, execute tradução de discurso sondar e texto secreto para a fala. O serviço está disponível em várias regiões com pontos finais únicos para o Speech SDK e REST APIs.

O portal Speech para realizar configurações personalizadas para a sua experiência de fala para todas as regiões está disponível aqui:https://speech.microsoft.com

Para invocações do seu serviço de Discurso, certifique-se de que a chamada corresponde à região para a sua subscrição.

## <a name="speech-sdk"></a>API de Voz

No [SDK](speech-sdk.md)do Discurso, as regiões são especificadas como uma `SpeechConfig.FromSubscription` corda (por exemplo, como parâmetro para no SDK de Fala para C#).

### <a name="speech-to-text-text-to-speech-and-translation"></a>Discurso-texto, texto-a-fala e tradução

O portal de personalização da fala está disponível aqui:https://speech.microsoft.com

O serviço de Fala está disponível nestas regiões para reconhecimento da **fala,** **texto-a-fala**e **tradução:**

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

Se utilizar o [SDK](speech-sdk.md)da fala, as regiões são especificadas pelo **identificador** regional (por exemplo, como parâmetro para `SpeechConfig.FromSubscription`). Certifique-se de que a região está a corresponder à região da sua subscrição.

### <a name="intent-recognition"></a>Reconhecimento de intenção

As regiões disponíveis para reconhecimento de **intenções** através do SDK da fala são as seguintes:

| Região global | Região           | Identificador de região |
| ------------- | ---------------- | -------------------- |
| Ásia          | Ásia Leste        | `eastasia`           |
| Ásia          | Ásia Sudeste   | `southeastasia`      |
| Austrália     | Leste da Austrália   | `australiaeast`      |
| Europa        | Europa do Norte     | `northeurope`        |
| Europa        | Europa ocidental      | `westeurope`         |
| América do Norte | E.U.A. Leste          | `eastus`             |
| América do Norte | E.U.A. Leste 2        | `eastus2`            |
| América do Norte | E.U.A. Centro-Sul | `southcentralus`     |
| América do Norte | E.U.A. Centro-Oeste  | `westcentralus`      |
| América do Norte | E.U.A. Oeste          | `westus`             |
| América do Norte | E.U.A.Oeste 2        | `westus2`            |
| América do Sul | Sul do Brasil     | `brazilsouth`        |

Trata-se de um subconjunto das regiões editoriais apoiadas pelo serviço de Compreensão da [Língua (LUIS).](/azure/cognitive-services/luis/luis-reference-regions)

### <a name="voice-assistants"></a>Assistentes de voz

O [SDK de Fala](speech-sdk.md) apoia as capacidades de assistente de **voz** nestas regiões:

| Região         | Identificador de região |
| -------------- | -------------------- |
| E.U.A. Oeste        | `westus`             |
| E.U.A.Oeste 2      | `westus2`            |
| E.U.A. Leste        | `eastus`             |
| E.U.A. Leste 2      | `eastus2`            |
| Europa ocidental    | `westeurope`         |
| Europa do Norte   | `northeurope`        |
| Ásia Sudeste | `southeastasia`      |

## <a name="rest-apis"></a>APIs REST

O serviço de Discurso também expõe os pontos finais do REST para pedidos de discurso a texto e texto-a-discurso.

### <a name="speech-to-text"></a>Conversão de voz em texto

Para documentação de referência fala-a-texto, consulte [API REST Speech-to-text](rest-speech-to-text.md).

O ponto final para a Rest API tem este formato:

```
https://<REGION_IDENTIFIER>.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1
```

Substitua-o `<REGION_IDENTIFIER>` pelo identificador que corresponde à região da sua subscrição a partir desta tabela:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

> [!NOTE]
> O parâmetro de idioma deve ser anexado ao URL para evitar receber um erro de 4xx HTTP. Por exemplo, a língua definida para o inglês `https://westus.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US`americano usando o ponto final dos EUA Ocidental é: .

### <a name="text-to-speech"></a>Conversão de texto em voz

Para obter documentação de referência texto-a-voz, consulte [API REST Text-to-speech](rest-text-to-speech.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]
