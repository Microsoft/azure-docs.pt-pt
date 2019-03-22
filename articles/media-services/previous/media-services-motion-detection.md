---
title: Detetar movimentos com análise de multimédia do Azure | Documentos da Microsoft
description: O processador de multimédia detetor de movimento de suporte de dados do Azure (MP) permite-lhe com eficiência identificar seções de interesse dentro de um vídeo de longo e rotineira, caso contrário.
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
ms.date: 03/19/2019
ms.author: milanga;juliako;
ms.openlocfilehash: e0b083cba575f4d1c0eb19afb76fca29431ae75e
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259107"
---
# <a name="detect-motions-with-azure-media-analytics"></a>Detetar movimentos com análise de multimédia do Azure
## <a name="overview"></a>Descrição geral
O **detetor de movimento de suporte de dados do Azure** processador de multimédia (MP) permite-lhe com eficiência identificar seções de interesse dentro de um vídeo de longo e rotineira, caso contrário. Deteção de movimento pode ser utilizada no filmagens de câmaras estático para identificar seções do vídeo que ocorre a equipe do motion. Gera um ficheiro JSON que contém uma metadados com carimbos de hora e a região delimitadora onde ocorreu o evento.

Essa tecnologia voltado para transmissões de vídeo de segurança, é possível categorizar o movimento em eventos relevantes e falsos positivos, como sombras e alterações de iluminação. Isto permite-lhe gerar alertas de segurança de feeds de câmera sem a ser spammed com eventos irrelevantes intermináveis, enquanto a capacidade de extrair os momentos de interesse de vídeos de vigilância longo.

O **detetor de movimento de suporte de dados do Azure** MP está atualmente em pré-visualização.

Este artigo fornece detalhes sobre **detetor de movimento de suporte de dados do Azure** e mostra como utilizá-lo com o SDK de Media Services para .NET

## <a name="motion-detector-input-files"></a>Ficheiros de entrada de detetor de movimento
Ficheiros de vídeo. Atualmente, são suportados os seguintes formatos: MP4, MOV e WMV.

## <a name="task-configuration-preset"></a>Configuração da tarefa (predefinição)
Ao criar uma tarefa com **detetor de movimento de suporte de dados do Azure**, tem de especificar uma predefinição de configuração. 

### <a name="parameters"></a>Parâmetros
Pode utilizar os seguintes parâmetros:

| Name | Opções | Descrição | Predefinição |
| --- | --- | --- | --- |
| sensitivityLevel |String:'low', 'medium', 'high' |Define a sensibilidade de nível no qual propostas são comunicadas. Ajuste esta opção para ajustar o número de falsos positivos. |'medium' |
| frameSamplingValue |número inteiro positivo |Define a frequência no qual o algoritmo é executado. cada quadro de igual a 1, 2 significa que cada quadro segunda e assim por diante. |1 |
| detectLightChange |Boolean:'true', 'false' |Define se são pequenas alterações são relatadas nos resultados |'False' |
| mergeTimeThreshold |Xs-time: Hh:mm:ss<br/>Exemplo: 00:00:03 |Especifica a janela de tempo entre os eventos de movimento em que são 2 eventos ser combinada e reportada como 1. |00:00:00 |
| detectionZones |Uma matriz de zonas de Deteção:<br/>-Deteção zona é uma matriz de pontos de 3 ou mais<br/>-Ponto é um x e y coordenadas de 0 a 1. |Descreve a lista das zonas de deteção polygonal a ser utilizado.<br/>Os resultados são apresentados com as zonas como um ID, sendo o primeiro "id": 0 |Zona única, que abrange o quadro completo. |

### <a name="json-example"></a>Exemplo de JSON

```json
    {
      "version": "1.0",
      "options": {
        "sensitivityLevel": "medium",
        "frameSamplingValue": 1,
        "detectLightChange": "False",
        "mergeTimeThreshold":
        "00:00:02",
        "detectionZones": [
          [
            {"x": 0, "y": 0},
            {"x": 0.5, "y": 0},
            {"x": 0, "y": 1}
           ],
          [
            {"x": 0.3, "y": 0.3},
            {"x": 0.55, "y": 0.3},
            {"x": 0.8, "y": 0.3},
            {"x": 0.8, "y": 0.55},
            {"x": 0.8, "y": 0.8},
            {"x": 0.55, "y": 0.8},
            {"x": 0.3, "y": 0.8},
            {"x": 0.3, "y": 0.55}
          ]
        ]
      }
    }
```

## <a name="motion-detector-output-files"></a>Ficheiros de saída de detetor de movimento
Uma tarefa de deteção de movimento devolve um ficheiro JSON no recurso de saída, que descreve os alertas de movimento e as respetivas categorias, no vídeo. O ficheiro contém informações sobre o tempo e a duração do motion detetada no vídeo.

A API de detetor de movimento fornece indicadores, uma vez que existem objetos em movimento no plano de fundo fixo vídeo (por exemplo, uma vigilância vídeo). O detetor de movimento é preparado para reduzir falsos alarmes, como iluminação e alterações de cópias de sombra. Limitações atuais dos algoritmos incluem vídeos de visão da noite, objetos semitransparente e objetos pequenos.

### <a id="output_elements"></a>Elementos do ficheiro de saída JSON
> [!NOTE]
> Na versão mais recente, o formato JSON de saída foi alterado e pode representar uma alteração de última hora para alguns clientes.
> 
> 

A tabela seguinte descreve os elementos do ficheiro de saída JSON.

| Elemento | Descrição |
| --- | --- |
| Versão |Refere-se para a versão da API de vídeo. A versão atual é 2. |
| Escala temporal |"Ticks" por segundo do vídeo. |
| Desvio |O desvio de tempo para os carimbos de data / no "ticks". Na versão 1.0 de APIs de vídeo, ela sempre será 0. No futuro cenários que suportamos, pode alterar este valor. |
| Taxa de fotogramas |Fotogramas por segundo do vídeo. |
| Largura, Altura |Refere-se para a largura e altura do vídeo em pixéis. |
| Iniciar |O carimbo de hora de início em "ticks". |
| Duração |O comprimento do evento, em "ticks". |
| Intervalo |O intervalo de cada entrada no evento, em "ticks". |
| Eventos |Cada fragmento de evento contém o motion detetado dentro desse período de tempo. |
| Type |Na versão atual, isso é sempre "2" para o movimento genérico. Isto dá a etiqueta as APIs de vídeo a flexibilidade para categorizar o motion em futuras versões. |
| RegionID |Tal como explicado anteriormente, isso será sempre 0 nesta versão. Esta etiqueta proporciona a flexibilidade para localizar o movimento em várias regiões em futuras versões de API de vídeo. |
| Regiões |Refere-se para a área no seu vídeo em que se preocupa motion. <br/><br/>-o "id" representa a área de região – nesta versão existe apenas um, ID de 0. <br/>-"tipo" representa a forma da região que mais lhe interessa para movimento. Atualmente, são suportados "retângulo" e "polígono".<br/> Se tiver especificado "retângulo", a região tem dimensões no X, Y, largura e altura. As coordenadas X e Y representam as coordenadas XY esquerdas superiores da região numa escala normalizada de 0,0 a 1,0. A largura e altura representam o tamanho da região numa escala normalizada de 0,0 a 1,0. Na versão atual, X, Y, largura e altura são sempre fixa em 0, 0 e 1, 1. <br/>Se tiver especificado "polígono", a região tem dimensões em pontos. <br/> |
| Fragmentos |Os metadados é segmentado cópias em diferentes segmentos chamados fragmentos. Cada fragmento contém um início, duração, número de intervalos e evento(s). Um fragmento com nenhum evento significa que não foi detetado o movimento de durante essa hora de início e a duração. |
| Brackets [] |Cada Reto de fecho representa um intervalo no evento. Vazio Retos para esse intervalo significa que não foi detetado o movimento de. |
| localizações |Essa nova entrada em eventos de lista a localização onde o movimento ocorreu. Isso é mais específico do que as zonas de deteção. |

O exemplo JSON seguinte mostra a saída:

```json
    {
      "version": 2,
      "timescale": 23976,
      "offset": 0,
      "framerate": 24,
      "width": 1280,
      "height": 720,
      "regions": [
        {
          "id": 0,
          "type": "polygon",
          "points": [{'x': 0, 'y': 0},
            {'x': 0.5, 'y': 0},
            {'x': 0, 'y': 1}]
        }
      ],
      "fragments": [
        {
          "start": 0,
          "duration": 226765
        },
        {
          "start": 226765,
          "duration": 47952,
          "interval": 999,
          "events": [
            [
              {
                "type": 2,
                "typeName": "motion",
                "locations": [
                  {
                    "x": 0.004184,
                    "y": 0.007463,
                    "width": 0.991667,
                    "height": 0.985185
                  }
                ],
                "regionId": 0
              }
            ],
```

## <a name="limitations"></a>Limitações
* Os formatos de vídeo de entrada suportados incluem MP4, MOV e WMV.
* Deteção de movimento é otimizada para vídeos em segundo plano estacionários. O algoritmo se concentra em reduzir falsos alarmes, como alterações de iluminação e sombras.
* Alguns motion poderão não ser detetado devido a desafios técnicos; Por exemplo, vídeos de visão da noite, objetos semitransparente e objetos pequenos.

## <a name="net-sample-code"></a>Código de exemplo do .NET

O seguinte programa mostra como:

1. Criar um elemento e carregue um ficheiro de multimédia para o elemento.
2. Crie uma tarefa com uma tarefa de deteção de movimento em vídeo com base num arquivo de configuração que contém a seguinte configuração predefinida de json: 
   
    ```json
            {
            "Version": "1.0",
            "Options": {
                "SensitivityLevel": "medium",
                "FrameSamplingValue": 1,
                "DetectLightChange": "False",
                "MergeTimeThreshold":
                "00:00:02",
                "DetectionZones": [
                [
                    {"x": 0, "y": 0},
                    {"x": 0.5, "y": 0},
                    {"x": 0, "y": 1}
                ],
                [
                    {"x": 0.3, "y": 0.3},
                    {"x": 0.55, "y": 0.3},
                    {"x": 0.8, "y": 0.3},
                    {"x": 0.8, "y": 0.55},
                    {"x": 0.8, "y": 0.8},
                    {"x": 0.55, "y": 0.8},
                    {"x": 0.3, "y": 0.8},
                    {"x": 0.3, "y": 0.55}
                ]
                ]
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

namespace VideoMotionDetection
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

            // Run the VideoMotionDetection job.
            var asset = RunVideoMotionDetectionJob(@"C:\supportFiles\VideoMotionDetection\BigBuckBunny.mp4",
                                        @"C:\supportFiles\VideoMotionDetection\config.json");

            // Download the job output asset.
            DownloadAsset(asset, @"C:\supportFiles\VideoMotionDetection\Output");
        }

        static IAsset RunVideoMotionDetectionJob(string inputMediaFilePath, string configurationFile)
        {
            // Create an asset and upload the input media file to storage.
            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                "My Video Motion Detection Input Asset",
                AssetCreationOptions.None);

            // Declare a new job.
            IJob job = _context.Jobs.Create("My Video Motion Detection Job");

            // Get a reference to Azure Media Motion Detector.
            string MediaProcessorName = "Azure Media Motion Detector";

            var processor = GetLatestMediaProcessorByName(MediaProcessorName);

            // Read configuration from the specified file.
            string configuration = File.ReadAllText(configurationFile);

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My Video Motion Detection Task",
                processor,
                configuration,
                TaskOptions.None);

            // Specify the input asset.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("My Video Motion Detection Output Asset", AssetCreationOptions.None);

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
[Blogue de detetor de movimento de serviços de multimédia do Azure](https://azure.microsoft.com/blog/motion-detector-update/)

[Descrição geral da análise dos serviços de multimédia do Azure](media-services-analytics-overview.md)

[Demonstrações de análise de multimédia do Azure](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

