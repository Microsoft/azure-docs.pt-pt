---
title: Opções de suporte e ajuda para os desenvolvedores de aplicativos do Azure AD | Microsoft Docs
description: Saiba como obter ajuda e suporte para perguntas e problemas relacionados ao desenvolvimento ao criar aplicativos que se integram às identidades da Microsoft (Azure Active Directory e conta Microsoft)
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/23/2019
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a160f78f69ed559c576147e9758c837d5ae9b77e
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72429976"
---
# <a name="support-and-help-options-for-developers"></a>Opções de suporte e ajuda para programadores

Se você estiver começando a integrar com o Azure Active Directory (Azure AD), identidades da Microsoft ou API Microsoft Graph, ou quando estiver implementando um novo recurso para seu aplicativo, há ocasiões em que você precisa obter ajuda da Comunidade ou entender o opções de suporte que você tem como desenvolvedor. Este artigo ajuda você a entender essas opções, incluindo:

> [!div class="checklist"]
> * Como Pesquisar se sua pergunta não foi respondida pela Comunidade ou se já existe uma documentação existente para o recurso que você está tentando implementar
> * Em alguns casos, você só deseja usar nossas ferramentas de suporte para ajudá-lo a depurar um problema específico
> * Se você não conseguir encontrar a resposta de que precisa, talvez queira fazer uma pergunta em *Stack Overflow*
> * Se você encontrar um problema com uma de nossas bibliotecas de autenticação, gere um problema do *GitHub*
> * Por fim, se você precisar falar com alguém, talvez queira abrir uma solicitação de suporte

## <a name="search"></a>Procurar

Se você tiver uma pergunta relacionada ao desenvolvimento, poderá encontrar a resposta na documentação, nos [exemplos do GitHub](https://github.com/azure-samples)ou nas respostas para [Stack Overflow](https://www.stackoverflow.com) perguntas.

### <a name="scoped-search"></a>Pesquisa com escopo

Para obter resultados mais rápidos, use o escopo da pesquisa para Stack Overflow, a documentação e os exemplos de código usando a seguinte consulta em seu mecanismo de pesquisa favorito:

```
{Your Search Terms} (site:stackoverflow.com OR site:docs.microsoft.com OR site:github.com/azure-samples OR site:cloudidentity.com OR site:developer.microsoft.com/graph)
```

Em que *{os termos de pesquisa}* correspondem às palavras-chave de pesquisa.

## <a name="use-the-development-support-tools"></a>Usar as ferramentas de suporte de desenvolvimento

| Ferramenta  | Descrição  |
|---------|---------|
| [jwt.ms](https://jwt.ms) | Cole uma ID ou um token de acesso para decodificar os nomes e valores das declarações. |
| [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)| Ferramenta que permite que você faça solicitações e veja respostas em relação à API de Microsoft Graph. |

## <a name="post-a-question-to-stack-overflow"></a>Poste uma pergunta para Stack Overflow

Stack Overflow é o canal preferido para perguntas relacionadas ao desenvolvimento. Aqui, os membros da comunidade de desenvolvedores e dos membros da equipe da Microsoft estão diretamente envolvidos em ajudá-lo a resolver seus problemas.

Se você não encontrar uma resposta à sua pergunta por meio de pesquisa, envie uma nova pergunta para Stack Overflow. Use uma das seguintes marcas ao fazer perguntas para ajudar a Comunidade a identificar e responder sua pergunta mais rapidamente:

|Componente/área  | Etiquetas |
|---------|---------|
| Biblioteca ADAL | [Adal](https://stackoverflow.com/questions/tagged/adal) |
| Biblioteca MSAL     | [MSAL](https://stackoverflow.com/questions/tagged/msal) |
| Middleware OWIN  | [[Azure-Active-Directory]](https://stackoverflow.com/questions/tagged/azure-active-directory) |
| [B2B do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)  | [[Azure-ad-B2B]](https://stackoverflow.com/questions/tagged/azure-ad-b2b) |
| [B2C do Azure](https://azure.microsoft.com/services/active-directory-b2c/)  | [[Azure-ad-B2C]](https://stackoverflow.com/questions/tagged/azure-ad-b2c) |
| [Microsoft Graph API](https://developer.microsoft.com/graph/) | [[Microsoft-Graph]](https://stackoverflow.com/questions/tagged/microsoft-graph) |
| Qualquer outra área relacionada a tópicos de autenticação ou autorização | [[Azure-Active-Directory]](https://stackoverflow.com/questions/tagged/azure-active-directory) |

As postagens a seguir de Stack Overflow contêm dicas sobre como fazer perguntas e como adicionar o código-fonte. Siga estas diretrizes para aumentar as chances de os membros da Comunidade avaliarem e responderem rapidamente à sua pergunta:

* [Como fazer fazer uma boa pergunta](https://stackoverflow.com/help/how-to-ask)
* [Como criar um exemplo mínimo, completo e verificável](https://stackoverflow.com/help/mcve)

## <a name="create-a-github-issue"></a>Criar um problema do GitHub

Se você encontrar um bug ou problema relacionado às nossas bibliotecas, gere um problema em nossos repositórios do GitHub. Como nossas bibliotecas são de código aberto, você também pode enviar uma solicitação de pull.

Para obter uma lista de bibliotecas e seus repositórios GitHub, consulte o seguinte:

* Bibliotecas [Adal](active-directory-authentication-libraries.md) e repositórios github
* [MSAL.net](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) [MSAL. js](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs/README.md), [MSAL. ](https://github.com/AzureAD/microsoft-authentication-library-for-android)Bibliotecas do Android e [MSAL. obj _C](https://github.com/AzureAD/microsoft-authentication-library-for-objc) e repositórios github

## <a name="open-a-support-request"></a>Abrir uma solicitação de suporte

Se você precisar falar com alguém, poderá abrir uma solicitação de suporte. Se você for um cliente do Azure, há várias opções de suporte disponíveis. Para comparar planos, consulte [esta página](https://azure.microsoft.com/support/plans/). O suporte ao desenvolvedor também está disponível para clientes do Azure. Para obter informações sobre como comprar planos de suporte do desenvolvedor, consulte [esta página](https://azure.microsoft.com/support/plans/developer/).

* Se você já tiver um plano de suporte do Azure, [abra uma solicitação de suporte aqui](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

* Se você não for um cliente do Azure, também poderá abrir uma solicitação de suporte com a Microsoft por meio de [nosso suporte comercial](https://support.microsoft.com/en-us/gp/contactus81?Audience=Commercial).

Você também pode experimentar um [agente virtual](https://support.microsoft.com/contactus/?ws=support) para obter suporte ou fazer perguntas.
