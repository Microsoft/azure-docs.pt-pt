---
title: Regiões - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Uma lista de regiões e pontos finais disponíveis para o serviço de discurso, incluindo discurso-a-texto, texto-a-discurso e tradução da fala.
services: cognitive-services
author: mahilleb-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 27e26bb37b444b49797d46dd4e12b61f8fe11b16
ms.sourcegitcommit: 52d2f06ecec82977a1463d54a9000a68ff26b572
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/15/2020
ms.locfileid: "84782539"
---
# <a name="speech-service-supported-regions"></a>Regiões apoiadas por serviços de fala

O serviço Desemaguiso permite que a sua aplicação converta áudio em texto, realize tradução de voz e texto secreto para a fala. O serviço está disponível em várias regiões com pontos finais únicos para as APIs de Discurso e REST.

O portal Discurso para executar configurações personalizadas para a sua experiência de fala para todas as regiões está disponível aqui:https://speech.microsoft.com

Para invocações do seu serviço Speech, certifique-se de que a chamada corresponde à região para a sua subscrição.

## <a name="speech-sdk"></a>API de Voz

No [SDK](speech-sdk.md)do Discurso, as regiões são especificadas como uma corda (por exemplo, como um parâmetro para `SpeechConfig.FromSubscription` o Discurso SDK para C#).

### <a name="speech-to-text-text-to-speech-and-translation"></a>Discurso-a-texto, texto-a-discurso e tradução

O portal de personalização da fala está disponível aqui:https://speech.microsoft.com

O serviço de Discurso está disponível nestas regiões para **reconhecimento de voz,** **texto-a-discurso,** e **tradução:**

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

Se utilizar o [SDK de fala,](speech-sdk.md)as regiões são especificadas pelo **identificador da Região** (por exemplo, como parâmetro para `SpeechConfig.FromSubscription` ). Certifique-se de que a região está a corresponder à região da sua subscrição.

### <a name="intent-recognition"></a>Reconhecimento de intenção

As regiões disponíveis para **reconhecimento de intenções** através do SDK de discurso são as seguintes:

| Região global | Região           | Identificador da região |
| ------------- | ---------------- | -------------------- |
| Ásia          | Ásia Leste        | `eastasia`           |
| Ásia          | Ásia Sudeste   | `southeastasia`      |
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

Trata-se de um subconjunto das regiões editoriais apoiado pelo [serviço de Compreensão linguística (LUIS)](/azure/cognitive-services/luis/luis-reference-regions).

### <a name="voice-assistants"></a>Assistentes de voz

O [Speech SDK](speech-sdk.md) suporta capacidades **de assistente** de voz nestas regiões:

| Região         | Identificador da região |
| -------------- | -------------------- |
| E.U.A. Oeste        | `westus`             |
| E.U.A.Oeste 2      | `westus2`            |
| E.U.A. Leste        | `eastus`             |
| E.U.A. Leste 2      | `eastus2`            |
| Europa Ocidental    | `westeurope`         |
| Europa do Norte   | `northeurope`        |
| Ásia Sudeste | `southeastasia`      |

### <a name="speaker-recognition"></a>Reconhecimento de Orador

Atualmente, o Reconhecimento de Oradores só está disponível na `westus` região.

## <a name="rest-apis"></a>APIs REST

O serviço de discurso também expõe pontos finais do REST para pedidos de discurso a texto e texto-a-voz.

### <a name="speech-to-text"></a>Conversão de voz em texto

Para documentação de referência fala-a-texto, consulte [a API REST de voz a texto](rest-speech-to-text.md).

O ponto final para a API REST tem este formato:

```
https://<REGION_IDENTIFIER>.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1
```

`<REGION_IDENTIFIER>`Substitua-a pelo identificador correspondente à região da sua subscrição a partir desta tabela:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

> [!NOTE]
> O parâmetro da linguagem deve ser anexado ao URL para evitar receber um erro HTTP de 4xx. Por exemplo, a língua definida para o inglês americano usando o ponto final dos EUA é: `https://westus.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US` .

### <a name="text-to-speech"></a>Conversão de texto em voz

Para documentação de referência texto-a-discurso, consulte [a API DE REPOUSO TEXT-to-speech](rest-text-to-speech.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]
