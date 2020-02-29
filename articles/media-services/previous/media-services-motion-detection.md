---
title: Detete movimentos com Azure Media Analytics Microsoft Docs
description: O processador de mídia Azure Media Motion Detetor (MP) permite identificar de forma eficiente secções de interesse dentro de um vídeo longo e tranquilo.
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
ms.author: juliako
ms.reviewer: milanga
ms.openlocfilehash: f4c021531a4d04bf16e5dbee4172952433f675d9
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77913009"
---
# <a name="detect-motions-with-azure-media-analytics"></a>Detete movimentos com Azure Media Analytics

> [!NOTE]
> O processador de mídia **Azure Media Motion Detetor** será retirado. Para a data da reforma, consulte o tema dos [componentes do legado.](legacy-components.md)
 
## <a name="overview"></a>Descrição geral

O processador de mídia **Azure Media Motion Detetor** (MP) permite identificar de forma eficiente secções de interesse dentro de um vídeo longo e tranquilo. A deteção de movimentos pode ser usada em imagens estáticas de câmaras para identificar secções do vídeo onde o movimento ocorre. Gera um ficheiro JSON contendo metadados com selos temporais e a região de delimitação onde ocorreu o evento.

Direcionada para feeds de vídeo de segurança, esta tecnologia é capaz de categorizar o movimento em eventos relevantes e falsos positivos, como sombras e mudanças de iluminação. Isto permite-lhe gerar alertas de segurança a partir de imagens de câmaras sem ser enviado spam com eventos irrelevantes intermináveis, ao mesmo tempo que é capaz de extrair momentos de interesse de longos vídeos de vigilância.

O DETETOR de **Movimento Azure Media** está atualmente em Pré-visualização.

Este artigo fornece detalhes sobre o Detetor de Movimento de **Mídia Azure** e mostra como usá-lo com Media Services SDK para .NET

## <a name="motion-detector-input-files"></a>Ficheiros de entrada do Detetor de Movimento
Ficheiros de vídeo. Atualmente, os seguintes formatos são suportados: MP4, MOV e WMV.

## <a name="task-configuration-preset"></a>Configuração de tarefas (predefinição)
Ao criar uma tarefa com o Detetor de **Movimento Azure Media,** deve especificar um predefinição de configuração. 

### <a name="parameters"></a>Parâmetros
Pode utilizar os seguintes parâmetros:

| Nome | Opções | Descrição | Predefinição |
| --- | --- | --- | --- |
| sensibilidadeN |String: 'low', 'medium', 'high' |Define o nível de sensibilidade a que os movimentos são relatados. Ajuste isto para ajustar o número de falsos positivos. |'médio' |
| frameSamplingValue |Inteiro positivo |Define a frequência em que o algoritmo corre. 1 é igual a cada quadro, 2 significa cada segundo quadro, e assim por diante. |1 |
| detectLightChange |Boolean: 'true', 'false' |Define se as alterações de luz são reportadas nos resultados |'Falso' |
| mergeTimeThreshold |Xs-time: Hh:mm:ss<br/>Exemplo: 00:00:03 |Especifica a janela de tempo entre eventos de movimento onde 2 eventos são combinados e reportados como 1. |00:00:00 |
| zonas de deteção |Uma variedade de zonas de deteção:<br/>- Zona de Deteção é uma variedade de 3 ou mais pontos<br/>- O ponto é uma coordenada x e y de 0 a 1. |Descreve a lista de zonas de deteção poligonal a utilizar.<br/>Os resultados são reportados com as zonas como identificação, sendo que o primeiro é 'id':0 |Zona única, que cobre toda a moldura. |

### <a name="json-example"></a>Exemplo jSON

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

## <a name="motion-detector-output-files"></a>Ficheiros de saída do Detetor de Movimento
Um trabalho de deteção de movimentos devolve um ficheiro JSON no ativo de saída, que descreve os alertas de movimento, e as suas categorias, dentro do vídeo. O ficheiro contém informações sobre a hora e duração do movimento detetados no vídeo.

A API do Detetor de Movimento fornece indicadores uma vez que há objetos em movimento num vídeo de fundo fixo (por exemplo, um vídeo de vigilância). O Detetor de Movimento é treinado para reduzir falsos alarmes, tais como iluminação e mudanças de sombra. As limitações atuais dos algoritmos incluem vídeos de visão noturna, objetos semi-transparentes e pequenos objetos.

### <a id="output_elements"></a>Elementos do ficheiro JSON de saída
> [!NOTE]
> No último lançamento, o formato Output JSON mudou e pode representar uma mudança de rutura para alguns clientes.
> 
> 

A tabela seguinte descreve elementos do ficheiro JSON de saída.

| Elemento | Descrição |
| --- | --- |
| version |Isto refere-se à versão da API de vídeo. A versão atual é 2. |
| timescale |"Carrapatos" por segundo do vídeo. |
| compensado |O tempo compensado por carimbos de tempo em "tiques". Na versão 1.0 das APIs de Vídeo, este será sempre 0. Em cenários futuros que apoiamos, este valor pode mudar. |
| framerate |Fotogramas por segundo do vídeo. |
| largura, altura |Refere-se à largura e altura do vídeo em pixels. |
| start |A marca de início em "tiques". |
| duration |A duração do evento, em "carrapatos". |
| intervalo |O intervalo de cada entrada no evento, em "tiques". |
| eventos |Cada fragmento de evento contém o movimento detetado dentro desse período de tempo. |
| tipo |Na versão atual, este é sempre '2' para movimento genérico. Esta etiqueta dá aos APIs de vídeo a flexibilidade para categorizar o movimento em futuras versões. |
| regionId |Como explicado acima, este será sempre 0 nesta versão. Esta etiqueta confere à Video API a flexibilidade para encontrar movimento em várias regiões em futuras versões. |
| regiões |Refere-se à área no seu vídeo onde se preocupa com o movimento. <br/><br/>-"id" representa a região – nesta versão há apenas um, ID 0. <br/>-"tipo" representa a forma da região com que se preocupa com o movimento. Atualmente, são apoiados "retângulo" e "polígono".<br/> Se especificou "retângulo", a região tem dimensões em X, Y, Largura e Altura. As coordenadas X e Y representam as coordenadas XY superiores à esquerda da região numa escala normalizada de 0,0 a 1.0. A largura e a altura representam o tamanho da região numa escala normalizada de 0,0 a 1,0. Na versão atual, X, Y, Largura e Altura são sempre fixados a 0, 0 e 1, 1. <br/>Se especificou "polígono", a região tem dimensões em pontos. <br/> |
| fragmentos |Os metadados são divididos em diferentes segmentos chamados fragmentos. Cada fragmento contém um início, duração, número de intervalos e evento(s). Um fragmento sem eventos significa que não foi detetado nenhum movimento durante a hora e duração do início. |
| parênteses [] |Cada suporte representa um intervalo no caso. Os suportes vazios para esse intervalo significam que não foi detetado nenhum movimento. |
| locations |Esta nova entrada em eventos lista o local onde ocorreu a moção. Isto é mais específico do que as zonas de deteção. |

O exemplo json seguinte mostra a saída:

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
* A Deteção de Movimentos está otimizada para vídeos estacionários de fundo. O algoritmo centra-se na redução de falsos alarmes, tais como mudanças de iluminação e sombras.
* Alguma sufião pode não ser detetada devido a desafios técnicos; por exemplo, vídeos de visão noturna, objetos semi-transparentes e pequenos objetos.

## <a name="net-sample-code"></a>Código de amostra .NET

O seguinte programa mostra como:

1. Crie um ativo e faça upload de um ficheiro de mídia para o ativo.
2. Crie um trabalho com uma tarefa de deteção de movimento de vídeo com base num ficheiro de configuração que contenha o seguinte preset json: 
   
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
[Blog do Detetor de Movimento de Serviços de Mídia Azure](https://azure.microsoft.com/blog/motion-detector-update/)

[Visão geral da Análise de Serviços de Mídia Azure](media-services-analytics-overview.md)

[Demonstrações azure Media Analytics](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

