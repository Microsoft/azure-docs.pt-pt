---
title: O que é a API de Pesquisa de Comércio Local do Bing?
titleSuffix: Azure Cognitive Services
description: A API de Pesquisa de Comércio Local do Bing é um serviço RESTful que permite que as suas aplicações encontrem informações sobre negócios e sítios locais com base em consultas de pesquisa.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: article
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: 2078e4842f6b8af11fcf56760579ce1ec77dd23f
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423376"
---
# <a name="what-is-bing-local-business-search"></a>O que é a pesquisa de negócios local do Bing?
A API de pesquisa de negócios local do Bing é um serviço RESTful que permite que seus aplicativos encontrem informações sobre empresas locais com base em consultas de pesquisa. Por exemplo, `q=<business-name> in Redmond, Washington`, ou `q=Italian restaurants near me`. 

## <a name="features"></a>Funcionalidades
| Funcionalidade | Descrição |  
| -- | -- | 
| [Encontre locais e empresas locais](quickstarts/local-quickstart.md) | A API de pesquisa de negócios local do Bing obtém resultados localizados de uma consulta. Os resultados incluem uma URL para o site da empresa e o texto de exibição, o número de telefone e a localização geográfica, incluindo: Coordenadas do GPS, cidade, endereço |  
| [Filtrar resultados locais com limites geográficos](specify-geographic-search.md) | Adicione coordenadas como parâmetros de pesquisa para limitar os resultados a uma área geográfica específica, especificada por uma área circular ou uma caixa delimitadora quadrada. | 
| [Filtrar resultados de negócios locais por categoria](local-categories.md) | Pesquise resultados de negócios locais por categoria. Essa opção usa localização de IP reverso ou coordenadas de GPS do chamador para retornar resultados localizados em várias categorias de negócios.|

## <a name="workflow"></a>Fluxo de trabalho
Chame a API de pesquisa de negócios local do Bing de qualquer linguagem de programação que possa fazer solicitações HTTP e analisar respostas JSON. Esse serviço pode ser acessado usando a API REST.
 
1. Crie uma [conta de API de serviços cognitivas](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com acesso ao APIs de pesquisa do Bing. Se não tiver uma subscrição do Azure, pode [criar uma conta gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).   
2. Codifique a URL seus termos de pesquisa `q=""` para o parâmetro de consulta. Por exemplo, `q=nearby+restaurant` ou `q=nearby%20restaurant`. Defina a paginação também, se necessário. 
3. Enviar uma [solicitação para a API de pesquisa de negócios local do Bing](quickstarts/local-quickstart.md) 
4. Analisar a resposta JSON 

> [!NOTE]
> Atualmente, a pesquisa de negócios local dá `en-US` suporte apenas ao mercado. 
> [!NOTE]
> Atualmente, a pesquisa de negócios local não dá suporte à sugestão automática. 

## <a name="next-steps"></a>Passos Seguintes
- [Consulta e resposta](local-search-query-response.md)
- [Início rápido de pesquisa comercial local](quickstarts/local-quickstart.md)
- [Referência da API de pesquisa de negócios local](local-search-reference.md)
- [Requisitos de utilização e de apresentação](use-display-requirements.md)
