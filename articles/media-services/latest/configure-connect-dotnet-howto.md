---
title: Ligue-se ao Azure Media Services v3 API - .NET
description: Este artigo demonstra como ligar aos Media Services v3 API com .NET.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2019
ms.author: juliako
ms.openlocfilehash: b8f4de1a5b9d8216ae2442631f5f9135c3c72d0b
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78359488"
---
# <a name="connect-to-media-services-v3-api---net"></a>Ligue-se aos Media Services v3 API - .NET

Este artigo mostra-lhe como se conectar ao Azure Media Services v3 .NET SDK utilizando o método principal de login do serviço.

## <a name="prerequisites"></a>Pré-requisitos

- [Criar uma conta de Media Services.](create-account-cli-how-to.md) Lembre-se do nome do grupo de recursos e do nome da conta Media Services
- Instale uma ferramenta que gostaria de utilizar para o desenvolvimento .NET. Os passos deste artigo mostram como usar o [Visual Studio 2019 Community Edition.](https://www.visualstudio.com/downloads/) Pode usar o Código do Estúdio Visual, ver [Trabalhar com C# ](https://code.visualstudio.com/docs/languages/csharp). Ou podes usar um editor de código diferente.

> [!IMPORTANT]
> Rever convenções de [nomeação.](media-services-apis-overview.md#naming-conventions)

## <a name="create-a-console-application"></a>Criar uma aplicação de consola

1. Inicie o Visual Studio. 
1. A partir do menu **'Ficheiro',** clique em **New** > **Project**. 
1. Crie uma aplicação de consola **.NET Core.**

A aplicação de amostras neste tópico, visa `netcoreapp2.0`. O código utiliza 'assync main', que C# está disponível a partir de 7.1. Consulte este [blog](https://blogs.msdn.microsoft.com/benwilli/2017/12/08/async-main-is-available-but-hidden/) para mais detalhes.

## <a name="add-required-nuget-packages"></a>Adicione os pacotes NuGet necessários

1. No Estúdio Visual, selecione **Tools** > **NuGet Package Manager** > **NuGet Manager Console**.
2. Na janela consola do Gestor de **Pacotes,** utilize `Install-Package` comando para adicionar os seguintes pacotes NuGet. Por exemplo, `Install-Package Microsoft.Azure.Management.Media`.

|Pacote|Descrição|
|---|---|
|`Microsoft.Azure.Management.Media`|Azure Media Services SDK. <br/>Para se certificar de que está a utilizar o mais recente pacote azure Media Services, consulte [microsoft.Azure.Management.Media](https://www.nuget.org/packages/Microsoft.Azure.Management.Media).|
|`Microsoft.Rest.ClientRuntime.Azure.Authentication`|Biblioteca de autenticação ADAL para Azure SDK para NET|
|`Microsoft.Extensions.Configuration.EnvironmentVariables`|Leia os valores de configuração de variáveis ambientais e ficheiros JSON locais|
|`Microsoft.Extensions.Configuration.Json`|Leia os valores de configuração de variáveis ambientais e ficheiros JSON locais
|`WindowsAzure.Storage`|Armazenamento SDK|

## <a name="create-and-configure-the-app-settings-file"></a>Criar e configurar o ficheiro de definições da aplicação

### <a name="create-appsettingsjson"></a>Criar appssettings.json

1. Vá ao **arquivo**de texto **geral** > .
1. Nomeie-o "appsettings.json".
1. Detete a propriedade "Copy to Output Directory" do ficheiro .json para "Copiar se for mais recente" (para que a aplicação possa aceder-lhe quando publicada).

### <a name="set-values-in-appsettingsjson"></a>Definir valores em apps.json

Executar o comando `az ams account sp create` conforme descrito nas [APIs](access-api-cli-how-to.md)de acesso . O comando devolve a JSOn que deve copiar para as suas "definições de apps.json".
 
## <a name="add-configuration-file"></a>Adicionar o ficheiro de configuração

Por conveniência, adicione um ficheiro de configuração responsável pela leitura de valores de "appsettings.json".

1. Adicione uma nova classe .cs ao seu projeto. Dê-lhe o nome `ConfigWrapper`. 
1. Colar o seguinte código neste ficheiro (este exemplo pressupõe que tem o espaço de nome é `ConsoleApp1`).

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

        public string Region
        {
            get { return _config["Region"]; }
        }
    }
}
```

## <a name="connect-to-the-net-client"></a>Ligue-se ao cliente .NET

Para começar a utilizar as APIs dos Serviços de Multimédia com o .NET, tem de criar um objeto **AzureMediaServicesClient**. Para criar o objeto, tem de fornecer as credenciais necessárias para o cliente se ligar ao Azure com o Azure AD. No código abaixo, a função GetCredentialsAsync cria o objeto ServiceClientCredenciacom base nas credenciais fornecidas no ficheiro de configuração local.

1. Abra `Program.cs`.
1. Colar o seguinte código:

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

- [Tutorial: Upload, codificação e streaming de vídeos - .NET](stream-files-tutorial-with-api.md) 
- [Tutorial: Stream live with Media Services v3 - .NET](stream-live-tutorial-with-api.md)
- [Tutorial: Analise vídeos com Media Services v3 - .NET](analyze-videos-tutorial-with-api.md)
- [Criar uma entrada de emprego a partir de um ficheiro local - .NET](job-input-from-local-file-how-to.md)
- [Criar uma entrada de trabalho a partir de um URL HTTPS - .NET](job-input-from-http-how-to.md)
- [Codificar com um Transform personalizado - .NET](customize-encoder-presets-how-to.md)
- [Utilize encriptação dinâmica AES-128 e o serviço de entrega de chaves - .NET](protect-with-aes128.md)
- [Utilize encriptação dinâmica DRM e serviço de entrega de licenças - .NET](protect-with-drm.md)
- [Obtenha uma chave de assinatura da política existente - .NET](get-content-key-policy-dotnet-howto.md)
- [Criar filtros com Serviços de Media - .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Exemplos avançados de vídeo a pedido de Funções Azure v2 com Media Services v3](https://aka.ms/ams3functions)

## <a name="see-also"></a>Consulte também

* [.NET reference](https://docs.microsoft.com/dotnet/api/overview/azure/mediaservices/management?view=azure-dotnet) (Referência de .NET)
* Para obter mais exemplos de código, consulte o repo das [amostras .NET SDK.](https://github.com/Azure-Samples/media-services-v3-dotnet)
