---
title: Opções de suporte e ajuda para desenvolvedores de aplicações AD Azure
description: Saiba como obter ajuda e suporte para questões e problemas relacionados com o desenvolvimento ao criar uma aplicação que se integre com identidades da Microsoft (Azure Ative Directory e conta Microsoft)
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/23/2019
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: af363bb30d6515ce969afe146c780baa4b31cd83
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88117214"
---
# <a name="support-and-help-options-for-developers"></a>Opções de suporte e ajuda para programadores

Se está apenas a começar a integrar-se com o Azure Ative Directory (Azure AD), as identidades da Microsoft ou a Microsoft Graph API, ou quando está a implementar uma nova funcionalidade na sua aplicação, há momentos em que precisa de obter ajuda da comunidade ou de compreender as opções de suporte que tem enquanto desenvolvedor. Este artigo ajuda-o a compreender estas opções, incluindo:

> [!div class="checklist"]
> * Como pesquisar se a sua pergunta não foi respondida pela comunidade, ou se uma documentação existente para a funcionalidade que está a tentar implementar já existe
> * Em alguns casos, você só quer usar as nossas ferramentas de apoio para ajudá-lo a depurar um problema específico
> * Se não encontrar a resposta de que precisa, pode querer fazer uma pergunta sobre *Stack Overflow*
> * Se encontrar um problema com uma das nossas bibliotecas de autenticação, levante um problema *do GitHub*
> * Finalmente, se precisar de falar com alguém, talvez queira abrir um pedido de apoio.

## <a name="search"></a>Pesquisa

Se tiver uma pergunta relacionada com o desenvolvimento, poderá encontrar a resposta na documentação, [amostras do GitHub](https://github.com/azure-samples)ou respostas a perguntas [do Stack Overflow.](https://www.stackoverflow.com)

### <a name="scoped-search"></a>Pesquisa de âmbito

Para obter resultados mais rápidos, procure o Stack Overflow, a documentação e as amostras de código utilizando a seguinte consulta no seu motor de busca favorito:

```
{Your Search Terms} (site:stackoverflow.com OR site:docs.microsoft.com OR site:github.com/azure-samples OR site:cloudidentity.com OR site:developer.microsoft.com/graph)
```

Onde *{Os seus Termos de Pesquisa}* correspondem às suas palavras-chave de pesquisa.

## <a name="use-the-development-support-tools"></a>Utilize as ferramentas de apoio ao desenvolvimento

| Ferramenta  | Descrição  |
|---------|---------|
| [jwt.ms](https://jwt.ms) | Cole um ID ou ficha de acesso para descodificar os nomes e valores das reclamações. |
| [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)| Ferramenta que permite fazer pedidos e ver respostas contra a API do Gráfico microsoft. |

## <a name="post-a-question-to-stack-overflow"></a>Postar uma pergunta para Stack Overflow

Stack Overflow é o canal preferido para questões relacionadas com o desenvolvimento. Aqui, membros da comunidade de desenvolvedores e membros da equipa da Microsoft estão diretamente envolvidos em ajudá-lo a resolver os seus problemas.

Se não conseguir encontrar uma resposta à sua pergunta através da pesquisa, envie uma nova pergunta ao Stack Overflow. Use uma das seguintes etiquetas ao fazer perguntas para ajudar a comunidade a identificar e responder mais rapidamente à sua pergunta:

|Componente/área  | Etiquetas |
|---------|---------|
| Biblioteca ADAL | [[adal]](https://stackoverflow.com/questions/tagged/adal) |
| Biblioteca MSAL     | [[msal]](https://stackoverflow.com/questions/tagged/msal) |
| Middleware OWIN  | [[azure-active-directy]](https://stackoverflow.com/questions/tagged/azure-active-directory) |
| [Azure B2B](../external-identities/what-is-b2b.md)  | [[azure-ad-b2b]](https://stackoverflow.com/questions/tagged/azure-ad-b2b) |
| [Azure B2C](https://azure.microsoft.com/services/active-directory-b2c/)  | [[azure-ad-b2c]](https://stackoverflow.com/questions/tagged/azure-ad-b2c) |
| [Microsoft Graph API](https://developer.microsoft.com/graph/) | [[microsoft-graph]](https://stackoverflow.com/questions/tagged/microsoft-graph) |
| Qualquer outra área relacionada com temas de autenticação ou autorização | [[azure-active-directy]](https://stackoverflow.com/questions/tagged/azure-active-directory) |

Os seguintes posts do Stack Overflow contêm dicas sobre como fazer perguntas e como adicionar código fonte. Siga estas orientações para aumentar as possibilidades de os membros da comunidade avaliarem e responderem rapidamente à sua pergunta:

* [Como faço uma boa pergunta?](https://stackoverflow.com/help/how-to-ask)
* [Como criar um exemplo mínimo, completo e verificável](https://stackoverflow.com/help/mcve)

## <a name="create-a-github-issue"></a>Criar um problema do GitHub

Se encontrar um bug ou problema relacionado com as nossas bibliotecas, levante um problema nos nossos repositórios GitHub. Como as nossas bibliotecas são de código aberto, também pode submeter um pedido de retirada.

Para obter uma lista de bibliotecas e seus repositórios GitHub, consulte o seguinte:

* [Bibliotecas de autenticação de diretórios ativos Azure (ADAL)](../azuread-dev/active-directory-authentication-libraries.md) e repositórios Do GitHub
* [Bibliotecas da Microsoft Authentication Library (MSAL)](reference-v2-libraries.md) e repositórios GitHub

## <a name="open-a-support-request"></a>Abrir um pedido de apoio

Se precisar de falar com alguém, pode abrir um pedido de apoio. Se é cliente Azure, existem várias opções de suporte disponíveis. Para comparar planos, consulte [esta página.](https://azure.microsoft.com/support/plans/) O suporte do desenvolvedor também está disponível para os clientes Azure. Para obter informações sobre como comprar planos de suporte do Desenvolvedor, consulte [esta página](https://azure.microsoft.com/support/plans/developer/).

* Se já tem um Plano de Apoio Azure, [abra um pedido de apoio aqui](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

* Se não for um cliente Azure, também pode abrir um pedido de suporte com a Microsoft através do [nosso suporte comercial.](https://support.microsoft.com/en-us/gp/contactus81?Audience=Commercial)

Também pode tentar um [agente virtual](https://support.microsoft.com/contactus/?ws=support) para obter suporte ou fazer perguntas.