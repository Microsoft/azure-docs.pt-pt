---
title: Suporte ao idioma-API de Pesquisa na Web do Bing
titleSuffix: Azure Cognitive Services
description: Uma lista de idiomas, países e regiões naturais com suporte no API de Pesquisa de Notícias do Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: aahi
ms.openlocfilehash: 9425de6e75a9a46d71ff85ce49b0650c8e7a9a16
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68882679"
---
# <a name="language-and-region-support-for-the-bing-web-search-api"></a>Suporte a idiomas e regiões para o API de Pesquisa na Web do Bing

O API de Pesquisa na Web do Bing dá suporte a mais de três dezenas de países ou regiões, muitos com mais de um idioma. A especificação de um país ou região com uma consulta ajuda a refinar os resultados da pesquisa com base nesses interesses de país ou região. Os resultados podem incluir links para o Bing, e esses links podem localizar a experiência do usuário do Bing de acordo com o país/região ou idioma especificado.

Você pode especificar um país ou uma região usando `cc` o parâmetro de consulta. Quando um país ou região é especificado, você deve especificar um ou mais códigos de idioma com o [ `Accept-Language` cabeçalho](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#headers). Use a [tabela Markets](#markets) para obter uma lista de idiomas com suporte em cada mercado.

Como alternativa, você pode especificar o mercado com o `mkt` parâmetro de consulta e um código da tabela **Markets** . Especificar um mercado simultaneamente especifica um país ou região e um idioma preferencial. Você pode definir explicitamente o idioma com o `setLang` parâmetro de consulta.

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
|RAE de Hong Kong|HK|
|Índia|IN|
|Indonésia|id|
|Itália|it|
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

|País/região|Idioma|Código do mercado|
|-------|--------|-----------|
|Argentina|Espanhol|es-AR|
|Austrália|Português|EN-AU|
|Áustria|Alemão|Alemanha-AT|
|Bélgica|Neerlandês|NL-ser|
|Bélgica|Francês|fr-ser|
|Brasil|Português|pt-BR|
|Canadá|Português|en-CA|
|Canadá|Francês|fr-CA|
|Chile|Espanhol|es-CL|
|Dinamarca|Dinamarquês|da-DK|
|Finlândia|Finlandês|fi-FI|
|França|Francês|FR-FR|
|Alemanha|Alemão|de-DE|
|RAE de Hong Kong|Chinês tradicional|zh-HK|
|Índia|Português|EN-IN|
|Indonésia|Português|ID do en|
|Itália|Italiano|it-IT|
|Japão|Japonês|ja-JP|
|Coreia|Coreano|ko-KR|
|Malásia|Português|EN-MY|
|México|Espanhol|es-MX|
|Países Baixos|Neerlandês|NL-NL|
|Nova Zelândia|Português|EN-NZ|
|Noruega|Norueguês|no-NO|
|China|Chinês|zh-CN|
|Polónia|Polaco|pl-PL|
|Portugal|Português|pt-PT|
|Filipinas|Português|EN-PH|
|Rússia|Russo|ru-RU|
|Arábia Saudita|Árabe|ar SA|
|África do Sul|Português|EN-ZA|
|Espanha|Espanhol|es-ES|
|Suécia|Sueco|SV-SE|
|Suíça|Francês|FR-CH|
|Suíça|Alemão|Alemanha-CH|
|Taiwan|Chinês tradicional|zh-TW|
|Turquia|Turco|tr-TR|
|Reino Unido|Português|en-GB|
|Estados Unidos|Português|en-US|
|Estados Unidos|Espanhol|es-EUA|

## <a name="next-steps"></a>Passos Seguintes

* [Bing Image Search API reference](//docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) (Referência da API de Pesquisa de Imagens do Bing)
