---
title: Usar Azure Media Video Thumbnails para criar um resumo de vídeo | Microsoft Docs
description: O resumo de vídeo pode ajudá-lo a criar resumos de vídeos longos selecionando automaticamente trechos interessantes do vídeo de origem. Isso é útil quando você deseja fornecer uma visão geral rápida do que esperar em um vídeo longo.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: a245529f-3150-4afc-93ec-e40d8a6b761d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.reviewer: milanga
ms.openlocfilehash: a546df73f316b4eb6c215a6f52d68f87ad09c1a2
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72881696"
---
# <a name="use-azure-media-video-thumbnails-to-create-a-video-summarization"></a>Usar Azure Media Video Thumbnails para criar um resumo de vídeo  

> [!IMPORTANT]
> Examine os [planos de aposentadoria](media-services-analytics-overview.md#retirement-plans) de alguns processadores de mídia.

## <a name="overview"></a>Visão geral
O MP (processador de mídia **Azure Media Video thumbnails** ) permite que você crie um resumo de um vídeo que é útil para os clientes que desejam apenas visualizar um resumo de um vídeo longo. Por exemplo, os clientes talvez queiram ver um breve "vídeo de resumo" quando focalizam uma miniatura. Ao ajustar os parâmetros de **Azure Media Video thumbnails** por meio de uma predefinição de configuração, você pode usar a avançada tecnologia de detecção e concatenação do MP para forma algorítmica gerar um subclipe descritivo.  

O MP da **miniatura de vídeo de mídia do Azure** está atualmente em versão prévia.

Este artigo fornece detalhes sobre a **miniatura de vídeo de mídia do Azure** e mostra como usá-la com o SDK dos serviços de mídia para .net.

## <a name="limitations"></a>Limitações

Em alguns casos, se o vídeo não for composto de diferentes cenas, a saída será apenas uma única captura.

## <a name="video-summary-example"></a>Exemplo de Resumo de vídeo
Aqui estão alguns exemplos de como o processador de mídia Azure Media Video Thumbnails pode fazer:

### <a name="original-video"></a>Vídeo original
[Vídeo original](https://ampdemo.azureedge.net/azuremediaplayer.html?url=httpss%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Faed33834-ec2d-4788-88b5-a4505b3d032c%2FMicrosoft%27s%20HoloLens%20Live%20Demonstration.ism%2Fmanifest)

### <a name="video-thumbnail-result"></a>Resultado da miniatura do vídeo
[Resultado da miniatura do vídeo](https://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Ff5c91052-4232-41d4-b531-062e07b6a9ae%2FHololens%2520Demo_VideoThumbnails_MotionThumbnail.mp4)

## <a name="task-configuration-preset"></a>Configuração de tarefa (predefinição)
Ao criar uma tarefa de miniatura de vídeo com **Azure Media Video thumbnails**, você deve especificar uma predefinição de configuração. O exemplo de miniatura acima foi criado com a seguinte configuração básica de JSON:

```json
    {
        "version":"1.0"
    }
```

No momento, você pode alterar os seguintes parâmetros:

| Param | Descrição |
| --- | --- |
| outputAudio |Especifica se o vídeo resultante contém ou não qualquer áudio. <br/>Os valores permitidos são: true ou false. O padrão é true. |
| fadeInFadeOut |Especifica se as transições de esmaecimento são usadas ou não entre as miniaturas de movimento separadas.  <br/>Os valores permitidos são: true ou false.  O padrão é true. |
| maxMotionThumbnailDurationInSecs |Inteiro que especifica quanto tempo todo o vídeo resultante deve ser.  O padrão depende da duração original do vídeo. |

A tabela a seguir descreve a duração padrão quando **maxMotionThumbnailInSecs** não é usado.

|  |  |  |
| --- | --- | --- |
| Duração do vídeo |d < 3 min |3 min < d < 15 min |
| Duração da miniatura |15 s (2-3 cenas) |30 segundos (3-5 cenas) |

O JSON a seguir define os parâmetros disponíveis.

```json
    {
        "version": "1.0",
        "options": {
            "outputAudio": "true",
            "maxMotionThumbnailDurationInSecs": "10",
            "fadeInFadeOut": "true"
        }
    }
```

## <a name="net-sample-code"></a>Código de exemplo do .NET

O programa a seguir mostra como:

1. Crie um ativo e carregue um arquivo de mídia no ativo.
2. Cria um trabalho com uma miniatura de vídeo com base em um arquivo de configuração que contém a predefinição de JSON a seguir: 
    
    ```json
            {                
                "version": "1.0",
                "options": {
                    "outputAudio": "true",
                    "maxMotionThumbnailDurationInSecs": "30",
                    "fadeInFadeOut": "false"
                }
            }
    ```

3. Baixa os arquivos de saída. 

#### <a name="create-and-configure-a-visual-studio-project"></a>Criar e configurar um projeto de Visual Studio

Configure o seu ambiente de desenvolvimento e preencha o ficheiro app.config com informações da ligação, conforme descrito em [Media Services development with .NET](media-services-dotnet-how-to-use.md) (Desenvolvimento de Serviços de Multimédia com .NET). 

#### <a name="example"></a>Exemplo

```csharp
    using System;
    using System.Configuration;
    using System.IO;
    using System.Linq;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using System.Threading;
    using System.Threading.Tasks;

    namespace VideoSummarization
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


                // Run the thumbnail job.
                var asset = RunVideoThumbnailJob(@"C:\supportFiles\VideoThumbnail\BigBuckBunny.mp4",
                                            @"C:\supportFiles\VideoThumbnail\config.json");

                // Download the job output asset.
                DownloadAsset(asset, @"C:\supportFiles\VideoThumbnail\Output");
            }

            static IAsset RunVideoThumbnailJob(string inputMediaFilePath, string configurationFile)
            {
                // Create an asset and upload the input media file to storage.
                IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                    "My Video Thumbnail Input Asset",
                    AssetCreationOptions.None);

                // Declare a new job.
                IJob job = _context.Jobs.Create("My Video Thumbnail Job");

                // Get a reference to Azure Media Video Thumbnails.
                string MediaProcessorName = "Azure Media Video Thumbnails";

                var processor = GetLatestMediaProcessorByName(MediaProcessorName);

                // Read configuration from the specified file.
                string configuration = File.ReadAllText(configurationFile);

                // Create a task with the encoding details, using a string preset.
                ITask task = job.Tasks.AddNew("My Video Thumbnail Task",
                    processor,
                    configuration,
                    TaskOptions.None);

                // Specify the input asset.
                task.InputAssets.Add(asset);

                // Add an output asset to contain the results of the job.
                task.OutputAssets.AddNew("My Video Thumbnail Output Asset", AssetCreationOptions.None);

                // Use the following event handler to check job progress.  
                job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);

                // Launch the job.
                job.Submit();

                // Check job execution and wait for job to finish.
                Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);

                progressJobTask.Wait();

                // If job state is Error, the event handling
                // method for job progress should log errors.  Here we check
                // for error state and exit if needed.
                if (job.State == JobState.Error)
                {
                    ErrorDetail error = job.Tasks.First().ErrorDetails.First();
                    Console.WriteLine(string.Format("Error: {0}. {1}",
                                                    error.Code,
                                                    error.Message));
                    return null;
                }

                return job.OutputMediaAssets[0];
            }

            static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
            {
                IAsset asset = _context.Assets.Create(assetName, options);

                var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
                assetFile.Upload(filePath);

                return asset;
            }

            static void DownloadAsset(IAsset asset, string outputDirectory)
            {
                foreach (IAssetFile file in asset.AssetFiles)
                {
                    file.Download(Path.Combine(outputDirectory, file.Name));
                }
            }

            static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
            {
                var processor = _context.MediaProcessors
                    .Where(p => p.Name == mediaProcessorName)
                    .ToList()
                    .OrderBy(p => new Version(p.Version))
                    .LastOrDefault();

                if (processor == null)
                    throw new ArgumentException(string.Format("Unknown media processor",
                                                               mediaProcessorName));

                return processor;
            }

            static private void StateChanged(object sender, JobStateChangedEventArgs e)
            {
                Console.WriteLine("Job state changed event:");
                Console.WriteLine("  Previous state: " + e.PreviousState);
                Console.WriteLine("  Current state: " + e.CurrentState);

                switch (e.CurrentState)
                {
                    case JobState.Finished:
                        Console.WriteLine();
                        Console.WriteLine("Job is finished.");
                        Console.WriteLine();
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
                        // LogJobStop(job.Id);
                        break;
                    default:
                        break;
                }
            }

        }
    }
```

### <a name="video-thumbnail-output"></a>Saída de miniatura de vídeo
[Saída de miniatura de vídeo](https://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Fd06f24dc-bc81-488e-a8d0-348b7dc41b56%2FHololens%2520Demo_VideoThumbnails_MotionThumbnail.mp4)

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Hiperligações relacionadas
[Visão geral da análise dos serviços de mídia do Azure](media-services-analytics-overview.md)

[Demonstrações de Análise de Mídia do Azure](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

