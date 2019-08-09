---
title: API de Pesquisa na Web do Bing a estrutura de resposta e os tipos de resposta
titleSuffix: Azure Cognitive Services
description: Saiba mais sobre os tipos de resposta e as respostas usadas pelo API de Pesquisa na Web do Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: f19454868ad7be21777d725f61e09a84f6c7a313
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854732"
---
# <a name="bing-web-search-api-response-structure-and-answer-types"></a>API de Pesquisa na Web do Bing a estrutura de resposta e os tipos de resposta  

Quando você envia pesquisa na Web do Bing uma solicitação de pesquisa, ela retorna [`SearchResponse`](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#searchresponse) um objeto no corpo da resposta. O objeto inclui um campo para cada resposta que o Bing determinado foi relevante para a consulta. Este exemplo ilustra um objeto de resposta se o Bing retornou todas as respostas:

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

Normalmente, Pesquisa na Web do Bing retorna um subconjunto das respostas. Por exemplo, se o termo de consulta era *velejando Dinghies*, a resposta pode `webPages`incluir `images`, e `rankingResponse`. A menos que você tenha usado o [responseFilter](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#responsefilter) para filtrar páginas da Web, a resposta sempre `webpages` inclui `rankingResponse` as respostas e.

[!INCLUDE [cognitive-services-bing-url-note](../../../includes/cognitive-services-bing-url-note.md)]

## <a name="webpages-answer"></a>Resposta de páginas da Web

A resposta das [páginas da Web](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webanswer) contém uma lista de links para páginas da web que pesquisa na Web do Bing determinados eram relevantes para a consulta. Cada [página da Web](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webpage) na lista incluirá: o nome da página, a URL, a URL de exibição, uma breve descrição do conteúdo e a data em que o Bing encontrou o conteúdo.

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

Use `name` e`url` para criar um hiperlink que leva o usuário para a página da Web.

<!-- Remove until this can be replaced with a sanitized version.
The following shows an example of how you might display the webpage in a search results page.

![Rendered webpage example](./media/cognitive-services-bing-web-api/bing-rendered-webpage-example.PNG)
-->

## <a name="images-answer"></a>Resposta de imagens

A resposta de [imagens](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) contém uma lista de imagens que o Bing pensou que foram relevantes para a consulta. Cada [imagem](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) na lista inclui a URL da imagem, seu tamanho, suas dimensões e seu formato de codificação. O objeto de imagem também inclui o URL de uma miniatura da imagem e as dimensões da miniatura.

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

Dependendo do dispositivo do usuário, você normalmente exibiria um subconjunto das miniaturas, com uma opção para o usuário paginar [](paging-webpages.md) as imagens restantes.

<!-- Remove until this can be replaced with a sanitized version.
![List of thumbnail images](./media/cognitive-services-bing-web-api/bing-web-image-thumbnails.PNG)
-->

Também pode expandir a miniatura à medida que o utilizador paira o cursor sobre a mesma. Lembre-se de atribuir a imagem se a expandir. Por exemplo, extraindo o host de `hostPageDisplayUrl` e exibindo-o abaixo da imagem. Para obter informações sobre o redimensionamento da miniatura, veja [Redimensionar e Recortar Miniaturas](./resize-and-crop-thumbnails.md).

<!-- Remove until this can be replaced with a sanitized version.
![Expanded view of thumbnail image](./media/cognitive-services-bing-web-api/bing-web-image-thumbnail-expansion.PNG)
-->

Se o usuário clicar na miniatura, use `webSearchUrl` para levar o usuário para a página de resultados da pesquisa do Bing para imagens, que contém uma colagem das imagens.

Para obter detalhes sobre a resposta da imagem e as imagens, consulte [pesquisa de imagem API](../bing-image-search/search-the-web.md).

## <a name="related-searches-answer"></a>Resposta de pesquisas relacionadas

A resposta [relatedSearches](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#searchresponse-relatedsearches) contém uma lista das consultas relacionadas mais populares feitas por outros usuários. Cada [consulta](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query_obj) na lista inclui uma cadeia de caracteres de`text`consulta (), uma cadeia de caracteres de consulta com`displayText`realce de caracteres ()`webSearchUrl`e uma URL () para a página de resultados da pesquisa do Bing para essa consulta.

```json
{
    "text": "dinghy racing teams",
    "displayText": "dinghy racing teams",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=96C4CF214A0..."
}, ...
```

Use a `displayText` cadeia de caracteres de `webSearchUrl` consulta e a URL para criar um hiperlink que leva o usuário para a página de resultados da pesquisa do Bing para a consulta relacionada. Você também pode usar a `text` cadeia de caracteres de consulta em sua própria consulta de API pesquisa na Web e exibir os resultados por conta própria.

Para obter informações sobre como lidar com os marcadores de realce `displayText`no, consulte realce de [cliques](../bing-web-search/hit-highlighting.md).

Veja a seguir um exemplo do uso de consultas relacionadas em Bing.com.

![Exemplo de pesquisas relacionadas no Bing](./media/cognitive-services-bing-web-api/bing-web-rendered-relatedsearches.GIF)

## <a name="videos-answer"></a>Resposta de vídeos

A resposta dos [vídeos](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) contém uma lista de vídeos que o Bing pensou que foram relevantes para a consulta. Cada [vídeo](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video) na lista inclui a URL do vídeo, sua duração, suas dimensões e seu formato de codificação. O objeto de vídeo também inclui o URL de uma miniatura do vídeo e as dimensões da miniatura.

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

Dependendo do dispositivo do usuário, você normalmente exibiria um subconjunto dos vídeos com uma opção para que o usuário exiba os vídeos restantes. Você exibiria uma miniatura do vídeo com o comprimento, a descrição (nome) e a atribuição (editor) do vídeo.

<!-- Remove until this can be replaced with a sanitized version.
![List of video thumbnails](./media/cognitive-services-bing-web-api/bing-web-video-thumbnails.PNG)
-->

À medida que o usuário passa o mouse sobre a miniatura `motionThumbnailUrl` , você pode usar para reproduzir uma versão em miniatura do vídeo. Certifique-se de que adiciona um atributo à miniatura de movimento quando a apresentar.

<!-- Remove until this can be replaced with a sanitized version.
![Motion thumbnail of a video](./media/cognitive-services-bing-web-api/bing-web-video-motion-thumbnail.PNG)
-->

Se o utilizador clicar na miniatura, seguem-se as opções de visualização do vídeo:

- Use `hostPageUrl` para exibir o vídeo no site do host (por exemplo, YouTube)
- Use `webSearchUrl` para exibir o vídeo no navegador de vídeo do Bing
- Use `embedHtml` para inserir o vídeo em sua própria experiência

Para obter detalhes sobre a resposta de vídeo e vídeos, consulte [pesquisa de vídeo API](../bing-video-search/search-the-web.md).

## <a name="news-answer"></a>Resposta de notícias

A resposta de [notícias](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#news) contém uma lista de artigos de notícias que o Bing pensou que foram relevantes para a consulta. Cada [artigo](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle) na lista inclui o nome, a descrição e o URL do artigo no site do anfitrião. Se o artigo contiver uma imagem, o objeto inclui uma miniatura da imagem.

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

Dependendo do dispositivo do usuário, você exibiria um subconjunto dos artigos de notícias com uma opção para que o usuário exiba os artigos restantes. Utilize `name` e `url` para criar uma hiperligação que direcione o utilizador para o artigo no site do anfitrião. Se o artigo incluir uma imagem, torne a imagem clicável usando `url`. Certifique-se de que utiliza `provider` para atribuir o artigo.

<!-- Remove until this can be replaced with a sanitized version.
The following shows an example of how you might display articles in a search results page.

![List of news articles](./media/cognitive-services-bing-web-api/bing-web-news-list.PNG)
-->

Para obter detalhes sobre a resposta de notícias e artigos de notícias, consulte [pesquisa de notícias API](../bing-news-search/search-the-web.md).

## <a name="computation-answer"></a>Resposta de computação

Se o usuário inserir uma expressão matemática ou uma consulta de conversão de unidade, a resposta poderá conter uma resposta de [computação](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#computation) . A `computation` resposta contém a expressão normalizada e seu resultado.

Uma consulta de conversão de unidade é uma consulta que converte uma unidade em outra. Por exemplo, *Quantos pés em 10 metros?* ou quantos *tablespoons em uma xícara de 1/4?*

O seguinte mostra a `computation` resposta de *Quantos pés em 10 metros?*

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
    "expression": "10 meters",
    "value": "32.808399 feet"
}, ...
```

O exemplo a seguir mostra exemplos de consultas matemáticas `computation` e suas respostas correspondentes.

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
|^|Alimentar|
|!|Fatorial|
|.|Decimal|
|()|Agrupamento de precedência|
|[]|Função|

Uma expressão matemática pode conter as seguintes constantes:

|Símbolo|Descrição|
|------------|-----------------|
|13,PI|3,14159...|
|Determinado|Determinado|
|Eu|Número imaginário|
|Oriental|e, 2,71828...|
|GoldenRatio|Taxa de ouro, 1,61803...|

Uma expressão matemática pode conter as seguintes funções:

|Símbolo|Descrição|
|------------|-----------------|
|Ordenar|Raiz quadrada|
|Sin[x], Cos[x], Tan[x]<br />Csc[x], Sec[x], Cot[x]|Funções trigonométricas (com argumentos em radianos)|
|ArcSin[x], ArcCos[x], ArcTan[x]<br />ArcCsc[x], ArcSec[x], ArcCot[x]|Funções trigonométricas inversas (fornecendo resultados em radianos)|
|Exp[x], E^x|Função exponencial|
|Log [x]|Logaritmo natural|
|Sinh[x], Cosh[x], Tanh[x]<br />Csch [x], sech [x], coth [x]|Funções hiperbólicas|
|ArcSinh[x], ArcCosh[x], ArcTanh[x]<br />ArcCsch [x], ArcSech [x], ArcCoth [x]|Funções hiperbólicas inversas|

Expressões matemáticas que contêm variáveis (por exemplo, 4x + 6 = 18, em que x é a variável) não têm suporte.

## <a name="timezone-answer"></a>Resposta de fuso horário

Se o usuário inserir uma consulta de data/hora, a resposta poderá conter uma resposta de [fuso horário](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#timezone) . Essa resposta dá suporte a consultas implícitas ou explícitas. Uma consulta implícita, como *qual é o tempo?* , retorna a hora local com base no local do usuário. Uma consulta explícita, como *qual é o tempo em Seattle?* , retorna a hora local de Seattle, WA.

A `timeZone` resposta fornece o nome do local, a data e hora UTC atuais no local especificado e o deslocamento UTC. Se o limite do local estiver dentro de vários fusos horários, a resposta conterá a data e a hora UTC atuais de todos os fusos horários dentro do limite. Por exemplo, como o estado da Flórida cai dentro de dois fusos horários, a resposta contém a data e a hora locais de ambos os fusos horários.  

Se a consulta solicitar a hora de um Estado ou país/região, o Bing determinará a cidade principal dentro do limite geográfico do local e a `primaryCityTime` retornará no campo. Se o limite contiver vários fusos horários, os fusos horários restantes serão `otherCityTimes` retornados no campo.

Veja a seguir as consultas de exemplo que `timeZone` retornam a resposta.

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

## <a name="spellsuggestion-answer"></a>SpellSuggestion resposta

Se o Bing determinar que o usuário pode ter a intenção de pesquisar algo diferente, a resposta incluirá um objeto [SpellSuggestions](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#spellsuggestions) . Por exemplo, se o usuário procurar por uma *caneta Carlos*, o Bing poderá determinar que o usuário provavelmente pretendia procurar Carlos pena (com base nas pesquisas passadas por outras pessoas da *caneta Carlos*). O exemplo a seguir mostra uma resposta de verificação ortográfica.

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

As respostas da API de Pesquisa na Web do Bing podem conter os seguintes cabeçalhos:

|||
|-|-|
|`X-MSEdge-ClientID`|A ID exclusiva que o Bing atribuiu ao usuário|
|`BingAPIs-Market`|O mercado que foi usado para atender à solicitação|
|`BingAPIs-TraceId`|A entrada de log no servidor de API do Bing para esta solicitação (para suporte)|

É particularmente importante manter a ID do cliente e retorná-la com solicitações subsequentes. Quando você fizer isso, a pesquisa usará o contexto anterior na classificação dos resultados da pesquisa e também fornecerá uma experiência de usuário consistente.

No entanto, quando você chama o API de Pesquisa na Web do Bing do JavaScript, os recursos de segurança internos (CORS) do seu navegador podem impedi-lo de acessar os valores desses cabeçalhos.

Para obter acesso aos cabeçalhos, você pode fazer a solicitação de API de Pesquisa na Web do Bing por meio de um proxy CORS. A resposta de um proxy deste tipo inclui um cabeçalho `Access-Control-Expose-Headers`, que adiciona os cabeçalhos das respostas à lista de permissões e os disponibiliza para o JavaScript.

É fácil instalar um proxy CORS para permitir que nosso [aplicativo tutorial](tutorial-bing-web-search-single-page-app.md) acesse os cabeçalhos de cliente opcionais. Em primeiro lugar, se ainda não o tiver, [instale Node.js](https://nodejs.org/en/download/). Em seguida, digite o comando a seguir em um prompt de comando.

    npm install -g cors-proxy-server

Em seguida, altere o ponto de extremidade API de Pesquisa na Web do Bing no arquivo HTML para:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

Por fim, inicie o proxy do CORS com o comando seguinte:

    cors-proxy-server

Deixe a janela de comando aberta enquanto utiliza a aplicação de tutorial. Se a janela for fechada, o proxy é interrompido. Na secção Cabeçalhos HTTP expansíveis, abaixo dos resultados da pesquisa, pode agora ver o cabeçalho `X-MSEdge-ClientID` (entre outros) e confirmar se é o mesmo em todos os pedidos.

## <a name="response-headers-in-production"></a>Cabeçalhos de resposta em produção

A abordagem de proxy CORS descrita na resposta anterior é apropriada para desenvolvimento, teste e aprendizado.

Em um ambiente de produção, você deve hospedar um script do lado do servidor no mesmo domínio que a página da Web que usa o API de Pesquisa na Web do Bing. Esse script deve fazer chamadas à API mediante solicitação do JavaScript da página da Web e passar todos os resultados, incluindo os cabeçalhos, de volta ao cliente. Como os dois recursos (página e script) compartilham uma origem, o CORS não é usado e os cabeçalhos especiais são acessíveis para o JavaScript na página da Web.

Essa abordagem também protege sua chave de API contra exposição ao público, pois apenas o script do lado do servidor precisa dela. O script pode usar outro método para verificar se a solicitação está autorizada.

O seguinte mostra como o Bing usa a sugestão de ortografia.

![Exemplo de sugestão de ortografia do Bing](./media/cognitive-services-bing-web-api/bing-web-spellingsuggestion.GIF)  

## <a name="next-steps"></a>Passos Seguintes  

* Revisar documentação de [limitação de solicitação](throttling-requests.md) .  

## <a name="see-also"></a>Consulte também  

* [Referência de API de Pesquisa na Web do Bing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)
