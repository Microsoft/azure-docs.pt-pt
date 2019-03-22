---
title: Edite rostos com análise de multimédia do Azure | Documentos da Microsoft
description: Este tópico demonstra como edite rostos com análise de multimédia do Azure.
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
ms.author: juliako;
ms.openlocfilehash: 1fe003ae13bc5f195932f4f140e17c4dc2791959
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58188259"
---
# <a name="redact-faces-with-azure-media-analytics"></a>Edite rostos com análise de multimédia do Azure 
## <a name="overview"></a>Descrição geral
**O Azure Media Redactor** é um [análise de multimédia do Azure](media-services-analytics-overview.md) processador de multimédia (MP) que oferece a redação de rostos escalável na cloud. A redação de rostos permite-lhe modificar o vídeo para Desfoca rostos de indivíduos selecionados. Pode querer utilizar o serviço de redação de rostos em cenários de segurança e multimédia para notícias pública. Alguns minutos de imagens que contém vários rostos de podem demorar horas a edite manualmente, mas com este serviço, o processo de redação de rostos exigirá apenas alguns passos simples. Para obter mais informações, consulte [isso](https://azure.microsoft.com/blog/azure-media-redactor/) blog.

Este artigo fornece detalhes sobre **do Azure Media Redactor** e mostra como utilizá-lo com o SDK de Media Services para .NET.

## <a name="face-redaction-modes"></a>Modos de redação de rostos
Redação de facial funciona por detetar rostos em cada quadro de vídeo e rastrear o objeto de face, de ambos os para a frente e para trás no tempo, para que a mesma pessoa pode desfocada de outros ângulos também. O processo automatizado de redação é complexo e faz não sempre produzir 100% de saída desejado, por esse motivo, a análise de multimédia fornecem-lhe duas formas para modificar o resultado final.

Além de um modo totalmente automático, há um fluxo de trabalho de dois, que permite a seleção/desaprovisionamento-selection de faces encontradas por meio de uma lista de IDs. Além disso tornar arbitrário por ajustes de quadro o pacote de gestão utiliza um ficheiro de metadados no formato JSON. Este fluxo de trabalho é dividido em **Analyze** e **Redact** modos. Pode combinar os dois modos numa única passagem, que executa ambas as tarefas numa tarefa; Este modo é chamado **combinado**.

### <a name="combined-mode"></a>Modo combinado
Isso produz um mp4 eliminadas automaticamente sem qualquer manual de entrada.

| Fase | Nome do Ficheiro | Notas |
| --- | --- | --- |
| Elemento de entrada |foo.bar |Vídeo em formato WMV, MOV ou MP4 |
| Entrada de configuração |Configuração predefinida de configuração da tarefa |{'version':'1.0', 'options': {'mode':'combined'}} |
| Elemento de saída |foo_redacted.mp4 |Vídeo com desfocar aplicada |

#### <a name="input-example"></a>Exemplo de entrada:
[Veja este vídeo](https://ampdemo.azureedge.net/?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fed99001d-72ee-4f91-9fc0-cd530d0adbbc%2FDancing.mp4)

#### <a name="output-example"></a>Exemplo de saída:
[Veja este vídeo](https://ampdemo.azureedge.net/?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc6608001-e5da-429b-9ec8-d69d8f3bfc79%2Fdance_redacted.mp4)

### <a name="analyze-mode"></a>Analisar o modo
O **analisar** pass do fluxo de trabalho dois precisar de uma entrada de vídeo e produz um ficheiro JSON de localizações de rostos e jpg imagens de cada detetado face.

| Fase | Nome do Ficheiro | Notas |
| --- | --- | --- |
| Elemento de entrada |foo.bar |Vídeo em formato WMV, MPV ou MP4 |
| Entrada de configuração |Configuração predefinida de configuração da tarefa |{'version':'1.0', 'options': {'mode':'analyze'}} |
| Elemento de saída |foo_annotations.json |Dados de anotação de localizações de face no formato JSON. Isso pode ser editado pelo utilizador para modificar o desfocar delimitação de caixas. Veja o exemplo abaixo. |
| Elemento de saída |foo_thumb%06d.jpg [foo_thumb000001.jpg, foo_thumb000002.jpg] |Um jpg cropped de cada detetado face, em que o número indica o labelId do mostrador da |

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

### <a name="redact-mode"></a>Edite o modo
A segunda passagem do fluxo de trabalho demora um grande número de entradas que devem ser combinados num único recurso.

Isto inclui uma lista de IDs de Desfoca, o vídeo original e as anotações JSON. Esse modo usa as anotações para aplicar desfocar no vídeo de entrada.

O resultado da passagem de análise não inclui o vídeo original. O vídeo tem de ser carregados para o elemento de entrada para a tarefa de modo Redact e selecionada como ficheiro principal.

| Fase | Nome do Ficheiro | Notas |
| --- | --- | --- |
| Elemento de entrada |foo.bar |Vídeo em formato MP4, MPV ou WMV. Mesmo vídeo como no passo 1. |
| Elemento de entrada |foo_annotations.json |ficheiro de metadados de anotações da primeira fase, com modificações opcionais. |
| Elemento de entrada |foo_IDList.txt (opcional) |Lista de rosto IDs para edite separados por opcional nova linha. Se deixado em branco, isso torna obscura todos os rostos. |
| Entrada de configuração |Configuração predefinida de configuração da tarefa |{'version':'1.0', 'options': {'mode':'redact'}} |
| Elemento de saída |foo_redacted.mp4 |Vídeo com desfocar aplicadas com base em anotações |

#### <a name="example-output"></a>Exemplo de saída
Esta é a saída de um IDList com um ID selecionado.

[Veja este vídeo](https://ampdemo.azureedge.net/?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fad6e24a2-4f9c-46ee-9fa7-bf05e20d19ac%2Fdance_redacted1.mp4)

Foo_IDList.txt de exemplo
 
     1
     2
     3

## <a name="blur-types"></a>Desfoca tipos

Na **combinado** ou **Redact** modo, existem 5 obscurecimento diferentes modos, pode escolher de entre através da configuração de entrada de JSON: **Baixa**, **Med**, **elevada**, **caixa**, e **preto**. Por predefinição **Med** é utilizado.

Pode encontrar exemplos dos tipos de obscurecimento abaixo.

### <a name="example-json"></a>JSON de exemplo:

```json
    {'version':'1.0', 'options': {'Mode': 'Combined', 'BlurType': 'High'}}
```

#### <a name="low"></a>Baixa

![Baixa](./media/media-services-face-redaction/blur1.png)
 
#### <a name="med"></a>Med

![Med](./media/media-services-face-redaction/blur2.png)

#### <a name="high"></a>Elevado

![Elevado](./media/media-services-face-redaction/blur3.png)

#### <a name="box"></a>Box

![Box](./media/media-services-face-redaction/blur4.png)

#### <a name="black"></a>Preto

![Preto](./media/media-services-face-redaction/blur5.png)

## <a name="elements-of-the-output-json-file"></a>Elementos do ficheiro de saída JSON

O pacote de gestão de redação de fornece detecção de localização de rosto de alta precisão e de controlo que pode detetar até 64 rostos humanos num quadro de vídeo. Rostos frontal fornecem os melhores resultados, ao lado de rostos e pequeno rostos (menor ou igual a 24x24 pixels) são um desafio.

[!INCLUDE [media-services-analytics-output-json](../../../includes/media-services-analytics-output-json.md)]

## <a name="net-sample-code"></a>Código de exemplo do .NET

O seguinte programa mostra como:

1. Criar um elemento e carregue um ficheiro de multimédia para o elemento.
2. Crie uma tarefa com uma tarefa de redação de rostos com base num arquivo de configuração que contém a seguinte configuração predefinida de json: 

    ```json
            {
                'version':'1.0',
                'options': {
                    'mode':'combined'
                }
            }
    ```

3. Transferir os ficheiros JSON de saída. 

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

## <a name="next-steps"></a>Passos Seguintes

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Ligações relacionadas
[Descrição geral da análise dos serviços de multimédia do Azure](media-services-analytics-overview.md)

[Demonstrações de análise de multimédia do Azure](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

