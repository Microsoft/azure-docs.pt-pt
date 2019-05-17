---
title: Criar uma instância de uma aplicação cliente confidenciais com opções (biblioteca de autenticação da Microsoft para .NET) | Azure
description: Saiba como criar uma instância de uma aplicação cliente confidenciais com opções de configuração com a biblioteca de autenticação da Microsoft para .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/30/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f935b1b2815501710444e3f921a157ba02e3215
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544075"
---
# <a name="instantiate-a-confidential-client-application-with-configuration-options-using-msalnet"></a>Criar uma instância de uma aplicação cliente confidenciais com opções de configuração com MSAL.NET

Este artigo descreve como criar uma instância de um [aplicação de cliente confidencial](msal-client-applications.md) com biblioteca de autenticação da Microsoft para .NET (MSAL.NET).  O aplicativo é instanciado com as opções de configuração definidas no ficheiro de definições.

Antes de inicializar uma aplicação, primeiro tem de [registar](quickstart-register-app.md) -lo para que a sua aplicação pode ser integrada com a plataforma de identidade da Microsoft. Após o registo, poderá ter as seguintes informações (que podem ser encontradas no portal do Azure):

- O ID de cliente (uma cadeia que representa um GUID)
- URL do fornecedor de identidade (com o nome da instância) e o início de sessão público-alvo para a sua aplicação. Esses dois parâmetros são coletivamente conhecidos como a autoridade.
- O ID de inquilino se estiver escrevendo um aplicativo de linha de negócio unicamente para a sua organização (também denominada aplicação inquilino único).
- O segredo de aplicação (cadeia de caracteres secreta do cliente) ou o certificado (do tipo X509Certificate2) se for uma aplicação de cliente confidencial.
- Para aplicações web e, às vezes, para aplicações de cliente público (em especial quando a aplicação tem de utilizar um mediador), tem também definirá o redirectUri onde o fornecedor de identidade irá contactar back seu aplicativo com os tokens de segurança.

## <a name="configure-the-application-from-the-config-file"></a>Configurar a aplicação a partir do ficheiro de configuração
O nome das propriedades de opções no MSAL.NET corresponde ao nome das propriedades do `AzureADOptions` em ASP.NET Core, portanto, não precisa de escrever qualquer código "glue".

Uma configuração de aplicação ASP.NET Core está descrita numa *appSettings* ficheiro:

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

A partir de MSAL.NET v3.x, pode configurar a aplicação de cliente confidencial a partir de ficheiro de configuração. As classes relacionadas com a configuração de aplicação estão localizadas no `Microsoft.Identity.Client.AppConfig` espaço de nomes.

Na classe onde pretende configurar e criar uma instância da sua aplicação, tem de indicar um `ConfidentialClientApplicationOptions` objeto.  A configuração de ler a partir da origem (incluindo o arquivo de appconfig.json) do enlace para a instância das opções de aplicação:

```csharp
using Microsoft.Identity.Client.AppConfig;

private ConfidentialClientApplicationOptions _applicationOptions;
_applicationOptions = new ConfidentialClientApplicationOptions();
configuration.Bind("AzureAD", _applicationOptions);
```

Isto permite que o conteúdo da seção de "AzureAD" a *appSettings* arquivo sejam vinculados às propriedades correspondentes do `ConfidentialClientApplicationOptions` objeto.  Em seguida, crie um `ConfidentialClientApplication` objeto:

```csharp
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
        .Build();
```

## <a name="add-runtime-configuration"></a>Adicionar configuração de tempo de execução
Num aplicativo de cliente confidenciais, normalmente, têm uma cache por utilizador. Por isso precisará obter o cache associado ao utilizador e informar o criador da aplicação que pretende utilizá-lo. Da mesma forma, poderá ser necessário um URI de redirecionamento calculada dinamicamente. Nesse caso, o código é o seguinte:

```csharp
IConfidentialClientApplication app;
var request = httpContext.Request;
var currentUri = UriHelper.BuildAbsolute(request.Scheme, request.Host, request.PathBase, _azureAdOptions.CallbackPath ?? string.Empty);
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
       .WithRedirectUri(currentUri)
       .Build();
TokenCache userTokenCache = _tokenCacheProvider.SerializeCache(app.UserTokenCache,httpContext, claimsPrincipal);
```

