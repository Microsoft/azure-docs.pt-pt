---
title: Configure uma API web que chama APIs web | Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba como construir uma API web que chama APIs web (configuração de código da aplicação)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/26/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: d1ab7da9753642093505fa91ea76b9327612a6ac
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99582371"
---
# <a name="a-web-api-that-calls-web-apis-code-configuration"></a>Uma API web que chama APIs web: configuração de código

Depois de ter registado a sua API web, pode configurar o código da aplicação.

O código que utiliza para configurar a sua API web para que chame APIs web a jusante baseia-se no código que é usado para proteger uma API web. Para obter mais informações, consulte [API web protegida: configuração de aplicações](scenario-protected-web-api-app-configuration.md).

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

## <a name="microsoftidentityweb"></a>Microsoft.Identity.Web

A Microsoft recomenda que utilize o pacote [Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web) NuGet ao desenvolver uma API protegida ASP.NET Core chamando APIs web a jusante. Ver [API web protegido: configuração de código | Microsoft.Identity.Web](scenario-protected-web-api-app-configuration.md#microsoftidentityweb) para uma rápida apresentação dessa biblioteca no contexto de uma API web.

## <a name="client-secrets-or-client-certificates"></a>Segredos do cliente ou certificados de cliente

Dado que a sua API web agora chama uma API web a jusante, forneça um certificado secreto ou cliente do cliente no *appsettings.jsem* arquivo. Também pode adicionar uma secção que especifica:

- O URL da API da web a jusante
- Os âmbitos necessários para chamar a API

No exemplo seguinte, a `GraphBeta` secção especifica estas definições.

```JSON
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common"

   // To call an API
   "ClientSecret": "[Copy the client secret added to the app from the Azure portal]",
   "ClientCertificates": [
  ]
 },
 "GraphBeta": {
    "BaseUrl": "https://graph.microsoft.com/beta",
    "Scopes": "user.read"
    }
}
```

Em vez de um segredo de cliente, pode fornecer um certificado de cliente. O seguinte código snippet mostra usando um certificado armazenado em Azure Key Vault.

```JSON
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common"

   // To call an API
   "ClientCertificates": [
      {
        "SourceType": "KeyVault",
        "KeyVaultUrl": "https://msidentitywebsamples.vault.azure.net",
        "KeyVaultCertificateName": "MicrosoftIdentitySamplesCert"
      }
   ]
  },
  "GraphBeta": {
    "BaseUrl": "https://graph.microsoft.com/beta",
    "Scopes": "user.read"
  }
}
```

Microsoft.Identity.Web fornece várias formas de descrever certificados, tanto por configuração como por código. Para mais detalhes, consulte [Microsoft.Identity.Web wiki - Utilizando certificados](https://github.com/AzureAD/microsoft-identity-web/wiki/Using-certificates) no GitHub.

## <a name="startupcs"></a>Startup.cs

A sua API web terá de adquirir um símbolo para a API a jusante. Especifica-o adicionando a `.EnableTokenAcquisitionToCallDownstreamApi()` linha depois `.AddMicrosoftIdentityWebApi(Configuration)` . Esta linha expõe o `ITokenAcquisition` serviço, que pode utilizar nas ações do seu controlador/páginas. No entanto, como verás nos próximos dois pontos de bala, podes fazer ainda mais simples. Você também precisará escolher uma implementação de cache simbólico, por `.AddInMemoryTokenCaches()` exemplo, em *Startup.cs:*

```csharp
using Microsoft.Identity.Web;

public class Startup
{
  // ...
  public void ConfigureServices(IServiceCollection services)
  {
  // ...
  services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
          .AddMicrosoftIdentityWebApi(Configuration, Configuration.GetSection("AzureAd"))
            .EnableTokenAcquisitionToCallDownstreamApi()
            .AddInMemoryTokenCaches();
   // ...
  }
  // ...
}
```

Se você não quiser adquirir o token por si mesmo, *Microsoft.Identity.Web* fornece dois mecanismos para chamar uma API web a jusante de outra API. A opção que escolher depende se pretende ligar para o Microsoft Graph ou para outra API.

### <a name="option-1-call-microsoft-graph"></a>Opção 1: Ligue para o Gráfico da Microsoft

Se pretender ligar para o Microsoft Graph, o Microsoft.Identity.Web permite-lhe utilizar diretamente o `GraphServiceClient` (exposto pelo Microsoft Graph SDK) nas suas ações API. Para expor o Gráfico do Microsoft:

1. Adicione o pacote [Microsoft.Identity.Web.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph) NuGet ao seu projeto.
1. Adicione `.AddMicrosoftGraph()` depois no ficheiro `.EnableTokenAcquisitionToCallDownstreamApi()` *Startup.cs.* `.AddMicrosoftGraph()` tem vários sobreposições. Utilizando o sobreposição que toma uma secção de configuração como parâmetro, o código torna-se:

```csharp
using Microsoft.Identity.Web;

public class Startup
{
  // ...
  public void ConfigureServices(IServiceCollection services)
  {
  // ...
  services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
          .AddMicrosoftIdentityWebApi(Configuration, Configuration.GetSection("AzureAd"))
            .EnableTokenAcquisitionToCallDownstreamApi()
               .AddMicrosoftGraph(Configuration.GetSection("GraphBeta"))
            .AddInMemoryTokenCaches();
   // ...
  }
  // ...
}
```

### <a name="option-2-call-a-downstream-web-api-other-than-microsoft-graph"></a>Opção 2: Ligue para uma API web a jusante que não seja o Microsoft Graph

Para chamar uma API a jusante que não seja o Microsoft Graph, *o Microsoft.Identity.Web* fornece `.AddDownstreamWebApi()` , que solicita tokens e chama a API web a jusante.

```csharp
using Microsoft.Identity.Web;

public class Startup
{
  // ...
  public void ConfigureServices(IServiceCollection services)
  {
  // ...
  services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
          .AddMicrosoftIdentityWebApi(Configuration, "AzureAd")
            .EnableTokenAcquisitionToCallDownstreamApi()
               .AddDownstreamWebApi("MyApi", Configuration.GetSection("GraphBeta"))
            .AddInMemoryTokenCaches();
   // ...
  }
  // ...
}
```

Tal como acontece com as aplicações web, pode escolher várias implementações de cache simbólico. Para mais detalhes, consulte [a web de identidade da Microsoft - Serialization cache Token](https://aka.ms/ms-id-web/token-cache-serialization) no GitHub.

A imagem a seguir mostra as várias possibilidades do *Microsoft.Identity.Web* e o seu impacto no ficheiro *Startup.cs:*

:::image type="content" source="media/scenarios/microsoft-identity-web-startup-cs.svg" alt-text="Diagrama de bloco mostrando opções de configuração de serviço no ponto de arranque C S para chamar uma API web e especificar uma implementação de cache de símbolo":::

> [!NOTE]
> Para compreender plenamente os exemplos de código aqui, conheça [os fundamentos ASP.NET Core](/aspnet/core/fundamentals), e em particular com a injeção de [dependência](/aspnet/core/fundamentals/dependency-injection) e [opções.](/aspnet/core/fundamentals/configuration/options)

# <a name="java"></a>[Java](#tab/java)

O fluxo On-behalf-of (OBO) é usado para obter um token para chamar a API web a jusante. Neste fluxo, a sua API web recebe um token ao portador com permissões delegadas pelo utilizador a partir da aplicação do cliente e, em seguida, troca este token para outro token de acesso para ligar para a API web a jusante.

O código abaixo utiliza o quadro de Segurança da primavera `SecurityContextHolder` na API web para obter o token do portador validado. Em seguida, utiliza a biblioteca MSAL Java para obter um símbolo para a API a jusante utilizando a `acquireToken` chamada com `OnBehalfOfParameters` . MSAL caches o token para que as chamadas subsequentes para a API possam usar `acquireTokenSilently` para obter o token em cache.

```Java
@Component
class MsalAuthHelper {

    @Value("${security.oauth2.client.authority}")
    private String authority;

    @Value("${security.oauth2.client.client-id}")
    private String clientId;

    @Value("${security.oauth2.client.client-secret}")
    private String secret;

    @Autowired
    CacheManager cacheManager;

    private String getAuthToken(){
        String res = null;
        Authentication authentication = SecurityContextHolder.getContext().getAuthentication();

        if(authentication != null){
            res = ((OAuth2AuthenticationDetails) authentication.getDetails()).getTokenValue();
        }
        return res;
    }

    String getOboToken(String scope) throws MalformedURLException {
        String authToken = getAuthToken();

        ConfidentialClientApplication application =
                ConfidentialClientApplication.builder(clientId, ClientCredentialFactory.create(secret))
                        .authority(authority).build();

        String cacheKey = Hashing.sha256()
                .hashString(authToken, StandardCharsets.UTF_8).toString();

        String cachedTokens = cacheManager.getCache("tokens").get(cacheKey, String.class);
        if(cachedTokens != null){
            application.tokenCache().deserialize(cachedTokens);
        }

        IAuthenticationResult auth;
        SilentParameters silentParameters =
                SilentParameters.builder(Collections.singleton(scope))
                        .build();
        auth = application.acquireTokenSilently(silentParameters).join();

        if (auth == null){
            OnBehalfOfParameters parameters =
                    OnBehalfOfParameters.builder(Collections.singleton(scope),
                            new UserAssertion(authToken))
                            .build();

            auth = application.acquireToken(parameters).join();
        }

        cacheManager.getCache("tokens").put(cacheKey, application.tokenCache().serialize());

        return auth.accessToken();
    }
}
```

# <a name="python"></a>[Python](#tab/python)

O fluxo On-behalf-of (OBO) é usado para obter um token para chamar a API web a jusante. Neste fluxo, a sua API web recebe um token ao portador com permissões delegadas pelo utilizador a partir da aplicação do cliente e, em seguida, troca este token para outro token de acesso para ligar para a API web a jusante.

Uma API web Python terá de usar algum middleware para validar o sinal do portador recebido do cliente. A API web pode então obter o token de acesso para a API a jusante usando a biblioteca MSAL Python, chamando o [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) método. Para um exemplo de utilização desta API, consulte o [código de teste para a autenticação microsoft-library-for-python no GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.2.0/tests/test_e2e.py#L429-L472). Veja-se também a discussão da [questão 53](https://github.com/AzureAD/microsoft-authentication-library-for-python/issues/53) no mesmo repositório para uma abordagem que ignore a necessidade de uma aplicação de nível médio.

---

Também pode ver um exemplo da implementação do fluxo OBO em [Node.js e funções Azure](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions/blob/master/Function/MyHttpTrigger/index.js#L61).

## <a name="protocol"></a>Protocolo

Para obter mais informações sobre o protocolo OBO, consulte a plataforma de identidade da [Microsoft e o fluxo OAuth 2.0 On-Behalf-Of](./v2-oauth2-on-behalf-of-flow.md).

## <a name="next-steps"></a>Passos seguintes

Passe para o próximo artigo neste cenário, [adquira um símbolo para a app.](scenario-web-api-call-api-acquire-token.md)
