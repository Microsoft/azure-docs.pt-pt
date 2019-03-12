---
title: Referência da API - serviço de decisão personalizada
titlesuffix: Azure Cognitive Services
description: Um guia de API completado para o serviço de decisão personalizada.
services: cognitive-services
author: slivkins
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: slivkins
ms.openlocfilehash: be9966f5d8e8d94aa3f49aac91b35b105195b108
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57552068"
---
# <a name="api"></a>API

Serviço de decisão personalizada do Azure fornece duas APIs que são chamadas para cada decisão: os [API classificação](#ranking-api) para a classificação de ações de entrada e o [API de recompensa](#reward-api) para a recompensa de saída. Além disso, fornecem uma [a API de definir ação](#action-set-api-customer-provided) para especificar as ações de serviço de decisão personalizada do Azure. Este artigo aborda essas três APIs. Um cenário típico é utilizado abaixo para mostrar ao serviço de decisão personalizada otimiza a classificação de artigos.

## <a name="ranking-api"></a>API de classificação

A API de classificação utiliza um padrão [JSONP](https://en.wikipedia.org/wiki/JSONP)-padrão de comunicação de estilo para otimizar a latência e ignorar a [política de mesma origem](https://en.wikipedia.org/wiki/Same-origin_policy). O último proíbe o JavaScript de obtenção de dados a partir de fora da origem da página.

Inserir este trecho de código para o cabeçalho HTML da página frontal (onde são apresentadas uma lista personalizada de artigos):

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
> Para melhorar a latência, a API de classificação é exposta por meio de HTTP em vez de HTTPS, como na `https://ds.microsoft.com/api/v2/<appId>/rank/*`.
> No entanto, um ponto final HTTPS deve ser utilizado se a página é servida por meio de HTTPS.

Quando não são utilizados parâmetros, a resposta HTTP na API de classificação é uma cadeia de caracteres formatada em JSONP:

```json
callback({
   "ranking":[{"id":"url1"}, {"id":"url2"}, {"id":"url3"}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "actionSets":[{"id":"<A1>","lastRefresh":"2017-04-29T22:34:25.3401438Z"},
                 {"id":"<A2>","lastRefresh":"2017-04-30T22:34:25.3401438Z"}]});
```

Em seguida, o browser executa essa cadeia como uma chamada para a função `callback()`.

O parâmetro para a função de retorno de chamada no exemplo anterior tem o esquema seguinte:

- `ranking` Fornece a classificação de URLs a apresentar.
- `eventId` é utilizado internamente pelo serviço de decisão personalizada para fazer corresponder esta classificação com os cliques correspondentes.
- `appId` permite que a função de retorno de chamada para distinguir entre vários aplicativos de serviço de decisão personalizada em execução na mesma página Web.
- `actionSets` Apresenta uma lista de cada ação definida usada na chamada de API de classificação, juntamente com o carimbo de hora UTC da última atualização com êxito. Serviço de decisão personalizada atualiza periodicamente os feeds de conjunto de ação. Por exemplo, se alguns dos conjuntos de ação não estão atualizadas, a função de retorno de chamada poderá ter de reverter para a respetiva classificação predefinida.

> [!IMPORTANT]
> Os conjuntos de ação especificada são processados e, possivelmente, eliminados, para formar a classificação predefinida de artigos. Classificação predefinida, em seguida, obtém reordenada e devolvida na resposta HTTP. Classificação predefinida é definida aqui:
>
> - Dentro de cada conjunto de ação, os artigos são eliminados os 15 artigos mais recentes (se são devolvidas mais de 15).
> - Quando vários conjuntos de ação são especificados, eles são mesclados na mesma ordem, tal como a chamada de API. A ordem original dos artigos é preservada dentro de cada conjunto de ação. Duplicatas são removidas e substituídas pelas cópias anteriores.
> - A primeira `n` artigos são mantidos na lista unida de artigos, onde `n=20` por predefinição.

### <a name="ranking-api-with-parameters"></a>Classificação de API com parâmetros

A API de classificação permite que estes parâmetros:

- `details=1` e `details=2` insere detalhes adicionais sobre cada artigo listados na `ranking`.
- `limit=<n>` Especifica o número máximo de artigos na classificação predefinida. `n` tem de estar entre `2` e `30` (ou outro é truncado para `2` ou `30`, respectivamente).
- `dnt=1` desativa os cookies de utilizador.

Parâmetros podem ser combinados em padrão, sintaxe de cadeia de caracteres de consulta, por exemplo `details=2&dnt=1`.

> [!IMPORTANT]
> Deve ser a predefinição na Europa `dnt=1` até o cliente aceita a faixa de cookie. Também deve ser a predefinição para os Web sites direcionados para os menores. Para obter mais informações, consulte a [termos de utilização](https://www.microsoft.com/cognitive-services/en-us/legal/CognitiveServicesTerms20160804).

O `details=1` elemento insere cada artigo `guid`, se esta for servida pela API de definir a ação. A resposta HTTP:

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

O `details=2` elemento adiciona mais detalhes que o serviço de decisão personalizada poderá extrair metatags SEO dos artigos [featurization código](https://github.com/Microsoft/mwt-ds/tree/master/Crawl):

- `title` partir `<meta property="og:title" content="..." />` ou `<meta property="twitter:title" content="..." />` ou `<title>...</title>`
- `description` partir `<meta property="og:description" ... />` ou `<meta property="twitter:description" content="..." />` ou `<meta property="description" content="..." />`
- `image` De `<meta property="og:image" content="..." />`
- `ds_id` De `<meta name=”microsoft:ds_id” content="..." />`

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

## <a name="reward-api"></a>API de recompensa

O serviço de decisão personalizadas utiliza cliques apenas no bloco de principal. Cada clique é interpretado como uma recompensa de 1. A falta de um clique é interpretada como uma recompensa de 0. Cliques correspondidos com as classificações correspondentes ao utilizar os IDs de evento, que foram gerados pela [classificação API](#ranking-api) chamar. Se for necessário, evento IDs podem ser passados através de cookies de sessão.

Para lidar com um clique no bloco de principal, coloca esse código na sua página de front:

```javascript
$.ajax({
    type: "POST",
    url: '//ds.microsoft.com/api/v2/<appId>/reward/' + data.eventId,
    contentType: "application/json" })
```

Aqui `data` é o argumento para o `callback()` funcione, conforme descrito anteriormente. Usando `data` no, clique em código de processamento necessário algum cuidado. Um exemplo é mostrado nesta [tutorial](custom-decision-service-tutorial-news.md#use-the-apis).

Apenas para teste, a API de recompensa pode ser chamada [cURL](https://en.wikipedia.org/wiki/CURL):

```sh
curl -v https://ds.microsoft.com/api/v2/<appId>/reward/<eventId> -X POST -d 1 -H "Content-Type: application/json"
```

O efeito esperado é uma resposta HTTP de 200 (OK). Pode ver a recompensa de 1 para este evento no registo (se uma chave de conta de armazenamento do Azure foi fornecida no portal).

## <a name="action-set-api-customer-provided"></a>API de definir ação (pelo cliente)

Num alto nível, a API de definir a ação devolve uma lista de artigos (ações). Cada artigo é especificado pelo respetivo URL e (opcionalmente) o título do artigo e a data de publicação. É possível especificar que vários ação define no portal. Uma API diferente de definir ação deve ser usada para cada conjunto de ação, como uma URL diferente.

API de definir cada ação pode ser implementado de duas maneiras: como um feed RSS ou como um feed Atom. Seja um deve estar em conformidade com o padrão e retornar um XML correto. Para RSS, eis um exemplo:

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

Cada nível superior `<item>` elemento descreve uma ação:

- `<link>` é obrigatório e é utilizado como um ID de ação.
- `<date>` é ignorada se é inferior ou igual a 15 itens; caso contrário, é obrigatório.
  - Se existirem mais de 15 itens, são utilizados os 15 que mais recentes.
  - Tem de ter o formato padrão para RSS ou Atom, respectivamente:
    - [RFC 822](https://tools.ietf.org/html/rfc822) para RSS: por exemplo, `"Fri, 28 Apr 2017 18:02:06 GMT"`
    - [RFC 3339](https://tools.ietf.org/html/rfc3339) para Atom: por exemplo, `"2016-12-19T16:39:57-08:00"`
- `<title>` é opcional e é utilizado para gerar recursos que descrevem o artigo.
- `<guid>` é opcional e transmitido através do sistema para a função de retorno de chamada (se o `?details` parâmetro for especificado na chamada de API de classificação).

Outros elementos dentro de um `<item>` são ignorados.

O Atom feed versão utiliza a mesma sintaxe XML e convenções.

> [!TIP]
> Se o seu sistema utiliza o seu próprio artigo IDs, eles podem passar para a função de retorno de chamada por usando `<guid>`.
