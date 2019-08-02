---
title: Chamar a API de um Serviço de Decisão Personalizada de aplicativos
titlesuffix: Azure Cognitive Services
description: Como chamar as APIs de Serviço de Decisão Personalizada de um aplicativo do smartphone.
services: cognitive-services
author: slivkins
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/10/2018
ms.author: slivkins
ROBOTS: NOINDEX
ms.openlocfilehash: 08fbc1716d402c83bc2c33be82cba143c1737a55
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707260"
---
# <a name="call-api-from-an-app"></a>Chamar a API numa aplicação

Faça chamadas para as APIs de Serviço de Decisão Personalizada do Azure de um aplicativo do smartphone. Este artigo explica como começar a usar algumas opções básicas.

Certifique-se de [registrar seu aplicativo](custom-decision-service-get-started-register.md)primeiro.

Há duas chamadas à API que você faz de seu aplicativo do smartphone para Serviço de Decisão Personalizada: uma chamada para a API de classificação para obter uma lista classificada de seu conteúdo e uma chamada para a API de recompensa para relatar um prêmio. Aqui estão as chamadas de exemplo [](https://en.wikipedia.org/wiki/CURL)em ondulação.

Para obter mais informações, consulte a [API](custom-decision-service-api-reference.md)de referência.

Comece com a chamada à API de classificação. Crie o arquivo `<request.json>`, que tem a ID do conjunto de ações. Essa ID é o nome do feed RSS ou Atom correspondente que você inseriu no Portal:

```json
{"decisions":
     [{ "actionSets":[{"id":{"id":"<actionSetId>"}}] }]}
```

Muitos conjuntos de ações podem ser especificados da seguinte maneira:

```json
{"decisions":
    [{ "actionSets":[{"id":{"id":"<actionSetId1>"}},
                     {"id":{"id":"<actionSetId2>"}}] }]}
```

Esse arquivo JSON é enviado como parte da solicitação de classificação:

```shell
curl -d @<request.json> -X POST https://ds.microsoft.com/api/v2/<appId>/rank --header "Content-Type: application/json"
```

Aqui, `<appId>` é o nome do seu aplicativo registrado no Portal. Você deve receber um conjunto ordenado de itens de conteúdo, que podem ser renderizados em seu aplicativo. Um retorno de exemplo é semelhante A:

```json
[{ "ranking":[{"id":"actionId3"}, {"id":"actionId1"}, {"id":"actionId2"}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "rewardAction":"actionId3",
   "actionSets":[{"id":"<actionSetId1>","lastRefresh":"2017-04-29T22:34:25.3401438Z"},
                 {"id":"<actionSetId2>","lastRefresh":"2017-04-30T22:34:25.3401438Z"}]}]
```

A primeira parte do retorno tem uma lista de ações ordenadas, especificadas por suas IDs de ação. Para um artigo, a ID de ação é uma URL. A solicitação geral também tem um exclusivo `<eventId>`, criado pelo sistema.

Posteriormente, você pode especificar se observou um clique no primeiro item de conteúdo desse evento, que é `<actionId3>`. Em seguida, você pode relatar um recompensa `<eventId>` sobre isso para serviço de decisão personalizada por meio da API de recompensa, com outra solicitação, como:

```shell
curl -v https://ds.microsoft.com/api/v2/<appId>/reward/<eventId> -X POST
```

Por fim, você precisa fornecer a API de conjunto de ações, que retorna a lista de artigos (ações) a serem considerados pelo Serviço de Decisão Personalizada. Implemente essa API como um RSS feed, como mostrado aqui:

```xml
<rss version="2.0">
<channel>
   <item>
      <title><![CDATA[title (possibly with url) ]]></title>
      <link>url</link>
      <pubDate>Thu, 27 Apr 2017 16:30:52 GMT</pubDate>
    </item>
   <item>
       ....
   </item>
</channel>
</rss>
```

Aqui, cada elemento de nível `<item>` superior descreve um artigo. O `<link>` é obrigatório e é usado como uma ID de ação por serviço de decisão personalizada. Especifique `<date>` (em um formato RSS padrão) se você tiver mais de 15 artigos. Os 15 artigos mais recentes são usados. O `<title>` é opcional e é usado para criar recursos relacionados a texto para o artigo.

## <a name="next-steps"></a>Passos seguintes

* Trabalhe em um [tutorial](custom-decision-service-tutorial-news.md) para obter um exemplo mais aprofundado.
* Consulte a [API](custom-decision-service-api-reference.md) de referência para saber mais sobre a funcionalidade fornecida.
