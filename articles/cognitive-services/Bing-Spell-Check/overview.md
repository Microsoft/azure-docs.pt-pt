---
title: O que é a API de Verificação Ortográfica do Bing?
titlesuffix: Azure Cognitive Services
description: Saiba mais sobre a API de verificação do Bing ortográfica, que utiliza machine learning e estatísticos de tradução automática para verificação ortográfica contextual.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: overview
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: 22f75efb3cb4baa645030e7ad64072674de662ed
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56889954"
---
# <a name="what-is-the-bing-spell-check-api"></a>O que é a API de Verificação Ortográfica do Bing?

A API de verificação de ortografia do Bing permite-lhe realizar a gramática contextual e ortográfica no texto. Enquanto a maioria dos verificadores ortográficos dependem de conjuntos de regras com base no dicionário, o Verificador de ortografia do Bing utiliza machine learning e estatísticos de tradução automática para fornecer correções precisas e contextuais. 

## <a name="features"></a>Funcionalidades


|  |  |
|---------|---------|
|Vários modos de verificação ortográfica     | Vários modos de verificação de ortografia permitem-lhe obter as correções se concentrou em gramática e/ou ortografia. |
|Gíria e reconhecimento de linguagem informal     | Reconhece expressões comuns e termos informais utilizados no texto.         |
|Distinção entre palavras semelhantes     | Encontrar a correta utilização entre as palavras isso parece semelhante, mas diferem no significado (por exemplo, "ver" e "mar")        |
|Marca, o título e o suporte de utilização populares     | Reconhecer novas marcas, títulos e outras expressões populares que possam surgir |

## <a name="workflow"></a>Fluxo de trabalho

A API de verificação de ortografia do Bing é fácil chamar a partir de qualquer linguagem de programação que pode fazer solicitações HTTP e analisar as respostas JSON. O serviço está acessível através da API REST ou os SDKs de verificação de ortografia do Bing. 

1. Crie uma [conta de API dos Serviços Cognitivos](../cognitive-services-apis-create-account.md) com acesso às APIs de Pesquisa do Bing. Se não tiver uma subscrição do Azure, pode criar uma conta gratuita. 
2. Envie um pedido para a API de pesquisa Web Bing.
3. Analisar a resposta JSON

## <a name="next-steps"></a>Passos Seguintes

Em primeiro lugar, experimente a API de pesquisa do Bing de verificação de ortográfica [demonstração interativa](https://azure.microsoft.com/services/cognitive-services/spell-check/) para ver como pode verificar rapidamente uma variedade de textos.

Quando estiver pronto para chamar a API, crie uma [conta dos serviços cognitivos da API](../../cognitive-services/cognitive-services-apis-create-account.md). Se não tiver uma subscrição do Azure, pode [criar uma conta](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) gratuita.
