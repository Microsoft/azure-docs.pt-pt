---
title: Suporte de idiomas - API de pesquisa personalizada do Bing
titleSuffix: Azure Cognitive Services
description: Uma lista de idiomas suportados e regiões para a API de pesquisa personalizada do Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: aahi
ms.openlocfilehash: 85326ae9166f7ea15ec2f45c01755b8f9ef03aff
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66388568"
---
# <a name="language-and-region-support-for-the-bing-custom-search-api"></a>Suporte de idioma e região para a API de pesquisa personalizada do Bing

A API de pesquisa personalizada do Bing suporta mais de três doze países/regiões, muitos com mais de um idioma.

Embora seja opcional, a solicitação deve especificar o [mkt](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#mkt) parâmetro de consulta, que identifica onde pretende que os resultados provenientes de mercado. Para obter uma lista de parâmetros de consulta opcionais, consulte [parâmetros de consulta](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters)

Pode especificar um país/região a utilizar o `cc` parâmetro de consulta. Se especificar um país/região, também tem de especificar um ou mais códigos de idioma a utilizar o `Accept-Language` cabeçalho. Os idiomas com suporte variam consoante o país/região. são fornecidos para cada país/região no **mercados** tabela.

O `Accept-Language` cabeçalho e o `setLang` parâmetro de consulta são mutuamente exclusivos, não especifique ambos. Para obter detalhes, consulte [Accept-Language](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#acceptlanguage).

## <a name="countriesregions"></a>Países/regiões

|País/região|Código|
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

|País/região|Idioma|Código do mercado|
|-------|--------|-----------|
|Argentina|Espanhol|es-AR|
|Austrália|Português|EN-AU|
|Áustria|Alemão|Alemanha-AT|
|Bélgica|Neerlandês|nl-BE|
|Bélgica|Francês|fr-BE|
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
|Indonésia|Português|en-ID|
|Itália|Italiano|it-IT|
|Japão|Japonês|ja-JP|
|Coreia|Coreano|ko-KR|
|Malásia|Português|en-MY|
|México|Espanhol|es-MX|
|Países Baixos|Neerlandês|NL-NL|
|Nova Zelândia|Português|EN-NZ|
|Noruega|Norueguês|no-NO|
|China|Chinês|zh-CN|
|Polónia|Polaco|pl-PL|
|Portugal|Português|pt-PT|
|Filipinas|Português|en-PH|
|Rússia|Russo|ru-RU|
|Arábia Saudita|Árabe|ar SA|
|África do Sul|Português|en-ZA|
|Espanha|Espanhol|es-ES|
|Suécia|Sueco|SV-SE|
|Suíça|Francês|FR-CH|
|Suíça|Alemão|Alemanha-CH|
|Taiwan|Chinês tradicional|zh-TW|
|Turquia|Turco|tr-TR|
|Reino Unido|Português|en-GB|
|Estados Unidos|Português|en-US|
|Estados Unidos|Espanhol|es-US|
