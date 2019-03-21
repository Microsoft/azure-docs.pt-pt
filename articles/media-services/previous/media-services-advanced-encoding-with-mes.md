---
title: Realizar a codificação avançada ao personalizar predefinições MES | Documentos da Microsoft
description: Este tópico mostra como realizar a codificação avançada ao personalizar o Media Encoder Standard predefinições de tarefas.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 2a4ade25-e600-4bce-a66e-e29cf4a38369
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: aa29b1e2d0e80b3134be69d17ed5be454f8a51c3
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57850365"
---
# <a name="perform-advanced-encoding-by-customizing-mes-presets"></a>Realizar a codificação avançada ao personalizar predefinições MES 

## <a name="overview"></a>Descrição geral

Este tópico mostra como personalizar as predefinições do Media Encoder Standard. O [Encoding com Media Encoder Standard com configurações predefinidas personalizadas](media-services-custom-mes-presets-with-dotnet.md) tópico mostra como usar o .NET para criar uma tarefa de codificação e uma tarefa que executa essa tarefa. Depois de personalizar uma configuração predefinida, fornece as configurações predefinidas personalizadas para a tarefa de codificação. 

>[!NOTE]
>Se utilizar uma configuração predefinida XML, certifique-se preservar a ordem dos elementos, conforme mostrado nos exemplos XML abaixo (por exemplo, KeyFrameInterval deve preceda SceneChangeDetection).
>

Neste tópico, as configurações predefinidas personalizadas que executam as seguintes tarefas de codificação são demonstradas.

## <a name="support-for-relative-sizes"></a>Suporte para tamanhos relativos

Ao gerar miniaturas, não é necessário especificar sempre saída largura e altura em pixels. Pode especificá-las em porcentagens, no intervalo [% 1,..., 100%].

### <a name="json-preset"></a>Configuração predefinida JSON
    "Width": "100%",
    "Height": "100%"

### <a name="xml-preset"></a>Configuração predefinida XML
    <Width>100%</Width>
    <Height>100%</Height>

## <a id="thumbnails"></a>Gerar miniaturas

Esta secção mostra como personalizar uma predefinição que gera miniaturas. A configuração predefinida de definido abaixo contém informações sobre como pretende codificar o ficheiro, bem como informações necessárias para gerar miniaturas. Pode pegar qualquer as predefinições MES documentadas [isso](media-services-mes-presets-overview.md) secção e adicione código que gera miniaturas.  

> [!NOTE]
> O **SceneChangeDetection** definição na configuração predefinida seguinte só pode ser definida como VERDADEIRO se está a codificar para uma velocidade de transmissão única vídeo. Se está a codificar a uma velocidade de transmissão vídeo e um conjunto **SceneChangeDetection** para verdadeiro, o codificador devolve um erro.  
>
>

Para obter informações sobre o esquema, consulte [isso](media-services-mes-schema.md) tópico.

Lembre-se de que reveja os [considerações](#considerations) secção.

### <a id="json"></a>Configuração predefinida JSON
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
              "Width": 640,
              "Height": 360
            }
          ],
          "Start": "{Best}",
          "Type": "JpgImage"
        },
        {
          "PngLayers": [
            {
              "Type": "PngLayer",
              "Width": 640,
              "Height": 360,
            }
          ],
          "Start": "00:00:01",
          "Step": "00:00:10",
          "Range": "00:00:58",
          "Type": "PngImage"
        },
        {
          "BmpLayers": [
            {
              "Type": "BmpLayer",
              "Width": 640,
              "Height": 360
            }
          ],
          "Start": "10%",
          "Step": "10%",
          "Range": "90%",
          "Type": "BmpImage"
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
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "PngFormat"
          }
        },
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "BmpFormat"
          }
        },
        {
          "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }


### <a id="xml"></a>Configuração predefinida XML
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
              <Width>640</Width>
              <Height>360</Height>
              <Quality>90</Quality>
            </JpgLayer>
          </JpgLayers>
        </JpgImage>
        <BmpImage Start="10%" Step="10%" Range="90%">
          <BmpLayers>
            <BmpLayer>
              <Width>640</Width>
              <Height>360</Height>
            </BmpLayer>
          </BmpLayers>
        </BmpImage>
        <PngImage Start="00:00:01" Step="00:00:10" Range="00:00:58">
          <PngLayers>
            <PngLayer>
              <Width>640</Width>
              <Height>360</Height>
            </PngLayer>
          </PngLayers>
        </PngImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">
          <MP4Format />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <JpgFormat />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <BmpFormat />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <PngFormat />
        </Output>
      </Outputs>
    </Preset>

### <a name="considerations"></a>Considerações

As seguintes considerações aplicam-se:

* A utilização de carimbos de data / explícita para iniciar/passo/intervalo parte do princípio de que a origem de entrada é longo, pelo menos, 1 minuto.
* Elementos de jpg/Png/BmpImage tem início, passo e os atributos de cadeia de caracteres de intervalo – estas podem ser interpretadas como:

  * Número de quadros se forem inteiros não negativos, por exemplo, "Start": "120",
  * Caminho relativo para a duração da origem se for expresso com o sufixo %, por exemplo "Start": "15%", OU
  * Timestamp se for expresso no hh: mm:... formato, por exemplo, "Start": "00:01:00"

    Pode combinar e misturar notações como.

    Além disso, o início suporta também uma Macro especial: {melhores}, que tentará determinar o primeiro quadro "interessante" da nota conteúdo: (Passo e o intervalo são ignoradas quando início está definido como {melhor})
  * Predefinições: Iniciar: {melhor}
* Formato de saída tem de ser explicitamente fornecido para cada formato de imagem: Jpg/Png/BmpFormat. Quando estiver presente, o MES corresponde a JpgVideo para JpgFormat e assim por diante. OutputFormat introduz uma nova Macro específicos de codec de imagem: {Index}, que tem de estar presente (uma vez e apenas uma vez) para os formatos de saída de imagem.

## <a id="trim_video"></a>Cortar um vídeo (recorte)
Esta seção fala sobre como modificar as predefinições de codificador para recortar ou cortar o vídeo de entrada em que a entrada é um ficheiro de mezanino chamado ou ficheiro sob demanda. O codificador também pode ser utilizado para recortar ou cortar um ativo, o que é capturado ou arquivado num fluxo em direto – os detalhes para isso estão disponíveis na [este blog](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/).

Para cortar os seus vídeos, pode pegar qualquer das predefinições MES documentadas [isso](media-services-mes-presets-overview.md) secção e modificar a **origens** elemento (conforme mostrado abaixo). O valor de StartTime tem de corresponder os carimbos de data / absoluto de vídeo de entrada. Por exemplo, se o primeiro quadro de vídeo de entrada tem um carimbo de 12:00:10.000, em seguida, StartTime deve ser, pelo menos, 12:00:10.000 e superior. No exemplo a seguir, partimos do princípio de que o vídeo de entrada tem um carimbo inicial igual a zero. **Origens** devem ser colocadas no início da configuração predefinida.

### <a id="json"></a>Configuração predefinida JSON
    {
      "Version": 1.0,
      "Sources": [
        {
          "StartTime": "00:00:04",
          "Duration": "00:00:16"
        }
      ],
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "StretchMode": "AutoSize",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 3400,
              "MaxBitrate": 3400,
              "BufferWindow": "00:00:05",
              "Width": 1280,
              "Height": 720,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 2250,
              "MaxBitrate": 2250,
              "BufferWindow": "00:00:05",
              "Width": 960,
              "Height": 540,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 1500,
              "MaxBitrate": 1500,
              "BufferWindow": "00:00:05",
              "Width": 960,
              "Height": 540,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 1000,
              "MaxBitrate": 1000,
              "BufferWindow": "00:00:05",
              "Width": 640,
              "Height": 360,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 650,
              "MaxBitrate": 650,
              "BufferWindow": "00:00:05",
              "Width": 640,
              "Height": 360,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 400,
              "MaxBitrate": 400,
              "BufferWindow": "00:00:05",
              "Width": 320,
              "Height": 180,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            }
          ],
          "Type": "H264Video"
        },
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }

### <a name="xml-preset"></a>Configuração predefinida XML
Para cortar os seus vídeos, pode pegar qualquer das predefinições MES documentadas [aqui](media-services-mes-presets-overview.md) e modificar a **origens** elemento (conforme mostrado abaixo).

    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Sources>
        <Source StartTime="PT4S" Duration="PT14S"/>
      </Sources>
      <Encoding>
        <H264Video>
          <KeyFrameInterval>00:00:02</KeyFrameInterval>
          <H264Layers>
            <H264Layer>
              <Bitrate>3400</Bitrate>
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
              <MaxBitrate>3400</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>2250</Bitrate>
              <Width>960</Width>
              <Height>540</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>2250</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>1500</Bitrate>
              <Width>960</Width>
              <Height>540</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>1500</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>1000</Bitrate>
              <Width>640</Width>
              <Height>360</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>1000</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>650</Bitrate>
              <Width>640</Width>
              <Height>360</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>650</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>400</Bitrate>
              <Width>320</Width>
              <Height>180</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>400</MaxBitrate>
            </H264Layer>
          </H264Layers>
        </H264Video>
        <AACAudio>
          <Profile>AACLC</Profile>
          <Channels>2</Channels>
          <SamplingRate>48000</SamplingRate>
          <Bitrate>128</Bitrate>
        </AACAudio>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">
          <MP4Format />
        </Output>
      </Outputs>
    </Preset>

## <a id="overlay"></a>Criar uma sobreposição

O Media Encoder Standard permite-lhe sobrepor uma imagem para um vídeo existente. Atualmente, são suportados os seguintes formatos: png, jpg, gif e bmp. A configuração predefinida de definido abaixo é um exemplo básico de uma sobreposição de vídeo.

Além de definir um ficheiro predefinido, também tem de permitir que os serviços de multimédia sabe qual arquivo no elemento é a imagem de sobreposição e qual é o ficheiro é a origem de vídeo em que deseja sobrepor a imagem. O ficheiro de vídeo tem de ser o **primário** ficheiro.

Se estiver a utilizar o .NET, adicione as seguintes duas funções para o exemplo de .NET definido no [isso](media-services-custom-mes-presets-with-dotnet.md#encoding_with_dotnet) tópico. O **UploadMediaFilesFromFolder** função carrega ficheiros a partir de uma pasta (por exemplo, BigBuckBunny.mp4 e Image001.png) e define o ficheiro mp4 se o ficheiro primário no ativo. O **EncodeWithOverlay** função usa o arquivo predefinido personalizado que foi passado para ele (por exemplo, a configuração predefinida que se segue) para criar a tarefa de codificação.


    static public IAsset UploadMediaFilesFromFolder(string folderPath)
    {
        IAsset asset = _context.Assets.CreateFromFolder(folderPath, AssetCreationOptions.None);
    
        foreach (var af in asset.AssetFiles)
        {
            // The following code assumes 
            // you have an input folder with one MP4 and one overlay image file.
            if (af.Name.Contains(".mp4"))
                af.IsPrimary = true;
            else
                af.IsPrimary = false;
    
            af.Update();
        }
    
        return asset;
    }

    static public IAsset EncodeWithOverlay(IAsset assetSource, string customPresetFileName)
    {
        // Declare a new job.
        IJob job = _context.Jobs.Create("Media Encoder Standard Job");
        // Get a media processor reference, and pass to it the name of the 
        // processor to use for the specific task.
        IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

        // Load the XML (or JSON) from the local file.
        string configuration = File.ReadAllText(customPresetFileName);

        // Create a task
        ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
            processor,
            configuration,
            TaskOptions.None);

        // Specify the input assets to be encoded.
        // This asset contains a source file and an overlay file.
        task.InputAssets.Add(assetSource);

        // Add an output asset to contain the results of the job. 
        task.OutputAssets.AddNew("Output asset",
            AssetCreationOptions.None);

        job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
        job.Submit();
        job.GetExecutionProgressTask(CancellationToken.None).Wait();

        return job.OutputMediaAssets[0];
    }


> [!NOTE]
> Limitações atuais:
>
> A definição de opacidade de sobreposição não é suportada.
>
> O ficheiro de vídeo de origem e o arquivo de imagem de sobreposição têm de estar no mesmo elemento, e o ficheiro de vídeo tem de ser definida como o ficheiro primário deste recurso.
>
>

### <a name="json-preset"></a>Configuração predefinida JSON
    {
      "Version": 1.0,
      "Sources": [
        {
          "Streams": [],
          "Filters": {
            "VideoOverlay": {
              "Position": {
                "X": 100,
                "Y": 100,
                "Width": 100,
                "Height": 50
              },
              "AudioGainLevel": 0.0,
              "MediaParams": [
                {
                  "OverlayLoopCount": 1
                },
                {
                  "IsOverlay": true,
                  "OverlayLoopCount": 1
                }
              ],
              "Source": "Image001.png",
              "Clip": {
                "Duration": "00:00:05"
              },
              "FadeInDuration": {
                "Duration": "00:00:01"
              },
              "FadeOutDuration": {
                "StartTime": "00:00:03",
                "Duration": "00:00:04"
              }
            }
          },
          "Pad": true
        }
      ],
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 1045,
              "MaxBitrate": 1045,
              "BufferWindow": "00:00:05",
              "ReferenceFrames": 3,
              "EntropyMode": "Cavlc",
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "Width": "640",
              "Height": "360",
              "FrameRate": "0/1"
            }
          ],
          "Type": "H264Video"
        },
        {
          "Type": "CopyAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}{Extension}",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }


### <a name="xml-preset"></a>Configuração predefinida XML
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Sources>
        <Source>
          <Streams />
          <Filters>
            <VideoOverlay>
              <Source>Image001.png</Source>
              <Clip Duration="PT5S" />
              <FadeInDuration Duration="PT1S" />
              <FadeOutDuration StartTime="PT3S" Duration="PT4S" />
              <Position X="100" Y="100" Width="100" Height="50" />
              <Opacity>0</Opacity>
              <AudioGainLevel>0</AudioGainLevel>
              <MediaParams>
                <MediaParam>
                  <IsOverlay>false</IsOverlay>
                  <OverlayLoopCount>1</OverlayLoopCount>
                </MediaParam>
                <MediaParam>
                  <IsOverlay>true</IsOverlay>
                  <OverlayLoopCount>1</OverlayLoopCount>
                </MediaParam>
              </MediaParams>
            </VideoOverlay>
          </Filters>
          <Pad>true</Pad>
        </Source>
      </Sources>
      <Encoding>
        <H264Video>
          <KeyFrameInterval>00:00:02</KeyFrameInterval>
          <H264Layers>
            <H264Layer>
              <Bitrate>1045</Bitrate>
              <Width>640</Width>
              <Height>360</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>0</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cavlc</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>1045</MaxBitrate>
            </H264Layer>
          </H264Layers>
        </H264Video>
        <CopyAudio />
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}{Extension}">
          <MP4Format />
        </Output>
      </Outputs>
    </Preset>


## <a id="silent_audio"></a>Inserir uma faixa de áudio silenciosa quando a entrada tem sem áudio
Por predefinição, se tiver de enviar uma entrada ao codificador que contém apenas vídeo e áudio de nenhum, em seguida, o elemento de saída contém ficheiros que contêm dados apenas de vídeos. Alguns leitores podem não ser capazes de lidar com esses fluxos de saída. Pode utilizar esta definição para forçar o codificador para adicionar uma faixa de áudio automática para a saída desse cenário.

Para forçar o codificador para produzir um elemento que contenha uma faixa de áudio silenciosa quando a entrada tem sem áudio, especifique o valor de "InsertSilenceIfNoAudio".

Pode pegar qualquer as predefinições MES documentadas em [isso](media-services-mes-presets-overview.md) secção e efetuar a modificação do seguinte:

### <a name="json-preset"></a>Configuração predefinida JSON
    {
      "Channels": 2,
      "SamplingRate": 44100,
      "Bitrate": 96,
      "Type": "AACAudio",
      "Condition": "InsertSilenceIfNoAudio"
    }

### <a name="xml-preset"></a>Configuração predefinida XML
    <AACAudio Condition="InsertSilenceIfNoAudio">
      <Channels>2</Channels>
      <SamplingRate>44100</SamplingRate>
      <Bitrate>96</Bitrate>
    </AACAudio>

## <a id="deinterlacing"></a>Desativar automaticamente anular do entrelaçamento
Os clientes não precisam de fazer nada se desejarem o conteúdo de interlace sejam entrelaçadas automaticamente anular. Quando o automática anular entrelaçamento está ativada (predefinição) o MES faz a deteção automática de quadros entrelaçadas e apenas interlaces anular quadros marcados como entrelaçadas.

Pode desativar o automática anular entrelaçamento. Esta opção não é recomendada.

### <a name="json-preset"></a>Configuração predefinida JSON
    "Sources": [
    {
     "Filters": {
        "Deinterlace": {
          "Mode": "Off"
        }
      },
    }
    ]

### <a name="xml-preset"></a>Configuração predefinida XML
    <Sources>
    <Source>
      <Filters>
        <Deinterlace>
          <Mode>Off</Mode>
        </Deinterlace>
      </Filters>
    </Source>
    </Sources>


## <a id="audio_only"></a>Configurações predefinidas de só de áudio
Esta secção demonstra duas configurações predefinidas MES de só de áudio: Áudio AAC e AAC áudio de boa qualidade.

### <a name="aac-audio"></a>Áudio AAC
    {
      "Version": 1.0,
      "Codecs": [
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_AAC_{AudioBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }

### <a name="aac-good-quality-audio"></a>Áudio de boa qualidade AAC
    {
      "Version": 1.0,
      "Codecs": [
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 192,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_AAC_{AudioBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }

## <a id="concatenate"></a>Concatenar dois ou mais ficheiros de vídeo

O exemplo a seguir ilustra como pode gerar uma configuração predefinida para concatenar duas ou mais ficheiros de vídeo. O cenário mais comum é quando deseja adicionar um cabeçalho ou uma trailers ao vídeo principal. A utilização pretendida é quando propriedades (resolução de vídeo, taxa de quadros, contagem de faixa de áudio, etc.) da partilha de ficheiros de vídeo a ser editados em conjunto. Deve Tome cuidado para não misturar vídeos de taxas de quadros diferentes, ou com um número diferente de faixas de áudio.

>[!NOTE]
>O design atual da funcionalidade de concatenação de espera que os clips de vídeo de entrada são consistentes em termos de resolução, taxa de quadros etc. 

### <a name="requirements-and-considerations"></a>Requisitos e considerações

* Vídeos de entrada devem ter apenas uma faixa de áudio.
* Vídeos de entrada devem ter a mesma taxa de quadros.
* Tem de carregar os seus vídeos para recursos separados e definir os vídeos que o ficheiro primário em cada ativo.
* Precisa saber a duração dos seus vídeos.
* Os exemplos predefinidos abaixo parte do princípio de que todos os vídeos de entrada começar com um carimbo de zero. Terá de modificar os valores StartTime se os vídeos tem timestamp partida diferente, como é normalmente o caso com arquivos em direto.
* A configuração predefinida de JSON torna referências explícitas para os valores de AssetID os ativos de entrada.
* O código de exemplo parte do princípio de que a configuração predefinida de JSON tiver sido salva um arquivo local, como "C:\supportFiles\preset.json". Também parte do princípio de que dois recursos foram criados através do carregamento de ficheiros de vídeo de dois, e de que sabe os valores de AssetID resultantes.
* O trecho de código e JSON predefinido mostra um exemplo de concatenar dois ficheiros de vídeo. Pode expandi-lo a mais do que dois vídeos por:

  1. Tarefa de chamada. InputAssets.Add() repetidamente para adicionar mais vídeos, por ordem.
  2. Tornar correspondente edita para o elemento "Origens" em JSON, adicionando mais entradas, pela mesma ordem.

### <a name="net-code"></a>Código do .NET

    IAsset asset1 = _context.Assets.Where(asset => asset.Id == "nb:cid:UUID:606db602-efd7-4436-97b4-c0b867ba195b").FirstOrDefault();
    IAsset asset2 = _context.Assets.Where(asset => asset.Id == "nb:cid:UUID:a7e2b90f-0565-4a94-87fe-0a9fa07b9c7e").FirstOrDefault();

    // Declare a new job.
    IJob job = _context.Jobs.Create("Media Encoder Standard Job for Concatenating Videos");
    // Get a media processor reference, and pass to it the name of the
    // processor to use for the specific task.
    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

    // Load the XML (or JSON) from the local file.
    string configuration = File.ReadAllText(@"c:\supportFiles\preset.json");

    // Create a task
    ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
        processor,
        configuration,
        TaskOptions.None);

    // Specify the input videos to be concatenated (in order).
    task.InputAssets.Add(asset1);
    task.InputAssets.Add(asset2);
    // Add an output asset to contain the results of the job.
    // This output is specified as AssetCreationOptions.None, which
    // means the output asset is not encrypted.
    task.OutputAssets.AddNew("Output asset",
        AssetCreationOptions.None);

    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
    job.Submit();
    job.GetExecutionProgressTask(CancellationToken.None).Wait();

### <a name="json-preset"></a>Configuração predefinida JSON

Atualize seu personalizado predefinido com ids dos ativos que deseja concatenar e com o segmento de hora apropriada para cada vídeo.

    {
      "Version": 1.0,
      "Sources": [
        {
          "AssetID": "606db602-efd7-4436-97b4-c0b867ba195b",
          "StartTime": "00:00:01",
          "Duration": "00:00:15"
        },
        {
          "AssetID": "a7e2b90f-0565-4a94-87fe-0a9fa07b9c7e",
          "StartTime": "00:00:02",
          "Duration": "00:00:05"
        }
      ],
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "SceneChangeDetection": true,
          "H264Layers": [
            {
              "Level": "auto",
              "Bitrate": 1800,
              "MaxBitrate": 1800,
              "BufferWindow": "00:00:05",
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "Width": "640",
              "Height": "360",
              "FrameRate": "0/1"
            }
          ],
          "Type": "H264Video"
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
          "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }

## <a id="crop"></a>Recortar vídeos com o Media Encoder Standard
Consulte a [recortar vídeos com o Media Encoder Standard](media-services-crop-video.md) tópico.

## <a id="no_video"></a>Inserir um Roteiro de vídeo quando a entrada não tem nenhum vídeo

Por predefinição, se tiver de enviar uma entrada ao codificador que contenha apenas áudio e nenhum vídeo, em seguida, o elemento de saída contém ficheiros que contêm dados de áudio apenas. Alguns leitores, incluindo o leitor de multimédia do Azure (veja [isso](https://feedback.azure.com/forums/169396-azure-media-services/suggestions/8082468-audio-only-scenarios)) pode não ser capaz de lidar com tais fluxos. Pode utilizar esta definição para forçar o codificador para adicionar uma faixa de vídeo monocrômica à saída nesse cenário.

> [!NOTE]
> Forçar o codificador para inserir um track de vídeo de saída aumenta o tamanho da saída ativo, e, deste modo, o custo incorrido para a tarefa de codificação. Deve executar testes para verificar se este aumento resultante tem apenas um impacto modesto em suas Cobranças mensais.
>

### <a name="inserting-video-at-only-the-lowest-bitrate"></a>A inserção de vídeo no apenas a velocidade de transmissão mais baixa

Suponha que estiver a utilizar uma velocidade de transmissão múltiplas codificação predefinidos, tais como ["h264 taxa de bits múltiplas 720p"](media-services-mes-preset-h264-multiple-bitrate-720p.md) codificar sua entrado catálogo completo para transmissão em fluxo, que contém uma mistura de ficheiros de vídeo e ficheiros só de áudio. Neste cenário, quando a entrada não tem nenhum vídeo, pode querer forçar o codificador para inserir um Roteiro de vídeo monocrômica em apenas a mais baixa velocidade de transmissão, em vez de inserção de vídeo no cada velocidade de transmissão de saída. Para conseguir isso, tem de utilizar o **InsertBlackIfNoVideoBottomLayerOnly** sinalizador.

Pode pegar qualquer as predefinições MES documentadas em [isso](media-services-mes-presets-overview.md) secção e efetuar a modificação do seguinte:

#### <a name="json-preset"></a>Configuração predefinida JSON
    {
          "KeyFrameInterval": "00:00:02",
          "StretchMode": "AutoSize",
          "Condition": "InsertBlackIfNoVideoBottomLayerOnly",
          "H264Layers": [
          …
          ]
    }

#### <a name="xml-preset"></a>Configuração predefinida XML

Ao utilizar o XML, use a condição = "InsertBlackIfNoVideoBottomLayerOnly" como um atributo para o **H264Video** elemento e a condição = "InsertSilenceIfNoAudio" como um atributo para **AACAudio**.

```
. . .
<Encoding>
  <H264Video Condition="InsertBlackIfNoVideoBottomLayerOnly">
    <KeyFrameInterval>00:00:02</KeyFrameInterval>
    <SceneChangeDetection>true</SceneChangeDetection>
    <StretchMode>AutoSize</StretchMode>
    <H264Layers>
      <H264Layer>
        . . .
      </H264Layer>
    </H264Layers>
    <Chapters />
  </H264Video>
  <AACAudio Condition="InsertSilenceIfNoAudio">
    <Profile>AACLC</Profile>
    <Channels>2</Channels>
    <SamplingRate>48000</SamplingRate>
    <Bitrate>128</Bitrate>
  </AACAudio>
</Encoding>
. . .
```

### <a name="inserting-video-at-all-output-bitrates"></a>Inserir o vídeo em todos os velocidades de transmissão de saída
Suponha que estiver a utilizar uma velocidade de transmissão múltiplas codificação predefinidos, tais como ["h264 taxa de bits múltiplas 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) codificar sua entrado catálogo completo para transmissão em fluxo, que contém uma mistura de ficheiros de vídeo e ficheiros só de áudio. Neste cenário, quando a entrada não tem nenhum vídeo, pode querer forçar o codificador para inserir um Roteiro de vídeo monocrômica em todos os as velocidades de transmissão de saída. Isto garante que a saída ativos são todos homogéneo em relação ao número de faixas de vídeo e faixas de áudio. Para conseguir isso, terá de especificar o sinalizador "InsertBlackIfNoVideo".

Pode pegar qualquer as predefinições MES documentadas em [isso](media-services-mes-presets-overview.md) secção e efetuar a modificação do seguinte:

#### <a name="json-preset"></a>Configuração predefinida JSON
    {
          "KeyFrameInterval": "00:00:02",
          "StretchMode": "AutoSize",
          "Condition": "InsertBlackIfNoVideo",
          "H264Layers": [
          …
          ]
    }

#### <a name="xml-preset"></a>Configuração predefinida XML

Ao utilizar o XML, use a condição = "InsertBlackIfNoVideo" como um atributo para o **H264Video** elemento e a condição = "InsertSilenceIfNoAudio" como um atributo para **AACAudio**.

```
. . .
<Encoding>
  <H264Video Condition="InsertBlackIfNoVideo">
    <KeyFrameInterval>00:00:02</KeyFrameInterval>
    <SceneChangeDetection>true</SceneChangeDetection>
    <StretchMode>AutoSize</StretchMode>
    <H264Layers>
      <H264Layer>
        . . .
      </H264Layer>
    </H264Layers>
    <Chapters />
  </H264Video>
  <AACAudio Condition="InsertSilenceIfNoAudio">
    <Profile>AACLC</Profile>
    <Channels>2</Channels>
    <SamplingRate>48000</SamplingRate>
    <Bitrate>128</Bitrate>
  </AACAudio>
</Encoding>
. . .  
```

## <a id="rotate_video"></a>Girar um vídeo
O [Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md) suporta a rotação por ângulos de 0/90/180/270. O comportamento padrão é "Auto", onde ele tenta detetar os metadados de rotação no ficheiro de vídeo de entrada e compensar para o mesmo. Incluem o seguinte **origens** uma das predefinições definidas no elemento [isso](media-services-mes-presets-overview.md) secção:

### <a name="json-preset"></a>Configuração predefinida JSON
    "Sources": [
    {
      "Streams": [],
      "Filters": {
        "Rotation": "90"
      }
    }
    ],
    "Codecs": [

    ...
### <a name="xml-preset"></a>Configuração predefinida XML
    <Sources>
           <Source>
          <Streams />
          <Filters>
            <Rotation>90</Rotation>
          </Filters>
        </Source>
    </Sources>

Além disso, veja [isso](media-services-mes-schema.md#PreserveResolutionAfterRotation) tópico para obter mais informações sobre como o codificador interpreta as definições de largura e altura na configuração predefinida, quando for acionada a compensação de rotação.

Pode usar o valor "0" para indicar ao codificador para ignorar os metadados de rotação, se estiver presente, no vídeo de entrada.

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Consultar Também
[Descrição geral da codificação de serviços de multimédia](media-services-encode-asset.md)
