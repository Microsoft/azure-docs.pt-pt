---
title: Suporte linguístico - Bing News Search API
titleSuffix: Azure Cognitive Services
description: Uma lista de línguas naturais, países e regiões que são apoiadas pela API de Pesquisa de Notícias Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 1/10/2019
ms.author: aahi
ms.openlocfilehash: d15058126f43fff328acfc563ffd081164a69a90
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84710563"
---
# <a name="language-and-region-support-for-the-bing-news-search-api"></a>Suporte linguístico e regional para a API de Pesquisa de Notícias Bing

A API Bing News Search suporta inúmeros países/regiões, muitos com mais de uma língua. Especificar um país/região com uma consulta serve principalmente para refinar os resultados da pesquisa com base em interesses nesse país/região. Além disso, os resultados podem conter ligações com Bing, e estas ligações podem localizar a experiência do utilizador Bing de acordo com o país/região ou idioma especificado.

Pode especificar um país/região utilizando o `cc` parâmetro de consulta. Se especificar um país/região, também deve especificar um ou mais códigos linguísticos utilizando o `Accept-Language` cabeçalho HTTP. As línguas apoiadas variam consoante a contagem/região; são dados para cada país/região na tabela Mercados.

Em alternativa, pode especificar o mercado utilizando o `mkt` parâmetro de consulta e um código da tabela **Mercados.** Especificar um mercado especifica simultaneamente um país/região e uma língua preferida. O `setLang` parâmetro de consulta pode ser definido para um código linguístico neste caso; normalmente este é o mesmo idioma especificado `mkt` por, a menos que o utilizador prefira ver Bing em outra língua.

## <a name="supported-markets-for-news-search-endpoint"></a>Mercados apoiados para o ponto final de pesquisa de notícias

Para o `/news/search` ponto final, a tabela a seguir lista os valores de código de mercado que pode utilizar para especificar o `mkt` parâmetro de consulta. Bing devolve conteúdo apenas para estes mercados. A lista está sujeita a alterações.  

Para obter uma lista de códigos de país/região que pode especificar no `cc` parâmetro de consulta, consulte [Códigos de País](#countrycodes).  

|Pais/região|Idioma|Código de mercado|  
|---------------------|--------------|-----------------|
|Dinamarca|Dinamarquês|da-DK|
|Áustria|Alemão|de-AT|
|Suíça|Alemão|de-CH|
|Alemanha|Alemão|de-DE|
|Austrália|Inglês|en-AU|
|Canadá|Inglês|en-CA|
|Reino Unido|Inglês|en-GB|
|Indonésia|Inglês|en-ID|
|Irlanda|Inglês|en-IE|
|Índia|Inglês|en-IN|
|Malásia|Inglês|en-MY|
|Nova Zelândia|Inglês|en-NZ|
|República das Filipinas|Inglês|en-PH|
|Singapura|Inglês|en-SG|
|Estados Unidos da América|Inglês|pt-PT|
|Inglês|geral|en-WW|
|Inglês|geral|en-XA|
|África do Sul|Inglês|en-ZA|
|Argentina|Espanhol|es-AR|
|Chile|Espanhol|es-CL|
|Espanha|Espanhol|es-ES|
|México|Espanhol|es-MX|
|Estados Unidos da América|Espanhol|es-EUA|
|Espanhol|geral|es-XL|
|Finlândia|Finlandês|fi-FI|  
|França|Francês|fr-BE|
|Canadá|Francês|fr-CA|
|Bélgica|Neerlandês|nl-BE|
|Suíça|Francês|fr-CH|
|França|Francês|fr-FR|  
|Itália|Italiano|it-IT|
|R.A.E. de Hong Kong|Chinês Tradicional|zh-HK|  
|Taiwan|Chinês Tradicional|zh-TW|
|Japão|Japonês|ja-JP|  
|Coreia|Coreano|ko-KR|  
|Países Baixos|Neerlandês|nl-NL|  
|República Popular da China|Chinês|zh-CN|  
|Brasil|Português|pt-BR|
|Rússia|Russo|ru-RU|  
|Suécia|Sueco|sv-SE|  
|Turquia|Turco|tr-TR|  

## <a name="supported-markets-for-news-endpoint"></a>Mercados apoiados para o ponto final de notícias
Para o `/news` ponto final, a tabela a seguir lista os valores de código de mercado que pode utilizar para especificar o `mkt` parâmetro de consulta. Bing devolve conteúdo apenas para estes mercados. A lista está sujeita a alterações.  

Para obter uma lista de códigos de país/região que pode especificar no `cc` parâmetro de consulta, consulte [Códigos de País](#countrycodes).  

|Pais/região|Idioma|Código de mercado|  
|---------------------|--------------|-----------------|
|Dinamarca|Dinamarquês|da-DK|
|Alemanha|Alemão|de-DE|
|Austrália|Inglês|en-AU|
|Reino Unido|Inglês|en-GB|
|Estados Unidos da América|Inglês|pt-PT|
|Inglês|geral|en-WW|
|Chile|Espanhol|es-CL|
|México|Espanhol|es-MX|
|Estados Unidos da América|Espanhol|es-EUA|
|Finlândia|Finlandês|fi-FI|  
|Canadá|Francês|fr-CA|
|França|Francês|fr-FR|  
|Itália|Italiano|it-IT|
|Brasil|Português|pt-BR|
|República Popular da China|Chinês|zh-CN|

## <a name="supported-markets-for-news-trending-endpoint"></a>Mercados apoiados para o ponto final da tendência noticiosa
Para o `/news/trendingtopics` ponto final, a tabela a seguir lista os valores de código de mercado que pode utilizar para especificar o `mkt` parâmetro de consulta. Bing devolve conteúdo apenas para estes mercados. A lista está sujeita a alterações.  

Para obter uma lista de códigos de país/região que pode especificar no `cc` parâmetro de consulta, consulte [Códigos de País](#countrycodes).  

|Pais/região|Idioma|Código de mercado|  
|---------------------|--------------|-----------------|
|Alemanha|Alemão|de-DE|
|Austrália|Inglês|en-AU|
|Reino Unido|Inglês|en-GB|
|Estados Unidos da América|Inglês|pt-PT|
|Canadá|Inglês|en-CA|
|Índia|Inglês|en-IN|
|França|Francês|fr-FR|
|Canadá|Francês|fr-CA|
|Brasil|Português|pt-BR|
|República Popular da China|Chinês|zh-CN|


<a name="countrycodes"></a>   
### <a name="country-codes"></a>Códigos do país  

Seguem-se os códigos país/região que pode especificar no `cc` parâmetro de consulta. A lista está sujeita a alterações.  

|Pais/região|Indicativo do país|  
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
|Estados Unidos da América|EUA|

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre os pontos finais da Bing News Search, consulte [a referência API v7 de pesquisa de notícias.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference)
