---
title: Serviços de multimédia do Azure - Smooth Streaming de modificação de protocolo (MS-SSTR) para HEVC | Documentos da Microsoft
description: Essa especificação descreve o protocolo e o formato para fragmentados com base em MP4 de transmissão em direto com HEVC nos serviços de multimédia do Azure. Trata-se de uma modificação para a documentação do protocolo de transmissão em fluxo uniforme (MS-SSTR) para incluir suporte HEVC de ingestão e transmissão em fluxo. Apenas as alterações necessárias para proporcionar HEVC são especificadas neste artigo, exceto foram "(não alterar)" indica o texto é copiado para apenas esclarecimentos.
services: media-services
documentationcenter: ''
author: cenkdin
manager: femila
editor: ''
ms.assetid: f27d85de-2cb8-4269-8eed-2efb566ca2c6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: johndeu;
ms.openlocfilehash: b26252ddda7997bebd730bb4c1007f76b3e645a4
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58294710"
---
# <a name="smooth-streaming-protocol-ms-sstr-amendment-for-hevc"></a>Modificação de protocolo (MS-SSTR) transmissão em fluxo uniforme para HEVC 

## <a name="1-introduction"></a>1 Introdução 

Este artigo fornece amendments detalhados a ser aplicado a especificação de protocolo de transmissão em fluxo uniforme [MS-SSTR] para ativar o vídeo de Smooth Streaming de HEVC codificado. Nessa especificação, descrevemos apenas as alterações necessárias para proporcionar o codec de vídeo HEVC. O artigo segue o mesmo esquema de numeração como a especificação [MS-SSTR]. As manchetes vazias apresentadas em todo o artigo são fornecidas para orientar o leitor para a respetiva posição na especificação [MS-SSTR].  "(Não alterar)" indica o texto é copiado esclarecimentos apenas para fins.

O artigo fornece os requisitos de implementação técnica para a sinalização de codec de vídeo HEVC num manifesto de transmissão em fluxo uniforme e referências normativas sejam atualizadas para referenciar os padrões de MPEG atuais incluem HEVC, encriptação comum de HEVC e caixa os nomes de formato de ficheiro ISO Base suporte de dados foram atualizados para ser consistente com as especificações mais recentes. 

A especificação de protocolo de transmissão em fluxo uniforme referenciada [MS-SSTR] descreve o formato wire utilizado para fornecer ao vivo e sob demanda mídia digital, como áudio e vídeo, as seguintes avançadas: de um codificador para um servidor web, de um servidor para outro servidor e de um servidor para um cliente HTTP.
A utilização de um MPEG-4 ([[MPEG4 RA])](https://go.microsoft.com/fwlink/?LinkId=327787)-permite a entrega de estrutura de dados com base em HTTP totalmente integrada de mudança em tempo real entre níveis de qualidade diferente de conteúdo multimédia comprimido. O resultado é uma experiência de reprodução constante para o usuário final do cliente HTTP, mesmo que a rede e as condições de processamento de vídeo alterar para o computador cliente ou dispositivo.

## <a name="11-glossary"></a>1.1 Glossário do 

Os termos seguintes são definidos no *[MS-GLOS]*:

>   **Identificador exclusivo global (GUID) universalmente UUID (Identificador exclusivo)**

Os termos seguintes são específicos a este documento:

>  **tempo de composição:** O tempo de uma amostra é apresentada no cliente, conforme definido na [[ISO/IEC-14496-12].](https://go.microsoft.com/fwlink/?LinkId=183695)
> 
>   **CENC**: Encriptação comum, como definido na [ISO/IEC 23001 7], Second Edition.
> 
>   **descodificar tempo:** O tempo de um exemplo é necessário para ser descodificado no cliente, conforme definido na [[ISO/IEC https://go.microsoft.com/fwlink/?LinkId=18369514496-12].](https://go.microsoft.com/fwlink/?LinkId=183695)

**fragment:** Uma unidade de forma independente que pode ser baixada da **multimédia** que é composto por um ou mais **exemplos**.

>   **HEVC:** Elevada eficiência vídeo de codificação, como definido pela [ISO/IEC 23008-2]
> 
>   **manifest:** Metadados sobre o **apresentação** que permite que um cliente fazer pedidos **multimédia**. **suporte de dados:** Comprimir dados de áudio, vídeo e texto utilizados pelo cliente para reproduzir um **apresentação**. **formato de suporte de dados:** Um formato bem definido para representar o áudio ou vídeo como um comprimido **exemplo**.
> 
>   **presentation:** O conjunto de todos os **fluxos** e metadados relacionados necessários para reproduzir um filme único. **Pedido:** Uma mensagem de HTTP enviada do cliente para o servidor, conforme definido na [[RFC2616].](https://go.microsoft.com/fwlink/?LinkId=90372) **Resposta:** Uma mensagem de HTTP enviada do servidor para o cliente, conforme definido na [[RFC2616].](https://go.microsoft.com/fwlink/?LinkId=90372)
> 
>   **sample:** A menor unidade fundamental (por exemplo, um quadro) no qual **multimédia** são armazenados e processados.
> 
>   **MAIO, DEVE, TEM, NÃO DEVE, NÃO PODEM:** Estes termos (em maiúsculas) são utilizados conforme descrito em [[RFC2119].](https://go.microsoft.com/fwlink/?LinkId=90317) Todas as instruções de utilização de comportamento opcional ou pode, SHOULD, ou deve não.

## <a name="12-references"></a>1.2 referências de

>   Referências a documentação de especificações aberto da Microsoft não incluem um ano de publicação porque as ligações são para a versão mais recente dos documentos, que são atualizados com frequência. Referências a outros documentos incluem um ano de publicação quando estiver disponível.

### <a name="121-normative-references"></a>1.2.1 referências normativas 

>  [MS-SSTR] Protocolo de transmissão em fluxo uniforme *v20140502* [https://msdn.microsoft.com/library/ff469518.aspx](https://msdn.microsoft.com/library/ff469518.aspx)
> 
>   [ISO/IEC 14496-12] International Organization for Standardization, "tecnologia da informação, codificação de objetos audiovisuais-- parte 12: Suporte de dados Base formato de arquivo ISO", ISO/IEC 14496-12:2014, 4 de edição, além de Corrigendum 1, Amendments 1 e 2.
>   <https://standards.iso.org/ittf/PubliclyAvailableStandards/c061988_ISO_IEC_14496-12_2012.zip>
> 
>   [ISO/IEC 14496 15] International Organization for Standardization, "tecnologia da informação, codificação de objetos audiovisuais-- parte 15: Símbolo de vídeo de unidade estruturado NAL no formato de ficheiro ISO Base de dados", ISO 14496-15:2015, 3 de edição.
>   <https://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=65216>
> 
>   [ISO/IEC 23008-2] Tecnologia da informação – elevada eficiência de codificação e suporte de dados de entrega em ambientes heterogêneos – parte 2: Elevada eficiência vídeo codificação: edição 2013 ou mais recente   <https://standards.iso.org/ittf/PubliclyAvailableStandards/c035424_ISO_IEC_23008-2_2013.zip>
> 
>   [ISO/IEC 23001 7] Tecnologia da informação — tecnologias de sistemas MPEG — parte 7: Encriptação comum nos arquivos de formato de ficheiro de base de dados ISO, 2:2015 CENC Edition <https://www.iso.org/iso/catalogue_detail.htm?csnumber=65271>
> 
>   [RFC-6381] IETF RFC-6381, "a"Codecs"e os parâmetros de"Perfis"para"registo"tipos de suportes de dados" <https://tools.ietf.org/html/rfc6381>
> 
>   [MPEG4 RA] A autoridade de registo de MP4, "MP4REG", [http://www.mp4ra.org](https://go.microsoft.com/fwlink/?LinkId=327787)
> 
>   [RFC2119] Bradner, S., "palavras-chave para utilização nos RFCs para níveis de requisito de indica", 14 de BCP, RFC 2119, Março de 1997   [https://www.rfc-editor.org/rfc/rfc2119.txt](https://go.microsoft.com/fwlink/?LinkId=90317)

### <a name="122-informative-references"></a>1.2.2 referências informativas 

>   [MS-GLOS] Microsoft Corporation, "*Glossário de mestre de protocolos do Windows*."
> 
>   [RFC3548] Ed de Josefsson, S.,., "O Base16 Base32 e Base64 codificações de dados", RFC 3548, Julho de 2003, [https://www.ietf.org/rfc/rfc3548.txt](https://go.microsoft.com/fwlink/?LinkId=90432)
> 
>   [RFC5234] Crocker, d, Ed. e Overell, P., "aumentados BNF especificações de sintaxe: ABNF", e padrão 68 RFC 5234, Janeiro de 2008   [https://www.rfc-editor.org/rfc/rfc5234.txt](https://go.microsoft.com/fwlink/?LinkId=123096)


## <a name="13-overview"></a>1.3 Descrição geral de 

>   Apenas as alterações para a especificação de transmissão em fluxo uniforme de mensagens em fila necessário para o fornecimento de HEVC são especificadas abaixo. Cabeçalhos de secção inalterado estão listados para manter a localização na referenciado Smooth Streaming especificação [MS-SSTR].

## <a name="14-relationship-to-other-protocols"></a>1.4 relação de para outros protocolos 

## <a name="15-prerequisitespreconditions"></a>1.5 pré-requisitos de/pré-condições 

## <a name="16-applicability-statement"></a>1.6 declaração de aplicabilidade 

## <a name="17-versioning-and-capability-negotiation"></a>1.7 controle de versão e a negociação de capacidade 

## <a name="18-vendor-extensible-fields"></a>1.8 campos fornecedor extensível 

>   O método seguinte deverá ser utilizado identificar fluxos com o formato de vídeo HEVC:
> 
>   * **Descritivos códigos personalizados para os formatos de mídia:** Esta capacidade é proporcionada pela **FourCC** campo, conforme especificado na secção *2.2.2.5*.
>   Implementadores podem garantir que as extensões não entram em conflito ao registar a códigos de extensão com o RA MPEG4, conforme especificado no [[ISO/IEC-14496-12]](https://go.microsoft.com/fwlink/?LinkId=183695)

## <a name="19-standards-assignments"></a>1,9 padrões de atribuições 

## <a name="2-messages"></a>2 mensagens 

## <a name="21-transport"></a>2.1 transporte

## <a name="22-message-syntax"></a>2.2 sintaxe de mensagem de 

### <a name="221-manifest-request"></a>2.2.1 pedido manifesto 

### <a name="222-manifest-response"></a>2.2.2 resposta manifesto 

#### <a name="2221-smoothstreamingmedia"></a>2.2.2.1 SmoothStreamingMedia 

>   **MinorVersion (variável):** A versão secundária da mensagem de resposta do manifesto. TEM de ser definido como 2. (Não alterar)
> 
>   **Escala temporal (variável):** A escala de tempo do atributo de duração, especificado como o número de incrementos num segundo. O valor predefinido é
> 1. (Não alterar)
> 
>    O valor recomendado é 90000 para representar a duração exata dos quadros de vídeo e fragmentos, que contém a framerate fracionários vídeo (por exemplo, 30/1.001 Hz).

#### <a name="2222-protectionelement"></a>2.2.2.2 ProtectionElement 

O ProtectionElement deve estar presente quando a encriptação comum (CENC) foi aplicado aos fluxos de vídeos ou áudio. Fluxos HEVC encriptado em conformidade com a encriptação comum 2a edição [ISO/IEC 23001 7]. Apenas os dados de setor em VCL NAL unidades devem ser encriptados.

#### <a name="2223-streamelement"></a>2.2.2.3 StreamElement 

>   **StreamTimeScale (variável):** A escala de tempo por duração e a hora valores este fluxo, especificado como o número de incrementos num segundo. Para fluxos HEVC, recomenda-se um valor de 90000. Para fluxos de áudio, recomenda-se um valor correspondente a frequência de exemplo de forma de onda (por exemplo, 48000 ou 44100).

##### <a name="22231-streamprotectionelement"></a>2.2.2.3.1 StreamProtectionElement

#### <a name="2224-urlpattern"></a>2.2.2.4 UrlPattern 

#### <a name="225-trackelement"></a>2.2.5 TrackElement 

>   **FourCC (variável):** Um código de quatro caracteres que identifica qual formato de suporte de dados é utilizado para cada exemplo. O seguinte intervalo de valores está reservado com o significado semântico seguinte:
> 
> * "hev1”: Exemplos de vídeo para este vídeo HEVC track de uso, usando o hev1 o formato de descrição especificado no [ISO/IEC-14496-15] de exemplo.
> 
>   **CodecPrivateData (variável):** Dados que especifica os parâmetros específicos para o formato de suporte de dados e comuns a todos os exemplos no Roteiro, representados como uma cadeia de caracteres de bytes codificado em hexadecimal. O formato e o significado semântico da seqüência de bytes varia de acordo com o valor do **FourCC** campo da seguinte forma:
> 
>   * Quando um TrackElement descreve vídeo HEVC, o **FourCC** campo deve ser igual **"hev1"** e;
> 
>   O **CodecPrivateData** campo deve conter uma representação de cadeia de caracteres hexadecimal codificada da seguinte sequência de byte, especificada no ABNF [[RFC5234]:](https://go.microsoft.com/fwlink/?LinkId=123096) (nenhuma alteração MS SSTR)
> 
>   * %x 00 %x 00 %x 00 %x 01 SPSField %x 00 %x 00 %x 00 %x 01 PPSField
> 
>   * SPSField contém a sequência de parâmetro definido (SPS).
> 
>   * PPSField contém o setor de parâmetro definido (PPS).
> 
>   Nota: O vídeo parâmetro definido VPSS () não está contido no CodecPrivateData, mas deve estar contido no cabeçalho do ficheiro de ficheiros armazenados na caixa 'hvcC'. Sistemas usando o protocolo de transmissão em fluxo uniforme devem sinalizar parâmetros decodificação adicionais (por exemplo, a camada de HEVC) usando o atributo personalizado "codecs."

##### <a name="22251-customattributeselement"></a>2.2.2.5.1 CustomAttributesElement 

#### <a name="226-streamfragmentelement"></a>2.2.6 StreamFragmentElement 

>   O **MajorVersion do SmoothStreamingMedia** campo tem de ser definido como 2, e **MinorVersion** campo tem de ser definido como 2. (Não alterar)

##### <a name="22261-trackfragmentelement"></a>2.2.2.6.1 TrackFragmentElement 

### <a name="223-fragment-request"></a>2.2.3 pedido de fragmento 

>   **Nota**: O formato de suporte de dados padrão pedida para **MinorVersion** 2 e 'hev1' é "iso8" marca ISO Base suporte de dados de ficheiro de formato especificado no [ISO/IEC 14496-12] ISO Base suporte de dados de ficheiro de formato quarta edição e [ISO/IEC 23001 7] comuns encriptação segundo Edição.

### <a name="224-fragment-response"></a>2.2.4 resposta de fragmento 

#### <a name="2241-moofbox"></a>2.2.4.1 MoofBox 

#### <a name="2242-mfhdbox"></a>2.2.4.2 MfhdBox 

#### <a name="2243-trafbox"></a>2.2.4.3 TrafBox 

#### <a name="2244-tfxdbox"></a>2.2.4.4 TfxdBox 

>   O **TfxdBox** foi preterido e sua função substituído pelo Track fragmento descodificar tempo caixa ('tfdt') especificado na seção de [ISO/IEC 14496-12] 8.8.12.
> 
>   **Nota**: Um cliente pode calcular a duração de um fragmento ao somar as durações de exemplo listadas na caixa de execução de controle ('trun") ou multiplicar o número de amostras vezes a duração de exemplo do padrão. BaseMediaDecodeTime de duração "tfdt" plus fragmento é igual do parâmetro da hora de URL para o fragmento seguinte.
> 
>   Uma caixa de tempo de referência de produtor ('prft") deve ser inserida antes de uma caixa de fragmento de filmes ('moof"), conforme necessário indicar a hora UTC correspondente para o controle fragmento decodificar o tempo do primeiro exemplo referenciado pela caixa de fragmento de filme, como especificado no [ISO/IEC 14496 -12] secção 8.16.5.

#### <a name="2245-tfrfbox"></a>2.2.4.5 TfrfBox 

>   O **TfrfBox** foi preterido e sua função substituído pelo Track fragmento descodificar tempo caixa ('tfdt') especificado na seção de [ISO/IEC 14496-12] 8.8.12.
> 
>   **Nota**: Um cliente pode calcular a duração de um fragmento ao somar as durações de exemplo listadas na caixa de execução de controle ('trun") ou multiplicar o número de amostras vezes a duração de exemplo do padrão. BaseMediaDecodeTime de duração "tfdt" plus fragmento é igual do parâmetro da hora de URL para o fragmento seguinte. Verificar endereços são preteridos porque eles atrasar a transmissão em fluxo em direto.

#### <a name="2246-tfhdbox"></a>2.2.4.6 TfhdBox 

>   O **TfhdBox** e padrões para por metadados de exemplo no fragmento de encapsular campos relacionados. A sintaxe do **TfhdBox** campo é um subconjunto estrito da sintaxe da caixa de cabeçalho do fragmento de controle definidos no [[ISO/IEC-14496-12]](https://go.microsoft.com/fwlink/?LinkId=183695) secção 8.8.7.
> 
>   **BaseDataOffset (8 bytes):** O deslocamento, em bytes, do início dos **MdatBox** campo para o campo de exemplo na **MdatBox** campo. Sinalizar esta restrição, tem de definir o sinalizador predefinido-base-for-moof (0x020000).

#### <a name="2247-trunbox"></a>2.2.4.7 TrunBox 

>   O **TrunBox** e campos relacionados encapsulam por metadados de exemplo para o fragmento de pedido. A sintaxe de **TrunBox** é um subconjunto estrito da versão 1 Track fragmento executar caixa definido em [[ISO/IEC-14496-](https://go.microsoft.com/fwlink/?LinkId=183695)*12]* secção 8.8.8.
> 
>   **SampleCompositionTimeOffset (4 bytes):** O desvio de tempo de composição de exemplo de cada exemplo ajustado para que o tempo de apresentação do primeiro exemplo apresentado no fragmento de é igual ao tempo de decodificação do primeiro exemplo decodificado. Os deslocamentos de composição de exemplo de vídeo negativo devem ser usados,
> 
>   conforme definido na [[ISO/IEC-14496-12].](https://go.microsoft.com/fwlink/?LinkId=183695)
> 
>   Nota: Isso evita a um erro de sincronização de vídeo causado por vídeo lagging áudio igual para o atraso de remoção de memória intermédia de imagem decodificada maior e mantém a temporização de apresentação entre fragmentos alternativas que possam ter atrasos de remoção diferentes.
> 
>   A sintaxe dos campos definidos nesta secção, especificado no ABNF [[RFC5234],](https://go.microsoft.com/fwlink/?LinkId=123096) permanece o mesmo, com exceção da seguinte forma:
> 
>   SampleCompositionTimeOffset = SIGNED_INT32

#### <a name="2248-mdatbox"></a>2.2.4.8 MdatBox 

#### <a name="2249-fragment-response-common-fields"></a>2.2.4.9 fragmentados campos comuns de resposta 

### <a name="225-sparse-stream-pointer"></a>2.2.5 Stream dispersas ponteiro 

### <a name="226-fragment-not-yet-available"></a>2.2.6 fragmentados ainda não disponível 

### <a name="227-live-ingest"></a>2.2.7 ingestão dinâmica 

#### <a name="2271-filetype"></a>2.2.7.1 FileType 

>   **FileType (variável):** Especifica a utilização de subtipo e previsto de MPEG-4 ([[MPEG4 RA])](https://go.microsoft.com/fwlink/?LinkId=327787) ficheiro e atributos de alto nível.
> 
>   **MajorBrand (variável):** A marca de principais do ficheiro de multimédia. TEM de ser definido como "isml."
> 
>   **MinorVersion (variável):** A versão secundária do ficheiro de suporte de dados. TEM de ser definido como 1.
> 
>   **CompatibleBrands (variável):** Especifica as marcas suportadas de MPEG-4.
>   TEM de incluir "ccff" e "iso8."
> 
>   A sintaxe dos campos definidos nesta secção, especificado no ABNF [[RFC5234],](https://go.microsoft.com/fwlink/?LinkId=123096) é o seguinte:

    FileType = MajorBrand MinorVersion CompatibleBrands
    MajorBrand = STRING_UINT32
    MinorVersion = STRING_UINT32
    CompatibleBrands = "ccff" "iso8" 0\*(STRING_UINT32)

**Nota**: As marcas de compatibilidade "ccff' e"iso8"indicam que fragmentos está em conformidade com"Formato de ficheiro de contentor comum"e a encriptação comum [ISO/IEC 23001 7] e ISO Base suporte de dados de ficheiro de formato Edition 4 [ISO/IEC 14496-12].

#### <a name="2272-streammanifestbox"></a>2.2.7.2 StreamManifestBox 

##### <a name="22721-streamsmil"></a>2.2.7.2.1 StreamSMIL 

#### <a name="2273-liveservermanifestbox"></a>2.2.7.3 LiveServerManifestBox 

##### <a name="22731-livesmil"></a>2.2.7.3.1 LiveSMIL 

#### <a name="2274-moovbox"></a>2.2.7.4 MoovBox 

#### <a name="2275-fragment"></a>2.2.7.5 o fragmento de 

##### <a name="22751-track-fragment-extended-header"></a>2.2.7.5.1 fragmento de controle estendido cabeçalho 

### <a name="228-server-to-server-ingest"></a>2.2.8 ingestão de-para-servidor 

## <a name="3-protocol-details"></a>3 detalhes de protocolo 


## <a name="31-client-details"></a>3.1 detalhes do cliente de 

### <a name="311-abstract-data-model"></a>3.1.1 modelo de dados abstrato 

#### <a name="3111-presentation-description"></a>3.1.1.1 Descrição de apresentação de 

>   O elemento de dados de descrição de apresentação encapsula todos os metadados para a apresentação.
> 
>   Metadados de apresentação: Um conjunto de metadados que são comuns a todos os fluxos na apresentação. Metadados de apresentação incluem os seguintes campos, especificados na seção *2.2.2.1*:
> 
> * **MajorVersion**
> * **MinorVersion**
> * **TimeScale**
> * **Duração**
> * **IsLive**
> * **LookaheadCount**
> * **DVRWindowLength**
> 
>   Apresentações que contém o conjunto de fluxos de HEVC deverá:

    MajorVersion = 2
    MinorVersion = 2

>   LookaheadCount = 0 (Note: Caixas de preterida)
> 
>   Apresentações também devem definir:

    TimeScale = 90000

>   Coleção de Stream: Uma coleção de elementos de dados de descrição do Stream, como especificada na seção *3.1.1.1.2*.
> 
>   Descrição de proteção: Uma coleção de elementos de dados de descrição de metadados do sistema de proteção, como especificada na seção *3.1.1.1.1*.

##### <a name="31111-protection-system-metadata-description"></a>3.1.1.1.1 Descrição de metadados do sistema de proteção de 

>   O elemento de dados de descrição de metadados do sistema de proteção encapsula os metadados específicos para um único sistema de proteção de conteúdo. (Não alterar)
> 
>   Descrição do cabeçalho de proteção: Metadados de proteção de conteúdo relacionado com um único sistema de proteção de conteúdo. Descrição de cabeçalho de proteção inclui os seguintes campos, especificados na seção *2.2.2.2*:
> 
>   * **SystemID**
>   * **ProtectionHeaderContent**

##### <a name="31112-stream-description"></a>3.1.1.1.2 descrição de Stream 

###### <a name="311121-track-description"></a>3.1.1.1.2.1 Descrição de track 

###### <a name="3111211-custom-attribute-description"></a>3.1.1.1.2.1.1 Descrição do atributo personalizado 

##### <a name="3113-fragment-reference-description"></a>3.1.1.3 descrição de referência do fragmento de 

###### <a name="31131-track-specific-fragment-reference-description"></a>3.1.1.3.1 Descrição da referência de fragmento específicos de controlar 

#### <a name="3112-fragment-description"></a>3.1.1.2 descrição de fragmento 

##### <a name="31121-sample-description"></a>3.1.1.2.1 Descrição de exemplo 

### <a name="312-timers"></a>3.1.2 temporizadores 

### <a name="313-initialization"></a>3.1.3 inicialização de 

### <a name="314-higher-layer-triggered-events"></a>3.1.4 camada superior de acionada por eventos 

#### <a name="3141-open-presentation"></a>3.1.4.1 Abra Presentation 

#### <a name="3142-get-fragment"></a>3.1.4.2 obter fragmento 

#### <a name="3143-close-presentation"></a>3.1.4.3 fechar apresentação 

### <a name="315-processing-events-and-sequencing-rules"></a>3.1.5 a processar eventos e regras de sequenciamento 

#### <a name="3151-manifest-request-and-manifest-response"></a>3.1.5.1 manifesto pedido e resposta do manifesto 

#### <a name="3152-fragment-request-and-fragment-response"></a>3.1.5.2 fragmentados pedido e resposta de fragmentados

## <a name="32-server-details"></a>3.2 detalhes do servidor de

## <a name="33-live-encoder-details"></a>3.3 detalhes do codificador em direto 

## <a name="4-protocol-examples"></a>Exemplos de protocolo 4 

## <a name="5-security"></a>5 de segurança 

## <a name="51-security-considerations-for-implementers"></a>5.1 considerações de segurança de para Implementadores

>   Se o conteúdo transportado utilizar este protocolo tem de alto valor comercial, um sistema de proteção de conteúdo deve ser usado para evitar utilizações não autorizadas do conteúdo. O **ProtectionElement** pode ser utilizado para transportar os metadados relacionados com a utilização de um sistema de proteção de conteúdo. Áudio protegido e o conteúdo de vídeo encriptados conforme especificado pelo MPEG comuns encriptação Second Edition: 2015 [ISO/IEC 23001-7].
> 
>   **Nota**: Para vídeo HEVC, apenas os dados de setor em VCL NALs são encriptados. Cabeçalhos de setor e outros NALs são acessíveis para aplicações de apresentação antes da desencriptação. um caminho de vídeo segura, informações encriptadas não estão disponíveis para aplicativos de apresentação.

## <a name="52-index-of-security-parameters"></a>5.2 índice dos parâmetros de segurança 


| **Parâmetro de segurança**  | **Section**         |
|-------------------------|---------------------|
| ProtectionElement       | *2.2.2.2*           |
| Caixas de encriptação comuns | *[ISO/IEC 23001-7]* |

## <a name="53-common-encryption-boxes"></a>5.3 caixas de encriptação comuns

As seguintes caixas podem estar presentes nas respostas do fragmento quando a encriptação comum é aplicada e estão especificados na [ISO/IEC 23001 7] ou [ISO/IEC 14496-12]:

1.  Proteção sistema específico cabeçalho caixa ('pssh")

2.  Caixa de encriptação de exemplo ('senc")

3.  Exemplo auxiliar deslocamento da caixa de informações ('saio")

4.  Caixa de tamanho de informações auxiliares do exemplo ('saiz")

5.  Caixa de descrição do grupo de exemplo ('sgpd")

6.  Exemplo de caixa de grupo ('sbgp")

-----------------------

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

[image1]: ./media/media-services-fmp4-live-ingest-overview/media-services-image1.png
[image2]: ./media/media-services-fmp4-live-ingest-overview/media-services-image2.png
[image3]: ./media/media-services-fmp4-live-ingest-overview/media-services-image3.png
[image4]: ./media/media-services-fmp4-live-ingest-overview/media-services-image4.png
[image5]: ./media/media-services-fmp4-live-ingest-overview/media-services-image5.png
[image6]: ./media/media-services-fmp4-live-ingest-overview/media-services-image6.png
[image7]: ./media/media-services-fmp4-live-ingest-overview/media-services-image7.png
