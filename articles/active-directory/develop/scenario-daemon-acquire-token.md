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
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4a5a3ac1438d5e958317f1899fc6c447f5c149ac
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175511"
---
# <a name="daemon-app-that-calls-web-apis---acquire-a-token"></a>Aplicativo daemon que chama APIs da Web – adquirir um token

Depois que o aplicativo cliente confidencial é construído, você pode adquirir um token para o aplicativo chamando ``AcquireTokenForClient``, passando o escopo e forçando ou não uma atualização do token.

## <a name="scopes-to-request"></a>Escopos a serem solicitados

O escopo a ser solicitado para um fluxo de credenciais de cliente é o nome do recurso seguido por `/.default`. Essa notação informa ao Azure AD para usar as **permissões de nível de aplicativo** declaradas estaticamente durante o registro do aplicativo. Além disso, como visto anteriormente, essas permissões de API devem ser concedidas por um administrador de locatários

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

```CSharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

No MSAL Python, o arquivo de configuração se pareceria com o seguinte trecho de código:

```Json
{
    "scope": ["https://graph.microsoft.com/.default"],
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

```Java
final static String GRAPH_DEFAULT_SCOPE = "https://graph.microsoft.com/.default";
```

---

### <a name="case-of-azure-ad-v10-resources"></a>Casos de recursos do Azure AD (v 1.0)

O escopo usado para credenciais do cliente sempre deve ser ResourceId + "/.default"

> [!IMPORTANT]
> Para MSAL que solicitam um token de acesso para um recurso que aceita um token de acesso v 1.0, o Azure AD analisa o público-alvo desejado do escopo solicitado, levando tudo antes da última barra e usando-o como o identificador de recurso.
> Portanto, se, como o Azure SQL ( **https://database.windows.net** ), o recurso espera que um público termine com uma barra (para SQL do azure: `https://database.windows.net/` ), você precisará solicitar um escopo de `https://database.windows.net//.default` (Observe a barra dupla). Consulte também MSAL.NET Issue [#747](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747): a barra à direita da URL do recurso é omitida, o que causou a falha de autenticação do SQL.

## <a name="acquiretokenforclient-api"></a>API AcquireTokenForClient

Para adquirir um token para o aplicativo, você usará `AcquireTokenForClient` ou o equivalente, dependendo das plataformas.

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

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

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
# The pattern to acquire a token looks like this.
result = None

# Firstly, looks up a token from cache
# Since we are looking for token for the current app, NOT for an end user,
# notice we give account parameter as None.
result = app.acquire_token_silent(config["scope"], account=None)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    result = app.acquire_token_for_client(scopes=config["scope"])

if "access_token" in result:
    # Call a protected API with the access token
    print(result["token_type"])
else:
    print(result.get("error"))
    print(result.get("error_description"))
    print(result.get("correlation_id"))  # You may need this when reporting a bug
```

# <a name="javatabjava"></a>[Java](#tab/java)

Esta é uma extração dos [exemplos de desenvolvimento do MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/confidential-client/).

```Java
ClientCredentialParameters clientCredentialParam = ClientCredentialParameters.builder(
        Collections.singleton(GRAPH_DEFAULT_SCOPE))
        .build();

CompletableFuture<IAuthenticationResult> future = app.acquireToken(clientCredentialParam);

BiConsumer<IAuthenticationResult, Throwable> processAuthResult = (res, ex) -> {
    if (ex != null) {
        System.out.println("Oops! We have an exception - " + ex.getMessage());
    }
    System.out.println("Returned ok - " + res);
    System.out.println("ID Token - " + res.idToken());

    /* call a protected API with res.accessToken() */
};

future.whenCompleteAsync(processAuthResult);
future.join();
```

---

### <a name="protocol"></a>Protocolo

Se você ainda não tiver uma biblioteca para o idioma de sua escolha, talvez queira usar o protocolo diretamente:

#### <a name="first-case-access-token-request-with-a-shared-secret"></a>Primeiro caso: solicitação de token de acesso com um segredo compartilhado

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

#### <a name="second-case-access-token-request-with-a-certificate"></a>Segundo caso: solicitação de token de acesso com um certificado

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

Para obter mais informações, consulte a documentação do protocolo: [plataforma de identidade da Microsoft e o fluxo de credenciais do cliente OAuth 2,0](v2-oauth2-client-creds-grant-flow.md).

## <a name="application-token-cache"></a>Cache de token de aplicativo

No MSAL.NET, `AcquireTokenForClient` usa o **cache de token de aplicativo** (todos os outros métodos AcquireTokenXX usam o cache de token de usuário) não chamam `AcquireTokenSilent` antes de chamar `AcquireTokenForClient` conforme `AcquireTokenSilent` usa o cache de token de **usuário** . `AcquireTokenForClient` verifica o cache do token de **aplicativo** e o atualiza.

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="did-you-use-the-resourcedefault-scope"></a>Você usou o escopo de recurso/. padrão?

Se você receber uma mensagem de erro informando que usou um escopo inválido, provavelmente não usou o escopo de `resource/.default`.

### <a name="did-you-forget-to-provide-admin-consent-daemon-apps-need-it"></a>Você se esqueceu de fornecer consentimento de administrador? Aplicativos de daemon precisam!

Se você receber um erro ao chamar os **privilégios insuficientes da API para concluir a operação**, o administrador de locatários precisará conceder permissões ao aplicativo. Consulte a etapa 6 de registrar o aplicativo cliente acima.
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

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [Aplicativo de daemon – chamando uma API da Web](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=dotnet)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Aplicativo de daemon – chamando uma API da Web](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=python)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Aplicativo de daemon – chamando uma API da Web](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=java)

---
