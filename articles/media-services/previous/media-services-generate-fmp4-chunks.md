---
title: Criar uma tarefa de codificação da Azure Media Services que gere pedaços fMP4 [ Microsoft Docs
description: Este tópico mostra como criar uma tarefa de codificação que gera pedaços fMP4. Quando esta tarefa for utilizada com o codificador de fluxo de trabalho Media Encoder Standard ou Media Encoder Premium Workflow, o ativo de saída conterá pedaços fMP4 em vez de ficheiros ISO MP4.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 48c09a6100586c0c99f3d54f9708c770488c681f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "61463872"
---
#  <a name="create-an-encoding-task-that-generates-fmp4-chunks"></a>Criar uma tarefa de codificação que gere pedaços fMP4

## <a name="overview"></a>Descrição geral

Este artigo mostra como criar uma tarefa de codificação que gera pedaços de MP4 fragmentados (fMP4) em vez de ficheiros ISO MP4. Para gerar pedaços fMP4, utilize o codificador de fluxo de trabalho Media **Encoder Standard** ou **Media Encoder Premium Workflow** para criar uma tarefa de codificação e especificar também a **opção AssetFormatOption.AdaptiveStreaming,** como mostra este código:  
    
```csharp
    task.OutputAssets.AddNew(@"Output Asset containing fMP4 chunks", 
            options: AssetCreationOptions.None, 
            formatOption: AssetFormatOption.AdaptiveStreaming);
```

## <a name="encoding-with-media-services-net-sdk"></a><a id="encoding_with_dotnet"></a>Codificação com Serviços de Media .NET SDK

O exemplo de código que se segue utiliza media services .NET SDK para executar as seguintes tarefas:

- Criar um trabalho de codificação.
- Obtenha uma referência ao codificador Padrão do **Codificador De Media.**
- Adicione uma tarefa de codificação ao trabalho e especifique para utilizar o pré-definido **de Streaming Adaptativo.** 
- Crie um ativo de saída que contenha pedaços fMP4 e um ficheiro .ism.
- Adicione um manipulador de eventos para verificar o progresso do trabalho.
- Submeta o trabalho.

#### <a name="create-and-configure-a-visual-studio-project"></a>Criar e configurar um projeto de Visual Studio

Configure o seu ambiente de desenvolvimento e preencha o ficheiro app.config com informações da ligação, conforme descrito em [Media Services development with .NET](media-services-dotnet-how-to-use.md) (Desenvolvimento de Serviços de Multimédia com .NET). 

#### <a name="example"></a>Exemplo

```csharp
using System;
using System.Configuration;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Threading;

namespace AdaptiveStreaming
{
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

        // Field for service context.
        private static CloudMediaContext _context = null;

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            // Get an uploaded asset.
            var asset = _context.Assets.FirstOrDefault();

            // Encode and generate the output using the "Adaptive Streaming" preset.
            EncodeToAdaptiveBitrateMP4Set(asset);

            Console.ReadLine();
        }
        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("Media Encoder Standard Job");

            // Get a media processor reference, and pass to it the name of the 
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

            // Create a task
            ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
            processor,
            "Adaptive Streaming",
            TaskOptions.None);

            // Specify the input asset to be encoded.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job. 

            // This output is specified as AssetCreationOptions.None, which 
            // means the output asset is not encrypted. 
            // It is also specified to use AssetFormatOption.AdaptiveStreaming, 
            // which means the output asset will contain fMP4 chunks.

            task.OutputAssets.AddNew(@"Output Asset containing fMP4 chunks",
            options: AssetCreationOptions.None,
            formatOption: AssetFormatOption.AdaptiveStreaming);

            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
            job.Submit();
            job.GetExecutionProgressTask(CancellationToken.None).Wait();

            return job.OutputMediaAssets[0];
        }
        private static void JobStateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine("Job state changed event:");
            Console.WriteLine("  Previous state: " + e.PreviousState);
            Console.WriteLine("  Current state: " + e.CurrentState);
            switch (e.CurrentState)
            {
                case JobState.Finished:
                    Console.WriteLine();
                    Console.WriteLine("Job is finished. Please wait while local tasks or downloads complete...");
                    break;
                case JobState.Canceling:
                case JobState.Queued:
                case JobState.Scheduled:
                case JobState.Processing:
                    Console.WriteLine("Please wait...\n");
                    break;
                case JobState.Canceled:
                case JobState.Error:

                    // Cast sender as a job.
                    IJob job = (IJob)sender;

                    // Display or log error details as needed.
                    break;
                default:
                    break;
            }
        }
        private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
        {
            var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
            ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

            if (processor == null)
                throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

            return processor;
        }
    }
}
```

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Veja também
[Visão geral de codificação de serviços de mídia](media-services-encode-asset.md)

