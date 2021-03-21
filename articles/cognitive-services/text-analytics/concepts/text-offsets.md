---
title: Compensações de texto na API de Análise de Texto
titleSuffix: Azure Cognitive Services
description: Saiba mais sobre as compensações causadas por codificações multilingues e emojis.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 03/09/2020
ms.author: aahi
ms.reviewer: jdesousa
ms.openlocfilehash: f5b63503792b13e089568004ba67e5be8a3d0c7f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98932368"
---
# <a name="text-offsets-in-the-text-analytics-api-output"></a>Compensações de texto na saída da API de Análise de Texto

O suporte multilíngue e emoji levou a codificações Unicode que usam mais de um ponto de [código](https://wikipedia.org/wiki/Code_point) para representar um único carácter exibido, chamado grafeme. Por exemplo, emojis como 🌷 e 👍 podem usar vários caracteres para compor a forma com caracteres adicionais para atributos visuais, como o tom de pele. Da mesma forma, a palavra hindi `अनुच्छेद` é codificada como cinco letras e três marcas combinadas.

Devido aos diferentes comprimentos de possíveis codificações multilingues e emojis, a API text Analytics pode devolver compensações na resposta.

## <a name="offsets-in-the-api-response"></a>Compensações na resposta da API. 

Sempre que forem devolvidas as compensações, a resposta da API, como [reconhecimento de entidades nomeadas](../how-tos/text-analytics-how-to-entity-linking.md) ou [análise de sentimento,](../how-tos/text-analytics-how-to-sentiment-analysis.md)lembre-se do seguinte:

* Os elementos da resposta podem ser específicos do ponto final que foi chamado. 
* AS cargas POST/GET HTTP são codificadas em [UTF-8,](https://www.w3schools.com/charsets/ref_html_utf8.asp)que pode ou não ser a codificação de caracteres padrão no seu compilador ou sistema operativo do lado do cliente.
* As compensações referem-se às contagens de grafeme com base na norma [Unicode 8.0.0,](https://unicode.org/versions/Unicode8.0.0) não contagens de caracteres.

## <a name="extracting-substrings-from-text-with-offsets"></a>Extrair sublagem de texto com compensações

As compensações podem causar problemas ao utilizar métodos de sublagem baseados no carácter, por exemplo, o método [de sublagem](/dotnet/api/system.string.substring) .NET(). Um problema é que uma compensação pode fazer com que um método de substring termine no meio de uma codificação de grafeme de vários caracteres em vez do fim.

Em .NET, considere a utilização da classe [StringInfo,](/dotnet/api/system.globalization.stringinfo) que lhe permite trabalhar com uma corda como uma série de elementos texcionais, em vez de objetos de caracteres individuais. Também pode procurar bibliotecas de splitter de grafeme no seu ambiente de software preferido. 

A API text Analytics devolve estes elementos texcionais também, por conveniência.

## <a name="offsets-in-api-version-31-preview"></a>Compensações na versão API 3.1-pré-visualização

Começando pela versão API 3.1-preview.1, todos os pontos finais da API API de Análise de Texto que devolvem uma compensação irão suportar o `stringIndexType` parâmetro. Este parâmetro ajusta os `offset` atributos e `length` atributos na saída API para corresponder ao esquema de iteração de cordas solicitado. Atualmente, apoiamos três tipos:

1. `textElement_v8`(predefinição): iterados sobre grafemes definidos pela norma [Unicode 8.0.0](https://unicode.org/versions/Unicode8.0.0)
2. `unicodeCodePoint`: iterates over [Unicode Code Points](http://www.unicode.org/versions/Unicode13.0.0/ch02.pdf#G25564), o esquema padrão para Python 3
3. `utf16CodeUnit`: iterates sobre [utf-16 Unidades de Código](https://unicode.org/faq/utf_bom.html#UTF16), o regime padrão para Javascript, Java e .NET

Se o `stringIndexType` pedido corresponder ao ambiente de programação de eleição, a extração de sub-cordas pode ser feita utilizando métodos de sublagem ou fatias padrão. 

## <a name="see-also"></a>Ver também

* [Descrição geral da Análise de Texto](../overview.md)
* [Análise de sentimentos](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Reconhecimento de entidades](../how-tos/text-analytics-how-to-entity-linking.md)
* [Detetar linguagem](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Reconhecimento linguístico](../how-tos/text-analytics-how-to-language-detection.md)
