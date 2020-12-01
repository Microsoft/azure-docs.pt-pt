---
title: O que é a API de Pesquisa de Entidade Bing?
titleSuffix: Azure Cognitive Services
description: Saiba mais detalhes sobre a API de Pesquisa de EntidadeS Bing e como extrair e procurar entidades e locais a partir de consultas de pesquisa.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: scottwhi
ms.openlocfilehash: 2a3d971ce9a4f89555eb3ffa489f8b19172a4b83
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351474"
---
# <a name="what-is-bing-entity-search-api"></a>O que é Bing Entity Search API?

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)

A API de Pesquisa de Entidades do Bing envia uma consulta de pesquisa ao Bing e obtém resultados que incluem entidades e locais. Os resultados de locais incluem restaurantes, hotéis ou outras empresas locais. O Bing devolve locais, se a consulta especificar o nome da empresa local ou pedir um tipo de negócio (por exemplo, restaurantes perto de mim). Bing devolve entidades se a consulta especificar pessoas, locais bem conhecidos (atrações turísticas, estados, países/regiões, etc.), ou coisas.

|Funcionalidade  |Descrição  |
|---------|---------|
|[Sugestões de pesquisa em tempo real](concepts/search-for-entities.md#suggest-search-terms-with-the-bing-autosuggest-api)     | Forneça sugestões de pesquisa que possam ser apresentadas como uma lista de dropdown como o seu tipo de utilizadores.       | 
| [Desambiguação de entidades](concepts/search-for-entities.md#the-bing-entity-search-api-response)  | Obtenha várias entidades para consultas com múltiplos significados possíveis. |
| [Encontrar lugares](concepts/search-for-entities.md#find-places) | Procurar e devolver informações sobre empresas e entidades locais  |

## <a name="workflow"></a>Fluxo de trabalho

A API de Pesquisa de Entidade Bing é um serviço web RESTful, facilitando a chamada a partir de qualquer linguagem de programação que possa fazer pedidos HTTP e analisar o JSON. Pode utilizar o serviço através da API REST ou o SDK.

1. Crie uma [conta de API dos Serviços Cognitivos](../cognitive-services-apis-create-account.md) com acesso às APIs de Pesquisa do Bing. Se não tiver uma subscrição do Azure, pode [criar uma conta](https://azure.microsoft.com/free/cognitive-services/) gratuita.
2. Envie um pedido para a API com uma consulta de pesquisa válida.
3. Processe a resposta da API ao analisar a mensagem JSON devolvida.

## <a name="next-steps"></a>Passos seguintes

* Experimente a [demonstração interativa](https://azure.microsoft.com/services/cognitive-services/bing-entity-search-api/) para a API de Pesquisa de EntidadeS Bing. 
* Para começar rapidamente com o seu primeiro pedido, experimente um [Quickstart](quickstarts/csharp.md).
* A secção de referência [de pesquisa de API v7 da Entidade Bing.](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference)
* Os [Requisitos de apresentação e utilização do Bing](../bing-web-search/use-display-requirements.md) especificam as utilizações aceitáveis do conteúdo e as informações obtidas por meio das APIs de Pesquisa do Bing.
* Visite a página do hub da [API de Pesquisa de Bing](../bing-web-search/overview.md) para explorar as outras APIs disponíveis.