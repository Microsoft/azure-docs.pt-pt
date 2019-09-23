---
title: Detectar movimentos com Análise de Mídia do Azure | Microsoft Docs
description: O MP (processador de mídia Azure Media Motion Detector) permite que você identifique com eficiência as seções de interesse em um vídeo muito longo e não-evento.
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
ms.openlocfilehash: c053e4dfc38fc0f055ec91a6622ef7f767c13a86
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/16/2019
ms.locfileid: "69015325"
---
# <a name="detect-motions-with-azure-media-analytics"></a>Detectar movimentos com Análise de Mídia do Azure
## <a name="overview"></a>Descrição geral
O MP (processador de mídia **Azure Media Motion detector** ) permite que você identifique com eficiência as seções de interesse em um vídeo muito longo e não-evento. A detecção de movimento pode ser usada em seqüências de imagens de câmera estática para identificar as seções do vídeo em que ocorre o movimento. Ele gera um arquivo JSON contendo metadados com carimbos de data/hora e a região delimitadora em que o evento ocorreu.

Direcionado para feeds de vídeo de segurança, essa tecnologia é capaz de categorizar o movimento em eventos relevantes e falsos positivos, como sombras e mudanças de iluminação. Isso permite que você gere alertas de segurança de feeds de câmera sem ser spam com eventos irrelevantes intermináveis, além de poder extrair momentos de interesse de vídeos de vigilância longos.

O **Azure Media Motion detector** MP está atualmente em visualização.

Este artigo fornece detalhes sobre **Azure Media Motion detector** e mostra como usá-lo com o SDK dos serviços de mídia para .net

## <a name="motion-detector-input-files"></a>Arquivos de entrada do detector de movimento
Arquivos de vídeo. Atualmente, há suporte para os seguintes formatos: MP4, MOV e WMV.

## <a name="task-configuration-preset"></a>Configuração de tarefa (predefinição)
Ao criar uma tarefa com **Azure Media Motion detector**, você deve especificar uma predefinição de configuração. 

### <a name="parameters"></a>Parâmetros
Você pode usar os seguintes parâmetros:

| Nome | Opções | Descrição | Predefinição |
| --- | --- | --- | --- |
| sensitivityLevel |String: 'low', 'medium', 'high' |Define o nível de sensibilidade no qual os movimentos são relatados. Ajuste para ajustar o número de falsos positivos. |médio |
| frameSamplingValue |Inteiro positivo |Define a frequência na qual o algoritmo é executado. 1 é igual a cada quadro, 2 significa cada segundo quadro e assim por diante. |1 |
| detectLightChange |Boolean: 'true', 'false' |Define se as alterações leves são relatadas nos resultados |For |
| mergeTimeThreshold |XS-time: Hh:mm:ss<br/>Exemplo: 00:00:03 |Especifica a janela de tempo entre eventos de movimento em que dois eventos são combinados e relatados como 1. |00:00:00 |
| detectionZones |Uma matriz de zonas de detecção:<br/>-A zona de detecção é uma matriz de três ou mais pontos<br/>-Point é uma coordenada x e y de 0 a 1. |Descreve a lista de zonas de detecção poligonal a serem usadas.<br/>Os resultados são relatados com as zonas como uma ID, com a primeira sendo ' ID ': 0 |Zona única, que abrange todo o quadro. |

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

## <a name="motion-detector-output-files"></a>Arquivos de saída do detector de movimento
Um trabalho de detecção de movimento retorna um arquivo JSON no ativo de saída, que descreve os alertas de movimento e suas categorias dentro do vídeo. O arquivo contém informações sobre a hora e a duração do movimento detectado no vídeo.

A API do detector de movimento fornece indicadores quando há objetos em movimento em um vídeo de plano de fundo fixo (por exemplo, um vídeo de vigilância). O detector de movimento é treinado para reduzir alarmes falsos, como alterações de iluminação e sombra. As limitações atuais dos algoritmos incluem vídeos de visão noturna, objetos semitransparentes e objetos pequenos.

### <a id="output_elements"></a>Elementos do arquivo JSON de saída
> [!NOTE]
> Na versão mais recente, o formato JSON de saída foi alterado e pode representar uma alteração significativa para alguns clientes.
> 
> 

A tabela a seguir descreve os elementos do arquivo JSON de saída.

| Elemento | Descrição |
| --- | --- |
| version |Isso se refere à versão da API de vídeo. A versão atual é 2. |
| timescale |"Tiques" por segundo do vídeo. |
| desvio |O deslocamento de tempo para carimbos de data/hora em "tiques". Na versão 1,0 de APIs de vídeo, isso será sempre 0. Em cenários futuros com suporte, esse valor pode ser alterado. |
| quadros |Fotogramas por segundo do vídeo. |
| largura, altura |Refere-se à largura e à altura do vídeo em pixels. |
| start |O carimbo de data/hora inicial em "tiques". |
| duration |O comprimento do evento, em "tiques". |
| interval |O intervalo de cada entrada no evento, em "tiques". |
| eventos |Cada fragmento de evento contém o movimento detectado dentro dessa duração de tempo. |
| type |Na versão atual, é sempre ' 2 ' para o movimento genérico. Esse rótulo fornece às APIs de vídeo a flexibilidade para categorizar o movimento em versões futuras. |
| regionId |Conforme explicado acima, isso sempre será 0 nesta versão. Esse rótulo dá à API de vídeo a flexibilidade para encontrar o movimento em várias regiões em versões futuras. |
| regiões |Refere-se à área em seu vídeo onde você se preocupa com o Motion. <br/><br/>-"ID" representa a área de região – nesta versão, há apenas uma, ID 0. <br/>-"tipo" representa a forma da região com a qual você se preocupa para o movimento. Atualmente, há suporte para "Rectangle" e "Polygon".<br/> Se você especificou "Rectangle", a região terá dimensões em X, Y, largura e altura. As coordenadas X e Y representam as coordenadas XY do lado esquerdo superior da região em uma escala normalizada de 0,0 a 1,0. A largura e a altura representam o tamanho da região em uma escala normalizada de 0,0 a 1,0. Na versão atual, X, Y, largura e altura são sempre corrigidos em 0, 0 e 1, 1. <br/>Se você tiver especificado "Polygon", a região terá dimensões em pontos. <br/> |
| fragmentos |Os metadados são divididos em segmentos diferentes chamados fragmentos. Cada fragmento contém um início, duração, número de intervalos e evento(s). Um fragmento sem eventos significa que nenhum movimento foi detectado durante essa hora e duração de início. |
| colchetes [] |Cada colchete representa um intervalo no evento. Colchetes vazios para esse intervalo significam que nenhum movimento foi detectado. |
| locations |Essa nova entrada em eventos lista o local em que o movimento ocorreu. Isso é mais específico do que as zonas de detecção. |

O exemplo de JSON a seguir mostra a saída:

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
* Os formatos de vídeo de entrada com suporte incluem MP4, MOV e WMV.
* A detecção de movimento é otimizada para vídeos em segundo plano estacionários. O algoritmo se concentra na redução de alarmes falsos, como alterações de iluminação e sombras.
* Algum movimento pode não ser detectado devido a desafios técnicos; por exemplo, vídeos de visão noturna, objetos semitransparentes e objetos pequenos.

## <a name="net-sample-code"></a>Código de exemplo do .NET

O programa a seguir mostra como:

1. Crie um ativo e carregue um arquivo de mídia no ativo.
2. Crie um trabalho com uma tarefa de detecção de movimento de vídeo com base em um arquivo de configuração que contenha a predefinição de JSON a seguir: 
   
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
[Blog do detector de movimento dos serviços de mídia do Azure](https://azure.microsoft.com/blog/motion-detector-update/)

[Visão geral da análise dos serviços de mídia do Azure](media-services-analytics-overview.md)

[Demonstrações de Análise de Mídia do Azure](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

