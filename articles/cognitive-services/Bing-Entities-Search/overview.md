---
title: O que é o API de Pesquisa de Entidade do Bing?
titleSuffix: Azure Cognitive Services
description: Use o API de Pesquisa de Entidade do Bing para extrair e Pesquisar entidades e locais de consultas de pesquisa.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: overview
ms.date: 02/01/2019
ms.author: scottwhi
ms.openlocfilehash: 8f43401296a154ee40e7c214ad63da878129244a
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68424024"
---
# <a name="what-is-bing-entity-search-api"></a>O que é API de Pesquisa de Entidade do Bing?

A API de Pesquisa de Entidades do Bing envia uma consulta de pesquisa ao Bing e obtém resultados que incluem entidades e locais. Os resultados de locais incluem restaurantes, hotéis ou outras empresas locais. O Bing devolve locais, se a consulta especificar o nome da empresa local ou pedir um tipo de negócio (por exemplo, restaurantes perto de mim). O Bing retornará entidades se a consulta especificar pessoas bem conhecidas, lugares (Tourist Attractions, Estados, países/regiões, etc.) ou coisas.

|Funcionalidade  |Descrição  |
|---------|---------|
|[Sugestões de pesquisa em tempo real](concepts/search-for-entities.md#suggest-search-terms-with-the-bing-autosuggest-api)     | Forneça sugestões de pesquisa que podem ser exibidas como uma lista suspensa como seu tipo de usuário.       | 
| [Desambiguidade de entidade](concepts/search-for-entities.md#the-bing-entity-search-api-response)  | Obtenha várias entidades para consultas com vários significados possíveis. |
| [Localizar locais](concepts/search-for-entities.md#find-places) | Pesquisar e retornar informações sobre empresas e entidades locais  |

## <a name="workflow"></a>Fluxo de trabalho

O API de Pesquisa de Entidade do Bing é um serviço Web RESTful, facilitando a chamada de qualquer linguagem de programação que possa fazer solicitações HTTP e analisar o JSON. Você pode usar o serviço usando a API REST ou o SDK.

1. Crie uma [conta de API dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com acesso às APIs de Pesquisa do Bing. Se não tiver uma subscrição do Azure, pode [criar uma conta](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) gratuita.
2. Envie uma solicitação para a API, com uma consulta de pesquisa válida.
3. Processe a resposta da API ao analisar a mensagem JSON devolvida.

## <a name="next-steps"></a>Passos Seguintes

* Experimente a [demonstração interativa](https://azure.microsoft.com/services/cognitive-services/bing-entity-search-api/) para o API de pesquisa de entidade do Bing. 
* Para começar rapidamente com sua primeira solicitação, experimente um guia de [início rápido](quickstarts/csharp.md).
* A seção de referência do [API de pesquisa de entidade do Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference) .
* Os [Requisitos de apresentação e utilização do Bing](./use-display-requirements.md) especificam as utilizações aceitáveis do conteúdo e as informações obtidas por meio das APIs de Pesquisa do Bing.
