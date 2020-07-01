---
title: Limites de pedido - Tradutor
titleSuffix: Azure Cognitive Services
description: Este artigo lista limites de pedido para o Tradutor. As cargas são incorridas com base na contagem de caracteres, não solicitam frequência com um limite de 5.000 caracteres por pedido. Os limites de caracteres são baseados em subscrição, com F0 limitado a 2 milhões de caracteres por hora.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: swmachan
ms.openlocfilehash: d9fda476a184731cd00317facbeceaf7bdf55edf
ms.sourcegitcommit: 73ac360f37053a3321e8be23236b32d4f8fb30cf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/30/2020
ms.locfileid: "85561466"
---
# <a name="request-limits-for-translator"></a>Limites de pedido para Tradutor

Este artigo prevê limites de estrangulamento para o Tradutor. Os serviços incluem tradução, transliteração, deteção de comprimento de frase, deteção de linguagem e traduções alternativas.

## <a name="character-and-array-limits-per-request"></a>Limites de caracteres e matrizes por pedido

Cada pedido de tradução é limitado a 5.000 caracteres, em todas as línguas-alvo a que está a traduzir. Por exemplo, o envio de um pedido de tradução de 1.500 caracteres para traduzir para 3 idiomas diferentes resulta num tamanho de pedido de 1.500x3 = 4.500 caracteres, o que satisfaz o limite de pedido. É cobrado por carácter, não pelo número de pedidos. É recomendado enviar pedidos mais curtos.

A tabela que se segue enumera os limites de matriz e de caracteres para cada funcionamento do Tradutor.

| Operação | Tamanho máximo do elemento matriz |    Número máximo de elementos de matriz |    Tamanho máximo do pedido (caracteres) |
|:----|:----|:----|:----|
| Traduzir | 5000    | 100    | 5000 |
| Transliterar | 5000    | 10    | 5000 |
| Detetar | 10,000 |    100 |    50.000 |
| BreakSentence | 10,000    | 100 |    50.000 |
| Pesquisa no Dicionário| 100 |    10    | 1,000 |
| Exemplos do dicionário | 100 para texto e 100 para tradução (200 no total)| 10|    2.000 |

## <a name="character-limits-per-hour"></a>Limites de caracteres por hora

O seu limite de caracteres por hora baseia-se no seu nível de subscrição do Tradutor. 

A quota horária deve ser consumida uniformemente ao longo da hora. Por exemplo, no limite de nível F0 de 2 milhões de caracteres por hora, os caracteres devem ser consumidos não mais rápido do que cerca de 33.300 caracteres por minuto de janela deslizante (2 milhões de caracteres divididos por 60 minutos).

Se atingir ou ultrapassar estes limites, ou enviar uma parte muito grande da quota num curto espaço de tempo, provavelmente receberá uma resposta fora da quota. Não há limites para pedidos simultâneos.

| Escalão | Limite de caracteres |
|------|-----------------|
| F0 | 2 milhões de caracteres por hora |
| S1 | 40 milhões de caracteres por hora |
| S2 / C2 | 40 milhões de caracteres por hora |
| S3 / C3 | 120 milhões de caracteres por hora |
| S4 / C4 | 200 milhões de caracteres por hora |

Os limites para [as assinaturas multi-serviços](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication) são os mesmos que o nível S1.

Estes limites limitam-se aos modelos de tradução padrão da Microsoft. Os modelos de tradução personalizados que utilizam o Custom Tradutor estão limitados a 1.800 caracteres por segundo.

## <a name="latency"></a>Latência

O Tradutor tem uma latência máxima de 15 segundos utilizando modelos standard e 120 segundos ao utilizar modelos personalizados. Tipicamente, as respostas *para texto dentro de 100 caracteres* são devolvidas em 150 milissegundos a 300 milissegundos. Os modelos de tradutor personalizado têm características de latência semelhantes na taxa de pedido sustentada e podem ter uma latência mais elevada quando a sua taxa de pedido é intermitente. Os tempos de resposta variarão em função do tamanho do pedido e do par de linguagem. Se não receber uma tradução ou uma [resposta de erro](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors) dentro desse prazo, verifique o seu código, a sua ligação de rede e volte a tentar. 

## <a name="sentence-length-limits"></a>Limites de comprimento da frase

Ao utilizar a função [BreakSentence,](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-break-sentence) o comprimento da frase é limitado a 275 caracteres. Existem exceções para estas línguas:

| Linguagem | Código | Limite de caracteres |
|----------|------|-----------------|
| Chinês | zh | 166 |
| Alemão | de | 800 |
| Italiano | que | 800 |
| Japonês | ja | 166 |
| Português | pt | 800 |
| Espanhol | es | 800 |
| Tailandês | th | 180 |

> [!NOTE]
> Este limite não se aplica às traduções.

## <a name="next-steps"></a>Passos seguintes

* [Preços](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
* [Disponibilidade regional](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
* [Referência do Tradutor v3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
