---
title: O que é a API de Verificação Ortográfica do Bing?
titleSuffix: Azure Cognitive Services
description: Saiba mais sobre a API de Verificação ortográfica de Bing, que utiliza machine learning e tradução automática estatística para verificação de feitiços contextuais.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: overview
ms.date: 12/19/2019
ms.author: aahi
ms.openlocfilehash: 205d7991551ca48c3ea2e61baef53f5d06bdcbd2
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86200491"
---
# <a name="what-is-the-bing-spell-check-api"></a>O que é a API de Verificação Ortográfica do Bing?

A API de Verificação ortográfica de Bing permite-lhe realizar gramática contextual e verificação ortográfica no texto. Embora a maioria dos verificadores de feitiços dependa de conjuntos de regras baseados no dicionário, o verificador ortográfico Bing aproveita a aprendizagem automática e a tradução de máquinas estatísticas para fornecer correções precisas e contextuais. 

## <a name="features"></a>Funcionalidades

| Funcionalidade | Descrição |
|---------|---------|
|Vários modos de verificação ortográfica     | Vários modos de verificação ortográfica permitem-lhe obter correções focadas na gramática e/ou ortografia. |
|Gíria e reconhecimento de linguagem informal     | Reconhecer expressões comuns e termos informais usados em texto.         |
|Diferenciar entre palavras semelhantes     | Encontre o uso correto entre palavras que soam semelhantes mas diferem no significado (por exemplo, "ver" e "mar")        |
|Marca, título e suporte popular de uso     | Reconhecer novas marcas, títulos e outras expressões populares à medida que emergem |

## <a name="workflow"></a>Fluxo de trabalho

A API de Verificação ortográfica de Bing é fácil de ligar a partir de qualquer linguagem de programação que possa fazer pedidos HTTP e analisar respostas JSON. O serviço é acessível utilizando a API REST ou os Bing Spell Check SDKs. 

1. Crie uma [conta de API dos Serviços Cognitivos](../cognitive-services-apis-create-account.md) com acesso às APIs de Pesquisa do Bing. Se não tiver uma subscrição do Azure, pode criar uma conta gratuita. 
2. Envie um pedido para a API de Pesquisa na Web do Bing.
3. Parse a resposta JSON

## <a name="next-steps"></a>Passos seguintes

Primeiro, experimente a [demonstração interativa](https://azure.microsoft.com/services/cognitive-services/spell-check/) de pesquisa de Bing Spell Check API para ver como pode verificar rapidamente uma variedade de textos.

Quando estiver pronto para chamar a API, crie uma [conta dos serviços cognitivos da API](../../cognitive-services/cognitive-services-apis-create-account.md). Se não tiver uma subscrição do Azure, pode [criar uma conta](https://azure.microsoft.com/free/cognitive-services/) gratuita.

Também pode visitar a página do hub da [Bing Search API](../bing-web-search/search-the-web.md) para explorar as outras APIs disponíveis.