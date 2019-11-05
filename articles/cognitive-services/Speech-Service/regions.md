---
title: Regiões-serviço de fala
titleSuffix: Azure Cognitive Services
description: Referência para regiões do serviço de fala.
services: cognitive-services
author: mahilleb-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 469dab093ed7a62171d232695af3258cc874b5f3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73481303"
---
# <a name="speech-service-supported-regions"></a>Regiões com suporte do serviço de fala

O serviço de fala permite que seu aplicativo converta áudio em texto, execute a tradução de fala e aborde texto em fala. O serviço está disponível em várias regiões com pontos de extremidade exclusivos para o SDK de fala e APIs REST.

Certifique-se de usar o ponto de extremidade que corresponde à região da sua assinatura.

## <a name="speech-sdk"></a>SDK de Voz

No [SDK de fala](speech-sdk.md), as regiões são especificadas como uma cadeia de caracteres (por exemplo, como um parâmetro para `SpeechConfig.FromSubscription` no SDK C#de fala para).

### <a name="speech-to-text-text-to-speech-and-translation"></a>Conversão de fala em texto, texto em fala e tradução

O SDK de fala está disponível nessas regiões para **reconhecimento de fala**, conversão de **texto em fala**e **tradução**:

  Região | Parâmetro do SDK de fala | Portal de personalização de fala
 ------|-------|--------
 Oeste dos E.U.A. | `westus` | https://westus.cris.ai
 EUA Oeste 2 | `westus2` | https://westus2.cris.ai
 EUA Leste | `eastus` | https://eastus.cris.ai
 EUA Leste 2 | `eastus2` | https://eastus2.cris.ai
 EUA Central | `centralus` | https://centralus.cris.ai
 E.U.A. Centro-Norte | `northcentralus` | https://northcentralus.cris.ai
 EUA Centro-Sul | `southcentralus` | https://southcentralus.cris.ai
 Índia Central | `centralindia` | https://centralindia.cris.ai
 Ásia Oriental | `eastasia` | https://eastasia.cris.ai
 Sudeste Asiático | `southeastasia` | https://southeastasia.cris.ai
 Este do Japão | `japaneast` | https://japaneast.cris.ai
 Coreia do Sul Central | `koreacentral` | https://koreacentral.cris.ai
 Leste da Austrália | `australiaeast` | https://australiaeast.cris.ai
 Canadá Central | `canadacentral` | https://canadacentral.cris.ai
 Europa do Norte | `northeurope` | https://northeurope.cris.ai
 Europa Ocidental | `westeurope` | https://westeurope.cris.ai
 Sul do Reino Unido | `uksouth` | https://uksouth.cris.ai
 França Central | `francecentral` | https://francecentral.cris.ai

### <a name="intent-recognition"></a>Reconhecimento de intenção

As regiões disponíveis para o **reconhecimento de intenções** por meio do SDK de fala são as seguintes:

 Região global | Região | Parâmetro do SDK de fala
 ------|-------|--------
 Ásia | Ásia Oriental | `eastasia`
 Ásia | Sudeste Asiático | `southeastasia`
 Austrália | Leste da Austrália | `australiaeast`
 Europa | Europa do Norte | `northeurope`
 Europa | Europa Ocidental | `westeurope`
 América do Norte | EUA Leste | `eastus`
 América do Norte | EUA Leste 2 | `eastus2`
 América do Norte | EUA Centro-Sul | `southcentralus`
 América do Norte | E.U.A. Centro-Oeste | `westcentralus`
 América do Norte | Oeste dos E.U.A. | `westus`
 América do Norte | EUA Oeste 2 | `westus2`
 América do Sul | Sul do Brasil | `brazilsouth`

Esse é um subconjunto das regiões de publicação com suporte do [Luis (serviço de reconhecimento vocal)](/azure/cognitive-services/luis/luis-reference-regions).

### <a name="voice-assistants"></a>Assistentes de voz

O [SDK de fala](speech-sdk.md) dá suporte a recursos do **Assistente de voz** nessas regiões:

Região | Parâmetro do SDK de fala
-------|---------------------
Oeste dos E.U.A. | `westus`
EUA Oeste 2 | `westus2`
EUA Leste | `eastus`
EUA Leste 2 | `eastus2`
Europa Ocidental | `westeurope`
Europa do Norte | `northeurope`
Sudeste Asiático | `southeastasia`

## <a name="rest-apis"></a>APIs REST

O serviço de fala também expõe pontos de extremidade REST para solicitações de conversão de fala em texto e texto em fala.

### <a name="speech-to-text"></a>Conversão de voz em texto

Para obter a documentação de referência de fala para texto, consulte [API REST de fala em texto](rest-speech-to-text.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

### <a name="text-to-speech"></a>Conversão de texto em voz

Para obter a documentação de referência de conversão de texto em fala, consulte [API REST de conversão de texto em fala](rest-text-to-speech.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]