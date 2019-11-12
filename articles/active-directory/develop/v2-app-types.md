---
title: Tipos de aplicativos para a plataforma Microsoft Identity | Azure
description: Os tipos de aplicativos e cenários com suporte no ponto de extremidade da plataforma Microsoft Identity (v 2.0).
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 494a06b8-0f9b-44e1-a7a2-d728cf2077ae
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/06/2019
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 21f1da8738946078cb625c76e11ce1bfd62d97ac
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73927165"
---
# <a name="application-types-for-microsoft-identity-platform"></a>Tipos de aplicativos para a plataforma Microsoft Identity

O ponto de extremidade da plataforma Microsoft Identity (v 2.0) dá suporte à autenticação para uma variedade de arquiteturas de aplicativo modernas, todas elas baseadas em protocolos padrão do setor [OAuth 2,0 ou OpenID Connect](active-directory-v2-protocols.md). Este artigo descreve os tipos de aplicativos que você pode criar usando a plataforma de identidade da Microsoft, independentemente do idioma ou da plataforma preferida. As informações foram projetadas para ajudá-lo a entender cenários de alto nível antes [de começar a trabalhar com o código](v2-overview.md#getting-started).

> [!NOTE]
> O ponto de extremidade da plataforma de identidade da Microsoft não dá suporte a todos os cenários e recursos do Azure Active Directory (Azure AD). Para determinar se você deve usar o ponto de extremidade da plataforma de identidade da Microsoft, leia sobre as [limitações da plataforma de identidade da Microsoft](active-directory-v2-limitations.md).

## <a name="the-basics"></a>Noções básicas

Você deve registrar cada aplicativo que usa o ponto de extremidade da plataforma de identidade da Microsoft no novo [portal de registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908). O processo de registro de aplicativo coleta e atribui esses valores para seu aplicativo:

* Uma **ID de aplicativo (cliente)** que identifica exclusivamente seu aplicativo
* Um **URI de redirecionamento** que você pode usar para direcionar as respostas de volta para seu aplicativo
* Alguns outros valores específicos de cenário, como tipos de conta com suporte

Para obter detalhes, saiba como [registrar um aplicativo](quickstart-register-app.md).

Depois que o aplicativo é registrado, o aplicativo se comunica com a plataforma de identidade da Microsoft enviando solicitações ao ponto de extremidade. Fornecemos estruturas e bibliotecas de software livre que lidam com os detalhes dessas solicitações. Você também tem a opção de implementar a lógica de autenticação Criando solicitações para esses pontos de extremidade:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

## <a name="single-page-apps-javascript"></a>Aplicativos de página única (JavaScript)

Muitos aplicativos modernos têm um front-end de aplicativo de página única que é basicamente escrito em JavaScript. Geralmente, ele é escrito usando uma estrutura como angular, reagir ou Vue. O ponto de extremidade da plataforma de identidade da Microsoft dá suporte a esses aplicativos usando o [fluxo implícito do OAuth 2,0](v2-oauth2-implicit-grant-flow.md).

Nesse fluxo, o aplicativo recebe tokens diretamente do ponto de extremidade de autorização da plataforma Microsoft Identity, sem nenhuma troca de servidor para servidor. Toda a lógica de autenticação e o tratamento de sessão ocorrem inteiramente no cliente JavaScript, sem redirecionamentos de página adicionais.

![Mostra o fluxo de autenticação implícito](./media/v2-app-types/convergence-scenarios-implicit.svg)

Para ver esse cenário em ação, experimente um dos exemplos de código do aplicativo de página única na seção [introdução à plataforma de identidade da Microsoft](v2-overview.md#getting-started) .

## <a name="web-apps"></a>Web Apps

Para aplicativos Web (.NET, PHP, Java, Ruby, Python, Node) que o usuário acessa por meio de um navegador, você pode usar o [OpenID Connect](active-directory-v2-protocols.md) para entrada do usuário. No OpenID Connect, o aplicativo Web recebe um token de ID. Um token de ID é um token de segurança que verifica a identidade do usuário e fornece informações sobre o usuário na forma de declarações:

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

Mais detalhes de tipos diferentes de tokens usados no ponto de extremidade da plataforma Microsoft Identity estão disponíveis na referência de [token de acesso](access-tokens.md) e [referência de id_token](id-tokens.md)

Em aplicativos de servidor Web, o fluxo de autenticação de entrada usa estas etapas de alto nível:

![Mostra o fluxo de autenticação do aplicativo Web](./media/v2-app-types/convergence-scenarios-webapp.svg)

Você pode garantir a identidade do usuário Validando o token de ID com uma chave de assinatura pública que é recebida do ponto de extremidade da plataforma Microsoft Identity. Um cookie de sessão é definido, que pode ser usado para identificar o usuário nas solicitações de página subsequentes.

Para ver esse cenário em ação, tente um dos exemplos de código de entrada do aplicativo Web na seção [introdução à plataforma de identidade da Microsoft](v2-overview.md#getting-started) .

Além do logon simples, um aplicativo de servidor Web pode precisar acessar outro serviço Web, como uma API REST. Nesse caso, o aplicativo do servidor Web se encaixa em um fluxo do OpenID Connect e do OAuth 2,0 combinado, usando o [fluxo de código de autorização do oauth 2,0](active-directory-v2-protocols.md). Para obter mais informações sobre esse cenário, leia sobre como [começar com aplicativos Web e APIs Web](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md).

## <a name="web-apis"></a>APIs da Web

Você pode usar o ponto de extremidade da plataforma de identidade da Microsoft para proteger serviços Web, como a API Web RESTful do seu aplicativo. As APIs da Web podem ser implementadas em várias plataformas e linguagens. Eles também podem ser implementados usando gatilhos HTTP no Azure Functions. Em vez de tokens de ID e cookies de sessão, uma API Web usa um token de acesso OAuth 2,0 para proteger seus dados e autenticar solicitações de entrada. O chamador de uma API da Web acrescenta um token de acesso no cabeçalho de autorização de uma solicitação HTTP, desta forma:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

A API da Web usa o token de acesso para verificar a identidade do chamador da API e extrair informações sobre o chamador de declarações que são codificadas no token de acesso. Mais detalhes de tipos diferentes de tokens usados no ponto de extremidade da plataforma Microsoft Identity estão disponíveis na referência de [token de acesso](access-tokens.md) e [referência de id_token](id-tokens.md)

Uma API da Web pode fornecer aos usuários o poder de aceitar ou recusar a funcionalidade ou os dados específicos, expondo permissões, também conhecidas como [escopos](v2-permissions-and-consent.md). Para que um aplicativo de chamada Obtenha permissão para um escopo, o usuário deve consentir o escopo durante um fluxo. O ponto de extremidade da plataforma de identidade da Microsoft solicita permissão ao usuário e, em seguida, registra permissões em todos os tokens de acesso que a API Web recebe. A API da Web valida os tokens de acesso que recebe em cada chamada e executa verificações de autorização.

Uma API da Web pode receber tokens de acesso de todos os tipos de aplicativos, incluindo aplicativos de servidor Web, aplicativos móveis e de área de trabalho, aplicativos de página única, daemons do lado do servidor e até outras APIs da Web. O fluxo de alto nível para uma API da Web tem esta aparência:

![Mostra o fluxo de autenticação da API Web](./media/v2-app-types/convergence-scenarios-webapi.svg)

Para saber como proteger uma API Web usando tokens de acesso OAuth2, confira os exemplos de código da API Web na seção [introdução à plataforma de identidade da Microsoft](v2-overview.md#getting-started) .

Em muitos casos, as APIs da Web também precisam fazer solicitações de saída para outras APIs da Web downstream protegidas pela plataforma de identidade da Microsoft. Para fazer isso, as APIs da Web podem aproveitar o fluxo **em nome de** , o que permite que a API da Web troque um token de acesso de entrada para que outro token de acesso seja usado em solicitações de saída. Para obter mais informações, consulte [plataforma de identidade da Microsoft e fluxo em nome de OAuth 2,0](v2-oauth2-on-behalf-of-flow.md).

## <a name="mobile-and-native-apps"></a>Aplicações móveis e nativas

Aplicativos instalados pelo dispositivo, como aplicativos móveis e de área de trabalho, geralmente precisam acessar serviços de back-end ou APIs Web que armazenam dados e executam funções em nome de um usuário. Esses aplicativos podem adicionar entrada e autorização a serviços de back-end usando o fluxo de [código de autorização do OAuth 2,0](v2-oauth2-auth-code-flow.md).

Nesse fluxo, o aplicativo recebe um código de autorização do ponto de extremidade da plataforma Microsoft Identity quando o usuário faz logon. O código de autorização representa a permissão do aplicativo para chamar serviços de back-end em nome do usuário que está conectado. O aplicativo pode trocar o código de autorização em segundo plano por um token de acesso OAuth 2,0 e um token de atualização. O aplicativo pode usar o token de acesso para autenticar as APIs da Web em solicitações HTTP e usar o token de atualização para obter novos tokens de acesso quando os tokens de acesso mais antigos expirarem.

![Mostra o fluxo de autenticação de aplicativo nativo](./media/v2-app-types/convergence-scenarios-native.svg)

## <a name="daemons-and-server-side-apps"></a>Daemons e aplicativos do lado do servidor

Os aplicativos que têm processos de execução longa ou que operam sem interação com um usuário também precisam de uma maneira de acessar recursos protegidos, como APIs da Web. Esses aplicativos podem autenticar e obter tokens usando a identidade do aplicativo, em vez da identidade delegada de um usuário, com o fluxo de credenciais do cliente OAuth 2,0. Você pode provar a identidade do aplicativo usando um certificado ou segredo do cliente. Para obter mais informações, consulte [Autenticando para a plataforma Microsoft Identity em aplicativos daemon com certificados](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/).

Nesse fluxo, o aplicativo interage diretamente com o ponto de extremidade `/token` para obter acesso:

![Mostra o fluxo de autenticação do aplicativo de daemon](./media/v2-app-types/convergence-scenarios-daemon.svg)

Para criar um aplicativo daemon, consulte a [documentação de credenciais do cliente](v2-oauth2-client-creds-grant-flow.md)ou experimente um [aplicativo de exemplo .net](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2).
