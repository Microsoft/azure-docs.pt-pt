---
title: Tipos de aplicações suportados por Azure AD B2C
titleSuffix: Azure AD B2C
description: Saiba mais sobre os tipos de aplicações que pode utilizar com o Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 07897823a3ba3b83e240e8e8dc005ea13b036fce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94952051"
---
# <a name="application-types-that-can-be-used-in-active-directory-b2c"></a>Tipos de aplicação que podem ser usados no Ative Directory B2C
 
O Azure Ative Directory B2C (Azure AD B2C) suporta a autenticação para uma variedade de arquiteturas de aplicações modernas. Todos elas baseiam-se nos protocolos padrão da indústria [OAuth 2.0](protocols-overview.md) ou [OpenID Connect](protocols-overview.md). Este artigo descreve os tipos de aplicações que pode construir, independentemente do idioma ou plataforma que prefere. Também ajuda-o a compreender os cenários de alto nível antes de começar a construir aplicações.

Todas as aplicações que utilizem o Azure AD B2C devem estar registadas no seu [inquilino Azure AD B2C](tutorial-create-tenant.md) utilizando o [portal Azure](https://portal.azure.com/). O processo de registo de candidatura recolhe e atribui valores, tais como:

* Um **ID de aplicação** que identifica exclusivamente a sua aplicação.
* Um **URL de resposta** que pode ser usado para direcionar respostas de volta à sua aplicação.

Cada pedido enviado para Azure AD B2C especifica um fluxo de **utilizador** (uma política incorporada) ou uma **política personalizada** que controla o comportamento do Azure AD B2C. Ambos os tipos de política permitem criar um conjunto altamente personalizável de experiências do utilizador.

A interação de cada aplicação segue um padrão de alto nível semelhante:

1. A aplicação direciona o utilizador para o ponto final v2.0 para executar uma [apólice](user-flow-overview.md).
2. O utilizador conclui a política de acordo com a sua definição.
3. O pedido recebe um sinal de segurança do ponto final v2.0.
4. A aplicação utiliza o símbolo de segurança para aceder a informações protegidas ou a um recurso protegido.
5. O servidor de recurso valida o token de segurança para verificar que o acesso pode ser concedido.
6. A aplicação atualiza periodicamente o token de segurança.

Estes passos podem diferir ligeiramente com base no tipo de aplicação que está a construir.

## <a name="web-applications"></a>Aplicações Web

Para aplicações web (incluindo .NET, PHP, Java, Ruby, Python e Node.js) que são hospedadas num servidor e acedidas através de um browser, o Azure AD B2C suporta [o OpenID Connect](protocols-overview.md) para todas as experiências do utilizador. Na implementação Azure AD B2C do OpenID Connect, a sua aplicação web inicia experiências de utilizador através da emissão de pedidos de autenticação ao Azure AD. O resultado do pedido é um `id_token`. Este token de segurança representa a identidade do utilizador. Também fornece informações sobre o utilizador na forma de afirmações:

```json
// Partial raw id_token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded id_token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

Saiba mais sobre os tipos de fichas e reclamações disponíveis para uma aplicação na [referência simbólica AZURE AD B2C](tokens-overview.md).

Numa aplicação web, cada execução de uma [política](user-flow-overview.md) toma estes passos de alto nível:

1. O utilizador navega para a aplicação web.
2. A aplicação web redireciona o utilizador para Azure AD B2C indicando a política a executar.
3. O utilizador completa a política.
4. Azure AD B2C devolve um `id_token` ao navegador.
5. O `id_token` é colocado no URI redirecionado.
6. O `id_token` é validado e um cookie de sessão é definido.
7. Uma página segura é devolvida ao utilizador.

A validação do `id_token` utilizando uma chave pública de assinatura que é recebida do Azure AD é suficiente para verificar a identidade do utilizador. Este processo também define um cookie de sessão que pode ser usado para identificar o utilizador em pedidos de página subsequentes.

Para ver este cenário em ação, experimente uma das amostras de código de inscrição da web na nossa [secção 'Iniciar).](overview.md)

Além de facilitar o simples sing-in, uma aplicação de servidor web também pode precisar de aceder a um serviço web de back-end. Neste caso, a aplicação web pode executar um [fluxo openID connect](openid-connect.md) ligeiramente diferente e adquirir fichas usando códigos de autorização e fichas de atualização. Este cenário é descrito na seguinte [secção APIs da Web](#web-apis).

## <a name="single-page-applications"></a>Aplicações de uma página única
Muitas aplicações web modernas são construídas como aplicações de página única do lado do cliente ("SPAs"). Os desenvolvedores escrevem-nos utilizando o JavaScript ou uma estrutura SPA como Angular, Vue e React. Estas aplicações são executadas num navegador web e têm características de autenticação diferentes das aplicações tradicionais do lado do servidor.

O Azure AD B2C oferece **duas** opções para permitir que aplicações de uma página única assinem nos utilizadores e obter fichas para aceder a serviços de back-end ou APIs web:

### <a name="authorization-code-flow-with-pkce"></a>Fluxo de código de autorização (com PKCE)
- [OAuth 2.0 Fluxo de código de autorização (com PKCE)](./authorization-code-flow.md). O fluxo de código de autorização permite que a aplicação troque um código de autorização para fichas **de identificação** para representar o utilizador autenticado e fichas de **acesso** necessárias para chamar APIs protegidas. Além disso, devolve tokens **Refresh** que fornecem acesso a longo prazo aos recursos em nome dos utilizadores sem necessidade de interação com esses utilizadores. 

Esta é a abordagem **recomendada.** Ter tokens de atualização de vida limitada ajuda a sua aplicação a adaptar-se às [limitações modernas](../active-directory/develop/reference-third-party-cookies-spas.md)de privacidade de cookies do navegador , como o Safari ITP.

Para tirar partido deste fluxo, a sua aplicação pode utilizar uma biblioteca de autenticação que o suporte, como [MSAL.js 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser).

<!-- ![Single-page applications-auth](./media/tutorial-single-page-app/spa-app-auth.svg) -->
![Aplicações de uma só página-auth](./media/tutorial-single-page-app/active-directory-oauth-code-spa.png)

### <a name="implicit-grant-flow"></a>Fluxo de subvenção implícito
- [OAuth 2.0 fluxo implícito](implicit-flow-single-page-application.md). Alguns quadros, como [MSAL.js 1.x,](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core)apenas apoiam o fluxo implícito de subvenções. O fluxo de subvenção implícita permite que a aplicação obtenha **ID** e tokens de **acesso.** Ao contrário do fluxo de código de autorização, o fluxo de subvenção implícita não devolve um **token Refresh**. 

Este fluxo de autenticação não inclui cenários de aplicação que utilizem quadros javaScript de plataforma cruzada, tais como Electrn e React-Native. Estes cenários requerem mais capacidades de interação com as plataformas nativas.

## <a name="web-apis"></a>APIs da Web

Pode utilizar o Azure AD B2C para garantir serviços web como a API web RESTful da sua aplicação. As APIs Web podem utilizar o OAuth 2.0 para proteger os seus dados, ao autenticar pedidos HTTP recebidos utilizando tokens. O autor da chamada de uma API web acrescenta um token no cabeçalho de autorização de um pedido HTTP:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
``` 

A API web pode utilizar o token para verificar a identidade do autor da chamada da API e extrair informações sobre o autor da chamada com base nas afirmações codificadas no token. Saiba mais sobre os tipos de tokens e afirmações disponíveis para uma aplicação na [referência de token do Azure AD B2C](tokens-overview.md).

Uma API web pode receber fichas de vários tipos de clientes, incluindo aplicações web, aplicações de desktop e mobile, aplicações de página única, daemons do lado do servidor e outras APIs web. Aqui está um exemplo do fluxo completo para uma aplicação web que chama uma API web:

1. A aplicação web executa uma política e o utilizador completa a experiência do utilizador.
2. Azure AD B2C devolve um (OpenID Connect) `id_token` e um código de autorização para o navegador.
3. O navegador coloca o `id_token` código e o código de autorização para o URI redirecionado.
4. O servidor web valida `id_token` o e define um cookie de sessão.
5. O servidor web pede a Azure AD B2C `access_token` um, fornecendo-lhe o código de autorização, iD do cliente de aplicação e credenciais do cliente.
6. O `access_token` e `refresh_token` são devolvidos ao servidor web.
7. A API web é chamada com o `access_token` cabeçalho de autorização.
8. A API web valida o token.
9. Os dados seguros são devolvidos à aplicação web.

Para obter mais informações sobre códigos de autorização, tokens de atualização e os passos para obter os tokens, leia sobre o [protocolo OAuth 2.0](authorization-code-flow.md).

Para saber como proteger uma API web utilizando o Azure AD B2C, consulte os tutoriais sobre web API na nossa [secção Introdução](overview.md).

## <a name="mobile-and-native-applications"></a>Aplicações móveis e nativas

As aplicações que são instaladas em dispositivos, como aplicações móveis e desktop, muitas vezes precisam de aceder a serviços de back-end ou APIs web em nome dos utilizadores. Pode adicionar experiências personalizadas de gestão de identidade às suas aplicações nativas e ligar de forma segura para serviços de back-end utilizando o Azure AD B2C e o [fluxo de código de autorização OAuth 2.0](authorization-code-flow.md).

Neste fluxo, a aplicação executa [políticas](user-flow-overview.md) e recebe uma `authorization_code` ad da Azure após o utilizador completar a apólice. A `authorization_code` aplicação representa a permissão da aplicação para ligar para serviços de back-end em nome do utilizador que está atualmente inscrito. A aplicação pode então trocar `authorization_code` o em segundo plano por um e um `access_token` `refresh_token` .  A aplicação pode utilizar `access_token` a autenticação para uma API web de back-end em pedidos HTTP. Também pode utilizar o `refresh_token` para obter um novo `access_token` quando o antigo expira.

## <a name="current-limitations"></a>Limitações atuais

### <a name="unsupported-application-types"></a>Tipos de aplicações não suportadas

#### <a name="daemonsserver-side-applications"></a>Aplicações do lado do daemons/servidor

As aplicações que contenham processos de longa duração ou que funcionem sem a presença de um utilizador também precisam de uma forma de aceder a recursos seguros, como APIs web. Estas aplicações podem autenticar e obter fichas utilizando a identidade da aplicação (em vez da identidade delegada de um utilizador) e utilizando o fluxo de credenciais de cliente OAuth 2.0. O fluxo de credencial do cliente não é o mesmo que o fluxo em nome e o fluxo em nome não deve ser utilizado para a autenticação do servidor-para-servidor.

Embora o fluxo de concessão de credenciais de cliente OAuth 2.0 não seja suportado diretamente pelo serviço de autenticação Azure AD B2C, pode configurar o fluxo de credencial do cliente utilizando a Azure AD e a plataforma de identidade da Microsoft/ponto final simbólico para uma aplicação no seu inquilino Azure AD B2C. Um inquilino Azure AD B2C partilha alguma funcionalidade com inquilinos da AZure AD.

Para configurar o fluxo de credencial do cliente, consulte [o Azure Ative Directory v2.0 e o fluxo de credenciais de clientes OAuth 2.0](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md). Uma autenticação bem sucedida resulta na receção de um token formatado para que possa ser utilizado pela Azure AD, conforme descrito na [referência simbólica Azure AD](../active-directory/develop/id-tokens.md).

Para obter instruções sobre o registo de uma aplicação de gestão, consulte [Manage Azure AD B2C com o Microsoft Graph](microsoft-graph-get-started.md).

#### <a name="web-api-chains-on-behalf-of-flow"></a>Cadeias de API Web (fluxo em-nome-de)

Muitas arquiteturas incluem uma API web que precisa chamar outra API web a jusante, estando ambas protegidas pelo Azure AD B2C. Este cenário é comum em clientes nativos que têm um back-end Web API e chama um serviço online da Microsoft, como o Microsoft Graph API.

Este cenário de API web em cadeia pode ser suportado utilizando a concessão de credencial de portador do OAuth 2.0 JWT, também conhecido como fluxo em-nome-de.  No entanto, o fluxo em nome de não está atualmente implementado no Azure AD B2C.

### <a name="faulted-apps"></a>Aplicações com falha

Não edite as aplicações Azure AD B2C desta forma:

- Noutros portais de gestão de aplicações, como o [Portal de Registo da Aplicação](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade).
- Utilizando API de Gráfico ou PowerShell.

Se editar a aplicação Azure AD B2C fora do portal Azure, torna-se uma aplicação defeituosa e já não é utilizável com Azure AD B2C. Elimine a aplicação e crie-a novamente.

Para eliminar a candidatura, aceda ao Portal de [Registo de Candidaturas](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) e elimine a aplicação no mesmo. Para que a aplicação fique visível, tem de ser o proprietário da aplicação (e não apenas um administrador do inquilino).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as políticas [incorporadas fornecidas pelos fluxos de utilizador no Azure Ative Directory B2C](user-flow-overview.md).