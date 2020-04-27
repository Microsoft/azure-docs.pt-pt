---
title: Como gerar miniaturas com o Media Encoder Standard com .NET
description: Este tópico mostra como usar .NET para codificar um ativo e gerar miniaturas ao mesmo tempo usando o Media Encoder Standard.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: b8dab73a-1d91-4b6d-9741-a92ad39fc3f7
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 6bc29c098bcf7ef1d1a2e2532a00c95f0ec7e927
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/26/2020
ms.locfileid: "61244234"
---
# <a name="how-to-generate-thumbnails-using-media-encoder-standard-with-net"></a>Como gerar miniaturas com o Media Encoder Standard com .NET 

Pode utilizar o Media Encoder Standard para gerar uma ou mais miniaturas a partir do seu vídeo de entrada em formatos de ficheiros de imagem [JPEG,](https://en.wikipedia.org/wiki/JPEG) [PNG](https://en.wikipedia.org/wiki/Portable_Network_Graphics)ou [BMP.](https://en.wikipedia.org/wiki/BMP_file_format) Pode submeter Tarefas que produzam apenas imagens, ou pode combinar geração de miniaturas com codificação. Este artigo fornece algumas predefinições de miniatura sinuosa xML e JSON para tais cenários. No final do artigo, existe um código de [amostra](#code_sample) que mostra como utilizar os Media Services .NET SDK para realizar a tarefa de codificação.

Para obter mais detalhes sobre os elementos utilizados nas predefinições da amostra, deve rever o esquema Padrão media [Encoder](media-services-mes-schema.md).

Certifique-se de rever a secção [Considerações.](media-services-dotnet-generate-thumbnail-with-mes.md#considerations)
    
## <a name="example-of-a-single-png-file-preset"></a>Exemplo de um preset de "único ficheiro PNG"

A predefinição json e XML seguinte pode ser usada para produzir um único ficheiro PNG de saída a partir dos primeiros segundos do vídeo de entrada, onde o codificador faz uma tentativa de melhor esforço para encontrar uma moldura "interessante". Note que as dimensões da imagem de saída foram definidas para 100%, o que significa que estas correspondem às dimensões do vídeo de entrada. Note também como a definição "Formato" em "Saídas" é necessária para combinar a utilização de "PngLayers" na secção "Codecs". 

### <a name="json-preset"></a>Predefinição JSON

```json
    {
      "Version": 1.0,
      "Codecs": [
        {
          "PngLayers": [
            {
              "Type": "PngLayer",
              "Width": "100%",
              "Height": "100%"
            }
          ],
          "Start": "{Best}",
          "Type": "PngImage"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "PngFormat"
          }
        }
      ]
    }
```
    
### <a name="xml-preset"></a>Predefinição XML

```xml
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <PngImage Start="{Best}">
          <PngLayers>
            <PngLayer>
              <Width>100%</Width>
              <Height>100%</Height>
            </PngLayer>
          </PngLayers>
        </PngImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Index}{Extension}">
          <PngFormat />
        </Output>
      </Outputs>
    </Preset>
```

## <a name="example-of-a-series-of-jpeg-images-preset"></a>Exemplo de uma predefinição de "série de imagens JPEG"

A predefinição json e XML seguinte pode ser usada para produzir um conjunto de 10 imagens em selos temporais de 5%, 15%, ..., 95% da linha temporal de entrada, onde o tamanho da imagem é especificado como um quarto do vídeo de entrada.

### <a name="json-preset"></a>Predefinição JSON

```json
    {
      "Version": 1.0,
      "Codecs": [
        {
          "JpgLayers": [
            {
              "Quality": 90,
              "Type": "JpgLayer",
              "Width": "25%",
              "Height": "25%"
            }
          ],
          "Start": "5%",
          "Step": "10%",
          "Range": "96%",
          "Type": "JpgImage"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
          }
        }
      ]
    }
```

### <a name="xml-preset"></a>Predefinição XML
    
```xml
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <JpgImage Start="5%" Step="10%" Range="96%">
          <JpgLayers>
            <JpgLayer>
              <Width>25%</Width>
              <Height>25%</Height>
              <Quality>90</Quality>
            </JpgLayer>
          </JpgLayers>
        </JpgImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Index}{Extension}">
          <JpgFormat />
        </Output>
      </Outputs>
    </Preset>
```

## <a name="example-of-a-one-image-at-a-specific-timestamp-preset"></a>Exemplo de predefinição de "uma imagem a uma marca de tempo específica"

A predefinição JSON e XML seguinte pode ser usada para produzir uma única imagem JPEG na marca de 30 segundos do vídeo de entrada. Este preset espera que o vídeo de entrada tenha mais de 30 segundos de duração (caso contrário, o trabalho falha).

### <a name="json-preset"></a>Predefinição JSON

```json
    {
      "Version": 1.0,
      "Codecs": [
        {
          "JpgLayers": [
            {
              "Quality": 90,
              "Type": "JpgLayer",
              "Width": "25%",
              "Height": "25%"
            }
          ],
          "Start": "00:00:30",
          "Step": "1",
          "Range": "1",
          "Type": "JpgImage"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
          }
        }
      ]
    }
```

### <a name="xml-preset"></a>Predefinição XML
```xml
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <JpgImage Start="00:00:30" Step="00:00:01" Range="00:00:01">
          <JpgLayers>
            <JpgLayer>
              <Width>25%</Width>
              <Height>25%</Height>
              <Quality>90</Quality>
            </JpgLayer>
          </JpgLayers>
        </JpgImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Index}{Extension}">
          <JpgFormat />
        </Output>
      </Outputs>
    </Preset>
```

## <a name="example-of-a-thumbnails-at-different-resolutions-preset"></a>Exemplo de um predefinido de "miniaturas em diferentes resoluções"

O preset seguinte pode ser usado para gerar miniaturas em diferentes resoluções numa tarefa. No exemplo, nas posições 5%, 15%, ..., 95% da linha temporal de entrada, o codificador gera duas imagens – uma a 100% da resolução de vídeo de entrada e outra a 50%.

Note a utilização de {Resolução} macro no Nome de Ficheiro; indica ao codificador a utilização da largura e altura que especificou na secção de codificação do predefinido enquanto gera o nome de ficheiro das imagens de saída. Isto também ajuda a distinguir entre as diferentes imagens facilmente

### <a name="json-preset"></a>Predefinição JSON

```json
    {
      "Version": 1.0,
      "Codecs": [
        {
          "JpgLayers": [
        {
          "Quality": 90,
          "Type": "JpgLayer",
          "Width": "100%",
          "Height": "100%"
        },
        {
          "Quality": 90,
          "Type": "JpgLayer",
          "Width": "50%",
          "Height": "50%"
        }

          ],
          "Start": "5%",
          "Step": "10%",
          "Range": "96%",
          "Type": "JpgImage"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Resolution}_{Index}{Extension}",
          "Format": {
        "Type": "JpgFormat"
          }
        }
      ]
    }
```

### <a name="xml-preset"></a>Predefinição XML
```xml
    <?xml version="1.0" encoding="utf-8"?>
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
    <Encoding>
    <JpgImage Start="5%" Step="10%" Range="96%"><JpgImage Start="00:00:01" Step="00:00:15">
      <JpgLayers>
       <JpgLayer>
        <Width>100%</Width>
        <Height>100%</Height>
        <Quality>90</Quality>
       </JpgLayer>
       <JpgLayer>
        <Width>50%</Width>
        <Height>50%</Height>
        <Quality>90</Quality>
       </JpgLayer>
      </JpgLayers>
    </JpgImage>
    </Encoding>
    <Outputs>
      <Output FileName="{Basename}_{Resolution}_{Index}{Extension}">
        <JpgFormat/>
      </Output>
    </Outputs>
    </Preset>
```

## <a name="example-of-generating-a-thumbnail-while-encoding"></a>Exemplo de gerar uma miniatura enquanto codifica

Embora todos os exemplos acima tenham discutido como pode submeter uma tarefa de codificação que produz apenas imagens, também pode combinar codificação de vídeo/áudio com geração de miniaturas. O predefinido JSON e XML seguinte seguem dizer ao **Media Encoder Standard** para gerar uma miniatura durante a codificação.

### <a name="json-preset"></a><a id="json"></a>Predefinição JSON
Para obter informações sobre esquemas, consulte [este](https://msdn.microsoft.com/library/mt269962.aspx) artigo.

```json
    {
      "Version": 1.0,
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "SceneChangeDetection": "true",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 4500,
              "MaxBitrate": 4500,
              "BufferWindow": "00:00:05",
              "Width": 1280,
              "Height": 720,
              "ReferenceFrames": 3,
              "EntropyMode": "Cabac",
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
    
            }
          ],
          "Type": "H264Video"
        },
        {
          "JpgLayers": [
            {
              "Quality": 90,
              "Type": "JpgLayer",
              "Width": "100%",
              "Height": "100%"
            }
          ],
          "Start": "{Best}",
          "Type": "JpgImage"
        },
        {
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
          }
        },
        {
          "FileName": "{Basename}_{Resolution}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }
```

### <a name="xml-preset"></a><a id="xml"></a>Predefinição XML
Para obter informações sobre esquemas, consulte [este](https://msdn.microsoft.com/library/mt269962.aspx) artigo.

```csharp
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <H264Video>
          <KeyFrameInterval>00:00:02</KeyFrameInterval>
          <SceneChangeDetection>true</SceneChangeDetection>
          <H264Layers>
            <H264Layer>
              <Bitrate>4500</Bitrate>
              <Width>1280</Width>
              <Height>720</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>4500</MaxBitrate>
            </H264Layer>
          </H264Layers>
        </H264Video>
        <AACAudio>
          <Profile>AACLC</Profile>
          <Channels>2</Channels>
          <SamplingRate>48000</SamplingRate>
          <Bitrate>128</Bitrate>
        </AACAudio>
        <JpgImage Start="{Best}">
          <JpgLayers>
            <JpgLayer>
              <Width>100%</Width>
              <Height>100%/Height>
              <Quality>90</Quality>
            </JpgLayer>
          </JpgLayers>
        </JpgImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Resolution}_{VideoBitrate}.mp4">
          <MP4Format />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <JpgFormat />
        </Output>
      </Outputs>
    </Preset>   
```

## <a name="encode-video-and-generate-thumbnail-with-net"></a><a id="code_sample"></a>Encode vídeo e gere miniatura com .NET

O exemplo de código que se segue utiliza media services .NET SDK para executar as seguintes tarefas:

* Criar um trabalho de codificação.
* Obtenha uma referência ao codificador Padrão do Codificador De Media.
* Carregue o conjunto de [xml](media-services-dotnet-generate-thumbnail-with-mes.md#xml) ou [JSON](media-services-dotnet-generate-thumbnail-with-mes.md#json) que contenham o predefinido de codificação, bem como as informações necessárias para gerar miniaturas. Pode guardar este [XML](media-services-dotnet-generate-thumbnail-with-mes.md#xml) ou [JSON](media-services-dotnet-generate-thumbnail-with-mes.md#json) num ficheiro e utilizar o seguinte código para carregar o ficheiro.
  
        // Load the XML (or JSON) from the local file.
        string configuration = File.ReadAllText(fileName);  
* Adicione uma única tarefa de codificação ao trabalho. 
* Especifique o ativo de entrada a codificar.
* Crie um ativo de saída que contenha o ativo codificado.
* Adicione um manipulador de eventos para verificar o progresso do trabalho.
* Submeta o trabalho.

Consulte o desenvolvimento dos [Serviços de Media com o](media-services-dotnet-how-to-use.md) artigo .NET para obter instruções sobre como configurar o seu ambiente de dev.

```csharp
using System;
using System.Configuration;
using System.IO;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Threading;

namespace EncodeAndGenerateThumbnails
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

        private static CloudMediaContext _context = null;

        private static readonly string _mediaFiles =
        Path.GetFullPath(@"../..\Media");

        private static readonly string _singleMP4File =
            Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            // Get an uploaded asset.
            var asset = _context.Assets.FirstOrDefault();

            // Encode and generate the thumbnails.
            EncodeToAdaptiveBitrateMP4Set(asset);

            Console.ReadLine();
        }

        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("Media Encoder Standard Thumbnail Job");
            // Get a media processor reference, and pass to it the name of the 
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

            // Load the XML (or JSON) from the local file.
            string configuration = File.ReadAllText("ThumbnailPreset_JSON.json");

            // Create a task
            ITask task = job.Tasks.AddNew("Media Encoder Standard Thumbnail task",
                    processor,
                    configuration,
                    TaskOptions.None);

            // Specify the input asset to be encoded.
            task.InputAssets.Add(asset);
            // Add an output asset to contain the results of the job. 
            // This output is specified as AssetCreationOptions.None, which 
            // means the output asset is not encrypted. 
            task.OutputAssets.AddNew("Output asset",
                    AssetCreationOptions.None);

            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
            job.Submit();
            job.GetExecutionProgressTask(CancellationToken.None).Wait();

            return job.OutputMediaAssets[0];
        }

        private static void JobStateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine("Job state changed event:");
            Console.WriteLine("  Previous state: " + e.PreviousState);
            Console.WriteLine("  Current state: " + e.CurrentState);
            switch (e.CurrentState)
            {
                case JobState.Finished:
                    Console.WriteLine();
                    Console.WriteLine("Job is finished. Please wait while local tasks or downloads complete...");
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
                    break;
                default:
                    break;
            }
        }

        private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
        {
            var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
            ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

            if (processor == null)
                throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

            return processor;
        }
    }
}
```

## <a name="considerations"></a>Considerações
As seguintes considerações são aplicáveis:

* A utilização de carimbos de tempo explícitos para Iniciar/Passo/Alcance pressupõe que a fonte de entrada tem pelo menos 1 minuto de duração.
* Os elementos jpg/Png/BmpImage têm atributos de cadeia Start, Step e Range – estes podem ser interpretados como:
  
  * Número do quadro se forem inteiros não negativos, por exemplo "Iniciar": "120",
  * Relativamente à duração da fonte se expresso como %-sufixo, por exemplo "Iniciar": "15%", OU
  * Carimbo de tempo se expresso como HH:MM:SS... formato. Por exemplo, "Iniciar" : "00:01:00"
    
    Pode misturar e combinar notações como quiser.
    
    Além disso, o Start também suporta um Macro especial:{Best}, que tenta determinar o primeiro quadro "interessante" do conteúdo NOTA: (Passo e Alcance são ignorados quando o Início está definido para {Best})
  * Predefinições: Início:{Best}
* O formato de saída tem de ser explicitamente fornecido para cada formato de Imagem: Jpg/Png/BmpFormat. Quando presente, o MES corresponde ao JpgVideo ao JpgFormat e assim por diante. OutputFormat introduz um novo Macro específico do código de imagem: {Index}, que precisa estar presente (uma vez e apenas uma vez) para formatos de saída de imagem.

## <a name="next-steps"></a>Passos seguintes

Pode verificar o progresso do [trabalho](media-services-check-job-progress.md) enquanto o trabalho de codificação está pendente.

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Veja também
[Visão geral de codificação de serviços de mídia](media-services-encode-asset.md)

