---
title: Configurar uma API Web que chama APIs da Web | Azure
titleSuffix: Microsoft identity platform
description: Saiba como criar uma API Web que chama APIs da Web (configuração de código do aplicativo)
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
ms.openlocfilehash: dd38cb58e6e6db9767be9adb8f299107601de580
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701778"
---
# <a name="a-web-api-that-calls-web-apis-code-configuration"></a>Uma API Web que chama APIs da Web: configuração de código

Depois de registrar sua API Web, você pode configurar o código para o aplicativo.

O código que você usa para configurar sua API Web para que ele chame APIs da Web downstream com base no código usado para proteger uma API da Web. Para obter mais informações, consulte [API Web protegida: configuração de aplicativo](scenario-protected-web-api-app-configuration.md).

## <a name="code-subscribed-to-ontokenvalidated"></a>Código assinado para OnTokenValidated

Na parte superior da configuração de código de qualquer API Web protegida, você precisa assinar a validação do token de portador que você recebe quando sua API é chamada:

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

## <a name="on-behalf-of-flow"></a>Fluxo em nome de

O método AddAccountToCacheFromJwt () precisa:

- Crie uma instância de um aplicativo cliente confidencial da MSAL (biblioteca de autenticação da Microsoft).
- Chame o método `AcquireTokenOnBehalf`. Essa chamada troca o token de portador que foi adquirido pelo cliente para a API Web em relação a um token de portador para o mesmo usuário, mas ele tem a chamada à API de uma API downstream.

### <a name="instantiate-a-confidential-client-application"></a>Criar uma instância de um aplicativo cliente confidencial

Esse fluxo está disponível apenas no fluxo do cliente confidencial, para que a API da Web protegida forneça credenciais do cliente (segredo do cliente ou certificado) para a [classe ConfidentialClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder) por meio do método `WithClientSecret` ou `WithCertificate`.

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

Por fim, em vez de provar sua identidade por meio de um segredo do cliente ou de um certificado, os aplicativos cliente confidenciais podem provar sua identidade usando as declarações do cliente.
Para obter mais informações sobre esse cenário avançado, consulte [asserções de cliente confidenciais](msal-net-client-assertions.md).

### <a name="how-to-call-on-behalf-of"></a>Como chamar em nome de

Você faz a chamada em nome de (OBO) chamando o [método AcquireTokenOnBehalf](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenonbehalfofparameterbuilder) na interface `IConfidentialClientApplication`.

A classe `UserAssertion` é criada a partir do token de portador que é recebido pela API Web de seus próprios clientes. Há [dois construtores](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientcredential.-ctor?view=azure-dotnet):
* Um que usa um token de portador JWT (token Web JSON)
* Um que usa qualquer tipo de declaração de usuário, outro tipo de token de segurança, cujo tipo é especificado em um parâmetro adicional chamado `assertionType`

![Métodos e propriedades de userassertion](https://user-images.githubusercontent.com/13203188/37082180-afc4b708-21e3-11e8-8af8-a6dcbd2dfba8.png)

Na prática, o fluxo OBO geralmente é usado para adquirir um token para uma API downstream e armazená-lo no cache do token de usuário do MSAL.NET. Você faz isso para que outras partes da API da Web possam, posteriormente, chamar as [substituições](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientapplicationbase.acquiretokensilent?view=azure-dotnet) de ``AcquireTokenOnSilent`` para chamar as APIs downstream. Essa chamada tem o efeito de atualizar os tokens, se necessário.

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

Você também pode ver um exemplo de implementação de fluxo OBO em [node. js e Azure Functions](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions/blob/master/MiddleTierAPI/MyHttpTrigger/index.js#L61).

## <a name="protocol"></a>Protocolo

Para obter mais informações sobre o protocolo OBO, consulte [plataforma de identidade da Microsoft e fluxo em nome de OAuth 2,0](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Uma API Web que chama APIs da Web: adquirir um token para o aplicativo](scenario-web-api-call-api-acquire-token.md)
