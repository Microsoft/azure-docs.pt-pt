---
title: O que é a API de pesquisa de entidades do Bing?
titlesuffix: Azure Cognitive Services
description: Utilize a API de pesquisa de entidades do Bing para extrair e procurar entidades e locais das consultas de pesquisa.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: overview
ms.date: 02/01/2019
ms.author: scottwhi
ms.openlocfilehash: 0755688d1cc2f00ffef253b7695d9cb504f9e169
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "64866846"
---
# <a name="what-is-bing-entity-search-api"></a>O que é a API de pesquisa de entidades do Bing?

A API de Pesquisa de Entidades do Bing envia uma consulta de pesquisa ao Bing e obtém resultados que incluem entidades e locais. Os resultados de locais incluem restaurantes, hotéis ou outras empresas locais. O Bing devolve locais, se a consulta especificar o nome da empresa local ou pedir um tipo de negócio (por exemplo, restaurantes perto de mim). O Bing devolve entidades, se a consulta Especifica bem conhecidas pessoas, lugares (tourist attractions, Estados, países/regiões, etc.) ou coisas.

|Funcionalidade  |Descrição  |
|---------|---------|
|[Sugestões de pesquisa em tempo real](concepts/search-for-entities.md#suggest-search-terms-with-the-bing-autosuggest-api)     | Fornece sugestões de pesquisa que podem ser apresentados como uma lista suspensa como tipo de utilizadores.       | 
| [Desambiguação de entidades](concepts/search-for-entities.md#the-bing-entity-search-api-response)  | Obtenha várias entidades para consultas com várias significados possíveis. |
| [Encontrar locais](concepts/search-for-entities.md#find-places) | Procurar e devolver informações sobre os negócios locais e entidades  |

## <a name="workflow"></a>Fluxo de trabalho

A API de pesquisa de entidades do Bing é um serviço RESTful web, facilitando a chamar a partir de qualquer linguagem de programação que pode fazer solicitações HTTP e analisar JSON. Pode utilizar o serviço usando a API REST ou o SDK.

1. Crie uma [conta de API dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com acesso às APIs de Pesquisa do Bing. Se não tiver uma subscrição do Azure, pode [criar uma conta](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) gratuita.
2. Envie um pedido para a API, com uma consulta de pesquisa válidos.
3. Processe a resposta da API ao analisar a mensagem JSON devolvida.

## <a name="next-steps"></a>Passos Seguintes

* Experimente o [demonstração interativa](https://azure.microsoft.com/services/cognitive-services/bing-entity-search-api/) para a API de pesquisa de entidades do Bing. 
* Para começar a utilizar rapidamente com a sua primeira solicitação, experimente um [guia de introdução](quickstarts/csharp.md).
* O [API de pesquisa de entidades do Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference) secção de referência.
* Os [Requisitos de apresentação e utilização do Bing](./use-display-requirements.md) especificam as utilizações aceitáveis do conteúdo e as informações obtidas por meio das APIs de Pesquisa do Bing.
