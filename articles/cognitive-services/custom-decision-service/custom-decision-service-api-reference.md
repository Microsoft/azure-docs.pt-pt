---
title: Referência de API-Serviço de Decisão Personalizada
titlesuffix: Azure Cognitive Services
description: Um guia de API completo para Serviço de Decisão Personalizada.
services: cognitive-services
author: slivkins
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: slivkins
ROBOTS: NOINDEX
ms.openlocfilehash: 4f263e3b57103174f0084ab3d25430d8c47359fd
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707302"
---
# <a name="api"></a>API

O Azure Serviço de Decisão Personalizada fornece duas APIs que são chamadas para cada decisão: a [API de classificação](#ranking-api) para inserir a classificação de ações e a API de [recompensa](#reward-api) para gerar a recompensa. Além disso, você fornece uma [API de conjunto de ações](#action-set-api-customer-provided) para especificar as ações para o Azure serviço de decisão personalizada. Este artigo aborda essas três APIs. Um cenário típico é usado abaixo para mostrar quando Serviço de Decisão Personalizada otimiza a classificação de artigos.

## <a name="ranking-api"></a>API de classificação

A API de classificação usa um padrão de comunicação de estilo [JSONP](https://en.wikipedia.org/wiki/JSONP)padrão para otimizar a latência e ignorar a [política de mesma origem](https://en.wikipedia.org/wiki/Same-origin_policy). O último proíbe que o JavaScript busque dados de fora da origem da página.

Insira este trecho de código no cabeçalho HTML da sua página inicial (onde uma lista personalizada de artigos é exibida):

```html
// define the "callback function" to render UI
<script> function callback(data) { … } </script>
// "data" provides the ranking of URLs, see example below.

// call to Ranking API
<script src="https://ds.microsoft.com/api/v2/<appId>/rank/<actionSetId>?<parameters>" async></script>
// action set id is the name of the corresponding RSS/Atom feed.

// same call with multiple action sets:
// <script src="https://ds.microsoft.com/api/v2/<appId>/rank/<A1>/<A2>/.../<An>?<parameters>" async></script>
```

> [!IMPORTANT]
> A função de retorno de chamada deve ser definida antes da chamada para a API de classificação.

> [!TIP]
> Para melhorar a latência, a API de classificação é exposta via HTTP em vez de HTTPS `https://ds.microsoft.com/api/v2/<appId>/rank/*`, como no.
> No entanto, um ponto de extremidade HTTPS deve ser usado se a página inicial for servida por HTTPS.

Quando os parâmetros não são usados, a resposta HTTP da API de classificação é uma cadeia de caracteres JSONP:

```json
callback({
   "ranking":[{"id":"url1"}, {"id":"url2"}, {"id":"url3"}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "actionSets":[{"id":"<A1>","lastRefresh":"2017-04-29T22:34:25.3401438Z"},
                 {"id":"<A2>","lastRefresh":"2017-04-30T22:34:25.3401438Z"}]});
```

Em seguida, o browser executa essa cadeia como uma chamada para a função `callback()`.

O parâmetro para a função de retorno de chamada no exemplo anterior tem o seguinte esquema:

- `ranking`fornece a classificação de URLs a serem exibidas.
- `eventId`é usado internamente por Serviço de Decisão Personalizada para corresponder a essa classificação com os cliques correspondentes.
- `appId`permite que a função de retorno de chamada Diferencie entre vários aplicativos do Serviço de Decisão Personalizada em execução na mesma página da Web.
- `actionSets`lista cada conjunto de ações usado na chamada à API de classificação, juntamente com o carimbo de data/hora UTC da última atualização bem-sucedida. O Serviço de Decisão Personalizada atualiza periodicamente os feeds de conjunto de ações. Por exemplo, se alguns dos conjuntos de ações não forem atuais, a função de retorno de chamada poderá precisar retornar à sua classificação padrão.

> [!IMPORTANT]
> Os conjuntos de ações especificados são processados e, possivelmente, removidos, para formar a classificação padrão de artigos. Em seguida, a classificação padrão é reordenada e retornada na resposta HTTP. A classificação padrão é definida aqui:
>
> - Dentro de cada conjunto de ações, os artigos são removidos para os 15 artigos mais recentes (se mais de 15 forem retornados).
> - Quando vários conjuntos de ações são especificados, eles são mesclados na mesma ordem que na chamada à API. A ordenação original dos artigos é preservada em cada conjunto de ações. As duplicatas são removidas em favor das cópias anteriores.
> - Os primeiros `n` artigos são mantidos da lista mesclada de artigos, onde `n=20` por padrão.

### <a name="ranking-api-with-parameters"></a>API de classificação com parâmetros

A API de classificação permite esses parâmetros:

- `details=1`e `details=2` insere detalhes adicionais sobre cada artigo listado em `ranking`.
- `limit=<n>`Especifica o número máximo de artigos na classificação padrão. `n`deve estar entre `2` e `30` (ou, senão, é truncado `2` para `30`ou, respectivamente).
- `dnt=1`desabilita os cookies do usuário.

Os parâmetros podem ser combinados no padrão, a sintaxe da cadeia de `details=2&dnt=1`caracteres de consulta, por exemplo.

> [!IMPORTANT]
> A configuração padrão na Europa deve ser `dnt=1` até que o cliente concorde com a faixa do cookie. Ele também deve ser a configuração padrão para sites que se destinam a pequenos. Para obter mais informações, consulte os [termos de uso](https://www.microsoft.com/cognitive-services/en-us/legal/CognitiveServicesTerms20160804).

O `details=1` elemento insere cada `guid`artigo, se for servido pela API de conjunto de ações. A resposta HTTP:

```json
callback({
   "ranking":[{"id":"url1","details":[{"guid":"123"}]},
              {"id":"url2","details":[{"guid":"456"}]},
              {"id":"url3","details":[{"guid":"789"}]}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "actionSets":[{"id":"<A1>","lastRefresh":"timeStamp1"},
                 {"id":"<A2>","lastRefresh":"timeStamp2"}]});
```

O `details=2` elemento adiciona mais detalhes que serviço de decisão personalizada podem extrair do [código personalização](https://github.com/Microsoft/mwt-ds/tree/master/Crawl)de submarcações de SEO de artigos:

- `title`de `<meta property="og:title" content="..." />` ou `<meta property="twitter:title" content="..." />` ou`<title>...</title>`
- `description`de `<meta property="og:description" ... />` ou `<meta property="twitter:description" content="..." />` ou`<meta property="description" content="..." />`
- `image`De`<meta property="og:image" content="..." />`
- `ds_id`De`<meta name=”microsoft:ds_id” content="..." />`

A resposta HTTP:

```json
callback({
   "ranking":[{"id":"url1","details":<details>},
              {"id":"url2","details":<details>},
              {"id":"url3","details":<details>}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "actionSets":[{"id":"<A1>","lastRefresh":"timeStamp1"},
                 {"id":"<A2>","lastRefresh":"timeStamp2"}]});
```

O `<details>` elemento:

```json
[{"guid":"123"}, {"description":"some text", "ds_id":"234", "image":"ImageUrl1", "title":"some text"}]
```

## <a name="reward-api"></a>A API de recompensa

Serviço de Decisão Personalizada usa apenas cliques no slot superior. Cada clique é interpretado como um prêmio de 1. A falta de um clique é interpretada como um prêmio de 0. Os cliques são correspondidos com as classificações correspondentes usando IDs de eventos, que são geradas pela chamada à [API de classificação](#ranking-api) . Se necessário, as IDs de evento podem ser passadas por meio de cookies de sessão.

Para lidar com um clique no slot superior, coloque esse código em sua página inicial:

```javascript
$.ajax({
    type: "POST",
    url: '//ds.microsoft.com/api/v2/<appId>/reward/' + data.eventId,
    contentType: "application/json" })
```

Aqui `data` está o argumento para a `callback()` função, conforme descrito anteriormente. O `data` uso do no código de tratamento de clique requer algum cuidado. Um exemplo é mostrado neste [tutorial](custom-decision-service-tutorial-news.md#use-the-apis).

Somente para teste, a API de recompensa pode ser chamada [](https://en.wikipedia.org/wiki/CURL)por meio da ondulação:

```sh
curl -v https://ds.microsoft.com/api/v2/<appId>/reward/<eventId> -X POST -d 1 -H "Content-Type: application/json"
```

O efeito esperado é uma resposta HTTP de 200 (OK). Você pode ver a recompensa de 1 para esse evento no log (se uma chave de conta de armazenamento do Azure foi fornecida no Portal).

## <a name="action-set-api-customer-provided"></a>API de conjunto de ação (fornecida pelo cliente)

Em um alto nível, a API de conjunto de ações retorna uma lista de artigos (ações). Cada artigo é especificado por sua URL e (opcionalmente) o título do artigo e a data da publicação. Você pode especificar vários conjuntos de ações no Portal. Uma API de conjunto de ação diferente deve ser usada para cada conjunto de ações, como uma URL distinta.

Cada API de conjunto de ações pode ser implementada de duas maneiras: como um RSS feed ou como um feed ATOM. Qualquer um deve estar em conformidade com o padrão e retornar um XML correto. Para RSS, aqui está um exemplo:

```xml
<rss version="2.0">
<channel>
   <item>
      <title><![CDATA[title (possibly with url) ]]></title>
      <link>url</link>
      <guid>guid (could be a your internal database id)</guid>
      <pubDate>date</pubDate>
    </item>
   <item>
       ....
   </item>
</channel>
</rss>
```

Cada elemento de nível `<item>` superior descreve uma ação:

- `<link>`é obrigatório e é usado como uma ID de ação.
- `<date>`será ignorado se for menor ou igual a 15 itens; caso contrário, é obrigatório.
  - Se houver mais de 15 itens, serão usados os 15 mais recentes.
  - Ele deve estar no formato padrão para RSS ou Atom, respectivamente:
    - [RFC 822](https://tools.ietf.org/html/rfc822) para RSS: por exemplo,`"Fri, 28 Apr 2017 18:02:06 GMT"`
    - [RFC 3339](https://tools.ietf.org/html/rfc3339) para Atom: por exemplo,`"2016-12-19T16:39:57-08:00"`
- `<title>`é opcional e é usado para gerar recursos que descrevem o artigo.
- `<guid>`é opcional e transmitido pelo sistema para a função de retorno de chamada `?details` (se o parâmetro for especificado na chamada à API de classificação).

Outros elementos dentro de `<item>` um são ignorados.

A versão de Feed Atom usa a mesma sintaxe e convenções XML.

> [!TIP]
> Se o seu sistema usar suas próprias IDs de artigo, elas poderão ser passadas para a função `<guid>`de retorno de chamada usando.
