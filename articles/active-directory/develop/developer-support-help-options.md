---
title: Opções de suporte e ajuda para programadores de aplicações DaD Azure  Microsoft Docs
description: Saiba como obter ajuda e suporte para questões e problemas relacionados com o desenvolvimento ao criar uma aplicação que se integre com identidades da Microsoft (Azure Ative Directory e conta Microsoft)
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/23/2019
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: 89bf49fb44d8575b251a0b33698bc4ce8425cc2b
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77160972"
---
# <a name="support-and-help-options-for-developers"></a>Opções de suporte e ajuda para programadores

Se está apenas a começar a integrar-se com o Azure Ative Directory (Azure AD), identidades da Microsoft ou Microsoft Graph API, ou quando está a implementar uma nova funcionalidade na sua aplicação, há momentos em que precisa de obter ajuda da comunidade ou entender o opções de suporte que tem como desenvolvedor. Este artigo ajuda-o a compreender estas opções, incluindo:

> [!div class="checklist"]
> * Como pesquisar se a sua pergunta não foi respondida pela comunidade, ou se uma documentação existente para a funcionalidade que está a tentar implementar já existe
> * Em alguns casos, você só quer usar as nossas ferramentas de suporte para ajudá-lo a depurar um problema específico
> * Se não encontrar a resposta de que precisa, talvez queira fazer uma pergunta sobre *Stack Overflow*
> * Se encontrar um problema com uma das nossas bibliotecas de autenticação, levante um problema *gitHub*
> * Finalmente, se precisar de falar com alguém, talvez queira abrir um pedido de apoio.

## <a name="search"></a>Pesquisa

Se tiver uma pergunta relacionada com o desenvolvimento, poderá encontrar a resposta na documentação, [amostras gitHub](https://github.com/azure-samples)ou respostas às perguntas do [Stack Overflow.](https://www.stackoverflow.com)

### <a name="scoped-search"></a>Pesquisa com escopo

Para obter resultados mais rápidos, procure o Stack Overflow, a documentação e as amostras de código utilizando a seguinte consulta no seu motor de pesquisa favorito:

```
{Your Search Terms} (site:stackoverflow.com OR site:docs.microsoft.com OR site:github.com/azure-samples OR site:cloudidentity.com OR site:developer.microsoft.com/graph)
```

Onde *{Os seus termos* de pesquisa} correspondem às suas palavras-chave de pesquisa.

## <a name="use-the-development-support-tools"></a>Utilize as ferramentas de apoio ao desenvolvimento

| Ferramenta  | Descrição  |
|---------|---------|
| [jwt.ms](https://jwt.ms) | Colhe um ID ou um token de acesso para descodificar os nomes e valores das reclamações. |
| [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)| Ferramenta que permite fazer pedidos e ver respostas contra a API do Microsoft Graph. |

## <a name="post-a-question-to-stack-overflow"></a>Poste uma pergunta para Stack Overflow

Stack Overflow é o canal preferido para perguntas relacionadas ao desenvolvimento. Aqui, membros da comunidade de desenvolvedores e membros da equipa da Microsoft estão diretamente envolvidos em ajudá-lo a resolver os seus problemas.

Se não encontrar uma resposta à sua pergunta através de pesquisa, envie uma nova pergunta ao Stack Overflow. Use uma das seguintes tags ao fazer perguntas para ajudar a comunidade a identificar e responder mais rapidamente à sua pergunta:

|Componente/área  | Etiquetas |
|---------|---------|
| Biblioteca ADAL | [[adal]](https://stackoverflow.com/questions/tagged/adal) |
| Biblioteca MSAL     | [[msal]](https://stackoverflow.com/questions/tagged/msal) |
| Middleware OWIN  | [[azure-active-directório]](https://stackoverflow.com/questions/tagged/azure-active-directory) |
| [Azure B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)  | [[azure-ad-b2b]](https://stackoverflow.com/questions/tagged/azure-ad-b2b) |
| [Azure B2C](https://azure.microsoft.com/services/active-directory-b2c/)  | [[azure-ad-b2c]](https://stackoverflow.com/questions/tagged/azure-ad-b2c) |
| [Microsoft Graph API](https://developer.microsoft.com/graph/) | [[microsoft-graph]](https://stackoverflow.com/questions/tagged/microsoft-graph) |
| Qualquer outra área relacionada com tópicos de autenticação ou autorização | [[azure-active-directório]](https://stackoverflow.com/questions/tagged/azure-active-directory) |

Os seguintes posts do Stack Overflow contêm dicas sobre como fazer perguntas e como adicionar código fonte. Siga estas orientações para aumentar as possibilidades de os membros da comunidade avaliarem e responderem rapidamente à sua pergunta:

* [Como faço uma boa pergunta?](https://stackoverflow.com/help/how-to-ask)
* [Como criar um exemplo mínimo, completo e verificável](https://stackoverflow.com/help/mcve)

## <a name="create-a-github-issue"></a>Criar um problema do GitHub

Se encontrar um bug ou problema relacionado com as nossas bibliotecas, levante um problema nos nossos repositórios GitHub. Como as nossas bibliotecas são de código aberto, também pode apresentar um pedido de pull.

Para obter uma lista de bibliotecas e seus repositórios GitHub, consulte o seguinte:

* [Bibliotecas da Biblioteca de Autenticação de Diretórios Ativos azure (ADAL)](../azuread-dev/active-directory-authentication-libraries.md) e repositórios GitHub
* [Bibliotecas da Biblioteca de Autenticação da Microsoft (MSAL)](reference-v2-libraries.md) e repositórios GitHub

## <a name="open-a-support-request"></a>Abra um pedido de apoio

Se precisar esquelhá-lo com alguém, pode abrir um pedido de apoio. Se é cliente Azure, existem várias opções de suporte disponíveis. Para comparar planos, consulte [esta página.](https://azure.microsoft.com/support/plans/) O suporte ao programador também está disponível para os clientes Azure. Para obter informações sobre como adquirir planos de suporte do Desenvolvedor, consulte [esta página](https://azure.microsoft.com/support/plans/developer/).

* Se já tem um Plano de Apoio Azure, [abra um pedido de apoio aqui](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

* Se não for um cliente Azure, também pode abrir um pedido de suporte com a Microsoft através do [nosso suporte comercial.](https://support.microsoft.com/en-us/gp/contactus81?Audience=Commercial)

Também pode tentar um [agente virtual](https://support.microsoft.com/contactus/?ws=support) para obter apoio ou fazer perguntas.
