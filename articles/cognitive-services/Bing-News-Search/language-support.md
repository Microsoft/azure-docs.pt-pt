---
title: Suporte de idiomas - API de pesquisa de notícias do Bing
titleSuffix: Azure Cognitive Services
description: Uma lista de idiomas, países e regiões que são suportados pela API de pesquisa de notícias do Bing.
services: cognitive-services
author: MikeDodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 1/10/2019
ms.author: v-gedod
ms.openlocfilehash: f94ebb9b46ade0c03f65fdeb78bc7be96794a3d2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60201419"
---
# <a name="language-and-region-support-for-the-bing-news-search-api"></a>Suporte de idioma e região para a API de pesquisa de notícias do Bing

A API de pesquisa de notícias do Bing suporta vários países/regiões, muitos com mais de um idioma. Especificação de um país/região com uma consulta serve principalmente para refinar os resultados da pesquisa com base nos interesses desse país/região. Além disso, os resultados podem conter hiperligações para do Bing e estas ligações podem localizar a experiência do usuário do Bing, de acordo com o idioma ou país/região especificado.

Pode especificar um país/região a utilizar o `cc` parâmetro de consulta. Se especificar um país/região, também tem de especificar um ou mais códigos de idioma a utilizar o `Accept-Language` cabeçalho de HTTP. Os idiomas com suporte variam consoante o countr/região. são fornecidos para cada país/região na tabela de mercados.

Em alternativa, pode especificar o mercado utilizando o `mkt` consultar o parâmetro e um código do **mercados** tabela. Especificar um mercado em simultâneo Especifica um país/região e um idioma preferencial. O `setLang` parâmetro de consulta pode ser definido como um código de idioma neste caso; normalmente, este é o mesmo idioma especificado pelo `mkt` , a menos que o utilizador prefere ver do Bing em outro idioma.

## <a name="supported-markets-for-news-search-endpoint"></a>Suportado mercados para o ponto final de pesquisa de notícias

Para o `/news/search` ponto final, a tabela seguinte lista os valores de código do mercado que pode ser usado para especificar o `mkt` parâmetro de consulta. O Bing devolve o conteúdo para apenas esses mercados. A lista está sujeita a alterações.  

Para obter uma lista de país/região códigos que especifique no `cc` parâmetro de consulta, consulte [códigos de país](#countrycodes).  

|País/região|Idioma|Código do mercado|  
|---------------------|--------------|-----------------|
|Dinamarca|Dinamarquês|da-DK|
|Áustria|Alemão|Alemanha-AT|
|Suíça|Alemão|Alemanha-CH|
|Alemanha|Alemão|de-DE|
|Austrália|Português|EN-AU|
|Canadá|Português|en-CA|
|Reino Unido|Português|en-GB|
|Indonésia|Português|en-ID|
|Irlanda|Português|EN-IE|
|Índia|Português|EN-IN|
|Malásia|Português|en-MY|
|Nova Zelândia|Português|EN-NZ|
|República das Filipinas|Português|en-PH|
|Singapura|Português|en-SG|
|Estados Unidos|Português|en-US|
|Português|Geral|en-WW|
|Português|Geral|en-XA|
|África do Sul|Português|en-ZA|
|Argentina|Espanhol|es-AR|
|Chile|Espanhol|es-CL|
|Espanha|Espanhol|es-ES|
|México|Espanhol|es-MX|
|Estados Unidos|Espanhol|es-US|
|Espanhol|Geral|es-XL|
|Finlândia|Finlandês|fi-FI|  
|França|Francês|fr-BE|
|Canadá|Francês|fr-CA|
|Bélgica|Neerlandês|nl-BE|
|Suíça|Francês|FR-CH|
|França|Francês|FR-FR|  
|Itália|Italiano|it-IT|
|RAE de Hong Kong|Chinês tradicional|zh-HK|  
|Taiwan|Chinês tradicional|zh-TW|
|Japão|Japonês|ja-JP|  
|Coreia|Coreano|ko-KR|  
|Países Baixos|Neerlandês|NL-NL|  
|República Popular da China|Chinês|zh-CN|  
|Brasil|Português|pt-BR|
|Rússia|Russo|ru-RU|  
|Suécia|Sueco|SV-SE|  
|Turquia|Turco|tr-TR|  

## <a name="supported-markets-for-news-endpoint"></a>Mercados suportados para o ponto final de notícias
Para o `/news` ponto final, a tabela seguinte lista os valores de código do mercado que pode ser usado para especificar o `mkt` parâmetro de consulta. O Bing devolve o conteúdo para apenas esses mercados. A lista está sujeita a alterações.  

Para obter uma lista de país/região códigos que especifique no `cc` parâmetro de consulta, consulte [códigos de país](#countrycodes).  

|País/região|Idioma|Código do mercado|  
|---------------------|--------------|-----------------|
|Dinamarca|Dinamarquês|da-DK|
|Alemanha|Alemão|de-DE|
|Austrália|Português|EN-AU|
|Reino Unido|Português|en-GB|
|Estados Unidos|Português|en-US|
|Português|Geral|en-WW|
|Chile|Espanhol|es-CL|
|México|Espanhol|es-MX|
|Estados Unidos|Espanhol|es-US|
|Finlândia|Finlandês|fi-FI|  
|Canadá|Francês|fr-CA|
|França|Francês|FR-FR|  
|Itália|Italiano|it-IT|
|Brasil|Português|pt-BR|
|República Popular da China|Chinês|zh-CN|

## <a name="supported-markets-for-news-trending-endpoint"></a>Suportado mercados para o ponto final mais populares de notícias
Para o `/news/trendingtopics` ponto final, a tabela seguinte lista os valores de código do mercado que pode ser usado para especificar o `mkt` parâmetro de consulta. O Bing devolve o conteúdo para apenas esses mercados. A lista está sujeita a alterações.  

Para obter uma lista de país/região códigos que especifique no `cc` parâmetro de consulta, consulte [códigos de país](#countrycodes).  

|País/região|Idioma|Código do mercado|  
|---------------------|--------------|-----------------|
|Alemanha|Alemão|de-DE|
|Austrália|Português|EN-AU|
|Reino Unido|Português|en-GB|
|Estados Unidos|Português|en-US|
|Canadá|Português|en-CA|
|Índia|Português|EN-IN|
|França|Francês|FR-FR|
|Canadá|Francês|fr-CA|
|Brasil|Português|pt-BR|
|República Popular da China|Chinês|zh-CN|


<a name="countrycodes"></a>   
### <a name="country-codes"></a>Códigos de país  

Seguem-se os códigos de país/região que pode especificar o `cc` parâmetro de consulta. A lista está sujeita a alterações.  

|País/região|Indicativo do país|  
|---------------------|------------------|  
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
|República Popular da China|CN|  
|Polónia|PL|  
|Portugal|PT|  
|República das Filipinas|PH|  
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

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre os pontos de extremidade de pesquisa do Bing notícias, consulte [referência da API de pesquisa de notícias v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference).
