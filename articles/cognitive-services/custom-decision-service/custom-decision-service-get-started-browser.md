---
title: Chamar a API de um navegador-Serviço de Decisão Personalizada
titlesuffix: Azure Cognitive Services
description: Como otimizar uma página da Web fazendo chamadas de API diretamente de um navegador para o Serviço de Decisão Personalizada.
services: cognitive-services
author: slivkins
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: slivkins
ROBOTS: NOINDEX
ms.openlocfilehash: 28ad4681242765bf2da9b1f13dc828e23cce1794
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707273"
---
# <a name="call-api-from-a-browser"></a>Chamar a API num browser

Este artigo ajuda você a fazer chamadas para as APIs de Serviço de Decisão Personalizada do Azure diretamente de um navegador.

Certifique-se de [registrar seu aplicativo](custom-decision-service-get-started-register.md)primeiro.

Vamos começar. Seu aplicativo é modelado como tendo uma página frontal, que é vinculada a várias páginas de artigo. A página frontal usa Serviço de Decisão Personalizada para especificar a ordenação de suas páginas de artigo. Insira o código a seguir no cabeçalho HTML da página inicial:

```html
// Define the "callback function" to render UI
<script> function callback(data) { … } </script>

// call Ranking API, after callback() is defined
<script src="https://ds.microsoft.com/api/v2/<appId>/rank/<actionSetId>" async></script>
```

O `data` argumento contém a classificação de URLs a serem renderizadas. Para obter mais informações, consulte a [API](custom-decision-service-api-reference.md)de referência.

Para manipular um usuário, clique no artigo superior, chame o seguinte código na página inicial:

```javascript
// call Reward API to report a click
$.ajax({
    type: "POST",
    url: '//ds.microsoft.com/api/v2/<appId>/reward/' + data.eventId,
    contentType: "application/json" })
```

Aqui, `data` é o argumento para a `callback()` função. Um exemplo de implementação pode ser encontrado neste [tutorial](custom-decision-service-tutorial-news.md#use-the-apis).

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

## <a name="next-steps"></a>Passos Seguintes

* Trabalhe em um [tutorial](custom-decision-service-tutorial-news.md) para obter um exemplo mais aprofundado.
* Consulte a [API](custom-decision-service-api-reference.md) de referência para saber mais sobre a funcionalidade fornecida.
