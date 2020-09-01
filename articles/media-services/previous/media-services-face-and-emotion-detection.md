---
title: Detetar Rosto e Emoção com Azure Media Analytics ! Microsoft Docs
description: Este tópico demonstra como detetar rostos e emoções com a Azure Media Analytics.
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
ms.custom: devx-track-csharp
ms.openlocfilehash: ac27fa5773e266d546b6511a24c6a5e14c5fda7f
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89257845"
---
# <a name="detect-face-and-emotion-with-azure-media-analytics"></a>Detetar rosto e emoção com Azure Media Analytics

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> O processador **de mídia Azure Media Face Detetor** será retirado. Para a data da aposentadoria, consulte o tema dos [componentes do legado.](legacy-components.md)

## <a name="overview"></a>Descrição geral

O processador **de mídia Azure Media Face Detetor** (MP) permite-lhe contar, rastrear movimentos e até avaliar a participação e a reação do público através de expressões faciais. Este serviço contém duas características: 

* **Deteção de rostos**
  
    A deteção facial encontra e rastreia rostos humanos dentro de um vídeo. Várias faces podem ser detetadas e posteriormente rastreadas à medida que se deslocam, com os metadados de hora e localização devolvidos num ficheiro JSON. Durante o rastreio, tenta dar uma identificação consistente à mesma face enquanto a pessoa se desloca no ecrã, mesmo que esteja obstruída ou deixe brevemente a moldura.
  
  > [!NOTE]
  > Este serviço não realiza reconhecimento facial. Um indivíduo que sai da moldura ou fica obstruído por muito tempo receberá uma nova identificação quando regressar.
  > 
  > 
* **Deteção de emoções**
  
    A Deteção de Emoção é um componente opcional do Processador de Meios de Deteção facial que devolve a análise de múltiplos atributos emocionais dos rostos detetados, incluindo felicidade, tristeza, medo, raiva e muito mais. 

O **Azure Media Face Detetor** MP encontra-se atualmente em Pré-visualização.

Este artigo dá detalhes sobre  **o Azure Media Face Detetor** e mostra como usá-lo com a Media Services SDK para .NET.

## <a name="face-detector-input-files"></a>Ficheiros de entrada do Detetor de Rosto
Ficheiros de vídeo. Atualmente, os seguintes formatos são suportados: MP4, MOV e WMV.

## <a name="face-detector-output-files"></a>Ficheiros de saída do Detetor de Rosto
A API de deteção e rastreio do rosto fornece deteção e rastreamento de alta precisão que pode detetar até 64 rostos humanos num vídeo. As faces frontais fornecem os melhores resultados, enquanto as faces laterais e as faces pequenas (menos ou igual a 24x24 pixels) podem não ser tão precisas.

Os rostos detetados e rastreados são devolvidos com coordenadas (esquerda, superior, largura e altura) indicando a localização das faces na imagem em pixels, bem como um número de identificação facial indicando o rastreio desse indivíduo. Os números de identificação facial são propensos a ser reiniciados em circunstâncias em que a face frontal é perdida ou sobreposta no quadro, resultando em alguns indivíduos que recebem vários IDs atribuídos.

## <a name="elements-of-the-output-json-file"></a><a id="output_elements"></a>Elementos do ficheiro JSON de saída

[!INCLUDE [media-services-analytics-output-json](../../../includes/media-services-analytics-output-json.md)]

O Detetor facial utiliza técnicas de fragmentação (onde os metadados podem ser divididos em pedaços baseados no tempo e só pode descarregar o que precisa) e segmentação (onde os eventos são interrompidos no caso de ficarem demasiado grandes). Alguns cálculos simples podem ajudá-lo a transformar os dados. Por exemplo, se um evento começou em 6300 (tiques), com um prazo de 2997 (carraças/seg) e framerate de 29,97 (quadros/seg), então:

* Início/Escala temporal = 2,1 segundos
* Segundos x Framerate = 63 quadros

## <a name="face-detection-input-and-output-example"></a>Entrada de deteção facial e exemplo de saída
### <a name="input-video"></a>Vídeo de entrada
[Vídeo de entrada](https://ampdemo.azureedge.net/azuremediaplayer.html?url=httpss%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

### <a name="task-configuration-preset"></a>Configuração de tarefa (predefinição)
Ao criar uma tarefa com **o Azure Media Face Detetor,** tem de especificar uma pré-sintonia de configuração. A pré-sintonia seguinte é apenas para deteção facial.

```json
    {
      "version":"1.0",
      "options":{
          "TrackingMode": "Fast"
      }
    }
```

#### <a name="attribute-descriptions"></a>Atribuir descrições
| Nome do atributo | Descrição |
| --- | --- |
| Modo |Velocidade de processamento rápida, mas menos precisa (predefinição).|

### <a name="json-output"></a>Saída JSON
O exemplo seguinte da produção de JSON foi truncado.

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


## <a name="emotion-detection-input-and-output-example"></a>Entrada de deteção de emoção e exemplo de saída
### <a name="input-video"></a>Vídeo de entrada
[Vídeo de entrada](https://ampdemo.azureedge.net/azuremediaplayer.html?url=httpss%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

### <a name="task-configuration-preset"></a>Configuração de tarefa (predefinição)
Ao criar uma tarefa com **o Azure Media Face Detetor,** tem de especificar uma pré-sintonia de configuração. A pré-configuração a seguir especifica para criar JSON com base na deteção de emoção.

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


#### <a name="attribute-descriptions"></a>Atribuir descrições
| Nome do atributo | Descrição |
| --- | --- |
| Modo |Faces: Apenas deteção facial.<br/>PerFaceEmotion: Devolva a emoção de forma independente para cada deteção facial.<br/>AgregaçãoEmoção: Devolva valores médios de emoção para todos os rostos no quadro. |
| AgregaçãoEmotionWindowMs |Utilize se o modo Agregação Emotion for selecionado. Especifica o comprimento do vídeo utilizado para produzir cada resultado agregado, em milissegundos. |
| AgregadoEmotionIntervalMs |Utilize se o modo Agregação Emotion for selecionado. Especifica com que frequência produzir resultados agregados. |

#### <a name="aggregate-defaults"></a>Incumprimentos agregados
Abaixo estão os valores recomendados para as definições de janela e intervalo agregados. AgregadoEmotionWindowMs deve ser mais longo do que aggregateEmotionIntervalMs.

|| Predefinições(s) | Max(s) | Min(s) |
|--- | --- | --- | --- |
| **AgregaçãoEmotionWindowMs** |0,5 |2 |0,25|
| **AgregadoEmotionIntervalMs** |0,5 |1 |0,25|

### <a name="json-output"></a>Saída JSON
Saída JSON para emoção agregada (truncada):

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
* A gama de tamanhos faciais detetáveis é de 24x24 a 2048x2048 pixels. Os rostos fora deste alcance não serão detetados.
* Para cada vídeo, o número máximo de rostos devolvidos é de 64.
* Algumas faces podem não ser detetadas devido a desafios técnicos; por exemplo, ângulos faciais muito grandes (pose de cabeça) e grande oclusão. Rostos frontais e quase frontais têm os melhores resultados.

## <a name="net-sample-code"></a>.NET código de amostra

O seguinte programa mostra como:

1. Crie um ativo e carre faça o upload de um ficheiro de media para o ativo.
2. Criar um trabalho com uma tarefa de deteção facial baseada num ficheiro de configuração que contenha a seguinte predefinição json: 

    ```json
            {
                "version": "1.0"
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
[Visão geral da Azure Media Services Analytics](media-services-analytics-overview.md)

[Azure Media Analytics demos](https://amslabs.azurewebsites.net/demos/Analytics.html)

