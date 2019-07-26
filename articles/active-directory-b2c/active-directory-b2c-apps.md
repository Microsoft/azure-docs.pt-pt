---
title: Tipos de aplicativos que podem ser usados no Azure Active Directory B2C
description: Saiba mais sobre os tipos de aplicativos que você pode usar com Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 09cdc2fb5dba152e467164fd757225c7a9183264
ms.sourcegitcommit: c71306fb197b433f7b7d23662d013eaae269dc9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68369396"
---
# <a name="application-types-that-can-be-used-in-active-directory-b2c"></a>Tipos de aplicativos que podem ser usados no Active Directory B2C

O Azure Active Directory (Azure AD) B2C dá suporte à autenticação para uma variedade de arquiteturas de aplicativo modernas. Todos elas baseiam-se nos protocolos padrão da indústria [OAuth 2.0](active-directory-b2c-reference-protocols.md) ou [OpenID Connect](active-directory-b2c-reference-protocols.md). Este artigo descreve os tipos de aplicativos que você pode criar, independentemente da linguagem ou da plataforma que preferir. Ele também ajuda a entender os cenários de alto nível antes de começar a criar aplicativos.

Cada aplicativo que usa Azure AD B2C deve ser registrado em seu [locatário Azure ad B2C](active-directory-b2c-get-started.md) usando o [portal do Azure](https://portal.azure.com/). O processo de registro de aplicativo coleta e atribui valores, como:

* Uma **ID de aplicativo** que identifica exclusivamente seu aplicativo.
* Uma **URL de resposta** que pode ser usada para direcionar as respostas de volta ao seu aplicativo.

Cada solicitação enviada para Azure AD B2C especifica um fluxo de **usuário** (uma política interna) ou uma **política personalizada** que controla o comportamento de Azure ad B2C. Os dois tipos de política permitem que você crie um conjunto altamente personalizável de experiências de usuário.

A interação de cada aplicação segue um padrão de alto nível semelhante:

1. O aplicativo direciona o usuário para o ponto de extremidade v 2.0 para executar uma [política](active-directory-b2c-reference-policies.md).
2. O utilizador conclui a política de acordo com a sua definição.
3. O aplicativo recebe um token de segurança do ponto de extremidade v 2.0.
4. O aplicativo usa o token de segurança para acessar informações protegidas ou um recurso protegido.
5. O servidor de recurso valida o token de segurança para verificar se o acesso pode ser concedido.
6. A aplicação atualiza periodicamente o token de segurança.

Essas etapas podem diferir ligeiramente com base no tipo de aplicativo que você está criando.

## <a name="web-applications"></a>Aplicações Web

Para aplicativos Web (incluindo .NET, PHP, Java, Ruby, Python e node. js) hospedados em um servidor e acessados por meio de um navegador, o Azure AD B2C dá suporte a [OpenID Connect](active-directory-b2c-reference-protocols.md) para todas as experiências do usuário. Na implementação de Azure AD B2C do OpenID Connect, seu aplicativo Web inicia as experiências do usuário emitindo solicitações de autenticação para o Azure AD. O resultado do pedido é um `id_token`. Este token de segurança representa a identidade do utilizador. Também fornece informações sobre o utilizador na forma de afirmações:

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

Saiba mais sobre os tipos de tokens e declarações disponíveis para um aplicativo na [referência de token de Azure ad B2C](active-directory-b2c-reference-tokens.md).

Em um aplicativo Web, cada execução de uma [política](active-directory-b2c-reference-policies.md) usa estas etapas de alto nível:

1. O usuário navega para o aplicativo Web.
2. O aplicativo Web redireciona o usuário para Azure AD B2C indicando a política a ser executada.
3. O usuário conclui a política.
4. Azure ad B2C retorna um `id_token` para o navegador.
5. O `id_token` é Postado no URI de redirecionamento.
6. O `id_token` é validado e um cookie de sessão é definido.
7. Uma página segura é retornada ao usuário.

A validação do `id_token` utilizando uma chave pública de assinatura que é recebida do Azure AD é suficiente para verificar a identidade do utilizador. Esse processo também define um cookie de sessão que pode ser usado para identificar o usuário nas solicitações de página subsequentes.

Para ver esse cenário em ação, experimente um dos exemplos de código de entrada do aplicativo Web em nossa [seção Introdução](active-directory-b2c-overview.md).

Além de facilitar o logon simples, um aplicativo de servidor Web também pode precisar acessar um serviço Web de back-end. Nesse caso, o aplicativo Web pode executar um [fluxo de OpenID Connect](active-directory-b2c-reference-oidc.md) ligeiramente diferente e adquirir tokens usando códigos de autorização e tokens de atualização. Este cenário é descrito na seguinte [secção APIs da Web](#web-apis).

## <a name="web-apis"></a>APIs da Web

Você pode usar Azure AD B2C para proteger serviços Web, como a API Web RESTful do seu aplicativo. As APIs Web podem utilizar o OAuth 2.0 para proteger os seus dados, ao autenticar pedidos HTTP recebidos utilizando tokens. O autor da chamada de uma API web acrescenta um token no cabeçalho de autorização de um pedido HTTP:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

A API web pode utilizar o token para verificar a identidade do autor da chamada da API e extrair informações sobre o autor da chamada com base nas afirmações codificadas no token. Saiba mais sobre os tipos de tokens e afirmações disponíveis para uma aplicação na [referência de token do Azure AD B2C](active-directory-b2c-reference-tokens.md).

Uma API da Web pode receber tokens de muitos tipos de clientes, incluindo aplicativos Web, aplicativos móveis e de área de trabalho, aplicativos de página única, daemons do lado do servidor e outras APIs da Web. Aqui está um exemplo do fluxo completo para um aplicativo Web que chama uma API da Web:

1. O aplicativo Web executa uma política e o usuário conclui a experiência do usuário.
2. Azure ad B2C retorna um (OpenID Connect) `id_token` e um código de autorização para o navegador.
3. O navegador posta o `id_token` código de autorização e para o URI de redirecionamento.
4. O servidor Web valida o `id_token` e define um cookie de sessão.
5. O servidor Web solicita Azure ad B2C para um `access_token` fornecendo-o com o código de autorização, a ID do cliente do aplicativo e as credenciais do cliente.
6. O `access_token` e`refresh_token` são retornados ao servidor Web.
7. A API da Web é chamada com `access_token` o em um cabeçalho de autorização.
8. A API da Web valida o token.
9. Dados seguros são retornados para o aplicativo Web.

Para obter mais informações sobre códigos de autorização, tokens de atualização e os passos para obter os tokens, leia sobre o [protocolo OAuth 2.0](active-directory-b2c-reference-oauth-code.md).

Para saber como proteger uma API web utilizando o Azure AD B2C, consulte os tutoriais sobre web API na nossa [secção Introdução](active-directory-b2c-overview.md).

## <a name="mobile-and-native-applications"></a>Aplicativos móveis e nativos

Os aplicativos instalados em dispositivos, como aplicativos móveis e de área de trabalho, geralmente precisam acessar serviços de back-end ou APIs da Web em nome dos usuários. Você pode adicionar experiências personalizadas de gerenciamento de identidades a seus aplicativos nativos e chamar com segurança serviços de back-end usando Azure AD B2C e o [fluxo de código de autorização do OAuth 2,0](active-directory-b2c-reference-oauth-code.md).

Nesse fluxo, o aplicativo executa [políticas](active-directory-b2c-reference-policies.md) e recebe um `authorization_code` do Azure ad depois que o usuário conclui a política. O `authorization_code` representa a permissão do aplicativo para chamar serviços de back-end em nome do usuário que está conectado no momento. Em seguida, o aplicativo pode `authorization_code` trocar o em segundo plano `access_token` por um `refresh_token`e um.  O aplicativo pode usar o `access_token` para autenticar para uma API Web de back-end em solicitações HTTP. Também pode utilizar o `refresh_token` para obter um novo `access_token` quando o antigo expira.

## <a name="current-limitations"></a>Limitações atuais

### <a name="unsupported-application-types"></a>Tipos de aplicativos sem suporte

#### <a name="daemonsserver-side-applications"></a>Daemons/aplicativos do lado do servidor

Os aplicativos que contêm processos de execução longa ou que operam sem a presença de um usuário também precisam de uma maneira de acessar recursos protegidos, como APIs da Web. Esses aplicativos podem autenticar e obter tokens usando a identidade do aplicativo (em vez da identidade delegada de um usuário) e usando o fluxo de credenciais do cliente OAuth 2,0. O fluxo de credenciais do cliente não é o mesmo que o fluxo em nome e o em nome do fluxo não deve ser usado para autenticação de servidor para servidor.

Embora o fluxo de credenciais do cliente não seja suportado atualmente pelo Azure AD B2C, você pode configurar o fluxo de credenciais do cliente usando o Azure AD. Um locatário Azure AD B2C compartilha algumas funcionalidades com locatários do Azure AD Enterprise.  O fluxo de credenciais do cliente tem suporte usando a funcionalidade do Azure AD do locatário do Azure AD B2C.

Para configurar o fluxo de credenciais do cliente, consulte [Azure Active Directory v 2.0 e o fluxo de credenciais do cliente OAuth 2,0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols-oauth-client-creds). Uma autenticação bem-sucedida resulta no recebimento de um token formatado para que ele possa ser usado pelo Azure AD, conforme descrito em [referência de token do Azure ad](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).

#### <a name="web-api-chains-on-behalf-of-flow"></a>Cadeias de API Web (fluxo em-nome-de)

Muitas arquiteturas incluem uma API web que precisa chamar outra API web a jusante, estando ambas protegidas pelo Azure AD B2C. Esse cenário é comum em clientes nativos que têm um back-end da API Web e chama um serviço online da Microsoft, como o API do Graph do Azure AD.

Este cenário de API web em cadeia pode ser suportado utilizando a concessão de credencial de portador do OAuth 2.0 JWT, também conhecido como fluxo em-nome-de.  No entanto, o fluxo em nome de não está atualmente implementado no Azure AD B2C.

### <a name="faulted-apps"></a>Aplicações com falha

Não edite aplicativos Azure AD B2C das seguintes maneiras:

- Em outros portais de gerenciamento de aplicativos, como o [portal de registro de aplicativos](https://apps.dev.microsoft.com/).
- Usando API do Graph ou PowerShell.

Se você editar o aplicativo Azure AD B2C fora do portal do Azure, ele se tornará um aplicativo com falha e não poderá mais ser usado com Azure AD B2C. Exclua o aplicativo e crie-o novamente.

Para excluir o aplicativo, vá para o [portal de registro de aplicativos](https://apps.dev.microsoft.com/) e exclua o aplicativo. Para que a aplicação fique visível, tem de ser o proprietário da aplicação (e não apenas um administrador do inquilino).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as políticas internas fornecidas pelos [fluxos de usuário no Azure Active Directory B2C](active-directory-b2c-reference-policies.md).
