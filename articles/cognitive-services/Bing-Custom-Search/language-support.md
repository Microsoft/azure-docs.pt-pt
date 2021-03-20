---
title: Suporte linguístico - Bing Custom Search API
titleSuffix: Azure Cognitive Services
description: Uma lista de línguas e regiões apoiadas para a API de Pesquisa Personalizada Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: aahi
ms.openlocfilehash: 73b25ce1093ce697aa0937161d2af49526c231b7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96352359"
---
# <a name="language-and-region-support-for-the-bing-custom-search-api"></a>Suporte linguístico e regional para a API de Pesquisa Personalizada Bing

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)

A API de Pesquisa Personalizada Bing suporta mais de três dezenas de países/regiões, muitos com mais de uma língua.

Embora seja opcional, o pedido deve especificar o parâmetro de consulta [mkt,](/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#mkt) que identifica o mercado de onde deseja que os resultados venham. Para obter uma lista de parâmetros de consulta opcionais, consulte [Parâmetros de Consulta](/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters)

Pode especificar um país/região utilizando o `cc` parâmetro de consulta. Se especificar um país/região, também deve especificar um ou mais códigos linguísticos utilizando o `Accept-Language` cabeçalho. As línguas apoiadas variam por país/região; são dados para cada país/região na tabela **Mercados.**

O `Accept-Language` cabeçalho e o `setLang` parâmetro de consulta são mutuamente exclusivos — não especifique ambos. Para mais detalhes, consulte [a Língua de Aceitação.](/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#acceptlanguage)

## <a name="countriesregions"></a>Países/Regiões

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