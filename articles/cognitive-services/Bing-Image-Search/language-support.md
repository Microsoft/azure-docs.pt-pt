---
title: Suporte linguístico - Bing Image Search API
titleSuffix: Azure Cognitive Services
description: Descubra quais países/regiões e línguas são apoiados pela API de Pesquisa de Imagem bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: ca3821b6088e45730334d1b0971e270b1d86dfce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "68881912"
---
# <a name="language-and-region-support-for-the-bing-image-search-api"></a>Suporte de língua e região para a API de Pesquisa de Imagem bing

A API bing image search suporta mais de três dezenas de países/regiões, muitos com mais de uma língua. Especificar um país/região com uma consulta serve principalmente para refinar resultados de pesquisa baseados em interesses nesse país/região. Além disso, os resultados podem conter ligações ao Bing, e estas ligações podem localizar a experiência do utilizador Bing de acordo com o país/regiões ou língua especificados.

Para especificar o país/região `mkt` e a língua, coloque o parâmetro de consulta (mercado) num código da tabela **Markets** abaixo. O mercado especifica tanto um país/região como uma língua. Se o utilizador preferir ver texto de exibição num idioma diferente, defina `setLang` o parâmetro de consulta para o código de idioma apropriado.

Em alternativa, pode especificar o país/região utilizando o `cc` parâmetro de consulta. Se especificar um país/região, também deve especificar um `Accept-Language` ou mais códigos linguísticos utilizando o cabeçalho HTTP. As línguas apoiadas variam por país/região; são dadas para cada país/região na tabela Mercados.

> [!NOTE]
> A API trending Images suporta atualmente apenas os seguintes mercados:
> - en-EUA (Inglês, Estados Unidos)
> - en-CA (Inglês, Canadá)
> - en-UA (Inglês, Austrália)
> - zh-CN (China, China)

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
Para mais informações sobre os pontos finais da Pesquisa de Notícias bing, consulte [a referência da API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)de Pesquisa de Imagens de Notícias .
