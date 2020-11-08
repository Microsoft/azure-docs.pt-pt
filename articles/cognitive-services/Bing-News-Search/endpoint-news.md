---
title: Pontos finais de Pesquisa de Notícias do Bing
titleSuffix: Azure Cognitive Services
description: Este artigo fornece um resumo dos pontos finais da Pesquisa de Notícias da API; notícias, notícias de topo e notícias de tendência.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 1/10/2019
ms.author: aahi
ms.openlocfilehash: 9c095a46b3a0526b23645c9cbb5e99eb8eda9067
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2020
ms.locfileid: "94366405"
---
# <a name="bing-news-search-api-endpoints"></a>Bing News Search API pontos finais

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)

A **API de Pesquisa de Notícias** devolve artigos de notícias, páginas Web, imagens, vídeos e [entidades.](../bing-entities-search/overview.md) As entidades contêm informações sumárias sobre uma pessoa, lugar ou tópico.

## <a name="endpoints"></a>Pontos Finais

Para obter resultados de pesquisa de notícias usando a API de Pesquisa de Notícias Bing, envie um `GET` pedido para um dos seguintes pontos finais. Os cabeçalhos e os parâmetros URL definem especificações adicionais.

### <a name="news-items-by-search-query"></a>Notícias por consulta de pesquisa

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search
```

Retorna artigos de notícias com base numa consulta de pesquisa. Se a consulta de pesquisa estiver vazia, a API devolverá artigos de primeira das diferentes categorias. Envie uma consulta por url codificando o seu termo de pesquisa e anexando-o ao `q=""` parâmetro. Para disponibilidade, consulte [países/regiões e mercados apoiados.](language-support.md#supported-markets-for-news-search-endpoint)

### <a name="top-news-items-by-category"></a>Principais notícias por categoria

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news  
```

Devolve as principais notícias por categoria. Você pode especificamente solicitar os principais artigos de negócios, esportes ou entretenimento usando `category=business` `category=sports` , ou `category=entertainment` .  O `category` parâmetro só pode ser utilizado com o `/news` URL. Existem alguns requisitos formais para especificar categorias; refere-se `category` à documentação do [parâmetro de consulta.](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#query-parameters) Envie uma consulta por url codificando o seu termo de pesquisa e anexando-o ao `q=""` parâmetro. Para disponibilidade, consulte [países/regiões e mercados apoiados.](language-support.md#supported-markets-for-news-endpoint)

### <a name="trending-news-topics"></a>Tópicos de notícias de tendência 

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics
```

Devolve temas noticiosos que estão atualmente em tendência nas redes sociais. Quando a `/trendingtopics` opção é incluída, a pesquisa de Bing ignora vários outros parâmetros, tais como `freshness` e `?q=""` . Para disponibilidade, consulte [países/regiões e mercados apoiados.](language-support.md#supported-markets-for-news-trending-endpoint)

## <a name="next-steps"></a>Passos seguintes

Para mais detalhes sobre cabeçalhos, parâmetros, códigos de mercado, objetos de resposta, erros, etc., consulte a referência [de pesquisa de API v7 de pesquisa de Bing News.](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference)

Para obter informações completas sobre os parâmetros suportados por cada ponto final, consulte as páginas de referência para cada tipo.
Por exemplo, os pedidos básicos que utilizam a API de pesquisa de notícias, consulte [Bing News Search Quick-starts](/azure/cognitive-services/bing-news-search).