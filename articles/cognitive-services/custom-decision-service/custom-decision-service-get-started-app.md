---
title: Chamar API a partir de uma aplicação - serviço de decisão personalizada
titlesuffix: Azure Cognitive Services
description: Como chamar as APIs de serviço de decisão personalizada a partir de uma aplicação de smartphone.
services: cognitive-services
author: slivkins
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/10/2018
ms.author: slivkins
ms.openlocfilehash: 0e5c99aae61fb927ea7f101bab74d661a747f88b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60511552"
---
# <a name="call-api-from-an-app"></a>Chamar a API numa aplicação

Fazer chamadas para as APIs de serviço de decisão de personalizado do Azure a partir de uma aplicação de smartphone. Este artigo explica como começar com algumas opções básicas.

Não se esqueça [registar a sua aplicação](custom-decision-service-get-started-register.md), primeiro.

Existem duas chamadas de API que efetuar na sua aplicação de smartphone para o serviço de decisão personalizada: uma chamada para a API de classificação para obter uma lista ordenada de seu conteúdo e uma chamada para a API de recompensa para reportar uma recompensa. Aqui estão as chamadas de exemplo na [cURL](https://en.wikipedia.org/wiki/CURL).

Para obter mais informações, consulte a referência [API](custom-decision-service-api-reference.md).

Comece com a chamada para a API de classificação. Criar o ficheiro `<request.json>`, que tem a ação definida ID. Este ID é o nome do correspondente RSS ou Atom feed que introduziu no portal:

```json
{"decisions":
     [{ "actionSets":[{"id":{"id":"<actionSetId>"}}] }]}
```

Vários conjuntos de ação podem ser especificados da seguinte forma:

```json
{"decisions":
    [{ "actionSets":[{"id":{"id":"<actionSetId1>"}},
                     {"id":{"id":"<actionSetId2>"}}] }]}
```

Este ficheiro JSON, em seguida, é enviado como parte do pedido de classificação:

```shell
curl -d @<request.json> -X POST https://ds.microsoft.com/api/v2/<appId>/rank --header "Content-Type: application/json"
```

Aqui, `<appId>` é o nome da sua aplicação é registado no portal. Deverá receber um conjunto ordenado de itens de conteúdo, o que pode renderizar em seu aplicativo. Um exemplo de retorno é semelhante a:

```json
[{ "ranking":[{"id":"actionId3"}, {"id":"actionId1"}, {"id":"actionId2"}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "rewardAction":"actionId3",
   "actionSets":[{"id":"<actionSetId1>","lastRefresh":"2017-04-29T22:34:25.3401438Z"},
                 {"id":"<actionSetId2>","lastRefresh":"2017-04-30T22:34:25.3401438Z"}]}]
```

A primeira parte do retorno tem uma lista de ações ordenadas, especificado pelo respetivos IDs de ação. Para um artigo, o ID de ação é um URL. O pedido geral também tem uma `<eventId>`, criada pelo sistema.

Mais tarde, pode especificar se observado um clique no primeiro item de conteúdo desse evento, o que é `<actionId3>`. Em seguida, pode denunciar uma recompensa sobre isso `<eventId>` para serviço de decisão personalizada através da API de recompensa, com outro pedido, tais como:

```shell
curl -v https://ds.microsoft.com/api/v2/<appId>/reward/<eventId> -X POST
```

Por fim, tem de fornecer a API de conjunto de ação, que retorna a lista de artigos (ações) para ser considerado pelo serviço de decisão personalizada. Implemente esta API como um RSS feed, conforme mostrado aqui:

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

Aqui, cada nível superior `<item>` elemento descreve um artigo. O `<link>` é obrigatória e é utilizado como um ID de ação pelo serviço de decisão personalizada. Especificar `<date>` (num formato padrão de RSS) se tiver mais do que 15 artigos. São utilizados os 15 artigos mais recentes. O `<title>` é opcional e é utilizado para criar funcionalidades relacionadas a texto para o artigo.

## <a name="next-steps"></a>Passos Seguintes

* Trabalhar com um [tutorial](custom-decision-service-tutorial-news.md) para obter um exemplo mais aprofundado.
* Consulte a referência [API](custom-decision-service-api-reference.md) para saber mais sobre a funcionalidade fornecida.
