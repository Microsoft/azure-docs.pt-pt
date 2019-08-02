---
title: Aplicativo de daemon chamando APIs Web (adquirindo tokens para o aplicativo)-plataforma de identidade da Microsoft
description: Saiba como criar um aplicativo daemon que chama APIs da Web (adquirindo Tokens)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a5f15aa5264c0abf87cb15f0468e8a3a924e0b5
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562353"
---
# <a name="daemon-app-that-calls-web-apis---acquire-a-token"></a>Aplicativo daemon que chama APIs da Web – adquirir um token

Depois que o aplicativo cliente confidencial é construído, você pode adquirir um token para o aplicativo chamando ``AcquireTokenForClient``, passando o escopo e forçando ou não uma atualização do token.

## <a name="scopes-to-request"></a>Escopos a serem solicitados

O escopo a ser solicitado para um fluxo de credenciais de cliente é o nome do recurso `/.default`seguido por. Essa notação informa ao Azure AD para usar as **permissões de nível de aplicativo** declaradas estaticamente durante o registro do aplicativo. Além disso, como visto anteriormente, essas permissões de API devem ser concedidas por um administrador de locatários

### <a name="net"></a>.NET

```CSharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

### <a name="python"></a>Python

Em MSAL. Python, o arquivo de configuração se pareceria com o seguinte trecho de código:

```Python
{
    "authority": "https://login.microsoftonline.com/organizations",
    "client_id": "your_client_id",
    "secret": "This is a sample only. You better NOT persist your password."
    "scope": ["https://graph.microsoft.com/.default"]
}
```

### <a name="java"></a>Java

```Java
public final static String KEYVAULT_DEFAULT_SCOPE = "https://vault.azure.net/.default";
```

### <a name="all"></a>Todos

O escopo usado para credenciais do cliente sempre deve ser ResourceId + "/.default"

### <a name="case-of-azure-ad-v10-resources"></a>Casos de recursos do Azure AD (v 1.0)

> [!IMPORTANT]
> Para MSAL (ponto de extremidade da plataforma de identidade da Microsoft) solicitando um token de acesso para um recurso que aceita um token de acesso v 1.0, o Azure AD analisa o público desejado do escopo solicitado, levando tudo antes da última barra e usando-o como o identificador de recurso.
> Portanto, se, como o Azure **https://database.windows.net** SQL (), o recurso espera que um público termine com uma barra ( `https://database.windows.net/` para SQL do Azure:), você precisará `https://database.windows.net//.default` solicitar um escopo de (Observe a barra dupla). Confira também MSAL.NET problema [#747](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747): A barra à direita da URL do recurso é omitida, o que causou falha na autenticação do SQL.

## <a name="acquiretokenforclient-api"></a>API AcquireTokenForClient

### <a name="net"></a>.NET

```CSharp
using Microsoft.Identity.Client;

// With client credentials flows the scopes is ALWAYS of the shape "resource/.default", as the
// application permissions need to be set statically (in the portal or by PowerShell), and then granted by
// a tenant administrator
string[] scopes = new string[] { "https://graph.microsoft.com/.default" };

AuthenticationResult result = null;
try
{
 result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
    // The application does not have sufficient permissions
    // - did you declare enough app permissions in during the app creation?
    // - did the tenant admin needs to grant permissions to the application.
}
catch (MsalServiceException ex) when (ex.Message.Contains("AADSTS70011"))
{
    // Invalid scope. The scope has to be of the form "https://resourceurl/.default"
    // Mitigation: change the scope to be as expected !
}
```

#### <a name="application-token-cache"></a>Cache de token de aplicativo

No MSAL.net, `AcquireTokenForClient` o usa o **cache de token de aplicativo** (todos os outros métodos AcquireTokenXX usam o cache de token `AcquireTokenSilent` de usuário `AcquireTokenForClient` ) não chamam antes de chamar como `AcquireTokenSilent` usa o cache de token de **usuário** . `AcquireTokenForClient`verifica o cache do token de **aplicativo** e o atualiza.

### <a name="python"></a>Python

```Python
# Firstly, looks up a token from cache
# Since we are looking for token for the current app, NOT for an end user,
# notice we give account parameter as None.
result = app.acquire_token_silent(config["scope"], account=None)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    result = app.acquire_token_for_client(scopes=config["scope"])
```

### <a name="java"></a>Java

```Java
ClientCredentialParameters parameters = ClientCredentialParameters
        .builder(Collections.singleton(KEYVAULT_DEFAULT_SCOPE))
        .build();

CompletableFuture<AuthenticationResult> future = cca.acquireToken(parameters);

// You can complete the future in many different ways. Here we use .get() for simplicity
AuthenticationResult result = future.get();
```

### <a name="protocol"></a>Protocol

Se você ainda não tiver uma biblioteca para o idioma de sua escolha, talvez queira usar o protocolo diretamente:

#### <a name="first-case-access-token-request-with-a-shared-secret"></a>Primeiro caso: Solicitação de token de acesso com um segredo compartilhado

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

#### <a name="second-case-access-token-request-with-a-certificate"></a>Segundo caso: Solicitação de token de acesso com um certificado

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

### <a name="learn-more-about-the-protocol"></a>Saiba mais sobre o protocolo

Para obter mais informações, consulte a documentação do protocolo: [Plataforma de identidade da Microsoft e o fluxo de credenciais do cliente OAuth 2,0](v2-oauth2-client-creds-grant-flow.md).

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="did-you-use-the-resourcedefault-scope"></a>Você usou o escopo de recurso/. padrão?

Se você receber uma mensagem de erro informando que usou um escopo inválido, provavelmente não usou o `resource/.default` escopo.

### <a name="did-you-forget-to-provide-admin-consent-daemon-apps-need-it"></a>Você se esqueceu de fornecer consentimento de administrador? Aplicativos de daemon precisam!

Se você receber um erro ao chamar os privilégios insuficientes da API **para concluir a operação**, o administrador de locatários precisará conceder permissões ao aplicativo. Consulte a etapa 6 de registrar o aplicativo cliente acima.
Você normalmente verá e erro como a seguinte descrição de erro:

```JSon
Failed to call the web API: Forbidden
Content: {
  "error": {
    "code": "Authorization_RequestDenied",
    "message": "Insufficient privileges to complete the operation.",
    "innerError": {
      "request-id": "<guid>",
      "date": "<date>"
    }
  }
}
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Aplicativo de daemon – chamando uma API da Web](scenario-daemon-call-api.md)