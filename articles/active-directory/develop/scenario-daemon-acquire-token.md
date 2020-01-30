---
title: Adquirir tokens para chamar uma API Web (aplicativo daemon)-plataforma de identidade da Microsoft | Azure
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
ms.openlocfilehash: b2d388160c6ca744b10c17bda17c59e22940f98b
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76775246"
---
# <a name="daemon-app-that-calls-web-apis---acquire-a-token"></a>Aplicativo daemon que chama APIs da Web – adquirir um token

Depois de ter construído uma aplicação de cliente confidencial, pode adquirir um símbolo para a app, ligando para `AcquireTokenForClient`, passando o âmbito e forçando opcionalmente uma atualização do símbolo.

## <a name="scopes-to-request"></a>Escopos a serem solicitados

O escopo a ser solicitado para um fluxo de credenciais de cliente é o nome do recurso seguido por `/.default`. Esta notação indica ao Azure Ative Directory (Azure AD) que utilize as *permissões de nível de aplicação declaradas* estáticadurante o registo da aplicação. Além disso, estas permissões da API devem ser concedidas por um administrador inquilino.

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Em MSAL Python, o ficheiro de configuração parece este código:

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

### <a name="azure-ad-v10-resources"></a>Recursos Azure AD (v1.0)

O âmbito utilizado para as credenciais do cliente deve ser sempre o ID de recurso seguido de `/.default`.

> [!IMPORTANT]
> Quando a MSAL solicita um sinal de acesso para um recurso que aceite um token de acesso à versão 1.0, a Azure AD analisa o público desejado do âmbito solicitado, tomando tudo antes do último corte e usando-o como identificador de recursos.
> Assim, se, tal como a Base de Dados Azure SQL **(https:\//database.windows.net),** o recurso espera um público que termine com um corte (para a Base de Dados Azure SQL, `https://database.windows.net/`), terá de solicitar um âmbito de `https://database.windows.net//.default`. (Note o duplo corte.) Consulte também MSAL.NET problema [#747: O corte de rasto do url de recursos é omitido, o que causou falha no sql auth](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747).

## <a name="acquiretokenforclient-api"></a>API AcquireTokenForClient

Para adquirir um símbolo para a app, utilizará `AcquireTokenForClient` ou o seu equivalente, dependendo da plataforma.

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

```csharp
using Microsoft.Identity.Client;

// With client credentials flows, the scope is always of the shape "resource/.default" because the
// application permissions need to be set statically (in the portal or by PowerShell), and then granted by
// a tenant administrator.
string[] scopes = new string[] { "https://graph.microsoft.com/.default" };

AuthenticationResult result = null;
try
{
 result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
    // The application doesn't have sufficient permissions.
    // - Did you declare enough app permissions during app creation?
    // - Did the tenant admin grant permissions to the application?
}
catch (MsalServiceException ex) when (ex.Message.Contains("AADSTS70011"))
{
    // Invalid scope. The scope has to be in the form "https://resourceurl/.default"
    // Mitigation: Change the scope to be as expected.
}
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
# The pattern to acquire a token looks like this.
result = None

# First, the code looks up a token from the cache.
# Because we're looking for a token for the current app, not for a user,
# use None for the account parameter.
result = app.acquire_token_silent(config["scope"], account=None)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    result = app.acquire_token_for_client(scopes=config["scope"])

if "access_token" in result:
    # Call a protected API with the access token.
    print(result["token_type"])
else:
    print(result.get("error"))
    print(result.get("error_description"))
    print(result.get("correlation_id"))  # You might need this when reporting a bug.
```

# <a name="javatabjava"></a>[Java](#tab/java)

Este código é extraído das [amostras MSAL Java dev](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/confidential-client/).

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

    /* Call a protected API with res.accessToken() */
};

future.whenCompleteAsync(processAuthResult);
future.join();
```

---

### <a name="protocol"></a>Protocolo

Se ainda não tem uma biblioteca para a sua língua escolhida, talvez queira usar o protocolo diretamente:

#### <a name="first-case-access-the-token-request-by-using-a-shared-secret"></a>Primeiro caso: Aceda ao pedido simbólico utilizando um segredo partilhado

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity.
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

#### <a name="second-case-access-the-token-request-by-using-a-certificate"></a>Segundo caso: Aceda ao pedido simbólico utilizando um certificado

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity.
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

Em MSAL.NET, `AcquireTokenForClient` usa a cache token da aplicação. (Todos os outros métodos AcquireToken*XX* utilizam a cache token do utilizador.) Não ligue `AcquireTokenSilent` antes de ligar para `AcquireTokenForClient`, porque `AcquireTokenSilent` usa a cache token do *utilizador.* `AcquireTokenForClient` verifica o cache do token de *aplicativo* e o atualiza.

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="did-you-use-the-resourcedefault-scope"></a>Você usou o escopo de recurso/. padrão?

Se você receber uma mensagem de erro informando que usou um escopo inválido, provavelmente não usou o escopo de `resource/.default`.

### <a name="did-you-forget-to-provide-admin-consent-daemon-apps-need-it"></a>Você se esqueceu de fornecer consentimento de administrador? Aplicativos de daemon precisam!

Se obtém um **privilégio insuficiente para completar o** erro de operação quando liga para a API, o administrador do inquilino precisa de conceder permissões ao pedido. Consulte a etapa 6 de registrar o aplicativo cliente acima.
Normalmente verá um erro que se parece com este erro:

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
