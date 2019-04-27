---
title: Chamar API a partir de um browser - serviço de decisão personalizada
titlesuffix: Azure Cognitive Services
description: Como otimizar uma página Web por meio de chamadas de API, para o serviço de decisão personalizada, diretamente a partir de um browser.
services: cognitive-services
author: slivkins
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: slivkins
ms.openlocfilehash: 2b356e2f0fe9235d49dffa7417cd3894059f9caf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60829162"
---
# <a name="call-api-from-a-browser"></a>Chamar a API num browser

Este artigo ajuda-o a fazer chamadas para as APIs de serviço de decisão de personalizado do Azure diretamente a partir de um browser.

Não se esqueça [registar a sua aplicação](custom-decision-service-get-started-register.md), primeiro.

Vamos começar. Seu aplicativo é modelado como tendo uma página de front-, que contém ligações para várias páginas do artigo. Página frontal utiliza o serviço de decisão personalizada para especificar a ordem das suas páginas do artigo. Insira o seguinte código para o cabeçalho HTML da página frontal:

```html
// Define the "callback function" to render UI
<script> function callback(data) { … } </script>

// call Ranking API, after callback() is defined
<script src="https://ds.microsoft.com/api/v2/<appId>/rank/<actionSetId>" async></script>
```

O `data` argumento contém a classificação de URLs a ser processado. Para obter mais informações, consulte a referência [API](custom-decision-service-api-reference.md).

Para lidar com um clique de utilizador do artigo superior, chame o seguinte código na primeira página do:

```javascript
// call Reward API to report a click
$.ajax({
    type: "POST",
    url: '//ds.microsoft.com/api/v2/<appId>/reward/' + data.eventId,
    contentType: "application/json" })
```

Aqui, `data` é o argumento para o `callback()` função. Um exemplo de implementação pode ser encontrado neste [tutorial](custom-decision-service-tutorial-news.md#use-the-apis).

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
