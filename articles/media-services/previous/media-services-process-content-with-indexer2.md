---
title: Indexação de ficheiros de mídia com Índice de Mídia Azure 2 Pré-visualização / Microsoft Docs
description: O Azure Media Indexer permite-lhe tornar o conteúdo dos seus ficheiros de mídia pesquisável e gerar uma transcrição de texto completo para legendagem fechada e palavras-chave. Este tópico mostra como usar o Media Indexer 2 Preview.
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
ms.date: 09/22/2019
ms.author: juliako
ms.reviewer: adsolank
ms.openlocfilehash: c24218dc116803ca0e0a1f166b7b54b24fc4d5ef
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78163799"
---
# <a name="indexing-media-files-with-azure-media-indexer-2-preview"></a>Indexação de ficheiros de mídia com índice de mídia Azure 2 Pré-visualização

> [!NOTE]
> O processador de mídia **Azure Media Indexer 2** será retirado. Para as datas de aposentadoria, consulte este tema de [componentes legados.](legacy-components.md) [O Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) substitui este processador de mídia legado. Para mais informações, consulte [Migrate from Azure Media Indexer e Azure Media Indexer 2 para Azure Media Services Video Indexer](migrate-indexer-v1-v2.md).

O processador de mídia **de pré-visualização Azure Media Indexer 2** (MP) permite-lhe tornar os ficheiros de mídia e conteúdos pesquisáveis, bem como gerar faixas de legendagem fechadas. Em comparação com a versão anterior do [Azure Media Indexer](media-services-index-content.md) **2 Preview** executa uma indexação mais rápida e oferece um suporte linguístico mais amplo. As línguas apoiadas incluem inglês, espanhol, francês, alemão, italiano, chinês (mandarim, simplificado), português, árabe, russo e japonês.

O Mp de **pré-visualização do Azure Media Indexer 2** está atualmente em Pré-visualização.

Este artigo mostra como criar empregos indexantes com **o Azure Media Indexer 2 Preview**.

## <a name="considerations"></a>Considerações

As seguintes considerações são aplicáveis:
 
* O Indexer 2 não é apoiado no Governo Azure China 21Vianet e Azure.
* Ao indexar o conteúdo, certifique-se de usar ficheiros de mídia que tenham uma fala muito clara (sem música de fundo, ruído, efeitos ou assobios do microfone). Alguns exemplos de conteúdo apropriado são: reuniões gravadas, palestras ou apresentações. Os seguintes conteúdos podem não ser adequados para indexação: filmes, séries de TV, qualquer coisa com efeitos de áudio e som mistos, conteúdo mal gravado com ruído de fundo (assobios).
 
## <a name="input-and-output-files"></a>Ficheiros de entrada e saída
### <a name="input-files"></a>Ficheiros de entrada
Ficheiros de áudio ou vídeo

### <a name="output-files"></a>Ficheiros de saída
Um trabalho de indexação pode gerar ficheiros de legendas fechados nos seguintes formatos:  

* **TTML**
* **WebVTT**

Os ficheiros de Legenda Fechada (CC) nestes formatos podem ser utilizados para tornar os ficheiros de áudio e vídeo acessíveis a pessoas com deficiência auditiva.

## <a name="task-configuration-preset"></a>Configuração de tarefas (predefinição)
Ao criar uma tarefa de indexação com o **Indexante Azure Media 2,** deve especificar um predefinição de configuração.

Os seguintes conjuntos JSON disponíveis.

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
A pré-visualização do Azure Media Indexer 2 suporta o discurso a texto para os seguintes idiomas (ao especificar o nome de idioma na configuração da tarefa, utilize o código de 4 caracteres nos parênteses abaixo indicados):

* Inglês [EnUs]
* Espanhol [Eses]
* Chinês (Mandarim, Simplificado) [ZhCn]
* Francês [FrFr]
* Alemão [Dede]
* Italiano [Itit]
* Português [PtBr]
* Árabe (egípcio) [ArEg]
* Japonês [JaJp]
* Russo [RuRu]
* Inglês Britânico [EnGb]
* Espanhol (México) [EsMx] 

## <a name="supported-file-types"></a>Tipos de ficheiro suportados

Para obter informações sobre os tipos de ficheiros suportados, consulte a secção de [codecs/formatos suportados.](media-services-media-encoder-standard-formats.md#input-containerfile-formats)

## <a name="net-sample-code"></a>Código de amostra .NET

O seguinte programa mostra como:

1. Crie um ativo e faça upload de um ficheiro de mídia para o ativo.
2. Crie um trabalho com uma tarefa de indexação com base num ficheiro de configuração que contenha o seguinte preset json:

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
    
3. Descarregue os ficheiros de saída. 
   
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
[Visão geral da Análise de Serviços de Mídia Azure](media-services-analytics-overview.md)

[Demonstrações azure Media Analytics](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

