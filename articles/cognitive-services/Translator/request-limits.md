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
ms.date: 06/04/2019
ms.author: erhopf
ms.openlocfilehash: d04677362e0ba3ace59d55ede9bd6241f17130e9
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67269224"
---
# <a name="request-limits-for-translator-text"></a>Limites de pedido para o texto do tradutor

Este artigo fornece limites de limitação para a API de texto do Translator. Os serviços incluem a tradução, Transliteração, deteção de comprimento da sentença, deteção de idioma e traduções alternativas.

## <a name="character-and-array-limits-per-request"></a>Limites de caracteres e de matriz por pedido

Cada solicitação de Translate está limitada a 5000 carateres. É-lhe cobrado por caractere, não pelo número de pedidos. É recomendado para enviar pedidos mais curtos.

As seguintes tabela listas de elemento e o caráter limites da matriz para cada operação da API de texto do Translator.

| Operação | Tamanho máximo do elemento de matriz |   Número máximo de elementos de matriz |  Tamanho máximo do pedido (carateres) |
|:----|:----|:----|:----|
| Translate | 5,000 | 100   | 5,000 |
| Transliterate | 5,000 | 10    | 5,000 |
| Detect | 10,000 | 100 |   50,000 |
| BreakSentence | 10,000    | 100 | 5,0000 |
| Pesquisa no Dicionário| 100 |  10  | 1,000 |
| Exemplos do dicionário | 100 para texto e 100 para tradução (total de 200)| 10|   2\.000 |

## <a name="character-limits-per-hour"></a>Limites do caractere por hora

O limite de carateres por hora baseia-se a sua camada de subscrição de texto do Translator. A quota de hora a hora deve ser consumida uniformemente em toda a hora. Se chega a superar as estes limites ou enviar demasiado grande de uma parte da quota de num curto período de tempo, provavelmente receberá um fora de resposta de quota. Não há nenhum limite no pedidos em simultâneo.

| Escalão | Limite de carateres |
|------|-----------------|
| F0 | 2 milhões de carateres por hora |
| S1 | 40 milhões de carateres por hora |
| S2 / C2 | 40 milhões de carateres por hora |
| S3 / C3 | 120 milhões de carateres por hora |
| S4 / C4 | 200 milhões de carateres por hora |

Limites para [subscrições múltiplos serviços](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication) são os mesmos que o escalão S1.

Estes limites estão limitados aos modelos de tradução padrão da Microsoft. Modelos de tradução personalizadas que utilizam o Translator personalizados estão limitados a caráter 1.800 por segundo.

## <a name="latency"></a>Latência

A API de texto de tradutor tem uma latência máxima de 15 segundos com modelos padrão. Utilizar modelos personalizados de tradução tem uma latência máxima de 25 segundos. Agora irá receber um resultado ou uma resposta de tempo limite. Normalmente, as respostas são devolvidas em 150 milissegundos para 300 milissegundos. Tempos de resposta irão variar consoante o tamanho do par de solicitação e de idioma. Se não receber uma tradução ou uma [resposta de erro](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors) durante esse período de tempo, deve verificar a ligação de rede e tente novamente.

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
| Tailandês | TH | 258 |

> [!NOTE]
> Este limite não se aplica a traduções.

## <a name="next-steps"></a>Passos Seguintes

* [Preços](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
* [Disponibilidade regional](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
* [referência da API de texto de Microsoft Translator v3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
