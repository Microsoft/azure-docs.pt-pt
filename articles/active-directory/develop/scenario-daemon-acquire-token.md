---
title: APIs (aquisição de tokens para a aplicação) - de web de chamar de aplicação de daemon, plataforma de identidade da Microsoft
description: Saiba como criar uma aplicação de daemon que chamadas de web APIs (aquisição de tokens)
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
ms.openlocfilehash: aa4f5dc7a5aceaf81f71eacd36d131471a57e5c0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65075374"
---
# <a name="daemon-app-that-calls-web-apis---acquire-a-token"></a>Aplicação de daemon que chama o web APIs - adquirir um token

Assim que a aplicação de cliente confidencial é construída, pode adquirir um token para a aplicação ao chamar ``AcquireTokenForClient``, passando o âmbito e forçar ou não uma atualização do token.

## <a name="scopes-to-request"></a>Âmbitos de pedido

O âmbito para pedir para um fluxo de credenciais de cliente é o nome do recurso é seguido `/.default`. Este notação diz ao Azure AD para utilizar o **permissões ao nível do aplicativo** declarado estaticamente durante o registo de aplicação. Além disso, como visto anteriormente, estas API devem ser concedidas permissões por um administrador de inquilino

### <a name="net"></a>.NET

```CSharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

### <a name="python"></a>Python

No MSAL. O ficheiro de configuração seria Python, como o seguinte fragmento de código:

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

O âmbito utilizado para credenciais de cliente deve ser sempre resourceId + "/ seção"

### <a name="case-of-v10-resources"></a>Caso de recursos de v1.0

> [!IMPORTANT]
> Para MSAL (ponto final v2.0) solicitar um token de acesso a um recurso de aceitar um token de acesso da versão 1.0, o Azure AD analisa o público-alvo pretendido de âmbito do pedido Considerando tudo o que antes da última barra e utilizá-lo como o identificador de recurso.
> Portanto, se, como o Azure SQL ( **https://database.windows.net** ) o recurso espera um público-alvo que termina com uma barra (para o SQL do Azure: `https://database.windows.net/` ), terá de solicitar um escopo de `https://database.windows.net//.default` (Observe duas barras). Consulte também MSAL.NET problema [#747](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747): Barra do url de recurso for omitida, o que causou a falha de autenticação do sql.

## <a name="acquiretokenforclient-api"></a>AcquireTokenForClient API

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

#### <a name="application-token-cache"></a>Cache de tokens de aplicação

No MSAL.NET, `AcquireTokenForClient` utiliza o **cache de tokens de aplicação** (todos os outros métodos de AcquireTokenXX utilizam a cache de token de utilizador) não chamar `AcquireTokenSilent` antes de chamar `AcquireTokenForClient` como `AcquireTokenSilent` utiliza o **utilizador** cache de token. `AcquireTokenForClient` verifica a **aplicativo** token em cache em si e atualiza-o.

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

Se não tiver ainda uma biblioteca para a linguagem de sua escolha, pode querer utilizar o protocolo diretamente:

#### <a name="first-case-access-token-request-with-a-shared-secret"></a>Primeiro caso: Pedido de token de acesso com um segredo partilhado

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

#### <a name="second-case-access-token-request-with-a-certificate"></a>Segundo caso: Pedido de token de acesso com um certificado

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

Para obter mais informações, consulte a documentação do protocolo: [Fluxo de credenciais do Azure Active Directory v 2.0 e o cliente de OAuth 2.0](v2-oauth2-client-creds-grant-flow.md).

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="did-you-use-the-resourcedefault-scope"></a>Usou o âmbito de recursos/seção?

Se obtiver uma mensagem de erro informando que usou um âmbito inválido, provavelmente não usa o `resource/.default` âmbito.

### <a name="did-you-forget-to-provide-admin-consent-daemon-apps-need-it"></a>Esqueceu-se fornecer o consentimento de administrador? Aplicações daemon precisam!

Se obtiver um erro ao chamar a API **privilégios insuficientes para concluir a operação**, o administrador de inquilino tem de conceder permissões à aplicação. Consulte o passo 6 do Registre-se a aplicação de cliente acima.
Normalmente, verá e erro, como a descrição de erro seguinte:

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

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Aplicação de daemon - chamar uma API web](scenario-daemon-call-api.md)