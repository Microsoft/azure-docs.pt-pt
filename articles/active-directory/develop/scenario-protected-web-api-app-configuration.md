---
title: Configure aplicações web API protegidas  Azure
titleSuffix: Microsoft identity platform
description: Aprenda a construir uma API web protegida e configure o código da sua aplicação.
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
ms.openlocfilehash: c4b8fc629c94f5046861437367fe6050dad4c65f
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76702016"
---
# <a name="protected-web-api-code-configuration"></a>API da web protegida: Configuração de código

Para configurar o código para a sua API web protegida, precisa entender o que define as APIs como protegidas, como configurar um token ao portador e como validar o símbolo.

## <a name="what-defines-aspnetaspnet-core-apis-as-protected"></a>O que define ASP.NET/ASP.NET APIs do Núcleo como protegidos?

Tal como as aplicações web, as APIs web ASP.NET/ASP.NET Core são "protegidas" porque as suas ações de controlador são pré-fixadas com o atributo `[Authorize]`. Assim, as ações do controlador só podem ser chamadas se a API for chamada com uma identidade que seja autorizada.

Considere as perguntas seguintes:

- Como é que a Web API sabe a identidade da app que a chama? (Só uma aplicação pode chamar um API web.)
- Se a aplicação chamou a Web API em nome de um utilizador, qual é a identidade do utilizador?

## <a name="bearer-token"></a>Símbolo do portador

As informações sobre a identidade da app, e sobre o utilizador (a menos que a aplicação web aceite chamadas de serviço a serviço a partir de uma aplicação daemon), são mantidas no token do portador que está definido no cabeçalho quando a aplicação é chamada.

Aqui está C# um exemplo de código que mostra um cliente a ligar para a API depois de adquirir um símbolo com a Microsoft Authentication Library para .NET (MSAL.NET):

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
> O token portador foi solicitado por uma aplicação de cliente para o ponto final da plataforma de identidade microsoft *para a Web API*. A API web é a única aplicação que deve verificar o símbolo e ver as alegações que contém. As aplicações de clientes nunca devem tentar inspecionar as reclamações em fichas. (A API web pode exigir, no futuro, que o símbolo seja encriptado. Este requisito impediria o acesso de aplicações de clientes que possam ver fichas de acesso.)

## <a name="jwtbearer-configuration"></a>Configuração JwtBearer

Esta secção descreve como configurar um símbolo do portador.

### <a name="config-file"></a>Arquivo Config

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

### <a name="code-initialization"></a>Inicialização do código

Quando uma aplicação é chamada a uma ação de controlador que detém um atributo `[Authorize]`, ASP.NET/ASP.NET Core olha para o token do portador no cabeçalho de autorização do pedido de chamada e extrai o sinal de acesso. O símbolo é então encaminhado para o middleware JwtBearer, que chama as extensões do Microsoft IdentityModel para .NET.

Em ASP.NET Core, este middleware é inicializado no ficheiro Startup.cs:

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

O middleware é adicionado à Web API por esta instrução:

```csharp
 services.AddAzureAdBearer(options => Configuration.Bind("AzureAd", options));
```

 Atualmente, os modelos ASP.NET Core criam APIs web Azure Ative Directory (Azure AD) que assinam utilizadores dentro da sua organização ou de qualquer organização, e não com contas pessoais. Mas pode facilmente mudá-los para utilizar o ponto final da plataforma de identidade da Microsoft adicionando este código ao ficheiro Startup.cs:

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

Este snippet de código é extraído do tutorial incremental core Web Api ASP.NET em [Microsoft.Identity.Web/WebApiServiceCollectionExtensions.cs#L50-L63](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/154282843da2fc2958fad151e2a11e521e358d42/Microsoft.Identity.Web/WebApiServiceCollectionExtensions.cs#L50-L63). O método `AddProtectedWebApi`, que faz muito mais, é chamado a partir do Startup.cs

## <a name="token-validation"></a>Validação de fichas

O middleware JwtBearer, como o OpenID Connect middleware em aplicações web, é direcionado por `TokenValidationParameters` para validar o token. O símbolo é desencriptado (conforme necessário), as alegações são extraídas e a assinatura é verificada. O middleware valida então o símbolo verificando estes dados:

- É direcionado para a Web API (público).
- Foi emitido para uma aplicação que é permitida a chamada web API (sub).
- Foi emitido por um serviço de fichas de segurança confiável (STS) (emitente).
- A sua vida útil está ao alcance (expiração).
- Não foi adulterado com (assinatura).

Também pode haver validações especiais. Por exemplo, é possível validar que as chaves de assinatura (quando incorporadas numa ficha) são confiáveis e que o símbolo não está a ser repetido. Finalmente, alguns protocolos requerem validações específicas.

### <a name="validators"></a>Validadores

Os passos de validação são capturados em validadores, que estão todos nas extensões do Microsoft IdentityModel para a biblioteca de código aberto [.NET,](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) num ficheiro de origem: [Microsoft.IdentityModel.Tokens/Validators.cs](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs).

Os validadores são descritos nesta tabela:

| Validador | Descrição |
|---------|---------|
| `ValidateAudience` | Garante que o símbolo é para a aplicação que valida o símbolo (para mim). |
| `ValidateIssuer` | Garante que o símbolo foi emitido por um STS de confiança (de alguém em quem confio). |
| `ValidateIssuerSigningKey` | Garante que a aplicação que valida o símbolo confia na chave que foi usada para assinar o símbolo. (Caso especial em que a chave está incorporada no símbolo. Não normalmente necessário.) |
| `ValidateLifetime` | Garante que o símbolo ainda é (ou já) válido. O validador verifica se a vida útil do token (`notbefore` e `expires` reclamações) estiver ao alcance. |
| `ValidateSignature` | Garante que o símbolo não foi adulterado. |
| `ValidateTokenReplay` | Garante que o símbolo não é repetido. (Caso especial para alguns protocolos de utilização única.) |

Os validadores estão todos associados a propriedades da classe `TokenValidationParameters`, eles próprios inicializados a partir da configuração ASP.NET/ASP.NET Core. Na maioria dos casos, não terá que mudar os parâmetros. Há uma exceção para apps que não são inquilinos individuais. (Isto é, aplicações web que aceitam utilizadores de qualquer organização ou de contas pessoais da Microsoft.) Neste caso, o emitente deve ser validado.

## <a name="token-validation-in-azure-functions"></a>Validação simbólica em Funções Azure

Também é possível validar fichas de acesso em funções Azure. Pode encontrar exemplos de validação de fichas em funções Azure em [Dotnet,](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions) [NodeJS](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions)e [Python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Verifique os âmbitos e as funções da aplicação no seu código](scenario-protected-web-api-verification-scope-app-roles.md)
