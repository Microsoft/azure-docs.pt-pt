---
title: Ligue-se ao Azure Media Services v3 API - .NET
description: Este artigo demonstra como ligar aos Serviços de Mídia v3 API com .NET.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/17/2020
ms.author: inhenkel
ms.custom: has-adal-ref, devx-track-csharp
ms.openlocfilehash: 8946f6e94dd26db45622bc7609fb2375d59bb57e
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102455387"
---
# <a name="connect-to-media-services-v3-api---net"></a>Ligar aos Serviços de Mídia v3 API - .NET

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Este artigo mostra-lhe como ligar-se ao Azure Media Services v3 .NET SDK utilizando o método de login principal do serviço.

## <a name="prerequisites"></a>Pré-requisitos

- [Criar uma conta de Serviços de Comunicação](./create-account-howto.md)Social. Certifique-se de lembrar o nome do grupo de recursos e o nome da conta dos Serviços de Mídia
- Instale uma ferramenta que pretende utilizar para o desenvolvimento de .NET. Os passos deste artigo mostram como usar o [Visual Studio 2019 Community Edition.](https://www.visualstudio.com/downloads/) Pode utilizar o Código do Estúdio Visual, ver [Trabalhar com C#](https://code.visualstudio.com/docs/languages/csharp). Ou podes usar um editor de código diferente.

> [!IMPORTANT]
> Rever [convenções de nomeação](media-services-apis-overview.md#naming-conventions).

## <a name="create-a-console-application"></a>Criar uma aplicação de consola

1. Inicie o Visual Studio. 
1. A partir do menu **'Ficheiro',** clique em   >  **'Novo Projeto'.** 
1. Crie uma aplicação de consola **.NET Core.**

A aplicação de amostra neste tópico, `netcoreapp2.0` alvos. O código utiliza o 'async main', que está disponível a partir de C# 7.1. Consulte este [blog](/archive/blogs/benwilli/async-main-is-available-but-hidden) para mais detalhes.

## <a name="add-required-nuget-packagesassemblies"></a>Adicionar pacotes/conjuntos NuGet necessários

1. No Estúdio Visual, selecione **Tools**  >  **NuGet Package Manager** Consola  >  **.**
2. Na janela consola do **gestor de pacotes,** utilize `Install-Package` o comando para adicionar os seguintes pacotes NuGet. Por exemplo, `Install-Package Microsoft.Azure.Management.Media`.

|Pacote|Descrição|
|---|---|
|`Microsoft.Azure.Management.Media`|Azure Media Services SDK. <br/>Para se certificar de que está a utilizar o mais recente pacote Azure Media Services, consulte [microsoft.Azure.Management.Media](https://www.nuget.org/packages/Microsoft.Azure.Management.Media).|

### <a name="other-required-assemblies"></a>Outras assembleias necessárias

- Azure.Storage.Blobs
- Microsoft.Extensions.Configuration
- Microsoft.Extensions.Configuration. Ambiente Variaables
- Microsoft.Extensions.Configuration.Jsem
- Microsoft.rest.ClientRuntime.Azure.Authentication

## <a name="create-and-configure-the-app-settings-file"></a>Criar e configurar o ficheiro de definições de aplicações

### <a name="create-appsettingsjson"></a>Criar appsettings.jsem

1. Vai para **o** ficheiro  >  **Texto** Geral.
1. Diga-lhe "appsettings.jsligado".
1. Desave a propriedade "Copy to Output Directory" do ficheiro .json para "Copiar se mais recente" (de modo a que a aplicação possa aceder-lhe quando publicada).

### <a name="set-values-in-appsettingsjson"></a>Definir valores em appsettings.jsem

Executar o `az ams account sp create` comando como descrito nas [APIs de acesso](./access-api-howto.md). O comando devolve a json que deve copiar para o seu "appsettings.jsligado".
 
## <a name="add-configuration-file"></a>Adicionar o ficheiro de configuração

Por conveniência, adicione um ficheiro de configuração que é responsável por valores de leitura a partir de "appsettings.json".

1. Adicione uma nova aula de .cs ao seu projeto. Dê-lhe o nome `ConfigWrapper`. 
1. Cole o seguinte código neste ficheiro (este exemplo pressupõe que tem o espaço de `ConsoleApp1` nomes).

```csharp
using System;

using Microsoft.Extensions.Configuration;

namespace ConsoleApp1
{
    public class ConfigWrapper
    {
        private readonly IConfiguration _config;

        public ConfigWrapper(IConfiguration config)
        {
            _config = config;
        }

        public string SubscriptionId
        {
            get { return _config["SubscriptionId"]; }
        }

        public string ResourceGroup
        {
            get { return _config["ResourceGroup"]; }
        }

        public string AccountName
        {
            get { return _config["AccountName"]; }
        }

        public string AadTenantId
        {
            get { return _config["AadTenantId"]; }
        }

        public string AadClientId
        {
            get { return _config["AadClientId"]; }
        }

        public string AadSecret
        {
            get { return _config["AadSecret"]; }
        }

        public Uri ArmAadAudience
        {
            get { return new Uri(_config["ArmAadAudience"]); }
        }

        public Uri AadEndpoint
        {
            get { return new Uri(_config["AadEndpoint"]); }
        }

        public Uri ArmEndpoint
        {
            get { return new Uri(_config["ArmEndpoint"]); }
        }

        public string Location
        {
            get { return _config["Location"]; }
        }
    }
}
```

## <a name="connect-to-the-net-client"></a>Ligue-se ao cliente .NET

Para começar a utilizar as APIs dos Serviços de Multimédia com o .NET, tem de criar um objeto **AzureMediaServicesClient**. Para criar o objeto, tem de fornecer as credenciais necessárias para o cliente se ligar ao Azure com o Azure AD. No código abaixo, a função GetCredentialsAsync cria o objeto ServiceClientCredentials com base nas credenciais fornecidas no ficheiro de configuração local.

1. Abra `Program.cs`.
1. Cole o seguinte código:

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;

using Microsoft.Azure.Management.Media;
using Microsoft.Azure.Management.Media.Models;
using Microsoft.Extensions.Configuration;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Rest;
using Microsoft.Rest.Azure.Authentication;

namespace ConsoleApp1
{
    class Program
    {
        public static async Task Main(string[] args)
        {
            
            ConfigWrapper config = new ConfigWrapper(new ConfigurationBuilder()
                .SetBasePath(Directory.GetCurrentDirectory())
                .AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
                .AddEnvironmentVariables()
                .Build());

            try
            {
                IAzureMediaServicesClient client = await CreateMediaServicesClientAsync(config);
                Console.WriteLine("connected");
            }
            catch (Exception exception)
            {
                if (exception.Source.Contains("ActiveDirectory"))
                {
                    Console.Error.WriteLine("TIP: Make sure that you have filled out the appsettings.json file before running this sample.");
                }

                Console.Error.WriteLine($"{exception.Message}");

                ApiErrorException apiException = exception.GetBaseException() as ApiErrorException;
                if (apiException != null)
                {
                    Console.Error.WriteLine(
                        $"ERROR: API call failed with error code '{apiException.Body.Error.Code}' and message '{apiException.Body.Error.Message}'.");
                }
            }

            Console.WriteLine("Press Enter to continue.");
            Console.ReadLine();
        }
 
        private static async Task<ServiceClientCredentials> GetCredentialsAsync(ConfigWrapper config)
        {
            // Use ApplicationTokenProvider.LoginSilentWithCertificateAsync or UserTokenProvider.LoginSilentAsync to get a token using service principal with certificate
            //// ClientAssertionCertificate
            //// ApplicationTokenProvider.LoginSilentWithCertificateAsync

            // Use ApplicationTokenProvider.LoginSilentAsync to get a token using a service principal with symmetric key
            ClientCredential clientCredential = new ClientCredential(config.AadClientId, config.AadSecret);
            return await ApplicationTokenProvider.LoginSilentAsync(config.AadTenantId, clientCredential, ActiveDirectoryServiceSettings.Azure);
        }

        private static async Task<IAzureMediaServicesClient> CreateMediaServicesClientAsync(ConfigWrapper config)
        {
            var credentials = await GetCredentialsAsync(config);

            return new AzureMediaServicesClient(config.ArmEndpoint, credentials)
            {
                SubscriptionId = config.SubscriptionId,
            };
        }

    }
}
```

## <a name="next-steps"></a>Passos seguintes

- [Tutorial: Carregar, codificar e transmitir vídeos - .NET](stream-files-tutorial-with-api.md) 
- [Tutorial: Stream ao vivo com Serviços de Mídia v3 - .NET](stream-live-tutorial-with-api.md)
- [Tutorial: Analisar vídeos com Serviços de Comunicação v3 - .NET](analyze-videos-tutorial-with-api.md)
- [Criar uma entrada de trabalho a partir de um ficheiro local - .NET](job-input-from-local-file-how-to.md)
- [Criar uma entrada de trabalho a partir de um URL HTTPS - .NET](job-input-from-http-how-to.md)
- [Codificar com uma transformação personalizada - .NET](customize-encoder-presets-how-to.md)
- [Utilize encriptação dinâmica AES-128 e o serviço de entrega de chaves - .NET](protect-with-aes128.md)
- [Utilize o serviço de encriptação dinâmica DRM e entrega de licenças - .NET](protect-with-drm.md)
- [Obtenha uma chave de assinatura da política existente - .NET](get-content-key-policy-dotnet-howto.md)
- [Criar filtros com Serviços de Mídia - .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Exemplos avançados de vídeo a pedido de Azure Functions v2 com Media Services v3](https://aka.ms/ams3functions)

## <a name="see-also"></a>Ver também

* [.NET reference](/dotnet/api/overview/azure/mediaservices/management) (Referência de .NET)
* Para obter mais exemplos de código, consulte as [amostras .NET SDK.](https://github.com/Azure-Samples/media-services-v3-dotnet)
