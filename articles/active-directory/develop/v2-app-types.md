---
title: Tipos de aplicações para plataforma de identidade Microsoft  Azure
description: Os tipos de apps e cenários suportados pela plataforma de identidade da Microsoft (v2.0) ponto final.
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
ms.openlocfilehash: 8bfe668dc2eb4e0e00de34231f4c232f5240a82d
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76700758"
---
# <a name="application-types-for-microsoft-identity-platform"></a>Tipos de aplicação para plataforma de identidade Microsoft

A plataforma de identidade da Microsoft (v2.0) suporta a autenticação para uma variedade de arquiteturas de aplicações modernas, todas baseadas em protocolos padrão da indústria [OAuth 2.0 ou OpenID Connect](active-directory-v2-protocols.md). Este artigo descreve os tipos de aplicações que pode construir utilizando a plataforma de identidade da Microsoft, independentemente do seu idioma ou plataforma preferido. A informação foi concebida para o ajudar a compreender cenários de alto nível antes de [começar a trabalhar com o código](v2-overview.md#getting-started).

> [!NOTE]
> O ponto final da plataforma de identidade da Microsoft não suporta todos os cenários e funcionalidades do Azure Ative Directory (Azure AD). Para determinar se você deve usar o ponto de extremidade da plataforma de identidade da Microsoft, leia sobre as [limitações da plataforma de identidade da Microsoft](active-directory-v2-limitations.md).

## <a name="the-basics"></a>Noções básicas

Tem de registar cada aplicação que utilize o ponto final da plataforma de identidade da Microsoft no novo portal de [registos](https://go.microsoft.com/fwlink/?linkid=2083908)da App . O processo de registo da aplicação recolhe e atribui estes valores à sua aplicação:

* Um ID de **Aplicação (cliente)** que identifica exclusivamente a sua aplicação
* Um **URI redirecionamento** que pode usar para responder diretamente à sua aplicação
* Alguns outros valores específicos do cenário, tais como tipos de conta suportados

Para mais detalhes, saiba [como registar uma aplicação](quickstart-register-app.md).

Após a sua registo, a aplicação comunica com a plataforma de identidade da Microsoft enviando pedidos para o ponto final. Fornecemos quadros e bibliotecas de código aberto que tratam dos detalhes destes pedidos. Também tem a opção de implementar a lógica de autenticação por si mesmo, criando pedidos para estes pontos finais:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

## <a name="single-page-apps-javascript"></a>Aplicativos de página única (JavaScript)

Muitas aplicações modernas têm uma ponta frontal de uma aplicação de uma página única que está escrita principalmente no JavaScript. Muitas vezes, é escrito usando uma estrutura como Angular, Reagir ou Vue. O ponto final da plataforma de identidade da Microsoft suporta estas aplicações utilizando o [fluxo implícito OAuth 2.0](v2-oauth2-implicit-grant-flow.md).

Neste fluxo, a aplicação recebe tokens diretamente da plataforma de identidade da Microsoft autoriza o ponto final, sem qualquer troca de servidor-a-servidor. Toda a lógica de autenticação e manuseamento de sessões ocorre inteiramente no cliente JavaScript, sem redirecionamentos de página extra.

![Mostra o fluxo implícito de autenticação](./media/v2-app-types/convergence-scenarios-implicit.svg)

Para ver este cenário em ação, experimente uma das amostras de código de aplicação de uma página única na plataforma de identidade da [Microsoft a começar a](v2-overview.md#getting-started) secção.

## <a name="web-apps"></a>Aplicações Web

Para aplicações web (.NET, PHP, Java, Ruby, Python, Node) a que o utilizador acede através de um browser, pode utilizar o [OpenID Connect](active-directory-v2-protocols.md) para iniciar sessão do utilizador. No OpenID Connect, a aplicação web recebe um token ID. Um token de identificação é um símbolo de segurança que verifica a identidade do utilizador e fornece informações sobre o utilizador sob a forma de reclamações:

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

Mais detalhes sobre diferentes tipos de fichas utilizadas no ponto final da plataforma de identidade da Microsoft estão disponíveis na referência de [acesso e](access-tokens.md) [referência id_token](id-tokens.md)

Nas aplicações do servidor web, o fluxo de autenticação de entrada-in toma estes passos de alto nível:

![Mostra o fluxo de autenticação da aplicação web](./media/v2-app-types/convergence-scenarios-webapp.svg)

Pode garantir a identidade do utilizador validando o token de identificação com uma chave de assinatura pública que é recebida a partir do ponto final da plataforma de identidade da Microsoft. É definido um cookie de sessão, que pode ser usado para identificar o utilizador nos pedidos de página subsequentes.

Para ver este cenário em ação, experimente uma das amostras de código de início da aplicação web na [plataforma de identidade da Microsoft.](v2-overview.md#getting-started)

Além de um simples sessão, uma aplicação de servidor web pode precisar de aceder a outro serviço web, como um REST API. Neste caso, a aplicação do servidor web envolve-se num fluxo combinado openID Connect e OAuth 2.0, utilizando o fluxo de código de [autorização OAuth 2.0](active-directory-v2-protocols.md). Para mais informações sobre este cenário, leia sobre [começar com aplicações web e APIs Web.](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md)

## <a name="web-apis"></a>APIs da Web

Pode utilizar o ponto final da plataforma de identidade da Microsoft para garantir serviços web, como a API Web RESTful da sua aplicação. As APIs web podem ser implementadas em inúmeras plataformas e idiomas. Também podem ser implementados utilizando gatilhos HTTP em funções Azure. Em vez de tokens de ID e cookies de sessão, um API web usa um token de acesso OAuth 2.0 para proteger os seus dados e autenticar pedidos de entrada. O autor de uma API web anexa um sinal de acesso no cabeçalho de autorização de um pedido HTTP, como este:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

A Web API utiliza o sinal de acesso para verificar a identidade do chamador da API e para extrair informações sobre o chamador de reclamações codificadas no token de acesso. Mais detalhes sobre diferentes tipos de fichas utilizadas no ponto final da plataforma de identidade da Microsoft estão disponíveis na referência de [acesso e](access-tokens.md) [referência id_token](id-tokens.md)

Uma API Web pode dar aos utilizadores o poder de optar ou optar por funcionalidades ou dados específicos expondo permissões, também conhecidas como [âmbitos.](v2-permissions-and-consent.md) Para que uma aplicação de chamada adquira permissão a um âmbito, o utilizador deve consentir com o âmbito durante um fluxo. O ponto final da plataforma de identidade da Microsoft pede permissão ao utilizador e, em seguida, regista permissões em todas as fichas de acesso que a Web API recebe. A Web API valida as fichas de acesso que recebe em cada chamada e realiza verificações de autorização.

Um API web pode receber tokens de acesso de todos os tipos de aplicações, incluindo aplicações de servidores web, aplicações de desktop e mobile, aplicações de uma página única, daemons do lado do servidor e até mesmo outras APIs web. O fluxo de alto nível para uma API web é assim:

![Mostra o fluxo de autenticação api web](./media/v2-app-types/convergence-scenarios-webapi.svg)

Para aprender a garantir um API Web utilizando fichas de acesso OAuth2, consulte as amostras de código Web API na plataforma de identidade da [Microsoft a iniciar a](v2-overview.md#getting-started) secção.

Em muitos casos, as APIs web também precisam de fazer pedidos de saída para outras APIs web a jusante protegidas pela plataforma de identidade microsoft. Para tal, as APIs web podem tirar partido do fluxo **On-Behalf-Of,** que permite à Web API trocar um sinal de acesso de entrada para outro token de acesso a ser usado em pedidos de saída. Para mais informações, consulte a [plataforma de identidade da Microsoft e o OAuth 2.0 On-Behalf-Of flow](v2-oauth2-on-behalf-of-flow.md).

## <a name="mobile-and-native-apps"></a>Aplicações móveis e nativas

As aplicações instaladas por dispositivos, como aplicações móveis e de desktop, muitas vezes precisam de aceder a serviços de back-end ou APIs web que armazenam dados e desempenham funções em nome de um utilizador. Estas aplicações podem adicionar entrada e autorização para serviços back-end utilizando o fluxo de código de [autorização OAuth 2.0](v2-oauth2-auth-code-flow.md).

Neste fluxo, a aplicação recebe um código de autorização a partir do ponto final da plataforma de identidade da Microsoft quando o utilizador faz a entrada. O código de autorização representa a permissão da app para chamar os serviços back-end em nome do utilizador que está inscrito. A aplicação pode trocar o código de autorização em segundo plano por um token de acesso OAuth 2.0 e um token de atualização. A aplicação pode usar o sinal de acesso para autenticar apis web em pedidos HTTP, e usar o token de atualização para obter novos tokens de acesso quando fichas de acesso mais antigas expirarem.

![Mostra o fluxo de autenticação de aplicações nativas](./media/v2-app-types/convergence-scenarios-native.svg)

## <a name="daemons-and-server-side-apps"></a>Daemons e aplicativos do lado do servidor

As aplicações que têm processos de longa duração ou que operam sem interação com um utilizador também precisam de uma forma de aceder a recursos seguros, como as APIs web. Estas aplicações podem autenticar e obter fichas utilizando a identidade da aplicação, em vez da identidade delegada de um utilizador, com o fluxo de credenciais de cliente OAuth 2.0. Pode provar a identidade da aplicação usando um segredo ou certificado do cliente. Para mais informações, consulte [Autenticação na plataforma de identidade da Microsoft em aplicações daemon com certificados](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/).

Neste fluxo, a aplicação interage diretamente com o ponto final `/token` para obter acesso:

![Mostra o fluxo de autenticação da aplicação daemon](./media/v2-app-types/convergence-scenarios-daemon.svg)

Para construir uma aplicação dada, consulte a [documentação](v2-oauth2-client-creds-grant-flow.md)das credenciais do cliente, ou experimente uma aplicação de [amostra .NET.](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2)
