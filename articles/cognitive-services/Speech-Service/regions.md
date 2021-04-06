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
ms.date: 08/20/2020
ms.author: panosper
ms.custom: seodec18,references_regions
ms.openlocfilehash: 646d29e72b91cd6afcde8e70ad8fd8715442b88e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "98786796"
---
# <a name="speech-service-supported-regions"></a>Regiões apoiadas por serviços de fala

O serviço Desemaguiso permite que a sua aplicação converta áudio em texto, realize tradução de voz e texto secreto para a fala. O serviço está disponível em várias regiões com pontos finais únicos para as APIs de Discurso e REST.

O portal Discurso para executar configurações personalizadas para a sua experiência de fala para todas as regiões está disponível aqui: https://speech.microsoft.com

Tenha em mente os seguintes pontos ao considerar as regiões:

* Se a sua aplicação utilizar um [Speech SDK,](speech-sdk.md)forneça o identificador da região, por `westus` exemplo, ao criar uma configuração de fala.
* Se a sua aplicação utilizar uma das [APIs rest](./overview.md#reference-docs)do serviço de fala, a região faz parte do ponto final que utiliza ao fazer pedidos.
* As chaves criadas para uma região são válidas apenas naquela região. Tentar usá-los com outras regiões resultará em erros de autenticação.

## <a name="speech-sdk"></a>API de Voz

No [SDK](speech-sdk.md)do Discurso, as regiões são especificadas como uma corda (por exemplo, como um parâmetro para `SpeechConfig.FromSubscription` o Discurso SDK para C#).

### <a name="speech-to-text-text-to-speech-and-translation"></a>Discurso-a-texto, texto-a-discurso e tradução

O portal de personalização da fala está disponível aqui: https://speech.microsoft.com

O serviço de Discurso está disponível nestas regiões para **reconhecimento de voz,** **texto-a-discurso,** e **tradução:**

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

Se utilizar o [SDK de fala,](speech-sdk.md)as regiões são especificadas pelo **identificador da Região** (por exemplo, como parâmetro para `SpeechConfig.FromSubscription` ). Certifique-se de que a região está a corresponder à região da sua subscrição.

Se planeia treinar um modelo personalizado com dados áudio, utilize uma das [regiões com hardware dedicado](custom-speech-overview.md#set-up-your-azure-account) para um treino mais rápido. Pode utilizar a [API REST](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CopyModelToSubscription) para copiar o modelo totalmente treinado para outra região mais tarde.

### <a name="intent-recognition"></a>Reconhecimento de intenção

As regiões disponíveis para **reconhecimento de intenções** através do SDK de discurso são as seguintes:

| Região global | Region           | Identificador da região |
| ------------- | ---------------- | -------------------- |
| Ásia          | Ásia Leste        | `eastasia`           |
| Ásia          | Sudeste Asiático   | `southeastasia`      |
| Austrália     | Leste da Austrália   | `australiaeast`      |
| Europa        | Europa do Norte     | `northeurope`        |
| Europa        | Europa Ocidental      | `westeurope`         |
| América do Norte | E.U.A. Leste          | `eastus`             |
| América do Norte | E.U.A. Leste 2        | `eastus2`            |
| América do Norte | E.U.A. Centro-Sul | `southcentralus`     |
| América do Norte | E.U.A. Centro-Oeste  | `westcentralus`      |
| América do Norte | E.U.A. Oeste          | `westus`             |
| América do Norte | E.U.A. Oeste 2        | `westus2`            |
| América do Sul | Sul do Brasil     | `brazilsouth`        |

Trata-se de um subconjunto das regiões editoriais apoiado pelo [serviço de Compreensão linguística (LUIS)](../luis/luis-reference-regions.md).

### <a name="voice-assistants"></a>Assistentes de voz

O [Speech SDK](speech-sdk.md) suporta capacidades **de assistente** de voz através do Discurso de Linha [Direta](./direct-line-speech.md) nestas regiões:

| Região global | Region           | Identificador da região    |
| ------------- | ---------------- | -------------------- |
| América do Norte | E.U.A. Oeste          | `westus`             |
| América do Norte | E.U.A. Oeste 2        | `westus2`            |
| América do Norte | E.U.A. Leste          | `eastus`             |
| América do Norte | E.U.A. Leste 2        | `eastus2`            |
| América do Norte | E.U.A. Centro-Oeste  | `westcentralus`      |
| América do Norte | E.U.A. Centro-Sul | `southcentralus`     |
| Europa        | Europa Ocidental      | `westeurope`         |
| Europa        | Europa do Norte     | `northeurope`        |
| Ásia          | Ásia Leste        | `eastasia`           |
| Ásia          | Sudeste Asiático   | `southeastasia`      |
| Índia         | Índia Central    | `centralindia`       |

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