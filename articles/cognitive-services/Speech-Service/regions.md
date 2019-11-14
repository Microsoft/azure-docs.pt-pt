---
title: Regiões-serviço de fala
titleSuffix: Azure Cognitive Services
description: Uma lista de regiões e pontos de extremidade disponíveis para o serviço de fala, incluindo conversão de fala em texto, texto em fala e tradução de fala.
services: cognitive-services
author: mahilleb-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 478a62eaddcf3b9b831812a0930ff10c1adce99d
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74072449"
---
# <a name="speech-service-supported-regions"></a>Regiões suportadas do serviço de voz

O serviço de voz permite que seu aplicativo converter áudio em texto, efetuar a tradução de voz e converter texto em voz. O serviço está disponível em várias regiões com pontos de extremidade exclusivos para o SDK de voz e REST APIs.

Certifique-se de que use o ponto final que corresponde à região para a sua subscrição.

## <a name="speech-sdk"></a>API de Voz

No [SDK de fala](speech-sdk.md), as regiões são especificadas como uma cadeia de caracteres (por exemplo, como um parâmetro para `SpeechConfig.FromSubscription` no SDK C#de fala para).

### <a name="speech-to-text-text-to-speech-and-translation"></a>Conversão de fala em texto, texto em fala e tradução

O SDK de fala está disponível nessas regiões para **reconhecimento de fala**, conversão de **texto em fala**e **tradução**:

| Região           | Parâmetro do SDK de voz | Portal de personalização de voz    |
| ---------------- | -------------------- | ------------------------------ |
| EUA Oeste          | `westus`             | https://westus.cris.ai         |
| EUA Oeste 2        | `westus2`            | https://westus2.cris.ai        |
| EUA Leste          | `eastus`             | https://eastus.cris.ai         |
| EUA Leste 2        | `eastus2`            | https://eastus2.cris.ai        |
| EUA Central       | `centralus`          | https://centralus.cris.ai      |
| EUA Centro-Norte | `northcentralus`     | https://northcentralus.cris.ai |
| EUA Centro-Sul | `southcentralus`     | https://southcentralus.cris.ai |
| Índia Central    | `centralindia`       | https://centralindia.cris.ai   |
| Ásia Oriental        | `eastasia`           | https://eastasia.cris.ai       |
| Sudeste asiático   | `southeastasia`      | https://southeastasia.cris.ai  |
| Leste do Japão       | `japaneast`          | https://japaneast.cris.ai      |
| Coreia do Sul Central    | `koreacentral`       | https://koreacentral.cris.ai   |
| Leste da Austrália   | `australiaeast`      | https://australiaeast.cris.ai  |
| Canadá Central   | `canadacentral`      | https://canadacentral.cris.ai  |
| Europa do Norte     | `northeurope`        | https://northeurope.cris.ai    |
| Europa ocidental      | `westeurope`         | https://westeurope.cris.ai     |
| Reino Unido Sul         | `uksouth`            | https://uksouth.cris.ai        |
| França Central   | `francecentral`      | https://francecentral.cris.ai  |

### <a name="intent-recognition"></a>Reconhecimento de intenção

As regiões disponíveis para o **reconhecimento de intenções** por meio do SDK de fala são as seguintes:

| Região global | Região           | Parâmetro do SDK de voz |
| ------------- | ---------------- | -------------------- |
| Ásia          | Ásia Oriental        | `eastasia`           |
| Ásia          | Sudeste asiático   | `southeastasia`      |
| Austrália     | Leste da Austrália   | `australiaeast`      |
| Europa        | Europa do Norte     | `northeurope`        |
| Europa        | Europa ocidental      | `westeurope`         |
| América do Norte | EUA Leste          | `eastus`             |
| América do Norte | EUA Leste 2        | `eastus2`            |
| América do Norte | EUA Centro-Sul | `southcentralus`     |
| América do Norte | EUA Centro-Oeste  | `westcentralus`      |
| América do Norte | EUA Oeste          | `westus`             |
| América do Norte | EUA Oeste 2        | `westus2`            |
| América do Sul | Sul do Brasil     | `brazilsouth`        |

Esse é um subconjunto das regiões de publicação com suporte do [Luis (serviço de reconhecimento vocal)](/azure/cognitive-services/luis/luis-reference-regions).

### <a name="voice-assistants"></a>Assistentes de voz

O [SDK de fala](speech-sdk.md) dá suporte a recursos do **Assistente de voz** nessas regiões:

| Região         | Parâmetro do SDK de voz |
| -------------- | -------------------- |
| EUA Oeste        | `westus`             |
| EUA Oeste 2      | `westus2`            |
| EUA Leste        | `eastus`             |
| EUA Leste 2      | `eastus2`            |
| Europa ocidental    | `westeurope`         |
| Europa do Norte   | `northeurope`        |
| Sudeste asiático | `southeastasia`      |

## <a name="rest-apis"></a>APIs REST

O serviço de voz também expõe os pontos finais REST para pedidos de voz em texto e texto para voz.

### <a name="speech-to-text"></a>Conversão de voz em texto

Para obter a documentação de referência de fala para texto, consulte [API REST de fala em texto](rest-speech-to-text.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

### <a name="text-to-speech"></a>Conversão de texto em voz

Para obter a documentação de referência de conversão de texto em fala, consulte [API REST de conversão de texto em fala](rest-text-to-speech.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]
