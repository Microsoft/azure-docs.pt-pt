---
title: Modelo de candidatura Azure
titleSuffix: Microsoft identity platform
description: Conheça o processo de registo da sua aplicação para que possa integrar-se na plataforma de identidade da Microsoft (v2.0).
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
ms.openlocfilehash: 774822eae5ab327f57da3eca4499ca14d3e0c7a1
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82584314"
---
# <a name="application-model"></a>Modelo de aplicação

As aplicações podem inscrever-se nos próprios utilizadores ou delegar o sessão a um fornecedor de identidade. Este tópico discute os passos necessários para registar uma aplicação com a plataforma de identidade da Microsoft.

## <a name="registering-an-application"></a>Registar uma aplicação

Para que um fornecedor de identidade saiba que um utilizador tem acesso a uma determinada aplicação, tanto o utilizador como a aplicação devem ser registados junto do fornecedor de identidade. Ao registar a sua aplicação com o Azure AD, está a fornecer uma configuração de identidade para a sua aplicação que lhe permite integrar-se com a plataforma de identidade da Microsoft. Registar a aplicação também lhe permite:

* Personalize a marca da sua aplicação no diálogo de início de sessão. Isto é importante porque esta é a primeira experiência que um utilizador terá com a sua aplicação.
* Decida se pretende deixar que os utilizadores assinem apenas se pertencerem à sua organização. Este é um único pedido de inquilino. Ou permitir que os utilizadores assinem usando qualquer trabalho ou conta escolar. Esta é uma aplicação multi-inquilino. Também pode permitir contas pessoais da Microsoft, ou uma conta social do LinkedIn, Google, e assim por diante.
* Solicite permissões de âmbito de pedidos. Por exemplo, pode solicitar o âmbito "user.read", que concede permissão para ler o perfil do utilizador inscrito.
* Defina os âmbitos que definem o acesso à sua Web API. Normalmente, quando uma aplicação quer aceder à sua API, terá de solicitar permissões aos âmbitos que define.
* Partilhe um segredo com a plataforma de identidade da Microsoft que comprove a identidade da aplicação.  Isto é relevante no caso em que a app é uma aplicação confidencial do cliente. Uma aplicação confidencial do cliente é uma aplicação que pode manter credenciais de forma segura. Exigem um servidor de backend de confiança para armazenar as credenciais.

Uma vez registada, a aplicação receberá um identificador único que a aplicação partilha com a plataforma de identidade da Microsoft quando solicitar tokens. Se a aplicação for uma [aplicação confidencial](developer-glossary.md#client-application)do cliente, também partilhará o segredo ou a chave pública, dependendo se foram usados certificados ou segredos.

A plataforma de identidade da Microsoft representa aplicações utilizando um modelo que cumpre duas funções principais:

* Identifique a app pelos protocolos de autenticação que suporta
* Fornecer todos os identificadores, URLs, segredos e informações relacionadas que são necessárias para autenticar

Plataforma de identidade da Microsoft:

* Detém todos os dados necessários para suportar a autenticação em tempo de execução
* Detém todos os dados para decidir quais os recursos que uma app pode precisar de aceder, e em que circunstâncias um determinado pedido deve ser cumprido
* Fornece infraestrutura para implementação de oprovisionamento de aplicações dentro do inquilino do desenvolvedor de aplicações, e a qualquer outro inquilino da AD Azure
* Lida com o consentimento do utilizador durante o tempo de pedido simbólico e facilita o fornecimento dinâmico de aplicações entre inquilinos

**O consentimento** é o processo de um proprietário de recursos que concede autorização para um pedido de cliente para aceder a recursos protegidos, sob permissões específicas, em nome do proprietário do recurso. Plataforma de identidade da Microsoft:

* Permite aos utilizadores e administradores conceder ou negar dinamicamente o consentimento da aplicação para aceder a recursos em seu nome.
* Por fim, permite aos administradores decidir que aplicações estão autorizados a fazer, que utilizadores podem utilizar aplicações específicas e de que forma são acedidos os recursos de diretório.

## <a name="multi-tenant-apps"></a>Aplicações multi-inquilino

Na plataforma de identidade da Microsoft, um objeto de [aplicação](developer-glossary.md#application-object) descreve uma aplicação. No momento da implementação, a plataforma de identidade da Microsoft utiliza o objeto de aplicação como um plano para criar um diretor de [serviço](developer-glossary.md#service-principal-object), o que representa uma instância concreta de uma aplicação dentro de um diretório ou inquilino. O diretor de serviço define o que a app pode realmente fazer num diretório de destino específico, que pode usá-la, a que recursos tem acesso, e assim por diante. A plataforma de identidade da Microsoft cria um principal de serviço a partir de um objeto de aplicação através [do consentimento](developer-glossary.md#consent).

O diagrama seguinte mostra um fluxo simplificado de plataforma de identidade da Microsoft impulsionado pelo consentimento. Mostra dois inquilinos: *A* e *B.*

* *Inquilino A* é o dono do requerimento.
* *O inquilino B* está a instantaneamente a aplicação através de um diretor de serviço.

![Fluxo de aprovisionamento simplificado orientado por consentimento](./media/authentication-scenarios/simplified-provisioning-flow-consent-driven.svg)

Neste fluxo de aprovisionamento:

1. Um utilizador do inquilino B tenta iniciar sessão com a app, o ponto final de autorização solicita um sinal para a aplicação.
1. As credenciais do utilizador são adquiridas e verificadas para autenticação.
1. O utilizador é solicitado a dar consentimento para que a app obtenha acesso ao inquilino B.
1. A plataforma de identidade da Microsoft utiliza o objeto de aplicação no inquilino A como uma planta para criar um diretor de serviço no inquilino B.
1. O utilizador recebe o token solicitado.

Pode repetir este processo para inquilinos adicionais. Inquilino A mantém a planta para a app (objeto de aplicação). Os utilizadores e administradores de todos os outros inquilinos onde a app é dada o consentimento mantêm o controlo sobre o que a aplicação é permitida a fazer através do objeto principal de serviço correspondente em cada inquilino. Para mais informações, consulte [os objetos principais da Aplicação e do serviço na plataforma de identidade da Microsoft](app-objects-and-service-principals.md).

## <a name="next-steps"></a>Passos seguintes

Para outros tópicos que abranjam a autenticação e os fundamentos de autorização:

* Consulte a [Autenticação vs. autorização](authentication-vs-authorization.md) para conhecer os conceitos básicos de autenticação e autorização na plataforma de identidade da Microsoft.
* Consulte [fichas](security-tokens.md) de segurança para saber como os tokens de acesso, fichas de atualização e fichas de identificação são usados na autenticação e autorização.
* Consulte o [fluxo de entrada de apps](app-sign-in-flow.md) para saber sobre o fluxo de entrada de aplicações web, desktop e mobile na plataforma de identidade da Microsoft.

Para saber mais sobre o modelo de aplicação:

* Veja como e por que as [aplicações são adicionadas ao Azure AD](active-directory-how-applications-are-added.md) para obter mais informações sobre objetos de aplicação e diretores de serviço na plataforma de identidade da Microsoft.
* Consulte [o Tenancy no Azure Ative Directory](single-and-multi-tenant-apps.md) para obter mais informações sobre aplicações de inquilino único e aplicações multi-inquilinos.
* Consulte a [documentação do Azure Ative Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c) para obter mais informações sobre como o Azure AD também fornece o Azure Ative Directory B2C para que as organizações possam inscrever utilizadores, normalmente clientes, usando identidades sociais como uma conta google.
