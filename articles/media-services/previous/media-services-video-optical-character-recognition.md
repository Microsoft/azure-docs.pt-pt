---
title: Digitalizar texto com Análise de Mídia do Azure OCR | Microsoft Docs
description: Análise de Mídia do Azure OCR (reconhecimento óptico de caracteres) permite que você converta o conteúdo de texto em arquivos de vídeo em texto digital editável e pesquisável.  Isso permite que você automatize a extração de metadados significativos do sinal de vídeo de sua mídia.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 307c196e-3a50-4f4b-b982-51585448ffc6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 11f897852ce820e666d7403f42735b2ee3bdd73b
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084813"
---
# <a name="use-azure-media-analytics-to-convert-text-content-in-video-files-into-digital-text"></a>Use Análise de Mídia do Azure para converter o conteúdo de texto em arquivos de vídeo em texto digital  

## <a name="overview"></a>Descrição geral
Se você precisar extrair o conteúdo de texto de seus arquivos de vídeo e gerar um texto digital editável e pesquisável, deverá usar Análise de Mídia do Azure OCR (reconhecimento óptico de caracteres). Esse processador de mídia do Azure detecta o conteúdo de texto em seus arquivos de vídeo e gera arquivos de texto para seu uso. O OCR permite automatizar a extração de metadados significativos do sinal de vídeo de sua mídia.

Quando usado em conjunto com um mecanismo de pesquisa, você pode indexar facilmente sua mídia por texto e aprimorar a capacidade de descoberta do seu conteúdo. Isso é extremamente útil em vídeo altamente textual, como uma gravação de vídeo ou captura de tela de uma apresentação de slides. O processador de mídia do OCR do Azure é otimizado para texto digital.

O processador de mídia de **OCR de mídia do Azure** está atualmente em versão prévia.

Este artigo fornece detalhes sobre o **OCR de mídia do Azure** e mostra como usá-lo com o SDK dos serviços de mídia para .net. Para obter mais informações e exemplos, consulte [este blog](https://azure.microsoft.com/blog/announcing-video-ocr-public-preview-new-config/).

## <a name="ocr-input-files"></a>Arquivos de entrada OCR
Arquivos de vídeo. Atualmente, há suporte para os seguintes formatos: MP4, MOV e WMV.

## <a name="task-configuration"></a>Configuração das tarefas
Configuração da tarefa (predefinição). Ao criar uma tarefa com o **OCR de mídia do Azure**, você deve especificar uma predefinição de configuração usando JSON ou XML. 

>[!NOTE]
>O mecanismo de OCR usa apenas uma região de imagem com, no mínimo, 40 pixels para máximo de 32000 pixels como uma entrada válida em altura/largura.
>

### <a name="attribute-descriptions"></a>Descrições de atributo
| Nome do atributo | Descrição |
| --- | --- |
|AdvancedOutput| Se você definir AdvancedOutput como true, a saída JSON conterá dados posicionais para cada palavra única (além de frases e regiões). Se você não quiser ver esses detalhes, defina o sinalizador como false. O valor predefinido é false. Para obter mais informações, veja [este blogue](https://azure.microsoft.com/blog/azure-media-ocr-simplified-output/).|
| Idioma |(opcional) descreve o idioma do texto a ser examinado. Um dos seguintes: AutoDetect (padrão), árabe, ChineseSimplified, ChineseTraditional, tcheco dinamarquês, holandês, inglês, finlandês, francês, alemão, grego, húngaro, italiano, japonês, coreano, norueguês, polonês, Português, romeno, russo, SerbianCyrillic, SerbianLatin, eslovaco, espanhol, Sueco, Turco. |
| TextOrientation |(opcional) descreve a orientação do texto a ser examinado.  "Left" significa que a parte superior de todas as letras é apontada para a esquerda.  O texto padrão (como o que pode ser encontrado em um livro) pode ser chamado de "up" orientado.  Um dos seguintes: AutoDetect (padrão), up, direita, Down e Left. |
| TimeInterval |(opcional) descreve a taxa de amostragem.  O padrão é a cada 1/2 segundo.<br/>Formato JSON – HH: mm: SS. SSS (padrão 00:00:00.500)<br/>Formato XML – duração XSD do W3C (padrão PT 0.5) |
| DetectRegions |adicional Uma matriz de objetos DetectRegion especificando regiões dentro do quadro de vídeo no qual detectar texto.<br/>Um objeto DetectRegion é composto pelos quatro valores inteiros a seguir:<br/>Esquerda – pixels da margem esquerda<br/>Superior – pixels da margem superior<br/>Largura – largura da região em pixels<br/>Altura – altura da região em pixels |

#### <a name="json-preset-example"></a>Exemplo de predefinição de JSON

```json
    {
        "Version":1.0, 
        "Options": 
        {
            "AdvancedOutput":"true",
            "Language":"English", 
            "TimeInterval":"00:00:01.5",
            "TextOrientation":"Up",
            "DetectRegions": [
                    {
                       "Left": 10,
                       "Top": 10,
                       "Width": 100,
                       "Height": 50
                    }
             ]
        }
    }
```

#### <a name="xml-preset-example"></a>Exemplo de predefinição de XML

```xml
    <?xml version=""1.0"" encoding=""utf-16""?>
    <VideoOcrPreset xmlns:xsi=""https://www.w3.org/2001/XMLSchema-instance"" xmlns:xsd=""https://www.w3.org/2001/XMLSchema"" Version=""1.0"" xmlns=""https://www.windowsazure.com/media/encoding/Preset/2014/03"">
      <Options>
         <AdvancedOutput>true</AdvancedOutput>
         <Language>English</Language>
         <TimeInterval>PT1.5S</TimeInterval>
         <DetectRegions>
             <DetectRegion>
                   <Left>10</Left>
                   <Top>10</Top>
                   <Width>100</Width>
                   <Height>50</Height>
            </DetectRegion>
       </DetectRegions>
       <TextOrientation>Up</TextOrientation>
      </Options>
    </VideoOcrPreset>
```

## <a name="ocr-output-files"></a>Arquivos de saída de OCR
A saída do processador de mídia de OCR é um arquivo JSON.

### <a name="elements-of-the-output-json-file"></a>Elementos do arquivo JSON de saída
A saída de OCR de vídeo fornece dados de segmento de tempo nos caracteres encontrados em seu vídeo.  Você pode usar atributos como idioma ou orientação para aprimorar exatamente as palavras que você está interessado em analisar. 

A saída contém os seguintes atributos:

| Elemento | Descrição |
| --- | --- |
| Escala temporal |"tiques" por segundo do vídeo |
| Desvio |diferença de tempo para carimbos de data/hora. Na versão 1,0 de APIs de vídeo, isso será sempre 0. |
| Taxa de fotogramas |Quadros por segundo do vídeo |
| Largura |largura do vídeo em pixels |
| tamanho |altura do vídeo em pixels |
| Fragmentos |matriz de partes baseadas em tempo de vídeo em que os metadados são fragmentados |
| start |hora de início de um fragmento em "tiques" |
| duration |comprimento de um fragmento em "tiques" |
| intervalo |intervalo de cada evento dentro do fragmento fornecido |
| eventos |matriz que contém regiões |
| . |objeto representando palavras ou frases detectadas |
| language |idioma do texto detectado em uma região |
| direção |orientação do texto detectado em uma região |
| alinha |matriz de linhas de texto detectadas em uma região |
| texto |o texto real |

### <a name="json-output-example"></a>Exemplo de saída JSON
O exemplo de saída a seguir contém as informações gerais de vídeo e vários fragmentos de vídeo. Em cada fragmento de vídeo, ele contém todas as regiões, que são detectadas pelo MP de OCR com a linguagem e sua orientação de texto. A região também contém todas as linhas de palavras nessa região com o texto da linha, a posição da linha e todas as informações do Word (conteúdo, posição e confiança do Word) nessa linha. Veja a seguir um exemplo, e coloco alguns comentários embutidos.

```json
    {
        "version": 1, 
        "timescale": 90000, 
        "offset": 0, 
        "framerate": 30, 
        "width": 640, 
        "height": 480,  // general video information
        "fragments": [
            {
                "start": 0, 
                "duration": 180000, 
                "interval": 90000,  // the time information about this fragment
                "events": [
                    [
                       { 
                            "region": { // the detected region array in this fragment 
                                "language": "English",  // region language
                                "orientation": "Up",  // text orientation
                                "lines": [  // line information array in this region, including the text and the position
                                    {
                                        "text": "One Two", 
                                        "left": 10, 
                                        "top": 10, 
                                        "right": 210, 
                                        "bottom": 110, 
                                        "word": [  // word information array in this line
                                            {
                                                "text": "One", 
                                                "left": 10, 
                                                "top": 10, 
                                                "right": 110, 
                                                "bottom": 110, 
                                                "confidence": 900
                                            }, 
                                            {
                                                "text": "Two", 
                                                "left": 110, 
                                                "top": 10, 
                                                "right": 210, 
                                                "bottom": 110, 
                                                "confidence": 910
                                            }
                                        ]
                                    }
                                ]
                            }
                        }
                    ]
                ]
            }
        ]
    }
```

## <a name="net-sample-code"></a>Código de exemplo do .NET

O programa a seguir mostra como:

1. Crie um ativo e carregue um arquivo de mídia no ativo.
2. Crie um trabalho com um arquivo de configuração/predefinição de OCR.
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

namespace OCR
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

            // Run the OCR job.
            var asset = RunOCRJob(@"C:\supportFiles\OCR\presentation.mp4",
                                        @"C:\supportFiles\OCR\config.json");

            // Download the job output asset.
            DownloadAsset(asset, @"C:\supportFiles\OCR\Output");
        }

        static IAsset RunOCRJob(string inputMediaFilePath, string configurationFile)
        {
            // Create an asset and upload the input media file to storage.
            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                "My OCR Input Asset",
                AssetCreationOptions.None);

            // Declare a new job.
            IJob job = _context.Jobs.Create("My OCR Job");

            // Get a reference to Azure Media OCR.
            string MediaProcessorName = "Azure Media OCR";

            var processor = GetLatestMediaProcessorByName(MediaProcessorName);

            // Read configuration from the specified file.
            string configuration = File.ReadAllText(configurationFile);

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My OCR Task",
                processor,
                configuration,
                TaskOptions.None);

            // Specify the input asset.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("My OCR Output Asset", AssetCreationOptions.None);

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

