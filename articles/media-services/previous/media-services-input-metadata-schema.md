---
title: Esquema de metadados de entrada de serviços de multimédia do Azure | Documentos da Microsoft
description: O tópico apresenta uma visão geral do esquema de metadados de entrada de serviços de multimédia do Azure.
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
ms.openlocfilehash: cdca2823962c442c9142fa439795269f419aa489
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58189687"
---
# <a name="input-metadata"></a>Metadados de entrada 

Uma tarefa de codificação está associada a um recurso de entrada (ou recursos) no qual pretende efetuar algumas tarefas de codificação.  Após a conclusão de uma tarefa, um elemento de saída é produzido.  O elemento de saída contém vídeo, áudio, miniaturas, manifesto, etc. O elemento de saída também contém um ficheiro com metadados sobre o elemento de entrada. O nome de ficheiro dos metadados de XML tem o seguinte formato: &lt;asset_id&gt;_metadata.xml (por exemplo, d de 57-8 41114ad3-eb5e - 4c 92-5354e2b7d4a4_metadata.xml), onde &lt;asset_id&gt; é o valor de AssetId das elemento de entrada.  

Serviços de multimédia não rastreia preventivamente ativos de entrada para gerar metadados. Metadados de entrada é gerado apenas como um artefato quando uma entrada de que recurso é processado numa tarefa. Por conseguinte, este artefacto é escrito na saída ativo. Ferramentas diferentes são utilizadas para gerar metadados para os recursos de entrada e saída ativos. Por conseguinte, os metadados de entrada tem um esquema ligeiramente diferentes dos metadados de saída.

Se quiser examinar o ficheiro de metadados, pode criar uma **SAS** localizador e transfira o ficheiro para o computador local. Pode encontrar um exemplo sobre como criar um localizador SAS e transfira um ficheiro [usando as extensões de SDK de .NET de serviços de multimédia](media-services-dotnet-get-started.md).  

Este artigo aborda os elementos e tipos do esquema XML no qual os metadados de entrada (&lt;asset_id&gt;_metadata.xml) baseia-se.  Para obter informações sobre o ficheiro que contém metadados sobre o elemento de saída, consulte [metadados de saída](media-services-output-metadata-schema.md).  

Pode encontrar os [código de esquema](media-services-input-metadata-schema.md#code) um [exemplo XML](media-services-input-metadata-schema.md#xml) no final deste artigo.  
 

## <a name="AssetFiles"></a> AssetFiles elemento (elemento de raiz)
Contém uma coleção de [AssetFile elemento](media-services-input-metadata-schema.md#AssetFile)s para o trabalho de codificação.  

Ver um exemplo XML no final deste artigo: [Exemplo XML](media-services-input-metadata-schema.md#xml).  

| Name | Descrição |
| --- | --- |
| **AssetFile**<br /><br /> minOccurs="1" maxOccurs="unbounded" |Um elemento único filho. Para obter mais informações, consulte [AssetFile elemento](media-services-input-metadata-schema.md#AssetFile). |

## <a name="AssetFile"></a> Elemento de AssetFile
 Contém atributos e elementos que descrevem um ficheiro de elemento.  

 Ver um exemplo XML no final deste artigo: [Exemplo XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atributos
| Name | Tipo | Descrição |
| --- | --- | --- |
| **Nome**<br /><br /> Necessário |**xs:string** |Nome de ficheiro do elemento. |
| **Tamanho**<br /><br /> Necessário |**xs:long** |Tamanho do ficheiro do elemento em bytes. |
| **Duração**<br /><br /> Necessário |**xs:duration** |Duração de back-play conteúdo. Exemplo: Duration="PT25M37.757S". |
| **NumberOfStreams**<br /><br /> Necessário |**xs:int** |Número de fluxos no ficheiro do elemento. |
| **FormatNames**<br /><br /> Necessário |**xs: string** |Nomes de formato. |
| **FormatVerboseNames**<br /><br /> Necessário |**xs: string** |Formate os nomes verbosos. |
| **StartTime** |**xs:duration** |Hora de início de conteúdo. Exemplo: StartTime="PT2.669S". |
| **OverallBitRate** |**xs: int** |Velocidade de transmissão média de ficheiro do elemento em kbps. |

> [!NOTE]
> Os seguintes elementos quatro subordinados têm de aparecer numa sequência.  
> 
> 

### <a name="child-elements"></a>Elementos subordinados
| Name | Tipo | Descrição |
| --- | --- | --- |
| **Programas**<br /><br /> minOccurs="0" | |Coleção de todos os [elemento programas](media-services-input-metadata-schema.md#Programs) quando o ficheiro de elemento está no formato de MPEG-TS. |
| **VideoTracks**<br /><br /> minOccurs="0" | |Cada arquivo de ativos físicos pode conter zero ou mais cursos de vídeos intercalados num formato de contêiner apropriado. Este elemento contém uma coleção de todos os [VideoTracks](media-services-input-metadata-schema.md#VideoTracks) que fazem parte do ficheiro do elemento. |
| **AudioTracks**<br /><br /> minOccurs="0" | |Cada arquivo de ativos físicos pode conter zero ou mais faixas de áudio intercaladas num formato de contêiner apropriado. Este elemento contém uma coleção de todos os [AudioTracks](media-services-input-metadata-schema.md#AudioTracks) que fazem parte do ficheiro do elemento. |
| **metadados**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[MetadataType](media-services-input-metadata-schema.md#MetadataType) |Metadados do ficheiro de elemento representados como key\value cadeias de caracteres. Por exemplo:<br /><br /> **&lt;Metadata key="language" value="eng" /&gt;** |

## <a name="TrackType"></a> TrackType
Ver um exemplo XML no final deste artigo: [Exemplo XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atributos
| Name | Tipo | Descrição |
| --- | --- | --- |
| **Id**<br /><br /> Necessário |**xs:int** |Índice baseado em zero desta faixa de áudio ou vídeo.<br /><br /> Isso não é necessariamente que o TrackID como usado num arquivo MP4. |
| **Codec** |**xs:string** |Cadeia de caracteres de codec track de vídeo. |
| **CodecLongName** |**xs: string** |Codec de faixa de áudio ou vídeo nome longo. |
| **TimeBase**<br /><br /> Necessário |**xs:string** |Tempo base. Exemplo: TimeBase="1/48000" |
| **NumberOfFrames** |**xs:int** |Número de quadros (presentes roteiros de vídeo). |
| **StartTime** |**xs: duration** |Hora de início de controle. Exemplo: StartTime="PT2.669S" |
| **Duração** |**xs:duration** |Controlar a duração. Exemplo: Duração = "PTSampleFormat M37.757S". |

> [!NOTE]
> Os seguintes elementos dois subordinados têm de aparecer numa sequência.  
> 
> 

### <a name="child-elements"></a>Elementos subordinados
| Name | Tipo | Descrição |
| --- | --- | --- |
| **Disposição**<br /><br /> minOccurs="0" maxOccurs="1" |[StreamDispositionType](media-services-input-metadata-schema.md#StreamDispositionType) |Contém informações de apresentação (por exemplo, se uma faixa de áudio particular é para visualizadores deficiência). |
| **metadados**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[MetadataType](media-services-input-metadata-schema.md#MetadataType) |Cadeias de chave/valor genérico que podem ser utilizadas para conter diversas informações. Por exemplo, chave = "language" e o valor = "engenharia". |

## <a name="AudioTrackType"></a> AudioTrackType (que é herdeira de TrackType)
 **AudioTrackType** é um tipo complexo global que é herdeira de [TrackType](media-services-input-metadata-schema.md#TrackType).  

 O tipo representa uma faixa de áudio específica no arquivo de recurso.  

 Ver um exemplo XML no final deste artigo: [Exemplo XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atributos
| Name | Tipo | Descrição |
| --- | --- | --- |
| **SampleFormat** |**xs:string** |Formato de exemplo. |
| **ChannelLayout** |**xs: string** |Esquema de canal. |
| **canais**<br /><br /> Necessário |**xs:int** |Número (0 ou mais) de canais de áudio. |
| **SamplingRate**<br /><br /> Necessário |**xs:int** |Frequência de amostragem de áudio em exemplos por segundo ou Hz. |
| **Velocidade de transmissão** |**xs:int** |Taxa de bits de áudio de média em bits por segundo, conforme calculado a partir do ficheiro do elemento. Apenas o payload de stream elementares é contabilizado, e a sobrecarga de empacotamento não está incluída nesta contagem. |
| **BitsPerSample** |**xs:int** |Tipo de bits por amostra para o formato de wFormatTag. |

## <a name="VideoTrackType"></a> VideoTrackType (que é herdeira de TrackType)
**VideoTrackType** é um tipo complexo global que é herdeira de [TrackType](media-services-input-metadata-schema.md#TrackType).  

O tipo representa um Roteiro de vídeo específico no arquivo de recurso.  

Ver um exemplo XML no final deste artigo: [Exemplo XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atributos
| Name | Tipo | Descrição |
| --- | --- | --- |
| **FourCC**<br /><br /> Necessário |**xs:string** |Codec de vídeo FourCC código. |
| **Perfil** |**xs: string** |Perfil de vídeo track. |
| **Nível** |**xs: string** |Nível de vídeo track. |
| **PixelFormat** |**xs: string** |Formato de pixel de vídeo track. |
| **Width**<br /><br /> Necessário |**xs:int** |Com a codificação de vídeo largura em pixels. |
| **Height**<br /><br /> Necessário |**xs:int** |Com a codificação de vídeo altura em pixels. |
| **DisplayAspectRatioNumerator**<br /><br /> Necessário |**xs: double** |Monitor de vídeo numerador de taxa de proporção. |
| **DisplayAspectRatioDenominator**<br /><br /> Necessário |**xs:double** |Denominador de taxa de proporção de monitor de vídeo. |
| **DisplayAspectRatioDenominator**<br /><br /> Necessário |**xs: double** |Numerador de taxa de proporção de exemplo de vídeo. |
| **SampleAspectRatioNumerator** |**xs: double** |Numerador de taxa de proporção de exemplo de vídeo. |
| **SampleAspectRatioNumerator** |**xs:double** |Denominador de taxa de proporção de exemplo de vídeo. |
| **FrameRate**<br /><br /> Necessário |**xs:decimal** |Taxa de quadros de vídeo no formato de .3f de medida. |
| **Velocidade de transmissão** |**xs:int** |Taxa de bits de vídeo de média de kilobits por segundo, conforme calculado a partir do ficheiro do elemento. Apenas o payload de stream elementares é contabilizado, e a sobrecarga de empacotamento não está incluída. |
| **MaxGOPBitrate** |**xs: int** |GOP máx. velocidade média para este Roteiro de vídeo, no kilobits por segundo. |
| **HasBFrames** |**xs:int** |Vídeo de controlar o número de fotogramas de B. |

## <a name="MetadataType"></a> MetadataType
**MetadataType** é um tipo complexo global que descreve os metadados de um ficheiro de elemento como cadeias de caracteres de chave/valor. Por exemplo, chave = "language" e o valor = "engenharia".  

Ver um exemplo XML no final deste artigo: [Exemplo XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atributos
| Name | Tipo | Descrição |
| --- | --- | --- |
| **chave**<br /><br /> Necessário |**xs:string** |A chave do par chave/valor. |
| **valor**<br /><br /> Necessário |**xs:string** |O valor do par chave/valor. |

## <a name="ProgramType"></a> ProgramType
**ProgramType** é um tipo complexo global que descreve um programa.  

### <a name="attributes"></a>Atributos
| Name | Tipo | Descrição |
| --- | --- | --- |
| **ProgramId**<br /><br /> Necessário |**xs:int** |Id do programa |
| **NumberOfPrograms**<br /><br /> Necessário |**xs:int** |Número de programas. |
| **PmtPid**<br /><br /> Necessário |**xs:int** |Tabelas de mapa do programa (PMTs) contêm informações sobre os programas.  Para obter mais informações, consulte [PMt](https://en.wikipedia.org/wiki/MPEG_transport_stream#PMT). |
| **PcrPid**<br /><br /> Necessário |**xs: int** |Utilizado pelo Decodificador. Para obter mais informações, consulte [PCR](https://en.wikipedia.org/wiki/MPEG_transport_stream#PCR) |
| **StartPTS** |**xs: long** |Carimbo de data / hora de apresentação inicial. |
| **EndPTS** |**xs: long** |Carimbo de hora do fim da apresentação. |

## <a name="StreamDispositionType"></a> StreamDispositionType
**StreamDispositionType** é um tipo complexo global que descreve o fluxo.  

Ver um exemplo XML no final deste artigo: [Exemplo XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atributos
| Name | Tipo | Descrição |
| --- | --- | --- |
| **Predefinição**<br /><br /> Necessário |**xs: int** |Defina este atributo como 1 para indicar que esta é a apresentação predefinida. |
| **Dub**<br /><br /> Necessário |**xs:int** |Defina este atributo como 1 para indicar que esta é a apresentação dubbed. |
| **Original**<br /><br /> Necessário |**xs: int** |Defina este atributo como 1 para indicar que esta é a apresentação original. |
| **Comentário**<br /><br /> Necessário |**xs:int** |Defina este atributo como 1 para indicar que este Roteiro contém comentários. |
| **Lyrics**<br /><br /> Necessário |**xs:int** |Defina este atributo como 1 para indicar que este Roteiro contém a letra de música. |
| **Karaokê**<br /><br /> Necessário |**xs:int** |Defina este atributo como 1 para indicar que isso representa a faixa de karaokê (música em segundo plano, sem vocals). |
| **Forçado**<br /><br /> Necessário |**xs:int** |Defina este atributo como 1 para indicar que esta é a apresentação forçada. |
| **HearingImpaired**<br /><br /> Necessário |**xs:int** |Defina este atributo como 1 para indicar que este Roteiro destina-se ouvir com deficiências visuais. |
| **VisualImpaired**<br /><br /> Necessário |**xs:int** |Defina este atributo como 1 para indicar este Roteiro para o deficiente. |
| **CleanEffects**<br /><br /> Necessário |**xs: int** |Defina este atributo como 1 para indicar que este Roteiro tem efeitos limpos. |
| **AttachedPic**<br /><br /> Necessário |**xs: int** |Defina este atributo como 1 para indicar que este Roteiro tem imagens. |

## <a name="Programs"></a> Elemento de programas
Elemento de invólucro que contém vários **programa** elementos.  

### <a name="child-elements"></a>Elementos subordinados
| Name | Tipo | Descrição |
| --- | --- | --- |
| **Programa**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[ProgramType](media-services-input-metadata-schema.md#ProgramType) |Para ficheiros de recursos que estão no formato de MPEG-TS, contém informações sobre programas no ficheiro do elemento. |

## <a name="VideoTracks"></a> Elemento de VideoTracks
 Elemento de invólucro que contém vários **VideoTrack** elementos.  

 Ver um exemplo XML no final deste artigo: [Exemplo XML](media-services-input-metadata-schema.md#xml).  

### <a name="child-elements"></a>Elementos subordinados
| Name | Tipo | Descrição |
| --- | --- | --- |
| **VideoTrack**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[VideoTrackType (que é herdeira de TrackType)](media-services-input-metadata-schema.md#VideoTrackType) |Contém informações sobre roteiros de vídeo no ficheiro do elemento. |

## <a name="AudioTracks"></a> Elemento de AudioTracks
 Elemento de invólucro que contém vários **AudioTrack** elementos.  

 Ver um exemplo XML no final deste artigo: [Exemplo XML](media-services-input-metadata-schema.md#xml).  

### <a name="elements"></a>Elementos
| Name | Tipo | Descrição |
| --- | --- | --- |
| **AudioTrack**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[AudioTrackType (que é herdeira de TrackType)](media-services-input-metadata-schema.md#AudioTrackType) |Contém informações sobre as faixas de áudio no ficheiro do elemento. |

## <a name="code"></a> Código de esquema
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


## <a name="xml"></a> Exemplo XML
Segue-se um exemplo do ficheiro de metadados de entrada.  

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

## <a name="next-steps"></a>Passos Seguintes
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

