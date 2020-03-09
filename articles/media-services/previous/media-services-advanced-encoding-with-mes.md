---
title: Realizar codificação avançada personalizando predefinições mes / Microsoft Docs
description: Este tópico mostra como realizar codificação avançada personalizando predefinições de tarefa sintetizadas de Media Encoder Standard.
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
ms.openlocfilehash: fadf1aa54f525fb3d4c414161583f8a89f2e4c05
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78385442"
---
# <a name="perform-advanced-encoding-by-customizing-mes-presets"></a>Realizar codificação avançada personalizando predefinições mes 

## <a name="overview"></a>Descrição geral

Este tópico mostra como personalizar as predefinições padrão do Media Encoder Standard. A [Codificação com media Encoder Standard utilizando](media-services-custom-mes-presets-with-dotnet.md) o tópico de predefinições personalizadas mostra como usar .NET para criar uma tarefa de codificação e um trabalho que executa esta tarefa. Assim que personalizar um preset, forneça as predefinições personalizadas à tarefa de codificação. 

Se utilizar um preset XML, certifique-se de preservar a ordem dos elementos, como mostra as amostras XML abaixo (por exemplo, o KeyFrameInterval deve preceder o SceneChangeDetection).

> [!NOTE] 
> Muitas das funcionalidades avançadas dos Media Services v2 da Norma Media Encoder não estão atualmente disponíveis na v3. Para mais informações, consulte [lacunas de recurso.](https://docs.microsoft.com/azure/media-services/latest/migrate-from-v2-to-v3#feature-gaps-with-respect-to-v2-apis)

## <a name="support-for-relative-sizes"></a>Suporte para tamanhos relativos

Ao gerar miniaturas, não é necessário especificar sempre a largura e a altura de saída em pixels. Pode especificá-las em percentagens, no intervalo [1%, ..., 100%].

### <a name="json-preset"></a>Predefinição JSON
    "Width": "100%",
    "Height": "100%"

### <a name="xml-preset"></a>Predefinição XML
    <Width>100%</Width>
    <Height>100%</Height>

## <a id="thumbnails"></a>Gerar miniaturas

Esta secção mostra como personalizar um preset que gera miniaturas. O predefinido abaixo definido contém informações sobre como pretende codificar o seu ficheiro, bem como as informações necessárias para gerar miniaturas. Pode pegar em qualquer uma das predefinições mes documentadas [nesta](media-services-mes-presets-overview.md) secção e adicionar código que gere miniaturas.  

> [!NOTE]
> A definição **de SceneChangeDetection** no preset seguinte só pode ser definida se estiver a codificar para um único vídeo bitrate. Se estiver a codificar um vídeo multibitado e definir o **SceneChangeDetection** como verdadeiro, o codificador devolve um erro.  
>
>

Para obter informações sobre esquemas, consulte [este](media-services-mes-schema.md) tópico.

Certifique-se de rever a secção [Considerações.](#considerations)

### <a id="json"></a>Predefinição JSON
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


### <a id="xml"></a>Predefinição XML
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

Aplicam-se as seguintes considerações:

* A utilização de carimbos de tempo explícitos para Iniciar/Passo/Alcance pressupõe que a fonte de entrada tem pelo menos 1 minuto de duração.
* Os elementos jpg/Png/BmpImage têm atributos de cadeia Start, Step e Range – estes podem ser interpretados como:

  * Número do quadro se forem inteiros não negativos, por exemplo "Iniciar": "120",
  * Relativamente à duração da fonte se expresso como %-sufixo, por exemplo "Iniciar": "15%", OU
  * Carimbo de tempo se expresso como HH:MM:SS... formato, por exemplo "Iniciar": "00:01:00"

    Pode combinar e misturar notações como.

    Além disso, o Start também suporta um Macro especial:{Best}, que tenta determinar o primeiro quadro "interessante" do conteúdo NOTA: (Passo e Alcance são ignorados quando o Início está definido para {Best})
  * Predefinições: Início:{Best}
* O formato de saída tem de ser explicitamente fornecido para cada formato de Imagem: Jpg/Png/BmpFormat. Quando presente, o MES corresponde ao JpgVideo ao JpgFormat e assim por diante. OutputFormat introduz um novo Macro específico do código de imagem: {Index}, que precisa estar presente (uma vez e apenas uma vez) para formatos de saída de imagem.

## <a id="trim_video"></a>Corte um vídeo (recorte)
Esta secção fala em modificar as predefinições do codificador para cortar ou cortar o vídeo de entrada onde a entrada é um ficheiro chamado mezanino ou ficheiro a pedido. O codificador também pode ser usado para cortar ou aparar um ativo, que é capturado ou arquivado a partir de um live stream – os detalhes para isso estão disponíveis [neste blog](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/).

Para aparar os seus vídeos, pode pegar em qualquer uma das predefinições mes documentadas [nesta](media-services-mes-presets-overview.md) secção e modificar o elemento **Fontes** (como mostrado abaixo). O valor do StartTime precisa de corresponder aos selos de tempo absolutos do vídeo de entrada. Por exemplo, se o primeiro quadro do vídeo de entrada tiver um carimbo de tempo de 12:00:10.000, então o StartTime deve ser pelo menos 12:00:10.000 e maior. No exemplo abaixo, assumimos que o vídeo de entrada tem um carimbo de tempo inicial de zero. **As fontes** devem ser colocadas no início do predefinição.

### <a id="json"></a>Predefinição JSON
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

### <a name="xml-preset"></a>Predefinição XML
Para aparar os seus vídeos, pode pegar em qualquer predefinição de MES [aqui](media-services-mes-presets-overview.md) documentada e modificar o elemento **Fontes** (como mostrado abaixo).

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

O Media Encoder Standard permite-lhe sobrepor uma imagem num vídeo existente. Atualmente, são suportados os seguintes formatos: png, jpg, gif e bmp. O predefinido definido abaixo é um exemplo básico de uma sobreposição de vídeo.

Além de definir um ficheiro predefinido, também tem de informar os Media Services sobre qual o ficheiro do ativo é a imagem sobreposição e qual é o vídeo de origem no qual pretende sobrepor a imagem. O ficheiro de vídeo tem de ser o ficheiro **principal.**

Se estiver a utilizar .NET, adicione as seguintes duas funções ao exemplo .NET definido [neste](media-services-custom-mes-presets-with-dotnet.md#encoding_with_dotnet) tópico. A função **UploadMediaFilesFromFolder** envia ficheiros a partir de uma pasta (por exemplo, BigBuckBunny.mp4 e Image001.png) e define o ficheiro MP4 como o ficheiro principal do ativo. A função **EncodeWithOverlay** utiliza o ficheiro predefinido personalizado que lhe foi passado (por exemplo, o predefinido que se segue) para criar a tarefa de codificação.


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
> O cenário de opacidade sobreposta não é suportado.
>
> O ficheiro de vídeo de origem e o ficheiro de imagem sobreposto têm de estar no mesmo ativo, e o ficheiro de vídeo tem de ser definido como o ficheiro principal deste ativo.
>
>

### <a name="json-preset"></a>Predefinição JSON
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


### <a name="xml-preset"></a>Predefinição XML
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


## <a id="silent_audio"></a>Insira uma faixa de áudio silenciosa quando a entrada não tiver áudio
Por predefinição, se enviar uma entrada para o codificador que contém apenas vídeo, e nenhum áudio, então o ativo de saída contém ficheiros que contêm apenas dados de vídeo. Alguns jogadores podem não ser capazes de lidar com estes fluxos de saída. Pode utilizar esta definição para forçar o codificador a adicionar uma faixa de áudio silenciosa à saída nesse cenário.

Para forçar o codificador a produzir um ativo que contenha uma faixa de áudio silenciosa quando a entrada não tiver áudio, especifique o valor "InsertSilenceIfNoAudio".

Pode pegar em qualquer uma das predefinições mes documentadas [nesta](media-services-mes-presets-overview.md) secção e fazer a seguinte modificação:

### <a name="json-preset"></a>Predefinição JSON
    {
      "Channels": 2,
      "SamplingRate": 44100,
      "Bitrate": 96,
      "Type": "AACAudio",
      "Condition": "InsertSilenceIfNoAudio"
    }

### <a name="xml-preset"></a>Predefinição XML
    <AACAudio Condition="InsertSilenceIfNoAudio">
      <Channels>2</Channels>
      <SamplingRate>44100</SamplingRate>
      <Bitrate>96</Bitrate>
    </AACAudio>

## <a id="deinterlacing"></a>Desativar o desbloqueio automático
Os clientes não precisam de fazer nada se gostarem que o conteúdo do interlace seja automaticamente desinterligado. Quando o desamento automático está em (padrão) o MES faz a deteção automática de quadros entrelaçados e apenas desliga os quadros marcados como entrelaçados.

Pode desligar o auto-desatar. Esta opção não é recomendada.

### <a name="json-preset"></a>Predefinição JSON
    "Sources": [
    {
     "Filters": {
        "Deinterlace": {
          "Mode": "Off"
        }
      },
    }
    ]

### <a name="xml-preset"></a>Predefinição XML
    <Sources>
    <Source>
      <Filters>
        <Deinterlace>
          <Mode>Off</Mode>
        </Deinterlace>
      </Filters>
    </Source>
    </Sources>


## <a id="audio_only"></a>Predefinições só de áudio
Esta secção demonstra duas predefinições MES só de áudio: AAC Audio e AAC Good Quality Audio.

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

## <a id="concatenate"></a>Concatenate dois ou mais ficheiros de vídeo

O exemplo que se segue ilustra como pode gerar um preset para concatenar dois ou mais ficheiros de vídeo. O cenário mais comum é quando se quer adicionar um cabeçalho ou um trailer ao vídeo principal. A utilização pretendida é quando os ficheiros de vídeo que são editados em conjunto partilham propriedades (resolução de vídeo, taxa de fotogramas, contagem de faixas de áudio, etc.). Deve ter o cuidado de não misturar vídeos de diferentes taxas de fotogramas, ou com um número diferente de faixas áudio.

>[!NOTE]
>O design atual da funcionalidade de concatenação espera que os videoclips de entrada sejam consistentes em termos de resolução, taxa de fotogramas, etc. 

### <a name="requirements-and-considerations"></a>Requisitos e considerações

* Os vídeos de entrada devem ter apenas uma faixa de áudio.
* Os vídeos de entrada devem ter a mesma taxa de fotogramas.
* Tem de fazer o upload dos seus vídeos em ativos separados e definir os vídeos como ficheiro principal em cada ativo.
* Precisa saber a duração dos seus vídeos.
* Os exemplos predefinidos abaixo pressupõe que todos os vídeos de entrada começam com um carimbo de tempo de zero. É necessário modificar os valores do StartTime se os vídeos tiverem um carimbo de tempo de partida diferente, como é habitual nos arquivos ao vivo.
* O predefinido JSON faz referências explícitas aos valores do AssetID dos ativos de entrada.
* O código da amostra pressupõe que o preset JSON foi guardado num ficheiro local, como "C:\supportFiles\preset.json". Também assume que dois ativos foram criados através do upload de dois ficheiros de vídeo, e que conhece os valores do AssetID resultantes.
* O código de corte e o preconjunto JSON mostram um exemplo de concatenação de dois ficheiros de vídeo. Pode estender a mais de dois vídeos:

  1. Tarefa de chamar. InputAssets.Add() repetidamente para adicionar mais vídeos, por ordem.
  2. Edição correspondente ao elemento "Fontes" no JSON, adicionando mais entradas, na mesma ordem.

### <a name="net-code"></a>Código .NET

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

### <a name="json-preset"></a>Predefinição JSON

Atualize o seu preset personalizado com ids dos ativos que pretende consanificar, e com o segmento de tempo adequado para cada vídeo.

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

## <a id="crop"></a>Vídeos de colheita com Media Encoder Standard
Veja os vídeos da Crop com o tema [Media Encoder Standard.](media-services-crop-video.md)

## <a id="no_video"></a>Insira uma pista de vídeo quando a entrada não tiver vídeo

Por predefinição, se enviar uma entrada para o codificador que contém apenas áudio, e nenhum vídeo, então o ativo de saída contém ficheiros que contêm apenas dados áudio. Alguns jogadores, incluindo o Azure Media Player (ver [isto)](https://feedback.azure.com/forums/169396-azure-media-services/suggestions/8082468-audio-only-scenarios)podem não ser capazes de lidar com tais fluxos. Pode utilizar esta definição para forçar o codificador a adicionar uma pista de vídeo monocromática à saída nesse cenário.

> [!NOTE]
> Forçar o codificador a inserir uma via de vídeo de saída aumenta o tamanho do Ativo de saída e, assim, o custo incorrido para a Tarefa de codificação. Deve fazer testes para verificar se este aumento resultante tem apenas um impacto modesto nas suas tarifas mensais.
>

### <a name="inserting-video-at-only-the-lowest-bitrate"></a>Inserindo o vídeo apenas no bitrate mais baixo

Suponha que está a usar um preconjunto de codificação de várias bitrates, como ["H264 Multiple Bitrate 720p"](media-services-mes-preset-h264-multiple-bitrate-720p.md) para codificar todo o seu catálogo de entrada para streaming, que contém uma mistura de ficheiros de vídeo e ficheiros apenas áudio. Neste cenário, quando a entrada não tem vídeo, pode querer forçar o codificador a inserir uma faixa de vídeo monocromática apenas no bitrate mais baixo, em oposição à inserção de vídeo em cada bitrate de saída. Para tal, tem de utilizar a bandeira **InsertBlackIfNoVideoBottomLayerOnly.**

Pode pegar em qualquer uma das predefinições mes documentadas [nesta](media-services-mes-presets-overview.md) secção e fazer a seguinte modificação:

#### <a name="json-preset"></a>Predefinição JSON
    {
          "KeyFrameInterval": "00:00:02",
          "StretchMode": "AutoSize",
          "Condition": "InsertBlackIfNoVideoBottomLayerOnly",
          "H264Layers": [
          …
          ]
    }

#### <a name="xml-preset"></a>Predefinição XML

Quando utilizar o XML, utilize o Condição="InsertBlackIfNoVideoBottomLayerOnly" como um atributo ao elemento **H264Video** e condição="InsertSilenceIfNoAudio" como atributo ao **AACAudio**.

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

### <a name="inserting-video-at-all-output-bitrates"></a>Inserindo o vídeo em todos os bitrates de saída
Suponha que está a usar um preconjunto de codificação de várias bitrates, como ["H264 Multiple Bitrate 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) para codificar todo o seu catálogo de entrada para streaming, que contém uma mistura de ficheiros de vídeo e ficheiros apenas áudio. Neste cenário, quando a entrada não tem vídeo, é possível forçar o codificador a inserir uma faixa de vídeo monocromática em todos os bitrates de saída. Isto garante que os seus Ativos de saída são todos homogéneos em relação ao número de faixas de vídeo e de áudio. Para tal, é necessário especificar a bandeira "InsertBlackIfNoVideo".

Pode pegar em qualquer uma das predefinições mes documentadas [nesta](media-services-mes-presets-overview.md) secção e fazer a seguinte modificação:

#### <a name="json-preset"></a>Predefinição JSON
    {
          "KeyFrameInterval": "00:00:02",
          "StretchMode": "AutoSize",
          "Condition": "InsertBlackIfNoVideo",
          "H264Layers": [
          …
          ]
    }

#### <a name="xml-preset"></a>Predefinição XML

Ao utilizar o XML, utilize o Condição="InsertBlackIfNoVideo" como atributo ao elemento **H264Video** e condição="InsertSilenceIfNoAudio" como atributo ao **AACAudio**.

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

## <a id="rotate_video"></a>Rode um vídeo
A [Norma Media Encoder](media-services-dotnet-encode-with-media-encoder-standard.md) suporta a rotação por ângulos de 0/90/180/270. O comportamento predefinido é "Auto", onde tenta detetar os metadados de rotação no ficheiro de vídeo que está a chegar e compensar. Incluir o seguinte elemento **Fontes** a uma das predefinições definidas [nesta](media-services-mes-presets-overview.md) secção:

### <a name="json-preset"></a>Predefinição JSON
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
### <a name="xml-preset"></a>Predefinição XML
    <Sources>
           <Source>
          <Streams />
          <Filters>
            <Rotation>90</Rotation>
          </Filters>
        </Source>
    </Sources>

Além disso, consulte [este](media-services-mes-schema.md#PreserveResolutionAfterRotation) tópico para obter mais informações sobre como o codificador interpreta as definições de Largura e Altura no predefinido, quando é desencadeada uma compensação de rotação.

Pode utilizar o valor "0" para indicar ao codificador que ignore os metadados de rotação, se presentes, no vídeo de entrada.

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Veja Também
[Visão geral de codificação de serviços de mídia](media-services-encode-asset.md)
