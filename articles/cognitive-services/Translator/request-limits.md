---
title: Limites de solicitação-API de Tradução de Texto
titleSuffix: Azure Cognitive Services
description: Este artigo lista os limites de solicitação para o API de Tradução de Texto. Os encargos são incorridos com base na contagem de caracteres, não na frequência de solicitação com um limite de 5.000 caracteres por solicitação. Os limites de caracteres são baseados em assinatura, com F0 limitados a 2 milhões caracteres por hora.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 11/25/2019
ms.author: swmachan
ms.openlocfilehash: 3694c8cb34b2a050c9e18265c8cc0a0198456076
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533710"
---
# <a name="request-limits-for-translator-text"></a>Limites de solicitação para Tradução de Texto

Este artigo fornece limites de limitação para o API de Tradução de Texto. Os serviços incluem tradução, transliteração, detecção de comprimento de frase, detecção de idioma e traduções alternativas.

## <a name="character-and-array-limits-per-request"></a>Limites de caracteres e de matriz por solicitação

Cada solicitação de conversão é limitada a 5.000 caracteres. Você é cobrado por caractere, não pelo número de solicitações. É recomendável enviar solicitações mais curtas.

A tabela a seguir lista os limites de elemento e de caracteres de matriz para cada operação do API de Tradução de Texto.

| Operação | Tamanho máximo do elemento de matriz |   Número máximo de elementos da matriz |  Tamanho máximo da solicitação (caracteres) |
|:----|:----|:----|:----|
| Traduzir | 5\.000 | 100   | 5\.000 |
| Transliterar | 5\.000 | 10    | 5\.000 |
| Detetar | 10,000 | 100 |   50 000 |
| BreakSentence | 10,000    | 100 | 50 000 |
| Pesquisa no Dicionário| 100 |  10  | 1,000 |
| Exemplos do dicionário | 100 para texto e 100 para conversão (total de 200)| 10|   2,000 |

## <a name="character-limits-per-hour"></a>Limites de caracteres por hora

O limite de caracteres por hora é baseado em sua camada de assinatura Tradução de Texto. 

A cota por hora deve ser consumida uniformemente durante a hora. Por exemplo, no limite da camada F0 de 2 milhões caracteres por hora, os caracteres devem ser consumidos não mais rápido do que aproximadamente 33.300 caracteres por minuto janela deslizante (2 milhões caracteres divididos por 60 minutos).

Se você atingir ou ultrapassar esses limites, ou enviar um grande número de uma parte da cota em um curto período de tempo, provavelmente receberá uma resposta de cota insuficiente. Não há limites em solicitações simultâneas.

| Escalão | Limite de caracteres |
|------|-----------------|
| F0 | 2 milhões caracteres por hora |
| S1 | 40 milhões caracteres por hora |
| S2 / C2 | 40 milhões caracteres por hora |
| S3 / C3 | 120 milhões caracteres por hora |
| S4 / C4 | 200 milhões caracteres por hora |

Os limites para [assinaturas de vários serviços](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication) são os mesmos da camada S1.

Esses limites são restritos aos modelos de tradução padrão da Microsoft. Os modelos de tradução personalizados que usam o tradutor personalizado são limitados a 1.800 caracteres por segundo.

## <a name="latency"></a>Latência

A API de Tradução de Texto tem uma latência máxima de 15 segundos usando modelos padrão e 120 segundos ao usar modelos personalizados. Normalmente, as respostas *de texto dentro de 100 caracteres* são retornadas em 150 milissegundos a 300 milissegundos. Os modelos de Tradutor personalizados têm características de latência semelhantes na taxa de solicitação sustentada e podem ter uma latência mais alta quando a taxa de solicitação é intermitente. Os tempos de resposta variam de acordo com o tamanho da solicitação e do par de idiomas. Se você não receber uma conversão ou uma [resposta de erro](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors) dentro desse período, verifique seu código, sua conexão de rede e tente novamente. 

## <a name="sentence-length-limits"></a>Limites de comprimento da frase

Ao usar a função [BreakSentence](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-break-sentence) , o comprimento da frase é limitado a 275 caracteres. Há exceções para estes idiomas:

| Idioma | Código | Limite de caracteres |
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
> Esse limite não se aplica a traduções.

## <a name="next-steps"></a>Passos seguintes

* [Preços](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
* [Disponibilidade regional](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
* [V3 API de Tradução de Texto referência](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
