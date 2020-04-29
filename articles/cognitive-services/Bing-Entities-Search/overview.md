---
title: O que é a API de Pesquisa de Entidades Bing?
titleSuffix: Azure Cognitive Services
description: Utilize a API de Pesquisa de Entidades Bing para extrair e procurar entidades e locais a partir de consultas de pesquisa.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: scottwhi
ms.openlocfilehash: 2f374e29f4dc5406956cd56d1bb0bd1466e65773
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75384524"
---
# <a name="what-is-bing-entity-search-api"></a>O que é a API de Pesquisa de Entidades Bing?

A API de Pesquisa de Entidades do Bing envia uma consulta de pesquisa ao Bing e obtém resultados que incluem entidades e locais. Os resultados de locais incluem restaurantes, hotéis ou outras empresas locais. O Bing devolve locais, se a consulta especificar o nome da empresa local ou pedir um tipo de negócio (por exemplo, restaurantes perto de mim). Bing devolve entidades se a consulta especifica pessoas conhecidas, lugares (atrações turísticas, estados, países/regiões, etc.), ou coisas.

|Funcionalidade  |Descrição  |
|---------|---------|
|[Sugestões de pesquisa em tempo real](concepts/search-for-entities.md#suggest-search-terms-with-the-bing-autosuggest-api)     | Forneça sugestões de pesquisa que podem ser apresentadas como uma lista de dropdown à medida que os seus utilizadores escrevem.       | 
| [Desambiguação da entidade](concepts/search-for-entities.md#the-bing-entity-search-api-response)  | Obtenha várias entidades para consultas com múltiplos significados possíveis. |
| [Encontre lugares](concepts/search-for-entities.md#find-places) | Pesquisar e devolver informações sobre empresas e entidades locais  |

## <a name="workflow"></a>Fluxo de trabalho

O Bing Entity Search API é um serviço web RESTful, facilitando a chamada de qualquer linguagem de programação que possa fazer pedidos HTTP e analisar a JSON. Pode utilizar o serviço através da API REST ou o SDK.

1. Crie uma [conta de API dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com acesso às APIs de Pesquisa do Bing. Se não tiver uma subscrição do Azure, pode [criar uma conta](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) gratuita.
2. Envie um pedido para a API com uma consulta de pesquisa válida.
3. Processe a resposta da API ao analisar a mensagem JSON devolvida.

## <a name="next-steps"></a>Passos seguintes

* Experimente a [demonstração interativa](https://azure.microsoft.com/services/cognitive-services/bing-entity-search-api/) para a API de Pesquisa de Entidades Bing. 
* Para começar rapidamente com o seu primeiro pedido, tente um [Quickstart.](quickstarts/csharp.md)
* A secção de referência Da Pesquisa de [Entidades Bing API v7.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference)
* Os [Requisitos de apresentação e utilização do Bing](./use-display-requirements.md) especificam as utilizações aceitáveis do conteúdo e as informações obtidas por meio das APIs de Pesquisa do Bing.
* Visite a página do centro da [Bing Search API](../bing-web-search/search-the-web.md) para explorar as outras APIs disponíveis.