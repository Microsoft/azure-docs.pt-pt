---
title: Estrutura de resposta e tipos de resposta da API de pesquisa web bing web
titleSuffix: Azure Cognitive Services
description: Ao enviar um pedido de pesquisa no `SearchResponse` Bing Web Search, devolve um objeto no corpo de resposta.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 95ebfaef863a1fa05e8a5d3b46fca9659c61f6b7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "74110626"
---
# <a name="bing-web-search-api-response-structure-and-answer-types"></a>Estrutura de resposta e tipos de resposta da API de pesquisa web bing web  

Ao enviar um pedido de pesquisa no [`SearchResponse`](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#searchresponse) Bing Web Search, devolve um objeto no corpo de resposta. O objeto inclui um campo para cada resposta que Bing determinou ser relevante para a consulta. Este exemplo ilustra um objeto de resposta se Bing devolveu todas as respostas:

```json
{
    "_type": "SearchResponse",
    "queryContext": {...},
    "webPages": {...},
    "images": {...},
    "relatedSearches": {...},
    "videos": {...},
    "news": {...},
    "spellSuggestion": {...},
    "computation": {...},
    "timeZone": {...},
    "rankingResponse": {...}
}, ...
```

Tipicamente, bing Web Search devolve um subconjunto das respostas. Por exemplo, se o termo de consulta fosse *velando dinghies*, a resposta poderia incluir `webPages`, `images`e `rankingResponse`. A menos que tenha usado [o filtro](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#responsefilter) de resposta `webpages` para `rankingResponse` filtrar páginas web, a resposta inclui sempre as respostas e respostas.

[!INCLUDE [cognitive-services-bing-url-note](../../../includes/cognitive-services-bing-url-note.md)]

## <a name="webpages-answer"></a>Resposta de páginas web

A resposta [webPages](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webanswer) contém uma lista de links para páginas web que bing Web Search determinada si são relevantes para a consulta. Cada [página web](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webpage) da lista incluirá: o nome da página, url, URL de exibição, uma descrição curta do conteúdo, e a data em que Bing encontrou o conteúdo.

```json
{
    "id": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0",
    "name": "Dinghy sailing",
    "url": "https:\/\/www.bing.com\/cr?IG=3A43CA5...",
    "displayUrl": "https:\/\/en.contoso.com\/wiki\/Dinghy_sailing",
    "snippet": "Dinghy sailing is the activity of sailing small boats...",
    "dateLastCrawled": "2017-04-05T16:25:00"
}, ...
```

Utilize `name` `url` e crie uma hiperligação que leve o utilizador à página web.

<!-- Remove until this can be replaced with a sanitized version.
The following shows an example of how you might display the webpage in a search results page.

![Rendered webpage example](./media/cognitive-services-bing-web-api/bing-rendered-webpage-example.PNG)
-->

## <a name="images-answer"></a>Resposta das imagens

A resposta das [imagens](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) contém uma lista de imagens que Bing pensava serem relevantes para a consulta. Cada [imagem](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) da lista inclui o URL da imagem, o seu tamanho, as suas dimensões e o seu formato de codificação. O objeto de imagem também inclui o URL de uma miniatura da imagem e as dimensões da miniatura.

```json
{
    "name": "Rich Passage Sailing Dinghy",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=3A43CA5CA64...",
    "thumbnailUrl": "https:\/\/tse1.mm.bing.net\/th?id=OIP....",
    "datePublished": "2011-10-29T11:26:00",
    "contentUrl": "http:\/\/upload.contoso.com\/sailing\/...",
    "hostPageUrl": "http:\/\/www.bing.com\/cr?IG=3A43CA5CA6464....",
    "contentSize": "79239 B",
    "encodingFormat": "jpeg",
    "hostPageDisplayUrl": "http:\/\/en.contoso.com\/wiki\/File...",
    "width": 526,
    "height": 688,
    "thumbnail": {
        "width": 229,
        "height": 300
    },
    "insightsSourcesSummary": {
        "shoppingSourcesCount": 0,
        "recipeSourcesCount": 0
    }
}, ...
```

Dependendo do dispositivo do utilizador, normalmente exibe um subconjunto das miniaturas, com a opção de o utilizador [páginar através](paging-webpages.md) das restantes imagens.

<!-- Remove until this can be replaced with a sanitized version.
![List of thumbnail images](./media/cognitive-services-bing-web-api/bing-web-image-thumbnails.PNG)
-->

Também pode expandir a miniatura à medida que o utilizador paira o cursor sobre a mesma. Lembre-se de atribuir a imagem se a expandir. Por exemplo, extraindo `hostPageDisplayUrl` o hospedeiro e exibindo-o abaixo da imagem. Para obter informações sobre o redimensionamento da miniatura, veja [Redimensionar e Recortar Miniaturas](./resize-and-crop-thumbnails.md).

<!-- Remove until this can be replaced with a sanitized version.
![Expanded view of thumbnail image](./media/cognitive-services-bing-web-api/bing-web-image-thumbnail-expansion.PNG)
-->

Se o utilizador clicar na `webSearchUrl` miniatura, utilize para levar o utilizador à página de resultados de pesquisa do Bing para obter imagens, que contém uma colagem das imagens.

Para mais detalhes sobre a resposta da imagem e imagens, consulte a [Image Search API](../bing-image-search/search-the-web.md).

## <a name="related-searches-answer"></a>Resposta de pesquisas relacionadas

A resposta [relacionadaAs Pesquisas](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#searchresponse-relatedsearches) contém uma lista das consultas relacionadas mais populares feitas por outros utilizadores. Cada [consulta](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query_obj) na lista inclui uma`text`corda de consulta ( ), uma`displayText`corda de`webSearchUrl`consulta com caracteres de destaque de sucesso ( ) e um URL ( ) para a página de resultados de pesquisa de Bing para essa consulta.

```json
{
    "text": "dinghy racing teams",
    "displayText": "dinghy racing teams",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=96C4CF214A0..."
}, ...
```

Utilize `displayText` a cadeia de `webSearchUrl` consulta e o URL para criar uma hiperligação que leve o utilizador à página de resultados de pesquisa do Bing para a consulta relacionada. Também pode utilizar `text` a cadeia de consulta na sua própria consulta de Pesquisa Web API e exibir os resultados por si mesmo.

Para obter informações sobre como lidar `displayText`com os marcadores de destaque, consulte [O Destaque de Hit](../bing-web-search/hit-highlighting.md).

O seguinte mostra um exemplo do uso de consultas relacionadas em Bing.com.

![Pesquisas relacionadas exemplo em Bing](./media/cognitive-services-bing-web-api/bing-web-rendered-relatedsearches.GIF)

## <a name="videos-answer"></a>Resposta de vídeos

A resposta dos [vídeos](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) contém uma lista de vídeos que Bing pensava serem relevantes para a consulta. Cada [vídeo](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video) na lista inclui o URL do vídeo, a sua duração, as suas dimensões e o seu formato de codificação. O objeto de vídeo também inclui o URL de uma miniatura do vídeo e as dimensões da miniatura.

```json
{
    "name": "Sailing dinghy",
    "description": "Northwind Traders is a 12 foot gunter rigged...",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=1CAE739681D84...",
    "thumbnailUrl": "https:\/\/tse2.mm.bing.net\/th?id=OVP.wsKiL...",
    "datePublished": "2013-11-06T01:56:28",
    "publisher": [{
        "name": "Fabrikam"
    }],
    "contentUrl": "https:\/\/www.fabrikam.com\/watch?v=MrVBWZpJjX",
    "hostPageUrl": "https:\/\/www.bing.com\/cr?IG=1CAE739681D8400DB...",
    "encodingFormat": "mp4",
    "hostPageDisplayUrl": "https:\/\/www.fabrikam.com\/watch?v=MrBWZpJjXo",
    "width": 1280,
    "height": 720,
    "duration": "PT3M47S",
    "motionThumbnailUrl": "https:\/\/tse2.mm.bing.net\/th?id=OM.oa...",
    "embedHtml": "<iframe width=\"1280\" height=\"720\" src=\"http:\/\/www....><\/iframe>",
    "allowHttpsEmbed": true,
    "viewCount": 19089,
    "thumbnail": {
        "width": 300,
        "height": 168
    },
    "allowMobileEmbed": true,
    "isSuperfresh": false
}, ...
```

Dependendo do dispositivo do utilizador, normalmente exibe um subconjunto dos vídeos com uma opção para o utilizador ver os restantes vídeos. Exibia uma miniatura do vídeo com o comprimento, descrição (nome) do vídeo.

<!-- Remove until this can be replaced with a sanitized version.
![List of video thumbnails](./media/cognitive-services-bing-web-api/bing-web-video-thumbnails.PNG)
-->

À medida que o utilizador paira sobre a miniatura, pode utilizar `motionThumbnailUrl` para reproduzir uma versão miniatura do vídeo. Certifique-se de que adiciona um atributo à miniatura de movimento quando a apresentar.

<!-- Remove until this can be replaced with a sanitized version.
![Motion thumbnail of a video](./media/cognitive-services-bing-web-api/bing-web-video-motion-thumbnail.PNG)
-->

Se o utilizador clicar na miniatura, seguem-se as opções de visualização do vídeo:

- Use `hostPageUrl` para ver o vídeo no site do anfitrião (por exemplo, YouTube)
- Use `webSearchUrl` para ver o vídeo no navegador de vídeo Bing
- Use `embedHtml` para incorporar o vídeo na sua própria experiência

Para mais detalhes sobre a resposta em vídeo e vídeos, consulte a [Video Search API](../bing-video-search/search-the-web.md).

## <a name="news-answer"></a>Resposta noticiosa

A resposta [noticiosa](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#news) contém uma lista de artigos noticiosos que Bing pensava serem relevantes para a consulta. Cada [artigo](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle) na lista inclui o nome, a descrição e o URL do artigo no site do anfitrião. Se o artigo contiver uma imagem, o objeto inclui uma miniatura da imagem.

```json
{
    "name": "WC Sailing Qualifies for America Trophy with...",
    "url": "http:\/\/www.bing.com\/cr?IG=3445EEF15DAF4FFFBF7...",
    "image": {
        "contentUrl": "http:\/\/www.contoso.com\/sports\/sail...",
        "thumbnail": {
            "contentUrl": "https:\/\/www.bing.com\/th?id=ON.1...",
            "width": 400,
            "height": 272
        }
    },
    "description": "The WC sailing team qualified for a...",
    "provider": [{
        "_type": "Organization",
        "name": "contoso.com"
    }],
    "datePublished": "2017-04-16T21:56:00"
}, ...
```

Dependendo do dispositivo do utilizador, apresentaria um subconjunto dos artigos noticiosos com uma opção para o utilizador visualizar os restantes artigos. Utilize `name` e `url` para criar uma hiperligação que direcione o utilizador para o artigo no site do anfitrião. Se o artigo incluir uma imagem, `url`faça a imagem clicável usando . Certifique-se de que utiliza `provider` para atribuir o artigo.

<!-- Remove until this can be replaced with a sanitized version.
The following shows an example of how you might display articles in a search results page.

![List of news articles](./media/cognitive-services-bing-web-api/bing-web-news-list.PNG)
-->

Para mais detalhes sobre a resposta noticiosa e artigos noticiosos, consulte a API de [Pesquisa de Notícias](../bing-news-search/search-the-web.md).

## <a name="computation-answer"></a>Resposta computação

Se o utilizador introduzir uma expressão matemática ou uma consulta de conversão de unidade, a resposta pode conter uma resposta [computation.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#computation) A `computation` resposta contém a expressão normalizada e o seu resultado.

Uma consulta de conversão de unidade é uma consulta que converte uma unidade para outra. Por exemplo, *quantos pés em 10 metros?* *How many tablespoons in a 1/4 cup?*

O seguinte `computation` mostra a resposta para *quantos pés em 10 metros?*

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
    "expression": "10 meters",
    "value": "32.808399 feet"
}, ...
```

O seguinte mostra exemplos de consultas `computation` matemáticas e suas respostas correspondentes.

```
Query: (5+3)(10/2)+8
Encoded query: %285%2B3%29%2810%2F2%29%2B8
```

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
    "expression": "((5+3)*(10\/2))+8",
    "value": "48"
}
```

```
Query: sqrt(4^2+8^2)
Encoded query: sqrt%284^2%2B8^2%29
```

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
    "expression": "sqrt((4^2)+(8^2))",
    "value": "8.94427191"
}
```

```
Query: 30 6/8 - 18 8/16
Encoded query: 30%206%2F8%20-%2018%208%2F16
```

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#WolframAlpha",
    "expression": "30 6\/8-18 8\/16",
    "value": "12.25"
}
```

```
Query: 8^2+11^2-2*8*11*cos(37)
Encoded query: 8^2%2B11^2-2*8*11*cos%2837%29
```

```json
"computation": {
        "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
        "expression": "(8^2)+(11^2)-(2*8*11*cos(37))",
        "value": "44.4401502"
}
```

Uma expressão matemática pode conter os seguintes símbolos:

|Símbolo|Descrição|
|------------|-----------------|
|+|Adição|
|-|Subtração|
|/|Divisão|
|*|Multiplicação|
|^|Power|
|!|Fatorial|
|.|Decimal|
|()|Agrupamento de precedência|
|[]|Função|

Uma expressão matemática pode conter as seguintes constantes:

|Símbolo|Descrição|
|------------|-----------------|
|Pi|3.14159...|
|Licenciatura|Licenciatura|
|i|Número imaginário|
|e|e, 2.71828...|
|GoldenRatio|Relação dourada, 1.61803...|

Uma expressão matemática pode conter as seguintes funções:

|Símbolo|Descrição|
|------------|-----------------|
|Ordenar|Raiz quadrada|
|Pecado[x], Cos[x], Tan[x]<br />Csc[x], Sec[x], Cot[x]|Funções trigonométricas (com argumentos em radianos)|
|ArcSin[x], ArcCos[x], ArcTan[x]<br />ArcCsc[x], ArcSec[x], ArcCot[x]|Funções trigonométricas inversas (dando resultados em radianos)|
|Exp[x], E^x|Função exponencial|
|Log[x]|Logarithm natural|
|Sinh[x], Cosh[x], Tanh[x]<br />Csch[x], Sech[x], Coth[x]|Funções hiperbólicas|
|ArcSinh[x], ArcCosh[x], ArcTanh[x]<br />ArcCsch[x], ArcSech[x], ArcCoth[x]|Funções hiperbólicas inversas|

Expressões matemáticas que contenham variáveis (por exemplo, 4x+6=18, onde x é a variável) não são suportadas.

## <a name="timezone-answer"></a>Resposta timeZone

Se o utilizador introduzir uma consulta de hora ou data, a resposta pode conter uma resposta [timeZone.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#timezone) Esta resposta suporta consultas implícitas ou explícitas. Uma consulta implícita, como *a que horas são?* Uma consulta explícita, como *a que horas é em Seattle?*

A `timeZone` resposta fornece o nome da localização, a data e hora utc atual no local especificado, e a utc compensada. Se o limite da localização estiver dentro de vários fusos horários, a resposta contém a data e hora utc atuais de todos os fusos horários dentro do limite. Por exemplo, como o Estado da Flórida se enquadra dentro de dois fusos horários, a resposta contém a data e hora locais de ambos os fusos horários.  

Se a consulta solicitar o tempo de um estado ou país/região, Bing determina a cidade primária `primaryCityTime` dentro da fronteira geográfica do local e devolve-a no campo. Se o limite contiver vários fusos horários, `otherCityTimes` os fusos horários restantes são devolvidos no campo.

O seguinte mostra perguntas exemplo `timeZone` que devolvem a resposta.

```
Query: What time is it?

"timeZone": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
    "primaryCityTime": {
        "location": "Redmond, Washington, United States",
        "time": "2015-10-27T08:38:12.1189231Z",
        "utcOffset": "UTC-7"
    }
}

Query: What time is it in the Pacific time zone?

"timeZone": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
    "primaryCityTime": {
        "location": "Pacific Time Zone",
        "time": "2015-10-23T12:33:19.0728146Z",
        "utcOffset": "UTC-7"
    }
}

Query: Time in Florida?

"timeZone": {
        "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
        "primaryCityTime": {
            "location": "Tallahassee, Florida, United States",
            "time": "2015-10-23T13:04:56.6774389Z",
            "utcOffset": "UTC-4"
        },
        "otherCityTimes": [{
            "location": "Pensacola",
            "time": "2015-10-23T12:04:56.6664294Z",
            "utcOffset": "UTC-5"
        }]
}

Query: What time is it in the U.S.

"timeZone": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
    "primaryCityTime": {
        "location": "Washington, D.C., United States",
        "time": "2015-10-23T15:27:59.8892745Z",
        "utcOffset": "UTC-4"
    },
    "otherCityTimes": [{
        "location": "Honolulu",
        "time": "2015-10-23T09:27:59.8892745Z",
        "utcOffset": "UTC-10"
    },
    {
        "location": "Anchorage",
        "time": "2015-10-23T11:27:59.8892745Z",
        "utcOffset": "UTC-8"
    },
    {
        "location": "Phoenix",
        "time": "2015-10-23T12:27:59.8892745Z",
        "utcOffset": "UTC-7"
    },
    {
        "location": "Los Angeles",
        "time": "2015-10-23T12:27:59.8942788Z",
        "utcOffset": "UTC-7"
    },
    {
        "location": "Denver",
        "time": "2015-10-23T13:27:59.8812681Z",
        "utcOffset": "UTC-6"
    },
    {
        "location": "Chicago",
        "time": "2015-10-23T14:27:59.8892745Z",
        "utcOffset": "UTC-5"
    }]
}
```

## <a name="spellsuggestion-answer"></a>Resposta SpellSuggestion

Se bing determinar que o utilizador pode ter a intenção de procurar algo diferente, a resposta inclui um objeto [SpellSuggestions.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#spellsuggestions) Por exemplo, se o utilizador pesquisar por *Carlos Pen,* bing pode determinar que o utilizador provavelmente pretendia procurar Carlos Pena (com base em pesquisas passadas por outros de *Carlos Pen).* O seguinte mostra uma resposta ortográfica exemplo.

```json
"spellSuggestions": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#SpellSuggestions",
    "value": [{
        "text": "carlos pena",
        "displayText": "carlos pena"
    }]
}, ...
```

## <a name="response-headers"></a>Cabeçalhos de resposta

As respostas da API de pesquisa web bing podem conter os seguintes cabeçalhos:

|||
|-|-|
|`X-MSEdge-ClientID`|O ID único que Bing atribuiu ao utilizador|
|`BingAPIs-Market`|O mercado que foi usado para cumprir o pedido|
|`BingAPIs-TraceId`|A entrada de log no servidor Bing API para este pedido (para suporte)|

É particularmente importante persistir na identificação do cliente e devolvê-la com pedidos subsequentes. Quando o fizer, a pesquisa utilizará o contexto passado nos resultados de pesquisa de ranking e também proporcionará uma experiência consistente do utilizador.

No entanto, quando liga para a API de Pesquisa Web Bing do JavaScript, as funcionalidades de segurança incorporadas do seu navegador (CORS) podem impedir que aceda aos valores destes cabeçalhos.

Para ter acesso aos cabeçalhos, pode fazer o pedido de API de Pesquisa Web Bing através de um proxy CORS. A resposta de um proxy deste tipo inclui um cabeçalho `Access-Control-Expose-Headers`, que adiciona os cabeçalhos das respostas à lista de permissões e os disponibiliza para o JavaScript.

É fácil instalar um proxy CORS para permitir que a nossa [aplicação tutorial](tutorial-bing-web-search-single-page-app.md) aceda aos cabeçalhos opcionais do cliente. Em primeiro lugar, se ainda não o tiver, [instale Node.js](https://nodejs.org/en/download/). Em seguida, introduza o seguinte comando num pedido de comando.

    npm install -g cors-proxy-server

Em seguida, altere o ponto final da Pesquisa Web Bing no ficheiro HTML para:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

Por fim, inicie o proxy do CORS com o comando seguinte:

    cors-proxy-server

Deixe a janela de comando aberta enquanto utiliza a aplicação de tutorial. Se a janela for fechada, o proxy é interrompido. Na secção Cabeçalhos HTTP expansíveis, abaixo dos resultados da pesquisa, pode agora ver o cabeçalho `X-MSEdge-ClientID` (entre outros) e confirmar se é o mesmo em todos os pedidos.

## <a name="response-headers-in-production"></a>Cabeçalhos de resposta na produção

A abordagem de procuração cors descrita na resposta anterior é adequada para o desenvolvimento, teste e aprendizagem.

Num ambiente de produção, deve hospedar um script do lado do servidor no mesmo domínio que a página Web que utiliza a API de Pesquisa Web bing. Este script deve fazer chamadas aPi a pedido da página Web JavaScript e passar todos os resultados, incluindo cabeçalhos, de volta ao cliente. Uma vez que os dois recursos (página e script) partilham uma origem, o CORS não é utilizado e os cabeçalhos especiais são acessíveis ao JavaScript na página Web.

Esta abordagem também protege a sua chave API da exposição ao público, uma vez que apenas o script do lado do servidor precisa dela. O script pode usar outro método para garantir que o pedido é autorizado.

O seguinte mostra como Bing usa a sugestão de ortografia.

![Exemplo de sugestão de ortografia bing](./media/cognitive-services-bing-web-api/bing-web-spellingsuggestion.GIF)  

## <a name="next-steps"></a>Passos seguintes  

* [Reveja](throttling-requests.md) a documentação do pedido de revisão.  

## <a name="see-also"></a>Consulte também  

* [Referência a API de pesquisa web bing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)
