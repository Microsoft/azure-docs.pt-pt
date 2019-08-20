---
title: Alteração do protocolo de Smooth Streaming (MS-SSTR) dos serviços de mídia do Azure para HEVC | Microsoft Docs
description: Esta especificação descreve o protocolo e o formato para transmissão ao vivo com base em MP4 fragmentado com HEVC nos serviços de mídia do Azure. Essa é uma emenda à documentação do protocolo Smooth Streaming (MS-SSTR) para incluir suporte para ingestão e streaming do HEVC. Somente as alterações necessárias para fornecer HEVC são especificadas neste artigo, exceto "(sem alteração)" indica que o texto é copiado apenas para fins de esclarecimento.
services: media-services
documentationcenter: ''
author: johndeu
manager: femila
editor: ''
ms.assetid: f27d85de-2cb8-4269-8eed-2efb566ca2c6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/19/2019
ms.author: johndeu
ms.openlocfilehash: e0637b2a015a610f9c3f92809f63a442980b63b1
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624809"
---
# <a name="smooth-streaming-protocol-ms-sstr-amendment-for-hevc"></a>Alteração do protocolo de Smooth Streaming (MS-SSTR) para HEVC 

## <a name="1-introduction"></a>1 Introdução 

Este artigo fornece emendas detalhadas a serem aplicadas à especificação do protocolo Smooth Streaming [MS-SSTR] para habilitar a Smooth Streaming de vídeo codificado HEVC. Nesta especificação, descrevemos apenas as alterações necessárias para fornecer o codec de vídeo HEVC. O artigo segue o mesmo esquema de numeração da especificação [MS-SSTR]. As manchetes vazias apresentadas em todo o artigo são fornecidas para orientar o leitor à sua posição na especificação [MS-SSTR].  "(Sem alteração)" indica que o texto é copiado apenas para fins de esclarecimento.

O artigo fornece requisitos de implementação técnica para a sinalização do codec de vídeo HEVC (usando as faixas de formato ' HEV1 ' ou ' hvc1 ') em um manifesto Smooth Streaming e as referências de normativas são atualizadas para fazer referência aos padrões MPEG atuais que inclua HEVC, Criptografia Comum de HEVC, e os nomes de caixas para o formato de arquivo de mídia base ISO foram atualizados para serem consistentes com as especificações mais recentes. 

A especificação de protocolo Smooth Streaming referenciada [MS-SSTR] descreve o formato de conexão usado para fornecer mídia digital ao vivo e sob demanda, como áudio e vídeo, no seguinte modos: de um codificador para um servidor Web, de um servidor para outro servidor e de um servidor para um cliente HTTP.
O uso de uma distribuição de estrutura de dados com base em MPEG-4 ([[MPEG4-ra])](https://go.microsoft.com/fwlink/?LinkId=327787)sobre http permite a alternância direta em tempo quase real entre diferentes níveis de qualidade de conteúdo de mídia compactado. O resultado é uma experiência de reprodução constante para o usuário final do cliente HTTP, mesmo que as condições de renderização de rede e vídeo sejam alteradas para o computador ou dispositivo cliente.

## <a name="11-glossary"></a>Glossário de 1,1 

Os termos a seguir são definidos em *[MS-Glos]* :

>   **UUID (identificador exclusivo universal) de identificador exclusivo (GUID)**

Os termos a seguir são específicos para este documento:

>  **hora da composição:** O momento em que um exemplo é apresentado no cliente, conforme definido em [[ISO/IEC-14496-12]](https://go.microsoft.com/fwlink/?LinkId=183695).
> 
>   **CENC**: Criptografia Comum, conforme definido em [ISO/IEC 23001-7] Second Edition.
> 
>   **tempo de decodificação:** O tempo que um exemplo deve ser decodificado no cliente, conforme definido em [[ISO/IEC 14496-12:2008]](https://go.microsoft.com/fwlink/?LinkId=183695).

**fragmento** Uma unidade de **mídia** que pode ser baixada independentemente, que abrange uma ou mais **amostras**.

>   **HEVC:** Codificação de vídeo de alta eficiência, conforme definido em [ISO/IEC 23008-2]
> 
>   **manifesto** Metadados sobre a **apresentação** que permite que um cliente faça solicitações de **mídia**. **meio** Dados compactados de áudio, vídeo e texto usados pelo cliente para executar uma **apresentação**. **formato de mídia:** Um formato bem definido para representar áudio ou vídeo como um **exemplo**compactado.
> 
>   **Presentation** O conjunto de todos os **fluxos** e metadados relacionados necessários para reproduzir um único filme. **Quest** Uma mensagem HTTP enviada do cliente para o servidor, conforme definido em [[rfc2616]](https://go.microsoft.com/fwlink/?LinkId=90372) **resposta:** Uma mensagem HTTP enviada do servidor para o cliente, conforme definido em [[rfc2616]](https://go.microsoft.com/fwlink/?LinkId=90372)
> 
>   **sample:** A menor unidade fundamental (como um quadro) na qual a **mídia** é armazenada e processada.
> 
>   **MAIO, DEVE, DEVE, NÃO DEVE:** Esses termos (em todos os limites) são usados conforme descrito em [[RFC2119]](https://go.microsoft.com/fwlink/?LinkId=90317) todas as instruções de comportamento opcional usam pode, deve ou não.

## <a name="12-references"></a>1,2 referências

>   Referências à documentação do Microsoft Open Specifications não incluem um ano de publicação porque os links são para a versão mais recente dos documentos, que são atualizados com frequência. Referências a outros documentos incluem um ano de publicação quando uma está disponível.

### <a name="121-normative-references"></a>1.2.1 referências de normativas 

>  [MS-SSTR] *V20140502* do protocolo de Smooth streaming[https://msdn.microsoft.com/library/ff469518.aspx](https://msdn.microsoft.com/library/ff469518.aspx)
> 
>   [ISO/IEC 14496-12] Organização Internacional de Normalização, "tecnologia da informação--codificação de objetos de áudio-visual-parte 12: Formato de arquivo de mídia de base ISO ", ISO/IEC 14496-12:2014, edição 4, mais Corrigendum 1, emendas 1 & 2.
>   <https://standards.iso.org/ittf/PubliclyAvailableStandards/c061988_ISO_IEC_14496-12_2012.zip>
> 
>   [ISO/IEC 14496-15] Organização Internacional de Normalização, "tecnologia da informação--codificação de objetos de áudio-visual-parte 15: Carro do vídeo estruturado da unidade de NAL no formato de arquivo de mídia de base ISO ", ISO 14496-15:2015, edição 3.
>   <https://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=65216>
> 
>   [ISO/IEC 23008-2] Tecnologia da informação--codificação de alta eficiência e entrega de mídia em ambientes heterogêneos – parte 2: Codificação de vídeo de alta eficiência: 2013 ou edição mais recente<https://standards.iso.org/ittf/PubliclyAvailableStandards/c035424_ISO_IEC_23008-2_2013.zip>
> 
>   [ISO/IEC 23001-7] Tecnologia da informação — tecnologias de sistemas MPEG — parte 7: Criptografia comum em arquivos de formato de arquivo de mídia de base ISO, CENC Edition 2:2015<https://www.iso.org/iso/catalogue_detail.htm?csnumber=65271>
> 
>   [RFC-6381] IETF RFC-6381, "os parâmetros" codecs "e" perfis "para os tipos de mídia" Bucket "<https://tools.ietf.org/html/rfc6381>
> 
>   [MPEG4-RA] A autoridade de registro MP4, "MP4REG",[http://www.mp4ra.org](https://go.microsoft.com/fwlink/?LinkId=327787)
> 
>   RFC2119 Bradner, S., "palavras-chave para uso em RFCs para indicar os níveis de requisito", BCP 14, RFC 2119, Março 1997,[https://www.rfc-editor.org/rfc/rfc2119.txt](https://go.microsoft.com/fwlink/?LinkId=90317)

### <a name="122-informative-references"></a>Referências informativas do 1.2.2 

>   [MS-GLOS] Microsoft Corporation, "*Glossário mestre de protocolos do Windows*".
> 
>   RFC3548 Josefsson, S., Ed., "as codificações de dados para base16, Base32 e base64", RFC 3548, Julho 2003,[https://www.ietf.org/rfc/rfc3548.txt](https://go.microsoft.com/fwlink/?LinkId=90432)
> 
>   RFC5234 Crocker, D., Ed. e Overell, P., "BNF aumentada para especificações de sintaxe: ABNF ", STD 68, RFC 5234, janeiro 2008,[https://www.rfc-editor.org/rfc/rfc5234.txt](https://go.microsoft.com/fwlink/?LinkId=123096)


## <a name="13-overview"></a>1,3 visão geral 

>   Somente as alterações na especificação de Smooth Streaming necessárias para a entrega de HEVC são especificadas abaixo. Os cabeçalhos de seção inalterados são listados para manter o local na especificação de Smooth Streaming referenciada [MS-SSTR].

## <a name="14-relationship-to-other-protocols"></a>1,4 relação com outros protocolos 

## <a name="15-prerequisitespreconditions"></a>1,5 pré-requisitos/precondições 

## <a name="16-applicability-statement"></a>Declaração de aplicabilidade 1,6 

## <a name="17-versioning-and-capability-negotiation"></a>versão 1,7 e negociação de capacidade 

## <a name="18-vendor-extensible-fields"></a>1,8 de fornecedores – campos extensíveis 

>   O método a seguir deve ser usado para identificar fluxos usando o formato de vídeo HEVC:
> 
>   * **Códigos descritivos personalizados para formatos de mídia:** Esse recurso é fornecido pelo campo **FOURCC** , conforme especificado na seção *2.2.2.5*.
>   Os implementadores podem garantir que as extensões não entrem em conflito registrando códigos de extensão com o MPEG4-RA, conforme especificado em [[ISO/IEC-14496-12]](https://go.microsoft.com/fwlink/?LinkId=183695)

## <a name="19-standards-assignments"></a>1,9 atribuições de padrões 

## <a name="2-messages"></a>2 mensagens 

## <a name="21-transport"></a>transporte 2,1

## <a name="22-message-syntax"></a>Sintaxe de mensagem 2,2 

### <a name="221-manifest-request"></a>Solicitação de manifesto 2.2.1 

### <a name="222-manifest-response"></a>Resposta de manifesto 2.2.2 

#### <a name="2221-smoothstreamingmedia"></a>2.2.2.1 SmoothStreamingMedia 

>   **MinorVersion (variável):** A versão secundária da mensagem de resposta do manifesto. DEVE ser definido como 2. (Sem alteração)
> 
>   **Escala de TIMESCALE (variável):** A escala de tempo do atributo Duration, especificada como o número de incrementos em um segundo. O valor padrão é
> 1. (Sem alteração)
> 
>    O valor recomendado é 90000 para representar a duração exata de quadros de vídeo e fragmentos contendo vídeo de taxa de quadros fracionários (por exemplo, 30/1.001 Hz).

#### <a name="2222-protectionelement"></a>2.2.2.2 protectionelement 

O Protectionelement deverá estar presente quando Criptografia Comum (CENC) tiver sido aplicado a fluxos de áudio ou vídeo. Os fluxos criptografados HEVC devem estar em conformidade com Criptografia Comum 2ª edição [ISO/IEC 23001-7]. Somente os dados de fatia em unidades de NAL VCL devem ser criptografados.

#### <a name="2223-streamelement"></a>2.2.2.3 streamelement 

>   **StreamTimeScale (variável):** A escala de tempo dos valores de duração e hora neste fluxo, especificada como o número de incrementos em um segundo. Um valor de 90000 é recomendado para fluxos de HEVC. É recomendado um valor correspondente à frequência de exemplo de onda (por exemplo, 48000 ou 44100) para fluxos de áudio.

##### <a name="22231-streamprotectionelement"></a>2.2.2.3.1 StreamProtectionElement

#### <a name="2224-urlpattern"></a>2.2.2.4 UrlPattern 

#### <a name="225-trackelement"></a>2.2.5 trackelement 

>   **FourCC (variável):** Um código de quatro caracteres que identifica qual formato de mídia é usado para cada exemplo. O intervalo de valores a seguir é reservado com os seguintes significados semânticos:
> 
> * "HEV1": Exemplos de vídeo para esta faixa usam vídeo HEVC, usando o formato de descrição de exemplo ' HEV1 ' especificado em [ISO/IEC-14496-15].
>
> * "hvc1": Exemplos de vídeo para esta faixa usam vídeo HEVC, usando o formato de descrição de exemplo ' hvc1 ' especificado em [ISO/IEC-14496-15].
> 
>   **CodecPrivateData (variável):** Dados que especificam parâmetros específicos para o formato de mídia e comuns a todos os exemplos na faixa, representados como uma cadeia de caracteres de bytes codificados em Hex. O formato e o significado semântico da sequência de bytes varia com o valor do campo **FOURCC** da seguinte maneira:
> 
>   * Quando um Trackelement descreve o vídeo HEVC, o campo **FOURCC** deve ser igual a **"HEV1"** ou **"hvc1"**
> 
>   O campo **CODECPRIVATEDATA** deve conter uma representação de cadeia de caracteres codificada em Hex da seguinte sequência de bytes, especificada em ABNF [[RFC5234]:](https://go.microsoft.com/fwlink/?LinkId=123096) (sem alteração de MS-SSTR)
> 
>   * %x00 %x00 %x00 %x01 SPSField %x00 %x00 %x00 %x01 PPSField
> 
>   * SPSField contém o SPS (conjunto de parâmetros de sequência).
> 
>   * PPSField contém o PPS (conjunto de parâmetros de fatia).
> 
>   Nota: O conjunto de parâmetros de vídeo (VPSs) não está contido em CodecPrivateData, mas deve estar contido no cabeçalho de arquivo dos arquivos armazenados na caixa ' hvcC '. Os sistemas que usam Smooth Streaming protocolo devem sinalizar parâmetros adicionais de decodificação (por exemplo, a camada HEVC) usando os "codecs" de atributo personalizado.

##### <a name="22251-customattributeselement"></a>2.2.2.5.1 CustomAttributesElement 

#### <a name="226-streamfragmentelement"></a>2.2.6 StreamFragmentElement 

>   O campo **MajorVersion do SMOOTHSTREAMINGMEDIA** deve ser definido como 2 e o campo **MinorVersion** deve ser definido como 2. (Sem alteração)

##### <a name="22261-trackfragmentelement"></a>2.2.2.6.1 TrackFragmentElement 

### <a name="223-fragment-request"></a>Solicitação de fragmento 2.2.3 

>   **Nota**: O formato de mídia padrão solicitado para **MinorVersion** 2 e ' HEV1 ' ou ' hvc1 ' é o formato de arquivo de mídia de base ISO da marca ' iso8 ' especificado em [ISO/IEC 14496-12] formato de arquivo de mídia base ISO quarta edição e [ISO/IEC 23001-7] criptografia comum Second Edition.

### <a name="224-fragment-response"></a>Resposta de fragmento 2.2.4 

#### <a name="2241-moofbox"></a>2.2.4.1 MoofBox 

#### <a name="2242-mfhdbox"></a>2.2.4.2 MfhdBox 

#### <a name="2243-trafbox"></a>2.2.4.3 TrafBox 

#### <a name="2244-tfxdbox"></a>2.2.4.4 TfxdBox 

>   O **TfxdBox** foi preterido e sua função substituída pela caixa de tempo de decodificação do fragmento de rastreamento (' TFDT ') especificada na seção [ISO/IEC 14496-12] 8.8.12.
> 
>   **Nota**: Um cliente pode calcular a duração de um fragmento somando as durações de exemplo listadas na caixa de execução de rastreamento (' Trun ') ou multiplicando o número de amostras vezes a duração de amostra padrão. O baseMediaDecodeTime em ' TFDT ' mais a duração do fragmento é igual ao parâmetro de tempo de URL para o próximo fragmento.
> 
>   Uma caixa de tempo de referência de produtor (' prft ') deve ser inserida antes de uma caixa de fragmento de filme (' Moof '), conforme necessário, para indicar a hora UTC correspondente ao tempo de decodificação do fragmento de rastreamento do primeiro exemplo referenciado pela caixa de fragmento do filme, conforme especificado em [ISO/IEC 14496 -12] seção 8.16.5.

#### <a name="2245-tfrfbox"></a>2.2.4.5 TfrfBox 

>   O **TfrfBox** foi preterido e sua função substituída pela caixa de tempo de decodificação do fragmento de rastreamento (' TFDT ') especificada na seção [ISO/IEC 14496-12] 8.8.12.
> 
>   **Nota**: Um cliente pode calcular a duração de um fragmento somando as durações de exemplo listadas na caixa de execução de rastreamento (' Trun ') ou multiplicando o número de amostras vezes a duração de amostra padrão. O baseMediaDecodeTime em ' TFDT ' mais a duração do fragmento é igual ao parâmetro de tempo de URL para o próximo fragmento. Os endereços de busca antecipada são preteridos porque atrasam a transmissão ao vivo.

#### <a name="2246-tfhdbox"></a>2.2.4.6 TfhdBox 

>   O **TfhdBox** e os campos relacionados encapsulam padrões para os metadados por exemplo no fragmento. A sintaxe do campo **TfhdBox** é um subconjunto estrito da sintaxe da caixa rastrear cabeçalho do fragmento definida na seção [[ISO/IEC-14496-12]](https://go.microsoft.com/fwlink/?LinkId=183695) 8.8.7.
> 
>   **BaseDataOffset (8 bytes):** O deslocamento, em bytes, desde o início do campo **MdatBox** até o campo de exemplo no campo **MdatBox** . Para sinalizar essa restrição, o sinalizador padrão-base-is-Moof (0x020000) deve ser definido.

#### <a name="2247-trunbox"></a>2.2.4.7 TrunBox 

>   O **TrunBox** e os campos relacionados encapsulam por metadados de exemplo para o fragmento solicitado. A sintaxe de **TrunBox** é um subconjunto estrito da caixa de execução de fragmento de faixa da versão 1 definida na seção [[ISO/IEC-14496-](https://go.microsoft.com/fwlink/?LinkId=183695)*12]* 8.8.8.
> 
>   **SampleCompositionTimeOffset (4 bytes):** O deslocamento de tempo de composição do exemplo de cada amostra ajustado de forma que o tempo de apresentação do primeiro exemplo apresentado no fragmento seja igual ao tempo de decodificação do primeiro exemplo decodificado. Deslocamentos de composição de amostra de vídeo negativos devem ser usados,
> 
>   conforme definido em [[ISO/IEC-14496-12].](https://go.microsoft.com/fwlink/?LinkId=183695)
> 
>   Nota: Isso evita um erro de sincronização de vídeo causado por áudio de defasagem de vídeo igual ao maior atraso de remoção de buffer de imagem decodificada e mantém o tempo de apresentação entre fragmentos alternativos que podem ter atrasos de remoção diferentes.
> 
>   A sintaxe dos campos definidos nesta seção, especificada em ABNF [[RFC5234],](https://go.microsoft.com/fwlink/?LinkId=123096) permanece a mesma, exceto da seguinte maneira:
> 
>   SampleCompositionTimeOffset = SIGNED_INT32

#### <a name="2248-mdatbox"></a>2.2.4.8 MdatBox 

#### <a name="2249-fragment-response-common-fields"></a>Campos comuns de resposta de fragmento 2.2.4.9 

### <a name="225-sparse-stream-pointer"></a>Ponteiro de fluxo esparso 2.2.5 

### <a name="226-fragment-not-yet-available"></a>o fragmento 2.2.6 ainda não está disponível 

### <a name="227-live-ingest"></a>ingestão ao vivo do 2.2.7 

#### <a name="2271-filetype"></a>FileType 2.2.7.1 

>   **Filetype (variável):** especifica o subtipo e o uso pretendido do arquivo MPEG-4 ([[MPEG4-ra])](https://go.microsoft.com/fwlink/?LinkId=327787) e os atributos de alto nível.
> 
>   **MajorBrand (variável):** A marca principal do arquivo de mídia. DEVE ser definido como "ISML".
> 
>   **MinorVersion (variável):** A versão secundária do arquivo de mídia. DEVE ser definido como 1.
> 
>   **CompatibleBrands (variável):** Especifica as marcas com suporte de MPEG-4.
>   DEVE incluir "ccff" e "iso8".
> 
>   A sintaxe dos campos definidos nesta seção, especificada em ABNF [[RFC5234],](https://go.microsoft.com/fwlink/?LinkId=123096) é a seguinte:

    FileType = MajorBrand MinorVersion CompatibleBrands
    MajorBrand = STRING_UINT32
    MinorVersion = STRING_UINT32
    CompatibleBrands = "ccff" "iso8" 0\*(STRING_UINT32)

**Nota**: As marcas de compatibilidade ' ccff ' e ' iso8 ' indicam que os fragmentos estão em conformidade com o "formato de arquivo de contêiner comum" e Criptografia Comum [ISO/IEC 23001-7] e a edição 4 do formato de arquivo de mídia de base ISO [ISO/IEC 14496-12].

#### <a name="2272-streammanifestbox"></a>2.2.7.2 StreamManifestBox 

##### <a name="22721-streamsmil"></a>2.2.7.2.1 StreamSMIL 

#### <a name="2273-liveservermanifestbox"></a>2.2.7.3 LiveServerManifestBox 

##### <a name="22731-livesmil"></a>2.2.7.3.1 LiveSMIL 

#### <a name="2274-moovbox"></a>2.2.7.4 MoovBox 

#### <a name="2275-fragment"></a>fragmento de 2.2.7.5 

##### <a name="22751-track-fragment-extended-header"></a>Cabeçalho estendido do fragmento do 2.2.7.5.1 Track 

### <a name="228-server-to-server-ingest"></a>ingestão de servidor para servidor 2.2.8 

## <a name="3-protocol-details"></a>3 detalhes do protocolo 


## <a name="31-client-details"></a>3,1 detalhes do cliente 

### <a name="311-abstract-data-model"></a>Modelo de dados abstrato 3.1.1 

#### <a name="3111-presentation-description"></a>Descrição da apresentação do 3.1.1.1 

>   O elemento de dados descrição da apresentação encapsula todos os metadados da apresentação.
> 
>   Metadados de apresentação: Um conjunto de metadados que é comum a todos os fluxos na apresentação. Os metadados de apresentação incluem os seguintes campos, especificados na seção *2.2.2.1*:
> 
> * **MajorVersion**
> * **MinorVersion**
> * **TimeScale**
> * **Permanência**
> * **IsLive**
> * **LookaheadCount**
> * **DVRWindowLength**
> 
>   As apresentações que contêm fluxos de HEVC devem definir:

    MajorVersion = 2
    MinorVersion = 2

>   LookaheadCount = 0 (Observação: Caixas preteridas)
> 
>   As apresentações também devem definir:

    TimeScale = 90000

>   Coleção de fluxos: Uma coleção de elementos de dados de descrição do fluxo, conforme especificado na seção *3.1.1.1.2*.
> 
>   Descrição da proteção: Uma coleção de elementos de dados de descrição de metadados do sistema de proteção, conforme especificado na seção *3.1.1.1.1*.

##### <a name="31111-protection-system-metadata-description"></a>Descrição de metadados do sistema de proteção 3.1.1.1.1 

>   O elemento de dados de descrição de metadados do sistema de proteção encapsula metadados específicos para um único sistema de Proteção de Conteúdo. (Sem alteração)
> 
>   Descrição do cabeçalho de proteção: Metadados de proteção de conteúdo que pertencem a um único sistema de Proteção de Conteúdo. Descrição do cabeçalho de proteção inclui os seguintes campos, especificados na seção *2.2.2.2*:
> 
>   * **SystemID**
>   * **ProtectionHeaderContent**

##### <a name="31112-stream-description"></a>Descrição do fluxo de 3.1.1.1.2 

###### <a name="311121-track-description"></a>Descrição da faixa de 3.1.1.1.2.1 

###### <a name="3111211-custom-attribute-description"></a>Descrição do atributo personalizado do 3.1.1.1.2.1.1 

##### <a name="3113-fragment-reference-description"></a>Descrição da referência do fragmento 3.1.1.3 

###### <a name="31131-track-specific-fragment-reference-description"></a>Descrição de referência de fragmento específico do 3.1.1.3.1 Track 

#### <a name="3112-fragment-description"></a>Descrição do fragmento 3.1.1.2 

##### <a name="31121-sample-description"></a>Descrição de exemplo do 3.1.1.2.1 

### <a name="312-timers"></a>temporizadores de 3.1.2 

### <a name="313-initialization"></a>inicialização do 3.1.3 

### <a name="314-higher-layer-triggered-events"></a>3.1.4 eventos disparados de camada superior 

#### <a name="3141-open-presentation"></a>3.1.4.1 abrir apresentação 

#### <a name="3142-get-fragment"></a>3.1.4.2 obter fragmento 

#### <a name="3143-close-presentation"></a>3.1.4.3 fechar apresentação 

### <a name="315-processing-events-and-sequencing-rules"></a>Eventos de processamento de 3.1.5 e regras de sequenciamento 

#### <a name="3151-manifest-request-and-manifest-response"></a>Solicitação de manifesto 3.1.5.1 e resposta de manifesto 

#### <a name="3152-fragment-request-and-fragment-response"></a>Solicitação de fragmento 3.1.5.2 e resposta de fragmento

## <a name="32-server-details"></a>3,2 detalhes do servidor

## <a name="33-live-encoder-details"></a>Detalhes do codificador ao vivo 3,3 

## <a name="4-protocol-examples"></a>4 exemplos de protocolo 

## <a name="5-security"></a>5 segurança 

## <a name="51-security-considerations-for-implementers"></a>5,1 considerações de segurança para implementadores

>   Se o conteúdo transportado usando esse protocolo tiver alto valor comercial, um sistema de Proteção de Conteúdo deverá ser usado para impedir o uso não autorizado do conteúdo. O **protectionelement** pode ser usado para transportar metadados relacionados ao uso de um sistema de proteção de conteúdo. O conteúdo de vídeo e áudio protegido deve ser criptografado conforme especificado pelo MPEG Criptografia Comum Second Edition: 2015 [ISO/IEC 23001-7].
> 
>   **Nota**: Para vídeo HEVC, somente os dados de fatia em VCL NALs são criptografados. Os cabeçalhos de fatia e outros NALs podem ser acessados para aplicativos de apresentação antes da descriptografia. em um caminho de vídeo seguro, as informações criptografadas não estão disponíveis para aplicativos de apresentação.

## <a name="52-index-of-security-parameters"></a>5,2 índice de parâmetros de segurança 


| **Parâmetro de segurança**  | **Section**         |
|-------------------------|---------------------|
| ProtectionElement       | *2.2.2.2*           |
| Caixas de Criptografia Comum | *[ISO/IEC 23001-7]* |

## <a name="53-common-encryption-boxes"></a>5,3 caixas de Criptografia Comum

As caixas a seguir podem estar presentes em respostas de fragmento quando Criptografia Comum é aplicado e são especificadas em [ISO/IEC 23001-7] ou [ISO/IEC 14496-12]:

1.  Caixa de cabeçalho específico do sistema de proteção (' Pssh ')

2.  Caixa de criptografia de exemplo (' senc ')

3.  Caixa de deslocamento de informações auxiliares de exemplo (' saio ')

4.  Caixa de tamanho de informações auxiliares de exemplo (' Saiz ')

5.  Caixa de descrição do grupo de exemplo (' sgpd ')

6.  Exemplo de caixa de grupo (' SBGP ')

---

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

[image1]: ./media/media-services-fmp4-live-ingest-overview/media-services-image1.png
[image2]: ./media/media-services-fmp4-live-ingest-overview/media-services-image2.png
[image3]: ./media/media-services-fmp4-live-ingest-overview/media-services-image3.png
[image4]: ./media/media-services-fmp4-live-ingest-overview/media-services-image4.png
[image5]: ./media/media-services-fmp4-live-ingest-overview/media-services-image5.png
[image6]: ./media/media-services-fmp4-live-ingest-overview/media-services-image6.png
[image7]: ./media/media-services-fmp4-live-ingest-overview/media-services-image7.png
