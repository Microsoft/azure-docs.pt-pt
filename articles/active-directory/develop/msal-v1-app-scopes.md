---
title: Âmbitos para uma aplicação de v1.0 (biblioteca de autenticação da Microsoft) | Azure
description: Saiba mais sobre os âmbitos para uma aplicação de v1.0 com o Microsoft Authentication Library (MSAL).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/23/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e43bc245a5908ba1bf91e7b4bee6df2f5cfc618
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67514361"
---
# <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>Âmbitos para uma API Web a aceitação de tokens de v1.0

Permissões de OAuth2 são âmbitos de permissões de um Azure AD para a aplicação de API (recurso) de web de programadores (v1.0) expõe para as aplicações cliente. Estes âmbitos de permissão podem ser concedidos às aplicações cliente durante o consentimento. Consulte a secção `oauth2Permissions` no [referência de manifesto de aplicação do Azure Active Directory](reference-app-manifest.md#manifest-reference).

## <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>Âmbitos para solicitar acesso a permissões de OAuth2 específicas de um aplicativo de v1.0
Se deseja adquirir tokens para âmbitos específicos de um aplicativo de v1.0 (por exemplo o Azure AD graph, que é https:\//graph.windows.net), tem de criar âmbitos concatenando um identificador de recurso pretendido com uma permissão de OAuth2 pretendido para esse recurso.

Por exemplo, para acesso em nome do utilizador de uma API da web v1.0 em que é a URI de ID de aplicação `ResourceId`:

```csharp
var scopes = new [] {  ResourceId+"/user_impersonation"};
```

```javascript
var scopes = [ ResourceId + "/user_impersonation"];
```

Se pretender ler e escrever com MSAL.NET do Azure Active Directory com a graph API do Azure AD (https:\//graph.windows.net/), criaria uma lista de âmbitos de tal como o seguinte:

```csharp
string ResourceId = "https://graph.windows.net/";
var scopes = new [] { ResourceId + "Directory.Read", ResourceID + "Directory.Write"}
```

```javascript
var ResourceId = "https://graph.windows.net/";
var scopes = [ ResourceId + "Directory.Read", ResourceID + "Directory.Write"];
```

Se quiser escrever o escopo correspondente para a API do Azure Resource Manager (https:\//management.core.windows.net/), tem de pedir ao seguinte âmbito (tenha em atenção as duas barras):

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

> [!NOTE]
> Tem de utilizar duas barras, porque a API do Azure Resource Manager espera uma barra na sua declaração de público-alvo (aud) e, em seguida, há uma barra para separar o nome da API do âmbito.

A lógica usada pelo Azure AD é o seguinte:

- Para o ponto final da ADAL (v1.0) com um token de v1.0 acesso (possível única), aud = recursos
- Para solicitar um token de acesso a um recurso de aceitação de tokens de v2.0, aud MSAL (Microsoft identity platform (v2.0) ponto final) = resource. AppId
- Para MSAL (ponto final v2.0) solicitar um token de acesso a um recurso de aceitar um token de acesso de v1.0 (que é o caso acima), o Azure AD analisa o público-alvo pretendido de âmbito do pedido Considerando tudo o que antes da última barra e utilizá-lo como o identificador de recurso. Portanto se https:\//database.windows.net espera um público-alvo de "https:\//database.windows.net/", terá de solicitar um escopo de "https:\//database.windows.net//.default". Consulte também GitHub problema [#747: Barra do url de recurso for omitida, o que causou a falha de autenticação de sql](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747).

## <a name="scopes-to-request-access-to-all-the-permissions-of-a-v10-application"></a>Âmbitos para solicitar acesso a todas as permissões de um aplicativo de v1.0
Se desejar adquirir um token para todos os âmbitos estáticos de um aplicativo de v1.0, acrescente "seção" para a URI de ID da API de aplicação:

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

```javascript
var ResourceId = "someAppIDURI";
var scopes = [ ResourceId + "/.default"];
```

## <a name="scopes-to-request-for-client-credential-flow--daemon-app"></a>Fluxo de credenciais de âmbitos para pedir para o cliente / aplicação daemon
No caso do fluxo de credenciais de cliente, o âmbito de passar também seria `/.default`. Isso informa ao Azure AD: "todas ao nível da aplicação permissões que o administrador já autorizou do registo de aplicação.
