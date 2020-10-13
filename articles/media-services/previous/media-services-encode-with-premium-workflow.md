---
title: Codificação avançada com media Encoder Premium Workflow / Microsoft Docs
description: Saiba como codificar com o Fluxo de Trabalho Premium Media Encoder. As amostras de código são escritas em C# e utilizam o SDK dos Serviços de Mídia para .NET.
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
ms.custom: devx-track-csharp
ms.openlocfilehash: 29a889fc15b1889711d08d3386920652a8bc8f57
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89269051"
---
# <a name="advanced-encoding-with-media-encoder-premium-workflow"></a>Advanced encoding with Media Encoder Premium Workflow (Codificação avançada com o Media Encoder Premium Workflow)

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> O processador de mídia Media Encoder Premium Workflow discutido neste artigo não está disponível na China.
>
>

## <a name="overview"></a>Descrição geral
A Microsoft Azure Media Services está a introduzir o processador de mídia **Media Encoder Premium Workflow.** Este processador oferece capacidades de codificação antecipadas para os seus fluxos de trabalho premium a pedido.

Os seguintes tópicos delineiam detalhes relacionados com **o fluxo de trabalho premium da Media Encoder:**

* [Formatos Suportados pelo Fluxo de Trabalho Premium Media Encoder](media-services-premium-workflow-encoder-formats.md) – Discute os formatos de ficheiros e codecs suportados pelo **Fluxo de Trabalho Premium Media Encoder**.
* [A visão geral e a comparação dos codificadores de mídia Azure on-demand](media-services-encode-asset.md) comparam as capacidades de codificação do **Media Encoder Premium Workflow** e **da Media Encoder Standard**.

Este artigo demonstra como codificar com o Fluxo de **Trabalho Premium Media Encoder** utilizando .NET.

As tarefas de codificação para o **Fluxo de Trabalho Premium Media Encoder** requerem um ficheiro de configuração separado, chamado ficheiro Workflow. Estes ficheiros têm uma extensão de fluxo de trabalho e são criados utilizando a ferramenta [Workflow Designer.](media-services-workflow-designer.md)

Também pode obter os ficheiros de fluxo de trabalho [predefinidos aqui](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows). A pasta também contém a descrição destes ficheiros.

Os ficheiros de fluxo de trabalho precisam de ser enviados para a sua conta de Serviços de Media como Um Ativo, e este Ativo deve ser transmitido para a Tarefa de codificação.

## <a name="create-and-configure-a-visual-studio-project"></a>Criar e configurar um projeto de Visual Studio

Configure o seu ambiente de desenvolvimento e preencha o ficheiro app.config com informações da ligação, conforme descrito em [Media Services development with .NET](media-services-dotnet-how-to-use.md) (Desenvolvimento de Serviços de Multimédia com .NET). 

## <a name="encoding-example"></a>Exemplo de codificação

O exemplo a seguir demonstra como codificar com o Fluxo de **Trabalho Premium Media Encoder**.

São realizados os seguintes passos:

1. Crie um ativo e carre fique com um ficheiro de fluxo de trabalho.
2. Crie um ativo e carre fique com um ficheiro de mídia de origem.
3. Obtenha o processador de mídia "Media Encoder Premium Workflow".
4. Criar um emprego e uma tarefa.

    Na maioria dos casos, a cadeia de configuração para a tarefa está vazia (como no exemplo seguinte). Existem alguns cenários avançados (que requerem que você desemcorde as propriedades de tempo de execução dinamicamente) nesse caso você forneceria uma cadeia XML para a tarefa de codificação. Exemplos de tais cenários são: criação de uma sobreposição, costura em meios paralelos ou sequenciais, legendagem.
5. Adicione dois ativos de entrada à tarefa.

   1. 1º – o ativo do fluxo de trabalho.
   2. 2º – o ativo de vídeo.

      >[!NOTE]
      >O ativo de fluxo de trabalho deve ser adicionado à tarefa antes do ativo dos meios de comunicação.
      A cadeia de configuração desta tarefa deve estar vazia.
   
6. Submeta o trabalho de codificação.

```csharp
using System;
using System.Linq;
using System.Configuration;
using System.IO;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.WindowsAzure.MediaServices.Client;

namespace MediaEncoderPremiumWorkflowSample
{
    class Program
    {
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

        private static readonly string _supportFiles =
            Path.GetFullPath(@"../..\Media");

        private static readonly string _workflowFilePath =
            Path.GetFullPath(_supportFiles + @"\H264 Progressive Download MP4.workflow");

        private static readonly string _singleMP4InputFilePath =
            Path.GetFullPath(_supportFiles + @"\BigBuckBunny.mp4");

        static void Main(string[] args)
        {

            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            var workflowAsset = CreateAssetAndUploadSingleFile(_workflowFilePath);
            var videoAsset = CreateAssetAndUploadSingleFile(_singleMP4InputFilePath);
            IAsset outputAsset = CreateEncodingJob(workflowAsset, videoAsset);

        }

        static public IAsset CreateAssetAndUploadSingleFile(string singleFilePath)
        {
            var assetName = "UploadSingleFile_" + DateTime.UtcNow.ToString();
            var asset = _context.Assets.Create(assetName, AssetCreationOptions.None);

            var fileName = Path.GetFileName(singleFilePath);

            var assetFile = asset.AssetFiles.Create(fileName);

            Console.WriteLine("Created assetFile {0}", assetFile.Name);

            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading {0}", assetFile.Name);

            return asset;
        }

        static public IAsset CreateEncodingJob(IAsset workflow, IAsset video)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("Premium Workflow encoding job");
            // Get a media processor reference, and pass to it the name of the
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Premium Workflow");

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("Premium Workflow encoding task",
                processor,
                "",
                TaskOptions.None);

            // Specify the input asset to be encoded.
            task.InputAssets.Add(workflow);
            task.InputAssets.Add(video); // we add one asset
                                         // Add an output asset to contain the results of the job.
                                         // This output is specified as AssetCreationOptions.None, which
                                         // means the output asset is not encrypted.
            task.OutputAssets.AddNew("Output asset",
                AssetCreationOptions.None);

            // Use the following event handler to check job progress.  
            job.StateChanged += new
                    EventHandler<JobStateChangedEventArgs>(StateChanged);

            // Launch the job.
            job.Submit();

            // Check job execution and wait for job to finish.
            Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
            progressJobTask.Wait();

            // If job state is Error the event handling
            // method for job progress should log errors.  Here we check
            // for error state and exit if needed.
            if (job.State == JobState.Error)
            {
                throw new Exception("\nExiting method due to job error.");
            }

            return job.OutputMediaAssets[0];
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

## <a name="need-help"></a>Precisa de ajuda?

Você pode abrir um bilhete de apoio navegando para [novo pedido de apoio](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
