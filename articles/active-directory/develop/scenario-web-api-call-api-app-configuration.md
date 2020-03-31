---
title: Configure uma API web que chama APIs web / Azure
titleSuffix: Microsoft identity platform
description: Saiba como construir uma API web que chama APIs web (configuração de código da aplicação)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 82b5e1d9753fbb65fd81f24b06016d302457144e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76834098"
---
# <a name="a-web-api-that-calls-web-apis-code-configuration"></a>Um API web que chama APIs web: Configuração de código

Depois de ter registado a sua Web API, pode configurar o código para a aplicação.

O código que utiliza para configurar a sua Web API para que chame APIs web a jusante constrói-se em cima do código que é usado para proteger uma API web. Para mais informações, consulte [API web protegida: Configuração](scenario-protected-web-api-app-configuration.md)de aplicações .

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

## <a name="code-subscribed-to-ontokenvalidated"></a>Código subscrito a OnTokenValidd

Além da configuração do código para quaisquer APIs web protegidos, precisa subscrever a validação do token do portador que recebe quando a sua API é chamada:

```csharp
/// <summary>
/// Protects the web API with the Microsoft identity platform, or Azure Active Directory (Azure AD) developer platform
/// This supposes that the configuration files have a section named "AzureAD"
/// </summary>
/// <param name="services">The service collection to which to add authentication</param>
/// <param name="configuration">Configuration</param>
/// <returns></returns>
public static IServiceCollection AddProtectedApiCallsWebApis(this IServiceCollection services,
                                                             IConfiguration configuration,
                                                             IEnumerable<string> scopes)
{
    services.AddTokenAcquisition();
    services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        // When an access token for our own web API is validated, we add it
        // to the MSAL.NET cache so that it can be used from the controllers.
        options.Events = new JwtBearerEvents();

        options.Events.OnTokenValidated = async context =>
        {
            context.Success();

            // Adds the token to the cache and handles the incremental consent
            // and claim challenges
            AddAccountToCacheFromJwt(context, scopes);
            await Task.FromResult(0);
        };
    });
    return services;
}
```

## <a name="on-behalf-of-flow"></a>Em nome do fluxo

O método AddAccountToCacheFromJwt () precisa:

- Instantiate uma aplicação confidencial de cliente da Microsoft Authentication Library (MSAL).
- Chame `AcquireTokenOnBehalf` o método. Esta chamada troca o token do portador que foi adquirido pelo cliente para a Web API contra um token portador para o mesmo utilizador, mas tem a Chamada API chamada API a jusante.

### <a name="instantiate-a-confidential-client-application"></a>Instantiate uma aplicação cliente confidencial

Este fluxo está disponível apenas no fluxo confidencial do cliente, de modo que a API web protegida fornece `WithClientSecret` credenciais de cliente (segredo de cliente ou certificado) à [classe ConfidentialClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder) através do método ou método. `WithCertificate`

![Lista de métodos IConfidentialClientApplication](https://user-images.githubusercontent.com/13203188/55967244-3d8e1d00-5c7a-11e9-8285-a54b05597ec9.png)

```csharp
IConfidentialClientApplication app;

#if !VariationWithCertificateCredentials
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .Build();
#else
// Building the client credentials from a certificate
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .Build();
#endif
```

Finalmente, em vez de provar a sua identidade através de um segredo de cliente ou de um certificado, os pedidos confidenciais do cliente podem provar a sua identidade utilizando afirmações do cliente.
Para obter mais informações sobre este cenário avançado, consulte [afirmações confidenciais](msal-net-client-assertions.md)do cliente.

### <a name="how-to-call-on-behalf-of"></a>Como chamar Em Nome-Of

Faça a chamada Em Nome -Of (OBO) ligando para o método `IConfidentialClientApplication` [AcquireTokenOnBehalf](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenonbehalfofparameterbuilder) na interface.

A `UserAssertion` classe é construída a partir do token portador que é recebido pela Web API dos seus próprios clientes. Há [dois construtores:](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientcredential.-ctor?view=azure-dotnet)
* Um que leva um símbolo do portador da JSON Web Token (JWT)
* Um que requer qualquer tipo de afirmação do utilizador, outro tipo de ficha de segurança, cujo tipo é então especificado num parâmetro adicional chamado`assertionType`

![Propriedades e métodos userAfirma](https://user-images.githubusercontent.com/13203188/37082180-afc4b708-21e3-11e8-8af8-a6dcbd2dfba8.png)

Na prática, o fluxo OBO é frequentemente usado para adquirir um símbolo para uma API a jusante e armazená-lo na cache token do utilizador MSAL.NET. Faça isto para que outras partes da Web API possam ``AcquireTokenOnSilent`` mais tarde recorrer às [substituições](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientapplicationbase.acquiretokensilent?view=azure-dotnet) de chamar as APIs a jusante. Esta chamada tem o efeito de refrescar os tokens, se necessário.

```csharp
private void AddAccountToCacheFromJwt(IEnumerable<string> scopes, JwtSecurityToken jwtToken, ClaimsPrincipal principal, HttpContext httpContext)
{
    try
    {
        UserAssertion userAssertion;
        IEnumerable<string> requestedScopes;
        if (jwtToken != null)
        {
            userAssertion = new UserAssertion(jwtToken.RawData, "urn:ietf:params:oauth:grant-type:jwt-bearer");
            requestedScopes = scopes ?? jwtToken.Audiences.Select(a => $"{a}/.default");
        }
        else
        {
            throw new ArgumentOutOfRangeException("tokenValidationContext.SecurityToken should be a JWT Token");
        }

        // Create the application
        var application = BuildConfidentialClientApplication(httpContext, principal);

        // .Result to make sure that the cache is filled in before the controller tries to get access tokens
        var result = application.AcquireTokenOnBehalfOf(requestedScopes.Except(scopesRequestedByMsalNet),
                                                        userAssertion)
                                .ExecuteAsync()
                                .GetAwaiter().GetResult();
     }
     catch (MsalException ex)
     {
         Debug.WriteLine(ex.Message);
         throw;
     }
}
```
# <a name="java"></a>[Java](#tab/java)

O fluxo em nome da OBO é utilizado para obter um símbolo para chamar a API web a jusante. Neste fluxo, a sua Web API recebe um token ao portador com permissões delegadas pelo utilizador a partir da aplicação do cliente e, em seguida, troca este token por outro token de acesso para chamar a API web a jusante.

O código abaixo usa a `SecurityContextHolder` estrutura de Segurança da primavera na Web API para obter o símbolo validado do portador. Em seguida, utiliza a biblioteca MSAL Java para obter `acquireToken` um `OnBehalfOfParameters`símbolo para a API a jusante usando a chamada com . A MSAL caches o símbolo para que as `acquireTokenSilently` chamadas subsequentes para a API possam usar para obter o símbolo em cache.

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

# <a name="python"></a>[Pitão](#tab/python)

O fluxo em nome da OBO é utilizado para obter um símbolo para chamar a API web a jusante. Neste fluxo, a sua Web API recebe um token ao portador com permissões delegadas pelo utilizador a partir da aplicação do cliente e, em seguida, troca este token por outro token de acesso para chamar a API web a jusante.

Uma API web Python terá de usar algum middleware para validar o token do portador recebido do cliente. A Web API pode então obter o token de acesso para [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) API a jusante usando a biblioteca MSAL Python, chamando o método. Uma amostra que demonstre este fluxo com a MSAL Python ainda não está disponível.

---

Também pode ver um exemplo de implementação de fluxo oBO nas [funções Nó.js e Azure](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions/blob/master/MiddleTierAPI/MyHttpTrigger/index.js#L61).

## <a name="protocol"></a>Protocolo

Para obter mais informações sobre o protocolo OBO, consulte a [plataforma de identidade da Microsoft e o OAuth 2.0 On-Behalf-Of flow](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Um API web que chama APIs web: Adquira um símbolo para a app](scenario-web-api-call-api-acquire-token.md)
