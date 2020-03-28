---
title: O que é a API de Verificação Ortográfica do Bing?
titleSuffix: Azure Cognitive Services
description: Conheça a API bing spell check, que utiliza machine learning e tradução estatística de máquinas para verificação de feitiços contextuais.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: overview
ms.date: 12/19/2019
ms.author: aahi
ms.openlocfilehash: 5586704e098fc568c714e779c2eed44aa3d1df9c
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75383004"
---
# <a name="what-is-the-bing-spell-check-api"></a>O que é a API de Verificação Ortográfica do Bing?

A API bing spell check permite-lhe realizar gramática contextual e verificação ortográfica no texto. Embora a maioria dos verificadores de feitiços dependa de conjuntos de regras baseados no dicionário, o verificador de feitiços Bing aproveita a aprendizagem automática e a tradução de máquinas estatísticas para fornecer correções precisas e contextuais. 

## <a name="features"></a>Funcionalidades


|  |  |
|---------|---------|
|Vários modos de verificação de feitiços     | Vários modos de verificação de feitiços permitem-lhe obter correções focadas na gramática e/ou ortografia. |
|Gíria e reconhecimento linguístico informal     | Reconhecer expressões comuns e termos informais utilizados no texto.         |
|Diferenciar entre palavras semelhantes     | Encontre o uso correto entre palavras que soam semelhantes mas diferem em significado (por exemplo, "ver" e "mar")        |
|Brand, título e suporte popular de uso     | Reconhecer novas marcas, títulos e outras expressões populares à medida que surgem |

## <a name="workflow"></a>Fluxo de trabalho

O Bing Spell Check API é fácil de ligar de qualquer linguagem de programação que possa fazer pedidos HTTP e analisar respostas JSON. O serviço é acessível utilizando a API REST ou os SDKs de Verificação de Feitiços de Bing. 

1. Crie uma [conta de API dos Serviços Cognitivos](../cognitive-services-apis-create-account.md) com acesso às APIs de Pesquisa do Bing. Se não tiver uma subscrição do Azure, pode criar uma conta gratuita. 
2. Envie um pedido para a API de Pesquisa na Web do Bing.
3. Parse a resposta JSON

## <a name="next-steps"></a>Passos seguintes

Primeiro, experimente a [demonstração interativa](https://azure.microsoft.com/services/cognitive-services/spell-check/) Bing Spell Check API para ver como pode verificar rapidamente uma variedade de textos.

Quando estiver pronto para chamar a API, crie uma [conta dos serviços cognitivos da API](../../cognitive-services/cognitive-services-apis-create-account.md). Se não tiver uma subscrição do Azure, pode [criar uma conta](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) gratuita.

Também pode visitar a página do [hub da Bing Search API](../bing-web-search/search-the-web.md) para explorar as outras APIs disponíveis.