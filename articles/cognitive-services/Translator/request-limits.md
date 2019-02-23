---
title: Limites - API de texto de tradutor de pedido
titleSuffix: Azure Cognitive Services
description: Este artigo apresenta os limites de pedido para a API de texto do Translator. Incorrerá em encargos com base na contagem de carateres, não pedido frequência com um limite de 5000 carateres por pedido. Limites de caracteres são a subscrição com base, com F0 limitado a 2 milhões de carateres por hora.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: erhopf
ms.openlocfilehash: 96ee65bfe8792bd56feb39f48659397cb517471a
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56735371"
---
# <a name="request-limits-for-translator-text"></a>Limites de pedido para o texto do tradutor

Este artigo fornece limites de limitação para a API de texto do Translator. Os serviços incluem a tradução, Transliteração, deteção de comprimento da sentença, deteção de idioma e traduções alternativas.

## <a name="character-limits-per-request"></a>Limites do caractere por pedido

Cada solicitação é uma limite de 5000 carateres. É-lhe cobrado por caractere, não pelo número de pedidos. É recomendado para enviar pedidos mais curtos e ter alguns pedidos pendentes num determinado momento.

Não existe nenhum limite no número de pedidos pendentes para a API de texto do Translator.

## <a name="character-limits-per-hour"></a>Limites do caractere por hora

O limite de carateres por hora baseia-se a sua camada de subscrição de texto do Translator. Se atinge ou superar as estes limites, provavelmente receberá um fora de resposta de quota:

| Escalão | Limite de carateres |
|------|-----------------|
| F0 | 2 milhões de carateres por hora |
| S1 | 40 milhões de carateres por hora |
| S2 | 40 milhões de carateres por hora |
| S3 | 120 milhões de carateres por hora |
| S4 | 200 milhões de carateres por hora |

Estes limites são restritas aos sistemas de genéricos da Microsoft. Sistemas de tradução personalizadas que utilizam o Hub de Microsoft Translator da Microsoft estão limitados a caráter 1.800 por segundo.

## <a name="latency"></a>Latência

Texto do tradutor tem uma latência máxima de 13 segundos. Agora irá receber um resultado ou uma resposta de tempo limite. Normalmente, as respostas são devolvidas em 150 milissegundos para 300 milissegundos. Tempos de resposta irão variar consoante o tamanho ou o par de solicitação e de idioma.

## <a name="sentence-length-limits"></a>Limites de tamanho de frase

Ao utilizar o [BreakSentence](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-break-sentence) função, o comprimento de sentença está limitado a 275 carateres. Existem exceções nos seguintes idiomas:

| Idioma | Código | Limite de carateres |
|----------|------|-----------------|
| Chinês | zh | 132 |
| Alemão | de | 290 |
| Italiano | it | 280 |
| Japonês | ja | 150 |
| Português | pt | 290 |
| Espanhol | es | 280 |
| Italiano | it | 280 |
| Tailandês | º | 258 |

> [!NOTE]
> Este limite não se aplica a traduções.

## <a name="next-steps"></a>Passos Seguintes

* [Preços](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
* [Disponibilidade regional](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
* [referência da API de texto de Microsoft Translator v3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
