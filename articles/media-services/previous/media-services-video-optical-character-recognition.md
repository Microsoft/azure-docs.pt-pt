---
title: Digitalizar texto com Azure Media Analytics OCR [ Microsoft Docs
description: O Azure Media Analytics OCR (reconhecimento de caracteres óticos) permite converter conteúdos de texto em ficheiros de vídeo em texto digital editável e pescável.  Isto permite automatizar a extração de metadados significativos a partir do sinal de vídeo dos seus meios de comunicação.
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
ms.openlocfilehash: 11889bd6df0bcc9564c17fdaacc333df1d418660
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77918347"
---
# <a name="use-azure-media-analytics-to-convert-text-content-in-video-files-into-digital-text"></a>Use a Azure Media Analytics para converter conteúdo de texto em ficheiros de vídeo em texto digital  

> [!NOTE]
> O processador de mídia **Azure Media OCR** será retirado. Para a data da aposentadoria, consulte o tema dos [componentes do legado.](legacy-components.md)

## <a name="overview"></a>Descrição geral
Se precisar extrair conteúdo de texto dos seus ficheiros de vídeo e gerar um texto digital editável e pespável, deverá utilizar o OCR Azure Media Analytics (reconhecimento de caracteres óticos). Este Processador Azure Media deteta conteúdo de texto nos seus ficheiros de vídeo e gera ficheiros de texto para a sua utilização. O OCR permite-lhe automatizar a extração de metadados significativos a partir do sinal de vídeo dos seus meios de comunicação.

Quando usado em conjunto com um motor de busca, pode facilmente indexar os seus meios de comunicação por texto e melhorar a descoberta do seu conteúdo. Isto é extremamente útil em vídeos altamente texucionais, como uma gravação de vídeo ou captura de ecrã de uma apresentação de apresentação de slideshow. O Processador Azure OCR Media está otimizado para texto digital.

O processador **Azure Media OCR** encontra-se atualmente em Pré-Visualização.

Este artigo dá detalhes sobre **o Azure Media OCR** e mostra como usá-lo com a Media Services SDK para .NET. Para mais informações e exemplos, consulte [este blog.](https://azure.microsoft.com/blog/announcing-video-ocr-public-preview-new-config/)

## <a name="ocr-input-files"></a>Ficheiros de entrada de OCR
Ficheiros de vídeo. Atualmente, os seguintes formatos são suportados: MP4, MOV e WMV.

## <a name="task-configuration"></a>Configuração das tarefas
Configuração de tarefa (predefinição). Ao criar uma tarefa com **o Azure Media OCR,** deve especificar uma pré-sintonia de configuração utilizando JSON ou XML. 

>[!NOTE]
>O motor OCR só leva uma região de imagem com um mínimo de 40 pixels para máximos de 32000 pixels como uma entrada válida tanto em altura como em largura.
>

### <a name="attribute-descriptions"></a>Atribuir descrições
| Nome do atributo | Descrição |
| --- | --- |
|Saída Avançada| Se definir o AdvancedOutput como verdadeiro, a saída JSON conterá dados posicionais para cada palavra (além de frases e regiões). Se não quiser ver estes detalhes, coloque a bandeira em falso. O valor predefinido é false. Para mais informações, consulte [este blog.](https://azure.microsoft.com/blog/azure-media-ocr-simplified-output/)|
| Linguagem |(opcional) descreve a linguagem do texto para a qual procurar. Um dos seguintes: AutoDetect (padrão), Árabe, Chinês Simplificado, Chinês Tradicional, Português, Português, Holandês, Inglês, Finlandês, Francês, Alemão, Grego, Húngaro, Italiano, Japonês, Coreano, Norueguês, Polaco, Português, Romeno, Russo, SérvioCyrillic, Sérvio, Sérvio, Eslovaco, Espanhol, Sueco, Turco. |
| TextOrientation |(opcional) descreve a orientação do texto para o qual procurar.  "Esquerda" significa que o topo de todas as letras são apontados para a esquerda.  O texto predefinido (como o que pode ser encontrado num livro) pode ser chamado de "Up" orientado para o texto.  Um dos seguintes: AutoDetect (padrão), Up, Right, Down, Left. |
| TimeInterval |(opcional) descreve a taxa de amostragem.  O padrão é a cada 1/2 segundo.<br/>Formato JSON – HH:mm:ss. SSS (padrão 00:00:00.500)<br/>Formato XML – W3C XSD duração primitiva (PT0.5 padrão) |
| Deteção de Regiões |(opcional) Uma matriz de objetos detecteRegion especificando regiões dentro da moldura de vídeo para detetar texto.<br/>Um objeto detectRegion é feito dos seguintes quatro valores inteiros:<br/>Esquerda – pixels da margem esquerda<br/>Top – pixels da margem superior<br/>Largura – largura da região em pixels<br/>Altura – altura da região em pixels |

#### <a name="json-preset-example"></a>Exemplo predefinido JSON

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

#### <a name="xml-preset-example"></a>Exemplo predefinido XML

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

## <a name="ocr-output-files"></a>Ficheiros de saída OCR
A saída do processador de mídia OCR é um ficheiro JSON.

### <a name="elements-of-the-output-json-file"></a>Elementos do ficheiro JSON de saída
A saída de OCR de vídeo fornece dados segmentados por tempo sobre os caracteres encontrados no seu vídeo.  Pode utilizar atributos como linguagem ou orientação para aprimorar exatamente as palavras que está interessado em analisar. 

A saída contém os seguintes atributos:

| Elemento | Descrição |
| --- | --- |
| Escala temporal |"tiques" por segundo do vídeo |
| Desvio |tempo compensado para os tempos. Na versão 1.0 das APIs de vídeo, este será sempre 0. |
| Taxa de fotogramas |Quadros por segundo do vídeo |
| largura |largura do vídeo em pixels |
| altura |altura do vídeo em pixels |
| Fragmentos |matriz de pedaços de vídeo baseados no tempo em que os metadados são em pedaços |
| start |tempo de início de um fragmento em "tiques" |
| duration |comprimento de um fragmento em "carrapatos" |
| intervalo |intervalo de cada evento dentro do fragmento dado |
| eventos |matriz que contém regiões |
| . |objeto representando palavras ou frases detetadas |
| language |linguagem do texto detetado numa região |
| orientação |orientação do texto detetado numa região |
| linhas |matriz de linhas de texto detetadas dentro de uma região |
| texto |o texto real |

### <a name="json-output-example"></a>Exemplo de saída JSON
O exemplo de saída que se segue contém as informações gerais de vídeo e vários fragmentos de vídeo. Em cada fragmento de vídeo, contém todas as regiões, que são detetadas pelo OCR MP com a linguagem e a sua orientação de texto. A região também contém todas as linhas de palavras desta região com o texto da linha, a posição da linha, e cada palavra informação (conteúdo de palavras, posição e confiança) nesta linha. O que se segue é um exemplo, e eu coloquei alguns comentários em linha.

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

## <a name="net-sample-code"></a>.NET código de amostra

O seguinte programa mostra como:

1. Crie um ativo e carre faça o upload de um ficheiro de media para o ativo.
2. Crie um trabalho com um ficheiro de configuração/predefinição de OCR.
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
[Visão geral da Azure Media Services Analytics](media-services-analytics-overview.md)

