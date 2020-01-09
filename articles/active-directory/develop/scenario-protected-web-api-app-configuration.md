---
title: Configurar aplicativos de API Web protegidos | Azure
titleSuffix: Microsoft identity platform
description: Saiba como criar uma API Web protegida e configurar o código do aplicativo.
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
ms.openlocfilehash: 76d5aabc30d0375185130b9781caeaf4d5457455
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75423728"
---
# <a name="protected-web-api-code-configuration"></a>API Web protegida: configuração de código

Para configurar o código para sua API Web protegida, você precisa entender o que define as APIs como protegidas, como configurar um token de portador e como validar o token.

## <a name="what-defines-aspnetaspnet-core-apis-as-protected"></a>O que define as APIs principais do ASP.NET/ASP.NET como protegidas?

Como os aplicativos Web, as APIs Web do ASP.NET/ASP.NET Core são "protegidas" porque suas ações de controlador são prefixadas com o atributo `[Authorize]`. Assim, as ações do controlador poderão ser chamadas somente se a API for chamada com uma identidade autorizada.

Considere as perguntas seguintes:

- Como a API Web sabe a identidade do aplicativo que a chama? (Somente um aplicativo pode chamar uma API da Web.)
- Se o aplicativo chamou a API da Web em nome de um usuário, qual é a identidade do usuário?

## <a name="bearer-token"></a>Token de portador

As informações sobre a identidade do aplicativo e sobre o usuário (a menos que o aplicativo Web aceite chamadas de serviço a serviço de um aplicativo daemon), é mantida no token de portador que é definido no cabeçalho quando o aplicativo é chamado.

Aqui está um C# exemplo de código que mostra um cliente que chama a API depois de adquirir um token com a biblioteca de autenticação da Microsoft para .net (MSAL.net):

```csharp
var scopes = new[] {$"api://.../access_as_user}";
var result = await app.AcquireToken(scopes)
                      .ExecuteAsync();

httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
```

> [!IMPORTANT]
> O token de portador foi solicitado por um aplicativo cliente para o ponto de extremidade da plataforma de identidade da Microsoft *para a API da Web*. A API Web é o único aplicativo que deve verificar o token e exibir as declarações que ele contém. Os aplicativos cliente nunca devem tentar inspecionar as declarações em tokens. (A API da Web pode exigir, no futuro, que o token seja criptografado. Esse requisito impediria o acesso para aplicativos cliente que podem exibir tokens de acesso.)

## <a name="jwtbearer-configuration"></a>Configuração do JwtBearer

Esta seção descreve como configurar um token de portador.

### <a name="config-file"></a>Arquivo de configuração

```Json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    /*
      You need specify the TenantId only if you want to accept access tokens from a single tenant
     (line-of-business app).
      Otherwise, you can leave them set to common.
      This can be:
      - A GUID (Tenant ID = Directory ID)
      - 'common' (any organization and personal accounts)
      - 'organizations' (any organization)
      - 'consumers' (Microsoft personal accounts)
    */
    "TenantId": "common"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="code-initialization"></a>Inicialização de código

Quando um aplicativo é chamado em uma ação do controlador que contém um atributo `[Authorize]`, o ASP.NET/ASP.NET Core examina o token de portador no cabeçalho Authorization da solicitação de chamada e extrai o token de acesso. Em seguida, o token é encaminhado para o middleware JwtBearer, que chama as extensões Microsoft IdentityModel para .NET.

Em ASP.NET Core, esse middleware é inicializado no arquivo Startup.cs:

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

O middleware é adicionado à API Web por esta instrução:

```csharp
 services.AddAzureAdBearer(options => Configuration.Bind("AzureAd", options));
```

 Atualmente, os modelos de ASP.NET Core criam APIs da Web do Azure Active Directory (Azure AD) que conectam usuários em sua organização ou em qualquer organização, não com contas pessoais. Mas você pode alterá-las facilmente para usar o ponto de extremidade da plataforma de identidade da Microsoft adicionando esse código ao arquivo Startup.cs:

```csharp
services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
{
    // This is a Microsoft identity platform web API.
    options.Authority += "/v2.0";

    // The web API accepts as audiences both the Client ID (options.Audience) and api://{ClientID}.
    options.TokenValidationParameters.ValidAudiences = new []
    {
     options.Audience,
     $"api://{options.Audience}"
    };

    // Instead of using the default validation (validating against a single tenant,
    // as we do in line-of-business apps),
    // we inject our own multitenant validation logic (which even accepts both v1 and v2 tokens).
    options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.GetIssuerValidator(options.Authority).Validate;;
});
```

Este trecho de código é extraído do ASP.NET Core tutorial incremental da API Web em [Microsoft. Identity. Web/WebApiServiceCollectionExtensions. cs # L50-L63](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/154282843da2fc2958fad151e2a11e521e358d42/Microsoft.Identity.Web/WebApiServiceCollectionExtensions.cs#L50-L63). O método `AddProtectedWebApi`, que faz muito mais, é chamado do Startup.cs

## <a name="token-validation"></a>Validação de token

O middleware JwtBearer, como o middleware OpenID Connect em aplicativos Web, é direcionado pelo `TokenValidationParameters` para validar o token. O token é descriptografado (conforme necessário), as declarações são extraídas e a assinatura é verificada. Em seguida, o middleware valida o token verificando os dados:

- Ele é direcionado para a API da Web (público).
- Ele foi emitido para um aplicativo que tem permissão para chamar a API da Web (sub).
- Ele foi emitido por um STS (serviço de token de segurança) confiável (emissor).
- Seu tempo de vida está no intervalo (expiração).
- Ele não foi adulterado (assinatura).

Também pode haver validações especiais. Por exemplo, é possível validar que as chaves de assinatura (quando inseridas em um token) sejam confiáveis e que o token não esteja sendo repetido. Por fim, alguns protocolos exigem validações específicas.

### <a name="validators"></a>Validadores

As etapas de validação são capturadas em validadores, que estão todas na biblioteca [Microsoft IdentityModel Extensions para .net](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) Open-Source, em um arquivo de origem: [Microsoft. IdentityModel. Tokens/validators. cs](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs).

Os validadores são descritos nesta tabela:

| Validator | Descrição |
|---------|---------|
| `ValidateAudience` | Garante que o token seja para o aplicativo que valida o token (para mim). |
| `ValidateIssuer` | Garante que o token foi emitido por um STS confiável (de alguém que eu confio). |
| `ValidateIssuerSigningKey` | Garante que o aplicativo que valida o token confie na chave que foi usada para assinar o token. (Caso especial em que a chave é inserida no token. Geralmente não é necessário.) |
| `ValidateLifetime` | Garante que o token ainda é válido (ou já). O validador verifica se o tempo de vida do token (`notbefore` e `expires` declarações) está no intervalo. |
| `ValidateSignature` | Garante que o token não tenha sido adulterado. |
| `ValidateTokenReplay` | Garante que o token não seja reproduzido. (Caso especial para alguns protocolos de uso de OneTime). |

Os validadores são todos associados às propriedades da classe `TokenValidationParameters`, inicializados a partir da configuração do ASP.NET/ASP.NET Core. Na maioria dos casos, você não precisará alterar os parâmetros. Há uma exceção para aplicativos que não são locatários únicos. (Ou seja, aplicativos Web que aceitam usuários de qualquer organização ou de contas pessoais da Microsoft). Nesse caso, o emissor deve ser validado.

## <a name="token-validation-in-azure-functions"></a>Validação de token no Azure Functions

Também é possível validar tokens de acesso de entrada no Azure functions. Você pode encontrar exemplos de validação de tokens no Azure Functions no [dotnet](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions), [NodeJS](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions)e [python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Verificar escopos e funções de aplicativo em seu código](scenario-protected-web-api-verification-scope-app-roles.md)
