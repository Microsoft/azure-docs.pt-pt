---
title: Redação faces com Análise de Mídia do Azure | Microsoft Docs
description: Este tópico demonstra como redação faces com o Azure Media Analytics.
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
ms.openlocfilehash: e350b6ed90324e7ed645d85c046fd74c0a089452
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "69016023"
---
# <a name="redact-faces-with-azure-media-analytics"></a>Redação faces com Análise de Mídia do Azure 
## <a name="overview"></a>Descrição geral
**Azure Media redactor** é um processador de mídia [análise de mídia do Azure](media-services-analytics-overview.md) (MP) que oferece uma edição facial escalonável na nuvem. A edição facial permite que você modifique seu vídeo para desfocar rostos de indivíduos selecionados. Talvez você queira usar o serviço de edição facial em cenários de segurança pública e de mídia de notícias. Alguns minutos de seqüência de imagens que contém vários rostos podem levar horas para serem editados manualmente, mas com esse serviço, o processo de edição facial exigirá apenas algumas etapas simples. Para obter mais informações, consulte [este](https://azure.microsoft.com/blog/azure-media-redactor/) blog.

Este artigo fornece detalhes sobre **Azure Media redactor** e mostra como usá-lo com o SDK dos serviços de mídia para .net.

## <a name="face-redaction-modes"></a>Modos de edição facial
A edição facial funciona detectando faces em cada quadro de vídeo e acompanhando o objeto de face para frente e para trás no tempo, para que a mesma pessoa também possa ser desfocada de outros ângulos. O processo de redação automatizado é complexo e nem sempre produz 100% da saída desejada, por esse motivo Análise de Mídia fornece duas maneiras de modificar a saída final.

Além de um modo totalmente automático, há um fluxo de trabalho de duas passagens, que permite a seleção/remoção de faces encontradas por meio de uma lista de IDs. Além disso, para fazer ajustes arbitrários por quadro, o MP usa um arquivo de metadados no formato JSON. Esse fluxo de trabalho é dividido em modos de **análise** e redação. Você pode combinar os dois modos em uma única passagem que executa ambas as tarefas em um trabalho; Esse modo é chamado **combinado**.

### <a name="combined-mode"></a>Modo combinado
Isso produz um MP4 reproduzido automaticamente sem nenhuma entrada manual.

| Fase | Nome de Ficheiro | Notas |
| --- | --- | --- |
| Ativo de entrada |foo.bar |Vídeo no formato WMV, MOV ou MP4 |
| Configuração de entrada |Predefinição de configuração de trabalho |{'version':'1.0', 'options': {'mode':'combined'}} |
| Ativo de saída |foo_redacted.mp4 |Vídeo com Desfoque aplicado |

#### <a name="input-example"></a>Exemplo de entrada:
[exibir este vídeo](https://ampdemo.azureedge.net/?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fed99001d-72ee-4f91-9fc0-cd530d0adbbc%2FDancing.mp4)

#### <a name="output-example"></a>Exemplo de saída:
[exibir este vídeo](https://ampdemo.azureedge.net/?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc6608001-e5da-429b-9ec8-d69d8f3bfc79%2Fdance_redacted.mp4)

### <a name="analyze-mode"></a>Modo de análise
A passagem de **análise** do fluxo de trabalho de duas passagens usa uma entrada de vídeo e produz um arquivo JSON de locais de face e imagens de jpg de cada face detectada.

| Fase | Nome de Ficheiro | Notas |
| --- | --- | --- |
| Ativo de entrada |foo.bar |Vídeo no formato WMV, MPV ou MP4 |
| Configuração de entrada |Predefinição de configuração de trabalho |{' version ': ' 1.0 ', ' Opções ': {' Mode ': ' ANALYZE '}} |
| Ativo de saída |foo_annotations.json |Dados de anotação de locais de face no formato JSON. Isso pode ser editado pelo usuário para modificar as caixas delimitadoras de desfoque. Consulte o exemplo abaixo. |
| Ativo de saída |foo_thumb%06d.jpg [foo_thumb000001.jpg, foo_thumb000002.jpg] |Um jpg cortado de cada face detectada, em que o número indica o rótulo da face |

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

### <a name="redact-mode"></a>Modo de redação
A segunda passagem do fluxo de trabalho usa um número maior de entradas que devem ser combinadas em um único ativo.

Isso inclui uma lista de IDs para desfoque, o vídeo original e as anotações JSON. Esse modo usa as anotações para aplicar o desfoque no vídeo de entrada.

A saída da passagem de análise não inclui o vídeo original. O vídeo precisa ser carregado no ativo de entrada para a tarefa modo de edição e selecionado como o arquivo primário.

| Fase | Nome de Ficheiro | Notas |
| --- | --- | --- |
| Ativo de entrada |foo.bar |Vídeo no formato WMV, MPV ou MP4. Mesmo vídeo da etapa 1. |
| Ativo de entrada |foo_annotations.json |arquivo de metadados de anotações da fase 1, com modificações opcionais. |
| Ativo de entrada |foo_IDList. txt (opcional) |Nova lista separada por linhas opcional de IDs de face para redigir. Se for deixado em branco, isso desfocará todas as faces. |
| Configuração de entrada |Predefinição de configuração de trabalho |{'version':'1.0', 'options': {'mode':'redact'}} |
| Ativo de saída |foo_redacted.mp4 |Vídeo com Desfoque aplicado com base em anotações |

#### <a name="example-output"></a>Exemplo de saída
Esta é a saída de um IDList com uma ID selecionada.

[exibir este vídeo](https://ampdemo.azureedge.net/?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fad6e24a2-4f9c-46ee-9fa7-bf05e20d19ac%2Fdance_redacted1.mp4)

Exemplo de foo_IDList. txt
 
     1
     2
     3

## <a name="blur-types"></a>Tipos de desfoque

No modo **combinado** ou **redação** , há cinco modos de desfoque diferentes que podem ser escolhidos por meio da configuração de entrada JSON: **Baixo**, **médio**, **alto**, **caixa**e **preto**. Por padrão, o **med** é usado.

Você pode encontrar exemplos dos tipos de desfoque abaixo.

### <a name="example-json"></a>Exemplo de JSON:

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

## <a name="elements-of-the-output-json-file"></a>Elementos do arquivo JSON de saída

O MP de edição fornece detecção e acompanhamento de local de face de alta precisão que podem detectar até 64 rostos humanas em um quadro de vídeo. Os frontais faces fornecem os melhores resultados, enquanto as faces laterais e pequenas faces (menores ou iguais a 24x24 pixels) são desafiadoras.

[!INCLUDE [media-services-analytics-output-json](../../../includes/media-services-analytics-output-json.md)]

## <a name="net-sample-code"></a>Código de exemplo do .NET

O programa a seguir mostra como:

1. Crie um ativo e carregue um arquivo de mídia no ativo.
2. Crie um trabalho com uma tarefa de edição facial baseada em um arquivo de configuração que contenha a predefinição JSON a seguir: 

    ```json
            {
                'version':'1.0',
                'options': {
                    'mode':'combined'
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
[Visão geral da análise dos serviços de mídia do Azure](media-services-analytics-overview.md)

[Demonstrações de Análise de Mídia do Azure](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

