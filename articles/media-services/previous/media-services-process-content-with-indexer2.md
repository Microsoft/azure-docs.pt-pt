---
title: Indexe ficheiros multimédia com a pré-visualização de 2 de indexador de multimédia do Azure | Documentos da Microsoft
description: O indexador de multimédia do Azure permite-lhe para tornar o conteúdo dos seus ficheiros multimédia pesquisáveis e gerar uma transcrição de texto completo para as legendas de áudio e palavras-chave fechado. Este tópico mostra como utilizar a pré-visualização de 2 de indexador de multimédia.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/19/2019
ms.author: adsolank;juliako;
ms.openlocfilehash: 6a798d0f33ba4d1960f07e670e6e71916f68eff1
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58258275"
---
# <a name="indexing-media-files-with-azure-media-indexer-2-preview"></a>Indexe ficheiros multimédia com a pré-visualização de 2 de indexador de multimédia do Azure
## <a name="overview"></a>Descrição geral
O **pré-visualização do Azure Media Indexer 2** processador de multimédia (MP) permite-lhe tornar pesquisável os arquivos de mídia e conteúdo, bem como gerar faixas de legenda codificadas fechadas. Em comparação com a versão anterior do [Azure Media Indexer](media-services-index-content.md), **pré-visualização do Azure Media Indexer 2** executa a indexação mais rápida e oferece suporte a idiomas mais amplo. Idiomas suportados incluem o inglês, espanhol, francês, alemão, italiano, chinês (simplificado, de Mandarim), português, árabe, russo e japonês.

O **pré-visualização do Azure Media Indexer 2** MP está atualmente em pré-visualização.

Este artigo mostra como criar tarefas de indexação com **pré-visualização do Azure Media Indexer 2**.

> [!NOTE]
> As seguintes considerações aplicam-se:
> 
> O indexador 2 não é suportado no Azure China e Azure Government.
> 
> Quando precisa de indexar conteúdo, certifique-se utilizar ficheiros de suporte de dados que tenham fala muito clara (sem música em segundo plano, ruído, efeitos ou hiss microfone). Alguns exemplos de conteúdo apropriado são: registadas reuniões, palestras ou apresentações. O seguinte conteúdo pode não ser adequado para indexação: filmes, programas de TV, qualquer coisa com áudio misto e efeitos sonoros, mal registadas conteúdo com o barulho de fundo (hiss).
> 
> 

Este artigo fornece detalhes sobre **pré-visualização do Azure Media Indexer 2** e mostra como utilizá-lo com o SDK de Media Services para .NET

## <a name="input-and-output-files"></a>Ficheiros de entrada e saídos
### <a name="input-files"></a>Ficheiros de entrada
Arquivos de áudio ou vídeos

### <a name="output-files"></a>Ficheiros de saída
Uma tarefa de indexação pode gerar ficheiros de legendas nos seguintes formatos:  

* **SAMI**
* **TTML**
* **WebVTT**

Fechado (CC) de legenda arquivos esses formatos podem ser usados para tornar os arquivos de áudio e vídeo acessível para pessoas portadoras de deficiência audição.

## <a name="task-configuration-preset"></a>Configuração da tarefa (predefinição)
Quando criar uma indexação de tarefas com **pré-visualização do Azure Media Indexer 2**, tem de especificar uma predefinição de configuração.

O JSON seguinte define os parâmetros disponíveis.

```json
    {
      "version":"1.0",
      "Features":
        [
           {
           "Options": {
                "Formats":["WebVtt","ttml"],
                "Language":"enUs",
                "Type":"RecoOptions"
           },
           "Type":"SpReco"
        }]
    }
```

## <a name="supported-languages"></a>Linguagens suportadas
Pré-visualização de 2 de indexador de multimédia do Azure suporta a conversão de voz em texto para os seguintes idiomas (ao especificar o nome do idioma na configuração da tarefa, utilize o código de 4 carateres entre parênteses Retos, conforme mostrado abaixo):

* Inglês [EnUs]
* Espanhol [EsEs]
* Chinês (Mandarim, simplificado) [ZhCn]
* Francês [FrFr]
* Alemão [DeDe]
* Italiano [ItIt]
* Português [PtBr]
* Árabe (Egyptian) [ArEg]
* Japonês [JaJp]
* Russo [RuRu]
* Inglês britânico [EnGb]
* Espanhol do México [EsMx] 

## <a name="supported-file-types"></a>Tipos de ficheiro suportados

Para obter informações sobre os tipos de ficheiros suportados, consulte a [codecs/formatos suportados](media-services-media-encoder-standard-formats.md#input-containerfile-formats) secção.

## <a name="net-sample-code"></a>Código de exemplo do .NET

O seguinte programa mostra como:

1. Criar um elemento e carregue um ficheiro de multimédia para o elemento.
2. Crie uma tarefa com uma tarefa de indexação com base num arquivo de configuração que contém a seguinte configuração predefinida de json:

    ```json
            {
            "version":"1.0",
            "Features":
                [
                {
                "Options": {
                        "Formats":["WebVtt","ttml"],
                        "Language":"enUs",
                        "Type":"RecoOptions"
                },
                "Type":"SpReco"
                }]
            }
    ```
    
3. Transferir os ficheiros de saída. 
   
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

namespace IndexContent
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

            // Run indexing job.
            var asset = RunIndexingJob(@"C:\supportFiles\Indexer\BigBuckBunny.mp4",
                                        @"C:\supportFiles\Indexer\config.json");

            // Download the job output asset.
            DownloadAsset(asset, @"C:\supportFiles\Indexer\Output");
        }

        static IAsset RunIndexingJob(string inputMediaFilePath, string configurationFile)
        {
            // Create an asset and upload the input media file to storage.
            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                "My Indexing Input Asset",
                AssetCreationOptions.None);

            // Declare a new job.
            IJob job = _context.Jobs.Create("My Indexing Job");

            // Get a reference to Azure Media Indexer 2 Preview.
            string MediaProcessorName = "Azure Media Indexer 2 Preview";

            var processor = GetLatestMediaProcessorByName(MediaProcessorName);

            // Read configuration from the specified file.
            string configuration = File.ReadAllText(configurationFile);

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My Indexing Task",
                processor,
                configuration,
                TaskOptions.None);

            // Specify the input asset to be indexed.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("My Indexing Output Asset", AssetCreationOptions.None);

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

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Ligações relacionadas
[Descrição geral da análise dos serviços de multimédia do Azure](media-services-analytics-overview.md)

[Demonstrações de análise de multimédia do Azure](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

