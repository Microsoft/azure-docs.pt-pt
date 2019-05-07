---
title: Criar uma instância de uma aplicação cliente pública com opções (biblioteca de autenticação da Microsoft para .NET) | Azure
description: Saiba como criar uma instância de uma aplicação cliente pública com opções de configuração com a biblioteca de autenticação da Microsoft para .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
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
ms.openlocfilehash: 3a483cd6e22ce3c9fafb9b2b4d839e22c248f020
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65158704"
---
# <a name="instantiate-a-public-client-application-with-configuration-options-using-msalnet"></a>Criar uma instância de uma aplicação cliente pública com opções de configuração com MSAL.NET

Este artigo descreve como criar uma instância de um [aplicação de cliente público](msal-client-applications.md) com biblioteca de autenticação da Microsoft para .NET (MSAL.NET).  O aplicativo é instanciado com as opções de configuração definidas no ficheiro de definições.

Antes de inicializar uma aplicação, primeiro tem de [registar](quickstart-register-app.md) -lo para que a sua aplicação pode ser integrada com a plataforma de identidade da Microsoft. Após o registo, poderá ter as seguintes informações (que podem ser encontradas no portal do Azure):

- O ID de cliente (uma cadeia que representa um GUID)
- URL do fornecedor de identidade (com o nome da instância) e o início de sessão público-alvo para a sua aplicação. Esses dois parâmetros são coletivamente conhecidos como a autoridade.
- O ID de inquilino se estiver escrevendo um aplicativo de linha de negócio unicamente para a sua organização (também denominada aplicação inquilino único).
- Para aplicações web e, às vezes, para aplicações de cliente público (em especial quando a aplicação tem de utilizar um mediador), tem também definirá o redirectUri onde o fornecedor de identidade irá contactar back seu aplicativo com os tokens de segurança.


Uma aplicação de consola .NET Core pode ter o seguinte procedimento *appSettings* ficheiro de configuração:

```json
{
  "Authentication": {
    "AzureCloudInstance": "AzurePublic",
    "AadAuthorityAudience": "AzureAdMultipleOrgs",
    "ClientId": "ebe2ab4d-12b3-4446-8480-5c3828d04c50"
  },

  "WebAPI": {
    "MicrosoftGraphBaseEndpoint": "https://graph.microsoft.com"
  }
}
```

O código seguinte lê este ficheiro com a configuração do .NET framework:

```csharp
public class SampleConfiguration
{
    /// <summary>
    /// Authentication options
    /// </summary>
    public PublicClientApplicationOptions PublicClientApplicationOptions { get; set; }

    /// <summary>
    /// Base URL for Microsoft Graph (it varies depending on whether the application is ran
    /// in Microsoft Azure public clouds or national / sovereign clouds
    /// </summary>
    public string MicrosoftGraphBaseEndpoint { get; set; }

    /// <summary>
    /// Reads the configuration from a json file
    /// </summary>
    /// <param name="path">Path to the configuration json file</param>
    /// <returns>SampleConfiguration as read from the json file</returns>
    public static SampleConfiguration ReadFromJsonFile(string path)
    {
        // .NET configuration
        IConfigurationRoot Configuration;
        var builder = new ConfigurationBuilder()
          .SetBasePath(Directory.GetCurrentDirectory())
        .AddJsonFile(path);
        Configuration = builder.Build();

        // Read the auth and graph endpoint config
        SampleConfiguration config = new SampleConfiguration()
        {
            PublicClientApplicationOptions = new PublicClientApplicationOptions()
        };
        Configuration.Bind("Authentication", config.PublicClientApplicationOptions);
        config.MicrosoftGraphBaseEndpoint = Configuration.GetValue<string>("WebAPI:MicrosoftGraphBaseEndpoint");
        return config;
    }
}
```

O código seguinte cria a sua aplicação, utilizando a configuração do arquivo de configurações:

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .Build();
```

