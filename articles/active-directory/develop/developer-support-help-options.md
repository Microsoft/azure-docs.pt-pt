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
ms.openlocfilehash: ecbb7ec503011d78c2f83a76cea921e1cf438ac1
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102215107"
---
# <a name="support-and-help-options-for-developers"></a>Opções de suporte e ajuda para programadores

Se está apenas a começar a integrar-se com o Azure Ative Directory (Azure AD), as identidades da Microsoft ou a Microsoft Graph API, ou quando está a implementar uma nova funcionalidade na sua aplicação, há momentos em que precisa de obter ajuda da comunidade ou de compreender as opções de suporte que tem enquanto desenvolvedor. Este artigo ajuda-o a compreender estas opções, incluindo:

> [!div class="checklist"]
> * Como pesquisar se a sua pergunta não foi respondida pela comunidade, ou se uma documentação existente para a funcionalidade que está a tentar implementar já existe
> * Em alguns casos, você só quer usar as nossas ferramentas de apoio para ajudá-lo a depurar um problema específico
> * Se não encontrar a resposta de que precisa, talvez queira fazer uma pergunta no *Microsoft Q&A*
> * Se encontrar um problema com uma das nossas bibliotecas de autenticação, levante um problema *do GitHub*
> * Finalmente, se precisar de falar com alguém, talvez queira abrir um pedido de apoio.

## <a name="search"></a>Pesquisar

Se tiver uma pergunta relacionada com o desenvolvimento, poderá encontrar a resposta na documentação, [amostras do GitHub](https://github.com/azure-samples)ou respostas às perguntas [do Microsoft Q&A.](/answers/products/)

### <a name="scoped-search"></a>Pesquisa de âmbito


Para obter resultados mais rápidos, coloque a sua pesquisa no [Microsoft Q&A](https://docs.microsoft.com/answers/products/) a documentação e as amostras de código utilizando a seguinte consulta no seu motor de pesquisa favorito:

Para obter resultados mais rápidos, coloque a sua pesquisa no [Microsoft Q&A](/answers/products/)a documentação e as amostras de código utilizando a seguinte consulta no seu motor de pesquisa favorito:


```
{Your Search Terms} (site:http://www.docs.microsoft.com/answers/products/ OR site:docs.microsoft.com OR site:github.com/azure-samples OR site:cloudidentity.com OR site:developer.microsoft.com/graph)
```

Onde *{Os seus Termos de Pesquisa}* correspondem às suas palavras-chave de pesquisa.

## <a name="use-the-development-support-tools"></a>Utilize as ferramentas de apoio ao desenvolvimento

| Ferramenta  | Descrição  |
|---------|---------|
| [jwt.ms](https://jwt.ms) | Cole um ID ou ficha de acesso para descodificar os nomes e valores das reclamações. |
| [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)| Ferramenta que permite fazer pedidos e ver respostas contra a API do Gráfico microsoft. |

## <a name="post-a-question-to-microsoft-qa"></a>Publique uma pergunta ao Microsoft Q&A

[Microsoft Q&A](/answers/products/) é o canal preferido para questões relacionadas com o desenvolvimento. Aqui, membros da comunidade de desenvolvedores e membros da equipa da Microsoft estão diretamente envolvidos em ajudá-lo a resolver os seus problemas.

Se não conseguir encontrar uma resposta à sua pergunta através da pesquisa, envie uma nova pergunta ao [Microsoft Q&A](/answers/products/) . Use uma das seguintes etiquetas ao fazer perguntas para ajudar a comunidade a identificar e responder mais rapidamente à sua pergunta:

|Componente/área  | Etiquetas |
|---------|---------|
| Biblioteca ADAL | [[adal]](/answers/topics/azure-ad-adal-deprecation.html) |
| Biblioteca MSAL     | [[msal]](/answers/topics/azure-ad-msal.html) |
| Middleware OWIN  | [[azure-active-directy]](/answers/topics/azure-active-directory.html) |
| [Azure B2B](../external-identities/what-is-b2b.md)  | [[azure-ad-b2b]](/answers/topics/azure-ad-b2b.html) |
| [Azure B2C](https://azure.microsoft.com/services/active-directory-b2c/)  | [[azure-ad-b2c]](/answers/topics/azure-ad-b2c.html) |
| [Microsoft Graph API](https://developer.microsoft.com/graph/) | [[azure-ad-graph]](/answers/topics/azure-ad-graph.html) |
| Qualquer outra área relacionada com temas de autenticação ou autorização | [[azure-active-directy]](/answers/topics/azure-active-directory.html) |

As seguintes publicações do [Microsoft Q&A](/answers/products/) contêm dicas sobre como fazer perguntas e como adicionar código fonte. Siga estas orientações para aumentar as possibilidades de os membros da comunidade avaliarem e responderem rapidamente à sua pergunta:

* [Como faço uma boa pergunta?](/answers/articles/24951/how-to-write-a-quality-question.html)
* [Como criar um exemplo mínimo, completo e verificável](/answers/articles/24907/how-to-write-a-quality-answer.html)

## <a name="create-a-github-issue"></a>Criar um problema do GitHub

Se encontrar um bug ou problema relacionado com as nossas bibliotecas, levante um problema nos nossos repositórios GitHub. Como as nossas bibliotecas são de código aberto, também pode submeter um pedido de retirada.

Para obter uma lista de bibliotecas e seus repositórios GitHub, consulte o seguinte:

* [Bibliotecas de autenticação de diretórios ativos Azure (ADAL)](../azuread-dev/active-directory-authentication-libraries.md) e repositórios Do GitHub
* [Bibliotecas da Microsoft Authentication Library (MSAL)](reference-v2-libraries.md) e repositórios GitHub

## <a name="open-a-support-request"></a>Abrir um pedido de apoio

Se precisar de falar com alguém, pode abrir um pedido de apoio. Se é cliente Azure, existem várias opções de suporte disponíveis. Para comparar planos, consulte [esta página.](https://azure.microsoft.com/support/plans/) O suporte do desenvolvedor também está disponível para os clientes Azure. Para obter informações sobre como comprar planos de suporte do Desenvolvedor, consulte [esta página](https://azure.microsoft.com/support/plans/developer/).

* Se já tem um Plano de Apoio Azure, [abra um pedido de apoio aqui](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

* Se não for um cliente Azure, também pode abrir um pedido de suporte com a Microsoft através do [nosso suporte comercial.](https://support.serviceshub.microsoft.com/supportforbusiness)

Também pode tentar um [agente virtual](https://support.microsoft.com/contactus/?ws=support) para obter suporte ou fazer perguntas.