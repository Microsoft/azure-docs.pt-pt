---
title: Bing Web Search API estrutura de resposta e tipos de resposta
titleSuffix: Azure Cognitive Services
description: Quando envia bing Web Procurar um pedido de pesquisa, ele devolve um `SearchResponse` objeto no corpo de resposta.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 3dda95312a0b9191ddc11de62959f308ee19fff4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "94380985"
---
# <a name="bing-web-search-api-response-structure-and-answer-types"></a>Bing Web Search API estrutura de resposta e tipos de resposta  

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)

Quando envia bing Web Procurar um pedido de pesquisa, ele devolve um [`SearchResponse`](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#searchresponse) objeto no corpo de resposta. O objeto inclui um campo para cada resposta que Bing determinou ser relevante para a consulta. Este exemplo ilustra um objeto de resposta se Bing devolveu todas as respostas:

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

Normalmente, bing Web Search devolve um subconjunto das respostas. Por exemplo, se o termo de consulta fosse *"vela",* a resposta pode `webPages` `images` incluir, e `rankingResponse` . A menos que tenha usado [o Filtro](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#responsefilter) de Resposta para filtrar as páginas web, a resposta inclui sempre as `webpages` `rankingResponse` respostas e respostas.

[!INCLUDE [cognitive-services-bing-url-note](../../../includes/cognitive-services-bing-url-note.md)]

## <a name="webpages-answer"></a>Resposta de páginas web

A resposta [webPages](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webanswer) contém uma lista de links para páginas web que bing Web Search determinou serem relevantes para a consulta. Cada [página web](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webpage) da lista incluirá: nome da página, url, URL de exibição, uma breve descrição do conteúdo e a data em que Bing encontrou o conteúdo.

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

Utilize `name` e crie uma `url` hiperligação que leve o utilizador à página web.

<!-- Remove until this can be replaced with a sanitized version.
The following shows an example of how you might display the webpage in a search results page.

![Rendered webpage example](./media/cognitive-services-bing-web-api/bing-rendered-webpage-example.PNG)
-->

## <a name="images-answer"></a>Resposta de imagens

A resposta [das imagens](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) contém uma lista de imagens que Bing pensou serem relevantes para a consulta. Cada [imagem](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) da lista inclui o URL da imagem, o seu tamanho, as suas dimensões e o seu formato de codificação. O objeto de imagem também inclui o URL de uma miniatura da imagem e as dimensões da miniatura.

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

Dependendo do dispositivo do utilizador, normalmente exibe um subconjunto das miniaturas, com uma opção para o utilizador [páginar através](./paging-search-results.md) das restantes imagens.

<!-- Remove until this can be replaced with a sanitized version.
![List of thumbnail images](./media/cognitive-services-bing-web-api/bing-web-image-thumbnails.PNG)
-->

Também pode expandir a miniatura à medida que o utilizador paira o cursor sobre a mesma. Lembre-se de atribuir a imagem se a expandir. Por exemplo, extraindo o hospedeiro `hostPageDisplayUrl` e exibindo-o abaixo da imagem. Para obter informações sobre o redimensionamento da miniatura, veja [Redimensionar e Recortar Miniaturas](./resize-and-crop-thumbnails.md).

<!-- Remove until this can be replaced with a sanitized version.
![Expanded view of thumbnail image](./media/cognitive-services-bing-web-api/bing-web-image-thumbnail-expansion.PNG)
-->

Se o utilizador clicar na miniatura, utilize `webSearchUrl` para levar o utilizador à página de resultados de pesquisa de Bing para imagens, que contém uma colagem das imagens.

Para mais detalhes sobre a resposta à imagem e imagens, consulte [a API de Pesquisa de Imagem](../bing-image-search/overview.md).

## <a name="related-searches-answer"></a>Resposta de pesquisas relacionadas

A resposta [relacionadaSequistão](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#searchresponse-relatedsearches) contém uma lista das consultas relacionadas mais populares feitas por outros utilizadores. Cada [consulta](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query_obj) na lista inclui uma cadeia de consulta `text` (), uma cadeia de consulta com caracteres de destaque de sucesso `displayText` (), e um URL `webSearchUrl` () para a página de resultados de pesquisa de Bing para essa consulta.

```json
{
    "text": "dinghy racing teams",
    "displayText": "dinghy racing teams",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=96C4CF214A0..."
}, ...
```

Utilize a `displayText` cadeia de consulta e o URL para criar uma `webSearchUrl` hiperligação que leva o utilizador à página de resultados de pesquisa de Bing para a consulta relacionada. Também pode utilizar a `text` cadeia de consulta na sua própria consulta de API de pesquisa web e exibir os resultados por si mesmo.

Para obter informações sobre como lidar com os marcadores de destaque em `displayText` , consulte [Hit Highlighting](../bing-web-search/hit-highlighting.md).

O seguinte mostra um exemplo do uso de consultas relacionadas em Bing.com.

![Exemplo de pesquisas relacionadas em Bing](./media/cognitive-services-bing-web-api/bing-web-rendered-relatedsearches.GIF)

## <a name="videos-answer"></a>Resposta dos vídeos

A resposta [dos vídeos](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) contém uma lista de vídeos que Bing pensou serem relevantes para a consulta. Cada [vídeo](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video) da lista inclui o URL do vídeo, a sua duração, as suas dimensões e o seu formato de codificação. O objeto de vídeo também inclui o URL de uma miniatura do vídeo e as dimensões da miniatura.

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

Dependendo do dispositivo do utilizador, normalmente exibia um subconjunto dos vídeos com a opção de visualizar os restantes vídeos. Exibia uma miniatura do vídeo com o comprimento, descrição (nome) e atribuição do vídeo (editora).

<!-- Remove until this can be replaced with a sanitized version.
![List of video thumbnails](./media/cognitive-services-bing-web-api/bing-web-video-thumbnails.PNG)
-->

À medida que o utilizador paira sobre a miniatura, pode usar `motionThumbnailUrl` para reproduzir uma versão miniatura do vídeo. Certifique-se de que adiciona um atributo à miniatura de movimento quando a apresentar.

<!-- Remove until this can be replaced with a sanitized version.
![Motion thumbnail of a video](./media/cognitive-services-bing-web-api/bing-web-video-motion-thumbnail.PNG)
-->

Se o utilizador clicar na miniatura, seguem-se as opções de visualização do vídeo:

- Use `hostPageUrl` para ver o vídeo no site do anfitrião (por exemplo, YouTube)
- Use `webSearchUrl` para ver o vídeo no navegador de vídeo Bing
- Use `embedHtml` para incorporar o vídeo na sua própria experiência

Para mais detalhes sobre a resposta ao vídeo e vídeos, consulte [a API de Pesquisa de Vídeo](../bing-video-search/overview.md).

## <a name="news-answer"></a>Resposta noticiosa

A [resposta noticiosa](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#news) contém uma lista de artigos noticiosos que Bing pensou serem relevantes para a consulta. Cada [artigo](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle) na lista inclui o nome, a descrição e o URL do artigo no site do anfitrião. Se o artigo contiver uma imagem, o objeto inclui uma miniatura da imagem.

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

Dependendo do dispositivo do utilizador, apresentaria um subconjunto dos artigos noticiosos com a opção de o utilizador visualizar os restantes artigos. Utilize `name` e `url` para criar uma hiperligação que direcione o utilizador para o artigo no site do anfitrião. Se o artigo incluir uma imagem, faça a imagem clicável usando `url` . Certifique-se de que utiliza `provider` para atribuir o artigo.

<!-- Remove until this can be replaced with a sanitized version.
The following shows an example of how you might display articles in a search results page.

![List of news articles](./media/cognitive-services-bing-web-api/bing-web-news-list.PNG)
-->

Para mais informações sobre a resposta noticiosa e artigos noticiosos, consulte [a API de Pesquisa de Notícias.](../bing-news-search/search-the-web.md)

## <a name="computation-answer"></a>Resposta computacional

Se o utilizador introduzir uma expressão matemática ou uma consulta de conversão de unidade, a resposta pode conter uma resposta [computacional.](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#computation) A `computation` resposta contém a expressão normalizada e o seu resultado.

Uma consulta de conversão de unidade é uma consulta que converte uma unidade para outra. Por exemplo, *quantos metros em 10 metros ou* *quantas colheres de sopa num copo de 1/4?*

O seguinte mostra a `computation` resposta para *quantos pés em 10 metros?*

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
    "expression": "10 meters",
    "value": "32.808399 feet"
}, ...
```

O que se segue mostra exemplos de consultas matemáticas e as suas `computation` respostas correspondentes.

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
|^|Energia|
|!|Fatorial|
|.|Decimal|
|()|Agrupamento de precedência|
|[]|Função|

Uma expressão matemática pode conter as seguintes constantes:

|Símbolo|Description|
|------------|-----------------|
|Pi|3.14159...|
|Grau|Grau|
|i|Número imaginário|
|e|e, 2.71828...|
|GoldenRatio|Relação dourada, 1.61803...|

Uma expressão matemática pode conter as seguintes funções:

|Símbolo|Description|
|------------|-----------------|
|Ordenar|Raiz quadrada|
|Pecado[x], Cos[x], Tan[x]<br />CSC[x], Sec[x], Berço[x]|Funções trigonométricas (com argumentos em radians)|
|ArcSin[x], ArcCos[x], ArcTan[x]<br />ArcCsc[x], ArcSec[x], ArcCot[x]|Funções trigonométricas inversas (dando resultados em radians)|
|Exp[x], E^x|Função exponencial|
|Log[x]|Logaritmos natural|
|Sinh[x], Cosh[x], Tanh[x]<br />Csch[x], Sech[x], Coth[x]|Funções hiperbólicas|
|ArcSinh[x], ArcCosh[x], ArcTanh[x]<br />ArcCsch[x], ArcSech[x], ArcCoth[x]|Funções hiperbólicas inversas|

Expressões matemáticas que contenham variáveis (por exemplo, 4x+6=18, onde x é a variável) não são suportadas.

## <a name="timezone-answer"></a>Resposta timeZone

Se o utilizador introduzir uma consulta de hora ou data, a resposta pode conter uma resposta [TimeZone.](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#timezone) Esta resposta suporta consultas implícitas ou explícitas. Uma consulta implícita, como *a hora?* Uma consulta explícita, como *a que horas é em Seattle?*

A `timeZone` resposta fornece o nome da localização, a data e hora utc atuais no local especificado, e a compensação UTC. Se o limite da localização estiver dentro de vários fusos horários, a resposta contém a data e hora atuais utc de todos os fusos horários dentro do limite. Por exemplo, como o Estado da Florida se enquadra em dois fusos horários, a resposta contém a data e a hora locais de ambos os fusos horários.  

Se a consulta solicitar a hora de um estado ou país/região, Bing determina a cidade primária dentro do limite geográfico do local e devolve-a no `primaryCityTime` campo. Se o limite contiver vários fusos horários, os fusos horários restantes são devolvidos no `otherCityTimes` campo.

O seguinte mostra exemplo de consultas que devolvem a `timeZone` resposta.

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

Se Bing determinar que o utilizador pode ter pretendido procurar algo diferente, a resposta inclui um objeto [SpellSuggestions.](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#spellsuggestions) Por exemplo, se o utilizador pesquisar por *Carlos Pen,* Bing poderá determinar que o utilizador provavelmente pretendia procurar Por Carlos Pena (com base em pesquisas passadas de outros de *Carlos Pen).* O seguinte mostra uma resposta ortográfica exemplo.

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

As respostas da API de Pesquisa Web de Bing podem conter os seguintes cabeçalhos:

| Cabeçalho | Description |
|-|-|
|`X-MSEdge-ClientID`|O ID único que Bing atribuiu ao utilizador|
|`BingAPIs-Market`|O mercado que foi usado para satisfazer o pedido|
|`BingAPIs-TraceId`|A entrada de registo no servidor Bing API para este pedido (para suporte)|

É particularmente importante persistir na identificação do cliente e devolvê-la com pedidos subsequentes. Ao fazê-lo, a pesquisa utilizará o contexto passado nos resultados de pesquisa do ranking e também proporcionará uma experiência consistente do utilizador.

No entanto, quando liga para a API de Pesquisa Web Bing do JavaScript, as funcionalidades de segurança incorporadas do seu navegador (CORS) podem impedir-lhe de aceder aos valores destes cabeçalhos.

Para obter acesso aos cabeçalhos, pode fazer o pedido de API de Pesquisa Web Bing através de um representante do CORS. A resposta de tal proxy tem um `Access-Control-Expose-Headers` cabeçalho que filtra cabeçalhos de resposta e os coloca disponíveis para JavaScript.

É fácil instalar um proxy CORS para permitir que a nossa [aplicação tutorial](tutorial-bing-web-search-single-page-app.md) aceda aos cabeçalhos opcionais do cliente. Em primeiro lugar, se ainda não o tiver, [instale Node.js](https://nodejs.org/en/download/). Em seguida, insira o seguinte comando num pedido de comando.

```console
npm install -g cors-proxy-server
```

Em seguida, altere o ponto final de API de pesquisa web Bing no ficheiro HTML para:\
`http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search`

Por fim, inicie o proxy do CORS com o comando seguinte:

```console
cors-proxy-server
```

Deixe a janela de comando aberta enquanto utiliza a aplicação de tutorial. Se a janela for fechada, o proxy é interrompido. Na secção Cabeçalhos HTTP expansíveis, abaixo dos resultados da pesquisa, pode agora ver o cabeçalho `X-MSEdge-ClientID` (entre outros) e confirmar se é o mesmo em todos os pedidos.

## <a name="response-headers-in-production"></a>Cabeçalhos de resposta na produção

A abordagem de procuração CORS descrita na resposta anterior é adequada para o desenvolvimento, teste e aprendizagem.

Num ambiente de produção, deve hospedar um script do lado do servidor no mesmo domínio que a página Web que utiliza a API de Pesquisa web Bing. Este script deve fazer chamadas API mediante pedido da página Web JavaScript e passar todos os resultados, incluindo cabeçalhos, de volta para o cliente. Uma vez que os dois recursos (página e script) partilham uma origem, o CORS não é utilizado e os cabeçalhos especiais estão acessíveis ao JavaScript na página Web.

Esta abordagem também protege a sua chave API da exposição ao público, uma vez que apenas o script do lado do servidor precisa dela. O script pode usar outro método para se certificar de que o pedido é autorizado.

O seguinte mostra como Bing usa a sugestão ortográfica.

![Bing exemplo de sugestão ortográfica](./media/cognitive-services-bing-web-api/bing-web-spellingsuggestion.GIF)  

## <a name="next-steps"></a>Passos seguintes  

* Reveja a documentação de estrangulamento do pedido de [revisão.](throttling-requests.md)  

## <a name="see-also"></a>Ver também  

* [Bing Web Search API referência](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)