---
title: Tipos de aplicativos para a versão 2.0 | Azure
description: Os tipos de aplicações e os cenários suportados pelo ponto de final de v2.0 do Azure Active Directory.
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
ms.date: 12/18/2018
ms.author: celested
ms.reviewer: saeeda, jmprieur, andret
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0aac3e40089c67cd71a2c634280e333964e47902
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58499986"
---
# <a name="application-types-for-v20"></a>Tipos de aplicativos para o v2.0

O ponto de final de v2.0 do Azure Active Directory (Azure AD) suporta a autenticação para uma variedade de arquiteturas de aplicações modernas, todos eles com base em protocolos de norma da indústria [OAuth 2.0 ou OpenID Connect](active-directory-v2-protocols.md). Este artigo descreve os tipos de aplicações que pode criar usando v2.0 do Azure AD, independentemente do seu idioma preferencial ou plataforma. As informações neste artigo foi concebidas para ajudar a compreender os cenários de alto nível antes de [começar a trabalhar com o código](v2-overview.md#getting-started).

> [!NOTE]
> O ponto final v2.0 não suporta todos os cenários do Azure Active Directory e funcionalidades. Para determinar se deve utilizar o ponto final v2.0, leia sobre [v2.0 limitações](active-directory-v2-limitations.md).

## <a name="the-basics"></a>Noções básicas

Deve registrar cada aplicação que utiliza o ponto final v2.0 no [Portal de registo de aplicação do Microsoft](https://apps.dev.microsoft.com). O processo de registo de aplicação recolhe e atribui esses valores para a sua aplicação:

* Uma **ID da aplicação** que identifica exclusivamente a sua aplicação
* R **URI de redirecionamento** que pode usar para direcionar as respostas de volta à sua aplicação
* Alguns outros valores específicos de cenário

Para obter detalhes, saiba como [registar uma aplicação](quickstart-v2-register-an-app.md).

Depois da aplicação é registada, a aplicação comunica com o Azure AD ao enviar pedidos para o ponto de final de v2.0 do Azure AD. Fornecemos arquiteturas open-source e bibliotecas que lidar com os detalhes destes pedidos. Também tem a opção de implementar a lógica de autenticação por conta própria através da criação de pedidos para estes pontos finais:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

## <a name="single-page-apps-javascript"></a>Aplicações de página única (JavaScript)

Muitas aplicações modernas têm um aplicação de página única front-end que é principalmente escrito em JavaScript. Muitas vezes, ele é escrito usando uma estrutura como AngularJS, ember ou Durandal.js. O ponto de final de v2.0 do Azure AD suporta estas aplicações com o [fluxo implícito de OAuth 2.0](v2-oauth2-implicit-grant-flow.md).

Neste fluxo, a aplicação recebe tokens diretamente a partir da versão 2.0 autorizar o ponto de extremidade, sem quaisquer trocas de servidor a servidor. Todos os lógica de autenticação e sessão de processamento necessário colocar inteiramente no cliente JavaScript, sem os redirecionamentos de página extra.

![Fluxo de autenticação implícita](./media/v2-app-types/convergence_scenarios_implicit.png)

Para ver este cenário em ação, experimente um dos exemplos de código de aplicação de página única no [v2.0 Introdução](v2-overview.md#getting-started) secção.

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

Estão disponíveis em detalhes adicionais de diferentes tipos de tokens utilizados no ponto final v2.0 a [token de acesso](access-tokens.md) referência e [ `id_token` referência](id-tokens.md)

Nas aplicações de servidor web, o fluxo de autenticação de início de sessão utiliza estes passos de alto nível:

![Fluxo de autenticação de aplicação Web](./media/v2-app-types/convergence_scenarios_webapp.png)

Pode certificar-se a identidade do utilizador ao validar o token de ID com uma chave pública de assinatura que é recebida a partir do ponto final v2.0. Um cookie de sessão está definido, o que pode ser utilizado para identificar o utilizador nos pedidos subsequentes da página.

Para ver este cenário em ação, experimente um dos exemplos de código de início de sessão de aplicação web no [v2.0 Introdução](v2-overview.md#getting-started) secção.

Além das simple início de sessão, uma aplicação de servidor web poderá ter de aceder a outro serviço da web, como uma API REST. Neste caso, a aplicação de servidor web seja aplicada num fluxo de OpenID Connect e OAuth 2.0 combinado, utilizando o [fluxo de código de autorização de OAuth 2.0](active-directory-v2-protocols.md). Para obter mais informações sobre este cenário, leia sobre [introdução a aplicações web e Web APIs](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md).

## <a name="web-apis"></a>APIs da Web

Pode utilizar o ponto final v2.0 para proteger serviços da web, como a API de Web RESTful da sua aplicação. Em vez de tokens de ID e cookies de sessão, uma API Web usa um token de acesso de OAuth 2.0 para proteger seus dados e para autenticar os pedidos recebidos. O autor da chamada de uma API Web acrescenta um token de acesso no cabeçalho de autorização de uma solicitação HTTP, como este:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

A API Web utiliza o token de acesso para verificar a identidade do chamador de API e extrair informações sobre o autor da chamada de afirmações que são codificadas no token de acesso. Estão disponíveis em detalhes adicionais de diferentes tipos de tokens utilizados no ponto final v2.0 a [token de acesso](access-tokens.md) referência e [ `id_token` referência](id-tokens.md)

Uma API Web pode dar aos utilizadores a capacidade de optar ativamente por participar no ou optar por dados ou funcionalidades específicas, expondo as permissões, também conhecido como [âmbitos](v2-permissions-and-consent.md). Para uma aplicação de chamada adquirir permissão a um âmbito, o utilizador tem de dar consentimento ao âmbito durante um fluxo. O ponto final v2.0 pede ao utilizador permissão e, em seguida, regista as permissões em todos os tokens de acesso que recebe a API Web. A API Web valida os tokens de acesso que receber em cada chamada e executa verificações de autorização.

Uma API Web pode receber tokens de acesso de todos os tipos de aplicações, incluindo aplicações de servidor web, área de trabalho e aplicações móveis, aplicações de página única, daemons do lado do servidor e, inclusivamente, noutras APIs da Web. O fluxo de alto nível para uma API Web tem o seguinte aspeto:

![Fluxo de autenticação da API Web](./media/v2-app-types/convergence_scenarios_webapi.png)

Para saber como proteger uma API Web utilizando os tokens de acesso de OAuth2, veja os exemplos de código da Web API no [v2.0 Introdução](v2-overview.md#getting-started) secção.

Em muitos casos, as APIs web também precisa de fazer solicitações de saída para outro web a jusante APIs protegidas pelo Azure Active Directory. Para fazer isso, web APIs pode tirar partido do Azure AD **no nome de** fluxo, que permite que a API web para o exchange de um token de acesso de entrada para outro token de acesso a ser utilizado em pedidos de saída. O ponto final v2.0 em nome de fluxo é descrito em [detalhes aqui](v2-oauth2-on-behalf-of-flow.md).

## <a name="mobile-and-native-apps"></a>Aplicações móveis e nativas

Aplicações instaladas em dispositivos, tais como aplicações de ambiente de trabalho e móveis, muitas vezes precisam de aceder a serviços de back-end ou APIs Web que armazenar dados e executar as funções em nome de um utilizador. Estas aplicações podem adicionar início de sessão e autorização para serviços de back-end utilizando a [fluxo de código de autorização de OAuth 2.0](v2-oauth2-auth-code-flow.md).

Neste fluxo, a aplicação recebe um código de autorização do ponto final v2.0, quando o utilizador inicia sessão. O código de autorização representa a permissão da aplicação para chamar serviços de back-end em nome do utilizador que tenha sessão iniciada. A aplicação pode trocar o código de autorização em segundo plano para um token de acesso de OAuth 2.0 e um token de atualização. A aplicação pode utilizar o token de acesso para autenticar a Web APIs em pedidos de HTTP e usar o token de atualização para obter novos tokens de acesso, quando os tokens de acesso mais antigos expiram.

![Fluxo de autenticação de aplicação nativa](./media/v2-app-types/convergence_scenarios_native.png)

## <a name="daemons-and-server-side-apps"></a>Daemons e aplicações do lado do servidor

Aplicações que têm processos de longa execução ou que não funcionam sem a interação com um utilizador tem também de uma forma de aceder a recursos protegidos, como as APIs da Web. Estas aplicações podem autenticar e obter os tokens utilizando a identidade da aplicação, em vez de um utilizador delegado identidade, com o fluxo de credenciais de cliente OAuth 2.0. Pode comprovar a identidade da aplicação com um certificado ou um segredo do cliente. Para mais informações, veja [autenticar no Azure AD em aplicações daemon com certificados](https://azure.microsoft.com/resources/samples/active-directory-dotnet-daemon-certificate-credential/).

Neste fluxo, a aplicação interage diretamente com o `/token` ponto final para obter acesso:

![Fluxo de autenticação de aplicação de daemon](./media/v2-app-types/convergence_scenarios_daemon.png)

Para criar uma aplicação de daemon, veja a [documentação de credenciais de cliente](v2-oauth2-client-creds-grant-flow.md), ou tente um [aplicação de exemplo .NET](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2).
