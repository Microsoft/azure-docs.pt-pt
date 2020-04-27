---
title: Como configurar o computador para o desenvolvimento de serviços de mídia com .NET
description: Conheça os pré-requisitos para os Serviços de Media utilizando o SDK de Serviços de Media para .NET. Também aprenda a criar uma aplicação de Estúdio Visual.
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
ms.openlocfilehash: 51fffbd170daecfec6fcea95caa0526e6d881407
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/26/2020
ms.locfileid: "64724116"
---
# <a name="media-services-development-with-net"></a>Desenvolvimento de Serviços de Media com .NET 

> [!NOTE]
> Não serão adicionadas novas funcionalidades aos Serviços de Multimédia v2. <br/>Confira a versão mais recente, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Consulte também [a orientação de migração da v2 para a v3](../latest/migrate-from-v2-to-v3.md)

Este artigo discute como começar a desenvolver aplicações de Serviços de Media usando .NET.

A biblioteca **Azure Media Services .NET SDK** permite-lhe programar contra os Serviços de Media utilizando .NET. Para facilitar ainda mais o desenvolvimento com .NET, é fornecida a biblioteca de **extensões Azure Media Services .NET SDK.** Esta biblioteca contém um conjunto de métodos de extensão e funções auxiliares que simplificam o seu código .NET. Ambas as bibliotecas estão disponíveis através **do NuGet** e do **GitHub.**

## <a name="prerequisites"></a>Pré-requisitos
* Uma conta dos Media Services numa subscrição Azure nova ou existente. Veja o artigo Como Criar uma Conta de [Serviços de Media](media-services-portal-create-account.md).
* Sistemas operativos: Windows 10, Windows 7, Windows 2008 R2 ou Windows 8.
* .NET Quadro 4.5 ou posterior.
* Visual Studio.

## <a name="create-and-configure-a-visual-studio-project"></a>Criar e configurar um projeto de Visual Studio
Esta secção mostra-lhe como criar um projeto no Estúdio Visual e defini-lo para o desenvolvimento de Media Services.  Neste caso, o projeto é uma aplicação de consola C# Windows, mas os mesmos passos de configuração aqui mostrados aplicam-se a outros tipos de projetos que pode criar para aplicações de Media Services (por exemplo, uma aplicação Windows Forms ou uma aplicação Web ASP.NET).

Esta secção mostra como usar o **NuGet** para adicionar extensões De SDK .NET SDK e outras bibliotecas dependentes.

Em alternativa, pode obter os mais recentes media Services .NET SDK bits do GitHub ([github.com/Azure/azure-sdk-for-media-services](https://github.com/Azure/azure-sdk-for-media-services) ou [github.com/Azure/azure-sdk-for-media-services-extensions](https://github.com/Azure/azure-sdk-for-media-services-extensions)), construir a solução e adicionar as referências ao projeto do cliente. Todas as dependências necessárias são descarregadas e extraídas automaticamente.

1. Crie uma nova Aplicação de Consola C# no Visual Studio. Introduza o **nome**, **localização**e **nome da solução,** e clique em OK.
2. Compilar a solução.
3. Utilize o **NuGet** para instalar e adicionar **as extensões Azure Media Services .NET SDK** **(windowsazure.mediaservices.extensions**). Ao instalar este pacote, também é instalado o **SDK do .NET dos Media Services** e são adicionadas todas as outras dependências necessárias.
   
    Certifique-se de que tem a versão mais recente do NuGet instalada. Para obter mais informações e instruções de instalação, consulte [NuGet](https://nuget.codeplex.com/).

    1. No Solution Explorer, clique no nome do projeto e escolha **Gerir pacotes NuGet**.

    2. A caixa de diálogo Gerir Pacotes NuGet aparece.

    3. Na galeria Online, procure extensões azure MediaServices, escolha **extensões Azure Media Services .NET SDK** **(windowsazure.mediaservices.extensions)** e, em seguida, clique no botão **Instalar.**
   
    4. O projeto é modificado e são adicionadas referências às extensões sdk dos Media Services .NET, Media Services .NET SDK e outras assembleias dependentes.
4. Para promover um ambiente de desenvolvimento mais limpo, considere permitir a Restauração do Pacote NuGet. Para mais informações, consulte [NuGet Package Restore".](https://docs.nuget.org/consume/package-restore)
5. Adicione uma referência ao **conjunto System.Configuração.** Esta montagem contém o Sistema.Configuração. **ConfiguraçãoClasse Gestor** que é usada para aceder a ficheiros de configuração (por exemplo, App.config).
   
    1. Para adicionar referências utilizando o diálogo 'Gerir referências', clique no nome do projeto no Solution Explorer. Em seguida, clique em **Adicionar,** em seguida, clique em **Referência...**.
   
    2. Aparece o diálogo 'Referências de Gestão'.
    3. Em conjuntos de quadros .NET, encontre e selecione o conjunto de configuração do Sistema e prima **OK**.
6. Abra o ficheiro App.config e adicione uma secção de definições de **aplicações** ao ficheiro. Detete os valores necessários para se ligar à API dos Serviços de Media. Para mais informações, consulte [Aceda à API dos Serviços de Mídia Azure com autenticação Azure AD.](media-services-use-aad-auth-to-access-ams-api.md) 

    Detete os valores necessários para se ligar utilizando o método de autenticação principal do **Serviço.**

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

7. Adicione a referência **system.Configuração** ao seu projeto.
8. Reveja as declarações **existentes** no início do ficheiro Program.cs com o seguinte código:

    ```csharp      
            using System;
            using System.Configuration;
            using System.IO;
            using Microsoft.WindowsAzure.MediaServices.Client;
            using System.Threading;
            using System.Collections.Generic;
            using System.Linq;
    ```

    Neste momento, está pronto para começar a desenvolver uma aplicação de Serviços de Media.    

## <a name="example"></a>Exemplo

Aqui está um pequeno exemplo que se conecta à AMS API e lista todos os Processadores de Media disponíveis.

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

Agora [pode ligar-se à AmS API](media-services-use-aad-auth-to-access-ams-api.md) e começar [a desenvolver.](media-services-dotnet-get-started.md)


## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

