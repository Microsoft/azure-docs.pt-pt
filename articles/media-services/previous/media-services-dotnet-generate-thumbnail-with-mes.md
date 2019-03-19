---
title: Como gerar miniaturas com o Media Encoder Standard com .NET
description: Este tópico mostra como utilizar o .NET para codificar um recurso e gerar miniaturas em simultâneo com o Media Encoder Standard.
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
ms.date: 02/09/2019
ms.author: juliako
ms.openlocfilehash: e557f7f615a53bc7ed27849e9ea1721328c1afee
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57834609"
---
# <a name="how-to-generate-thumbnails-using-media-encoder-standard-with-net"></a>Como gerar miniaturas com o Media Encoder Standard com .NET 

Pode utilizar o Media Encoder Standard para gerar miniaturas de um ou mais a partir do seu vídeo de entrada [JPEG](https://en.wikipedia.org/wiki/JPEG), [PNG](https://en.wikipedia.org/wiki/Portable_Network_Graphics), ou [BMP](https://en.wikipedia.org/wiki/BMP_file_format) formatos de ficheiro de imagem. Pode submeter tarefas que produzem apenas imagens ou pode combinar a geração de miniaturas com codificação. Este artigo fornece algumas exemplo XML e JSON em miniatura configurações predefinidas para estes cenários. No final do artigo, há uma [código de exemplo](#code_sample) que mostra como utilizar o SDK de .NET de serviços de multimédia para realizar a tarefa de codificação.

Para obter mais detalhes sobre os elementos que são usados em suas configurações predefinidas de exemplo, deve rever [esquema Media Encoder Standard](media-services-mes-schema.md).

Lembre-se de que reveja os [considerações](media-services-dotnet-generate-thumbnail-with-mes.md#considerations) secção.
    
## <a name="example-of-a-single-png-file-preset"></a>Exemplo de uma configuração predefinida de "ficheiro PNG único"

A configuração predefinida seguinte de JSON e XML pode ser utilizada para produzir um único PNG ficheiro de saída do primeiro alguns segundos da entrada de vídeo, em que o codificador faz uma tentativa de melhor esforço em encontrar um quadro "interessante". Tenha em atenção que as dimensões da imagem de saída foram definidas para 100%, o que significa que correspondam às dimensões do vídeo de entrada. Observe também como a definição de "Formato" no "Saídas" é necessária para corresponder a utilização de "PngLayers" na secção "Codecs". 

### <a name="json-preset"></a>Configuração predefinida JSON

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
    
### <a name="xml-preset"></a>Configuração predefinida XML

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

## <a name="example-of-a-series-of-jpeg-images-preset"></a>Exemplo de uma configuração predefinida de "série de imagens JPEG"

A configuração predefinida seguinte de JSON e XML pode ser utilizada para gerar um conjunto de 10 imagens em carimbos de 5%, 15%,..., 95% da linha cronológica de entrada, em que o tamanho da imagem é especificado um trimestre que o vídeo de entrada.

### <a name="json-preset"></a>Configuração predefinida JSON

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

### <a name="xml-preset"></a>Configuração predefinida XML
    
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

## <a name="example-of-a-one-image-at-a-specific-timestamp-preset"></a>Exemplo de uma configuração predefinida de "uma imagem num carimbo específico"

A configuração predefinida seguinte de JSON e XML pode ser utilizada para produzir uma única imagem JPEG na marca de 30 segundos de vídeo de entrada. Esta configuração predefinida de espera que o vídeo de entrada para ser mais de 30 segundos de duração (caso contrário falha a tarefa).

### <a name="json-preset"></a>Configuração predefinida JSON

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

### <a name="xml-preset"></a>Configuração predefinida XML
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

## <a name="example-of-a-thumbnails-at-different-resolutions-preset"></a>Exemplo de uma configuração predefinida de "miniaturas em resoluções diferentes"

A configuração predefinida seguinte pode ser utilizada para gerar miniaturas em resoluções diferentes numa tarefa. No exemplo, a % de 5 de posições, 15%,..., 95% da linha cronológica de entrada, o codificador gera duas imagens – um de cada 100% de resolução de vídeo de entrada e o outro a 50%.

Observe o uso de macro {resolução} no nome de ficheiro; ele indica ao codificador para utilizar a largura e altura que especificou na secção de codificação da configuração predefinida ao gerar o nome de ficheiro das imagens de saída. Isto também ajuda a fazer a distinção entre as diferentes imagens facilmente

### <a name="json-preset"></a>Configuração predefinida JSON

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

### <a name="xml-preset"></a>Configuração predefinida XML
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

## <a name="example-of-generating-a-thumbnail-while-encoding"></a>Exemplo de gerar uma miniatura da codificação

Embora todos os exemplos acima foram abordados como pode submeter uma tarefa de codificação produz apenas imagens, também pode combinar a codificação de vídeo/áudio com geração de miniaturas. A configuração predefinida seguinte de JSON e XML dizer **Media Encoder Standard** para gerar uma miniatura durante a codificação.

### <a id="json"></a>Configuração predefinida JSON
Para obter informações sobre o esquema, consulte [isso](https://msdn.microsoft.com/library/mt269962.aspx) artigo.

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

### <a id="xml"></a>Configuração predefinida XML
Para obter informações sobre o esquema, consulte [isso](https://msdn.microsoft.com/library/mt269962.aspx) artigo.

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

## <a id="code_sample"></a>Codificar o vídeo e gerar a miniatura com .NET

O exemplo de código seguinte utiliza o SDK .NET dos Media Services para efetuar as seguintes tarefas:

* Crie uma tarefa de codificação.
* Obter uma referência ao codificador Media Encoder Standard.
* Carregar a configuração predefinida [XML](media-services-dotnet-generate-thumbnail-with-mes.md#xml) ou [JSON](media-services-dotnet-generate-thumbnail-with-mes.md#json) que contêm a configuração predefinida, bem como as informações necessárias para gerar miniaturas de codificação. Pode salvar isso [XML](media-services-dotnet-generate-thumbnail-with-mes.md#xml) ou [JSON](media-services-dotnet-generate-thumbnail-with-mes.md#json) num arquivo e utilize o seguinte código ao carregar o ficheiro.
  
        // Load the XML (or JSON) from the local file.
        string configuration = File.ReadAllText(fileName);  
* Adicione uma única tarefa de codificação para a tarefa. 
* Especifique o elemento de entrada estar codificado.
* Crie um elemento de saída que contém o elemento codificado.
* Adicione um manipulador de eventos para verificar o progresso da tarefa.
* Submeta a tarefa.

Consulte a [desenvolvimento de serviços de multimédia com .NET](media-services-dotnet-how-to-use.md) artigo para obter instruções sobre como configurar o ambiente de desenvolvimento.

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
As seguintes considerações aplicam-se:

* A utilização de carimbos de data / explícita para iniciar/passo/intervalo parte do princípio de que a origem de entrada é longo, pelo menos, 1 minuto.
* Elementos de jpg/Png/BmpImage tem início, passo e os atributos de cadeia de caracteres de intervalo – estas podem ser interpretadas como:
  
  * Número de quadros se forem inteiros não negativos, por exemplo, "Start": "120",
  * Caminho relativo para a duração da origem se for expresso com o sufixo %, por exemplo "Start": "15%", OU
  * Timestamp se for expresso no hh: mm:... formato. Por exemplo "começar:" "00:01:00"
    
    Pode combinar e misturar notações como.
    
    Além disso, o início suporta também uma Macro especial: {melhores}, que tentará determinar o primeiro quadro "interessante" da nota conteúdo: (Passo e o intervalo são ignoradas quando início está definido como {melhor})
  * Predefinições: Iniciar: {melhor}
* Formato de saída tem de ser explicitamente fornecido para cada formato de imagem: Jpg/Png/BmpFormat. Quando estiver presente, o MES corresponde a JpgVideo para JpgFormat e assim por diante. OutputFormat introduz uma nova Macro específicos de codec de imagem: {Index}, que tem de estar presente (uma vez e apenas uma vez) para os formatos de saída de imagem.

## <a name="next-steps"></a>Passos Seguintes

Pode verificar o [progresso da tarefa](media-services-check-job-progress.md) enquanto a tarefa de codificação está pendente.

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Consultar Também
[Descrição geral da codificação de serviços de multimédia](media-services-encode-asset.md)

