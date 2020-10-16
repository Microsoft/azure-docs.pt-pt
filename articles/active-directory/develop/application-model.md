---
title: Modelo de aplicação / Rio Azure
titleSuffix: Microsoft identity platform
description: Conheça o processo de registo da sua aplicação para que possa integrar-se com a plataforma de identidade da Microsoft (v2.0).
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
ms.openlocfilehash: 5aca96a9c3bc4e8f1061f677e316565b10014ac9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88117486"
---
# <a name="application-model"></a>Modelo de aplicação

As aplicações podem iniciar saúde nos próprios utilizadores ou delegar o sismo num fornecedor de identidade. Este tópico discute os passos necessários para registar uma aplicação com a plataforma de identidade da Microsoft.

## <a name="registering-an-application"></a>Registar uma aplicação

Para que um fornecedor de identidade saiba que um utilizador tem acesso a uma determinada aplicação, tanto o utilizador como a aplicação devem ser registados junto do fornecedor de identidade. Quando regista a sua aplicação com Azure AD, está a fornecer uma configuração de identidade para a sua aplicação que lhe permite integrar-se com a plataforma de identidade da Microsoft. O registo da aplicação também lhe permite:

* Personalize a marcação da sua aplicação no diálogo de inscrição. Isto é importante porque esta é a primeira experiência que um utilizador terá com a sua aplicação.
* Decida se deseja que os utilizadores assinem apenas se pertencerem à sua organização. Este é um único pedido de inquilino. Ou permitir que os utilizadores se inscrevam usando qualquer trabalho ou conta escolar. Esta é uma aplicação multi-inquilino. Também pode permitir contas pessoais da Microsoft, ou uma conta social do LinkedIn, Google, e assim por diante.
* Solicite permissões de âmbito. Por exemplo, pode solicitar o âmbito "user.read", que permite ler o perfil do utilizador inscrito.
* Defina âmbitos que definem o acesso à sua API web. Normalmente, quando uma aplicação quer aceder à sua API, terá de pedir permissões aos âmbitos que define.
* Partilhe um segredo com a plataforma de identidade da Microsoft que comprovam a identidade da aplicação.  Isto é relevante no caso em que a app é uma aplicação confidencial do cliente. Um pedido de cliente confidencial é uma aplicação que pode manter as credenciais de forma segura. Requerem um servidor de backend de confiança para armazenar as credenciais.

Uma vez registado, a aplicação receberá um identificador único que a aplicação partilha com a plataforma de identidade da Microsoft quando solicita fichas. Se a aplicação for uma [aplicação confidencial](developer-glossary.md#client-application)do cliente, também partilhará o segredo ou a chave pública, dependendo se foram usados certificados ou segredos.

A plataforma de identidade da Microsoft representa aplicações utilizando um modelo que cumpre duas funções principais:

* Identifique a aplicação pelos protocolos de autenticação que suporta
* Forneça todos os identificadores, URLs, segredos e informações relacionadas que são necessários para autenticar

Plataforma de identidade da Microsoft:

* Detém todos os dados necessários para suportar a autenticação em tempo de execução
* Detém todos os dados para decidir que recursos uma app pode precisar de aceder, e em que circunstâncias um dado pedido deve ser preenchido
* Fornece infraestruturas para implementar o provisionamento de aplicativos dentro do inquilino do desenvolvedor de aplicações, e para qualquer outro inquilino AZURE AD
* Lida com o consentimento do utilizador durante o tempo de pedido do token e facilita o fornecimento dinâmico de apps através dos inquilinos

**Consentimento** é o processo de um titular de recursos que concede autorização para um pedido de cliente para aceder a recursos protegidos, sob permissões específicas, em nome do proprietário do recurso. Plataforma de identidade da Microsoft:

* Permite aos utilizadores e administradores conceder ou negar dinamicamente o consentimento da aplicação para aceder a recursos em seu nome.
* Por fim, permite aos administradores decidir que aplicações estão autorizados a fazer, que utilizadores podem utilizar aplicações específicas e de que forma são acedidos os recursos de diretório.

## <a name="multi-tenant-apps"></a>Aplicações multi-inquilino

Na plataforma de identidade da Microsoft, um [objeto de aplicação](developer-glossary.md#application-object) descreve uma aplicação. No momento da implementação, a plataforma de identidade da Microsoft utiliza o objeto de aplicação como uma planta para criar um [principal serviço](developer-glossary.md#service-principal-object), o que representa uma instância concreta de uma aplicação dentro de um diretório ou inquilino. O diretor de serviço define o que a app pode realmente fazer num diretório-alvo específico, quem pode usá-lo, quais os recursos a que tem acesso, e assim por diante. A plataforma de identidade da Microsoft cria um principal de serviço a partir de um objeto de aplicação através do [consentimento.](developer-glossary.md#consent)

O diagrama seguinte mostra um fluxo de provisão de plataforma de identidade da Microsoft simplificado impulsionado pelo consentimento. Mostra dois inquilinos: *A* e *B.*

* *O inquilino A* é dono do requerimento.
* *O inquilino B* está a instantanear o pedido através de um diretor de serviço.

![Fluxo de aprovisionamento simplificado orientado por consentimento](./media/authentication-scenarios/simplified-provisioning-flow-consent-driven.svg)

Neste fluxo de aprovisionamento:

1. Um utilizador do inquilino B tenta entrar com a app, o ponto final de autorização solicita um sinal para a aplicação.
1. As credenciais de utilizador são adquiridas e verificadas para autenticação.
1. O utilizador é solicitado a fornecer consentimento para que a app obtenha acesso ao inquilino B.
1. A plataforma de identidade da Microsoft usa o objeto de aplicação no inquilino A como um plano para criar um principal de serviço no inquilino B.
1. O utilizador recebe o token solicitado.

Pode repetir este processo para inquilinos adicionais. O inquilino A mantém a planta para a aplicação (objeto de aplicação). Os utilizadores e administradores de todos os outros inquilinos onde a app é dada consentimento controlam o que a aplicação é permitida através do objeto principal de serviço correspondente em cada inquilino. Para obter mais informações, consulte [os objetos principais de aplicação e serviço na plataforma de identidade da Microsoft.](app-objects-and-service-principals.md)

## <a name="next-steps"></a>Passos seguintes

Para outros tópicos que abranjam os fundamentos básicos de autenticação e autorização:

* Consulte [a autenticação vs. autorização](authentication-vs-authorization.md) para conhecer os conceitos básicos de autenticação e autorização na plataforma de identidade da Microsoft.
* Consulte [fichas de segurança](security-tokens.md) para saber como os tokens de acesso, tokens de atualização e fichas de identificação são usados na autenticação e autorização.
* Consulte o [fluxo de entrada de app](app-sign-in-flow.md) para saber sobre o fluxo de entrada de aplicações web, desktop e mobile na plataforma de identidade da Microsoft.

Para saber mais sobre o modelo de candidatura:

* Veja [como e por que as aplicações são adicionadas ao Azure AD](active-directory-how-applications-are-added.md) para mais informações sobre objetos de aplicações e principais de serviço na plataforma de identidade da Microsoft.
* Consulte [o Tenancy in Azure Ative Directory](single-and-multi-tenant-apps.md) para obter mais informações sobre aplicações de inquilinos individuais e aplicações multi-arrendatários.
* Consulte [a documentação do Azure Ative Directory B2C](../../active-directory-b2c/index.yml) para obter mais informações sobre como a Azure AD também fornece o Azure Ative Directory B2C para que as organizações possam assinar nos utilizadores, normalmente clientes, utilizando identidades sociais como uma conta da Google.