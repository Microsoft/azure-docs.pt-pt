---
title: Escopos para um aplicativo v 1.0 (biblioteca de autenticação da Microsoft) | Azure
description: Saiba mais sobre os escopos para um aplicativo v 1.0 usando a MSAL (biblioteca de autenticação da Microsoft).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/23/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 17837c6f4d1b3c690c39c9f99ca4896fcce16b00
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68834916"
---
# <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>Escopos para uma API Web aceitando tokens v 1.0

As permissões OAuth2 são escopos de permissão que um aplicativo de API (recurso) da Web do Azure AD para desenvolvedores (v 1.0) expõe para aplicativos cliente. Esses escopos de permissão podem ser concedidos a aplicativos cliente durante o consentimento. Consulte a seção sobre `oauth2Permissions` na [referência do manifesto do aplicativo Azure Active Directory](reference-app-manifest.md#manifest-reference).

## <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>Escopos para solicitar acesso a permissões específicas de OAuth2 de um aplicativo v 1.0
Se você quiser adquirir tokens para escopos específicos de um aplicativo v 1.0 (por exemplo, o Azure ad Graph, que é\/https:/Graph.Windows.net), precisará criar escopos concatenando um identificador de recurso desejado com uma permissão OAuth2 desejada para esse recurso.

Por exemplo, para acessar em nome do usuário uma API Web v 1.0 em que o URI da ID do `ResourceId`aplicativo é:

```csharp
var scopes = new [] {  ResourceId+"/user_impersonation"};
```

```javascript
var scopes = [ ResourceId + "/user_impersonation"];
```

Se você quiser ler e gravar com MSAL.net Azure Active Directory usando a API do Graph do Azure AD (https\/:/Graph.Windows.net/), crie uma lista de escopos como no seguinte:

```csharp
string ResourceId = "https://graph.windows.net/";
var scopes = new [] { ResourceId + "Directory.Read", ResourceID + "Directory.Write"}
```

```javascript
var ResourceId = "https://graph.windows.net/";
var scopes = [ ResourceId + "Directory.Read", ResourceID + "Directory.Write"];
```

Se você quiser gravar o escopo correspondente à API de Azure Resource Manager (https:\//Management.Core.Windows.net/), precisará solicitar o seguinte escopo (Observe as duas barras):

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

> [!NOTE]
> Você precisa usar duas barras porque a API Azure Resource Manager espera uma barra em sua identidade de público (AUD) e, em seguida, há uma barra para separar o nome da API do escopo.

A lógica usada pelo Azure AD é a seguinte:

- Para o ponto de extremidade ADAL (v 1.0) com um token de acesso v 1.0 (o único possível), AUD = recurso
- Para MSAL (ponto de extremidade da plataforma Microsoft Identity (v 2.0)) que solicita um token de acesso para um recurso que aceita tokens v 2.0, AUD = Resource. AppId
- Para MSAL (ponto de extremidade v 2.0) que solicita um token de acesso para um recurso que aceita um token de acesso v 1.0 (que é o caso acima), o Azure AD analisa o público-alvo desejado do escopo solicitado, levando tudo antes da última barra e usando-o como o identificador de recurso. Portanto, se https\/:/Database.Windows.net espera um público de "https\/:/Database.Windows.net/", você precisará solicitar um escopo de "https:\//Database.Windows.net//.default". Consulte também o problema [do GitHub #747: A barra à direita da URL do recurso é omitida, o que](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747)causou falha na autenticação do SQL.

## <a name="scopes-to-request-access-to-all-the-permissions-of-a-v10-application"></a>Escopos para solicitar acesso a todas as permissões de um aplicativo v 1.0
Se você quiser adquirir um token para todos os escopos estáticos de um aplicativo v 1.0, acrescente ". default" ao URI da ID do aplicativo da API:

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

```javascript
var ResourceId = "someAppIDURI";
var scopes = [ ResourceId + "/.default"];
```

## <a name="scopes-to-request-for-client-credential-flow--daemon-app"></a>Escopos a serem solicitados para o aplicativo fluxo de credenciais do cliente/daemon
No caso do fluxo de credenciais do cliente, o escopo a ser `/.default`aprovado também seria. Isso diz ao Azure AD: "todas as permissões de nível de aplicativo que o administrador consentiu no registro do aplicativo.
