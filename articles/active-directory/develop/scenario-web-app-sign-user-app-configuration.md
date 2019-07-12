---
title: Aplicação Web que inicia sessão dos utilizadores (a configuração do código) – plataforma de identidade da Microsoft
description: Saiba como criar uma aplicação web que inicia sessão dos utilizadores (configuração de código)
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7484b627d3bc3f26fa01d4c38ee96047c70d007
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67785488"
---
# <a name="web-app-that-signs-in-users---code-configuration"></a>Aplicação Web que inicia sessão os utilizadores - configuração de código

Saiba como configurar o código para a sua aplicação Web que os utilizadores inicia sessão.

## <a name="libraries-used-to-protect-web-apps"></a>Bibliotecas utilizadas para proteger aplicações Web

<!-- This section can be in an include for Web App and Web APIs -->
As bibliotecas usadas para proteger uma aplicação Web (e uma API Web) são:

| Plataforma | Biblioteca | Descrição |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_net.png) | [Extensões de modelo de identidade para .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | Extensões de identidade da Microsoft para .NET usado diretamente pelo ASP.NET e ASP.NET Core, propõe a um conjunto de DLLs em execução no .NET Framework e .NET Core. Numa aplicação Web do ASP.NET/ASP.NET Core, pode controlar a validação de tokens utilizando o **TokenValidationParameters** classe (em especial em alguns cenários de ISV) |

## <a name="aspnet-core-configuration"></a>Configuração do ASP.NET Core

Trechos de código neste artigo e a seguir são extraídos do [capítulo tutorial, incremental de Web do ASP.NET Core aplicação 1](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg). Pode desejar referir-se para esse tutorial para obter detalhes de implementação completa.

### <a name="application-configuration-files"></a>Ficheiros de configuração de aplicação

No núcleo do ASP.NET, um aplicativo início de sessão nas usuários da Web com a plataforma de identidade da Microsoft é configurada através do `appsettings.json` ficheiro. As definições que tem de preencher são:

- a cloud `Instance` se pretender que a aplicação seja executada em clouds nacionais
- o público-alvo no `tenantId`
- o `clientId` para a sua aplicação, como copiados a partir do portal do Azure.

```JSon
{
  "AzureAd": {
    // Azure Cloud instance among:
    // "https://login.microsoftonline.com/" for Azure Public cloud.
    // "https://login.microsoftonline.us/" for Azure US government.
    // "https://login.microsoftonline.de/" for Azure AD Germany
    // "https://login.chinacloudapi.cn/" for Azure AD China operated by 21Vianet
    "Instance": "https://login.microsoftonline.com/",

    // Azure AD Audience among:
    // - the tenant Id as a a GUID obtained from the azure portal to sign-in users in your organization
    // - "organizations" to sign-in users in any work or school accounts
    // - "common" to sign-in users with any work and school account or Microsoft personal account
    // - "consumers" to sign-in users with Microsoft personal account only
    "TenantId": "[Enter the tenantId here]]",

    // Client Id (Application ID) obtained from the Azure portal
    "ClientId": "[Enter the Client Id]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc"
  }
}
```

No ASP.NET Core, há outro ficheiro que contém a URL (`applicationUrl`) e a porta de SSL (`sslPort`) para seu aplicativo, bem como diversos perfis.

```JSon
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:3110/",
      "sslPort": 44321
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    },
    "webApp": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      },
      "applicationUrl": "http://localhost:3110/"
    }
  }
}
```

No portal do Azure, a resposta URIs que deve se registrar no **autenticação** página para a sua aplicação precisa de acordo com estes URLs; ou seja, para os ficheiros de configuração de dois acima, elas podem ser `https://localhost:44321/signin-oidc` como o applicationUrl é `http://localhost:3110` mas o `sslPort` é especificado (44321) e o `CallbackPath` é `/signin-oidc` conforme definido no `appsettings.json`.
  
Da mesma forma, a fim de sessão URI seria definido como `https://localhost:44321/signout-callback-oidc`.

### <a name="initialization-code"></a>Código de inicialização

Em aplicações Web do ASP.NET Core (e as APIs da Web), o código de fazer a inicialização do aplicativo está localizado no `Startup.cs` arquivo, e, para adicionar a autenticação com o v2.0 do Microsoft Identity platform (anteriormente conhecido como o Azure AD), terá de adicionar o código a seguir. Os comentários no código devem ser facilmente compreensíveis.

  > [!NOTE]
  > Se iniciar seu projeto com o projeto da web padrão ASP.NET core no Visual studio ou usando `dotnet new mvc` o método `AddAzureAD` está disponível por predefinição, uma vez que os pacotes relacionados são carregados automaticamente. No entanto se criar um projeto do zero e que estão tentando usar o abaixo código sugerimos que adicione o pacote NuGet **"Microsoft.AspNetCore.Authentication.AzureAD.UI"** ao seu projeto para tornar o `AddAzureAD` método disponível.
  
```CSharp
 services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
         .AddAzureAD(options => configuration.Bind("AzureAd", options));

 services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
 {
  // The ASP.NET core templates are currently using Azure AD v1.0, and compute
  // the authority (as {Instance}/{TenantID}). We want to use the Microsoft Identity Platform v2.0 endpoint
  options.Authority = options.Authority + "/v2.0/";

  // If you want to restrict the users that can sign-in to specific organizations
  // Set the tenant value in the appsettings.json file to 'organizations', and add the
  // issuers you want to accept to options.TokenValidationParameters.ValidIssuers collection.
  // Otherwise validate the issuer
  options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.ForAadInstance(options.Authority).ValidateAadIssuer;

  // Set the nameClaimType to be preferred_username.
  // This change is needed because certain token claims from Azure AD v1.0 endpoint
  // (on which the original .NET core template is based) are different in Azure AD v2.0 endpoint.
  // For more details see [ID Tokens](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)
  // and [Access Tokens](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)
  options.TokenValidationParameters.NameClaimType = "preferred_username";
  ...
```

## <a name="aspnet-configuration"></a>Configuração do ASP.NET

No ASP.NET, a aplicação está configurada por meio do `Web.Config` ficheiro

```XML
<?xml version="1.0" encoding="utf-8"?>
<!--
  For more information on how to configure your ASP.NET application, please visit
  https://go.microsoft.com/fwlink/?LinkId=301880
  -->
<configuration>
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:ClientId" value="[Enter your client ID, as obtained from the app registration portal]" />
    <add key="ida:ClientSecret" value="[Enter your client secret, as obtained from the app registration portal]" />
    <add key="ida:AADInstance" value="https://login.microsoftonline.com/{0}{1}" />
    <add key="ida:RedirectUri" value="https://localhost:44326/" />
    <add key="vs:EnableBrowserLink" value="false" />
  </appSettings>
```

O código relacionados à autenticação na aplicação Web do ASP.NET / APIs da Web está localizada no `App_Start/Startup.Auth.cs` ficheiro.

```CSharp
 public void ConfigureAuth(IAppBuilder app)
 {
  app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

  app.UseCookieAuthentication(new CookieAuthenticationOptions());

  app.UseOpenIdConnectAuthentication(
    new OpenIdConnectAuthenticationOptions
    {
     // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0
     // The `Scope` describes the initial permissions that your app will need.
     //  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/
     ClientId = clientId,
     Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0"),
     RedirectUri = redirectUri,
     Scope = "openid profile",
     PostLogoutRedirectUri = redirectUri,
    });
 }
```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Iniciar sessão e terminar sessão](scenario-web-app-sign-user-sign-in.md)
