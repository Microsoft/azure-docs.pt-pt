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
ms.openlocfilehash: ee31eac55298c86a7bdd784ea54c9a0c8caa47a4
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2020
ms.locfileid: "94364620"
---
# <a name="what-is-bing-local-business-search"></a>O que é Bing Local Business Search?

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](https://aka.ms/cogsvcs/bingmove)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](https://aka.ms/cogsvcs/bingmigration)
A Bing Local Business Search API é um serviço RESTful que permite às suas aplicações encontrar informações sobre empresas locais com base em consultas de pesquisa. Por `q=<business-name> in Redmond, Washington` exemplo, ou `q=Italian restaurants near me` . . 

## <a name="features"></a>Funcionalidades
| Funcionalidade | Descrição |  
| -- | -- | 
| [Encontre negócios e locais locais](quickstarts/local-quickstart.md) | A API de Pesquisa de Negócios Locais Bing obtém resultados localizados a partir de uma consulta. Os resultados incluem um URL para o site da empresa e texto de exibição, número de telefone e localização geográfica, incluindo: coordenadas GPS, cidade, endereço de rua |  
| [Filtrar os resultados locais com limites geográficos](specify-geographic-search.md) | Adicione coordenadas como parâmetros de pesquisa para limitar os resultados a uma área geográfica específica, especificada por uma área circular ou caixa de delimitação quadrada. | 
| [Filtrar os resultados do negócio local por categoria](local-categories.md) | Procure por categoria os resultados do negócio local. Esta opção utiliza a localização IP inversa ou as coordenadas GPS do chamador para devolver resultados localizados em várias categorias de negócio.|

## <a name="workflow"></a>Fluxo de trabalho
Ligue para a API de pesquisa de negócios locais de Bing a partir de qualquer linguagem de programação que possa fazer pedidos HTTP e analisar respostas JSON. Este serviço é acessível utilizando a API REST.
 
1. Criar uma [conta API de Serviços Cognitivos](../cognitive-services-apis-create-account.md)  com acesso às APIs de Pesquisa de Bing. Se não tiver uma subscrição do Azure, pode [criar uma conta gratuita](https://azure.microsoft.com/free/cognitive-services/).   
2. URL codifica os seus termos de pesquisa para o `q=""` parâmetro de consulta. Por exemplo, `q=nearby+restaurant` ou `q=nearby%20restaurant`. Desfole a paginação também, se necessário. 
3. Envie um [pedido à API de Pesquisa Empresarial Local de Bing](quickstarts/local-quickstart.md) 
4. Parse a resposta JSON 

> [!NOTE]
> Atualmente, Pesquisa de Negócios Locais: 
> * Só apoia o `en-US` mercado. 
> * Não suporta Bing Autosuggest. 

## <a name="next-steps"></a>Passos seguintes
- [Consulta e resposta](local-search-query-response.md)
- [Pesquisa de negócios local quickstart](quickstarts/local-quickstart.md)
- [Referência da API de Pesquisa de Negócios Locais](local-search-reference.md)
- [Requisitos de utilização e de apresentação](../bing-web-search/use-display-requirements.md)