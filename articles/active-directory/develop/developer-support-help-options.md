---
title: Opções de suporte e ajuda para os programadores de aplicações do Azure AD | Documentos da Microsoft
description: Saber como obter ajuda e suporte para perguntas relacionadas com desenvolvimento e problemas durante a criação de aplicativos que se integram com identidades da Microsoft (conta do Azure Active Directory e a Microsoft)
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
ms.openlocfilehash: efa14e88eeb8ab43f998a32aaa0c14220acab03a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66235326"
---
# <a name="support-and-help-options-for-developers"></a>Opções de ajuda e suporte para programadores

Se estiver começando a integrar com o Azure Active Directory (Azure AD), as identidades da Microsoft ou Microsoft Graph API, ou ao implementar um novo recurso ao seu aplicativo, há alturas em que precisa para obter ajuda da Comunidade ou compreender o Opções de suporte que já têm como um desenvolvedor. Este artigo ajuda-o a compreender estas opções, incluindo:

> [!div class="checklist"]
> * Como pesquisar se a sua pergunta ainda não foram respondida pela Comunidade, ou se uma documentação existente para a funcionalidade que está a tentar implementar já existe
> * Em alguns casos, apenas desejar usar nossas ferramentas de suporte para o ajudar a depurar um problema específico
> * Se não conseguir encontrar a resposta que precisa, pode querer fazer uma pergunta sobre *Stack Overflow*
> * Se encontrar um problema com uma das nossas bibliotecas de autenticação, emitir uma *GitHub* problema
> * Por fim, se precisar de falar com alguém, poderá abrir um pedido de suporte

## <a name="search"></a>Pesquisa

Se tiver uma pergunta relacionados ao desenvolvimento, talvez consiga encontrar a resposta na documentação do [exemplos do GitHub](https://github.com/azure-samples), ou respostas para [Stack Overflow](https://www.stackoverflow.com) perguntas.

### <a name="scoped-search"></a>Procura de âmbito

Para obter resultados mais rápidos, definir o âmbito da pesquisa para o Stack Overflow, a documentação e exemplos de código com a seguinte consulta no seu mecanismo de pesquisa favorito:

```
{Your Search Terms} (site:stackoverflow.com OR site:docs.microsoft.com OR site:github.com/azure-samples OR site:cloudidentity.com OR site:developer.microsoft.com/graph)
```

Em que *{Your termos de pesquisa}* correspondem às suas palavras-chave de pesquisa.

## <a name="use-the-development-support-tools"></a>Utilize as ferramentas de suporte de desenvolvimento

| Ferramenta  | Descrição  |
|---------|---------|
| [jwt.ms](https://jwt.ms) | Colar um ID ou o token de acesso para decodificar os valores e nomes de afirmações. |
| [Do Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer)| Ferramenta que lhe permite fazer pedidos e ver respostas com o Microsoft Graph API. |

## <a name="post-a-question-to-stack-overflow"></a>Publique uma pergunta do Stack Overflow

Estouro de pilha é o canal preferido para questões relacionadas com desenvolvimento. Aqui, os membros da Comunidade de desenvolvedores e os membros da Equipe Microsoft estão diretamente envolvidos nas ajudá-lo a resolver os seus problemas.

Se não conseguir encontrar uma resposta à sua pergunta através da pesquisa, submeta uma nova pergunta para Stack Overflow. Utilize uma das etiquetas seguintes quando fazer perguntas para ajudar a Comunidade a identificar e responder mais rapidamente a sua pergunta:

|Componente/área  | Tags |
|---------|---------|
| Biblioteca ADAL | [[adal]](https://stackoverflow.com/questions/tagged/adal) |
| Biblioteca MSAL     | [[msal]](https://stackoverflow.com/questions/tagged/msal) |
| Middleware da OWIN  | [[azure-active-directory]](https://stackoverflow.com/questions/tagged/azure-active-directory) |
| [Azure B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)  | [[azure-ad-b2b]](https://stackoverflow.com/questions/tagged/azure-ad-b2b) |
| [Azure B2C](https://azure.microsoft.com/services/active-directory-b2c/)  | [[azure-ad-b2c]](https://stackoverflow.com/questions/tagged/azure-ad-b2c) |
| [Microsoft Graph API](https://developer.microsoft.com/graph/) | [[microsoft-graph]](https://stackoverflow.com/questions/tagged/microsoft-graph) |
| Qualquer outra área relacionadas com a autenticação ou autorização tópicos | [[azure-active-directory]](https://stackoverflow.com/questions/tagged/azure-active-directory) |

As seguintes mensagens de Stack Overflow contêm dicas sobre como fazer perguntas e como adicionar o código-fonte. Siga estas diretrizes para aumentar as hipóteses de membros da Comunidade avaliar e responder rapidamente a sua pergunta:

* [Como posso fazer uma boa pergunta](https://stackoverflow.com/help/how-to-ask)
* [Como criar um exemplo de mínimo, completo e verificável](https://stackoverflow.com/help/mcve)

## <a name="create-a-github-issue"></a>Criar um problema do GitHub

Se encontrar um erro ou problema relacionado com a nossas bibliotecas, gerar um problema nos nossos repositórios de GitHub. Como nossas bibliotecas de código-fonte aberto, também pode submeter um pedido pull.

Para obter uma lista de bibliotecas e seus repositórios do GitHub, consulte o seguinte:

* [ADAL](active-directory-authentication-libraries.md) bibliotecas e os repositórios do GitHub
* [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) [msal](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs/README.md), [MSAL. Android](https://github.com/AzureAD/microsoft-authentication-library-for-android), e [MSAL.obj_c](https://github.com/AzureAD/microsoft-authentication-library-for-objc) bibliotecas e os repositórios do GitHub

## <a name="open-a-support-request"></a>Abra um pedido de suporte

Se precisar de falar com alguém, pode abrir um pedido de suporte. Se for um cliente do Azure, existem várias opções de suporte disponíveis. Comparar planos, consulte [esta página](https://azure.microsoft.com/support/plans/). Suporte para programadores também está disponível para clientes do Azure. Para obter informações sobre como comprar planos de suporte programador, consulte [esta página](https://azure.microsoft.com/support/plans/developer/).

* Se já tiver um plano do Azure suporta, [abrir um pedido de suporte aqui](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

* Se não for um cliente do Azure, também pode abrir um pedido de suporte com a Microsoft via [nosso suporte comercial](https://support.microsoft.com/en-us/gp/contactus81?Audience=Commercial).

Também pode tentar uma [agente do virtual](https://support.microsoft.com/contactus/?ws=support) para obter suporte ou colocar questões.

### <a name="free-chat-support-for-a-limited-time"></a>Suporte de bate-papo gratuito por um período limitado

Também pode utilizar o nosso suporte de bate-papo, que é gratuito para Microsoft Partners por um período limitado. Se a sua empresa não é um Microsoft Partner, pode utilizar para inscrevê-lo gratuitamente e obter outros benefícios indo [aqui](https://partners.microsoft.com/PartnerProgram/simplifiedenrollment.aspx).

Depois de inscrever a sua empresa, pode iniciar o pedido de bate-papo [aqui](https://aka.ms/devchat).
