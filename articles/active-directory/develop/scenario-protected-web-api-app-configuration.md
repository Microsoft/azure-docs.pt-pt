---
title: API da web - configuração de código de aplicação protegida | Azure
description: Saiba como criar uma API web protegida e configurar o código de seu aplicativo.
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
ms.openlocfilehash: fa262c1c6a091575a70c5670b1c7a7c96e8e2128
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67536899"
---
# <a name="protected-web-api-code-configuration"></a>API web protegida: Configuração do código

Para configurar o código para a API web protegido, terá de compreender o que define as APIs como protegido, como configurar um token de portador e como validar o token.

## <a name="what-defines-aspnetaspnet-core-apis-as-protected"></a>O que define ASP.NET/ASP.NET principais APIs como protegido?

Como aplicações web, APIs da web de núcleo de ASP.NET/ASP.NET estão "protegidos" porque suas ações de controlador têm o prefixo a `[Authorize]` atributo. Portanto, as ações de controlador podem ser chamadas apenas se a API é chamada com uma identidade que esteja autorizada.

Considere as seguintes perguntas:

- Como a API web sabe a identidade da aplicação que chama-lo? (Apenas uma aplicação pode chamar uma API web).
- Se a aplicação chamado a API web em nome de um utilizador, o que é a identidade do utilizador?

## <a name="bearer-token"></a>Token de portador

As informações sobre a identidade da aplicação e sobre o utilizador (a menos que a aplicação web aceita as chamadas de serviço para serviço de aplicações daemon), é mantido no token de portador está definido no cabeçalho de quando a aplicação é chamada.

Aqui está um C# exemplo de código que mostra um cliente chamar a API depois de adquirir um token com a biblioteca de autenticação da Microsoft para .NET (MSAL.NET):

```CSharp
var scopes = new[] {$"api://.../access_as_user}";
var result = await app.AcquireToken(scopes)
                      .ExecuteAsync();

httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
```

> [!IMPORTANT]
> O token de portador foi pedido por uma aplicação de cliente para o ponto final do Microsoft identity platform *para a API web*. A API web é o único aplicativo que deve verificar o token e visualizar as declarações que ele contém. Aplicações de cliente nunca devem tentar inspecionar as afirmações nos tokens. (A API web pode exigir, no futuro, que o token de ser encriptados. Este requisito seria impedir o acesso para aplicações de cliente que pode ver os tokens de acesso.)

## <a name="jwtbearer-configuration"></a>Configuração de JwtBearer

Esta secção descreve como configurar um token de portador.

### <a name="config-file"></a>Ficheiro de configuração

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

Quando uma aplicação é chamada numa ação de controlador que contém um `[Authorize]` atributo, ASP.NET/ASP.NET Core examina o token de portador no cabeçalho de autorização do pedido chamado e extrai o token de acesso. O token, em seguida, é encaminhado para o middleware JwtBearer, que chama Microsoft IdentityModel Extensions para .NET.

No ASP.NET Core, este middleware é inicializado no ficheiro Startup.cs:

```CSharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

O middleware é adicionado para a API web por esta instrução:

```CSharp
 services.AddAzureAdBearer(options => Configuration.Bind("AzureAd", options));
```

 Atualmente, os modelos do ASP.NET Core criam web do Azure Active Directory (Azure AD) APIs que inicia a sessão dos utilizadores na sua organização ou de qualquer organização, não com contas pessoais. Mas pode facilmente alterar-lhes que utilizem o ponto de extremidade de plataforma de identidade Microsoft ao adicionar este código para o ficheiro de Startup.cs:

```CSharp
services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
{
    // This is a Microsoft identity platform v2.0 web API.
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
    options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.ValidateAadIssuer;
});
```

## <a name="token-validation"></a>Validação do token

O middleware de JwtBearer, como o middleware OpenID Connect nas aplicações web, é direcionado por `TokenValidationParameters` para validar o token. O token é desencriptado (conforme necessário), as afirmações são extraídas e a assinatura é verificada. O middleware, em seguida, valida o token ao procurar por estes dados:

- Ele é direcionado para a web API (público).
- Foi emitido para uma aplicação que tem permissão para chamar o web API (sub).
- Ele foi emitido por um serviço de token de segurança confiáveis (STS) (emissor).
- Seu ciclo de vida é no intervalo (expiração).
- Ele não foram adulterado com (assinatura).

Também pode ser validações especiais. Por exemplo, é possível validar que as chaves de assinatura (quando incorporado num token) são confiáveis e que o token não está a ser reproduzido. Por fim, alguns protocolos requerem validações específicas.

### <a name="validators"></a>Validadores

Os passos de validação são capturados na validadores, o qual estão todos no [Microsoft IdentityModel Extensions para .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) biblioteca de código-fonte aberto, no ficheiro de uma origem: [Microsoft.IdentityModel.Tokens/Validators.cs](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs).

Os validadores são descritos nesta tabela:

| Validação do | Descrição |
|---------|---------|
| `ValidateAudience` | Garante que o token destina-se a aplicação que valida o token (para mim). |
| `ValidateIssuer` | Garante que o token foi emitido por um STS fidedigno (de alguém que confio). |
| `ValidateIssuerSigningKey` | Garante que o aplicativo a validar as relações de confiança token a chave que foi utilizada para assinar o token. (Caso especial em que a chave é incorporada no token. Não, normalmente, é necessário.) |
| `ValidateLifetime` | Garante que o token é ainda (ou já) válido. O validador verifica se o tempo de vida do token (`notbefore` e `expires` afirmações) estiver ao alcance. |
| `ValidateSignature` | Garante que o token não foi violado. |
| `ValidateTokenReplay` | Garante que o token não é reproduzido. (Caso especial de alguns protocolos de utilização única.) |

Os validadores estão todas associados com propriedades do `TokenValidationParameters` classe próprios inicializado na configuração ASP.NET/ASP.NET Core. Na maioria dos casos, não terá de alterar os parâmetros. Há uma exceção, para aplicações que não são inquilinos únicos. (Ou seja, web apps que aceitam os utilizadores a partir de qualquer organização ou de contas Microsoft pessoais.) Neste caso, o emissor têm de ser validado.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Certifique-se de âmbitos e funções de aplicação no seu código](scenario-protected-web-api-verification-scope-app-roles.md)
