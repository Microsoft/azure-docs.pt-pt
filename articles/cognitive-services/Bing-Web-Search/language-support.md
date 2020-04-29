---
title: Suporte linguístico - Bing Web Search API
titleSuffix: Azure Cognitive Services
description: Uma lista de línguas naturais, países e regiões que são apoiados pela API bing news search.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: aahi
ms.openlocfilehash: 9425de6e75a9a46d71ff85ce49b0650c8e7a9a16
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "68882679"
---
# <a name="language-and-region-support-for-the-bing-web-search-api"></a>Suporte de língua e região para a API de pesquisa web bing

A API bing Web Search suporta mais de três dezenas de países ou regiões, muitos com mais de uma língua. Especificar um país ou região com uma consulta ajuda a refinar os resultados de pesquisa com base nos interesses do país ou das regiões. Os resultados podem incluir ligações ao Bing, e estas ligações podem localizar a experiência do utilizador Bing de acordo com o país/região ou língua especificado.

Pode especificar um país ou `cc` região utilizando o parâmetro de consulta. Quando um país ou região é especificado, deve especificar um ou mais códigos linguísticos com o [ `Accept-Language` cabeçalho](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#headers). Utilize a [tabela Mercados](#markets) para uma lista de línguas suportadas em cada mercado.

Em alternativa, pode especificar o `mkt` mercado com o parâmetro de consulta e um código da tabela **Mercados.** Especificar um mercado simultaneamente especifica um país ou região e uma língua preferida. Pode definir explicitamente a `setLang` linguagem com o parâmetro de consulta.

## <a name="countriesregions"></a>Países/regiões

|Pais/região|Código|
|-------|----|
|Argentina|AR|
|Austrália|AU|
|Áustria|AT|
|Bélgica|BE|
|Brasil|BR|
|Canadá|CA|
|Chile|CL|
|Dinamarca|DK|
|Finlândia|FI|
|França|FR|
|Alemanha|DE|
|RAE de Hong Kong|HK|
|Índia|IN|
|Indonésia|ID|
|Itália|TI|
|Japão|JP|
|Coreia|KR|
|Malásia|MY|
|México|MX|
|Países Baixos|NL|
|Nova Zelândia|NZ|
|Noruega|NO|
|China|CN|
|Polónia|PL|
|Portugal|PT|
|Filipinas|PH|
|Rússia|RU|
|Arábia Saudita|SA|
|África do Sul|ZA|
|Espanha|ES|
|Suécia|SE|
|Suíça|CH|
|Taiwan|TW|
|Turquia|TR|
|Reino Unido|GB|
|Estados Unidos|EUA|

## <a name="markets"></a>Mercados

|Pais/região|Idioma|Código de Mercado|
|-------|--------|-----------|
|Argentina|Espanhol|es-AR|
|Austrália|Inglês|en-UA|
|Áustria|Alemão|de-AT|
|Bélgica|Neerlandês|nl-BE|
|Bélgica|Francês|fr-BE|
|Brasil|Português|pt-BR|
|Canadá|Inglês|en-CA|
|Canadá|Francês|fr-CA|
|Chile|Espanhol|es-CL|
|Dinamarca|Dinamarquês|da-DK|
|Finlândia|Finlandês|fi-FI|
|França|Francês|fr-FR|
|Alemanha|Alemão|de-DE|
|RAE de Hong Kong|Chinês Tradicional|zh-HK|
|Índia|Inglês|en-IN|
|Indonésia|Inglês|en-ID|
|Itália|Italiano|it-IT|
|Japão|Japonês|ja-JP|
|Coreia|Coreano|ko-KR|
|Malásia|Inglês|en-MY|
|México|Espanhol|es-MX|
|Países Baixos|Neerlandês|nl-NL|
|Nova Zelândia|Inglês|en-NZ|
|Noruega|Norueguês|não-NÃO|
|China|Chinês|zh-CN|
|Polónia|Polaco|pl-PL|
|Portugal|Português|pt-PT|
|Filipinas|Inglês|en-PH|
|Rússia|Russo|ru-RU|
|Arábia Saudita|Árabe|ar-SA|
|África do Sul|Inglês|en-ZA|
|Espanha|Espanhol|es-ES|
|Suécia|Sueco|sv-SE|
|Suíça|Francês|fr-CH|
|Suíça|Alemão|de-CH|
|Taiwan|Chinês Tradicional|zh-TW|
|Turquia|Turco|tr-TR|
|Reino Unido|Inglês|en-GB|
|Estados Unidos|Inglês|pt-PT|
|Estados Unidos|Espanhol|es-EUA|

## <a name="next-steps"></a>Passos seguintes

* [Bing Image Search API reference](//docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) (Referência da API de Pesquisa de Imagens do Bing)
