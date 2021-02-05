---
title: Âmbitos para aplicações v1.0 (MSAL) | Rio Azure
description: Saiba mais sobre os âmbitos de uma aplicação v1.0 utilizando a Biblioteca de Autenticação do Microsoft (MSAL).
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
ms.openlocfilehash: 7e2fcf2dc0dc53038b82bbf182cb12f580d88357
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99583591"
---
# <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>Âmbitos para uma API web aceitando fichas v1.0

As permissões OAuth2 são âmbitos de permissão que um Diretório Ativo Azure (AD) para desenvolvedores (v1.0) web API (recurso) expõe a aplicações do cliente. Estes âmbitos de permissão podem ser concedidos aos pedidos do cliente durante o consentimento. Consulte a secção sobre `oauth2Permissions` a referência manifesto do [Azure Ative Directory](reference-app-manifest.md#manifest-reference).

## <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>Âmbitos para solicitar acesso a permissões específicas de OAuth2 de uma aplicação v1.0

Adquirir fichas para âmbitos específicos de uma aplicação v1.0 (por exemplo, a Microsoft Graph API, que https://graph.microsoft.com) é, criar âmbitos através da concatenação de um identificador de recursos pretendido com uma permissão OAuth2 desejada para esse recurso.

Por exemplo, para aceder em nome do utilizador a V1.0 web API onde a aplicação ID URI `ResourceId` é:

```csharp
var scopes = new [] {  ResourceId+"/user_impersonation"};
```

```javascript
var scopes = [ ResourceId + "/user_impersonation"];
```

Para ler e escrever com MSAL.NET AD AZure utilizando a API do Gráfico da Microsoft (https: \/ /graph.microsoft.com/), crie uma lista de âmbitos como mostrado nos seguintes exemplos:

```csharp
string ResourceId = "https://graph.microsoft.com/";
var scopes = new [] { ResourceId + "Directory.Read", ResourceID + "Directory.Write"}
```

```javascript
var ResourceId = "https://graph.microsoft.com/";
var scopes = [ ResourceId + "Directory.Read", ResourceID + "Directory.Write"];
```

Para escrever o âmbito correspondente à AZure Resource Manager API (https: \/ /management.core.windows.net/), solicite o seguinte âmbito (nota as duas barras):

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

> [!NOTE]
> Use duas barras porque a AZure Resource Manager API espera um corte na sua reivindicação de audiência (aud), e depois há um corte para separar o nome API do âmbito.

A lógica utilizada pela Azure AD é a seguinte:

- Para o ponto final ADAL (Azure AD v1.0) com um token de acesso v1.0 (o único possível), aud=recurso
- Para a MSAL (plataforma de identidade da Microsoft) pedir um token de acesso para um recurso que aceita tokens v2.0, `aud=resource.AppId`
- Para o MSAL (ponto final v2.0) pedir um token de acesso para um recurso que aceita um token de acesso v1.0 (o que é o caso acima), a Azure AD analisa o público desejado do âmbito solicitado, tomando tudo antes do último corte e usando-o como identificador de recursos. Por isso, se https: \/ /database.windows.net espera uma audiência de "https: \/ /database.windows.net/", terá de solicitar um âmbito de "https: \/ /database.windows.net//.default". Consulte também a edição do GitHub [#747: O corte de rastos do url de recursos é omitido, o que causou uma falha de auth sql](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747).

## <a name="scopes-to-request-access-to-all-the-permissions-of-a-v10-application"></a>Âmbitos para solicitar acesso a todas as permissões de uma aplicação v1.0

Se pretender adquirir um símbolo para todos os âmbitos estáticos de uma aplicação v1.0, apendam ".predefinido" à aplicação ID URI da API:

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

```javascript
var ResourceId = "someAppIDURI";
var scopes = [ ResourceId + "/.default"];
```

## <a name="scopes-to-request-for-a-client-credential-flowdaemon-app"></a>Âmbitos para solicitar uma aplicação de fluxo/daemon credencial do cliente

No caso do fluxo de credencial do cliente, o âmbito de passagem seria também `/.default` . Isto diz ao Azure AD: "todas as permissões ao nível da aplicação a que o administrador consentiu no registo da aplicação.
