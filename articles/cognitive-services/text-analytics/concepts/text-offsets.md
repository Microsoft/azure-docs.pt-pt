---
title: Compensações de texto na API de Análise de Texto
titleSuffix: Azure Cognitive Services
description: Aprenda sobre as compensações causadas por codificações multilingues e emojis.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 03/09/2020
ms.author: aahi
ms.reviewer: jdesousa
ms.openlocfilehash: 6e404c710a244f06676edf50c3f5c95a7d681e35
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "79219238"
---
# <a name="text-offsets-in-the-text-analytics-api-output"></a>Compensações de texto na saída da API de Análise de Texto

O suporte multilingual e emoji levou a codificações unicode que usam mais de um [ponto de código](https://wikipedia.org/wiki/Code_point) para representar um único personagem exibido, chamado grafeme. Por exemplo, emojis 👍 como 🌷 e podem usar vários caracteres para compor a forma com caracteres adicionais para atributos visuais, como o tom de pele. Da mesma forma, `अनुच्छेद` a palavra hindi é codificada como cinco letras e três marcas combinadas.

Devido aos diferentes comprimentos de possíveis codificações multilingues e emojis, a API text Analytics pode devolver compensações na resposta.

## <a name="offsets-in-the-api-response"></a>Compensações na resposta da API. 

Sempre que forem devolvidas as compensações a resposta da API, como o Reconhecimento de [Entidades Nomeadas](../how-tos/text-analytics-how-to-entity-linking.md) ou a Análise de [Sentimentos,](../how-tos/text-analytics-how-to-sentiment-analysis.md)lembre-se do seguinte:

* Os elementos na resposta podem ser específicos do ponto final que foi chamado. 
* HTTP POST/GET as cargas são codificadas em [UTF-8](https://www.w3schools.com/charsets/ref_html_utf8.asp), que pode ou não ser a codificação padrão de caracteres no seu compilador ou sistema operativo do lado do cliente.
* As compensações referem-se às contagens de grafeme com base no padrão [Unicode 8.0.0,](https://unicode.org/versions/Unicode8.0.0) e não nas contagens de caracteres.

## <a name="extracting-substrings-from-text-with-offsets"></a>Extrair subcordas do texto com compensações

As compensações podem causar problemas ao utilizar métodos de substring baseados no carácter, por exemplo, o método de [substring .NET().](https://docs.microsoft.com/dotnet/api/system.string.substring?view=netframework-4.8) Um problema é que uma compensação pode fazer com que um método de subcadeia termine no meio de uma codificação de grafeme multi-caracteres em vez do fim.

Em .NET, considere usar a classe [StringInfo,](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo?view=netframework-4.8) que lhe permite trabalhar com uma corda como uma série de elementos texuais, em vez de objetos de caracteres individuais. Também pode procurar bibliotecas de splitter de grafeme no seu ambiente de software preferido. 

A API text Analytics devolve estes elementos texuais também, por conveniência.

## <a name="see-also"></a>Consulte também

* [Descrição Geral da Análise de Texto](../overview.md)
* [Análise de sentimentos](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Reconhecimento de entidades](../how-tos/text-analytics-how-to-entity-linking.md)
* [Detetar idioma](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Reconhecimento linguístico](../how-tos/text-analytics-how-to-language-detection.md)
