---
title: O que é a API de Pesquisa de Comércio Local do Bing? | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: A API de Pesquisa de Comércio Local do Bing é um serviço RESTful que permite que as suas aplicações encontrem informações sobre negócios e sítios locais com base em consultas de pesquisa.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.topic: article
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: fb3b375a66ac9b05ad26287b7c39ce8e748ad699
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57780325"
---
# <a name="what-is-bing-local-business-search"></a>O que é o Local de pesquisa empresarial do Bing?
A API de pesquisa de negócios locais do Bing é um serviço RESTful, que permite que seus aplicativos obter informações sobre os negócios locais com base nas consultas de pesquisa. Por exemplo, `q=<business-name> in Redmond, Washington`, ou `q=Italian restaurants near me`. 

## <a name="features"></a>Funcionalidades
| Funcionalidade | Descrição |  
| -- | -- | 
| [Encontrar localizações e negócios locais](quickstarts/local-quickstart.md) | A API de pesquisa de negócios locais do Bing obtém resultados localizados de uma consulta. Os resultados incluem um URL para o site da empresa e apresentar o texto, número de telefone e localização geográfica, incluindo: Coordena de GPS, cidade, morada |  
| [Filtrar os resultados locais com limites geográficos](specify-geographic-search.md) | Adicione as coordenadas como parâmetros de pesquisa para limitar os resultados para uma área geográfica específica, especificado por uma área circular ou quadrado caixa delimitadora. | 
| [Filtrar os resultados de negócios locais por categoria](local-categories.md) | Pesquisar resultados de negócios locais por categoria. Esta opção utiliza a localização do IP inversa ou as coordenadas do GPS do chamador para devolver resultados localizados em várias categorias de negócio.|

## <a name="workflow"></a>Fluxo de Trabalho
Chame a API de pesquisa de negócios locais do Bing a partir de qualquer linguagem de programação que pode fazer solicitações HTTP e analisar as respostas JSON. Este serviço é acessível através da API REST.
 
1. Criar uma [conta de API dos serviços cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com acesso às APIs de pesquisa do Bing. Se não tiver uma subscrição do Azure, pode [criar uma conta gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).   
2. URL de codificar seus termos de pesquisa para o `q=""` parâmetro de consulta. Por exemplo, `q=nearby+restaurant` ou `q=nearby%20restaurant`. Definir a paginação, se necessário. 
3. Enviar um [pedido para a API de pesquisa de negócios locais do Bing](quickstarts/local-quickstart.md) 
4. Analisar a resposta JSON 

> [!NOTE]
> Atualmente, o Local de negócios de pesquisa suporta apenas o `en-US` mercado. 
> [!NOTE]
> Atualmente, o Local de negócios de pesquisa não suporta a sugestão automática. 

## <a name="next-steps"></a>Passos Seguintes
- [Consulta e resposta](local-search-query-response.md)
- [Guia de introdução de pesquisa de negócios local](quickstarts/local-quickstart.md)
- [Referência da API de pesquisa de negócios local](local-search-reference.md)
- [Requisitos de utilização e de apresentação](use-display-requirements.md)
