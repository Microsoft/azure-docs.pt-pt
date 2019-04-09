---
title: Tipos de aplicativos para a plataforma de identidade da Microsoft | Azure
description: Os tipos de aplicações e os cenários suportados pelo ponto de final do Microsoft identity platform (v2.0).
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 494a06b8-0f9b-44e1-a7a2-d728cf2077ae
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/06/2019
ms.author: celested
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 562b45f30bf057feba38a716e040ab2efc15da90
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59283329"
---
# <a name="application-types-for-microsoft-identity-platform"></a>Tipos de aplicativos para a plataforma de identidade da Microsoft

O ponto final do Microsoft identity platform (v2.0) suporta a autenticação para uma variedade de arquiteturas de aplicações modernas, todos eles com base em protocolos de norma da indústria [OAuth 2.0 ou OpenID Connect](active-directory-v2-protocols.md). Este artigo descreve os tipos de aplicações que pode criar usando a plataforma de identidade da Microsoft, independentemente do seu idioma preferencial ou plataforma. As informações foi concebidas para ajudar a compreender os cenários de alto nível antes de [começar a trabalhar com o código](v2-overview.md#getting-started).

> [!NOTE]
> O ponto de extremidade de plataforma de identidade Microsoft não suporta todos os cenários do Azure Active Directory (Azure AD) e funcionalidades. Para determinar se deve utilizar o ponto de extremidade de plataforma do Microsoft identity, leia sobre [limitações de plataforma de identidade do Microsoft](active-directory-v2-limitations.md).

## <a name="the-basics"></a>Noções básicas

Deve registrar cada aplicação que utiliza o ponto de extremidade de plataforma de identidade Microsoft no novo [portal de registos de aplicação](https://go.microsoft.com/fwlink/?linkid=2083908). O processo de registo de aplicação recolhe e atribui esses valores para a sua aplicação:

* Uma **ID da aplicação (cliente)** que identifica exclusivamente a sua aplicação
* R **URI de redirecionamento** que pode usar para direcionar as respostas de volta à sua aplicação
* Alguns outros tipos de conta de valores de cenário específico, tal como suportados

Para obter detalhes, saiba como [registar uma aplicação](quickstart-register-app.md).

Depois da aplicação é registada, a aplicação comunica com a plataforma de identidade da Microsoft através do envio de pedidos para o ponto final. Fornecemos arquiteturas open-source e bibliotecas que lidar com os detalhes destes pedidos. Também tem a opção de implementar a lógica de autenticação por conta própria através da criação de pedidos para estes pontos finais:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

## <a name="single-page-apps-javascript"></a>Aplicações de página única (JavaScript)

Muitas aplicações modernas têm um aplicação de página única front-end que é principalmente escrito em JavaScript. Muitas vezes, ele é escrito usando uma estrutura como AngularJS, ember ou Durandal.js. O ponto de extremidade de plataforma de identidade Microsoft suporta estas aplicações com o [fluxo implícito de OAuth 2.0](v2-oauth2-implicit-grant-flow.md).

Neste fluxo, a aplicação recebe tokens diretamente a partir de plataforma de identidades da Microsoft autorizar o ponto de extremidade, sem quaisquer trocas de servidor a servidor. Todos os lógica de autenticação e sessão de processamento necessário colocar inteiramente no cliente JavaScript, sem os redirecionamentos de página extra.

![Fluxo de autenticação implícita](./media/v2-app-types/convergence-scenarios-implicit.svg)

Para ver este cenário em ação, experimente um dos exemplos de código de aplicação de página única no [introdução à plataforma de identidade do Microsoft](v2-overview.md#getting-started) secção.

## <a name="web-apps"></a>Web Apps

Para aplicações web (.NET, PHP, Java, Ruby, Python, nó) que o usuário acessa por meio de um navegador, pode usar [OpenID Connect](active-directory-v2-protocols.md) para o início de sessão do utilizador. OpenID Connect, a aplicação web recebe um token de ID. Um token de ID é um token de segurança que verifica a identidade do utilizador e fornece informações sobre o utilizador na forma de afirmações:

```
// Partial raw ID token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded ID token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

Estão disponíveis em detalhes adicionais de diferentes tipos de tokens utilizados o ponto de final de plataforma de identidade Microsoft da [token de acesso](access-tokens.md) referência e [id_token referência](id-tokens.md)

Nas aplicações de servidor web, o fluxo de autenticação de início de sessão utiliza estes passos de alto nível:

![Fluxo de autenticação de aplicação Web](./media/v2-app-types/convergence-scenarios-webapp.svg)

Pode certificar-se a identidade do utilizador ao validar o token de ID com uma chave pública de assinatura que é recebida a partir do ponto de extremidade de plataforma de identidade Microsoft. Um cookie de sessão está definido, o que pode ser utilizado para identificar o utilizador nos pedidos subsequentes da página.

Para ver este cenário em ação, experimente um dos exemplos de código de início de sessão de aplicação web no [introdução à plataforma de identidade do Microsoft](v2-overview.md#getting-started) secção.

Além das simple início de sessão, uma aplicação de servidor web poderá ter de aceder a outro serviço da web, como uma API REST. Neste caso, a aplicação de servidor web seja aplicada num fluxo de OpenID Connect e OAuth 2.0 combinado, utilizando o [fluxo de código de autorização de OAuth 2.0](active-directory-v2-protocols.md). Para obter mais informações sobre este cenário, leia sobre [introdução a aplicações web e Web APIs](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md).

## <a name="web-apis"></a>APIs da Web

Pode utilizar o ponto de extremidade de plataforma de identidade Microsoft para proteger serviços da web, como a API de Web RESTful da sua aplicação. Em vez de tokens de ID e cookies de sessão, uma API Web usa um token de acesso de OAuth 2.0 para proteger seus dados e para autenticar os pedidos recebidos. O autor da chamada de uma API Web acrescenta um token de acesso no cabeçalho de autorização de uma solicitação HTTP, como este:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

A API Web utiliza o token de acesso para verificar a identidade do chamador de API e extrair informações sobre o autor da chamada de afirmações que são codificadas no token de acesso. Estão disponíveis em detalhes adicionais de diferentes tipos de tokens utilizados o ponto de final de plataforma de identidade Microsoft da [token de acesso](access-tokens.md) referência e [id_token referência](id-tokens.md)

Uma API Web pode dar aos utilizadores a capacidade de optar ativamente por participar no ou optar por dados ou funcionalidades específicas, expondo as permissões, também conhecido como [âmbitos](v2-permissions-and-consent.md). Para uma aplicação de chamada adquirir permissão a um âmbito, o utilizador tem de dar consentimento ao âmbito durante um fluxo. O ponto de extremidade de plataforma de identidade Microsoft pede ao utilizador permissão e, em seguida, regista as permissões em todos os tokens de acesso que recebe a API Web. A API Web valida os tokens de acesso que receber em cada chamada e executa verificações de autorização.

Uma API Web pode receber tokens de acesso de todos os tipos de aplicações, incluindo aplicações de servidor web, área de trabalho e aplicações móveis, aplicações de página única, daemons do lado do servidor e, inclusivamente, noutras APIs da Web. O fluxo de alto nível para uma API Web tem o seguinte aspeto:

![Fluxo de autenticação da API Web](./media/v2-app-types/convergence-scenarios-webapi.svg)

Para saber como proteger uma API Web utilizando os tokens de acesso de OAuth2, veja os exemplos de código da Web API no [introdução à plataforma de identidade do Microsoft](v2-overview.md#getting-started) secção.

Em muitos casos, as APIs web também precisa de fazer pedidos de saída para outro web a jusante APIs protegidas pela plataforma de identidade da Microsoft. Para isso, web APIs pode aproveitar o **em-nome-de** fluxo, que permite que a API web para o exchange de um token de acesso de entrada para outro token de acesso a ser utilizado em pedidos de saída. Para mais informações, veja [plataforma de identidade da Microsoft e o fluxo do OAuth 2.0 On-Behalf-Of](v2-oauth2-on-behalf-of-flow.md).

## <a name="mobile-and-native-apps"></a>Aplicações móveis e nativas

Aplicações instaladas em dispositivos, tais como aplicações de ambiente de trabalho e móveis, muitas vezes precisam de aceder a serviços de back-end ou APIs Web que armazenar dados e executar as funções em nome de um utilizador. Estas aplicações podem adicionar início de sessão e autorização para serviços de back-end utilizando a [fluxo de código de autorização de OAuth 2.0](v2-oauth2-auth-code-flow.md).

Neste fluxo, a aplicação recebe um código de autorização do ponto de extremidade do Microsoft identity platform quando o utilizador inicia sessão. O código de autorização representa a permissão da aplicação para chamar serviços de back-end em nome do utilizador que tenha sessão iniciada. A aplicação pode trocar o código de autorização em segundo plano para um token de acesso de OAuth 2.0 e um token de atualização. A aplicação pode utilizar o token de acesso para autenticar a Web APIs em pedidos de HTTP e usar o token de atualização para obter novos tokens de acesso, quando os tokens de acesso mais antigos expiram.

![Fluxo de autenticação de aplicação nativa](./media/v2-app-types/convergence-scenarios-native.svg)

## <a name="daemons-and-server-side-apps"></a>Daemons e aplicações do lado do servidor

Aplicações que têm processos de longa execução ou que não funcionam sem a interação com um utilizador tem também de uma forma de aceder a recursos protegidos, como as APIs da Web. Estas aplicações podem autenticar e obter os tokens utilizando a identidade da aplicação, em vez de um utilizador delegado identidade, com o fluxo de credenciais de cliente OAuth 2.0. Pode comprovar a identidade da aplicação com um certificado ou um segredo do cliente. Para mais informações, veja [autenticar para a plataforma de identidade da Microsoft nas aplicações daemon com certificados](https://azure.microsoft.com/resources/samples/active-directory-dotnet-daemon-certificate-credential/).

Neste fluxo, a aplicação interage diretamente com o `/token` ponto final para obter acesso:

![Fluxo de autenticação de aplicação de daemon](./media/v2-app-types/convergence-scenarios-daemon.svg)

Para criar uma aplicação de daemon, veja a [documentação de credenciais de cliente](v2-oauth2-client-creds-grant-flow.md), ou tente um [aplicação de exemplo .NET](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2).
