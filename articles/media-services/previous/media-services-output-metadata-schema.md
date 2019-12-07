---
title: Esquema de metadados de saída dos serviços de mídia do Azure | Microsoft Docs
description: Este artigo fornece uma visão geral do esquema de metadados de saída dos serviços de mídia do Azure.
author: Juliako
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
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 3f0c6b60e2be625d1f869c3eda4acb9dfd3c6e9e
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74886817"
---
# <a name="output-metadata"></a>Metadados de saída
## <a name="overview"></a>Visão geral
Um trabalho de codificação é associado a um ativo de entrada (ou ativos) no qual você deseja executar algumas tarefas de codificação. Por exemplo, codifique um arquivo MP4 para conjuntos de taxa de bits adaptável MP4 H. 264; criar uma miniatura; criar sobreposições. Após a conclusão de uma tarefa, um ativo de saída é produzido.  O ativo de saída contém vídeo, áudio, miniaturas, etc. O ativo de saída também contém um arquivo com metadados sobre o ativo de saída. O nome do arquivo XML de metadados tem o seguinte formato: &lt;source_file_name&gt;_manifest. XML (por exemplo, BigBuckBunny_manifest. xml).  

Os serviços de mídia não preventivamentem a verificação de ativos de entrada para gerar metadados. Os metadados de entrada são gerados somente como um artefato quando um ativo de entrada é processado em um trabalho. Portanto, esse artefato é gravado no ativo de saída. Ferramentas diferentes são usadas para gerar metadados para ativos de entrada e ativos de saída. Portanto, os metadados de entrada têm um esquema um pouco diferente dos metadados de saída.

Se você quiser examinar o arquivo de metadados, poderá criar um localizador **SAS** e baixar o arquivo em seu computador local.  

Este artigo aborda os elementos e tipos do esquema XML no qual o metadados de saída (&lt;source_file_name&gt;_manifest. xml) é baseado. Para obter informações sobre o arquivo que contém metadados sobre o ativo de entrada, consulte metadados de entrada.  

Você pode encontrar o código de esquema completo e o exemplo de XML no final deste artigo.  

## <a name="AssetFiles"></a>Elemento raiz AssetFiles
Coleção de entradas Assetfile para o trabalho de codificação.  

### <a name="child-elements"></a>Elementos filho
| Nome | Descrição |
| --- | --- |
| **AssetFile**<br/><br/> minOccurs="0" maxOccurs="1" |Um elemento Assetfile que faz parte da coleção AssetFiles. |

## <a name="AssetFile"></a>Elemento assetfile
Você pode encontrar um [exemplo XML](#xml)de exemplo XML.  

### <a name="attributes"></a>Atributos
| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Nome**<br/><br/> Obrigatório |**xs:string** |O nome do arquivo de ativo de mídia. |
| **Tamanho**<br/><br/> minInclusive ="0"<br/><br/> Obrigatório |**xs:long** |Tamanho do arquivo de ativo em bytes. |
| **Permanência**<br/><br/> Obrigatório |**xs:duration** |Duração da reprodução do conteúdo. |

### <a name="child-elements"></a>Elementos filho
| Nome | Descrição |
| --- | --- |
| **Causas** |Coleção de arquivos de mídia de entrada/origem que foi processada para produzir este Assetfile. Para obter mais informações, consulte elemento Source. |
| **VideoTracks**<br/><br/> minOccurs="0" maxOccurs="1" |Cada Assetfile físico pode conter zero ou mais faixas de vídeos intercaladas em um formato de contêiner apropriado. Para obter mais informações, consulte elemento VideoTracks. |
| **AudioTracks**<br/><br/> minOccurs="0" maxOccurs="1" |Cada Assetfile físico pode conter zero ou mais faixas de áudio intercaladas em um formato de contêiner apropriado. Esta é a coleção de todas essas faixas de áudio. Para obter mais informações, consulte elemento AudioTracks. |

## <a name="Sources"></a>Elemento sources
Coleção de arquivos de mídia de entrada/origem que foi processada para produzir este Assetfile.  

Você pode encontrar um [exemplo XML](#xml)de exemplo XML.  

### <a name="child-elements"></a>Elementos filho
| Nome | Descrição |
| --- | --- |
| **Origem**<br/><br/> minOccurs="1" maxOccurs="unbounded" |Um arquivo de entrada/origem usado ao gerar esse ativo. Para obter mais informações, consulte elemento Source. |

## <a name="Source"></a>Elemento de origem
Um arquivo de entrada/origem usado ao gerar esse ativo.  

Você pode encontrar um [exemplo XML](#xml)de exemplo XML.  

### <a name="attributes"></a>Atributos
| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Nome**<br/><br/> Obrigatório |**xs:string** |Nome do arquivo de origem de entrada. |

## <a name="VideoTracks"></a>Elemento VideoTracks
Cada Assetfile físico pode conter zero ou mais faixas de vídeos intercaladas em um formato de contêiner apropriado. O elemento **VideoTracks** representa uma coleção de todas as faixas de vídeo.  

Você pode encontrar um [exemplo XML](#xml)de exemplo XML.  

### <a name="child-elements"></a>Elementos filho
| Nome | Descrição |
| --- | --- |
| **VideoTrack**<br/><br/> minOccurs="1" maxOccurs="unbounded" |Uma faixa de vídeo específica no Assetfile pai. Para obter mais informações, consulte elemento VideoTrack. |

## <a name="VideoTrack"></a>Elemento VideoTrack
Uma faixa de vídeo específica no Assetfile pai.  

Você pode encontrar um [exemplo XML](#xml)de exemplo XML.  

### <a name="attributes"></a>Atributos
| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Id**<br/><br/> minInclusive ="0"<br/><br/> Obrigatório |**xs:int** |Índice de base zero desta faixa de vídeo. **Observação:**  Essa **ID** não é necessariamente a TrackID como usada em um arquivo MP4. |
| **FourCC**<br/><br/> Obrigatório |**xs:string** |Código FourCC do codec de vídeo. |
| **Perfil** |**xs:string** |Perfil H264 (aplicável somente ao Codec H264). |
| **Nível** |**xs:string** |Nível de H264 (aplicável somente ao Codec H264). |
| **Width**<br/><br/> minInclusive ="0"<br/><br/> Obrigatório |**xs:int** |Largura de vídeo codificada em pixels. |
| **Tamanho**<br/><br/> minInclusive ="0"<br/><br/> Obrigatório |**xs:int** |Altura de vídeo codificada em pixels. |
| **DisplayAspectRatioNumerator**<br/><br/> minInclusive ="0"<br/><br/> Obrigatório |**xs:double** |Numerador de taxa de proporção de exibição de vídeo. |
| **DisplayAspectRatioDenominator**<br/><br/> minInclusive ="0"<br/><br/> Obrigatório |**xs:double** |Denominador de taxa de proporção de exibição de vídeo. |
| **Quadros**<br/><br/> minInclusive ="0"<br/><br/> Obrigatório |**xs:decimal** |Taxa de quadros de vídeo medido no formato. 3F. |
| **TargetFramerate**<br/><br/> minInclusive ="0"<br/><br/> Obrigatório |**xs:decimal** |Taxa de quadros de vídeo de destino predefinida no formato. 3F. |
| **720p**<br/><br/> minInclusive ="0"<br/><br/> Obrigatório |**xs:int** |Taxa média de bits de vídeo em quilobits por segundo, calculada a partir do Assetfile. Conta apenas o conteúdo de fluxo elementar e não inclui a sobrecarga de empacotamento. |
| **TargetBitrate**<br/><br/> minInclusive ="0"<br/><br/> Obrigatório |**xs:int** |Taxa de bits média de destino para esta faixa de vídeo, conforme solicitado por meio da predefinição de codificação, em quilobits por segundo. |
| **MaxGOPBitrate**<br/><br/> minInclusive ="0" |**xs:int** |Taxa de bits média máxima de GOP para esta faixa de vídeo em quilobits por segundo. |

## <a name="AudioTracks"></a>Elemento AudioTracks
Cada Assetfile físico pode conter zero ou mais faixas de áudio intercaladas em um formato de contêiner apropriado. O elemento **AudioTracks** representa uma coleção de todas essas faixas de áudio.  

Você pode encontrar um [exemplo XML](#xml)de exemplo XML.  

### <a name="child-elements"></a>Elementos filho
| Nome | Descrição |
| --- | --- |
| **AudioTrack**<br/><br/> minOccurs="1" maxOccurs="unbounded" |Uma faixa de áudio específica no Assetfile pai. Para obter mais informações, consulte elemento AudioTrack. |

## <a name="AudioTrack"></a>Elemento AudioTrack
Uma faixa de áudio específica no Assetfile pai.  

Você pode encontrar um [exemplo XML](#xml)de exemplo XML.  

### <a name="attributes"></a>Atributos
| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Id**<br/><br/> minInclusive ="0"<br/><br/> Obrigatório |**xs:int** |Índice de base zero desta faixa de áudio. **Observação:**  Isso não é necessariamente o TrackID como usado em um arquivo MP4. |
| **Codec** |**xs:string** |Cadeia de caracteres do codec de faixa de áudio. |
| **EncoderVersion** |**xs:string** |Cadeia de caracteres de versão de codificador opcional, necessária para EAC3. |
| **Channels** (Canais)<br/><br/> minInclusive ="0"<br/><br/> Obrigatório |**xs:int** |Número de canais de áudio. |
| **SamplingRate**<br/><br/> minInclusive ="0"<br/><br/> Obrigatório |**xs:int** |Taxa de amostragem de áudio em amostras/s ou Hz. |
| **720p**<br/><br/> minInclusive ="0"<br/><br/> Obrigatório |**xs:int** |Taxa média de bits de áudio em bits por segundo, calculada a partir do Assetfile. Conta apenas o conteúdo de fluxo elementar e não inclui a sobrecarga de empacotamento. |
| **BitsPerSample**<br/><br/> minInclusive ="0"<br/><br/> Obrigatório |**xs:int** |Bits por amostra para o tipo de formato wFormatTag. |

### <a name="child-elements"></a>Elementos filho
| Nome | Descrição |
| --- | --- |
| **LoudnessMeteringResultParameters**<br/><br/> minOccurs="0" maxOccurs="1" |Parâmetros de resultado de medição de intensidade. Para obter mais informações, consulte elemento LoudnessMeteringResultParameters. |

## <a name="LoudnessMeteringResultParameters"></a>Elemento LoudnessMeteringResultParameters
Parâmetros de resultado de medição de intensidade.  

Você pode encontrar um [exemplo XML](#xml)de exemplo XML.  

### <a name="attributes"></a>Atributos
| Nome | Tipo | Descrição |
| --- | --- | --- |
| **DPLMVersionInformation** |**xs:string** |Versão do kit de desenvolvimento de medição de intensidade do **Dolby** Professional. |
| **DialogNormalization**<br/><br/> minInclusive="-31" maxInclusive="-1"<br/><br/> Obrigatório |**xs:int** |DialogNormalization gerado por meio de DPLM, necessário quando LoudnessMetering é definido |
| **IntegratedLoudness**<br/><br/> minInclusive="-70" maxInclusive="10"<br/><br/> Obrigatório |**xs:float** |Intensidade integrada |
| **IntegratedLoudnessUnit**<br/><br/> Obrigatório |**xs:string** |Unidade de intensidade integrada. |
| **IntegratedLoudnessGatingMethod**<br/><br/> Obrigatório |**xs:string** |Identificador de retenção |
| **IntegratedLoudnessSpeechPercentage**<br/><br/> minInclusive ="0" maxInclusive="100" |**xs:float** |Conteúdo de fala sobre o programa, como uma porcentagem. |
| **SamplePeak**<br/><br/> Obrigatório |**xs:float** |Valor de exemplo absoluto de pico, desde a redefinição ou desde a última limpeza, por canal.  As unidades são dBFS. |
| **SamplePeakUnit**<br/><br/> fixed="dBFS"<br/><br/> Obrigatório |**xs:anySimpleType** |Unidade de pico de exemplo. |
| **TruePeak**<br/><br/> Obrigatório |**xs:float** |Valor máximo real de pico, de acordo com ITU-R BS. 1770-2, desde a redefinição ou desde a última limpeza, por canal. As unidades são dBTP. |
| **TruePeakUnit**<br/><br/> fixed="dBTP"<br/><br/> Obrigatório |**xs:anySimpleType** |Unidade de pico real. |

## <a name="schema-code"></a>Código do esquema
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



## <a name="xml"></a>Exemplo de XML

O XML a seguir é um exemplo do arquivo de metadados de saída.  

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

## <a name="next-steps"></a>Passos seguintes
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
