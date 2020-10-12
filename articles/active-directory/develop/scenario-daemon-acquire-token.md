---
title: Adquira fichas para chamar uma API web (app Daemon) - Plataforma de identidade da Microsoft Rio Azure
description: Saiba como construir uma app daemon que chama APIs web (aquisição de fichas)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 13000c5a61dc2c4d49aa395271beddef64d32245
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88119220"
---
# <a name="daemon-app-that-calls-web-apis---acquire-a-token"></a>Daemon app que chama APIs web - adquira um token

Depois de ter construído uma aplicação confidencial para o cliente, pode adquirir um token para a app, através da `AcquireTokenForClient` chamada, passando o âmbito e, opcionalmente, forçando uma atualização do token.

## <a name="scopes-to-request"></a>Âmbitos a solicitar

O âmbito de pedido de um fluxo de credencial do cliente é o nome do recurso seguido por `/.default` . Esta notação diz ao Azure Ative Directory (Azure AD) para utilizar as *permissões de nível de aplicação declaradas* estáticas durante o registo da aplicação. Além disso, estas permissões da API devem ser concedidas por um administrador de inquilinos.

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

# <a name="python"></a>[Python](#tab/python)

No MSAL Python, o ficheiro de configuração parece este corte de código:

```Json
{
    "scope": ["https://graph.microsoft.com/.default"],
}
```

# <a name="java"></a>[Java](#tab/java)

```Java
final static String GRAPH_DEFAULT_SCOPE = "https://graph.microsoft.com/.default";
```

---

### <a name="azure-ad-v10-resources"></a>Recursos Azure AD (v1.0)

O âmbito utilizado para as credenciais dos clientes deve ser sempre o ID de recursos seguido por `/.default` .

> [!IMPORTANT]
> Quando a MSAL solicita um token de acesso para um recurso que aceita um token de acesso versão 1.0, a Azure AD analisa o público desejado a partir do âmbito solicitado, tomando tudo antes do último corte e usando-o como identificador de recursos.
> Assim, se, tal como a Base de Dados Azure SQL **(https: \/ /database.windows.net),** o recurso espera uma audiência que termine com um corte (para a Base de Dados Azure SQL, `https://database.windows.net/` terá de solicitar um âmbito de `https://database.windows.net//.default` . (Note o corte duplo.) Consulte também MSAL.NET edição [#747: O corte de rastos do url de recursos é omitido, o que causou uma falha de auth sql](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747).

## <a name="acquiretokenforclient-api"></a>AcquireTokenForClient API

Para adquirir um token para a app, você vai usar `AcquireTokenForClient` ou seu equivalente, dependendo da plataforma.

# <a name="net"></a>[.NET](#tab/dotnet)

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

# <a name="python"></a>[Python](#tab/python)

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

# <a name="java"></a>[Java](#tab/java)

Este código é extraído das [amostras dev MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/confidential-client/).

```Java
private static IAuthenticationResult acquireToken() throws Exception {

     // Load token cache from file and initialize token cache aspect. The token cache will have
     // dummy data, so the acquireTokenSilently call will fail.
     TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

     IClientCredential credential = ClientCredentialFactory.createFromSecret(CLIENT_SECRET);
     ConfidentialClientApplication cca =
             ConfidentialClientApplication
                     .builder(CLIENT_ID, credential)
                     .authority(AUTHORITY)
                     .setTokenCacheAccessAspect(tokenCacheAspect)
                     .build();

     IAuthenticationResult result;
     try {
         SilentParameters silentParameters =
                 SilentParameters
                         .builder(SCOPE)
                         .build();

         // try to acquire token silently. This call will fail since the token cache does not
         // have a token for the application you are requesting an access token for
         result = cca.acquireTokenSilently(silentParameters).join();
     } catch (Exception ex) {
         if (ex.getCause() instanceof MsalException) {

             ClientCredentialParameters parameters =
                     ClientCredentialParameters
                             .builder(SCOPE)
                             .build();

             // Try to acquire a token. If successful, you should see
             // the token information printed out to console
             result = cca.acquireToken(parameters).join();
         } else {
             // Handle other exceptions accordingly
             throw ex;
         }
     }
     return result;
 }
```

---

### <a name="protocol"></a>Protocolo

Se ainda não tem uma biblioteca para o seu idioma escolhido, talvez queira utilizar o protocolo diretamente:

#### <a name="first-case-access-the-token-request-by-using-a-shared-secret"></a>Primeiro caso: Aceda ao pedido simbólico utilizando um segredo partilhado

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity.
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

#### <a name="second-case-access-the-token-request-by-using-a-certificate"></a>Segundo caso: Aceda ao pedido simbólico utilizando um certificado

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity.
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

Para obter mais informações, consulte a documentação do protocolo: [plataforma de identidade da Microsoft e o fluxo de credenciais de cliente OAuth 2.0](v2-oauth2-client-creds-grant-flow.md).

## <a name="application-token-cache"></a>Cache de ficha de aplicação

Em MSAL.NET, `AcquireTokenForClient` utiliza a cache simbólica de aplicação. (Todos os outros métodos AcquireToken*XX* utilizam a cache de ficha do utilizador.) Não ligue `AcquireTokenSilent` antes de `AcquireTokenForClient` ligar, porque `AcquireTokenSilent` utiliza o cache de ficha de *utilizador.* `AcquireTokenForClient` verifica a própria cache simbólica da *aplicação* e atualiza-a.

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="did-you-use-the-resourcedefault-scope"></a>Usou o âmbito de recurso/.predefinido?

Se receber uma mensagem de erro a dizer-lhe que usou um âmbito inválido, provavelmente não usou o `resource/.default` âmbito.

### <a name="did-you-forget-to-provide-admin-consent-daemon-apps-need-it"></a>Esqueceu-se de dar consentimento administrativo? As aplicações da Daemon precisam!

Se tiver **um privilégio insuficiente para completar o** erro de funcionamento quando ligar para a API, o administrador do arrendatário precisa de conceder permissões ao pedido. Consulte o passo 6 do Registo da aplicação do cliente acima.
Normalmente verá um erro que se parece com este erro:

```json
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

# <a name="net"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [Daemon app - chamando uma web API](./scenario-daemon-call-api.md?tabs=dotnet)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Daemon app - chamando uma web API](./scenario-daemon-call-api.md?tabs=python)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Daemon app - chamando uma web API](./scenario-daemon-call-api.md?tabs=java)

---