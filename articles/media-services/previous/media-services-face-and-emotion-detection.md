---
title: Detectar rosto e emoções com Análise de Mídia do Azure | Microsoft Docs
description: Este tópico demonstra como detectar rostos e emoções com Análise de Mídia do Azure.
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
ms.author: juliako
ms.reviewer: milanga
ms.openlocfilehash: 3ae2e49b812e7a9515cef81b328ceb87e1a7f017
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/17/2019
ms.locfileid: "69015467"
---
# <a name="detect-face-and-emotion-with-azure-media-analytics"></a>Detectar rosto e emoções com Análise de Mídia do Azure
## <a name="overview"></a>Descrição geral
O MP (processador de mídia **Azure Media face detector** ) permite que você conte, acompanhe movimentos e até mesmo avalie a participação e a reação do público por meio de expressões faciais. Esse serviço contém dois recursos: 

* **Detecção facial**
  
    A detecção facial localiza e rastreia rostos humanas em um vídeo. Várias faces podem ser detectadas e, posteriormente, acompanhadas à medida que elas se movimentam, com os metadados de hora e local retornados em um arquivo JSON. Durante o acompanhamento, ele tenta dar uma ID consistente à mesma face, enquanto a pessoa está se movendo pela tela, mesmo que esteja obstruída ou saia do quadro rapidamente.
  
  > [!NOTE]
  > Esse serviço não executa o reconhecimento facial. Uma pessoa que deixa o quadro ou ficará obstruída por muito tempo receberá uma nova ID quando retornar.
  > 
  > 
* **Detecção de emoções**
  
    A detecção de emoções é um componente opcional do processador de mídia Detecção Facial que retorna a análise em vários atributos emocional dos rostos detectados, incluindo felicidade, tristeza, medo, raiva e muito mais. 

O **Azure Media face detector** MP está atualmente em visualização.

Este artigo fornece detalhes sobre **Azure Media face detector** e mostra como usá-lo com o SDK dos serviços de mídia para .net.

## <a name="face-detector-input-files"></a>Arquivos de entrada do detector de face
Arquivos de vídeo. Atualmente, há suporte para os seguintes formatos: MP4, MOV e WMV.

## <a name="face-detector-output-files"></a>Arquivos de saída de detector de face
A API de detecção e acompanhamento facial fornece detecção e acompanhamento de local de face de alta precisão que podem detectar até 64 rostos humanas em um vídeo. Os frontais faces fornecem os melhores resultados, enquanto as faces laterais e pequenas faces (menores ou iguais a 24x24 pixels) podem não ser tão precisas.

As faces detectadas e controladas são retornadas com coordenadas (esquerda, superior, largura e altura) que indicam o local das faces na imagem em pixels, bem como um número de identificação de face indicando o acompanhamento dessa pessoa. Os números de ID de face são propensos a redefinição em circunstâncias em que a face frontais é perdida ou sobreposta no quadro, resultando em alguns indivíduos que recebem várias IDs.

## <a id="output_elements"></a>Elementos do arquivo JSON de saída

[!INCLUDE [media-services-analytics-output-json](../../../includes/media-services-analytics-output-json.md)]

O tipo de detecção facial usa técnicas de fragmentação (onde os metadados podem ser divididos em partes baseadas em tempo e você pode baixar apenas o que precisa) e segmentação (onde os eventos são divididos caso eles fiquem muito grandes). Alguns cálculos simples podem ajudá-lo a transformar os dados. Por exemplo, se um evento for iniciado em 6300 (tiques), com uma escala de tempo de 2997 (tiques/s) e taxa de quadros de 29,97 (quadros/s), então:

* Início/Escala temporal = 2,1 segundos
* Segundos x taxa de quadros = 63 frames

## <a name="face-detection-input-and-output-example"></a>Exemplo de entrada e saída de detecção facial
### <a name="input-video"></a>Vídeo de entrada
[Vídeo de entrada](https://ampdemo.azureedge.net/azuremediaplayer.html?url=httpss%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

### <a name="task-configuration-preset"></a>Configuração de tarefa (predefinição)
Ao criar uma tarefa com **Azure Media face detector**, você deve especificar uma predefinição de configuração. A predefinição de configuração a seguir é apenas para detecção facial.

```json
    {
      "version":"1.0",
      "options":{
          "TrackingMode": "Fast"
      }
    }
```

#### <a name="attribute-descriptions"></a>Descrições de atributo
| Nome do atributo | Descrição |
| --- | --- |
| Modo |Velocidade de processamento rápida-rápida, mas menos precisa (padrão).|

### <a name="json-output"></a>Saída JSON
O exemplo de saída JSON a seguir foi truncado.

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


## <a name="emotion-detection-input-and-output-example"></a>Exemplo de entrada e saída de detecção de emoções
### <a name="input-video"></a>Vídeo de entrada
[Vídeo de entrada](https://ampdemo.azureedge.net/azuremediaplayer.html?url=httpss%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

### <a name="task-configuration-preset"></a>Configuração de tarefa (predefinição)
Ao criar uma tarefa com **Azure Media face detector**, você deve especificar uma predefinição de configuração. A predefinição de configuração a seguir especifica a criação de JSON com base na detecção de emoção.

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
| Nome do atributo | Descrição |
| --- | --- |
| Modo |Frente Somente detecção facial.<br/>PerFaceEmotion: Retorne a emoções de forma independente para cada detecção de face.<br/>AggregateEmotion: Retorna valores de emoções médias para todas as faces no quadro. |
| AggregateEmotionWindowMs |Use se o modo AggregateEmotion estiver selecionado. Especifica o comprimento do vídeo usado para produzir cada resultado agregado, em milissegundos. |
| AggregateEmotionIntervalMs |Use se o modo AggregateEmotion estiver selecionado. Especifica com que frequência produzir resultados de agregação. |

#### <a name="aggregate-defaults"></a>Agregar padrões
Abaixo estão os valores recomendados para a janela agregada e as configurações de intervalo. AggregateEmotionWindowMs deve ser maior que AggregateEmotionIntervalMs.

|| Padrões (es) | Máx. (s) | Min (s) |
|--- | --- | --- | --- |
| AggregateEmotionWindowMs |0,5 |2 |0.25|
| AggregateEmotionIntervalMs |0,5 |1 |0.25|

### <a name="json-output"></a>Saída JSON
Saída JSON para a emoção da agregação (truncada):

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
* Os formatos de vídeo de entrada com suporte incluem MP4, MOV e WMV.
* O intervalo de tamanho de face detectável é de 24x24 a 2048x2048 pixels. As faces fora desse intervalo não serão detectadas.
* Para cada vídeo, o número máximo de faces retornado é 64.
* Algumas faces podem não ser detectadas devido a desafios técnicos; por exemplo, ângulos de face muito grandes (pose de cabeça) e grandes oclusão. Os rostos frontais e Near-frontais têm os melhores resultados.

## <a name="net-sample-code"></a>Código de exemplo do .NET

O programa a seguir mostra como:

1. Crie um ativo e carregue um arquivo de mídia no ativo.
2. Crie um trabalho com uma tarefa de detecção facial baseada em um arquivo de configuração que contenha a predefinição JSON a seguir: 

    ```json
            {
                "version": "1.0"
            }
    ```
3. Baixe os arquivos JSON de saída. 

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
[Visão geral da análise dos serviços de mídia do Azure](media-services-analytics-overview.md)

[Demonstrações de Análise de Mídia do Azure](https://amslabs.azurewebsites.net/demos/Analytics.html)

