---
title: O que é a API de Verificação Ortográfica do Bing?
titleSuffix: Azure Cognitive Services
description: Saiba mais sobre a API Verificação Ortográfica do Bing, que usa o aprendizado de máquina e a tradução da máquina estatística para verificação ortográfica contextual.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: overview
ms.date: 12/19/2019
ms.author: aahi
ms.openlocfilehash: 5586704e098fc568c714e779c2eed44aa3d1df9c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75383004"
---
# <a name="what-is-the-bing-spell-check-api"></a>O que é a API de Verificação Ortográfica do Bing?

A API Verificação Ortográfica do Bing permite que você execute a gramática contextual e a verificação ortográfica no texto. Embora a maioria dos verificadores ortográficos dependam de conjuntos de regras baseados em dicionário, o verificador ortográfico do Bing aproveita o aprendizado de máquina e a tradução automática do computador para fornecer correções precisas e contextuais. 

## <a name="features"></a>Funcionalidades


|  |  |
|---------|---------|
|Vários modos de verificação ortográfica     | Vários modos de verificação ortográfica permitem que você obtenha correções focadas na gramática e/ou na ortografia. |
|Reconhecimento de idioma informal e gírias     | Reconheça expressões comuns e termos informais usados em texto.         |
|Diferenciar entre palavras semelhantes     | Encontre o uso correto entre palavras que parecem semelhantes, mas que diferem no significado (por exemplo, "Veja" e "Sea")        |
|Suporte de marca, título e uso popular     | Reconheça novas marcas, títulos e outras expressões populares à medida que elas surgirem |

## <a name="workflow"></a>Fluxo de Trabalho

A API de Verificação Ortográfica do Bing é fácil de chamar de qualquer linguagem de programação que possa fazer solicitações HTTP e analisar respostas JSON. O serviço pode ser acessado usando a API REST ou os SDKs de Verificação Ortográfica do Bing. 

1. Crie uma [conta de API dos Serviços Cognitivos](../cognitive-services-apis-create-account.md) com acesso às APIs de Pesquisa do Bing. Se você não tiver uma assinatura do Azure, poderá criar uma conta gratuita. 
2. Envie uma solicitação para o API de Pesquisa na Web do Bing.
3. Analisar a resposta JSON

## <a name="next-steps"></a>Passos seguintes

Primeiro, experimente a [demonstração interativa](https://azure.microsoft.com/services/cognitive-services/spell-check/) da API de pesquisa verificação ortográfica do Bing para ver como você pode verificar rapidamente uma variedade de textos.

Quando estiver pronto para chamar a API, crie uma [conta dos serviços cognitivos da API](../../cognitive-services/cognitive-services-apis-create-account.md). Se não tiver uma subscrição do Azure, pode [criar uma conta](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) gratuita.

Você também pode visitar a [página do hub de API pesquisa do Bing](../bing-web-search/search-the-web.md) para explorar as outras APIs disponíveis.