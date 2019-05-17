---
title: Suporte de idiomas - API de pesquisa Web Bing
titleSuffix: Azure Cognitive Services
description: Uma lista de idiomas, países e regiões que são suportados pela API de pesquisa de notícias do Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: article
ms.date: 05/15/2019
ms.author: aahi
ms.openlocfilehash: e657c4678c76e8ff667c1a3f30409fc157f52d16
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65798245"
---
# <a name="language-and-region-support-for-the-bing-web-search-api"></a>Suporte de idioma e região para a API de pesquisa Web Bing

A API de pesquisa Web Bing suporta mais de três doze países ou regiões, muitos com mais de um idioma. Especificação de um país ou região com uma consulta ajuda a refinar os resultados da pesquisa com base nos interesses desse país ou regiões. Os resultados podem incluir ligações para o Bing e estas ligações podem localizar a experiência do usuário do Bing, de acordo com o idioma ou país/região especificado.

Pode especificar um país ou região a utilizar o `cc` parâmetro de consulta. Quando é especificado um país ou região, tem de especificar um ou mais códigos de idioma com o [ `Accept-Language` cabeçalho](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#headers). Utilize o [tabela de mercados](#markets) para obter uma lista de linguagens suportadas em cada mercado.

Em alternativa, pode especificar o mercado com o `mkt` parâmetro de consulta e um código a partir de **mercados** tabela. Especificar um mercado em simultâneo Especifica um país ou região e um idioma preferencial. Pode definir explicitamente a linguagem com o `setLang` parâmetro de consulta.

## <a name="countriesregions"></a>Países/regiões

|País/região|Código|
|-------|----|
|Argentina|AR|
|Austrália|AU|
|Áustria|AT|
|Bélgica|BE|
|Brasil|BR|
|Canadá|AC|
|Chile|CL|
|Dinamarca|DK|
|Finlândia|FI|
|França|FR|
|Alemanha|DE|
|R.A.E. Hong Kong|HK|
|Índia|IN|
|Indonésia|ID|
|Itália|it|
|Japão|JP|
|Coreia|KR|
|Malásia|MY|
|México|MX|
|Países Baixos|NL|
|Nova Zelândia|NZ|
|Noruega|NÃO|
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

|País/região|Idioma|Código do mercado|
|-------|--------|-----------|
|Argentina|Espanhol|es-AR|
|Austrália|Inglês|EN-AU|
|Áustria|Alemão|Alemanha-AT|
|Bélgica|Neerlandês|nl-BE|
|Bélgica|Francês|fr-BE|
|Brasil|Português|pt-BR|
|Canadá|Inglês|en-CA|
|Canadá|Francês|fr-CA|
|Chile|Espanhol|es-CL|
|Dinamarca|Dinamarquês|da-DK|
|Finlândia|Finlandês|fi-FI|
|França|Francês|FR-FR|
|Alemanha|Alemão|de-DE|
|R.A.E. Hong Kong|Chinês tradicional|zh-HK|
|Índia|Inglês|EN-IN|
|Indonésia|Inglês|en-ID|
|Itália|Italiano|it-IT|
|Japão|Japonês|ja-JP|
|Coreia|Coreano|ko-KR|
|Malásia|Inglês|en-MY|
|México|Espanhol|es-MX|
|Países Baixos|Neerlandês|NL-NL|
|Nova Zelândia|Inglês|EN-NZ|
|Noruega|Norueguês|no-NO|
|China|Chinês|zh-CN|
|Polónia|Polaco|pl-PL|
|Portugal|Português|pt-PT|
|Filipinas|Inglês|en-PH|
|Rússia|Russo|ru-RU|
|Arábia Saudita|Árabe|ar SA|
|África do Sul|Inglês|en-ZA|
|Espanha|Espanhol|es-ES|
|Suécia|Sueco|SV-SE|
|Suíça|Francês|FR-CH|
|Suíça|Alemão|Alemanha-CH|
|Taiwan|Chinês tradicional|zh-TW|
|Turquia|Turco|tr-TR|
|Reino Unido|Inglês|en-GB|
|Estados Unidos|Inglês|en-US|
|Estados Unidos|Espanhol|es-US|

## <a name="next-steps"></a>Passos Seguintes

* [Bing Image Search API reference](//docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) (Referência da API de Pesquisa de Imagens do Bing)
