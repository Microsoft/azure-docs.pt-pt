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
ms.openlocfilehash: f1379202fc59e9cca7a3543be201f8ebff276bef
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77168365"
---
# <a name="speech-service-supported-regions"></a>Serviço de fala apoiado regiões

O serviço de voz permite que seu aplicativo converter áudio em texto, efetuar a tradução de voz e converter texto em voz. O serviço está disponível em várias regiões com pontos de extremidade exclusivos para o SDK de voz e REST APIs.

O portal Speech para realizar configurações personalizadas para a sua experiência de fala para todas as regiões está disponível aqui: https://speech.microsoft.com

Para invocações do seu serviço de Discurso, certifique-se de que a chamada corresponde à região para a sua subscrição.

## <a name="speech-sdk"></a>API de Voz

No [SDK](speech-sdk.md)do Discurso, as regiões são especificadas como uma corda (por exemplo, C#como parâmetro para `SpeechConfig.FromSubscription` no SDK do discurso para ).

### <a name="speech-to-text-text-to-speech-and-translation"></a>Discurso-texto, texto-a-fala e tradução

O portal de personalização da fala está disponível aqui: https://speech.microsoft.com

O serviço de Fala está disponível nestas regiões para reconhecimento da **fala,** **texto-a-fala**e **tradução:**

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

Se utilizar o [SDK](speech-sdk.md)da fala, as regiões são especificadas pelo **Identificador** regional (por exemplo, como parâmetro para `SpeechConfig.FromSubscription`). Certifique-se de que a região está a corresponder à região da sua subscrição.

### <a name="intent-recognition"></a>Reconhecimento de intenção

As regiões disponíveis para reconhecimento de **intenções** através do SDK da fala são as seguintes:

| Região global | Região           | Parâmetro do SDK de voz |
| ------------- | ---------------- | -------------------- |
| Ásia          | Ásia Oriental        | `eastasia`           |
| Ásia          | Sudeste Asiático   | `southeastasia`      |
| Austrália     | Leste da Austrália   | `australiaeast`      |
| Europa        | Europa do Norte     | `northeurope`        |
| Europa        | Europa Ocidental      | `westeurope`         |
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

| Região         | Parâmetro do SDK de voz |
| -------------- | -------------------- |
| E.U.A. Oeste        | `westus`             |
| E.U.A.Oeste 2      | `westus2`            |
| E.U.A. Leste        | `eastus`             |
| E.U.A. Leste 2      | `eastus2`            |
| Europa Ocidental    | `westeurope`         |
| Europa do Norte   | `northeurope`        |
| Sudeste Asiático | `southeastasia`      |

## <a name="rest-apis"></a>APIs REST

O serviço de voz também expõe os pontos finais REST para pedidos de voz em texto e texto para voz.

### <a name="speech-to-text"></a>Conversão de voz em texto

Para documentação de referência fala-a-texto, consulte [API REST Speech-to-text](rest-speech-to-text.md).

O ponto final para a Rest API tem este formato:

```
https://<REGION_IDENTIFIER>.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1
```

Substitua `<REGION_IDENTIFIER>` pelo identificador que corresponde à região da sua subscrição a partir desta tabela:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

> [!NOTE]
> O parâmetro de idioma deve ser anexado ao URL para evitar receber um erro de 4xx HTTP. Por exemplo, a língua definida para o inglês americano usando o ponto final dos EUA Ocidental é: `https://westus.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US`.

### <a name="text-to-speech"></a>Conversão de texto em voz

Para obter documentação de referência texto-a-voz, consulte [API REST Text-to-speech](rest-text-to-speech.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]
