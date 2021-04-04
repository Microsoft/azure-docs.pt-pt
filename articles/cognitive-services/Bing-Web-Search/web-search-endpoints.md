---
title: Ponto final de pesquisa web
titleSuffix: Azure Cognitive Services
description: Para obter os resultados da pesquisa na Web, envie um `GET` pedido para o seguinte ponto final. Os cabeçalhos e os parâmetros URL definem especificações adicionais.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: aahi
ms.openlocfilehash: c882e3e4d0cd6ba594a700f4fd53c14103a8d1d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "94381257"
---
# <a name="web-search-endpoint"></a>Ponto final de pesquisa web

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)

A **API de Pesquisa Web** devolve páginas web, notícias, imagens, vídeos e [entidades.](../bing-entities-search/overview.md) As entidades têm informações sumárias sobre uma pessoa, lugar ou tópico.

## <a name="endpoint"></a>Ponto final

Para obter os resultados da pesquisa na Web utilizando a API Bing, envie um `GET` pedido para o seguinte ponto final. Os cabeçalhos e os parâmetros URL definem especificações adicionais.

**Ponto final**: Devolve resultados web relevantes para a consulta de pesquisa do utilizador definida por `?q=""` .

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/search
```

Ponto final: Para mais detalhes sobre cabeçalhos, parâmetros, códigos de mercado, objetos de resposta, erros e muito mais, consulte a referência [Bing Web API v7.](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)

## <a name="response-json"></a>Resposta JSON

A resposta a um pedido de pesquisa na Web inclui todos os resultados como objetos JSON. A análise do resultado requer procedimentos que manuseiem os elementos de cada tipo. Consulte o [tutorial](./tutorial-bing-web-search-single-page-app.md) e [o código fonte,](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Web-Search) por exemplo.

## <a name="next-steps"></a>Passos seguintes

As **APIs de Bing suportam** ações de pesquisa que devolvem resultados de acordo com o seu tipo. Todos os pontos finais de pesquisa retornam os resultados como objetos de resposta JSON.  Todos os pontos finais suportam consultas que devolvem uma língua e localização específicas por longitude, latitude e raio de pesquisa.

Para obter informações completas sobre os parâmetros suportados por cada ponto final, consulte as páginas de referência para cada tipo.
Para, por exemplo, de pedidos básicos que utilizem a API de pesquisa web, consulte [pesquisar o Início Rápido da Web](./overview.md).