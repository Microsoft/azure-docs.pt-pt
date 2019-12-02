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
ms.date: 11/29/2019
ms.author: aahi
ms.openlocfilehash: 4e08596e8cf71bbb0e88abdc51f5d8e69972464d
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2019
ms.locfileid: "74665260"
---
# <a name="what-is-bing-local-business-search"></a>O que é a pesquisa de negócios local do Bing?
A API de pesquisa de negócios local do Bing é um serviço RESTful que permite que seus aplicativos encontrem informações sobre empresas locais com base em consultas de pesquisa. Por exemplo, `q=<business-name> in Redmond, Washington`ou `q=Italian restaurants near me`. 

## <a name="features"></a>Funcionalidades
| Funcionalidade | Descrição |  
| -- | -- | 
| [Encontre locais e empresas locais](quickstarts/local-quickstart.md) | A API de pesquisa de negócios local do Bing obtém resultados localizados de uma consulta. Os resultados incluem uma URL para o site da empresa e exibição de texto, número de telefone e localização geográfica, incluindo: coordenadas de GPS, cidade, endereço |  
| [Filtrar resultados locais com limites geográficos](specify-geographic-search.md) | Adicione coordenadas como parâmetros de pesquisa para limitar os resultados a uma área geográfica específica, especificada por uma área circular ou uma caixa delimitadora quadrada. | 
| [Filtrar resultados de negócios locais por categoria](local-categories.md) | Pesquise resultados de negócios locais por categoria. Essa opção usa localização de IP reverso ou coordenadas de GPS do chamador para retornar resultados localizados em várias categorias de negócios.|

## <a name="workflow"></a>Fluxo de Trabalho
Chame a API de pesquisa de negócios local do Bing de qualquer linguagem de programação que possa fazer solicitações HTTP e analisar respostas JSON. Esse serviço pode ser acessado usando a API REST.
 
1. Crie uma [conta de API de serviços cognitivas](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com acesso ao APIs de pesquisa do Bing. Se não tiver uma subscrição do Azure, pode [criar uma conta gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).   
2. Codifique a URL seus termos de pesquisa para o `q=""` parâmetro de consulta. Por exemplo, `q=nearby+restaurant` ou `q=nearby%20restaurant`. Defina a paginação também, se necessário. 
3. Enviar uma [solicitação para a API de pesquisa de negócios local do Bing](quickstarts/local-quickstart.md) 
4. Analisar a resposta JSON 

> [!NOTE]
> Atualmente, pesquisa de negócios local: 
> * Só dá suporte ao mercado de `en-US`. 
> * Não oferece suporte a Sugestão Automática do Bing. 

## <a name="next-steps"></a>Passos seguintes
- [Consulta e resposta](local-search-query-response.md)
- [Início rápido de pesquisa comercial local](quickstarts/local-quickstart.md)
- [Referência da API de pesquisa de negócios local](local-search-reference.md)
- [Requisitos de utilização e de apresentação](use-display-requirements.md)
