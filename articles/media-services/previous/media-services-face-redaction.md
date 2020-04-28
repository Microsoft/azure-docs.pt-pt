---
title: Redact enfrenta com Azure Media Analytics Microsoft Docs
description: Azure Media Redator é um processador de mídia Azure Media Analytics que oferece redação facial escalável na nuvem. Este artigo demonstra como redigir rostos com a análise dos meios de comunicação do Azure.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 6a1b7a76ef1efda51f09ac733b3d434235ff40ef
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "74900303"
---
# <a name="redact-faces-with-azure-media-analytics"></a>Redact enfrenta azure media analytics 
## <a name="overview"></a>Descrição geral
**Azure Media Redator** é um processador de mídia [Azure Media Analytics](media-services-analytics-overview.md) (MP) que oferece redação facial escalável na nuvem. A redação facial permite modificar o seu vídeo de forma a desfocar rostos de indivíduos selecionados. Você pode querer usar o serviço de redação facial em cenários de segurança pública e meios de comunicação. Alguns minutos de filmagens que contêm várias faces podem levar horas a redigir manualmente, mas com este serviço o processo de redação facial exigirá apenas alguns passos simples. Para mais informações, consulte [este](https://azure.microsoft.com/blog/azure-media-redactor/) blog.

Este artigo dá detalhes sobre **o Redator Azure Media** e mostra como usá-lo com a Media Services SDK para .NET.

## <a name="face-redaction-modes"></a>Modos de redação facial
A redação facial funciona detetando rostos em cada quadro de vídeo e rastreando o objeto facial tanto para a frente como para trás no tempo, para que o mesmo indivíduo possa ser borrado de outros ângulos também. O processo automatizado de redação é complexo e nem sempre produz 100% da produção desejada, por isso a Media Analytics fornece-lhe algumas formas de modificar a saída final.

Além de um modo totalmente automático, existe um fluxo de trabalho de dois passes, que permite a seleção/desselecção de rostos encontrados através de uma lista de IDs. Além disso, para efazer ajustes arbitrários por quadro, o MP utiliza um ficheiro de metadados no formato JSON. Este fluxo de trabalho é dividido em modos **Analyze** e **Redact.** Pode combinar os dois modos num único passe que executa ambas as tarefas num só trabalho; este modo chama-se **Combinado**.

### <a name="combined-mode"></a>Modo combinado
Isto produz automaticamente um mp4 redigido sem qualquer entrada manual.

| Fase | Nome de Ficheiro | Notas |
| --- | --- | --- |
| Ativo de entrada |foo.bar |Vídeo em formato WMV, MOV ou MP4 |
| Input config |Predefinição de configuração de trabalho |{'version':'1.0', 'options': {'mode':'combined'}} |
| Ativo de saída |foo_redacted.mp4 |Vídeo com desfocagem aplicado |

#### <a name="input-example"></a>Exemplo de entrada:
[ver este vídeo](https://ampdemo.azureedge.net/?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fed99001d-72ee-4f91-9fc0-cd530d0adbbc%2FDancing.mp4)

#### <a name="output-example"></a>Exemplo de saída:
[ver este vídeo](https://ampdemo.azureedge.net/?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc6608001-e5da-429b-9ec8-d69d8f3bfc79%2Fdance_redacted.mp4)

### <a name="analyze-mode"></a>Analisar o modo
O passe **de análise** do fluxo de trabalho de dois passes requer uma entrada de vídeo e produz um ficheiro JSON de localizações faciais, e imagens jpg de cada rosto detetado.

| Fase | Nome de Ficheiro | Notas |
| --- | --- | --- |
| Ativo de entrada |foo.bar |Vídeo em formato WMV, MPV ou MP4 |
| Input config |Predefinição de configuração de trabalho |{'version':'1.0', 'options': {'mode':'analyze'}} |
| Ativo de saída |foo_annotations.json |Dados de anotação das localizações faciais no formato JSON. Isto pode ser editado pelo utilizador para modificar as caixas de delimitação desfocadas. Veja a amostra abaixo. |
| Ativo de saída |foo_thumb%06d.jpg [foo_thumb000001.jpg, foo_thumb000002.jpg] |Um jpg cortado de cada rosto detetado, onde o número indica o rótuloId do rosto |

#### <a name="output-example"></a>Exemplo de saída:

```json
    {
      "version": 1,
      "timescale": 24000,
      "offset": 0,
      "framerate": 23.976,
      "width": 1280,
      "height": 720,
      "fragments": [
        {
          "start": 0,
          "duration": 48048,
          "interval": 1001,
          "events": [
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [
              {
                "index": 13,
                "id": 1138,
                "x": 0.29537,
                "y": -0.18987,
                "width": 0.36239,
                "height": 0.80335
              },
              {
                "index": 13,
                "id": 2028,
                "x": 0.60427,
                "y": 0.16098,
                "width": 0.26958,
                "height": 0.57943
              }
            ],

    … truncated
```

### <a name="redact-mode"></a>Modo Redact
A segunda passagem do fluxo de trabalho requer um maior número de inputs que devem ser combinados num único ativo.

Isto inclui uma lista de IDs para borrar, o vídeo original, e as anotações JSON. Este modo utiliza as anotações para aplicar borrão no vídeo de entrada.

A saída do passe Analyze não inclui o vídeo original. O vídeo precisa de ser enviado para o ativo de entrada para a tarefa do modo Redact e selecionado como ficheiro principal.

| Fase | Nome de Ficheiro | Notas |
| --- | --- | --- |
| Ativo de entrada |foo.bar |Vídeo em formato WMV, MPV ou MP4. O mesmo vídeo do passo 1. |
| Ativo de entrada |foo_annotations.json |anotações ficheiro de metadados da primeira fase, com modificações opcionais. |
| Ativo de entrada |foo_IDList.txt (Opcional) |Nova linha opcional lista separada de iDs faciais para redigir. Se ficar em branco, isto borra todos os rostos. |
| Input config |Predefinição de configuração de trabalho |{'version':'1.0', 'options': {'mode':'redact'}} |
| Ativo de saída |foo_redacted.mp4 |Vídeo com desfocagem aplicado com base em anotações |

#### <a name="example-output"></a>Saída de exemplo
Esta é a saída de uma IDList com um ID selecionado.

[ver este vídeo](https://ampdemo.azureedge.net/?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fad6e24a2-4f9c-46ee-9fa7-bf05e20d19ac%2Fdance_redacted1.mp4)

Exemplo foo_IDList.txt
 
     1
     2
     3

## <a name="blur-types"></a>Tipos de borrão

No modo **Combinado** ou **Redact,** existem 5 modos de desfocagem diferentes que pode escolher através da configuração de entrada JSON: **Low**, **Med,** **High,** **Box**e **Black**. Por padrão, **o Med** é utilizado.

Pode encontrar amostras dos tipos de borrão abaixo.

### <a name="example-json"></a>Exemplo JSON:

```json
    {'version':'1.0', 'options': {'Mode': 'Combined', 'BlurType': 'High'}}
```

#### <a name="low"></a>Baixa

![Baixa](./media/media-services-face-redaction/blur1.png)
 
#### <a name="med"></a>Med

![Med](./media/media-services-face-redaction/blur2.png)

#### <a name="high"></a>Alta

![Alta](./media/media-services-face-redaction/blur3.png)

#### <a name="box"></a>Box

![Box](./media/media-services-face-redaction/blur4.png)

#### <a name="black"></a>Preto

![Preto](./media/media-services-face-redaction/blur5.png)

## <a name="elements-of-the-output-json-file"></a>Elementos do ficheiro JSON de saída

O MP de Redaction fornece deteção e rastreio de localização facial de alta precisão que podem detetar até 64 rostos humanos numa moldura de vídeo. Os rostos frontais fornecem os melhores resultados, enquanto rostos laterais e rostos pequenos (menos ou iguais a 24x24 pixels) são desafiantes.

[!INCLUDE [media-services-analytics-output-json](../../../includes/media-services-analytics-output-json.md)]

## <a name="net-sample-code"></a>Código de amostra .NET

O seguinte programa mostra como:

1. Crie um ativo e faça upload de um ficheiro de mídia para o ativo.
2. Crie um trabalho com uma tarefa de redação facial com base num ficheiro de configuração que contenha o seguinte preset json: 

    ```json
            {
                'version':'1.0',
                'options': {
                    'mode':'combined'
                }
            }
    ```

3. Descarregue os ficheiros JSON de saída. 

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

namespace FaceRedaction
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

            // Run the FaceRedaction job.
            var asset = RunFaceRedactionJob(@"C:\supportFiles\FaceRedaction\SomeFootage.mp4",
                        @"C:\supportFiles\FaceRedaction\config.json");

            // Download the job output asset.
            DownloadAsset(asset, @"C:\supportFiles\FaceRedaction\Output");
        }

        static IAsset RunFaceRedactionJob(string inputMediaFilePath, string configurationFile)
        {
            // Create an asset and upload the input media file to storage.
            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
            "My Face Redaction Input Asset",
            AssetCreationOptions.None);

            // Declare a new job.
            IJob job = _context.Jobs.Create("My Face Redaction Job");

            // Get a reference to Azure Media Redactor.
            string MediaProcessorName = "Azure Media Redactor";

            var processor = GetLatestMediaProcessorByName(MediaProcessorName);

            // Read configuration from the specified file.
            string configuration = File.ReadAllText(configurationFile);

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My Face Redaction Task",
            processor,
            configuration,
            TaskOptions.None);

            // Specify the input asset.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("My Face Redaction Output Asset", AssetCreationOptions.None);

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

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Ligações relacionadas
[Visão geral da Análise de Serviços de Mídia Azure](media-services-analytics-overview.md)

[Demonstrações azure Media Analytics](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

