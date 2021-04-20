---
title: Limites de pedido - Tradutor
titleSuffix: Azure Cognitive Services
description: Este artigo lista limites de pedido para o Tradutor. As cargas são incorridas com base na contagem de caracteres, não solicitam frequência com um limite de 5.000 caracteres por pedido. Os limites de caracteres são baseados em subscrição, com F0 limitado a 2 milhões de caracteres por hora.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 04/19/2021
ms.author: lajanuar
ms.openlocfilehash: b5beb222ec20b1e7941f9438c0aacf98879a567a
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727947"
---
# <a name="request-limits-for-translator"></a>Limites de pedido para Tradutor

Este artigo prevê limites de estrangulamento para a tradução do Tradutor, transliteração, deteção do comprimento da frase, deteção de linguagem e traduções alternativas.

## <a name="character-and-array-limits-per-request"></a>Limites de caracteres e matrizes por pedido

Cada pedido de tradução é limitado a 10.000 caracteres, em todas as línguas-alvo a que está a traduzir. Por exemplo, o envio de um pedido de tradução de 3.000 caracteres para traduzir para três idiomas diferentes resulta num tamanho de pedido de 3000x3 = 9.000 caracteres, que satisfazem o limite de pedido. É cobrado por carácter, não pelo número de pedidos. É recomendado enviar pedidos mais curtos.

A tabela que se segue enumera os limites de matriz e de caracteres para cada funcionamento do Tradutor.

| Operação | Tamanho máximo do elemento matriz |    Número máximo de elementos de matriz |    Tamanho máximo do pedido (caracteres) |
|:----|:----|:----|:----|
| Tradução | 10,000| 100| 10,000 |
| Transliterar | 5000| 10| 5000 |
| Detetar | 50 000 |100 |50 000 |
| BreakSentence | 50 000| 100 |50 000 |
| Pesquisa no Dicionário| 100 |10| 1,000 |
| Exemplos do dicionário | 100 para texto e 100 para tradução (200 no total)| 10|2.000 |

## <a name="character-limits-per-hour"></a>Limites de caracteres por hora

O seu limite de caracteres por hora baseia-se no seu nível de subscrição do Tradutor.

A quota horária deve ser consumida uniformemente ao longo da hora. Por exemplo, no limite de nível F0 de 2 milhões de caracteres por hora, os caracteres devem ser consumidos não mais rápido do que cerca de 33.300 caracteres por minuto de janela deslizante (2 milhões de caracteres divididos por 60 minutos).

Se atingir ou ultrapassar estes limites, ou enviar uma parte muito grande da quota num curto espaço de tempo, provavelmente receberá uma resposta fora da quota. Não há limites para pedidos simultâneos.

| Escalão de serviço | Limite de caracteres |
|------|-----------------|
| F0 | 2 milhões de caracteres por hora |
| S1 | 40 milhões de caracteres por hora |
| S2 / C2 | 40 milhões de caracteres por hora |
| S3 / C3 | 120 milhões de caracteres por hora |
| S4 / C4 | 200 milhões de caracteres por hora |

Os limites para [as assinaturas multi-serviços](./reference/v3-0-reference.md#authentication) são os mesmos que o nível S1.

Estes limites limitam-se aos modelos de tradução padrão da Microsoft. Os modelos de tradução personalizados que utilizam o Custom Tradutor estão limitados a 1.800 caracteres por segundo, por modelo.

## <a name="latency"></a>Latência

O Tradutor tem uma latência máxima de 15 segundos utilizando modelos standard e 120 segundos ao utilizar modelos personalizados. Tipicamente, as respostas *para texto dentro de 100 caracteres* são devolvidas em 150 milissegundos a 300 milissegundos. Os modelos de tradutor personalizado têm características de latência semelhantes na taxa de pedido sustentada e podem ter uma latência mais elevada quando a sua taxa de pedido é intermitente. Os tempos de resposta variarão em função do tamanho do pedido e do par de linguagem. Se não receber uma tradução ou uma [resposta de erro](./reference/v3-0-reference.md#errors) dentro desse prazo, verifique o seu código, a sua ligação de rede e volte a tentar.

## <a name="sentence-length-limits"></a>Limites de comprimento da frase

Ao utilizar a função [BreakSentence,](./reference/v3-0-break-sentence.md) o comprimento da frase é limitado a 275 caracteres. Existem exceções para estas línguas:

| Linguagem | Código | Limite de caracteres |
|----------|------|-----------------|
| Chinês | zh | 166 |
| Alemão | de | 800 |
| Italiano | que | 800 |
| Japonês | ja | 166 |
| Português | pt | 800 |
| Em espanhol | es | 800 |
| Tailandês | th | 180 |

> [!NOTE]
> Este limite não se aplica às traduções.

## <a name="next-steps"></a>Passos seguintes

* [Preços](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
* [Disponibilidade regional](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
* [Referência do Tradutor v3](./reference/v3-0-reference.md)