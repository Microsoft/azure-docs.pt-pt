---
title: Instantiate uma aplicação de cliente confidencial (MSAL.NET) / Azure
titleSuffix: Microsoft identity platform
description: Saiba como instantaneamente uma aplicação confidencial do cliente com opções de configuração utilizando a Biblioteca de Autenticação da Microsoft para .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 1a520c5a1002e401f880fba84f8fc02a0a678133
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2020
ms.locfileid: "77084732"
---
# <a name="instantiate-a-confidential-client-application-with-configuration-options-using-msalnet"></a>Instantiate uma aplicação cliente confidencial com opções de configuração usando MSAL.NET

Este artigo descreve como instantaneamente uma [aplicação de cliente confidencial](msal-client-applications.md) usando a Microsoft Authentication Library para .NET (MSAL.NET).  A aplicação é instantânea com opções de configuração definidas num ficheiro de definições.

Antes de inicializar uma aplicação, primeiro é necessário [registá-la](quickstart-register-app.md) para que a sua aplicação possa ser integrada na plataforma de identidade da Microsoft. Após o registro, talvez você precise das seguintes informações (que podem ser encontradas no portal do Azure):

- O ID do cliente (uma corda que representa um GUID)
- A URL do provedor de identidade (chamada de instância) e o público-alvo de entrada para seu aplicativo. Esses dois parâmetros são coletivamente conhecidos como autoridade.
- O ID do inquilino se estiver a escrever uma linha de aplicação de negócios exclusivamente para a sua organização (também nomeada candidatura de inquilino único).
- O segredo da aplicação (string secreto do cliente) ou certificado (do tipo X509Certificate2) se for uma aplicação de cliente confidencial.
- Para aplicações web, e por vezes para aplicações de clientes públicos (em particular quando a sua aplicação precisa de usar um corretor), também terá definido o redirectUri onde o fornecedor de identidade irá contactar de volta a sua aplicação com os tokens de segurança.

## <a name="configure-the-application-from-the-config-file"></a>Configure a aplicação a partir do ficheiro config
O nome das propriedades das opções em MSAL.NET corresponde ao nome das propriedades do `AzureADOptions` no Núcleo ASP.NET, pelo que não precisa de escrever nenhum código de cola.

Uma configuração de aplicação ASP.NET Core é descrita num ficheiro *appsettings.json:*

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

A partir de MSAL.NET v3.x, pode configurar a sua aplicação de cliente confidencial a partir do ficheiro config.

Na aula onde pretende configurar e instantaneamente a sua aplicação, tem de declarar um objeto `ConfidentialClientApplicationOptions`.  Ligue a configuração lida a partir da fonte (incluindo o ficheiro appconfig.json) à instância das opções de aplicação, utilizando o método `IConfigurationRoot.Bind()` a partir do [pacote Microsoft.Extensions.Configuration.Binder nuget:](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder)

```csharp
using Microsoft.Identity.Client;

private ConfidentialClientApplicationOptions _applicationOptions;
_applicationOptions = new ConfidentialClientApplicationOptions();
configuration.Bind("AzureAD", _applicationOptions);
```

Isto permite que o conteúdo da secção "AzureAD" do ficheiro *appsettings.json* esteja ligado às propriedades correspondentes do `ConfidentialClientApplicationOptions` objeto.  Em seguida, construa um objeto `ConfidentialClientApplication`:

```csharp
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
        .Build();
```

## <a name="add-runtime-configuration"></a>Adicionar configuração de tempo de execução
Numa aplicação confidencial do cliente, normalmente tem uma cache por utilizador. Por isso, terá de obter o cache associado ao utilizador e informar o construtor de aplicações de que pretende utilizá-lo. Da mesma forma, pode ter um URI redirecionado dinamicamente computorizado. Neste caso, o código é o seguinte:

```csharp
IConfidentialClientApplication app;
var request = httpContext.Request;
var currentUri = UriHelper.BuildAbsolute(request.Scheme, request.Host, request.PathBase, _azureAdOptions.CallbackPath ?? string.Empty);
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
       .WithRedirectUri(currentUri)
       .Build();
TokenCache userTokenCache = _tokenCacheProvider.SerializeCache(app.UserTokenCache,httpContext, claimsPrincipal);
```

