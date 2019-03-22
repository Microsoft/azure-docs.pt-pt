---
title: A digitalização de texto com o OCR de análise de multimédia do Azure | Documentos da Microsoft
description: OCR de análise de multimédia do Azure (reconhecimento ótico de carateres) permite-lhe converter o conteúdo de texto em ficheiros de vídeo em texto digital editável, pesquisável.  Isto permite-lhe automatizar a extração de metadados significativos do sinal de vídeo do seu suporte de dados.
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
ms.openlocfilehash: 91fad34073d7505c596bedfb6c93946ee7393dd7
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58315135"
---
# <a name="use-azure-media-analytics-to-convert-text-content-in-video-files-into-digital-text"></a>Utilize a análise de multimédia do Azure para converter o conteúdo de texto em ficheiros de vídeo digital texto  
## <a name="overview"></a>Descrição geral
Se precisar de extrair conteúdo de texto dos seus ficheiros de vídeo e gerar um texto digital editável, pesquisável, deve utilizar o Azure Media Analytics OCR (reconhecimento ótico de carateres). Este processador de multimédia do Azure Deteta o conteúdo de texto nos seus ficheiros de vídeo e gera arquivos de texto para a sua utilização. OCR permite-lhe automatizar a extração de metadados significativos do sinal de vídeo do seu suporte de dados.

Quando utilizado em conjunto com um mecanismo de pesquisa, pode facilmente seus suportes de dados de índice pelo texto e otimizar a deteção do seu conteúdo. Isso é extremamente útil vídeo altamente textuais, como uma gravação de vídeo ou a captura de tela de uma apresentação de apresentação de slides. O processador de multimédia do Azure OCR está otimizado para texto digital.

O **OCR de suporte de dados do Azure** processador de multimédia está atualmente em pré-visualização.

Este artigo fornece detalhes sobre **OCR de suporte de dados do Azure** e mostra como utilizá-lo com o SDK de Media Services para .NET. Para obter mais informações e exemplos, consulte [este blog](https://azure.microsoft.com/blog/announcing-video-ocr-public-preview-new-config/).

## <a name="ocr-input-files"></a>Ficheiros de entrada de OCR
Ficheiros de vídeo. Atualmente, são suportados os seguintes formatos: MP4, MOV e WMV.

## <a name="task-configuration"></a>Configuração das tarefas
Configuração da tarefa (predefinição). Ao criar uma tarefa com **OCR de suporte de dados do Azure**, tem de especificar uma configuração predefinida com o JSON ou XML de configuração. 

>[!NOTE]
>O motor de OCR só aceita uma região de imagem com o mínimo de 40 pixéis para 32000 pixels máximos como uma entrada válida em ambos os largura/altura.
>

### <a name="attribute-descriptions"></a>Descrições de atributo
| Nome de atributo | Descrição |
| --- | --- |
|AdvancedOutput| Se definir AdvancedOutput como true, a saída JSON irá conter dados posicionais para cada única palavra (além de frases e regiões). Se não quiser ver estes detalhes, defina o sinalizador como false. O valor predefinido é false. Para obter mais informações, veja [este blogue](https://azure.microsoft.com/blog/azure-media-ocr-simplified-output/).|
| Idioma |(opcional) descreve o idioma de texto para o qual a aparência. Um dos seguintes: Deteção automática (predefinição), árabe, ChineseSimplified, ChineseTraditional, Tcheco dinamarquês, Holandês, inglês, finlandês, francês, alemão, grego, húngaro, italiano, japonês, coreano, norueguês, polaco, português, romeno, russo, SerbianCyrillic, SerbianLatin , Eslovaco, espanhol, sueco, turco. |
| TextOrientation |(opcional) descreve a orientação do texto para o qual a aparência.  "Left" significa que a parte superior de todas as letras são apontado em direção à esquerda.  Texto predefinido (como o que pode ser encontrado num livro) pode ser chamado "Up" orientado.  Um dos seguintes: Deteção automática (predefinição), até, certo, para baixo, à esquerda. |
| TimeInterval |(opcional) descreve a frequência de amostragem.  A predefinição é a cada segundo de 1/2.<br/>Formato JSON – hh: mm:. SSS (00:00:00.500 predefinido)<br/>Formato XML – primitivo de duração do W3C XSD (predefinição PT0.5) |
| DetectRegions |(opcional) Uma matriz de objetos de DetectRegion especificando regiões no âmbito do quadro do vídeo na qual detetar texto.<br/>Um objeto de DetectRegion é constituído pelos seguintes valores de número inteiro de quatro:<br/>Esquerda – pixels a partir da margem da esquerda<br/>Parte superior – pixels a partir da margem da parte superior<br/>Largura – a largura da região em pixels<br/>Altura – a altura da região em pixels |

#### <a name="json-preset-example"></a>Exemplo de JSON predefinido

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

#### <a name="xml-preset-example"></a>Exemplo XML predefinido

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

## <a name="ocr-output-files"></a>Ficheiros de saída de OCR
A saída do processador de multimédia OCR é um ficheiro JSON.

### <a name="elements-of-the-output-json-file"></a>Elementos do ficheiro de saída JSON
A saída de vídeo OCR fornece dados de tempo-segmentado nos caracteres encontrados no seu vídeo.  Pode usar atributos, tais como o idioma ou orientação para hone-in no exatamente as palavras que está interessado na análise. 

A saída contém os seguintes atributos:

| Elemento | Descrição |
| --- | --- |
| Escala temporal |"ticks" por segundo do vídeo |
| Desvio |tempo de deslocamento de carimbos. Na versão 1.0 de APIs de vídeo, ela sempre será 0. |
| Taxa de fotogramas |Quadros por segundo do vídeo |
| Largura |largura do vídeo em pixéis |
| Altura |altura do vídeo em pixéis |
| Fragmentos |matriz de segmentos baseados no tempo de vídeo para o qual os metadados é segmentado |
| start |hora de início de um fragmento de "ticks" |
| duração |comprimento de um fragmento de "ticks" |
| intervalo |intervalo de cada evento dentro do fragmento de determinado |
| eventos |matriz que contém regiões |
| Região |objeto que representa detetado palavras ou frases |
| language |idioma do texto detetado dentro de uma região |
| Orientação |orientação do texto detetado dentro de uma região |
| linhas |matriz de linhas de texto detetados dentro de uma região |
| texto |o texto real |

### <a name="json-output-example"></a>Exemplo de saída do JSON
O exemplo de saída seguinte contém as informações de vídeo gerais e de vários fragmentos de vídeo. Em cada fragmento de vídeo, ele contém todas as regiões, o que é detectada pelo pacote de gestão de OCR com a linguagem e sua orientação de texto. A região também contém todas as linhas word nesta região com o texto da linha, posição da linha e todas as informações de palavra (conteúdo do word, posição e confiança) nessa linha. Segue-se um exemplo e coloquei alguns inline de comentários.

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

O seguinte programa mostra como:

1. Criar um elemento e carregue um ficheiro de multimédia para o elemento.
2. Crie uma tarefa com um ficheiro de configuração/configuração predefinida de OCR.
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
[Descrição geral da análise dos serviços de multimédia do Azure](media-services-analytics-overview.md)

