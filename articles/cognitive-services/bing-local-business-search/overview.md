---
title: O que é a API de Pesquisa de Comércio Local do Bing?
titleSuffix: Azure Cognitive Services
description: A API de Pesquisa de Comércio Local do Bing é um serviço RESTful que permite que as suas aplicações encontrem informações sobre negócios e sítios locais com base em consultas de pesquisa.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: overview
ms.date: 03/24/2020
ms.author: aahi
ms.openlocfilehash: 0006761126eb1d561da7eeff97e8a9928d62ddb0
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478929"
---
# <a name="what-is-bing-local-business-search"></a>O que é Bing Local Business Search?
O Bing Local Business Search API é um serviço RESTful que permite às suas aplicações encontrar informações sobre empresas locais com base em consultas de pesquisa. Por exemplo, `q=<business-name> in Redmond, Washington` `q=Italian restaurants near me`ou . 

## <a name="features"></a>Funcionalidades
| Funcionalidade | Descrição |  
| -- | -- | 
| [Encontre negócios e locais locais](quickstarts/local-quickstart.md) | A API local de pesquisa de negócios bing obtém resultados localizados de uma consulta. Os resultados incluem um URL para o site da empresa e mostrar texto, número de telefone e localização geográfica, incluindo: coordenadas GPS, cidade, endereço de rua |  
| [Filtrar os resultados locais com limites geográficos](specify-geographic-search.md) | Adicione coordenadas como parâmetros de pesquisa para limitar os resultados a uma área geográfica específica, especificada por uma área circular ou uma caixa de delimitação quadrada. | 
| [Filtrar os resultados do negócio local por categoria](local-categories.md) | Pesquisa de resultados de negócios locais por categoria. Esta opção utiliza a localização IP inversa ou as coordenadas GPS do chamador para devolver resultados localizados em várias categorias de negócios.|

## <a name="workflow"></a>Fluxo de trabalho
Ligue para a API de Pesquisa de Negócios Local Bing a partir de qualquer linguagem de programação que possa fazer pedidos HTTP e analisar respostas JSON. Este serviço é acessível utilizando a API REST.
 
1. Crie uma conta API de [Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com acesso às APIs de Pesquisa bing. Se não tiver uma subscrição do Azure, pode [criar uma conta gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).   
2. URL codifica os seus `q=""` termos de pesquisa para o parâmetro de consulta. Por exemplo, `q=nearby+restaurant` ou `q=nearby%20restaurant`. Definir paginação também, se necessário. 
3. Envie um [pedido para a API](quickstarts/local-quickstart.md) local de pesquisa de negócios de Bing 
4. Parse a resposta JSON 

> [!NOTE]
> Atualmente, Pesquisa local de negócios: 
> * Só apoia o `en-US` mercado. 
> * Não suporta bing Autosuggest. 

## <a name="next-steps"></a>Passos seguintes
- [Consulta e resposta](local-search-query-response.md)
- [Pesquisa de negócios locais quickstart](quickstarts/local-quickstart.md)
- [Referência da API de Pesquisa de Negócios Locais](local-search-reference.md)
- [Requisitos de utilização e de apresentação](use-display-requirements.md)
