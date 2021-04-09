---
title: Instantiate uma aplicação de cliente confidencial (MSAL.NET) | Rio Azure
titleSuffix: Microsoft identity platform
description: Aprenda a instantaneaizar uma aplicação confidencial do cliente com opções de configuração utilizando a Biblioteca de Autenticação do Microsoft para .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 04/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: d477c419bb677a6b8f24a3aae26c403e47cc96cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99583947"
---
# <a name="instantiate-a-confidential-client-application-with-configuration-options-using-msalnet"></a>Instantiizar uma aplicação confidencial do cliente com opções de configuração usando MSAL.NET

Este artigo descreve como instantaneamente uma [aplicação de cliente confidencial](msal-client-applications.md) utilizando a Biblioteca de Autenticação da Microsoft para .NET (MSAL.NET).  A aplicação é instantânea com opções de configuração definidas num ficheiro de definições.

Antes de rubricar uma aplicação, primeiro precisa [de a registar](quickstart-register-app.md) para que a sua aplicação possa ser integrada na plataforma de identidade da Microsoft. Após o registo, poderá necessitar das seguintes informações (que podem ser encontradas no portal Azure):

- O ID do cliente (uma corda que representa um GUID)
- O URL do fornecedor de identidade (nomeado o caso) e o público de inscrição para a sua aplicação. Estes dois parâmetros são coletivamente conhecidos como a autoridade.
- O iD do inquilino se estiver a escrever uma aplicação de linha de negócio apenas para a sua organização (também nomeado pedido de inquilino único).
- O segredo da aplicação (cadeia secreta do cliente) ou certificado (do tipo X509Certificate2) se for uma aplicação confidencial do cliente.
- Para aplicações web, e às vezes para aplicações de clientes públicos (em particular quando a sua aplicação precisa de usar um corretor), também terá definido o redirectUri onde o fornecedor de identidade irá contactar de volta a sua aplicação com os tokens de segurança.

## <a name="configure-the-application-from-the-config-file"></a>Configure o pedido a partir do ficheiro config
O nome das propriedades das opções em MSAL.NET correspondem ao nome das propriedades do `AzureADOptions` núcleo de ASP.NET, pelo que não precisa de escrever nenhum código de cola.

Uma configuração de aplicação core ASP.NET é descrita num *appsettings.jsno* ficheiro:

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "[Enter the domain of your tenant, e.g. contoso.onmicrosoft.com]",
    "TenantId": "[Enter 'common', or 'organizations' or the Tenant Id (Obtained from the Azure portal. Select 'Endpoints' from the 'App registrations' blade and use the GUID in any of the URLs), e.g. da41245a5-11b3-996c-00a8-4d99re19f292]",
    "ClientId": "[Enter the Client Id (Application ID obtained from the Azure portal), e.g. ba74781c2-53c2-442a-97c2-3d60re42f403]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc",

    "ClientSecret": "[Copy the client secret added to the app from the Azure portal]"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

A partir de MSAL.NET v3.x, pode configurar a sua aplicação confidencial de cliente a partir do ficheiro config.

Na classe onde pretende configurar e instantaneamente a sua aplicação, declare um `ConfidentialClientApplicationOptions` objeto.  Ligue a configuração lida da fonte (incluindo a appconfig.jsem ficheiro) à instância das opções de aplicação, utilizando o `IConfigurationRoot.Bind()` método a partir daMicrosoft.Extensions.Config[ uration. Pacote Binder NuGet](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder):

```csharp
using Microsoft.Identity.Client;

private ConfidentialClientApplicationOptions _applicationOptions;
_applicationOptions = new ConfidentialClientApplicationOptions();
configuration.Bind("AzureAD", _applicationOptions);
```

Isto permite que o conteúdo da secção "AzureAD" do *appsettings.jsem* ficheiro seja ligado às propriedades correspondentes do `ConfidentialClientApplicationOptions` objeto.  Em seguida, construa um `ConfidentialClientApplication` objeto:

```csharp
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
        .Build();
```

## <a name="add-runtime-configuration"></a>Adicionar configuração de tempo de execução
Numa aplicação confidencial ao cliente, normalmente tem uma cache por utilizador. Por isso, terá de obter a cache associada ao utilizador e informar o construtor de aplicações que pretende utilizar. Da mesma forma, pode ter um URI de redirecionamento calculado dinamicamente. Neste caso, o código é o seguinte:

```csharp
IConfidentialClientApplication app;
var request = httpContext.Request;
var currentUri = UriHelper.BuildAbsolute(request.Scheme, request.Host, request.PathBase, _azureAdOptions.CallbackPath ?? string.Empty);
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
       .WithRedirectUri(currentUri)
       .Build();
TokenCache userTokenCache = _tokenCacheProvider.SerializeCache(app.UserTokenCache,httpContext, claimsPrincipal);
```

