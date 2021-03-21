---
title: Suporte linguístico - Bing Web Search API
titleSuffix: Azure Cognitive Services
description: Uma lista de línguas naturais, países e regiões que são apoiadas pela API de Pesquisa Web de Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: aahi
ms.openlocfilehash: 6de01d2c12454f43a2802b4a7a979b5f74b46f0e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96340276"
---
# <a name="language-and-region-support-for-the-bing-web-search-api"></a>Suporte linguístico e regional para a API de Pesquisa Web de Bing

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)

A API de Pesquisa Web Bing suporta mais de três dezenas de países ou regiões, muitos com mais de uma língua. Especificar um país ou região com uma consulta ajuda a refinar os resultados da pesquisa com base nesse país ou regiões interesses. Os resultados podem incluir ligações a Bing, e estas ligações podem localizar a experiência do utilizador Bing de acordo com o país/região ou idioma especificado.

Pode especificar um país ou região utilizando o `cc` parâmetro de consulta. Quando um país ou região é especificado, deve especificar um ou mais códigos linguísticos com o [ `Accept-Language` cabeçalho](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#headers). Utilize a [tabela Mercados](#markets) para uma lista de línguas suportadas em cada mercado.

Em alternativa, pode especificar o mercado com o `mkt` parâmetro de consulta e um código da tabela **Mercados.** Especificar um mercado especifica simultaneamente um país ou região e uma língua preferida. Pode definir explicitamente o idioma com o `setLang` parâmetro de consulta.

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
|R.A.E. de Hong Kong|HK|
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
|Estados Unidos da América|EUA|

## <a name="markets"></a>Mercados

|Pais/região|Linguagem|Código de Mercado|
|-------|--------|-----------|
|Argentina|Espanhol|es-AR|
|Austrália|Inglês|en-AU|
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
|R.A.E. de Hong Kong|Chinês Tradicional|zh-HK|
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
|Estados Unidos da América|Inglês|en-PT|
|Estados Unidos da América|Espanhol|es-EUA|

## <a name="next-steps"></a>Passos seguintes

* [Bing Image Search API reference](//docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) (Referência da API de Pesquisa de Imagens do Bing)