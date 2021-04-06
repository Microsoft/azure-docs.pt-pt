---
title: Modelo de aplicação | Rio Azure
titleSuffix: Microsoft identity platform
description: Conheça o processo de registo da sua aplicação para que possa integrar-se na plataforma de identidade da Microsoft.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/28/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 86543b961698e736b2211553b0dca367b28158ef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98795662"
---
# <a name="application-model"></a>Modelo de aplicação

As aplicações podem iniciar saúde nos próprios utilizadores ou delegar o sismo num fornecedor de identidade. Este artigo discute as etapas que são necessárias para registar uma aplicação com a plataforma de identidade da Microsoft.

## <a name="register-an-application"></a>Registar uma aplicação

Para que um fornecedor de identidade saiba que um utilizador tem acesso a uma determinada aplicação, tanto o utilizador como a aplicação devem ser registados junto do fornecedor de identidade. Quando regista a sua aplicação no Azure Ative Directory (Azure AD), está a fornecer uma configuração de identidade para a sua aplicação que lhe permite integrar-se na plataforma de identidade da Microsoft. O registo da aplicação também lhe permite:

* Personalize a marcação da sua aplicação na caixa de diálogo de entrada. Esta marca é importante porque a inscrição é a primeira experiência que um utilizador terá com a sua aplicação.
* Decida se deseja permitir que os utilizadores só entrem se pertencerem à sua organização. Esta arquitetura é conhecida como uma aplicação de inquilino único. Ou, você pode permitir que os utilizadores se inscrevam usando qualquer conta de trabalho ou escola, que é conhecida como uma aplicação multi-inquilino. Também pode permitir contas pessoais da Microsoft ou uma conta social do LinkedIn, Google, e assim por diante.
* Solicite permissões de âmbito. Por exemplo, pode solicitar o âmbito "user.read", que permite ler o perfil do utilizador inscrito.
* Defina âmbitos que definem o acesso à sua API web. Normalmente, quando uma aplicação quer aceder à sua API, terá de pedir permissões aos âmbitos que define.
* Partilhe um segredo com a plataforma de identidade da Microsoft que comprovam a identidade da aplicação. A utilização de um segredo é relevante no caso de a app ser uma aplicação confidencial do cliente. Um pedido de cliente confidencial é uma aplicação que pode manter as credenciais de forma segura. Um servidor back-end de confiança é necessário para armazenar as credenciais.

Depois de a aplicação estar registada, é dado um identificador único que partilha com a plataforma de identidade da Microsoft quando solicita fichas. Se a aplicação for uma [aplicação confidencial do cliente,](developer-glossary.md#client-application)também partilhará o segredo ou a chave pública dependendo se foram usados certificados ou segredos.

A plataforma de identidade da Microsoft representa aplicações utilizando um modelo que cumpre duas funções principais:

* Identifique a aplicação pelos protocolos de autenticação que suporta.
* Forneça todos os identificadores, URLs, segredos e informações relacionadas que são necessários para autenticar.

A plataforma de identidade da Microsoft:

* Detém todos os dados necessários para suportar a autenticação em tempo de execução.
* Detém todos os dados para decidir quais os recursos a que uma aplicação pode precisar de aceder e em que circunstâncias um determinado pedido deve ser preenchido.
* Fornece infraestruturas para implementar o provisionamento de aplicativos dentro do inquilino do desenvolvedor de aplicações, e para qualquer outro inquilino AZure AD.
* Lida com o consentimento do utilizador durante o tempo de pedido simbólico e facilita o fornecimento dinâmico de aplicações entre os inquilinos.

*Consentimento* é o processo de um titular de recursos que concede autorização para um pedido de cliente para aceder a recursos protegidos, sob permissões específicas, em nome do proprietário do recurso. A plataforma de identidade da Microsoft permite:

* Os utilizadores e administradores concedem ou negam o consentimento para que a app aceda aos recursos em seu nome.
* Os administradores para decidir em última análise quais as aplicações que são permitidas e quais os utilizadores que podem usar aplicações específicas, e como os recursos do diretório são acedidos.

## <a name="multi-tenant-apps"></a>Aplicações multi-inquilino

Na plataforma de identidade da Microsoft, um [objeto de aplicação](developer-glossary.md#application-object) descreve uma aplicação. No momento da implementação, a plataforma de identidade da Microsoft utiliza o objeto de aplicação como um plano para criar um [principal serviço](developer-glossary.md#service-principal-object), o que representa uma instância concreta de uma aplicação dentro de um diretório ou inquilino. O diretor de serviço define o que a app pode realmente fazer num diretório-alvo específico, quem pode usá-lo, quais os recursos a que tem acesso, e assim por diante. A plataforma de identidade da Microsoft cria um principal de serviço a partir de um objeto de aplicação através do [consentimento.](developer-glossary.md#consent)

O diagrama seguinte mostra um fluxo de provisão de plataforma de identidade da Microsoft simplificado impulsionado pelo consentimento. Mostra dois inquilinos: *A* e *B.*

* *O inquilino A* é dono do requerimento.
* *O inquilino B* está a instantanear o pedido através de um diretor de serviço.

![Diagrama que mostra um fluxo de provisão simplificado impulsionado pelo consentimento.](./media/authentication-scenarios/simplified-provisioning-flow-consent-driven.svg)

Neste fluxo de aprovisionamento:

1. Um utilizador do inquilino B tenta entrar com a app. O ponto final de autorização solicita um sinal para o pedido.
1. As credenciais de utilizador são adquiridas e verificadas para autenticação.
1. O utilizador é solicitado a fornecer consentimento para que a app obtenha acesso ao inquilino B.
1. A plataforma de identidade da Microsoft usa o objeto de aplicação no inquilino A como um plano para criar um principal serviço no inquilino B.
1. O utilizador recebe o token solicitado.

Pode repetir este processo para mais inquilinos. O inquilino A mantém a planta para a aplicação (objeto de aplicação). Os utilizadores e administradores de todos os outros inquilinos onde a app é dada consentimento controlam o que a aplicação é permitida através do objeto principal de serviço correspondente em cada inquilino. Para obter mais informações, consulte [os objetos principais de aplicação e serviço na plataforma de identidade da Microsoft.](app-objects-and-service-principals.md)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a autenticação e autorização na plataforma de identidade da Microsoft, consulte os seguintes artigos:

* Para conhecer os conceitos básicos de autenticação e autorização, consulte [autenticação vs. autorização.](authentication-vs-authorization.md)
* Para saber como os tokens de acesso, tokens de atualização e fichas de identificação são usados na autenticação e autorização, consulte [fichas de segurança.](security-tokens.md)
* Para saber mais sobre o fluxo de entrada de web, desktop e aplicativos móveis, consulte o [fluxo de entrada de app](app-sign-in-flow.md).

Para obter mais informações sobre o modelo de aplicação, consulte os seguintes artigos:

* Para obter mais informações sobre objetos de aplicações e principais de serviço na plataforma de identidade da Microsoft, consulte [como e por que as aplicações são adicionadas ao Azure AD](active-directory-how-applications-are-added.md).
* Para obter mais informações sobre aplicações de inquilinos individuais e aplicativos multi-inquilinos, consulte [Tenancy in Azure Ative Directory](single-and-multi-tenant-apps.md).
* Para obter mais informações sobre como a Azure AD também fornece a Azure Ative Directory B2C para que as organizações possam assinar nos utilizadores, normalmente clientes, utilizando identidades sociais como uma conta google, consulte [a documentação do Azure Ative Directory B2C.](../../active-directory-b2c/index.yml)