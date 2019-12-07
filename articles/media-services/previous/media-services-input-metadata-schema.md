---
title: Esquema de metadados de entrada dos serviços de mídia do Azure | Microsoft Docs
description: Este artigo fornece uma visão geral do esquema de metadados de entrada dos serviços de mídia do Azure.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: a81d6edfd887dc935a53742b7bc1492651c9bda5
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74887124"
---
# <a name="input-metadata"></a>Metadados de entrada 

Um trabalho de codificação é associado a um ativo de entrada (ou ativos) no qual você deseja executar algumas tarefas de codificação.  Após a conclusão de uma tarefa, um ativo de saída é produzido.  O ativo de saída contém vídeo, áudio, miniaturas, manifesto, etc. O ativo de saída também contém um arquivo com metadados sobre o ativo de entrada. O nome do arquivo XML de metadados tem o seguinte formato: &lt;asset_id&gt;_metadata. XML (por exemplo, 41114ad3-eb5e-4c57-8d92-5354e2b7d4a4_metadata. xml), em que &lt;asset_id&gt; é o valor de AssetID do ativo de entrada.  

Os serviços de mídia não preventivamentem a verificação de ativos de entrada para gerar metadados. Os metadados de entrada são gerados somente como um artefato quando um ativo de entrada é processado em um trabalho. Portanto, esse artefato é gravado no ativo de saída. Ferramentas diferentes são usadas para gerar metadados para ativos de entrada e ativos de saída. Portanto, os metadados de entrada têm um esquema um pouco diferente dos metadados de saída.

Se você quiser examinar o arquivo de metadados, poderá criar um localizador **SAS** e baixar o arquivo em seu computador local. Você pode encontrar um exemplo de como criar um localizador SAS e baixar um arquivo [usando as extensões do SDK do .net dos serviços de mídia](media-services-dotnet-get-started.md).  

Este artigo aborda os elementos e tipos do esquema XML no qual o metadados de entrada (&lt;asset_id&gt;_metadata. xml) é baseado.  Para obter informações sobre o arquivo que contém metadados sobre o ativo de saída, consulte [metadados de saída](media-services-output-metadata-schema.md).  

Você pode encontrar o [código do esquema](media-services-input-metadata-schema.md#code) um [exemplo de XML](media-services-input-metadata-schema.md#xml) no final deste artigo.  
 

## <a name="AssetFiles"></a>Elemento AssetFiles (elemento raiz)
Contém uma coleção de [elementos assetfile](media-services-input-metadata-schema.md#AssetFile)para o trabalho de codificação.  

Consulte um exemplo de XML no final deste artigo: [exemplo de XML](media-services-input-metadata-schema.md#xml).  

| Nome | Descrição |
| --- | --- |
| **AssetFile**<br /><br /> minOccurs="1" maxOccurs="unbounded" |Um único elemento filho. Para obter mais informações, consulte o [elemento assetfile](media-services-input-metadata-schema.md#AssetFile). |

## <a name="AssetFile"></a>Elemento assetfile
 Contém atributos e elementos que descrevem um arquivo de ativo.  

 Consulte um exemplo de XML no final deste artigo: [exemplo de XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atributos
| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Nome**<br /><br /> Obrigatório |**xs:string** |Nome do arquivo de ativo. |
| **Tamanho**<br /><br /> Obrigatório |**xs:long** |Tamanho do arquivo de ativo em bytes. |
| **Permanência**<br /><br /> Obrigatório |**xs:duration** |Duração da reprodução do conteúdo. Exemplo: Duration = "PT25M 37.757 S". |
| **NumberOfStreams**<br /><br /> Obrigatório |**xs:int** |Número de fluxos no arquivo de ativo. |
| **Formato de formatação**<br /><br /> Obrigatório |**xs: String** |Nomes de formato. |
| **FormatVerboseNames**<br /><br /> Obrigatório |**xs: String** |Formatar nomes detalhados. |
| **StartTime** |**xs:duration** |Hora de início do conteúdo. Exemplo: StartTime = "PT 2.669 S". |
| **OverallBitRate** |**xs: int** |Taxa de bits média do arquivo de ativo em Kbps. |

> [!NOTE]
> Os quatro elementos filho a seguir devem aparecer em uma sequência.  
> 
> 

### <a name="child-elements"></a>Elementos filho
| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Suplementa**<br /><br /> minOccurs="0" | |Coleção de todos os [elementos de programas](media-services-input-metadata-schema.md#Programs) quando o arquivo de ativo está no formato MPEG-TS. |
| **VideoTracks**<br /><br /> minOccurs="0" | |Cada arquivo de ativo físico pode conter zero ou mais faixas de vídeos intercaladas em um formato de contêiner apropriado. Esse elemento contém uma coleção de todos os [VideoTracks](media-services-input-metadata-schema.md#VideoTracks) que fazem parte do arquivo de ativo. |
| **AudioTracks**<br /><br /> minOccurs="0" | |Cada arquivo de ativo físico pode conter zero ou mais faixas de áudio intercaladas em um formato de contêiner apropriado. Esse elemento contém uma coleção de todos os [AudioTracks](media-services-input-metadata-schema.md#AudioTracks) que fazem parte do arquivo de ativo. |
| **Los**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[MetadataType](media-services-input-metadata-schema.md#MetadataType) |Metadados do arquivo de ativo representados como cadeias de caracteres chave\valor. Por exemplo:<br /><br /> **&lt;Metadata key="language" value="eng" /&gt;** |

## <a name="TrackType"></a>TrackType
Consulte um exemplo de XML no final deste artigo: [exemplo de XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atributos
| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Id**<br /><br /> Obrigatório |**xs:int** |Índice de base zero desta faixa de áudio ou vídeo.<br /><br /> Isso não é necessariamente que o TrackID como usado em um arquivo MP4. |
| **Codec** |**xs:string** |Cadeia de caracteres do codec de faixa de vídeo. |
| **CodecLongName** |**xs: String** |Nome longo do codec de faixa de áudio ou vídeo. |
| **TimeBase**<br /><br /> Obrigatório |**xs:string** |Base de tempo. Exemplo: timebase = "1/48000" |
| **NumberOfFrames** |**xs:int** |Número de quadros (presente para faixas de vídeo). |
| **StartTime** |**xs: Duration** |Hora de início da faixa. Exemplo: StartTime = "PT 2.669 S" |
| **Permanência** |**xs:duration** |Duração da faixa. Exemplo: Duration = "PTSampleFormat M 37.757 S". |

> [!NOTE]
> Os dois elementos filho a seguir devem aparecer em uma sequência.  
> 
> 

### <a name="child-elements"></a>Elementos filho
| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Venda**<br /><br /> minOccurs="0" maxOccurs="1" |[StreamDispositionType](media-services-input-metadata-schema.md#StreamDispositionType) |Contém informações de apresentação (por exemplo, se uma faixa de áudio específica é para visualizadores com deficiência visual). |
| **Los**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[MetadataType](media-services-input-metadata-schema.md#MetadataType) |Cadeias de caracteres de chave/valor genérico que podem ser usadas para armazenar uma variedade de informações. Por exemplo, Key = "Language" e Value = "ENG". |

## <a name="AudioTrackType"></a>AudioTrackType (herda de TrackType)
 **AudioTrackType** é um tipo global complexo que herda de [TrackType](media-services-input-metadata-schema.md#TrackType).  

 O tipo representa uma faixa de áudio específica no arquivo de ativo.  

 Consulte um exemplo de XML no final deste artigo: [exemplo de XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atributos
| Nome | Tipo | Descrição |
| --- | --- | --- |
| **SampleFormat** |**xs:string** |Formato de exemplo. |
| **ChannelLayout** |**xs: String** |Layout do canal. |
| **Channels** (Canais)<br /><br /> Obrigatório |**xs:int** |Número (0 ou mais) de canais de áudio. |
| **SamplingRate**<br /><br /> Obrigatório |**xs:int** |Taxa de amostragem de áudio em amostras/s ou Hz. |
| **720p** |**xs:int** |Taxa média de bits de áudio em bits por segundo, calculada a partir do arquivo de ativo. Somente a carga de fluxo elementar é contada e a sobrecarga de empacotamento não é incluída nessa contagem. |
| **BitsPerSample** |**xs:int** |Bits por amostra para o tipo de formato wFormatTag. |

## <a name="VideoTrackType"></a>VideoTrackType (herda de TrackType)
**VideoTrackType** é um tipo global complexo que herda de [TrackType](media-services-input-metadata-schema.md#TrackType).  

O tipo representa uma faixa de vídeo específica no arquivo de ativo.  

Consulte um exemplo de XML no final deste artigo: [exemplo de XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atributos
| Nome | Tipo | Descrição |
| --- | --- | --- |
| **FourCC**<br /><br /> Obrigatório |**xs:string** |Código FourCC do codec de vídeo. |
| **Perfil** |**xs: String** |Perfil da faixa de vídeo. |
| **Nível** |**xs: String** |Nível da faixa de vídeo. |
| **PixelFormat** |**xs: String** |Formato de pixel da faixa de vídeo. |
| **Width**<br /><br /> Obrigatório |**xs:int** |Largura de vídeo codificada em pixels. |
| **Tamanho**<br /><br /> Obrigatório |**xs:int** |Altura de vídeo codificada em pixels. |
| **DisplayAspectRatioNumerator**<br /><br /> Obrigatório |**xs: double** |Numerador de taxa de proporção de exibição de vídeo. |
| **DisplayAspectRatioDenominator**<br /><br /> Obrigatório |**xs:double** |Denominador de taxa de proporção de exibição de vídeo. |
| **DisplayAspectRatioDenominator**<br /><br /> Obrigatório |**xs: double** |Numerador de taxa de proporção de amostra de vídeo. |
| **SampleAspectRatioNumerator** |**xs: double** |Numerador de taxa de proporção de amostra de vídeo. |
| **SampleAspectRatioNumerator** |**xs:double** |Denominador de taxa de proporção de amostra de vídeo. |
| **FrameRate**<br /><br /> Obrigatório |**xs:decimal** |Taxa de quadros de vídeo medido no formato. 3F. |
| **720p** |**xs:int** |Taxa média de bits de vídeo em quilobits por segundo, calculada a partir do arquivo de ativo. Somente a carga de fluxo elementar é contada e a sobrecarga de empacotamento não é incluída. |
| **MaxGOPBitrate** |**xs: int** |Taxa de bits média máxima de GOP para esta faixa de vídeo em quilobits por segundo. |
| **HasBFrames** |**xs:int** |Faixa de vídeo número de quadros B. |

## <a name="MetadataType"></a>MetadataType
**MetadataType** é um tipo global complexo que descreve os metadados de um arquivo de ativo como cadeias de caracteres de chave/valor. Por exemplo, Key = "Language" e Value = "ENG".  

Consulte um exemplo de XML no final deste artigo: [exemplo de XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atributos
| Nome | Tipo | Descrição |
| --- | --- | --- |
| **chave**<br /><br /> Obrigatório |**xs:string** |A chave no par chave/valor. |
| **valor**<br /><br /> Obrigatório |**xs:string** |O valor no par chave/valor. |

## <a name="ProgramType"></a>Programtype
**Programtype** é um tipo global complexo que descreve um programa.  

### <a name="attributes"></a>Atributos
| Nome | Tipo | Descrição |
| --- | --- | --- |
| **ProgramId**<br /><br /> Obrigatório |**xs:int** |ID do programa |
| **NumberOfPrograms**<br /><br /> Obrigatório |**xs:int** |Número de programas. |
| **PmtPid**<br /><br /> Obrigatório |**xs:int** |As tabelas de mapa do programa (PMTs) contêm informações sobre programas.  Para obter mais informações, consulte [PGTO](https://en.wikipedia.org/wiki/MPEG_transport_stream#PMT). |
| **PcrPid**<br /><br /> Obrigatório |**xs: int** |Usado pelo decodificador. Para obter mais informações, consulte [PCR](https://en.wikipedia.org/wiki/MPEG_transport_stream#PCR) |
| **StartPTS** |**xs: long** |Iniciando carimbo de data/hora da apresentação. |
| **EndPTS** |**xs: long** |Marca de hora final da apresentação. |

## <a name="StreamDispositionType"></a> StreamDispositionType
**StreamDispositionType** é um tipo global complexo que descreve o fluxo.  

Consulte um exemplo de XML no final deste artigo: [exemplo de XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atributos
| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Predefinição**<br /><br /> Obrigatório |**xs: int** |Defina esse atributo como 1 para indicar que esta é a apresentação padrão. |
| **Dub**<br /><br /> Obrigatório |**xs:int** |Defina esse atributo como 1 para indicar que esta é a apresentação apelidada. |
| **Original**<br /><br /> Obrigatório |**xs: int** |Defina esse atributo como 1 para indicar que esta é a apresentação original. |
| **Comentar**<br /><br /> Obrigatório |**xs:int** |Defina esse atributo como 1 para indicar que esta faixa contém comentários. |
| **Letra**<br /><br /> Obrigatório |**xs:int** |Defina esse atributo como 1 para indicar que esta faixa contém letras de música. |
| **Karaokê**<br /><br /> Obrigatório |**xs:int** |Defina esse atributo como 1 para indicar que isso representa a faixa do karaokê (música em segundo plano, sem vozes). |
| **Exigir**<br /><br /> Obrigatório |**xs:int** |Defina esse atributo como 1 para indicar que esta é a apresentação forçada. |
| **HearingImpaired**<br /><br /> Obrigatório |**xs:int** |Defina esse atributo como 1 para indicar que esta faixa é para pessoas que estão com deficiência auditiva. |
| **VisualImpaired**<br /><br /> Obrigatório |**xs:int** |Defina esse atributo como 1 para indicar que esta faixa é para os deficientes visuais. |
| **CleanEffects**<br /><br /> Obrigatório |**xs: int** |Defina esse atributo como 1 para indicar que esta faixa tem efeitos de limpeza. |
| **AttachedPic**<br /><br /> Obrigatório |**xs: int** |Defina esse atributo como 1 para indicar que esta faixa tem imagens. |

## <a name="Programs"></a>Elemento Programs
Elemento wrapper que contém vários elementos **Program** .  

### <a name="child-elements"></a>Elementos filho
| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Program**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[Programtype](media-services-input-metadata-schema.md#ProgramType) |Para arquivos de ativo que estão no formato MPEG-TS, contém informações sobre programas no arquivo de ativo. |

## <a name="VideoTracks"></a>Elemento VideoTracks
 Elemento wrapper que contém vários elementos **VideoTrack** .  

 Consulte um exemplo de XML no final deste artigo: [exemplo de XML](media-services-input-metadata-schema.md#xml).  

### <a name="child-elements"></a>Elementos filho
| Nome | Tipo | Descrição |
| --- | --- | --- |
| **VideoTrack**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[VideoTrackType (herda de TrackType)](media-services-input-metadata-schema.md#VideoTrackType) |Contém informações sobre faixas de vídeo no arquivo de ativo. |

## <a name="AudioTracks"></a>Elemento AudioTracks
 Elemento wrapper que contém vários elementos **AudioTrack** .  

 Consulte um exemplo de XML no final deste artigo: [exemplo de XML](media-services-input-metadata-schema.md#xml).  

### <a name="elements"></a>elementos
| Nome | Tipo | Descrição |
| --- | --- | --- |
| **AudioTrack**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[AudioTrackType (herda de TrackType)](media-services-input-metadata-schema.md#AudioTrackType) |Contém informações sobre faixas de áudio no arquivo de ativo. |

## <a name="code"></a>Código do esquema
    <?xml version="1.0" encoding="utf-8"?>  
    <xs:schema xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns:msdata="urn:schemas-microsoft-com:xml-msdata" version="1.0"  
               xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2014/07/mediaencoder/inputmetadata"  
               targetNamespace="http://schemas.microsoft.com/windowsazure/mediaservices/2014/07/mediaencoder/inputmetadata"  
               elementFormDefault="qualified">  

      <xs:complexType name="MetadataType">  
        <xs:attribute name="key"   type="xs:string" use="required"/>  
        <xs:attribute name="value" type="xs:string" use="required"/>  
      </xs:complexType>  

      <xs:complexType name="ProgramType">  
        <xs:attribute name="ProgramId" type="xs:int" use="required">  
          <xs:annotation>  
            <xs:documentation>Program Id</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="NumberOfPrograms" type="xs:int" use="required">  
          <xs:annotation>  
            <xs:documentation>Number of programs</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="PmtPid" type="xs:int" use="required">  
          <xs:annotation>  
            <xs:documentation>pmt pid</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="PcrPid" type="xs:int" use="required">  
          <xs:annotation>  
            <xs:documentation>pcr pid</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="StartPTS" type="xs:long">  
          <xs:annotation>  
            <xs:documentation>start pts</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="EndPTS" type="xs:long">  
          <xs:annotation>  
            <xs:documentation>end pts</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
      </xs:complexType>  

      <xs:complexType name="StreamDispositionType">  
        <xs:attribute name="Default"          type="xs:int" use="required" />  
        <xs:attribute name="Dub"              type="xs:int" use="required" />  
        <xs:attribute name="Original"         type="xs:int" use="required" />  
        <xs:attribute name="Comment"          type="xs:int" use="required" />  
        <xs:attribute name="Lyrics"           type="xs:int" use="required" />  
        <xs:attribute name="Karaoke"          type="xs:int" use="required" />  
        <xs:attribute name="Forced"           type="xs:int" use="required" />  
        <xs:attribute name="HearingImpaired"  type="xs:int" use="required" />  
        <xs:attribute name="VisualImpaired"   type="xs:int" use="required" />  
        <xs:attribute name="CleanEffects"     type="xs:int" use="required" />  
        <xs:attribute name="AttachedPic"      type="xs:int" use="required" />  
      </xs:complexType>  

      <xs:complexType name="TrackType" abstract="true">  
        <xs:sequence>  
          <xs:element name="Disposition" type="StreamDispositionType" minOccurs="0" maxOccurs="1"/>  
          <xs:element name="Metadata" type="MetadataType" minOccurs="0" maxOccurs="unbounded"/>  
        </xs:sequence>  
        <xs:attribute name="Id" use="required">  
          <xs:annotation>  
            <xs:documentation>zero-based index of this video track. Note: this is not necessarily the TrackID as used in an MP4 file</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="Codec" type="xs:string">  
          <xs:annotation>  
            <xs:documentation>video track codec string</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="CodecLongName" type="xs:string">  
          <xs:annotation>  
            <xs:documentation>video track codec long name</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="TimeBase"  type="xs:string" use="required">  
          <xs:annotation>  
            <xs:documentation>Time base. Example: TimeBase="1/48000"</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="NumberOfFrames">  
          <xs:annotation>  
            <xs:documentation>number of frames</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="StartTime" type="xs:duration">  
          <xs:annotation>  
            <xs:documentation>Track start time. Example: StartTime="PT2.669S"</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="Duration" type="xs:duration">  
          <xs:annotation>  
            <xs:documentation>Track duration. Example: Duration="PT25M37.757S"</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
      </xs:complexType>  

      <xs:complexType name="VideoTrackType">  
        <xs:annotation>  
          <xs:documentation>A specific video track in the parent AssetFile</xs:documentation>  
        </xs:annotation>  
        <xs:complexContent>  
          <xs:extension base="TrackType">  
            <xs:attribute name="FourCC" type="xs:string" use="required">  
              <xs:annotation>  
                <xs:documentation>video codec FourCC code</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="Profile" type="xs:string">  
              <xs:annotation>  
                <xs:documentation>profile</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="Level" type="xs:string">  
              <xs:annotation>  
                <xs:documentation>level</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="PixelFormat" type="xs:string">  
              <xs:annotation>  
                <xs:documentation>Video track's pixel format</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="Width" use="required">  
              <xs:annotation>  
                <xs:documentation>encoded video width in pixels</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="Height" use="required">  
              <xs:annotation>  
                <xs:documentation>encoded video height in pixels</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="DisplayAspectRatioNumerator" use="required">  
              <xs:annotation>  
                <xs:documentation>video display aspect ratio numerator</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:double">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="DisplayAspectRatioDenominator" use="required">  
              <xs:annotation>  
                <xs:documentation>video display aspect ratio denominator</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:double">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="SampleAspectRatioNumerator">  
              <xs:annotation>  
                <xs:documentation>video sample aspect ratio numerator</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:double">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="SampleAspectRatioDenominator">  
              <xs:annotation>  
                <xs:documentation>video sample aspect ratio denominator</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:double">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="FrameRate" use="required">  
              <xs:annotation>  
                <xs:documentation>measured video frame rate in .3f format</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:decimal">  
                  <xs:minInclusive value="0"/>  
                  <xs:fractionDigits value="3"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="Bitrate">  
              <xs:annotation>  
                <xs:documentation>average video bit rate in kilobits per second, as calculated from the AssetFile. Counts only the elementary stream payload, and does not include the packaging overhead</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="MaxGOPBitrate">  
              <xs:annotation>  
                <xs:documentation>Max GOP average bitrate for this video track, in kilobits per second</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="HasBFrames" type="xs:int">  
              <xs:annotation>  
                <xs:documentation>video track number of B frames</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
          </xs:extension>  
        </xs:complexContent>  
      </xs:complexType>  

      <xs:complexType name="AudioTrackType">  
        <xs:annotation>  
          <xs:documentation>a specific audio track in the parent AssetFile</xs:documentation>  
        </xs:annotation>  
        <xs:complexContent>  
          <xs:extension base="TrackType">  
            <xs:attribute name="SampleFormat"  type="xs:string">  
              <xs:annotation>  
                <xs:documentation>sample format</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="ChannelLayout"  type="xs:string">  
              <xs:annotation>  
                <xs:documentation>channel layout</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="Channels" use="required">  
              <xs:annotation>  
                <xs:documentation>number of audio channels</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="SamplingRate" use="required">  
              <xs:annotation>  
                <xs:documentation>audio sampling rate in samples/sec or Hz</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="Bitrate">  
              <xs:annotation>  
                <xs:documentation>average audio bit rate in bits per second, as calculated from the AssetFile. Counts only the elementary stream payload, and does not include the packaging overhead</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="BitsPerSample">  
              <xs:annotation>  
                <xs:documentation>Bits per sample for the wFormatTag format type</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
          </xs:extension>  
        </xs:complexContent>  
      </xs:complexType>  

      <xs:element name="AssetFiles">  
        <xs:annotation>  
          <xs:documentation>Collection of AssetFile entries for the encoding job</xs:documentation>  
        </xs:annotation>  
        <xs:complexType>  
          <xs:sequence>  
            <xs:element name="AssetFile" minOccurs="1" maxOccurs="unbounded">  
              <xs:annotation>  
                <xs:documentation>asset file</xs:documentation>  
              </xs:annotation>  
              <xs:complexType>  
                <xs:sequence>  
                  <xs:element name="Programs" minOccurs="0">  
                    <xs:annotation>  
                      <xs:documentation>This is the collection of all programs when file is MPEG-TS</xs:documentation>  
                    </xs:annotation>  
                    <xs:complexType>  
                      <xs:sequence>  
                        <xs:element name="Program" type="ProgramType" minOccurs="0" maxOccurs="unbounded" />  
                      </xs:sequence>  
                    </xs:complexType>  
                  </xs:element>  
                  <xs:element name="VideoTracks" minOccurs="0">  
                    <xs:annotation>  
                      <xs:documentation>Each physical AssetFile can contain in it zero or more video tracks interleaved into an appropriate container format. This is the collection of all those video tracks</xs:documentation>  
                    </xs:annotation>  
                    <xs:complexType>  
                      <xs:sequence>  
                        <xs:element name="VideoTrack" type="VideoTrackType" minOccurs="0" maxOccurs="unbounded" />  
                      </xs:sequence>  
                    </xs:complexType>  
                  </xs:element>  
                  <xs:element name="AudioTracks" minOccurs="0">  
                    <xs:annotation>  
                      <xs:documentation>each physical AssetFile can contain in it zero or more audio tracks interleaved into an appropriate container format. This is the collection of all those audio tracks</xs:documentation>  
                    </xs:annotation>  
                    <xs:complexType>  
                      <xs:sequence>  
                        <xs:element name="AudioTrack" type="AudioTrackType" minOccurs="0" maxOccurs="unbounded" />  
                      </xs:sequence>  
                    </xs:complexType>  
                  </xs:element>  
                  <xs:element name="Metadata" type="MetadataType" minOccurs="0" maxOccurs="unbounded" />  
                </xs:sequence>  
                <xs:attribute name="Name" type="xs:string" use="required">  
                  <xs:annotation>  
                    <xs:documentation>the media asset file name</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="Size" use="required">  
                  <xs:annotation>  
                    <xs:documentation>size of file in bytes</xs:documentation>  
                  </xs:annotation>  
                  <xs:simpleType>  
                    <xs:restriction base="xs:long">  
                      <xs:minInclusive value="0"/>  
                    </xs:restriction>  
                  </xs:simpleType>  
                </xs:attribute>  
                <xs:attribute name="Duration" type="xs:duration" use="required">  
                  <xs:annotation>  
                    <xs:documentation>content play back duration. Example: Duration="PT25M37.757S"</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="NumberOfStreams" type="xs:int" use="required">  
                  <xs:annotation>  
                    <xs:documentation>number of streams in asset file</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="FormatNames" type="xs:string" use="required">  
                  <xs:annotation>  
                    <xs:documentation>format names</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="FormatVerboseName" type="xs:string" use="required">  
                  <xs:annotation>  
                    <xs:documentation>format verbose names</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="StartTime" type="xs:duration">  
                  <xs:annotation>  
                    <xs:documentation>content start time. Example: StartTime="PT2.669S"</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="OverallBitRate">  
                  <xs:annotation>  
                    <xs:documentation>average bitrate of the asset file in kbps</xs:documentation>  
                  </xs:annotation>  
                  <xs:simpleType>  
                    <xs:restriction base="xs:int">  
                      <xs:minInclusive value="0"/>  
                    </xs:restriction>  
                  </xs:simpleType>  
                </xs:attribute>  
              </xs:complexType>  
            </xs:element>  
          </xs:sequence>  
        </xs:complexType>  
      </xs:element>  
    </xs:schema>  


## <a name="xml"></a>Exemplo de XML
Veja a seguir um exemplo do arquivo de metadados de entrada.  

    <?xml version="1.0" encoding="utf-8"?>  
    <AssetFiles xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2014/07/mediaencoder/inputmetadata">  
      <AssetFile Name="bear.mp4" Size="1973733" Duration="PT12.678S" NumberOfStreams="2" FormatNames="mov,mp4,m4a,3gp,3g2,mj2" FormatVerboseName="QuickTime / MOV" StartTime="PT0S" OverallBitRate="1245">  
        <VideoTracks>  
          <VideoTrack Id="1" Codec="h264" CodecLongName="H.264 / AVC / MPEG-4 AVC / MPEG-4 part 10" TimeBase="1/29970" NumberOfFrames="375" StartTime="PT0.034S" Duration="PT12.645S" FourCC="avc1" Profile="High" Level="4.1" PixelFormat="yuv420p" Width="512" Height="384" DisplayAspectRatioNumerator="4" DisplayAspectRatioDenominator="3" SampleAspectRatioNumerator="1" SampleAspectRatioDenominator="1" FrameRate="29.656" Bitrate="1043" HasBFrames="1">  
            <Disposition Default="1" Dub="0" Original="0" Comment="0" Lyrics="0" Karaoke="0" Forced="0" HearingImpaired="0" VisualImpaired="0" CleanEffects="0" AttachedPic="0" />  
            <Metadata key="creation_time" value="2010-03-10 16:11:56" />  
            <Metadata key="language" value="eng" />  
            <Metadata key="handler_name" value="Mainconcept MP4 Video Media Handler" />  
          </VideoTrack>  
        </VideoTracks>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="aac" CodecLongName="AAC (Advanced Audio Coding)" TimeBase="1/44100" NumberOfFrames="546" StartTime="PT0S" Duration="PT12.678S" SampleFormat="fltp" ChannelLayout="stereo" Channels="2" SamplingRate="44100" Bitrate="156" BitsPerSample="0">  
            <Disposition Default="1" Dub="0" Original="0" Comment="0" Lyrics="0" Karaoke="0" Forced="0" HearingImpaired="0" VisualImpaired="0" CleanEffects="0" AttachedPic="0" />  
            <Metadata key="creation_time" value="2010-03-10 16:11:56" />  
            <Metadata key="language" value="eng" />  
            <Metadata key="handler_name" value="Mainconcept MP4 Sound Media Handler" />  
          </AudioTrack>  
        </AudioTracks>  
        <Metadata key="major_brand" value="mp42" />  
        <Metadata key="minor_version" value="0" />  
        <Metadata key="compatible_brands" value="mp42mp41" />  
        <Metadata key="creation_time" value="2010-03-10 16:11:53" />  
        <Metadata key="comment" value="Courtesy of National Geographic.  Used by Permission." />  
      </AssetFile>  
    </AssetFiles>  

## <a name="next-steps"></a>Passos seguintes
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

