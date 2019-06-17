---
title: API da web - configuração de código de aplicação protegida | Azure
description: Saiba como criar uma API Web protegida e configurar o código de seu aplicativo.
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
ms.openlocfilehash: bdd68d9ec5d0dd83df4628f39785ce255482245d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67111158"
---
# <a name="protected-web-api---code-configuration"></a>API - configuração de código web protegida

Para configurar com êxito o código para a sua API web protegida, precisa entender o que torna as APIs protegidas, o que precisa de configurar o token de portador e como validar o token.

## <a name="what-makes-aspnetaspnet-core-apis-protected"></a>O que torna as APIs de núcleo de ASP.NET/ASP.NET protegidos?

Como em aplicações web, o ASP.NET/ASP.NET core web APIs estão "protegidas" uma vez que suas ações de controlador têm o prefixo a `[Authorize]` atributo. Por conseguinte, as ações do controlador apenas podem ser chamadas, se a API é chamada com uma identidade que esteja autorizada.

Considere as seguintes perguntas:

- Como a API web sabe a identidade da aplicação que chama (apenas um aplicativo pode chamar uma API web)?
- Se o aplicativo chamado a API web em nome de um utilizador, o que é a identidade do utilizador?

## <a name="bearer-token"></a>Token de portador

As informações sobre a identidade da aplicação e sobre o utilizador (a menos que a aplicação web aceita as chamadas de serviço para serviço de um aplicativo de daemon), é mantido no token de portador está definido no cabeçalho ao chamar o aplicativo.

Aqui está um C# exemplo de código que mostra um cliente chamar a API depois de adquirir um token com MSAL.NET.

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
> O token de portador foi pedido por uma aplicação de cliente para o ponto final do Microsoft identity platform **para a API web**. A API web é o único aplicativo que deve verificar o token e examinar as declarações que ele contém. As aplicações cliente nunca devem examinar as afirmações nos tokens de (a API web poderia optar, no futuro, que exige que o token de ser encriptados e esta remoção irá interromper a aplicação de cliente que pode quebrar abrir os tokens de acesso).

## <a name="jwtbearer-configuration"></a>Configuração de JwtBearer

Esta seção aborda o que precisa de configurar o token de portador.

### <a name="config-file"></a>Ficheiro de configuração

```Json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    /*
      You need specify the TenantId only if you want to accept access tokens from a single tenant
     (line of business app)
      Otherwise you can leave them set to common
      This can be:
      - A guid (Tenant ID = Directory ID)
      - 'common' (Any organization and personal accounts)
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

Quando o aplicativo é chamado na retenção de ação de controlador um `[Authorize]` atributo, o núcleo ASP.NET/ASP.NET examina o token de portador no cabeçalho de autorização do pedido chamado e extrai o token de acesso, que, em seguida, é reencaminhado para o JwtBearer Middleware, que chama as extensões de modelo de identidade do Microsoft .NET.

No ASP.NET Core, este middleware é inicializado no `Startup.cs` ficheiro.

```CSharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

O middleware é adicionado para a API web, a instrução seguinte:

```CSharp
 services.AddAzureAdBearer(options => Configuration.Bind("AzureAd", options));
```

 Atualmente, os modelos do ASP.NET Core criam APIs de web de versão 1.0 do Azure AD. No entanto, pode facilmente alterá-las para utilizar o ponto de extremidade de plataforma de identidade Microsoft adicionando o seguinte código no `Startup.cs` ficheiro.

```CSharp
services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
{
    // This is a Microsoft identity platform v2.0 web API
    options.Authority += "/v2.0";

    // The web API accepts as audiences are both the Client ID (options.Audience) and api://{ClientID}
    options.TokenValidationParameters.ValidAudiences = new []
    {
     options.Audience,
     $"api://{options.Audience}"
    };

    // Instead of using the default validation (validating against a single tenant,
    // as we do in line of business apps),
    // we inject our own multi-tenant validation logic (which even accepts both V1 and V2 tokens)
    options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.ValidateAadIssuer;
});
```

## <a name="token-validation"></a>Validação do token

O middleware de JwtBearer, como o middleware OpenID Connect nas aplicações web, é direcionado pelo `TokenValidationParameters` para validar o token. O token é desencriptado (se necessário), as afirmações são extraídas e a assinatura é verificada. Em seguida, o token é validado verificando os seguintes dados:

- Ele é direcionado para a web API (público)
- Foi emitido para uma aplicação que tem permissão para chamar o web API (sub)
- Foi emitido por um fiável segurança Token Server (STS) (emissor)
- Duração do token está no intervalo (expiração)
- Ele não foi adulterado (assinatura)

Também pode ser validações especiais. Por exemplo, é possível validar que as chaves de assinatura (quando incorporado num token) são confiáveis e que o token não está a ser reproduzido. Por fim, alguns protocolos requerem validações específicas.

### <a name="validators"></a>Validadores

Os passos de validação são capturados em validadores, o qual estão todos no [extensão de modelo de identidade Microsoft para .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) biblioteca de código-fonte aberto, no ficheiro de uma origem: [Microsoft.IdentityModel.Tokens/Validators.cs](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs)

Os validadores são descritos na tabela a seguir:

| Validação do | Descrição |
|---------|---------|
| `ValidateAudience` | Garante que o token para a aplicação que valida o token (para mim). |
| `ValidateIssuer` | Garante que o token foi emitido por um STS fidedigno (de alguém que confio). |
| `ValidateIssuerSigningKey` | Garante que o aplicativo a validar as relações de confiança token a chave que foi utilizada para assinar o token (caso especial em que a chave é incorporada no token, não seja necessário). |
| `ValidateLifetime` | Garante que o token é ainda (ou já) válido. Feito verificando que o tempo de vida do token (`notbefore`, `expires` afirmações) estiver ao alcance. |
| `ValidateSignature` | Garante que o token não foi violado. |
| `ValidateTokenReplay` | Garante que o token não está reproduzido (caso especial para alguns protocolos de utilização única). |

Os validadores estão todas associados com propriedades do `TokenValidationParameters` classe próprios inicializado na configuração ASP.NET/ASP.NET Core. Na maioria dos casos, não terá de alterar os parâmetros. Há uma exceção para aplicativos que não são inquilinos únicos (ou seja, aplicações web que aceitam os utilizadores de qualquer organização ou contas Microsoft pessoais)--neste caso, o emissor têm de ser validado.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Certifique-se de âmbitos e funções de aplicação no seu código](scenario-protected-web-api-verification-scope-app-roles.md)
