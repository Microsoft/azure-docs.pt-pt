---
title: Azure Media Services introduz esquema de metadados | Microsoft Docs
description: Este artigo apresenta uma visão geral do esquema de metadados de entrada da Azure Media Services.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: eebe1c77c9af6791be94c16f0f792a9e7deb9a6e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103017414"
---
# <a name="input-metadata"></a>Inserir Metadados

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Um trabalho de codificação está associado a um ativo de entrada (ou ativos) no qual pretende executar algumas tarefas de codificação.  Após a conclusão de uma tarefa, é produzido um ativo de saída.  O ativo de saída contém vídeo, áudio, miniaturas, manifesto, etc. O ativo de saída também contém um ficheiro com metadados sobre o ativo de entrada. O nome do ficheiro XML dos metadados tem o seguinte formato: &lt; asset_id &gt;_metadata.xml (por exemplo, 41114ad3-eb5e-4c57-8d92-5354e2b7d4a4_metadata.xml), onde &lt; asset_id é o valor &gt; AssetId do ativo de entrada.  

Os Serviços de Mídia não digitalizam preventivamente os ativos de entrada para gerar metadados. Os metadados de entrada são gerados apenas como um artefacto quando um Ativo de entrada é processado num Job. Daí que este artefacto seja escrito para o Ativo de saída. São utilizadas diferentes ferramentas para gerar metadados para ativos de entrada e ativos de saída. Portanto, os metadados de entrada têm um esquema ligeiramente diferente dos metadados de saída.

Se quiser examinar o ficheiro de metadados, pode criar um localizador **SAS** e transferir o ficheiro para o seu computador local. Pode encontrar um exemplo sobre como criar um localizador SAS e descarregar um ficheiro [Utilizando as extensões Net SDK dos Serviços de Comunicação Social .](media-services-dotnet-get-started.md)  

Este artigo discute os elementos e tipos do esquema XML no qual se baseia a entrada metada &lt; (asset_id &gt;_metadata.xml).  Para obter informações sobre o ficheiro que contém metadados sobre o ativo de saída, consulte [os Metadados de Saída](media-services-output-metadata-schema.md).  

Pode encontrar o [Código Schema](media-services-input-metadata-schema.md#code) como [exemplo XML](media-services-input-metadata-schema.md#xml) no final deste artigo.  
 

## <a name="assetfiles-element-root-element"></a><a name="AssetFiles"></a> Elemento De Ficheiros de Ativos (elemento raiz)
Contém uma coleção de [elementos AssetFile](media-services-input-metadata-schema.md#AssetFile)s para o trabalho de codificação.  

Veja um exemplo XML no final deste artigo: [exemplo XML](media-services-input-metadata-schema.md#xml).  

| Nome | Descrição |
| --- | --- |
| **Ficheiro de Ativos**<br /><br /> minOccurs="1" maxOccurs="unbounded" |Um único elemento infantil. Para obter mais informações, consulte [o elemento AssetFile](media-services-input-metadata-schema.md#AssetFile). |

## <a name="assetfile-element"></a><a name="AssetFile"></a> Elemento AssetFile
 Contém atributos e elementos que descrevem um ficheiro de ativos.  

 Veja um exemplo XML no final deste artigo: [exemplo XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atributos
| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Nome**<br /><br /> Necessário |**xs:cadeia** |Nome do ficheiro do ativo. |
| **Tamanho**<br /><br /> Necessário |**xs:longo** |Tamanho do ficheiro de ativos em bytes. |
| **Duration**<br /><br /> Necessário |**xs:duração** |O conteúdo reproduz a duração. Exemplo: Duração="PT25M37.757S". |
| **NúmeroOfStreams**<br /><br /> Necessário |**xs:int** |Número de streams no ficheiro do ativo. |
| **FormatoNames**<br /><br /> Necessário |**xs: corda** |Nomes de formato. |
| **FormatoVerboseNames**<br /><br /> Necessário |**xs: corda** |Formato verbose nomes. |
| **Horário de início** |**xs:duração** |Hora de início de conteúdo. Exemplo: StartTime="PT2.669S". |
| **OverallBitRate** |**xs: int** |Bitrate médio do ficheiro do ativo em kbps. |

> [!NOTE]
> Os quatro elementos infantis seguintes devem aparecer numa sequência.  
> 
> 

### <a name="child-elements"></a>Elementos infantis
| Nome | Tipo | Description |
| --- | --- | --- |
| **Programas**<br /><br /> minOccurs="0" | |Recolha de todos os [elementos programos](media-services-input-metadata-schema.md#Programs) quando o ficheiro do ativo está no formato MPEG-TS. |
| **VideoTracks**<br /><br /> minOccurs="0" | |Cada ficheiro de ativo físico pode conter faixas de vídeos zero ou mais intercaladas num formato de recipiente apropriado. Este elemento contém uma coleção de todos os [VideoTracks](media-services-input-metadata-schema.md#VideoTracks) que fazem parte do ficheiro do ativo. |
| **AudioTracks**<br /><br /> minOccurs="0" | |Cada ficheiro de ativo físico pode conter zero ou mais faixas de áudio intercaladas num formato de recipiente apropriado. Este elemento contém uma coleção de todos os [AudioTracks](media-services-input-metadata-schema.md#AudioTracks) que fazem parte do ficheiro do ativo. |
| **Metadados**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[MetadadosType](media-services-input-metadata-schema.md#MetadataType) |Os metadados do ficheiro do ativo representados como cordas de valor chave\valor. Por exemplo:<br /><br /> **&lt;Chave de metadados="linguagem" valor="eng" /&gt;** |

## <a name="tracktype"></a><a name="TrackType"></a> TrackType
Veja um exemplo XML no final deste artigo: [exemplo XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atributos
| Nome | Tipo | Description |
| --- | --- | --- |
| **ID**<br /><br /> Necessário |**xs:int** |Índice baseado em zero desta faixa de áudio ou vídeo.<br /><br /> Isto não é necessariamente o TrackID usado num ficheiro MP4. |
| **Codec** |**xs:cadeia** |Cadeia codec de pista de vídeo. |
| **Nome codecLong** |**xs: corda** |Nome codec de linha de áudio ou vídeo. |
| **TimeBase**<br /><br /> Necessário |**xs:cadeia** |Base do tempo. Exemplo: TimeBase="1/48000" |
| **Números DeFrames** |**xs:int** |Número de quadros (presente para faixas de vídeo). |
| **Horário de início** |**xs: duração** |Hora de início da pista. Exemplo: StartTime="PT2.669S" |
| **Duration** |**xs:duração** |Duração da pista. Exemplo: Duração="PTSampleForm em M37.757S". |

> [!NOTE]
> Os dois elementos da criança seguintes devem aparecer numa sequência.  
> 
> 

### <a name="child-elements"></a>Elementos infantis
| Nome | Tipo | Description |
| --- | --- | --- |
| **Disposição**<br /><br /> minOccurs="0" maxOccurs="1" |[StreamDispositionType](media-services-input-metadata-schema.md#StreamDispositionType) |Contém informações de apresentação (por exemplo, se uma determinada faixa de áudio é para espectadores com deficiência visual). |
| **Metadados**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[MetadadosType](media-services-input-metadata-schema.md#MetadataType) |Cordas genéricas/valor que podem ser usadas para conter uma variedade de informações. Por exemplo, key="language", e value="eng". |

## <a name="audiotracktype-inherits-from-tracktype"></a><a name="AudioTrackType"></a> AudioTrackType (herda do TrackType)
 **AudioTrackType** é um tipo complexo global que herda a partir de [TrackType](media-services-input-metadata-schema.md#TrackType).  

 O tipo representa uma faixa áudio específica no ficheiro do ativo.  

 Veja um exemplo XML no final deste artigo: [exemplo XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atributos
| Nome | Tipo | Description |
| --- | --- | --- |
| **SampleFormat** |**xs:cadeia** |Formato de amostra. |
| **ChannelLayout** |**xs: corda** |Disposição do canal. |
| **Channels** (Canais)<br /><br /> Necessário |**xs:int** |Número (0 ou mais) de canais de áudio. |
| **AmostragemRate**<br /><br /> Necessário |**xs:int** |Taxa de amostragem de áudio em amostras/seg ou Hz. |
| **Bitrate** |**xs:int** |Taxa média de bits de áudio em bits por segundo, calculada a partir do ficheiro do ativo. Apenas a carga útil elementar é contada, e a sobrecarga da embalagem não está incluída nesta contagem. |
| **BitsPerSample** |**xs:int** |Bits por amostra para o tipo de formato wFormatTag. |

## <a name="videotracktype-inherits-from-tracktype"></a><a name="VideoTrackType"></a> VideoTrackType (herda do TrackType)
**VideoTrackType** é um tipo complexo global que herda a partir de [TrackType](media-services-input-metadata-schema.md#TrackType).  

O tipo representa uma faixa de vídeo específica no ficheiro do ativo.  

Veja um exemplo XML no final deste artigo: [exemplo XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atributos
| Nome | Tipo | Description |
| --- | --- | --- |
| **FourCC**<br /><br /> Necessário |**xs:cadeia** |Código de vídeo Código QuatroCC. |
| **Perfil** |**xs: corda** |Perfil da pista de vídeo. |
| **Nível** |**xs: corda** |Nível de pista de vídeo. |
| **PixelFormat** |**xs: corda** |O formato pixel da pista de vídeo. |
| **Width**<br /><br /> Necessário |**xs:int** |Largura de vídeo codificada em pixels. |
| **Height**<br /><br /> Necessário |**xs:int** |A altura de vídeo codificada em pixels. |
| **DisplayAspectRatioNumerator**<br /><br /> Necessário |**xs: duplo** |Numerador de aspeto de visualização de vídeo. |
| **DisplayAspectRatioDenominador**<br /><br /> Necessário |**xs:duplo** |Denominador de rácio de aspeto do visualização de vídeo. |
| **DisplayAspectRatioDenominador**<br /><br /> Necessário |**xs: duplo** |Numerador de aspeto da amostra de vídeo. |
| **SampleAspectRatioNumerator** |**xs: duplo** |Numerador de aspeto da amostra de vídeo. |
| **SampleAspectRatioNumerator** |**xs:duplo** |Denominador de rácio de aspeto da amostra de vídeo. |
| **FrameRate**<br /><br /> Necessário |**xs:decimal** |Taxa de fotogramas de vídeo medida em formato .3f. |
| **Bitrate** |**xs:int** |Taxa média de bits de vídeo em quilobitos por segundo, conforme calculado a partir do ficheiro do ativo. Apenas a carga útil elementar é contada, e a sobrecarga da embalagem não está incluída. |
| **MaxGOPBitrate** |**xs: int** |Taxa média de max GOP para esta faixa de vídeo, em quilobits por segundo. |
| **HasBFrames** |**xs:int** |Número de vídeo de quadros B. |

## <a name="metadatatype"></a><a name="MetadataType"></a> MetadadosType
**MetadadosType** é um tipo complexo global que descreve metadados de um ficheiro de ativos como cordas chave/valor. Por exemplo, key="language", e value="eng".  

Veja um exemplo XML no final deste artigo: [exemplo XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atributos
| Nome | Tipo | Description |
| --- | --- | --- |
| **chave**<br /><br /> Necessário |**xs:cadeia** |A chave no par chave/valor. |
| **value**<br /><br /> Necessário |**xs:cadeia** |O valor no par chave/valor. |

## <a name="programtype"></a><a name="ProgramType"></a> ProgramType
**ProgramType** é um tipo complexo global que descreve um programa.  

### <a name="attributes"></a>Atributos
| Nome | Tipo | Description |
| --- | --- | --- |
| **Programid**<br /><br /> Necessário |**xs:int** |Id do programa |
| **NúmerosProgramas**<br /><br /> Necessário |**xs:int** |Número de programas. |
| **PmtPid**<br /><br /> Necessário |**xs:int** |As tabelas de mapas do programa (PMTs) contêm informações sobre programas.  Para mais informações, consulte [PMt.](https://en.wikipedia.org/wiki/MPEG_transport_stream#PMT) |
| **PcrPid**<br /><br /> Necessário |**xs: int** |Usado por descodificador. Para mais informações, consulte [PCR](https://en.wikipedia.org/wiki/MPEG_transport_stream#PCR) |
| **StartPTS** |**xs: longo** |A começar a marca de tempo de apresentação. |
| **EndPTS** |**xs: longo** |Fim da hora de apresentação. |

## <a name="streamdispositiontype"></a><a name="StreamDispositionType"></a> StreamDispositionType
**StreamDispositionType** é um tipo complexo global que descreve o fluxo.  

Veja um exemplo XML no final deste artigo: [exemplo XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atributos
| Nome | Tipo | Description |
| --- | --- | --- |
| **Predefinição**<br /><br /> Necessário |**xs: int** |Desafine este atributo para 1 para indicar que esta é a apresentação predefinida. |
| **Dub**<br /><br /> Necessário |**xs:int** |Desapeda fique com este atributo para 1 para indicar que esta é a apresentação apelidada. |
| **Original**<br /><br /> Necessário |**xs: int** |Desa esta versão para 1 para indicar que esta é a apresentação original. |
| **Comentário**<br /><br /> Necessário |**xs:int** |Desa esta definição de atributo para 1 para indicar que esta faixa contém comentários. |
| **Letra**<br /><br /> Necessário |**xs:int** |Desa esta definição de atributo para 1 para indicar que esta faixa contém letras. |
| **Karaoke**<br /><br /> Necessário |**xs:int** |Desa esta atribuição a 1 para indicar que isto representa a faixa de karaoke (música de fundo, sem voz). |
| **Forçado**<br /><br /> Necessário |**xs:int** |Desa esta versão para 1 para indicar que esta é a apresentação forçada. |
| **HearingImpaired**<br /><br /> Necessário |**xs:int** |Desa parte deste atributo para 1 para indicar que esta faixa é para pessoas que são difíceis de ouvir. |
| **Visualimpaired**<br /><br /> Necessário |**xs:int** |Deite este atributo a 1 para indicar que esta faixa é para deficientes visuais. |
| **CleanEffects**<br /><br /> Necessário |**xs: int** |Desave este atributo a 1 para indicar que esta faixa tem efeitos limpos. |
| **Pic anexado**<br /><br /> Necessário |**xs: int** |Desa esta definição deste atributo para 1 para indicar que esta faixa tem imagens. |

## <a name="programs-element"></a><a name="Programs"></a> Elemento de programas
Elemento de invólucro segurando vários elementos **do Programa.**  

### <a name="child-elements"></a>Elementos infantis
| Nome | Tipo | Description |
| --- | --- | --- |
| **Programa**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[ProgramType](media-services-input-metadata-schema.md#ProgramType) |Para ficheiros de ativos que se encontram no formato MPEG-TS, contém informações sobre programas no ficheiro do ativo. |

## <a name="videotracks-element"></a><a name="VideoTracks"></a> Elemento VideoTracks
 Elemento de invólucro segurando vários elementos **videoTrack.**  

 Veja um exemplo XML no final deste artigo: [exemplo XML](media-services-input-metadata-schema.md#xml).  

### <a name="child-elements"></a>Elementos infantis
| Nome | Tipo | Description |
| --- | --- | --- |
| **VideoTrack**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[VideoTrackType (herda do TrackType)](media-services-input-metadata-schema.md#VideoTrackType) |Contém informações sobre faixas de vídeo no ficheiro do ativo. |

## <a name="audiotracks-element"></a><a name="AudioTracks"></a> Elemento AudioTracks
 Elemento de invólucro que mantém vários elementos **AudioTrack.**  

 Veja um exemplo XML no final deste artigo: [exemplo XML](media-services-input-metadata-schema.md#xml).  

### <a name="elements"></a>elementos
| Nome | Tipo | Description |
| --- | --- | --- |
| **AudioTrack**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[AudioTrackType (herda do TrackType)](media-services-input-metadata-schema.md#AudioTrackType) |Contém informações sobre faixas de áudio no ficheiro do ativo. |

## <a name="schema-code"></a><a name="code"></a> Código Schema
```xml
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
```


## <a name="xml-example"></a><a name="xml"></a> Exemplo XML
Segue-se um exemplo do ficheiro de metadados input.  

```xml
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
```

## <a name="next-steps"></a>Passos seguintes
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

