---
title: Âmbitos para aplicações v1.0 (MSAL) / Azure
description: Conheça os âmbitos para uma aplicação v1.0 utilizando a Microsoft Authentication Library (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/25/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: d5b2ef57af112169fb39e0da7a60b095698ff504
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78299835"
---
# <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>Âmbitos para uma Web API aceitando fichas v1.0

As permissões OAuth2 são âmbitos de permissão que uma aplicação Azure Ative Directory (Azure AD) para programadores (v1.0) web API (recurso) expõe às aplicações do cliente. Estes âmbitos de autorização podem ser concedidos aos pedidos do cliente durante o consentimento. Consulte a `oauth2Permissions` secção sobre a referência manifesto de pedido do [Diretório Ativo Azure](reference-app-manifest.md#manifest-reference).

## <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>Âmbitos para solicitar acesso a permissões específicas da OAuth2 de um pedido v1.0

Para adquirir tokens para âmbitos específicos de uma aplicação v1.0 https://graph.microsoft.com)(por exemplo, a Microsoft Graph API, que é, criar âmbitos concatenando um identificador de recursos desejado com uma permissão OAuth2 desejada para esse recurso.

Por exemplo, aceder em nome do utilizador a uma API web v1.0 onde a aplicação ID URI é: `ResourceId`

```csharp
var scopes = new [] {  ResourceId+"/user_impersonation"};
```

```javascript
var scopes = [ ResourceId + "/user_impersonation"];
```

Para ler e escrever com MSAL.NET Azure AD utilizando\/o Microsoft Graph API (https: /graph.microsoft.com/), é necessário criar uma lista de âmbitos, como mostram os seguintes exemplos:

```csharp
string ResourceId = "https://graph.microsoft.com/";
var scopes = new [] { ResourceId + "Directory.Read", ResourceID + "Directory.Write"}
```

```javascript
var ResourceId = "https://graph.microsoft.com/";
var scopes = [ ResourceId + "Directory.Read", ResourceID + "Directory.Write"];
```

Para escrever o âmbito correspondente à API (https:\//management.core.windows.net/), é necessário solicitar o seguinte âmbito (note as duas barras):

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

> [!NOTE]
> É preciso usar duas barras porque a API, gestora de recursos do Azure, espera um corte na sua reivindicação de audiência (aud), e depois há um corte para separar o nome API do âmbito.

A lógica utilizada pela Azure AD é a seguinte:

- Para o ponto final ADAL (Azure AD v1.0) com um sinal de acesso v1.0 (o único possível), aud=recurso
- Para mSAL (plataforma de identidade microsoft (v2.0)) ponto final pedindo um sinal de acesso para um recurso que aceite fichas v2.0,`aud=resource.AppId`
- Para a MSAL (v2.0 endpoint) pedir um sinal de acesso a um recurso que aceite um sinal de acesso v1.0 (o que é o caso acima), a Azure AD analisa o público desejado do âmbito solicitado, tomando tudo antes do último corte e usando-o como identificador de recursos. Portanto, se https:\//database.windows.net espera uma\/audiência de "https: /database.windows.net/", terá de\/solicitar um âmbito de "https: /database.windows.net//.default". Consulte também a emissão do GitHub #747: O [corte de rasto do url de recursos é omitido, o que causou falha no sql auth](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747).

## <a name="scopes-to-request-access-to-all-the-permissions-of-a-v10-application"></a>Âmbitos para solicitar acesso a todas as permissões de um pedido v1.0

Se pretender adquirir um símbolo para todos os âmbitos estáticos de uma aplicação v1.0, apreenda ".default" à aplicação ID URI da API:

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

```javascript
var ResourceId = "someAppIDURI";
var scopes = [ ResourceId + "/.default"];
```

## <a name="scopes-to-request-for-a-client-credential-flowdaemon-app"></a>Âmbitos para solicitar uma aplicação de fluxo/daemon de crédito de clientes

No caso do fluxo de credenciais do cliente, `/.default`o âmbito a passar seria também. Isto diz à Azure AD: "todas as permissões ao nível da aplicação que o administrador consentiu no registo da candidatura.
