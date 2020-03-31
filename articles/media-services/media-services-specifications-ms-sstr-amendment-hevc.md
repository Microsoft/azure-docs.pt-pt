---
title: Emenda ao Protocolo de Streaming Suave (MS-SSTR) para o HEVC - Azure
description: Esta especificação descreve o protocolo e o formato para o streaming ao vivo fragmentado baseado em MP4 com hevc em Azure Media Services. Apenas as alterações necessárias à entrega do HEVC são especificadas neste artigo, exceto se "(Sem Alteração)" indica que o texto é copiado apenas para esclarecimento.
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
ms.openlocfilehash: be4009d418f2f8f3dff755e2e990efee593f070b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76514226"
---
# <a name="smooth-streaming-protocol-ms-sstr-amendment-for-hevc"></a>Emenda ao Protocolo de Streaming Suave (MS-SSTR) para o HEVC 

## <a name="1-introduction"></a>1 Introdução 

Este artigo fornece alterações detalhadas a aplicar à especificação do Protocolo de Streaming Suave [MS-SSTR] para permitir o streaming suave de vídeo codificado hevc. Nesta especificação, delineamos apenas as alterações necessárias para entregar o código de vídeo HEVC. O artigo segue o mesmo esquema de numeração que a especificação [MS-SSTR]. As manchetes vazias apresentadas ao longo do artigo são fornecidas para orientar o leitor para a sua posição na especificação [MS-SSTR].  "(Sem Alteração)" indica que o texto é copiado apenas para fins de esclarecimento.

O artigo fornece requisitos técnicos de implementação para a sinalização do código de vídeo HEVC (utilizando faixas de formato 'hev1' ou 'hvc1') num manifesto de streaming suave e referências normativas são atualizadas para referência às normas mpeg atuais que incluir HEVC, Encriptação Comum de HEVC e nomes de caixas para formato de ficheiro de mídia base ISO foram atualizados para serem consistentes com as especificações mais recentes. 

A especificação referenciada do Protocolo de Streaming Suave [MS-SSTR] descreve o formato de fio utilizado para fornecer meios digitais ao vivo e a pedido, tais como áudio e vídeo, de formas seguintes: de um codificador a um servidor web, de um servidor para outro servidor, e de um servidor a um cliente HTTP.
A utilização de uma estrutura de dados baseada em[MPEG-4 ([MPEG4-RA])](https://go.microsoft.com/fwlink/?LinkId=327787)permite uma troca perfeita em tempo quase real entre diferentes níveis de qualidade do conteúdo dos meios de comunicação comprimidos. O resultado é uma experiência de reprodução constante para o utilizador final do cliente HTTP, mesmo que as condições de rede e de renderização de vídeo mudem para o computador ou dispositivo do cliente.

## <a name="11-glossary"></a>1.1 Glossário 

Os seguintes termos são definidos em *[MS-GLOS]*:

>   **identificador globalmente único (GUID) identificador universalmente único (UUID)**

Os seguintes termos são específicos deste documento:

>  **tempo de composição:** O momento em que uma amostra é apresentada no cliente, tal como definido em [[ISO/IEC-14496-12]](https://go.microsoft.com/fwlink/?LinkId=183695).
> 
>   **CENC**: Encriptação comum, tal como definido na Segunda Edição [ISO/IEC 23001-7].
> 
>   **descodificar o tempo:** O tempo de descodificação de uma amostra no cliente, tal como definido em [[ISO/IEC 14496-12:2008]](https://go.microsoft.com/fwlink/?LinkId=183695).

**fragmento:** Uma unidade de meios de **comunicação** descarregada independentemente que compreende uma ou mais **amostras**.

>   **HEVC:** Codificação de vídeo de alta eficiência, tal como definida em [ISO/IEC 23008-2]
> 
>   **manifesto:** Metadados sobre a **apresentação** que permite a um cliente fazer pedidos de **meios de comunicação.** meios de **comunicação:** Dados de áudio, vídeo e texto comprimidos utilizados pelo cliente para reproduzir uma **apresentação**. **formato de mídia:** Um formato bem definido para representar o áudio ou o vídeo como uma **amostra**comprimido .
> 
>   **apresentação:** O conjunto de todos os streams e metadados **relacionados** necessários para jogar um único filme. **pedido:** Uma mensagem HTTP enviada do cliente para o servidor, conforme definido na **resposta** [[RFC2616]:](https://go.microsoft.com/fwlink/?LinkId=90372) Uma mensagem HTTP enviada do servidor para o cliente, conforme definido em [[RFC2616]](https://go.microsoft.com/fwlink/?LinkId=90372)
> 
>   **amostra:** A unidade fundamental mais pequena (como uma moldura) na qual os **meios** são armazenados e processados.
> 
>   **PODE, DEVE, NÃO DEVE, NÃO DEVE, NÃO DEVE:** Estes termos (em todas as tampas) são usados como descrito em [[RFC2119]](https://go.microsoft.com/fwlink/?LinkId=90317) Todas as declarações de uso de comportamento opcional ou maio, deve ou NÃO devem.

## <a name="12-references"></a>1.2 Referências

>   As referências à documentação do Microsoft Open Specifications não incluem um ano de publicação porque as ligações são para a versão mais recente dos documentos, que são atualizados com frequência. As referências a outros documentos incluem um ano de publicação quando um está disponível.

### <a name="121-normative-references"></a>1.2.1 Referências Normativas 

>  [MS-SSTR] Protocolo de Streaming Suave *v20140502*[https://msdn.microsoft.com/library/ff469518.aspx](https://msdn.microsoft.com/library/ff469518.aspx)
> 
>   [ISO/IEC 14496-12] Organização Internacional para a Normalização, "Tecnologia sugódia -- Codificação de objetos audiovisuais -- Parte 12: Formato de Ficheiro sinuoso Base da ISO", ISO/IEC 14496-12:2014, Edição 4, Mais Corrigendum 1, Alterações 1 & 2.
>   <https://standards.iso.org/ittf/PubliclyAvailableStandards/c061988_ISO_IEC_14496-12_2012.zip>
> 
>   [ISO/IEC 14496-15] Organização Internacional para a Normalização, "Tecnologia de Informação -- Codificação de objetos audiovisuais -- Parte 15: Transporte de vídeo estruturado da unidade NAL no formato de ficheiro sinuoso base da ISO", ISO 14496-15:2015, Edição 3.
>   <https://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=65216>
> 
>   [ISO/IEC 23008-2] Tecnologias da informação -- Codificação de alta eficiência e entrega de meios em ambientes heterogéneos -- Parte 2: Codificação de vídeo de alta eficiência: 2013 ou mais recente edição<https://standards.iso.org/ittf/PubliclyAvailableStandards/c035424_ISO_IEC_23008-2_2013.zip>
> 
>   [ISO/IEC 23001-7] Tecnologias da informação — Tecnologias de sistemas MPEG — Parte 7: Encriptação comum em ficheiros de formato de ficheiros de ficheiros base iso, CENC Edition 2:2015<https://www.iso.org/iso/catalogue_detail.htm?csnumber=65271>
> 
>   [RFC-6381] IETF RFC-6381, "Os parâmetros dos 'Codecs' e 'Profiles' para tipos de mídia "balde"<https://tools.ietf.org/html/rfc6381>
> 
>   [MPEG4-RA] A Mp4 Autoridade de Registos, "MP4REG",[http://www.mp4ra.org](https://go.microsoft.com/fwlink/?LinkId=327787)
> 
>   [RFC2119] Bradner, S., "Palavras-chave para uso em RFCs para indicar níveis de requisito", BCP 14, RFC 2119, março de 1997,[https://www.rfc-editor.org/rfc/rfc2119.txt](https://go.microsoft.com/fwlink/?LinkId=90317)

### <a name="122-informative-references"></a>1.2.2 Referências Informativas 

>   [MS-GLOS] Microsoft Corporation, "*Windows Protocols Master Glossary*."
> 
>   [RFC3548] Josefsson, S., Ed., "The Base16, Base32 e Base64 Data Encodings", RFC 3548, julho de 2003,[https://www.ietf.org/rfc/rfc3548.txt](https://go.microsoft.com/fwlink/?LinkId=90432)
> 
>   [RFC5234] Crocker, D., Ed., e Overell, P., "BNF aumentado para especificações de sintaxe: ABNF, STD 68, RFC 5234, janeiro de 2008,[https://www.rfc-editor.org/rfc/rfc5234.txt](https://go.microsoft.com/fwlink/?LinkId=123096)


## <a name="13-overview"></a>1.3 Visão geral 

>   Apenas são especificadas alterações à especificação de streaming suave necessária para a entrega do HEVC. Os cabeçalhos de secção inalterados estão listados para manter a localização na especificação de streaming liso referenciada [MS-SSTR].

## <a name="14-relationship-to-other-protocols"></a>1.4 Relação com outros protocolos 

## <a name="15-prerequisitespreconditions"></a>1.5 Pré-requisitos/Pré-condições 

## <a name="16-applicability-statement"></a>1.6 Declaração de aplicabilidade 

## <a name="17-versioning-and-capability-negotiation"></a>1.7 Negociação de Versonamento e Capacidade 

## <a name="18-vendor-extensible-fields"></a>1.8 Campos Extensíveis de Fornecedor 

>   Deve utilizar-se o seguinte método de identificação de correntes utilizando o formato de vídeo HEVC:
> 
>   * **Códigos Descritivos Personalizados para Formatos** de Mídia: Esta capacidade é fornecida pelo campo **FourCC,** conforme especificado na secção *2.2.2.5*.
>   Os implementadores podem garantir que as extensões não entram em conflito registando códigos de extensão com o MPEG4-RA, conforme especificado em [[ISO/IEC-14496-12]](https://go.microsoft.com/fwlink/?LinkId=183695)

## <a name="19-standards-assignments"></a>1.9 Atribuições de normas 

## <a name="2-messages"></a>2 Mensagens 

## <a name="21-transport"></a>2.1 Transporte

## <a name="22-message-syntax"></a>2.2 Sintaxe de Mensagem 

### <a name="221-manifest-request"></a>2.2.1 Pedido manifesto 

### <a name="222-manifest-response"></a>2.2.2 Resposta manifesta 

#### <a name="2221-smoothstreamingmedia"></a>2.2.2.1 SmoothstreamingMedia 

>   **Versão Menor (variável):** A versão menor da mensagem De Resposta Manifesta. Deve ser definido para 2. (Sem Alteração)
> 
>   **Escala de tempo (variável):** A escala de tempo do atributo de duração, especificada como o número de incrementos num segundo. O valor predefinido é 
> 1. (Sem Alteração)
> 
>    O valor recomendado é de 90000 para representar a duração exata dos quadros de vídeo e fragmentos que contêm vídeo de fotogramas fracionado (por exemplo, 30/1.001 Hz).

#### <a name="2222-protectionelement"></a>2.2.2.2 Elemento de Proteção 

O Elemento de Proteção deve estar presente quando a encriptação comum (CENC) tiver sido aplicada a transmissões de vídeo ou áudio. Os fluxos criptografados HEVC devem estar em conformidade com a Encriptação Comum 2ª Edição [ISO/IEC 23001-7]. Apenas os dados de fatias nas unidades NAL da VCL devem ser encriptados.

#### <a name="2223-streamelement"></a>2.2.2.3 Elemento de fluxo 

>   **Escala de Tempo de Fluxo (variável):** A escala de tempo para os valores de duração e tempo neste fluxo, especificado como o número de incrementos num segundo. Um valor de 90000 é recomendado para os fluxos hevc. Recomenda-se um valor correspondente à frequência da amostra de forma de onda (por exemplo, 48000 ou 44100) para os fluxos de áudio.

##### <a name="22231-streamprotectionelement"></a>2.2.2.3.1 Elemento de Proteção de Fluxos

#### <a name="2224-urlpattern"></a>2.2.2.4 UrlPattern 

#### <a name="225-trackelement"></a>2.2.5 Elemento de pista 

>   **FourCC (variável):** Um código de quatro caracteres que identifica o formato de mídia utilizado para cada amostra. A seguinte gama de valores é reservada com os seguintes significados semânticos:
> 
> * "hev1": Amostras de vídeo para esta faixa utilizam vídeo HEVC, utilizando o formato de descrição da amostra 'hev1' especificado em [ISO/IEC-14496-15].
>
> * "hvc1": Amostras de vídeo para esta faixa utilizam vídeo HEVC, utilizando o formato de descrição da amostra 'hvc1' especificado em [ISO/IEC-14496-15].
> 
>   **CodecPrivateData (variável):** Dados que especificam parâmetros específicos do formato de mídia e comuns a todas as amostras da faixa, representadas como uma cadeia de bytes codificados por hexátes. O formato e o significado semântico da sequência byte variam com o valor do campo **FourCC** da seguinte forma:
> 
>   * Quando um TrackElement descreve o vídeo HEVC, o campo **FourCC** será igual **a "hev1"** ou **"hvc1"**
> 
>   O campo **CodecPrivateData** deve conter uma representação de cadeia codificada por hexágonos da seguinte sequência de byte, especificada em ABNF [[RFC5234]:](https://go.microsoft.com/fwlink/?LinkId=123096) (nenhuma alteração do MS-SSTR)
> 
>   * %x00 %x00 %x00 %x01 SPSField %x00 %x00 %x00 %x00 %x01 PPSField
> 
>   * SPSField contém o conjunto de parâmetros de sequência (SPS).
> 
>   * PPSField contém o conjunto de parâmetros de corte (PPS).
> 
>   Nota: O conjunto de parâmetros de vídeo (VPS) não está contido no CodecPrivateData, mas deve estar contido no cabeçalho de ficheiros armazenados na caixa 'hvcC'. Os sistemas que utilizam o Smooth Streaming Protocol devem sinalizar parâmetros de descodificação adicionais (por exemplo, hevc Tier) utilizando os "codecs" de atributo personalizado.

##### <a name="22251-customattributeselement"></a>2.2.2.5.1 CustomAtributosElement 

#### <a name="226-streamfragmentelement"></a>2.2.6 Elemento de fragmento de fluxo 

>   O campo **MajorVersion do SmoothStreamingMedia** DEVE ser definido para 2 e o campo **MinorVersion** DEVE ser definido para 2. (Sem Alteração)

##### <a name="22261-trackfragmentelement"></a>2.2.2.6.1 Elemento de fragmento de faixa 

### <a name="223-fragment-request"></a>2.2.3 Pedido de Fragmento 

>   **Nota:** O formato de media predefinido solicitado para **minorVersion** 2 e 'hev1' ou 'hvc1' é 'iso8' marca ISO Base Media File Format especificado em [ISO/IEC 14496-12] Formato de Ficheiro base iso base e [ISO/IEC 23001-7] Common Encryption Second Edition.

### <a name="224-fragment-response"></a>2.2.4 Resposta fragmentada 

#### <a name="2241-moofbox"></a>2.2.4.1 MoofBox 

#### <a name="2242-mfhdbox"></a>2.2.4.2 MfhdBox 

#### <a name="2243-trafbox"></a>2.2.4.3 TrafBox 

#### <a name="2244-tfxdbox"></a>2.2.4.4 TfxdBox 

>   O **TfxdBox** é depreciado, e a sua função substituída pela Caixa de Tempo de Descodificação de Fragmento de Faixa ('tfdt') especificada na secção 8.8.12 [ISO/IEC 14496-12].
> 
>   **Nota:** Um cliente pode calcular a duração de um fragmento resumindo as durações da amostra listadas na Track Run Box ('trun') ou multiplicando o número de amostras vezes a duração da amostra padrão. O baseMediaDecodeTime em 'tfdt' mais a duração do fragmento é igual ao parâmetro de tempo de URL para o próximo fragmento.
> 
>   Uma caixa de tempo de referência do produtor ('prft') deve ser inserida antes de uma Caixa de Fragmentode filme ('moof') conforme necessário, para indicar o tempo UTC correspondente ao Tempo de Descodificação do Fragmento de Faixa da primeira amostra referenciada pela Caixa de Fragmentos de Filme, conforme especificado na secção 8.16.5 [ISO/IEC 14496-12].

#### <a name="2245-tfrfbox"></a>2.2.4.5 TfrfBox 

>   A **TfrfBox** é depreciada e a sua função substituída pela Caixa de Tempo de Descodificação de Fragmento de Faixa ('tfdt') especificada na secção 8.8.12 [ISO/IEC 14496-12].
> 
>   **Nota:** Um cliente pode calcular a duração de um fragmento resumindo as durações da amostra listadas na Track Run Box ('trun') ou multiplicando o número de amostras vezes a duração da amostra padrão. O baseMediaDecodeTime em 'tfdt' mais a duração do fragmento é igual ao parâmetro de tempo de URL para o próximo fragmento. Olhe para a frente endereços são depreciados porque atrasam o streaming ao vivo.

#### <a name="2246-tfhdbox"></a>2.2.4.6 TfhdBox 

>   O **TfhdBox** e os campos relacionados encapsulam os predefinidos por metadados por amostra no fragmento. A sintaxe do campo **TfhdBox** é um subconjunto rigoroso da sintaxe da Caixa de Cabeçalho de Fragmento de Faixa definida na secção 8.8.7 do Fragmento de Pista definido na secção 8.8.7. [[ISO/IEC-14496-12]](https://go.microsoft.com/fwlink/?LinkId=183695)
> 
>   **BaseDataOffset (8 bytes):** A compensação, em bytes, desde o início do campo **MdatBox** até ao campo de amostras no campo **MdatBox.** Para assinalar esta restrição, deve ser definida a bandeira padrão-base-is-moof (0x020000).

#### <a name="2247-trunbox"></a>2.2.4.7 TrunBox 

>   O **TrunBox** e os campos relacionados encapsulam por metadados de amostra para o fragmento solicitado. A sintaxe da **TrunBox** é um subconjunto rigoroso da[ISO/IEC-14496-](https://go.microsoft.com/fwlink/?LinkId=183695)*caixa* de execução de fragmentos de faixa da versão 1 definida na secção 8.8.8 da versão 1.
> 
>   **SampleCompositionTimeOffset (4 bytes):** O tempo de composição da amostra de cada amostra ajustado de modo a que o tempo de apresentação da primeira amostra apresentada no fragmento seja igual ao tempo de descodificação da primeira amostra descodificada. Devem ser utilizadas compensações negativas de composição da amostra de vídeo,
> 
>   conforme definido em [[ISO/IEC-14496-12].](https://go.microsoft.com/fwlink/?LinkId=183695)
> 
>   Nota: Isto evita um erro de sincronização de vídeo causado por áudio em atraso de vídeo igual ao maior atraso de remoção de imagem descodificado, e mantém o tempo de apresentação entre fragmentos alternativos que podem ter diferentes atrasos de remoção.
> 
>   A sintaxe dos campos definidos nesta secção, especificada em ABNF [[RFC5234],](https://go.microsoft.com/fwlink/?LinkId=123096) permanece a mesma, exceto a seguinte:
> 
>   SampleCompositionTimeOffset = SIGNED_INT32

#### <a name="2248-mdatbox"></a>2.2.4.8 MdatBox 

#### <a name="2249-fragment-response-common-fields"></a>2.2.4.9 Campos Comuns de Resposta fragmentada 

### <a name="225-sparse-stream-pointer"></a>2.2.5 Ponteiro de fluxo escasso 

### <a name="226-fragment-not-yet-available"></a>2.2.6 Fragmento ainda não disponível 

### <a name="227-live-ingest"></a>2.2.7 Ingest ao vivo 

#### <a name="2271-filetype"></a>2.2.7.1 Tipo de ficheiro 

>   **FileType (variável):** especifica o subtipo e a utilização prevista do ficheiro[MPEG-4 ([MPEG4-RA])](https://go.microsoft.com/fwlink/?LinkId=327787) e atributos de alto nível.
> 
>   **MajorBrand (variável):** A maior marca do ficheiro mediático. Deve ser definido para "isml".
> 
>   **Versão Menor (variável):** A versão menor do ficheiro mediático. Deve ser definido para 1.
> 
>   **Marcas compatíveis (variável):** Especifica as marcas suportadas da MPEG-4.
>   DEVE incluir "ccff" e "iso8".
> 
>   A sintaxe dos campos definidos nesta secção, especificada em ABNF [[RFC5234],](https://go.microsoft.com/fwlink/?LinkId=123096) é a seguinte:

    FileType = MajorBrand MinorVersion CompatibleBrands
    MajorBrand = STRING_UINT32
    MinorVersion = STRING_UINT32
    CompatibleBrands = "ccff" "iso8" 0\*(STRING_UINT32)

**Nota**: As marcas de compatibilidade 'ccff' e 'iso8' indicam que os fragmentos estão em conformidade com o "Formato Comum de Ficheiros de Contentores" e com a Encriptação Comum [ISO/IEC 23001-7] e com a ISO Base Media File Format Edition 4 [ISO/IEC 14496-12].

#### <a name="2272-streammanifestbox"></a>2.2.7.2 StreamManifestBox 

##### <a name="22721-streamsmil"></a>2.2.7.2.1 StreamsMIL 

#### <a name="2273-liveservermanifestbox"></a>2.2.7.3 LiveServerManifestBox 

##### <a name="22731-livesmil"></a>2.2.7.3.1 LiveSMIL 

#### <a name="2274-moovbox"></a>2.2.7.4 MoovBox 

#### <a name="2275-fragment"></a>2.2.7.5 Fragmento 

##### <a name="22751-track-fragment-extended-header"></a>2.2.7.5.1 Cabeçalho estendido de fragmento de pista 

### <a name="228-server-to-server-ingest"></a>2.2.8 Ingest servidor-a-servidor 

## <a name="3-protocol-details"></a>3 Detalhes do Protocolo 


## <a name="31-client-details"></a>3.1 Detalhes do Cliente 

### <a name="311-abstract-data-model"></a>3.1.1 Modelo de Dados Abstratos 

#### <a name="3111-presentation-description"></a>3.1.1.1 Descrição da apresentação 

>   O elemento de dados da Descrição de Apresentação engloba todos os metadados para a apresentação.
> 
>   Apresentação Metadados: Um conjunto de metadados que é comum a todos os streams na apresentação. Apresentação Metadados compreende os seguintes campos, especificados na secção *2.2.2.1:*
> 
> * **Grandeversão**
> * **Versão Menor**
> * **Escala de tempo**
> * **Duração**
> * **IsLive**
> * **Contaque de espera**
> * **DVRWindowLength**
> 
>   As apresentações que contenham correntes HEVC devem definir:

    MajorVersion = 2
    MinorVersion = 2

>   LookaheadCount = 0 (Nota: Caixas depreciadas)
> 
>   As apresentações devem também definir:

    TimeScale = 90000

>   Recolha de fluxo: Uma recolha de elementos de dados da Descrição do Fluxo, conforme especificado na secção *3.1.1.1.2*.
> 
>   Descrição da proteção: Recolha de elementos de dados de dados do sistema de proteção Metadados, conforme especificado na secção *3.1.1.1.1.*

##### <a name="31111-protection-system-metadata-description"></a>3.1.1.1.1 Descrição dos metadados do sistema de proteção 

>   O elemento de dados de descrição do sistema de proteção abrange metadados específicos de um único Sistema de Proteção de Conteúdos. (Sem Alteração)
> 
>   Descrição do cabeçalho de proteção: Metadados de proteção de conteúdo que dizem respeito a um único Sistema de Proteção de Conteúdos. Proteção Cabeçalho Descrição compreende os seguintes campos, especificados na secção *2.2.2.2:*
> 
>   * **SystemID**
>   * **ProtectionHeaderContent**

##### <a name="31112-stream-description"></a>3.1.1.1.2 Descrição do fluxo 

###### <a name="311121-track-description"></a>3.1.1.1.2.1 Descrição da faixa 

###### <a name="3111211-custom-attribute-description"></a>3.1.1.1.1.1.1.1.1 Descrição do atributo personalizado 

##### <a name="3113-fragment-reference-description"></a>3.1.1.3 Descrição de referência de fragmento 

###### <a name="31131-track-specific-fragment-reference-description"></a>3.1.1.3.1 Descrição específica da referência de fragmentos 

#### <a name="3112-fragment-description"></a>3.1.1.2 Descrição do fragmento 

##### <a name="31121-sample-description"></a>3.1.1.2.1 Descrição da amostra 

### <a name="312-timers"></a>3.1.2 Tempors 

### <a name="313-initialization"></a>3.1.3 Inicialização 

### <a name="314-higher-layer-triggered-events"></a>3.1.4 Eventos desencadeados em camada superior 

#### <a name="3141-open-presentation"></a>3.1.4.1 Apresentação aberta 

#### <a name="3142-get-fragment"></a>3.1.4.2 Obter Fragmento 

#### <a name="3143-close-presentation"></a>3.1.4.3 Apresentação de encerramento 

### <a name="315-processing-events-and-sequencing-rules"></a>3.1.5 Processamento de Eventos e Regras de Sequenciação 

#### <a name="3151-manifest-request-and-manifest-response"></a>3.1.5.1 Pedido manifesto e resposta manifesta 

#### <a name="3152-fragment-request-and-fragment-response"></a>3.1.5.2 Pedido de fragmento e resposta de fragmentos

## <a name="32-server-details"></a>3.2 Detalhes do Servidor

## <a name="33-live-encoder-details"></a>3.3 Detalhes do Codificador ao Vivo 

## <a name="4-protocol-examples"></a>4 Exemplos de Protocolo 

## <a name="5-security"></a>5 Segurança 

## <a name="51-security-considerations-for-implementers"></a>5.1 Considerações de segurança para implementadores

>   Se o conteúdo transportado através deste protocolo tiver um elevado valor comercial, deve ser utilizado um Sistema de Proteção de Conteúdos para impedir a utilização não autorizada do conteúdo. O **ProtectionElement** pode ser utilizado para transportar metadados relacionados com a utilização de um Sistema de Proteção de Conteúdos. Os conteúdos de áudio e vídeo protegidos devem ser encriptados conforme especificado pela MPEG Common Encryption Second Edition: 2015 [ISO/IEC 23001-7].
> 
>   **Nota:** Para o vídeo HEVC, apenas os dados de fatias em NALs VCL são encriptados. Os cabeçalhos de fatiae outros NALs estão acessíveis a aplicações de apresentação antes da desencriptação. numa trajetória de vídeo segura, a informação encriptada não está disponível para aplicações de apresentação.

## <a name="52-index-of-security-parameters"></a>Índice 5.2 Parâmetros de Segurança 


| **Parâmetro de segurança**  | **Section**         |
|-------------------------|---------------------|
| Elemento de Proteção       | *2.2.2.2*           |
| Caixas comuns de encriptação | *[ISO/IEC 23001-7]* |

## <a name="53-common-encryption-boxes"></a>5.3 Caixas comuns de encriptação

As seguintes caixas podem estar presentes em respostas de fragmentos quando a encriptação comum é aplicada, e são especificadas em [ISO/IEC 23001-7] ou [ISO/IEC 14496-12]:

1.  Caixa de cabeçalho específica do sistema de proteção ('pssh')

2.  Caixa de encriptação de amostras ('senc')

3.  Prove a Caixa de Compensação de Informações Auxiliares ('saio')

4.  Caixa de tamanho de informação auxiliar da amostra ('saiz')

5.  Caixa de descrição do grupo de amostras ('sgpd')

6.  Amostra para caixa de grupo ('sbgp')

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
