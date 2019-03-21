---
title: Codificação avançada com o Media Encoder Premium Workflow | Documentos da Microsoft
description: Aprenda a codificar com o Media Encoder Premium Workflow. Exemplos de código são escritos em C# e utilizar o SDK de Media Services para .NET.
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
ms.openlocfilehash: d20fc0cee6bce1389649e6287170b1963e799ccf
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58187732"
---
# <a name="advanced-encoding-with-media-encoder-premium-workflow"></a>Codificação avançada com o Media Encoder Premium Workflow
> [!NOTE]
> Processador de multimédia do Media Encoder Premium Workflow discutido neste artigo não está disponível na China.
>
>

Para perguntas de codificador premium, envie um e-mail mepd@microsoft.com.

## <a name="overview"></a>Descrição geral
Serviços de multimédia do Microsoft Azure está a introduzir a **Media Encoder Premium Workflow** processador de multimédia. Este avanço de ofertas de processador codificação capacidades dos fluxos de trabalho premium a pedido.

Os tópicos seguintes descrevem os detalhes relacionados à **Media Encoder Premium Workflow**:

* [Formatos suportados pelos Media Encoder Premium Workflow](media-services-premium-workflow-encoder-formats.md) – Discusses o arquivo de formatos e codecs suportados pelo **Media Encoder Premium Workflow**.
* [Descrição geral e comparação dos codificadores de multimédia do Azure de sob demanda](media-services-encode-asset.md) compara as capacidades de codificação do **Media Encoder Premium Workflow** e **Media Encoder Standard**.

Este artigo demonstra como codificar com **Media Encoder Premium Workflow** usando o .NET.

Codificação de tarefas para o **Media Encoder Premium Workflow** requerem um ficheiro de configuração separado, chamado de arquivo de fluxo de trabalho. Esses arquivos têm uma extensão de .workflow e são criados com o [Designer de fluxo de trabalho](media-services-workflow-designer.md) ferramenta.

Também pode obter a predefinição ficheiros de fluxo de trabalho [aqui](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows). A pasta também contém a descrição destes ficheiros.

Os ficheiros de fluxo de trabalho devem ser carregados para a sua conta de Media Services como um recurso, e este elemento deve ser passado para a tarefa de codificação.

## <a name="create-and-configure-a-visual-studio-project"></a>Criar e configurar um projeto de Visual Studio

Configure o seu ambiente de desenvolvimento e preencha o ficheiro app.config com informações da ligação, conforme descrito em [Media Services development with .NET](media-services-dotnet-how-to-use.md) (Desenvolvimento de Serviços de Multimédia com .NET). 

## <a name="encoding-example"></a>Exemplo de codificação

O exemplo seguinte demonstra como codificar com **Media Encoder Premium Workflow**.

Os seguintes passos são efetuados:

1. Criar um elemento e carregar um ficheiro de fluxo de trabalho.
2. Criar um elemento e carregar um ficheiro de suporte de dados de origem.
3. Obtenha o processador de multimédia "Media Encoder Premium Workflow".
4. Crie um trabalho e uma tarefa.

    Na maioria dos casos, a cadeia de configuração para a tarefa está vazia (como no exemplo a seguir). Existem alguns cenários avançados (que requerem que defina propriedades de tempo de execução dinamicamente) caso em que forneceria uma cadeia de caracteres XML para a tarefa de codificação. Exemplos de tais cenários são: criar uma sobreposição, paralelas ou sequenciais multimédia clips, limitado.
5. Adicione dois elementos de entrada para a tarefa.

   1. dia 1 – o elemento de fluxo de trabalho.
   2. 2ª – o elemento de vídeo.

      >[!NOTE]
      >O elemento de fluxo de trabalho tem de ser adicionado à tarefa antes do elemento de multimédia.
      A cadeia de configuração para esta tarefa deve estar vazia.
   
6. Submeta a tarefa de codificação.

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

Para perguntas de codificador premium, envie um e-mail mepd@microsoft.com.

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
