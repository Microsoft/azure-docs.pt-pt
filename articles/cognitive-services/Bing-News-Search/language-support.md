---
title: Suporte de idiomas - Bing News Search API
titleSuffix: Azure Cognitive Services
description: Uma lista de línguas naturais, países e regiões que são apoiados pela API bing news search.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 1/10/2019
ms.author: aahi
ms.openlocfilehash: d15058126f43fff328acfc563ffd081164a69a90
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78385074"
---
# <a name="language-and-region-support-for-the-bing-news-search-api"></a>Apoio à língua e à região para a API de Pesquisa de Notícias bing

A API bing news search apoia numerosos países/regiões, muitos com mais de uma língua. Especificar um país/região com uma consulta serve principalmente para refinar resultados de pesquisa baseados em interesses nesse país/região. Além disso, os resultados podem conter ligações ao Bing, e estas ligações podem localizar a experiência do utilizador Bing de acordo com o país/região ou língua especificado.

Pode especificar um país/região utilizando o parâmetro de consulta `cc`. Se especificar um país/região, também deve especificar um ou mais códigos linguísticos utilizando o cabeçalho `Accept-Language` HTTP. As línguas apoiadas variam consoante o conde/região; são dadas para cada país/região na tabela Mercados.

Em alternativa, pode especificar o mercado utilizando o parâmetro de consulta `mkt` e um código da tabela **Mercados.** Especificar um mercado simultaneamente especifica um país/região e uma língua preferida. Neste caso, o parâmetro de consulta `setLang` pode ser definido para um código linguístico; geralmente esta é a mesma língua especificada por `mkt` a menos que o utilizador prefira ver Bing em outro idioma.

## <a name="supported-markets-for-news-search-endpoint"></a>Mercados apoiados para ponto final de pesquisa de notícias

Para o `/news/search` ponto final, a tabela que se segue lista os valores de código de mercado que pode utilizar para especificar o parâmetro de consulta `mkt`. Bing devolve conteúdo apenas para estes mercados. A lista está sujeita a alterações.  

Para obter uma lista de códigos de país/região que possa especificar no parâmetro de consulta `cc`, consulte [Os Códigos do País](#countrycodes).  

|País/região|Idioma|Código de mercado|  
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
|Estados Unidos|Português|pt-PT|
|Português|geral|en-WW|
|Português|geral|en-XA|
|África do Sul|Português|en-ZA|
|Argentina|Espanhol|es-AR|
|Chile|Espanhol|es-CL|
|Espanha|Espanhol|es-ES|
|México|Espanhol|es-MX|
|Estados Unidos|Espanhol|es-EUA|
|Espanhol|geral|es-XL|
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

## <a name="supported-markets-for-news-endpoint"></a>Mercados apoiados para o ponto final das notícias
Para o `/news` ponto final, a tabela que se segue lista os valores de código de mercado que pode utilizar para especificar o parâmetro de consulta `mkt`. Bing devolve conteúdo apenas para estes mercados. A lista está sujeita a alterações.  

Para obter uma lista de códigos de país/região que possa especificar no parâmetro de consulta `cc`, consulte [Os Códigos do País](#countrycodes).  

|País/região|Idioma|Código de mercado|  
|---------------------|--------------|-----------------|
|Dinamarca|Dinamarquês|da-DK|
|Alemanha|Alemão|de-DE|
|Austrália|Português|EN-AU|
|Reino Unido|Português|en-GB|
|Estados Unidos|Português|pt-PT|
|Português|geral|en-WW|
|Chile|Espanhol|es-CL|
|México|Espanhol|es-MX|
|Estados Unidos|Espanhol|es-EUA|
|Finlândia|Finlandês|fi-FI|  
|Canadá|Francês|fr-CA|
|França|Francês|FR-FR|  
|Itália|Italiano|it-IT|
|Brasil|Português|pt-BR|
|República Popular da China|Chinês|zh-CN|

## <a name="supported-markets-for-news-trending-endpoint"></a>Mercados suportados para notícias em tendência final
Para o `/news/trendingtopics` ponto final, a tabela que se segue lista os valores de código de mercado que pode utilizar para especificar o parâmetro de consulta `mkt`. Bing devolve conteúdo apenas para estes mercados. A lista está sujeita a alterações.  

Para obter uma lista de códigos de país/região que possa especificar no parâmetro de consulta `cc`, consulte [Os Códigos do País](#countrycodes).  

|País/região|Idioma|Código de mercado|  
|---------------------|--------------|-----------------|
|Alemanha|Alemão|de-DE|
|Austrália|Português|EN-AU|
|Reino Unido|Português|en-GB|
|Estados Unidos|Português|pt-PT|
|Canadá|Português|en-CA|
|Índia|Português|EN-IN|
|França|Francês|FR-FR|
|Canadá|Francês|fr-CA|
|Brasil|Português|pt-BR|
|República Popular da China|Chinês|zh-CN|


<a name="countrycodes"></a>   
### <a name="country-codes"></a>Códigos do país  

Seguem-se os códigos país/região que pode especificar no parâmetro de consulta `cc`. A lista está sujeita a alterações.  

|País/região|Indicativo de país|  
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

## <a name="next-steps"></a>Passos seguintes
Para mais informações sobre os pontos finais da Pesquisa de Notícias bing, consulte [a referência da API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference)de Pesquisa de Notícias .
