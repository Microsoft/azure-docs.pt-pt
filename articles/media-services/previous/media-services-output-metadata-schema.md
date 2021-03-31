---
title: Azure Media Services produção de metadados | Microsoft Docs
description: Este artigo apresenta uma visão geral do esquema de metadados de saída da Azure Media Services.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 1ed84c88-eea5-4a24-9c4f-f2428157d08a
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 519aa158d60eae97eb4c1b792bcecfc8a6c066e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103008298"
---
# <a name="output-metadata"></a>Metadados de saída

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>Descrição Geral
Um trabalho de codificação está associado a um ativo de entrada (ou ativos) no qual pretende executar algumas tarefas de codificação. Por exemplo, codificar um ficheiro MP4 para conjuntos de bitrate adaptativos H.264 MP4; criar uma miniatura; criar sobreposições. Após a conclusão de uma tarefa, é produzido um ativo de saída.  O ativo de saída contém vídeo, áudio, miniaturas, etc. O ativo de saída também contém um ficheiro com metadados sobre o ativo de saída. O nome do ficheiro XML dos metadados tem o seguinte formato: &lt; source_file_name &gt;_manifest.xml (por exemplo, BigBuckBunny_manifest.xml).  

Os Serviços de Mídia não digitalizam preventivamente os ativos de entrada para gerar metadados. Os metadados de entrada são gerados apenas como um artefacto quando um Ativo de entrada é processado num Job. Daí que este artefacto seja escrito para o Ativo de saída. São utilizadas diferentes ferramentas para gerar metadados para ativos de entrada e ativos de saída. Portanto, os metadados de entrada têm um esquema ligeiramente diferente dos metadados de saída.

Se quiser examinar o ficheiro de metadados, pode criar um localizador **SAS** e transferir o ficheiro para o seu computador local.  

Este artigo discute os elementos e tipos do esquema XML no qual se baseia a saída metada &lt; (source_file_name &gt;_manifest.xml). Para obter informações sobre o ficheiro que contenha metadados sobre o ativo de entrada, consulte Os Metadados de Entrada.  

Pode encontrar o código de esquema completo e o exemplo XML no final deste artigo.  

## <a name="assetfiles-root-element"></a><a name="AssetFiles"></a> Elemento raiz do AssetFiles
Cobrança de entradas do AssetFile para o trabalho de codificação.  

### <a name="child-elements"></a>Elementos infantis
| Nome | Descrição |
| --- | --- |
| **Ficheiro de Ativos**<br/><br/> minOccurs="0" maxOccurs="1" |Um elemento AssetFile que faz parte da coleção AssetFiles. |

## <a name="assetfile-element"></a><a name="AssetFile"></a> Elemento AssetFile
Pode encontrar um [exemplo XML de exemplo XML.](#xml)  

### <a name="attributes"></a>Atributos
| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Nome**<br/><br/> Necessário |**xs:cadeia** |O nome do ficheiro de ativos da comunicação social. |
| **Tamanho**<br/><br/> minInclusive ="0"<br/><br/> Necessário |**xs:longo** |Tamanho do ficheiro de ativos em bytes. |
| **Duration**<br/><br/> Necessário |**xs:duração** |O conteúdo reproduz a duração. |

### <a name="child-elements"></a>Elementos infantis
| Nome | Descrição |
| --- | --- |
| **Origens** |Recolha de ficheiros de meios de entrada/fonte, que foram processados para produzir este AssetFile. Para mais informações, consulte o elemento Fonte. |
| **VideoTracks**<br/><br/> minOccurs="0" maxOccurs="1" |Cada AssetFile físico pode conter nele zero ou mais vídeos faixas intercaladas num formato de recipiente apropriado. Para mais informações, consulte o elemento VideoTracks. |
| **AudioTracks**<br/><br/> minOccurs="0" maxOccurs="1" |Cada ActivoFile físico pode conter nele zero ou mais faixas de áudio intercaladas num formato de recipiente apropriado. Esta é a coleção de todas aquelas faixas de áudio. Para obter mais informações, consulte o elemento AudioTracks. |

## <a name="sources-element"></a><a name="Sources"></a> Elemento de fontes
Recolha de ficheiros de meios de entrada/fonte, que foram processados para produzir este AssetFile.  

Pode encontrar um [exemplo XML de exemplo XML.](#xml)  

### <a name="child-elements"></a>Elementos infantis
| Nome | Descrição |
| --- | --- |
| **Origem**<br/><br/> minOccurs="1" maxOccurs="unbounded" |Um ficheiro de entrada/fonte utilizado ao gerar este ativo. Para mais informações, consulte o elemento Fonte. |

## <a name="source-element"></a><a name="Source"></a> Elemento de origem
Um ficheiro de entrada/fonte utilizado ao gerar este ativo.  

Pode encontrar um [exemplo XML de exemplo XML.](#xml)  

### <a name="attributes"></a>Atributos
| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Nome**<br/><br/> Necessário |**xs:cadeia** |Nome do ficheiro de origem de entrada. |

## <a name="videotracks-element"></a><a name="VideoTracks"></a> Elemento VideoTracks
Cada AssetFile físico pode conter nele zero ou mais vídeos faixas intercaladas num formato de recipiente apropriado. O elemento **VideoTracks** representa uma coleção de todas as faixas de vídeo.  

Pode encontrar um [exemplo XML de exemplo XML.](#xml)  

### <a name="child-elements"></a>Elementos infantis
| Nome | Descrição |
| --- | --- |
| **VideoTrack**<br/><br/> minOccurs="1" maxOccurs="unbounded" |Uma faixa de vídeo específica no ActivoFile. Para mais informações, consulte o elemento VideoTrack. |

## <a name="videotrack-element"></a><a name="VideoTrack"></a> Elemento VideoTrack
Uma faixa de vídeo específica no ActivoFile.  

Pode encontrar um [exemplo XML de exemplo XML.](#xml)  

### <a name="attributes"></a>Atributos
| Nome | Tipo | Description |
| --- | --- | --- |
| **ID**<br/><br/> minInclusive ="0"<br/><br/> Necessário |**xs:int** |Índice baseado em zero desta pista de vídeo. **Nota:**  Este **Id** não é necessariamente o TrackID usado num ficheiro MP4. |
| **FourCC**<br/><br/> Necessário |**xs:cadeia** |Código de vídeo Código QuatroCC. |
| **Perfil** |**xs:cadeia** |Perfil H264 (apenas aplicável ao código H264). |
| **Nível** |**xs:cadeia** |Nível H264 (apenas aplicável ao código H264). |
| **Width**<br/><br/> minInclusive ="0"<br/><br/> Necessário |**xs:int** |Largura de vídeo codificada em pixels. |
| **Height**<br/><br/> minInclusive ="0"<br/><br/> Necessário |**xs:int** |A altura de vídeo codificada em pixels. |
| **DisplayAspectRatioNumerator**<br/><br/> minInclusive ="0"<br/><br/> Necessário |**xs:duplo** |Numerador de aspeto de visualização de vídeo. |
| **DisplayAspectRatioDenominador**<br/><br/> minInclusive ="0"<br/><br/> Necessário |**xs:duplo** |Denominador de rácio de aspeto do visualização de vídeo. |
| **Taxa de fotogramas**<br/><br/> minInclusive ="0"<br/><br/> Necessário |**xs:decimal** |Taxa de fotogramas de vídeo medida em formato .3f. |
| **TargetFramerate**<br/><br/> minInclusive ="0"<br/><br/> Necessário |**xs:decimal** |Predefinição da taxa de fotogramas de vídeo-alvo em formato .3f. |
| **Bitrate**<br/><br/> minInclusive ="0"<br/><br/> Necessário |**xs:int** |Taxa média de bits de vídeo em quilobitos por segundo, calculada a partir do AssetFile. Conta apenas a carga de fluxo elementar, e não inclui a sobrecarga da embalagem. |
| **TargetBitrate**<br/><br/> minInclusive ="0"<br/><br/> Necessário |**xs:int** |Bitrate médio-alvo para esta pista de vídeo, conforme solicitado através da predefinição codificante, em quilobits por segundo. |
| **MaxGOPBitrate**<br/><br/> minInclusive ="0" |**xs:int** |Taxa média de max GOP para esta faixa de vídeo, em quilobits por segundo. |

## <a name="audiotracks-element"></a><a name="AudioTracks"></a> Elemento AudioTracks
Cada ActivoFile físico pode conter nele zero ou mais faixas de áudio intercaladas num formato de recipiente apropriado. O elemento **AudioTracks** representa uma coleção de todas aquelas faixas de áudio.  

Pode encontrar um [exemplo XML de exemplo XML.](#xml)  

### <a name="child-elements"></a>Elementos infantis
| Nome | Descrição |
| --- | --- |
| **AudioTrack**<br/><br/> minOccurs="1" maxOccurs="unbounded" |Uma faixa áudio específica no ActivoFile principal. Para obter mais informações, consulte o elemento AudioTrack. |

## <a name="audiotrack-element"></a><a name="AudioTrack"></a> Elemento AudioTrack
Uma faixa áudio específica no ActivoFile principal.  

Pode encontrar um [exemplo XML de exemplo XML.](#xml)  

### <a name="attributes"></a>Atributos
| Nome | Tipo | Description |
| --- | --- | --- |
| **ID**<br/><br/> minInclusive ="0"<br/><br/> Necessário |**xs:int** |Índice baseado em zero desta faixa de áudio. **Nota:**  Este não é necessariamente o TrackID usado num ficheiro MP4. |
| **Codec** |**xs:cadeia** |Cadeia de codec de faixa de áudio. |
| **EncoderVersão** |**xs:cadeia** |Cadeia de versão de codificação opcional, necessária para OAC3. |
| **Channels** (Canais)<br/><br/> minInclusive ="0"<br/><br/> Necessário |**xs:int** |Número de canais de áudio. |
| **AmostragemRate**<br/><br/> minInclusive ="0"<br/><br/> Necessário |**xs:int** |Taxa de amostragem de áudio em amostras/seg ou Hz. |
| **Bitrate**<br/><br/> minInclusive ="0"<br/><br/> Necessário |**xs:int** |Taxa média de bits de áudio em bits por segundo, calculada a partir do AssetFile. Conta apenas a carga de fluxo elementar, e não inclui a sobrecarga da embalagem. |
| **BitsPerSample**<br/><br/> minInclusive ="0"<br/><br/> Necessário |**xs:int** |Bits por amostra para o tipo de formato wFormatTag. |

### <a name="child-elements"></a>Elementos infantis
| Nome | Descrição |
| --- | --- |
| **LoudnessMeteringParameters**<br/><br/> minOccurs="0" maxOccurs="1" |Parâmetros de resultado de medição de ruído alto. Para obter mais informações, consulte o elemento LoudnessMeteringResultParameters. |

## <a name="loudnessmeteringresultparameters-element"></a><a name="LoudnessMeteringResultParameters"></a> Elemento LoudnessMeteringResultParameters
Parâmetros de resultado de medição de ruído alto.  

Pode encontrar um [exemplo XML de exemplo XML.](#xml)  

### <a name="attributes"></a>Atributos
| Nome | Tipo | Description |
| --- | --- | --- |
| **DPLMVersionInformation** |**xs:cadeia** |Versão do kit de desenvolvimento de medidores de alto nível do **dolby** profissional. |
| **DiálogoNormalização**<br/><br/> minInclusive="-31" maxInclusive="-1"<br/><br/> Necessário |**xs:int** |DiálogoNormalização gerada através do DPLM, necessária quando o LoudnessMetering é definido |
| **IntegradoLoudness**<br/><br/> minInclusive="-70" maxInclusive="10"<br/><br/> Necessário |**xs:flutuar** |Ruído integrado |
| **Unidade Integrada de Iloudness**<br/><br/> Necessário |**xs:cadeia** |Unidade integrada de ruído. |
| **IntegradoLoudnessGatingMethod**<br/><br/> Necessário |**xs:cadeia** |Identificador gating |
| **IntegradoLoudnessSpeechPercentage**<br/><br/> minInclusive ="0" maxInclusive="100" |**xs:flutuar** |Conteúdo da fala sobre o programa, em percentagem. |
| **SamplePeak**<br/><br/> Necessário |**xs:flutuar** |Valor máximo da amostra absoluta, desde o reset ou desde que foi limpo pela última vez, por canal.  As unidades são dBFS. |
| **SamplePeakunit**<br/><br/> fixo="dBFS"<br/><br/> Necessário |**xs:anySimpleType** |Unidade de pico de amostra. |
| **TruePeak**<br/><br/> Necessário |**xs:flutuar** |Valor máximo de pico verdadeiro, de acordo com a ITU-R BS.1770-2, desde o reset ou desde que foi limpo pela última vez, por canal. As unidades são dBTP. |
| **TruePeakunit**<br/><br/> fixed="dBTP"<br/><br/> Necessário |**xs:anySimpleType** |Verdadeira unidade de pico. |

## <a name="schema-code"></a>Código Schema
```xml
<?xml version="1.0" encoding="utf-8"?>  
<xs:schema xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns:msdata="urn:schemas-microsoft-com:xml-msdata" version="1.2"  
            xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2013/05/mediaencoder/metadata"  
            targetNamespace="http://schemas.microsoft.com/windowsazure/mediaservices/2013/05/mediaencoder/metadata"  
            elementFormDefault="qualified">  
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
              <xs:element name="Sources">  
                <xs:annotation>  
                  <xs:documentation>Collection of input/source media files, that was processed in order to produce this AssetFile</xs:documentation>  
                </xs:annotation>  
                <xs:complexType>  
                  <xs:sequence>  
                    <xs:element name="Source" minOccurs="1" maxOccurs="unbounded">  
                      <xs:annotation>  
                        <xs:documentation>An input/source file used when generating this asset</xs:documentation>  
                      </xs:annotation>  
                      <xs:complexType>  
                        <xs:attribute name="Name" type="xs:string" use="required">  
                          <xs:annotation>  
                            <xs:documentation>input source file name</xs:documentation>  
                          </xs:annotation>  
                        </xs:attribute>  
                      </xs:complexType>  
                    </xs:element>  
                  </xs:sequence>  
                </xs:complexType>  
              </xs:element>  
              <xs:element name="VideoTracks" minOccurs="0">  
                <xs:annotation>  
                  <xs:documentation>Each physical AssetFile can contain in it zero or more video tracks interleaved into an appropriate container format. This is the collection of all those video tracks</xs:documentation>  
                </xs:annotation>  
                <xs:complexType>  
                  <xs:sequence>  
                    <xs:element name="VideoTrack" maxOccurs="unbounded">  
                      <xs:annotation>  
                        <xs:documentation>A specific video track in the parent AssetFile</xs:documentation>  
                      </xs:annotation>  
                      <xs:complexType>  
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
                        <xs:attribute name="FourCC" type="xs:string" use="required">  
                          <xs:annotation>  
                            <xs:documentation>video codec FourCC code</xs:documentation>  
                          </xs:annotation>  
                        </xs:attribute>  
                        <xs:attribute name="Profile" type="xs:string">  
                          <xs:annotation>  
                            <xs:documentation>H264 profile (only appliable for H264 codec)</xs:documentation>  
                          </xs:annotation>  
                        </xs:attribute>  
                        <xs:attribute name="Level" type="xs:string">  
                          <xs:annotation>  
                            <xs:documentation>H264 level (only appliable for H264 codec)</xs:documentation>  
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
                        <xs:attribute name="Framerate" use="required">  
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
                        <xs:attribute name="TargetFramerate" use="required">  
                          <xs:annotation>  
                            <xs:documentation>preset target video frame rate in .3f format</xs:documentation>  
                          </xs:annotation>  
                          <xs:simpleType>  
                            <xs:restriction base="xs:decimal">  
                              <xs:minInclusive value="0"/>  
                              <xs:fractionDigits value="3"/>  
                            </xs:restriction>  
                          </xs:simpleType>  
                        </xs:attribute>  
                        <xs:attribute name="Bitrate" use="required">  
                          <xs:annotation>  
                            <xs:documentation>average video bit rate in kilobits per second, as calculated from the AssetFile. Counts only the elementary stream payload, and does not include the packaging overhead</xs:documentation>  
                          </xs:annotation>  
                          <xs:simpleType>  
                            <xs:restriction base="xs:int">  
                              <xs:minInclusive value="0"/>  
                            </xs:restriction>  
                          </xs:simpleType>  
                        </xs:attribute>  
                        <xs:attribute name="TargetBitrate" use="required">  
                          <xs:annotation>  
                            <xs:documentation>target average bitrate for this video track, as requested via the encoding preset, in kilobits per second</xs:documentation>  
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
                      </xs:complexType>  
                    </xs:element>  
                  </xs:sequence>  
                </xs:complexType>  
              </xs:element>  
              <xs:element name="AudioTracks" minOccurs="0">  
                <xs:annotation>  
                  <xs:documentation>each physical AssetFile can contain in it zero or more audio tracks interleaved into an appropriate container format. This is the collection of all those audio tracks</xs:documentation>  
                </xs:annotation>  
                 <xs:complexType>  
                  <xs:sequence>  
                    <xs:element name="AudioTrack" maxOccurs="unbounded">  
                      <xs:annotation>  
                        <xs:documentation>a specific audio track in the parent AssetFile</xs:documentation>  
                      </xs:annotation>  
                      <xs:complexType>  
                        <xs:sequence>  
                          <xs:element name="LoudnessMeteringResultParameters" minOccurs="0" maxOccurs="1">  
                            <xs:annotation>  
                              <xs:documentation>Loudness Metering Result Parameters</xs:documentation>  
                            </xs:annotation>  
                            <xs:complexType>  
                              <xs:attribute name="DPLMVersionInformation" type="xs:string">  
                                <xs:annotation>  
                                  <xs:documentation>Dolby Professional Loudness Metering Development Kit Version</xs:documentation>  
                                </xs:annotation>  
                              </xs:attribute>  
                              <xs:attribute name="DialogNormalization" use="required">  
                                <xs:annotation>  
                                  <xs:documentation> DialogNormalization generated through DPLM, required when LoudnessMetering is set</xs:documentation>  
                                </xs:annotation>  
                                <xs:simpleType>  
                                  <xs:restriction base="xs:int">  
                                    <xs:minInclusive value="-31"/>  
                                    <xs:maxInclusive value="-1"/>  
                                  </xs:restriction>  
                                </xs:simpleType>  
                              </xs:attribute>  
                              <xs:attribute name="IntegratedLoudness" use="required">  
                                <xs:annotation>  
                                  <xs:documentation>Integrated loudness</xs:documentation>  
                                </xs:annotation>  
                                <xs:simpleType>  
                                  <xs:restriction base="xs:float">  
                                    <xs:minInclusive value="-70"/>  
                                    <xs:maxInclusive value="10"/>  
                                  </xs:restriction>  
                                </xs:simpleType>  
                              </xs:attribute>  
                              <xs:attribute name="IntegratedLoudnessUnit" use="required" type="xs:string">  
                              </xs:attribute>  
                              <xs:attribute name="IntegratedLoudnessGatingMethod" use="required" type="xs:string">  
                                <xs:annotation>  
                                  <xs:documentation>Gating identifier</xs:documentation>  
                                </xs:annotation>  
                              </xs:attribute>  
                              <xs:attribute name="IntegratedLoudnessSpeechPercentage">  
                                <xs:annotation>  
                                  <xs:documentation>Speech content over the program, as a percentage.</xs:documentation>  
                                </xs:annotation>  
                                <xs:simpleType>  
                                  <xs:restriction base="xs:float">  
                                    <xs:minInclusive value="0"/>  
                                    <xs:maxInclusive value="100"/>  
                                  </xs:restriction>  
                                </xs:simpleType>  
                              </xs:attribute>  
                              <xs:attribute name="SamplePeak" use="required" type="xs:float">  
                                <xs:annotation>  
                                  <xs:documentation>Peak absolute sample value, since reset or since it was last cleared, per channel.  Units are dBFS.</xs:documentation>  
                                </xs:annotation>  
                              </xs:attribute>  
                              <xs:attribute name="SamplePeakUnit" use="required" fixed="dBFS">  
                              </xs:attribute>  
                              <xs:attribute name="TruePeak" use="required" type="xs:float">  
                                <xs:annotation>  
                                  <xs:documentation>Maximum True Peak value, as per ITU-R BS.1770-2, since reset or since it was last cleared, per channel.  Units are dBTP.</xs:documentation>  
                                </xs:annotation>  
                              </xs:attribute>  
                              <xs:attribute name="TruePeakUnit" use="required" fixed="dBTP">  
                              </xs:attribute>  
                            </xs:complexType>  
                          </xs:element>  
                        </xs:sequence>  
                        <xs:attribute name="Id" use="required">  
                          <xs:annotation>  
                            <xs:documentation>zero-based index of this audio track. Note: this is not necessarily the TrackID as used in an MP4 file</xs:documentation>  
                          </xs:annotation>  
                          <xs:simpleType>  
                            <xs:restriction base="xs:int">  
                              <xs:minInclusive value="0"/>  
                            </xs:restriction>  
                          </xs:simpleType>  
                        </xs:attribute>  
                        <xs:attribute name="Codec" type="xs:string">  
                          <xs:annotation>  
                            <xs:documentation>audio track codec string</xs:documentation>  
                          </xs:annotation>  
                        </xs:attribute>  
                        <xs:attribute name="EncoderVersion" type="xs:string">  
                          <xs:annotation>  
                            <xs:documentation>optional encoder version string, required for EAC3</xs:documentation>  
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
                        <xs:attribute name="Bitrate" use="required">  
                          <xs:annotation>  
                            <xs:documentation>average audio bit rate in bits per second, as calculated from the AssetFile. Counts only the elementary stream payload, and does not include the packaging overhead</xs:documentation>  
                          </xs:annotation>  
                          <xs:simpleType>  
                            <xs:restriction base="xs:int">  
                              <xs:minInclusive value="0"/>  
                            </xs:restriction>  
                          </xs:simpleType>  
                        </xs:attribute>  
                        <xs:attribute name="BitsPerSample" use="required">  
                          <xs:annotation>  
                            <xs:documentation>Bits per sample for the wFormatTag format type</xs:documentation>  
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
            <xs:attribute name="Duration" use="required">  
              <xs:annotation>  
                <xs:documentation>content play back duration</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:duration"/>  
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

O seguinte XML é um exemplo do ficheiro de metadados de saída.  

```xml
<AssetFiles xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="https://www.w3.org/2001/XMLSchema"   
            xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2013/05/mediaencoder/metadata">  
  <AssetFile Name="BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4" Size="4646283" Duration="PT8.4288444S">  
    <Sources>  
      <Source Name="BigBuckBunny.mp4"/>  
    </Sources>  
    <VideoTracks>  
      <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.2" Width="1280" Height="720" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="4250" TargetBitrate="3400" MaxGOPBitrate="5514"/>  
    </VideoTracks>  
    <AudioTracks>  
      <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
    </AudioTracks>  
  </AssetFile>  
  <AssetFile Name="BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4" Size="3166728" Duration="PT8.4288444S">  
    <Sources>  
      <Source Name="BigBuckBunny.mp4"/>  
    </Sources>  
      <VideoTracks>  
      <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.1" Width="960" Height="540" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="2846" TargetBitrate="2250" MaxGOPBitrate="3630"/>  
    </VideoTracks>  
    <AudioTracks>  
      <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
    </AudioTracks>  
  </AssetFile>  
  <AssetFile Name="BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4" Size="2205095" Duration="PT8.4288444S">  
    <Sources>  
      <Source Name="BigBuckBunny.mp4"/>  
    </Sources>  
    <VideoTracks>  
      <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.1" Width="960" Height="540" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="1932" TargetBitrate="1500" MaxGOPBitrate="2513"/>  
    </VideoTracks>  
    <AudioTracks>  
      <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
    </AudioTracks>  
  </AssetFile>  
  <AssetFile Name="BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4" Size="1508567" Duration="PT8.4288444S">  
    <Sources>  
      <Source Name="BigBuckBunny.mp4"/>  
    </Sources>  
    <VideoTracks>  
      <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.0" Width="640" Height="360" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="1271" TargetBitrate="1000" MaxGOPBitrate="1527"/>  
    </VideoTracks>  
    <AudioTracks>  
      <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
    </AudioTracks>  
  </AssetFile>  
  <AssetFile Name="BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4" Size="1057155" Duration="PT8.4288444S">  
    <Sources>  
      <Source Name="BigBuckBunny.mp4"/>  
    </Sources>  
    <VideoTracks>  
      <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.0" Width="640" Height="360" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="843" TargetBitrate="650" MaxGOPBitrate="1086"/>  
    </VideoTracks>  
    <AudioTracks>  
      <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
    </AudioTracks>  
  </AssetFile>  
  <AssetFile Name="BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4" Size="699262" Duration="PT8.4288444S">  
    <Sources>  
      <Source Name="BigBuckBunny.mp4"/>  
    </Sources>  
    <VideoTracks>  
      <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="1.3" Width="320" Height="180" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="503" TargetBitrate="400" MaxGOPBitrate="661"/>  
    </VideoTracks>  
    <AudioTracks>  
      <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
    </AudioTracks>  
  </AssetFile>  
  <AssetFile Name="BigBuckBunny_AAC_und_ch2_96kbps.mp4" Size="166780" Duration="PT8.4288444S">  
    <Sources>  
      <Source Name="BigBuckBunny.mp4"/>  
    </Sources>  
    <AudioTracks>  
      <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
    </AudioTracks>  
  </AssetFile>  
  <AssetFile Name="BigBuckBunny_AAC_und_ch2_56kbps.mp4" Size="124576" Duration="PT8.4288444S">  
    <Sources>  
      <Source Name="BigBuckBunny.mp4"/>  
    </Sources>  
    <AudioTracks>  
      <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="53" BitsPerSample="16"/>  
    </AudioTracks>  
  </AssetFile>  
</AssetFiles>  
```

## <a name="next-steps"></a>Passos seguintes
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
