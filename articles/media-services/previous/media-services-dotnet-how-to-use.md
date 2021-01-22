---
title: Como configurar computador para desenvolvimento de serviços de mídia com .NET
description: Conheça os pré-requisitos para serviços de mídia utilizando o SDK dos Serviços de Comunicação Social para .NET. Também aprenda a criar uma aplicação visual Studio.
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
ms.date: 03/18/2019
ms.author: juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: dda7849b6a5e22eea4891eacb2678b4c500dc1e1
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98693668"
---
# <a name="media-services-development-with-net"></a>Desenvolvimento de Serviços de Mídia com .NET

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Não serão adicionadas novas funcionalidades aos Serviços de Multimédia v2. <br/>Confira a versão mais recente, [Media Services v3](../latest/index.yml). Além disso, consulte [a orientação de migração de v2 para v3](../latest/migrate-v-2-v-3-migration-introduction.md)

Este artigo discute como começar a desenvolver aplicações de Serviços de Mídia usando .NET.

A biblioteca **Azure Media Services .NET SDK** permite-lhe programar contra os Serviços de Mídia utilizando .NET. Para facilitar ainda mais o desenvolvimento com .NET, é fornecida a biblioteca **de extensões Azure Media Services .NET SDK.** Esta biblioteca contém um conjunto de métodos de extensão e funções de ajudante que simplificam o seu código .NET. Ambas as bibliotecas estão disponíveis através **do NuGet** e **do GitHub.**

## <a name="prerequisites"></a>Pré-requisitos
* Uma conta dos Media Services numa subscrição Azure nova ou existente. Consulte o artigo [Como Criar uma Conta de Serviços de Mídia.](media-services-portal-create-account.md)
* Sistemas Operativos: Windows 10, Windows 7, Windows 2008 R2 ou Windows 8.
* .NET Quadro 4.5 ou posterior.
* Visual Studio.

## <a name="create-and-configure-a-visual-studio-project"></a>Criar e configurar um projeto de Visual Studio
Esta secção mostra-lhe como criar um projeto no Visual Studio e criá-lo para o desenvolvimento dos Media Services.  Neste caso, o projeto é uma aplicação de consola C# Windows, mas os mesmos passos de configuração mostrados aqui aplicam-se a outros tipos de projetos que pode criar para aplicações de Serviços de Mídia (por exemplo, uma aplicação Windows Forms ou uma aplicação web ASP.NET).

Esta secção mostra como utilizar **o NuGet** para adicionar extensões net SDK e outras bibliotecas dependentes.

Em alternativa, pode obter os mais recentes bits net SDK dos Serviços de Media (NET SDK) do GitHub[(github.com/Azure/azure-sdk-for-media-services](https://github.com/Azure/azure-sdk-for-media-services) ou [github.com/Azure/azure-sdk-for-media-services-extensions),](https://github.com/Azure/azure-sdk-for-media-services-extensions)construir a solução e adicionar as referências ao projeto do cliente. Todas as dependências necessárias são descarregadas e extraídas automaticamente.

1. Crie uma nova Aplicação de Consola C# no Visual Studio. Introduza o **nome Nome,** **Localização** e **Solução** e, em seguida, clique em OK.
2. Compilar a solução.
3. Utilize **o NuGet** para instalar e adicionar **extensões Azure Media Services .NET SDK** **(windowsazure.mediaservices.extensions**). Ao instalar este pacote, também é instalado o **SDK do .NET dos Media Services** e são adicionadas todas as outras dependências necessárias.
   
    Certifique-se de que tem a versão mais recente do NuGet instalada. Para obter mais informações e instruções de instalação, consulte [o NuGet](https://nuget.codeplex.com/).

    1. No Solution Explorer, clique com o botão direito no nome do projeto e escolha **Gerir pacotes NuGet**.

    2. A caixa de diálogo Gerir Pacotes NuGet aparece.

    3. Na galeria Online, procure extensões Azure MediaServices, escolha **extensões Azure Media Services .NET SDK** **(windowsazure.mediaservices.extensions**) e, em seguida, clique no botão **Instalar.**
   
    4. O projeto é modificado e são adicionadas referências às Extensões Net SDK dos Serviços de Comunicação Social,Serviços de Mídia .NET SDK e outras assembleias dependentes.
4. Para promover um ambiente de desenvolvimento mais limpo, considere permitir a restauração do pacote NuGet. Para mais informações, consulte [o NuGet Package Restore"](https://docs.nuget.org/consume/package-restore).
5. Adicione uma referência àSystem.Configconjunto **de uration.** Esta assembléia contém a System.Configuration. **Configuração ClasseManager** que é usada para aceder a ficheiros de configuração (por exemplo, App.config).
   
    1. Para adicionar referências utilizando o diálogo 'Gerir referências', clique com o botão direito no nome do projeto no Solution Explorer. Em seguida, clique em **Adicionar,** em seguida, clique em **Referência...**.
   
    2. Aparece o diálogo 'Gestão referências'.
    3. Em conjuntos de quadros .NET, encontre e selecione o conjunto de System.Configuration e prima **OK**.
6. Abra o ficheiro App.config e adicione uma secção **de appsSettings** ao ficheiro. Desa estatudo os valores necessários para ligar à API dos Serviços de Mídia. Para mais informações, consulte [Aceda à AZure Media Services API com autenticação AD Azure.](media-services-use-aad-auth-to-access-ams-api.md) 

    Desa esta medida os valores necessários para ligar utilizando o método de autenticação **principal do Serviço.**

    ```xml
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

7. Adicione a **referênciaSystem.Configuration** ao seu projeto.
8. Sobrepor as declarações **existentes** no início do ficheiro Program.cs com o seguinte código:

    ```csharp      
    using System;
    using System.Configuration;
    using System.IO;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using System.Threading;
    using System.Collections.Generic;
    using System.Linq;
    ```

    Neste momento, está pronto para começar a desenvolver uma aplicação de Serviços de Mídia.    

## <a name="example"></a>Exemplo

Aqui está um pequeno exemplo que se conecta à AMS API e lista todos os Processadores de Mídia disponíveis.

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

## <a name="next-steps"></a>Passos seguintes

Agora [pode ligar-se à AMS API](media-services-use-aad-auth-to-access-ams-api.md) e começar [a desenvolver- se.](media-services-dotnet-get-started.md)


## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
