---
title: Regiões-serviço de fala
titleSuffix: Azure Cognitive Services
description: Referência para as regiões do serviço de voz.
services: cognitive-services
author: mahilleb-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: c0414277b4851891911908ba4f42e92abedc86e4
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68553197"
---
# <a name="speech-service-supported-regions"></a>Regiões suportadas do serviço de voz

O serviço de voz permite que seu aplicativo converter áudio em texto, efetuar a tradução de voz e converter texto em voz. O serviço está disponível em várias regiões com pontos de extremidade exclusivos para o SDK de voz e REST APIs.

Certifique-se de que use o ponto final que corresponde à região para a sua subscrição.

## <a name="speech-sdk"></a>API de Voz

No [SDK de fala](speech-sdk.md), as regiões são especificadas como uma cadeia de caracteres (por exemplo, como `SpeechConfig.FromSubscription` um parâmetro para no SDK C#de fala para).

### <a name="speech-to-text-text-to-speech-and-translation"></a>Conversão de fala em texto, texto em fala e tradução

O SDK de fala está disponível nessas regiões para **reconhecimento de fala**, conversão de **texto em fala**e **tradução**:

  Região | Parâmetro do SDK de voz | Portal de personalização de voz
 ------|-------|--------
 EUA Oeste | `westus` | https://westus.cris.ai
 EUA Oeste 2 | `westus2` | https://westus2.cris.ai
 East US | `eastus` | https://eastus.cris.ai
 EUA Leste 2 | `eastus2` | https://eastus2.cris.ai
 EUA Central | `centralus` | https://centralus.cris.ai
 EUA Centro-Norte | `northcentralus` | https://northcentralus.cris.ai
 EUA Centro-Sul | `southcentralus` | https://southcentralus.cris.ai
 Índia Central | `centralindia` | https://centralindia.cris.ai
 Ásia Oriental | `eastasia` | https://eastasia.cris.ai
 Sudeste Asiático | `southeastasia` | https://southeastasia.cris.ai
 Leste do Japão | `japaneast` | https://japaneast.cris.ai
 Coreia do Sul Central | `koreacentral` | https://koreacentral.cris.ai
 Leste da Austrália | `australiaeast` | https://australiaeast.cris.ai
 Canadá Central | `canadacentral` | https://canadacentral.cris.ai
 Europa do Norte | `northeurope` | https://northeurope.cris.ai
 Europa Ocidental | `westeurope` | https://westeurope.cris.ai
 Reino Unido Sul | `uksouth` | https://uksouth.cris.ai
 França Central | `francecentral` | https://francecentral.cris.ai

### <a name="intent-recognition"></a>Reconhecimento da intenção do

As regiões disponíveis para o **reconhecimento de intenções** por meio do SDK de fala são as seguintes:

 Região global | Região | Parâmetro do SDK de voz
 ------|-------|--------
 Ásia | Ásia Oriental | `eastasia`
 Ásia | Sudeste Asiático | `southeastasia`
 Austrália | Leste da Austrália | `australiaeast`
 Europa | Europa do Norte | `northeurope`
 Europa | Europa Ocidental | `westeurope`
 América do Norte | East US | `eastus`
 América do Norte | EUA Leste 2 | `eastus2`
 América do Norte | EUA Centro-Sul | `southcentralus`
 América do Norte | EUA Centro-Oeste | `westcentralus`
 América do Norte | EUA Oeste | `westus`
 América do Norte | EUA Oeste 2 | `westus2`
 América do Sul | Sul do Brasil | `brazilsouth`

Esse é um subconjunto das regiões de publicação com suporte do [Luis (serviço de reconhecimento vocal)](/azure/cognitive-services/luis/luis-reference-regions).

### <a name="voice-first-virtual-assistants"></a>Assistentes virtuais da primeira voz

O [SDK de fala](speech-sdk.md) dá suporte a recursos do **assistente virtual de voz primeiro** nessas regiões:

Região | Parâmetro do SDK de voz
-------|---------------------
EUA Oeste | `westus`
EUA Oeste 2 | `westus2`
East US | `eastus`
EUA Leste 2 | `eastus2`
Europa Ocidental | `westeurope`
Europa do Norte | `northeurope`
Sudeste Asiático | `southeastasia`

## <a name="rest-apis"></a>APIs REST

O serviço de voz também expõe os pontos finais REST para pedidos de voz em texto e texto para voz.

### <a name="speech-to-text"></a>Conversão de voz em texto

Para obter a documentação de referência de fala para texto, consulte [API REST de fala em texto](rest-speech-to-text.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

### <a name="text-to-speech"></a>Conversão de texto em voz

Para obter a documentação de referência de conversão de texto em fala, consulte [API REST de conversão de texto em fala](rest-text-to-speech.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]