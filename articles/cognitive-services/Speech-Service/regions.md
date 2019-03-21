---
title: Regiões - serviços de voz
titlesuffix: Azure Cognitive Services
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
ms.openlocfilehash: 1be51c4f5aede58a975dc9441b3bec7a83f7becd
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57900918"
---
# <a name="speech-service-supported-regions"></a>Regiões suportadas do serviço de voz

O serviço de voz permite que seu aplicativo converter áudio em texto, efetuar a tradução de voz e converter texto em voz. O serviço está disponível em várias regiões com pontos de extremidade exclusivos para o SDK de voz e REST APIs.

Certifique-se de que use o ponto final que corresponde à região para a sua subscrição.

## <a name="speech-sdk"></a>API de Voz

Na [SDK de voz](speech-sdk.md), regiões são especificadas como uma cadeia de caracteres (por exemplo, como um parâmetro para `SpeechConfig.FromSubscription` no SDK de voz para c#).

### <a name="speech-recognition-and-translation"></a>Reconhecimento de fala e tradução

O SDK de voz está disponível nestas regiões para **reconhecimento de fala** e **tradução**:

  Região | Parâmetro do SDK de voz | Portal de personalização de voz
 ------|-------|--------
 EUA Oeste | `westus` | https://westus.cris.ai
 E.U.A. Oeste 2 | `westus2` | https://westus2.cris.ai
 EUA Leste | `eastus` | https://eastus.cris.ai
 E.U.A. Leste 2 | `eastus2` | https://eastus2.cris.ai
 EUA Central | 'centralus' | https://centralus.cris.ai
 EUA Centro-Norte | 'northcentralus' | https://northcentralus.cris.ai
 EUA Centro-Sul | 'southcentralus' | https://southcentralus.cris.ai
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

Regiões disponíveis para **reconhecimento da intenção** através do SDK de voz são as seguintes:

 Região global | Região | Parâmetro do SDK de voz
 ------|-------|--------
 Ásia | Ásia Oriental | `eastasia`
 Ásia | Sudeste Asiático | `southeastasia`
 Austrália | Leste da Austrália | `australiaeast`
 Europa | Europa do Norte | `northeurope`
 Europa | Europa Ocidental | `westeurope`
 América do Norte | EUA Leste | `eastus`
 América do Norte | EUA Leste 2 | `eastus2`
 América do Norte | EUA Centro-Sul | `southcentralus`
 América do Norte | EUA Centro-Oeste | `westcentralus`
 América do Norte | EUA Oeste | `westus`
 América do Norte | EUA Oeste 2 | `westus2`
 América do Sul | Sul do Brasil | `brazilsouth`

Este é um subconjunto das regiões publicação suportados pelos [serviço de compreensão de idiomas (LUIS)](/azure/cognitive-services/luis/luis-reference-regions).

## <a name="rest-apis"></a>APIs REST

O serviço de voz também expõe os pontos finais REST para pedidos de voz em texto e texto para voz.

### <a name="speech-to-text"></a>Conversão de voz em texto

Para obter documentação de referência de fala em texto, consulte [REST APIs](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

### <a name="text-to-speech"></a>Conversão de texto em voz

Para obter documentação de referência de texto para voz, consulte [REST APIs](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]
