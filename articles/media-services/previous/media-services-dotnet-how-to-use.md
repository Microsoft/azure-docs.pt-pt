---
title: Como configurar o computador para o desenvolvimento de serviços de multimédia com .NET
description: Saiba mais sobre os pré-requisitos para serviços de multimédia com o SDK de Media Services para .NET. Além disso, saiba como criar uma aplicação do Visual Studio.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: ec2804c7-c656-4fbf-b3e4-3f0f78599a7f
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/12/2019
ms.author: juliako
ms.openlocfilehash: 7da57c01b6717b505ea09fa9e6197bab4d4976fd
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57873830"
---
# <a name="media-services-development-with-net"></a>Desenvolvimento de serviços de multimédia com .NET 
[!INCLUDE [media-services-selector-setup](../../../includes/media-services-selector-setup.md)]

Este artigo descreve como começar a desenvolver aplicativos de serviços de multimédia com .NET.

O **SDK de .NET de serviços de multimédia do Azure** biblioteca permite-lhe programar serviços de multimédia com .NET. Para facilitar ainda mais fácil de programar com .NET, o **extensões do SDK de .NET do Azure Media Services** biblioteca é fornecida. Esta biblioteca contém um conjunto de métodos de extensão e funções de programa auxiliar que simplificam o seu código .NET. Ambas as bibliotecas estão disponíveis por meio **NuGet** e **GitHub**.

## <a name="prerequisites"></a>Pré-requisitos
* Uma conta dos Media Services numa subscrição Azure nova ou existente. Consulte o artigo [como criar uma conta dos Media Services](media-services-portal-create-account.md).
* Sistemas operativos: Windows 10, Windows 7, Windows 2008 R2 ou Windows 8.
* .NET framework 4.5 ou posterior.
* Visual Studio.

## <a name="create-and-configure-a-visual-studio-project"></a>Criar e configurar um projeto de Visual Studio
Esta secção mostra-lhe como criar um projeto no Visual Studio e configurá-lo para o desenvolvimento de Media Services.  Neste caso, o projeto é um C# aplicação de consola do Windows, mas os mesmos passos de configuração mostrados aqui, que se aplica a outros tipos de projetos, pode criar para aplicativos de serviços de multimédia (por exemplo, um aplicativo Windows Forms ou um aplicativo ASP.NET Web).

Esta secção mostra como usar **NuGet** para adicionar extensões do SDK .NET dos Media Services e outras bibliotecas dependentes.

Em alternativa, pode obter os bits mais recentes do SDK .NET dos Media Services a partir do GitHub ([github.com/Azure/azure-sdk-for-media-services](https://github.com/Azure/azure-sdk-for-media-services) ou [github.com/Azure/azure-sdk-for-media-services-extensions](https://github.com/Azure/azure-sdk-for-media-services-extensions)), crie a solução e adicionar as referências ao projeto cliente. Todas as dependências necessárias obterem baixar e extrair automaticamente.

1. Crie uma nova Aplicação de Consola C# no Visual Studio. Introduza o **Name**, **localização**, e **nome da solução**e, em seguida, clique em OK.
2. Compilar a solução.
3. Uso **NuGet** para instalar e adicionar **extensões do SDK de .NET do Azure Media Services** (**windowsazure.mediaservices.extensions**). Ao instalar este pacote, também é instalado o **SDK do .NET dos Media Services** e são adicionadas todas as outras dependências necessárias.
   
    Certifique-se de que tem a versão mais recente do NuGet instalado. Para obter mais instruções de instalação e informações, consulte [NuGet](https://nuget.codeplex.com/).

    1. No Explorador de soluções, faça duplo clique o nome do projeto e escolha **gerir pacotes NuGet**.

    2. É apresentada a caixa de diálogo Gerir pacotes NuGet.

    3. Na Galeria Online, procure extensões de MediaServices do Azure, escolha **extensões do SDK de .NET do Azure Media Services** (**windowsazure.mediaservices.extensions**) e, em seguida, clique no  **Instalar** botão.
   
    4. O projeto é modificado e referências a extensões do SDK de .NET de serviços de suporte de dados, o SDK .NET dos Media Services e outros conjuntos de módulos dependentes são adicionadas.
4. Para promover um ambiente de desenvolvimento mais limpo, considere a ativar o restauro do pacote NuGet. Para obter mais informações, consulte [restauro do pacote NuGet "](https://docs.nuget.org/consume/package-restore).
5. Adicionar uma referência a **Configuration** assembly. Esse assembly contém o System. **ConfigurationManager** classe que é utilizada para aceder aos ficheiros de configuração (por exemplo, App. config).
   
    1. Para adicionar referências com a caixa de diálogo de referências de gerir, clique com botão direito no nome do projeto no Solution Explorer. Em seguida, clique em **Add**, em seguida, clique em **referência...** .
   
    2. É apresentada a caixa de diálogo de referências de gerir.
    3. Em assemblies do .NET framework, localize e selecione o assembly System e prima **OK**.
6. Abra o ficheiro App. config e adicione uma **appSettings** secção para o ficheiro. Defina os valores que são necessários para ligar à API de serviços de multimédia. Para obter mais informações, consulte [aceder a API de serviços de multimédia do Azure com a autenticação do Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

    Defina os valores que são necessárias para estabelecer ligação utilizando o **principal de serviço** método de autenticação.

        ```csharp
                <configuration>
                ...
                    <appSettings>
                        <add key="AMSAADTenantDomain" value="tenant"/>
                        <add key="AMSRESTAPIEndpoint" value="endpoint"/>
                        <add key="AMSClientId" value="id"/>
                        <add key="AMSClientSecret" value="secret"/>
                    </appSettings>
                </configuration>
        ```

7. Adicionar a **Configuration** referência ao seu projeto.
8. Substituir o existente **usando** instruções no início do ficheiro Program.cs pelo seguinte código:

    ```csharp      
            using System;
            using System.Configuration;
            using System.IO;
            using Microsoft.WindowsAzure.MediaServices.Client;
            using System.Threading;
            using System.Collections.Generic;
            using System.Linq;
    ```

    Neste momento, está pronto para começar a desenvolver um aplicativo de serviços de multimédia.    

## <a name="example"></a>Exemplo

Eis um pequeno exemplo que se liga à AMS API e apresenta uma lista de todos os processadores de multimédia disponíveis.

```csharp
        class Program
        {
            // Read values from the App.config file.

            private static readonly string _AADTenantDomain =
                ConfigurationManager.AppSettings["AMSAADTenantDomain"];
            private static readonly string _RESTAPIEndpoint =
                ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
            private static readonly string _AMSClientId =
                ConfigurationManager.AppSettings["AMSClientId"];
            private static readonly string _AMSClientSecret =
                ConfigurationManager.AppSettings["AMSClientSecret"];
        
            private static CloudMediaContext _context = null;
            static void Main(string[] args)
            {
                AzureAdTokenCredentials tokenCredentials = 
                    new AzureAdTokenCredentials(_AADTenantDomain,
                        new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                        AzureEnvironments.AzureCloudEnvironment);

                var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

                _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);
        
                // List all available Media Processors
                foreach (var mp in _context.MediaProcessors)
                {
                    Console.WriteLine(mp.Name);
                }
        
            }
 ```

## <a name="next-steps"></a>Passos Seguintes

Agora [pode ligar à AMS API](media-services-use-aad-auth-to-access-ams-api.md) e comece [desenvolver](media-services-dotnet-get-started.md).


## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

