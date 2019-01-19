---
title: Regiões - serviços de voz
titlesuffix: Azure Cognitive Services
description: Referência para as regiões do serviço de voz.
services: cognitive-services
author: mahilleb-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 01/14/2019
ms.author: mahilleb
ms.custom: seodec18
ms.openlocfilehash: e33bf7cd98cdd5862af6f4d68d3d73de1a07d229
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2019
ms.locfileid: "54413163"
---
# <a name="speech-service-supported-regions"></a>Regiões suportadas do serviço de voz

O serviço de voz permite que seu aplicativo converter áudio em texto, efetuar a tradução de voz e converter texto em voz. O serviço está disponível em várias regiões com pontos de extremidade exclusivos para o SDK de voz e REST APIs.

Certifique-se de que use o ponto final que corresponde à região para a sua subscrição.

## <a name="speech-sdk"></a>API de Voz

Na [SDK do serviço de voz](speech-sdk.md), regiões são especificadas como uma cadeia de caracteres (por exemplo, como um parâmetro para `SpeechConfig.FromSubscription` no SDK de voz para c#).

### <a name="speech-recognition-and-translation"></a>Reconhecimento de fala e tradução

O SDK de voz está disponível nestas regiões para **reconhecimento de fala** e **tradução**:

  Região | Parâmetro do SDK de voz | Portal de personalização de voz
 ------|-------|--------
 EUA Oeste | `westus` | https://westus.cris.ai
 E.U.A. Oeste 2 | `westus2` | https://westus2.cris.ai
 EUA Leste | `eastus` | https://eastus.cris.ai
 E.U.A. Leste 2 | `eastus2` | https://eastus2.cris.ai
 Ásia Oriental | `eastasia` | https://eastasia.cris.ai
 Sudeste Asiático | `southeastasia` | https://southeastasia.cris.ai
 Europa do Norte | `northeurope` | https://northeurope.cris.ai
 Europa Ocidental | `westeurope` | https://westeurope.cris.ai


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
