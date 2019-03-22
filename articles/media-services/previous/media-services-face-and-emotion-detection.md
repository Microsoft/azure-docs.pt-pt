---
title: Detetar rostos e emoções com análise de multimédia do Azure | Documentos da Microsoft
description: Este tópico explica como detetar rostos e emoções com análise de multimédia do Azure.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 5ca4692c-23f1-451d-9d82-cbc8bf0fd707
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/18/2019
ms.author: milanga;juliako;
ms.openlocfilehash: 46e60583da79006c133c8d9fac63e27f28bd699f
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58188191"
---
# <a name="detect-face-and-emotion-with-azure-media-analytics"></a>Detetar rostos e emoções com análise de multimédia do Azure
## <a name="overview"></a>Descrição geral
O **detetor de rostos de suporte de dados do Azure** processador de multimédia (MP) permite-lhe contar, controlar os movimentos e até mesmo avaliar a participação de público-alvo e reação por meio de expressões faciais. Este serviço contém dois recursos: 

* **Deteção de rostos**
  
    Deteção de rostos localiza e controla rostos humanos dentro de um vídeo. Várias faces podem ser detetadas e, em seguida, ser controladas quando eles passam, com os metadados de tempo e de localização devolvido num ficheiro JSON. Durante o controlo, ele tenta fornecer um ID de consistente para a mesma face enquanto a pessoa está se movendo em torno na tela, mesmo que eles são obstructed ou deixam resumidamente o quadro.
  
  > [!NOTE]
  > Este serviço não efetua o reconhecimento facial. Um indivíduo que deixa o quadro ou se torna obstructed para demasiado tempo terá um novo ID quando elas retornam.
  > 
  > 
* **Deteção de emoções**
  
    Deteção de emoções é um componente opcional do processador de suporte de dados de deteção de rostos que retorna análise em vários atributos emocional de rostos detetados, incluindo felicidade, tristeza, medo, raiva e muito mais. 

O **detetor de rostos de suporte de dados do Azure** MP está atualmente em pré-visualização.

Este artigo fornece detalhes sobre **detetor de rostos de suporte de dados do Azure** e mostra como utilizá-lo com o SDK de Media Services para .NET.

## <a name="face-detector-input-files"></a>Ficheiros de entrada detetor de rostos
Ficheiros de vídeo. Atualmente, são suportados os seguintes formatos: MP4, MOV e WMV.

## <a name="face-detector-output-files"></a>Ficheiros de saída detetor de rostos
A API de deteção e controlo de face fornece detecção de localização de rosto de alta precisão e controle que pode detetar até 64 rostos humanos num vídeo. Rostos frontal fornecem os melhores resultados, ao lado de rostos e pequeno rostos (menor ou igual a 24x24 pixels) podem não ser tão precisos.

Os rostos detetados e monitorizados são devolvidos com coordenadas (à esquerda, superior, largura e altura) que indica a localização de rostos na imagem em pixels, bem como um número de identificação de face indicando o rastreamento dessa pessoa. Números de ID do rosto são suscetíveis a repor em circunstâncias, o mostrador frontal caso de perda ou overlapped no frame, resultando em alguns indivíduos introdução atribuídos a vários IDs.

## <a id="output_elements"></a>Elementos do ficheiro de saída JSON

[!INCLUDE [media-services-analytics-output-json](../../../includes/media-services-analytics-output-json.md)]

Detetor de rostos utiliza técnicas de fragmentação (em que os metadados podem ser dividido em blocos baseados no tempo e pode transferir apenas o que precisa) e a segmentação de (em que os eventos são divididos no caso de se tornarem demasiado grandes). Alguns cálculos simples podem ajudá-lo a transformar os dados. Por exemplo, se um evento iniciou a 6300 (ticks), com uma escala temporal do 2997 (ticks/seg) e framerate de 29.97 (quadros/seg), em seguida:

* Início/Escala temporal = 2,1 segundos
* Segundos x Framerate = 63 quadros

## <a name="face-detection-input-and-output-example"></a>Exemplo de saída e entrada de deteção de rostos
### <a name="input-video"></a>Vídeo de entrada
[Vídeo de entrada](https://ampdemo.azureedge.net/azuremediaplayer.html?url=httpss%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

### <a name="task-configuration-preset"></a>Configuração da tarefa (predefinição)
Ao criar uma tarefa com **detetor de rostos de suporte de dados do Azure**, tem de especificar uma predefinição de configuração. A predefinição de configuração seguinte é apenas para a deteção de rostos.

```json
    {
      "version":"1.0",
      "options":{
          "TrackingMode": "Fast"
      }
    }
```

#### <a name="attribute-descriptions"></a>Descrições de atributo
| Nome de atributo | Descrição |
| --- | --- |
| Modo |Fast - rápida processamento velocidade, mas menos precisa (predefinição).|

### <a name="json-output"></a>Saída JSON
O exemplo a seguir da saída do JSON foi truncado.

```json
    {
    "version": 1,
    "timescale": 30000,
    "offset": 0,
    "framerate": 29.97,
    "width": 1280,
    "height": 720,
    "fragments": [
        {
        "start": 0,
        "duration": 60060
        },
        {
        "start": 60060,
        "duration": 60060,
        "interval": 1001,
        "events": [
            [
            {
                "id": 0,
                "x": 0.519531,
                "y": 0.180556,
                "width": 0.0867188,
                "height": 0.154167
            }
            ],
            [
            {
                "id": 0,
                "x": 0.517969,
                "y": 0.181944,
                "width": 0.0867188,
                "height": 0.154167
            }
            ],
            [
            {
                "id": 0,
                "x": 0.517187,
                "y": 0.183333,
                "width": 0.0851562,
                "height": 0.151389
            }
            ],
```


## <a name="emotion-detection-input-and-output-example"></a>Deteção de emoções de entrada e saída de exemplo
### <a name="input-video"></a>Vídeo de entrada
[Vídeo de entrada](https://ampdemo.azureedge.net/azuremediaplayer.html?url=httpss%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

### <a name="task-configuration-preset"></a>Configuração da tarefa (predefinição)
Ao criar uma tarefa com **detetor de rostos de suporte de dados do Azure**, tem de especificar uma predefinição de configuração. Especifica a predefinição de configuração seguinte para criar o JSON com base na deteção de emoções.

```json
    {
      "version": "1.0",
      "options": {
        "aggregateEmotionWindowMs": "987",
        "mode": "aggregateEmotion",
        "aggregateEmotionIntervalMs": "342"
      }
    }
```


#### <a name="attribute-descriptions"></a>Descrições de atributo
| Nome de atributo | Descrição |
| --- | --- |
| Modo |Rostos: Apenas a deteção.<br/>PerFaceEmotion: Devolva emoções de forma independente para cada deteção de rostos.<br/>AggregateEmotion: Devolve valores de média de emoções para todos os rostos no quadro. |
| AggregateEmotionWindowMs |Utilize se AggregateEmotion modo selecionado. Especifica o período de vídeo utilizado para produzir cada resultado agregado, em milissegundos. |
| AggregateEmotionIntervalMs |Utilize se AggregateEmotion modo selecionado. Especifica com que freqüência para produzir resultados de agregação. |

#### <a name="aggregate-defaults"></a>Predefinições de agregação
Abaixo, são recomendadas valores para as definições de janela e o intervalo de agregação. AggregateEmotionWindowMs deve ter mais de AggregateEmotionIntervalMs.

|| Predefinições (s) | Max(s) | Min (s) |
|--- | --- | --- | --- |
| AggregateEmotionWindowMs |0,5 |2 |0.25|
| AggregateEmotionIntervalMs |0,5 |1 |0.25|

### <a name="json-output"></a>Saída JSON
JSON de saída para emoções agregada (truncada):

```json
    {
     "version": 1,
     "timescale": 30000,
     "offset": 0,
     "framerate": 29.97,
     "width": 1280,
     "height": 720,
     "fragments": [
       {
         "start": 0,
         "duration": 60060,
         "interval": 15015,
         "events": [
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ]
         ]
       },
       {
         "start": 60060,
         "duration": 60060,
         "interval": 15015,
         "events": [
           [
             {
               "windowFaceDistribution": {
                 "neutral": 1,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0.688541,
                 "happiness": 0.0586323,
                 "surprise": 0.227184,
                 "sadness": 0.00945675,
                 "anger": 0.00592107,
                 "disgust": 0.00154993,
                 "fear": 0.00450447,
                 "contempt": 0.0042109
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 1,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
```

## <a name="limitations"></a>Limitações
* Os formatos de vídeo de entrada suportados incluem MP4, MOV e WMV.
* O intervalo de tamanho de face detetável é 24x24 para 2048 x 2048 pixels. Os rostos fora deste intervalo não serão detetados.
* Para cada vídeo, o número máximo de faces devolvido é 64.
* Alguns rostos poderão não ser detetados devido a desafios técnicos; Por exemplo, muito grande de face de ângulos (head-representam) e oclusão grandes. Rostos frontal e quase frontal tem os melhores resultados.

## <a name="net-sample-code"></a>Código de exemplo do .NET

O seguinte programa mostra como:

1. Criar um elemento e carregue um ficheiro de multimédia para o elemento.
2. Crie uma tarefa com uma tarefa de deteção de rostos com base num arquivo de configuração que contém a seguinte configuração predefinida de json: 

    ```json
            {
                "version": "1.0"
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

namespace FaceDetection
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

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            // Run the FaceDetection job.
            var asset = RunFaceDetectionJob(@"C:\supportFiles\FaceDetection\BigBuckBunny.mp4",
                                        @"C:\supportFiles\FaceDetection\config.json");

            // Download the job output asset.
            DownloadAsset(asset, @"C:\supportFiles\FaceDetection\Output");
        }

        static IAsset RunFaceDetectionJob(string inputMediaFilePath, string configurationFile)
        {
            // Create an asset and upload the input media file to storage.
            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                "My Face Detection Input Asset",
                AssetCreationOptions.None);

            // Declare a new job.
            IJob job = _context.Jobs.Create("My Face Detection Job");

            // Get a reference to Azure Media Face Detector.
            string MediaProcessorName = "Azure Media Face Detector";

            var processor = GetLatestMediaProcessorByName(MediaProcessorName);

            // Read configuration from the specified file.
            string configuration = File.ReadAllText(configurationFile);

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My Face Detection Task",
                processor,
                configuration,
                TaskOptions.None);

            // Specify the input asset.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("My Face Detection Output Asset", AssetCreationOptions.None);

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

[Demonstrações de análise de multimédia do Azure](https://amslabs.azurewebsites.net/demos/Analytics.html)

