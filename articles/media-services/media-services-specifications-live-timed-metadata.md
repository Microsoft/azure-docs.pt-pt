---
title: Serviços de mídia do Azure – sinalizando metadados cronometrados na transmissão ao vivo | Microsoft Docs
description: Esta especificação descreve métodos para sinalizar metadados cronometrados ao ingerir e transmitir para os serviços de mídia do Azure. Isso inclui o suporte a ID3 (sinais de metadados cronometrados) genéricos, bem como a sinalização de SCTE-35 para a inserção do anúncio e a sinalização de condição de União.
services: media-services
documentationcenter: ''
author: johndeu
manager: femila
editor: johndeu
ms.assetid: 265b94b1-0fb8-493a-90ec-a4244f51ce85
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/2/2019
ms.author: johndeu
ms.openlocfilehash: 444d5ca996c014bdbf2e62cacf2563c7b63372e4
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "69015726"
---
# <a name="signaling-timed-metadata-in-live-streaming"></a>Sinalizando metadados cronometrados na transmissão ao vivo 

Última Atualização: 2019-07-02

### <a name="conformance-notation"></a>Notação de conformidade

As palavras-chave "deve", "não deve", "REQUIRED", "is", "MAY" e "OPTIONAL" neste documento devem ser interpretadas, conforme descrito em RFC 2119 (RECOMENDÁvel), "de maio",

## <a name="1-introduction"></a>1. Introdução 

Para sinalizar a inserção de anúncios ou eventos de metadados personalizados em um player de cliente, os difusores geralmente fazem uso de metadados cronometrados inseridos no vídeo. Para habilitar esses cenários, os serviços de mídia fornecem suporte para o transporte de metadados cronometrados do ponto de ingestão do canal de transmissão ao vivo para o aplicativo cliente.
Esta especificação descreve vários modos com suporte dos serviços de mídia para metadados cronometrados em sinais de transmissão ao vivo.

1. [SCTE-35] sinalização que está em conformidade com os padrões descritos por [SCTE-35], [SCTE-214-1], [SCTE-214-3] e [RFC8216]

2. [SCTE-35] sinalização que está em conformidade com a especificação herdada [Adobe-Primetime] para sinalização de anúncio RTMP.
   
3. Um modo de sinalização de metadados cronometrado genérico, para mensagens que **não** são [SCTE-35] e podem carregar [ID3v2] ou outros esquemas personalizados definidos pelo desenvolvedor de aplicativos.

## <a name="11-terms-used"></a>1,1 termos usados

| Termo              | Definição |
|-------------------|------------|
| Intervalo de anúncio          | Um local ou ponto no tempo em que um ou mais anúncios podem ser agendados para entrega; o mesmo que a oportunidade de posicionamento e disp. |
| Serviço de decisão do AD| serviço externo que decide quais AD (s) e durações serão mostrados para o usuário. Os serviços normalmente são fornecidos por um parceiro e estão fora do escopo deste documento.|
| Marcar               | Indicação de tempo e parâmetros do próximo intervalo de anúncio. Observe que as indicações podem indicar uma mudança pendente para um intervalo de anúncio, pendente alternar para o próximo anúncio dentro de um intervalo de anúncio e a mudança pendente de um intervalo de anúncio para o conteúdo principal. |
| Packager          | O "ponto de extremidade de streaming" dos serviços de mídia do Azure fornece recursos de empacotamento dinâmico para DASH e HLS e é chamado de "Packager" no setor de mídia. 
| Hora da apresentação | A hora em que um evento é apresentado a um visualizador. A hora representa o momento na linha do tempo de mídia em que um visualizador veria o evento. Por exemplo, o tempo de apresentação de uma mensagem de comando SCTE-35 splice_info () é o splice_time (). |
| Hora de chegada      | A hora em que uma mensagem de evento chega. O tempo normalmente é diferente do tempo de apresentação do evento, pois as mensagens de evento são enviadas antes da hora da apresentação do evento.                                     |
| Faixa esparsa      | faixa de mídia que não é contínua e é sincronizada com um pai ou faixa de controle.                                                                                                                                    |
| Origem            | O serviço de streaming de mídia do Azure                                                                                                                                                                                                |
| Coletor de canal      | O serviço Azure Media Live streaming                                                                                                                                                                                           |
| HLS               | Protocolo Apple HTTP Live Streaming                                                                                                                                                                                               |
| DASH              | Streaming adaptável dinâmico sobre HTTP                                                                                                                                                                                             |
| Suave            | Protocolo Smooth Streaming                                                                                                                                                                                                        |
| MPEG2-TS          | Fluxos de transporte MPEG 2                                                                                                                                                                                                         |
| RTMP              | Protocolo de multimídia em tempo real                                                                                                                                                                                                    |
| uimsbf            | Inteiro sem sinal, o bit mais significativo primeiro.                                                                                                                                                                                    |

---

## <a name="12-normative-references"></a>1,2 referências de normativas

Os documentos a seguir contêm provisões, que, por meio de referência neste texto, constituem as disposições deste documento. Todos os documentos estão sujeitos à revisão pelos órgãos de padrões, e os leitores são incentivados a investigar a possibilidade de aplicar as edições mais recentes dos documentos listados abaixo. Os leitores também são lembrados de que as edições mais recentes dos documentos referenciados podem não ser compatíveis com esta versão da especificação de metadados cronometrada para os serviços de mídia do Azure.


|Standard  |Definição  |
|---------|---------|
|[Adobe-Primetime] | [Especificação de sinalização de inserção de programa digital Primetime 1,2](https://www.adobe.com/content/dam/acom/en/devnet/primetime/PrimetimeDigitalProgramInsertionSignalingSpecification.pdf) |
|[Adobe-Flash-AS] | [Referência de linguagem FLASH ActionScript](https://help.adobe.com/archive/en_US/as2/flashlite_2.x_3.x_aslr.pdf) |
| AMF0            | ["Formato da mensagem de ação AMF0"](https://download.macromedia.com/pub/labs/amf/amf0_spec_121207.pdf) |
| [DASH-IF-IOP]     | Orientação de interoperabilidade do fórum do setor v 4,2[https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html](https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html) |
| [HLS-TMD]         | Metadados cronometrados para HTTP Live Streaming-[https://developer.apple.com/streaming](https://developer.apple.com/streaming) |
| [ID3v2]           | 2\.4.0 de versão da marca ID3[http://id3.org/id3v2.4.0-structure](http://id3.org/id3v2.4.0-structure) |
| [ISO-14496-12]    | ISO/IEC 14496-12: Parte 12 formato de arquivo de mídia de base ISO, FourthEdition 2012-07-15  |
| [MPEGDASH]        | Tecnologia da informação – streaming adaptável dinâmico sobre HTTP (DASH)--parte 1: Formatos de descrição e segmento de apresentação de mídia. Maio de 2014. Checked. URL: https://www.iso.org/standard/65274.html |
| [MPEGCMAF]        | Tecnologia da informação--formato de aplicativo de multimídia (MPEG-A)--parte 19: CMAF (formato de aplicativo de mídia comum) para mídia segmentada. Janeiro de 2018. Checked. URL: https://www.iso.org/standard/71975.html |
| [MPEGCENC]        | Tecnologia da informação – tecnologias de sistemas MPEG--parte 7: Criptografia comum em arquivos de formato de arquivo de mídia de base ISO. Fevereiro de 2016. Checked. URL: https://www.iso.org/standard/68042.html |
| [MS-SSTR]         | ["Microsoft Smooth Streaming Protocol", 15 de maio de 2014](https://docs.microsoft.com/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251) |
| [MS-SSTR-ingestão]  | [Especificação de ingestão dinâmica de MP4 fragmentado dos serviços de mídia do Azure](https://docs.microsoft.com/azure/media-services/media-services-fmp4-live-ingest-overview) |
| [RFC8216]         | R. Pantos, Ed.; W. Maio. HTTP Live Streaming. Agosto de 2017. Informativa. [https://tools.ietf.org/html/rfc8216](https://tools.ietf.org/html/rfc8216) |
| RFC4648         |As codificações de dados para base16, Base32 e Base64-[https://tools.ietf.org/html/rfc4648](https://tools.ietf.org/html/rfc4648) |
| RTMP            |["Protocolo de mensagens em tempo real da Adobe", 21 de dezembro de 2012](https://www.adobe.com/devnet/rtmp.html)  |
| [SCTE-35-2019]    | SCTE 35: 2019-mensagem de advertência de inserção de programa digital para cabo- https://www.scte.org/SCTEDocs/Standards/ANSI_SCTE%2035%202019r1.pdf  |
| [SCTE-214-1]      | SCTE 214-1 2016 – MPEG DASH para serviços de cabo baseados em IP parte 1: Restrições e extensões do MPD |
| [SCTE-214-3]      | SCTE 214-3 2015 MPEG DASH para serviços de cabo baseados em IP parte 3: Perfil de traço/FF |
| [SCTE-224]        | SCTE 224 2018r1 – agendamento de eventos e interface de notificação |
| [SCTE-250]        | API de gerenciamento de evento e sinalização (ESAM) |

---


## <a name="2-timed-metadata-ingest"></a>2. Ingestão de metadados cronometrado

## <a name="21-rtmp-ingest"></a>ingestão RTMP de 2,1

O [RTMP] permite que os sinais de metadados Timed sejam enviados como [AMF0] indicações de mensagens inseridas no fluxo [RTMP]. As mensagens de indicação podem ser enviadas algum tempo antes do evento real ou do sinal de junção do AD [SCTE35] precisar ocorrer. Para dar suporte a esse cenário, a hora real do evento é enviada dentro da mensagem de indicação. Para obter mais informações, consulte [AMF0].

As tabelas a seguir descrevem o formato da carga da mensagem AMF que os serviços de mídia ingerirão para os modos de mensagem "simples" e [SCTE35].

O nome da mensagem [AMF0] pode ser usado para diferenciar vários fluxos de eventos do mesmo tipo.  Para as mensagens [SCTE-35] e o modo "simples", o nome da mensagem AMF deve ser "onAdCue", conforme necessário na especificação [Adobe-Primetime].  Os campos não listados abaixo devem ser ignorados pelos serviços de mídia do Azure em ingestão.

## <a name="211-rtmp-signal-syntax"></a>Sintaxe de sinal RTMP 2.1.1

Os serviços de mídia do Azure podem escutar e responder a vários tipos de mensagem [AMF0] que podem ser usados para sinalizar vários metadados sincronizados em tempo real na transmissão ao vivo.  A especificação [Adobe-Primetime] define dois tipos de indicação chamados de modo "simples" e "SCTE-35". Para o modo "simples", os serviços de mídia dão suporte a uma única mensagem de indicação de AMF chamada "onAdCue" usando uma carga que corresponde à tabela abaixo definida para o sinal "modo simples".  

A seção a seguir mostra o conteúdo RTMP "modo simples", que pode ser usado para sinalizar um sinal de anúncio básico "cojunção" que será transmitido ao manifesto do cliente para HLS, DASH e Microsoft Smooth Streaming. Isso é muito útil para cenários em que o cliente não tem um sistema de inserção de sinalização de anúncio com base em SCTE-35 complexo, e está usando um codificador básico local para enviar na mensagem de indicação por meio de uma API. Normalmente, o codificador local dará suporte a uma API baseada em REST para disparar esse sinal, que também "colocará a condição" no fluxo de vídeo inserindo um quadro de IDR no vídeo e iniciando um novo GOP.

## <a name="212--simple-mode-ad-signaling-with-rtmp"></a>Sinalização de AD do modo simples do 2.1.2 com RTMP

| Nome do Campo | Tipo de campo | Obrigatório? | Descrições                                                                                                             |
|------------|------------|----------|--------------------------------------------------------------------------------------------------------------------------|
| marcar        | Cadeia     | Requerido | A mensagem do evento.  Deve ser "Splice" para designar uma junção de modo simples.                                              |
| id         | Cadeia     | Requerido | Um identificador exclusivo que descreve a junção ou o segmento. Identifica esta instância da mensagem                            |
| duration   | Número     | Requerido | A duração da junção. As unidades são segundos fracionários.                                                                |
| elapsed    | Número     | Opcional | Quando o sinal está sendo repetido para dar suporte ao ajuste no, esse campo deve ser a quantidade de tempo de apresentação decorrido desde o início da União. As unidades são segundos fracionários. Ao usar o modo simples, esse valor não deve exceder a duração original da junção.                                                  |
| time       | Número     | Requerido | Deve ser a hora da junção, em tempo de apresentação. As unidades são segundos fracionários.                                     |

---
 
## <a name="213-scte-35-mode-ad-signaling-with-rtmp"></a>Sinalização de anúncio do modo 2.1.3 SCTE-35 com RTMP

Quando você estiver trabalhando com um fluxo de trabalho de produção de difusão mais avançado que exige que a mensagem de carga completa do SCTE-35 seja transportada para o manifesto HLS ou DASH, é melhor usar o "modo SCTE-35" da especificação [Adobe-Primetime].  Esse modo dá suporte a sinais SCTE-35 em banda sendo enviados diretamente para um codificador ao vivo local, que codifica os sinais para o fluxo RTMP usando o "modo SCTE-35" especificado na especificação [Adobe-Primetime]. 

Normalmente, as mensagens SCTE-35 podem aparecer apenas em entradas de TS (fluxo de transporte MPEG-2) em um codificador local. Consulte o fabricante do codificador para obter detalhes sobre como configurar um ingestão de fluxo de transporte que contém SCTE-35 e habilitá-lo para passagem para RTMP no modo Adobe SCTE-35.

Nesse cenário, a carga a seguir deve ser enviada do codificador local usando o tipo de mensagem **"onAdCue"** [AMF0].

| Nome do Campo | Tipo de campo | Obrigatório? | Descrições                                                                                                             |
|------------|------------|----------|--------------------------------------------------------------------------------------------------------------------------|
| marcar        | Cadeia     | Requerido | A mensagem do evento.  Para mensagens [SCTE-35], ele deve ser o binário codificado na base64 [RFC4648] splice_info_section () para que as mensagens sejam enviadas aos clientes HLS, Smooth e Dash.                                              |
| type       | Cadeia     | Requerido | Um URN ou URL que identifica o esquema de mensagem. Para mensagens [SCTE-35], isso **deve** ser **"scte35"** para que as mensagens sejam enviadas aos clientes HLS, Smooth e Dash, em conformidade com [Adobe-Primetime]. Opcionalmente, o URN "urn: SCTE: scte35:2013: bin" também pode ser usado para sinalizar uma mensagem [SCTE-35]. |
| id         | Cadeia     | Requerido | Um identificador exclusivo que descreve a junção ou o segmento. Identifica essa instância da mensagem.  As mensagens com semântica equivalente devem ter o mesmo valor.|
| duration   | Número     | Requerido | A duração do evento ou do AD Splice-Segment, se conhecido. Se for desconhecido, o valor **deverá** ser 0.                                                                 |
| elapsed    | Número     | Opcional | Quando o sinal do AD [SCTE-35] está sendo repetido para ajuste, esse campo deve ser a quantidade de tempo de apresentação decorrido desde o início da União. As unidades são segundos fracionários. No modo [SCTE-35], esse valor pode exceder a duração especificada original da junção ou segmento.                                                  |
| time       | Número     | Requerido | A hora da apresentação do evento ou a junção do AD.  A hora e a duração da apresentação **devem** alinhar-se com os pontos de acesso do fluxo (SAP) do tipo 1 ou 2, conforme definido em [ISO-14496-12] anexo I. Para saída de HLS, o tempo e a duração **devem ser** alinhados com limites de segmento. A hora da apresentação e a duração de diferentes mensagens de evento dentro do mesmo fluxo de eventos não devem se sobrepor. As unidades são segundos fracionários.

---
## <a name="214-elemental-live-oncuepoint-ad-markers-with-rtmp"></a>Marcadores do AD "onCuePoint" ao vivo 2.1.4 com RTMP

O codificador local em tempo real dá suporte a marcadores de anúncio no sinal RTMP. Atualmente, os serviços de mídia do Azure dão suporte apenas ao tipo de marcador do AD "onCuePoint" para RTMP.  Isso pode ser habilitado nas configurações de grupo do Adobe RTMP nas configurações do codificador dinâmico de mídia elementar ou na API, definindo "**ad_markers**" como "onCuePoint".  Consulte a documentação do Live Element para obter detalhes. Habilitar esse recurso no grupo RTMP passará sinais SCTE-35 para as saídas do Adobe RTMP a serem processadas pelos serviços de mídia do Azure.

O tipo de mensagem "onCuePoint" é definido em [Adobe-Flash-AS] e tem a seguinte estrutura de carga quando enviado da saída RTMP ao vivo elementar.


| Propriedade  |Descrição  |
|---------|---------|
|  name     | O nome deve ser '**scte35**' por um elemento ao vivo. |
|time     |  O tempo em segundos no qual o ponto de sinalização ocorreu no arquivo de vídeo durante a linha do tempo |
| type     | O tipo de ponto de sinalização deve ser definido como "**evento**". |
| parameters | Uma matriz associativa de cadeias de caracteres de pares de nome/valor que contém as informações da mensagem SCTE-35, incluindo a ID e a duração. Esses valores são analisados pelos serviços de mídia do Azure e incluídos na marca de decoração do manifesto.  |


Quando esse modo de marcador do AD é usado, a saída do manifesto HLS é semelhante ao modo "simples" da Adobe. 

### <a name="215-cancellation-and-updates"></a>cancelamento e atualizações do 2.1.5

As mensagens podem ser canceladas ou atualizadas enviando-se várias mensagens com a mesma ID e hora da apresentação. A hora e a ID da apresentação identificam exclusivamente o evento, e a última mensagem recebida para um tempo de apresentação específico que atende às restrições de pré-roll é a mensagem que é tratada. O evento updated substitui todas as mensagens recebidas anteriormente. A restrição de pré-roll é de quatro segundos. As mensagens recebidas pelo menos quatro segundos antes da hora da apresentação serão aplicadas.

## <a name="22-fragmented-mp4-ingest-smooth-streaming"></a>2,2 ingestão de MP4 fragmentado (Smooth Streaming)

Consulte [MS-SSTR-ingestão] para obter os requisitos de ingestão de fluxo ao vivo. As seções a seguir fornecem detalhes sobre a ingestão de metadados de apresentação cronometrados.  Os metadados de apresentação cronometrados são ingeridos como uma faixa esparsa, que é definida na caixa de manifesto do servidor ao vivo (consulte MS-SSTR) e a caixa de filme (' Moov ').  

Cada fragmento esparso consiste em uma caixa de fragmento de filme (' Moof ') e Data Box de mídia (' mdat '), em que a caixa ' mdat ' é a mensagem binária.

Para obter a inserção precisa de quadros de anúncios, o codificador deve dividir o fragmento no momento da apresentação onde a indicação precisa ser inserida.  É necessário criar um novo fragmento que começa com um quadro de IDR recém-criado ou com os pontos de acesso de fluxo (SAP) do tipo 1 ou 2, conforme definido em [ISO-14496-12] anexo I. Isso permite que o Azure Media Packager gere corretamente um manifesto de HLS e um manifesto de vários períodos de traço, em que o novo período começa no tempo de apresentação com condição exata do quadro.

### <a name="221-live-server-manifest-box"></a>Caixa de manifesto do 2.2.1 Live Server

A faixa esparsa **deve** ser declarada na caixa de manifesto do Live **\<\>** Server com uma entrada TextStream e **deve** ter os seguintes atributos definidos:

| **Nome do atributo** | **Tipo de campo** | **Necessário?** | **Descrição**                                                                                                                                                                                                                                                 |
|--------------------|----------------|---------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| systemBitrate      | Número         | Requerido      | **Deve** ser "0", indicando uma faixa com taxa de bits de variável desconhecida.                                                                                                                                                                                                 |
| parentTrackName    | Cadeia         | Requerido      | **Deve** ser o nome da faixa pai, para a qual os códigos de tempo de faixa esparsa são alinhados em escala temporal. A faixa pai não pode ser uma faixa esparsa.                                                                                                                    |
| manifestOutput     | Booleano        | Requerido      | **Deve** ser "true" para indicar que a faixa esparsa será inserida no manifesto do cliente suave.                                                                                                                                                               |
| Subtipo            | Cadeia         | Requerido      | **Deve** ser o código de quatro caracteres "dados".                                                                                                                                                                                                                        |
| Esquema             | Cadeia         | Requerido      | **Deve** ser um urn ou uma URL que identifica o esquema de mensagem. Para mensagens [SCTE-35], **deve** ser "urn: SCTE: scte35:2013: bin" para que as mensagens sejam enviadas aos clientes HLS, Smooth e Dash em conformidade com [SCTE-35]. |
| trackName          | Cadeia         | Requerido      | **Deve** ser o nome da faixa esparsa. O trackname pode ser usado para diferenciar vários fluxos de eventos com o mesmo esquema. Cada fluxo de eventos exclusivo **deve** ter um nome de faixa exclusivo.                                                                           |
| timescale          | Número         | Opcional      | **Deve** ser a escala de temporal da faixa pai.                                                                                                                                                                                                                      |

---

### <a name="222-movie-box"></a>Caixa de filme 2.2.2

A caixa de filme (' Moov ') segue a caixa de manifesto do Live Server como parte do cabeçalho de fluxo de uma faixa esparsa.

A caixa ' Moov ' **deve** conter uma caixa **TrackHeaderBox (' tkhd ')** conforme definido em [ISO-14496-12] com as seguintes restrições:

| **Nome do campo** | **Tipo de campo**          | **Necessário?** | **Descrição**                                                                                                |
|----------------|-------------------------|---------------|----------------------------------------------------------------------------------------------------------------|
| duration       | inteiro de 64 bits sem sinal | Requerido      | **Deve** ser 0, pois a caixa de controle tem zero amostra e a duração total dos exemplos na caixa de controle é 0. |

---

A caixa ' Moov ' **deve** conter um **HandlerBox (' hdlr ')** conforme definido em [ISO-14496-12] com as seguintes restrições:

| **Nome do campo** | **Tipo de campo**          | **Necessário?** | **Descrição**   |
|----------------|-------------------------|---------------|-------------------|
| handler_type   | inteiro de 32 bits sem sinal | Requerido      | **Deve** ser ' meta '. |

---

A caixa ' stsd ' **deve** conter uma caixa MetaDataSampleEntry com um nome de codificação, conforme definido em [ISO-14496-12].  Por exemplo, para mensagens SCTE-35, o nome de codificação **deve** ser ' SCTE '.

### <a name="223-movie-fragment-box-and-media-data-box"></a>Caixa de fragmento de filme 2.2.3 e mídia Data Box

Fragmentos de faixa esparsa consistem em uma caixa de fragmento de filme (' Moof ') e uma Data Box de mídia (' mdat ').

> [!NOTE]
> Para obter a inserção precisa de quadros de anúncios, o codificador deve dividir o fragmento no momento da apresentação onde a indicação precisa ser inserida.  É necessário criar um novo fragmento que começa com um quadro de IDR recém-criado ou com os pontos de acesso de fluxo (SAP) do tipo 1 ou 2, conforme definido em [ISO-14496-12] anexo I
> 

A caixa caixa moviefragmentbox (' Moof ') **deve** conter uma caixa **TrackFragmentExtendedHeaderBox (' UUID ')** , conforme definido em [MS-SSTR], com os seguintes campos:

| **Nome do campo**         | **Tipo de campo**          | **Necessário?** | **Descrição**                                                                               |
|------------------------|-------------------------|---------------|-----------------------------------------------------------------------------------------------|
| fragment_absolute_time | inteiro de 64 bits sem sinal | Requerido      | **Deve** ser a hora de chegada do evento. Esse valor alinha a mensagem com a faixa pai.   |
| fragment_duration      | inteiro de 64 bits sem sinal | Requerido      | **Deve** ser a duração do evento. A duração pode ser zero para indicar que a duração é desconhecida. |

---


A caixa MediaDataBox (' mdat ') **deve** ter o seguinte formato:

| **Nome do campo**          | **Tipo de campo**                   | **Necessário?** | **Descrição**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|-------------------------|----------------------------------|---------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| version                 | inteiro de 32 bits sem sinal (uimsbf) | Requerido      | Determina o formato do conteúdo da caixa ' mdat '. As versões não reconhecidas serão ignoradas. Atualmente, a única versão com suporte é 1.                                                                                                                                                                                                                                                                                                                                                      |
| id                      | inteiro de 32 bits sem sinal (uimsbf) | Requerido      | Identifica essa instância da mensagem. As mensagens com semântica equivalente devem ter o mesmo valor; ou seja, o processamento de qualquer caixa de mensagem de evento com a mesma ID é suficiente.                                                                                                                                                                                                                                                                                                            |
| presentation_time_delta | inteiro de 32 bits sem sinal (uimsbf) | Requerido      | A soma de fragment_absolute_time, especificada em TrackFragmentExtendedHeaderBox, e presentation_time_delta **deve** ser a hora da apresentação do evento. A hora e a duração da apresentação **devem** alinhar-se com os pontos de acesso do fluxo (SAP) do tipo 1 ou 2, conforme definido em [ISO-14496-12] anexo I. Para saída de HLS, o tempo e a duração **devem ser** alinhados com limites de segmento. A hora da apresentação e a duração de diferentes mensagens de evento dentro do mesmo fluxo de eventos não **devem** se sobrepor. |
| message                 | matriz de bytes                       | Requerido      | A mensagem do evento. Para mensagens [SCTE-35], a mensagem é o splice_info_section binário (). Para mensagens [SCTE-35], isso **deve** ser o splice_info_section () para que as mensagens sejam enviadas aos clientes HLS, Smooth e Dash em conformidade com [SCTE-35]. Para mensagens [SCTE-35], o splice_info_section binário () é a carga da caixa ' mdat ' e **não** é codificado em base64.                                                            |

---


### <a name="224-cancellation-and-updates"></a>cancelamento e atualizações do 2.2.4

As mensagens podem ser canceladas ou atualizadas enviando-se várias mensagens com a mesma ID e hora da apresentação.  A hora e a ID da apresentação identificam exclusivamente o evento. A última mensagem recebida para um tempo de apresentação específico, que atende às restrições de pré-roll, é a mensagem que é tratada. A mensagem atualizada substitui todas as mensagens recebidas anteriormente.  A restrição de pré-roll é de quatro segundos. As mensagens recebidas pelo menos quatro segundos antes da hora da apresentação serão aplicadas. 


## <a name="3-timed-metadata-delivery"></a>3 entrega de metadados em tempo hábil

Os dados de fluxo de eventos são opacos para os serviços de mídia. Os serviços de mídia simplesmente passam três partes de informações entre o ponto de extremidade de ingestão e o ponto de extremidade do cliente. As propriedades a seguir são entregues ao cliente, em conformidade com [SCTE-35] e/ou o protocolo de streaming do cliente:

1.  Esquema – um URN ou URL que identifica o esquema da mensagem.
2.  Hora da apresentação – a hora da apresentação do evento na linha do tempo de mídia.
3.  Duração – a duração do evento.
4.  ID – um identificador exclusivo opcional para o evento.
5.  Mensagem – os dados do evento.

## <a name="31-microsoft-smooth-streaming-manifest"></a>3,1 manifesto do Microsoft Smooth Streaming  

Consulte tratamento de faixas esparsas [MS-SSTR] para obter detalhes sobre como formatar uma faixa de mensagens esparsas. Para mensagens [SCTE35], Smooth Streaming produzirá o splice_info_section codificado na Base64 () em um fragmento esparso.
O StreamIndex **deve** ter um subtipo de "data" e o CustomAttributes **deve** conter um atributo com Name = "Schema" e Value = "urn: SCTE: scte35:2013: bin".

#### <a name="smooth-client-manifest-example-showing-base64-encoded-scte35-splice_info_section"></a>Exemplo de manifesto de cliente suave mostrando codificação Base64 [SCTE35] splice_info_section ()
~~~ xml
<?xml version=”1.0” encoding=”utf-8”?>
<SmoothStreamingMedia MajorVersion=”2” MinorVersion=”0” TimeScale=”10000000” IsLive=”true” Duration=”0”
  LookAheadFragmentCount=”2” DVRWindowLength=”6000000000”>

  <StreamIndex Type=”video” Name=”video” Subtype=”” Chunks=”0” TimeScale=”10000000”
    Url=”QualityLevels({bitrate})/Fragments(video={start time})”>
    <QualityLevel Index=”0” Bitrate=”230000”
      CodecPrivateData=”250000010FC3460B50878A0B5821FF878780490800704704DC0000010E5A67F840” FourCC=”WVC1”
      MaxWidth=”364” MaxHeight=”272”/>
    <QualityLevel Index=”1” Bitrate=”305000”
      CodecPrivateData=”250000010FC3480B50878A0B5821FF87878049080894E4A7640000010E5A67F840” FourCC=”WVC1”
      MaxWidth=”364” MaxHeight=”272”/>
    <c t=”0” d=”20000000” r=”300” />
  </StreamIndex>
  <StreamIndex Type=”audio” Name=”audio” Subtype=”” Chunks=”0” TimeScale=”10000000”
    Url=”QualityLevels({bitrate})/Fragments(audio={start time})”>
    <QualityLevel Index=”0” Bitrate=”96000” CodecPrivateData=”1000030000000000000000000000E00042C0”
      FourCC=”WMAP” AudioTag=”354” Channels=”2” SamplingRate=”44100” BitsPerSample=”16” PacketSize=”4459”/>
    <c t=”0” d=”20000000” r=”300” />
  </StreamIndex>
  <StreamIndex Type=”text” Name=”scte35-sparse-stream” Subtype=”DATA” Chunks=”0” TimeScale=”10000000”
    ParentStreamIndex=”video” ManifestOutput=”true” 
    Url=”QualityLevels({bitrate})/Fragments(captions={start time})”>
    <QualityLevel Index=”0” Bitrate=”0” CodecPrivateData=”” FourCC=””>
      <CustomAttributes>
        <Attribute Name=”Scheme” Value=”urn:scte:scte35:2013:bin”/>
      </CustomAttributes>
    </QualityLevel>
    <!-- The following <c> and <f> fragments contains the base64-encoded [SCTE35] splice_info_section() message -->
    <c t=”600000000” d=”300000000”>    <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTUwMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
    <c t=”1200000000” d=”400000000”>      <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTYyMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
  </StreamIndex>
</SmoothStreamingMedia>
~~~

## <a name="32-apple-hls-manifest-decoration"></a>Decoração de manifesto do Apple HLS 3,2

Os serviços de mídia do Azure dão suporte às seguintes marcas de manifesto HLS para sinalizar informações sobre o AD DISP durante um evento ao vivo ou sob demanda. 

- EXT-X-DATERANGE conforme definido no Apple HLS [RFC8216]
- "EXT-X-CUE" conforme definido em [Adobe-Primetime]-esse modo é considerado "legacy". Os clientes devem adotar a marca EXT-X-DATERANGE quando possível.

A saída de dados para cada marca irá variar com base no modo de sinal de ingestão usado. Por exemplo, a ingestão RTMP com o modo simples da Adobe não contém a carga codificada em base64 SCTE-35 completa.

## <a name="321-apple-hls-with-adobe-primetime-ext-x-daterange-recommended"></a>3.2.1 Apple HLS com Adobe Primetime EXT-X-DATERANGE (recomendado)

A especificação Apple HTTP Live Streaming [RFC8216] permite a sinalização de mensagens [SCTE-35]. As mensagens são inseridas na lista de reprodução de segmentos em uma marca EXT-X-DATERANGE por [RFC8216] seção intitulada "Mapping SCTE-35 em EXT-X-DATERANGE".  A camada de aplicativo cliente pode analisar a playlist de M3U e processar marcas de M3U ou receber os eventos por meio da estrutura Apple Player.  

A abordagem **recomendada** nos serviços de mídia do Azure (API da versão 3) é seguir [RFC8216] e usar a marca ext-X_DATERANGE para a decoração do AD DISP [SCTE35] no manifesto.

## <a name="322-apple-hls-with-adobe-primetime-ext-x-cue-legacy"></a>3.2.2 Apple HLS com Adobe Primetime EXT-X-CUE (Herdado)

Também há uma implementação "herdada" fornecida nos serviços de mídia do Azure (versão 2 e 3 API) que usa a marca EXT-X-CUE, conforme definido no [Adobe-Primetime] "modo SCTE-35". Nesse modo, os serviços de mídia do Azure incorporarão a codificação Base64 [SCTE-35] splice_info_section () na marca EXT-X-CUE.  

A marca "legacy" EXT-X-CUE é definida como abaixo e também pode ser normativas referenciada na especificação [Adobe-Primetime]. Isso só deve ser usado para sinalização de SCTE35 herdada quando necessário; caso contrário, a marca recomendada é definida em [RFC8216] como EXT-X-DATERANGE. 

| **Nome do atributo** | **Tipo**                      | **Necessário?**                             | **Descrição**                                                                                                                                                                                                                                                                      |
|--------------------|-------------------------------|-------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| MARCAR                | Cadeia de caracteres entre aspas                 | Requerido                                  | A mensagem codificada como uma cadeia de caracteres codificada em base64, conforme descrito em [RFC4648]. Para mensagens [SCTE-35], este é o splice_info_section codificado em Base64 ().                                                                                                |
| TIPO               | Cadeia de caracteres entre aspas                 | Requerido                                  | Um URN ou URL que identifica o esquema de mensagem. Para mensagens [SCTE-35], o tipo usa o valor especial "scte35".                                                                                                                                |
| id                 | Cadeia de caracteres entre aspas                 | Requerido                                  | Um identificador exclusivo para o evento. Se a ID não for especificada quando a mensagem for ingerida, os serviços de mídia do Azure irão gerar uma ID exclusiva.                                                                                                                                          |
| PERMANÊNCIA           | número de ponto flutuante decimal | Requerido                                  | A duração do evento. Se for desconhecido, o valor **deverá** ser 0. As unidades são frações segundos.                                                                                                                                                                                           |
| DECORRIDO            | número de ponto flutuante decimal | Opcional, mas necessário para a janela deslizante | Quando o sinal está sendo repetido para dar suporte a uma janela de apresentação deslizante, esse campo **deve** ser a quantidade de tempo de apresentação decorrido desde o início do evento. As unidades são segundos fracionários. Esse valor pode exceder a duração especificada original da junção ou do segmento. |
| TIME               | número de ponto flutuante decimal | Requerido                                  | A hora da apresentação do evento. As unidades são segundos fracionários.                                                                                                                                                                                                                    |


A camada de aplicativo do HLS Player usará o tipo para identificar o formato da mensagem, decodificará a mensagem, aplicará as conversões de tempo necessárias e processará o evento.  Os eventos são sincronizados na lista de reprodução de segmentos da faixa pai, de acordo com o carimbo de data/hora do evento.  Eles são inseridos antes do segmento mais próximo (marca de #EXTINF).

### <a name="323-hls-segment-playlist-example-using-legacy-adobe-primetime-ext-x-cue"></a>Exemplo de playlist de segmento 3.2.3 HLS usando "legacy" Adobe Primetime EXT-X-CUE

O exemplo a seguir mostra a decoração de manifesto HLS usando a marca Primetime EXT-X-CUE da Adobe.  O parâmetro "CUE" contém a carga completa codificada em base64 SCTE-35 splice_info, que indica que esse sinal veio usando RTMP no modo de sinal do Adobe SCTE-35 ou que ele veio por meio do modo de sinal Smooth Streaming SCTE-35. 

~~~
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-ALLOW-CACHE:NO
#EXT-X-MEDIA-SEQUENCE:346
#EXT-X-TARGETDURATION:6
#EXT-X-I-FRAMES-ONLY
#EXT-X-PROGRAM-DATE-TIME:2018-12-13T15:54:19.462Z
#EXTINF:4.000000,no-desc
KeyFrames(video_track=15447164594627600,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
KeyFrames(video_track=15447164634627600,format=m3u8-aapl)
#EXT-X-CUE:ID="1026",TYPE="scte35",DURATION=30.000000,TIME=1544716520.022760,CUE="/DAlAAAAAAAAAP/wFAUAAAQCf+//KRjAfP4AKTLgAAAAAAAAVYsh2w=="
#EXTINF:6.000000,no-desc
KeyFrames(video_track=15447165474627600,format=m3u8-aapl)
~~~

### <a name="324-hls-message-handling-for-legacy-adobe-primetime-ext-x-cue"></a>Manipulação de mensagens do 3.2.4 HLS para "herdado" Adobe Primetime EXT-X-CUE

Os eventos são sinalizados na lista de reprodução de segmentos de cada faixa de vídeo e áudio. A posição da marca EXT-X-CUE sempre **deve** ser imediatamente anterior ao primeiro segmento HLS (para junção ou início de segmento) ou imediatamente após o último segmento de HLS (para junção no ou fim de segmento) ao qual seus atributos de tempo e duração se referem, como exigido por [Adobe-Primetime].

Quando uma janela de apresentação deslizante está habilitada, a marca EXT-X-CUE **deve** ser repetida com frequência suficiente para que a junção ou o segmento sempre seja totalmente descrito na lista de reprodução de segmentos, e o atributo decorrido **deve** ser usado para indicar a quantidade de tempo que o a junção ou o segmento esteve ativo, conforme exigido pelo [Adobe-Primetime].

Quando uma janela de apresentação deslizante estiver habilitada, as marcas EXT-X-CUE serão removidas da lista de reprodução de segmentos quando o tempo de mídia ao qual se referem foi distribuído da janela de apresentação deslizante.

## <a name="33-dash-manifest-decoration-mpd"></a>Decoração de manifesto de traço 3,3 (MPD)

[MPEGDASH] fornece três maneiras de sinalizar eventos:

1.  Eventos sinalizados no EventStream do MPD
2.  Eventos sinalizados em banda usando a caixa de mensagem de evento (' EMSG ')
3.  Uma combinação de 1 e 2

Eventos sinalizados no EventStream do MPD são úteis para o streaming do VOD porque os clientes têm acesso a todos os eventos, imediatamente quando o MPD é baixado. Ele também é útil para a sinalização SSAI, em que o fornecedor downstream SSAI precisa analisar os sinais de um manifesto MPD de vários períodos e inserir conteúdo do AD dinamicamente.  A solução em banda (' EMSG ') é útil para transmissão ao vivo, em que os clientes não precisam baixar o MPD novamente, ou não há nenhuma manipulação de manifesto SSAI acontecendo entre o cliente e a origem. 

O comportamento padrão dos serviços de mídia do Azure para DASH é sinalizar ambos em MPD EventStream e em banda usando a caixa de mensagem de evento (' EMSG ').

As mensagens de indicação ingeridas em [RTMP] ou [MS-SSTR-ingestão] são mapeadas em eventos DASH, usando caixas ' EMSG ' em banda e/ou EventStreams in-MPD. 

A sinalização em banda SCTE-35 para DASH segue a definição e os requisitos definidos em [SCTE-214-3] e também na seção [DASH-IF-IOP] 13.12.2 (' SCTE35 Events '). 

Para o carro em banda [SCTE-35], a caixa de mensagem de evento (' EMSG ') usa o schemeid = "urn: SCTE: scte35:2013: bin". Para decoração de manifesto do MPD, o EventStream schemeid usa "urn: SCTE: scte35:2014: XML + bin".  Esse formato é uma representação XML do evento que inclui uma saída binária codificada em base64 da mensagem SCTE-35 completa que chegou à ingestão. 

Normativas as definições de referência de carro de [SCTE-35] indicações de mensagens em DASH estão disponíveis em [SCTE-214-1] SEC 6.7.4 (MPD) e [SCTE-214-3] s 7.3.2 (carro de SCTE 35 Cue Messages).

### <a name="331-mpeg-dash-mpd-eventstream-signaling"></a>Sinalização do 3.3.1 MPEG DASH (MPD) EventStream

A decoração de eventos do manifesto (MPD) será sinalizada no MPD usando o elemento EventStream, que aparece dentro do elemento period. O schemeid usado é "urn: SCTE: scte35:2014: XML + bin".

> [!NOTE]
> Para fins de resumição [SCTE-35] permite o uso da seção codificada em base64 no elemento Signal. Binary (em vez do elemento Signal. SpliceInfoSection) como uma alternativa ao carro de uma mensagem de indicação completamente analisada.
> Os serviços de mídia do Azure usam essa abordagem de ' XML + bin ' para sinalizar no manifesto MPD.
> Esse também é o método recomendado usado na seção [DASH-IF-IOP]-consulte intitulada [' fluxos de eventos de inserção do AD ' do Dash se a diretriz de IOP](https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html#ads-insertion-event-streams)
> 

O elemento EventStream tem os seguintes atributos:

| **Nome do atributo** | **Tipo**                | **Necessário?** | **Descrição**                                                                                                                                                                                                                                                                                   |
|--------------------|-------------------------|---------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| scheme_id_uri      | Cadeia de caracteres                  | Requerido      | Identifica o esquema da mensagem. O esquema é definido como o valor do atributo de esquema na caixa do manifesto do Live Server. O valor **deve** ser um urn ou URL que identifica o esquema de mensagem; O schemeset de saída com suporte deve ser "urn: SCTE: scte35:2014: XML + bin" por [SCTE-214-1] SEC 6.7.4 (MPD), pois o serviço oferece suporte apenas a "XML + bin" neste momento para fins de brevidade no MPD.  |
| value              | Cadeia de caracteres                  | Opcional      | Um valor de cadeia de caracteres adicional usado pelos proprietários do esquema para personalizar a semântica da mensagem. Para diferenciar vários fluxos de eventos com o mesmo esquema, o valor **deve** ser definido como o nome do fluxo de eventos (trackname para [MS-SSTR-ingestão] ou o nome da mensagem AMF para ingestão [RTMP]). |
| Timescale          | inteiro de 32 bits sem sinal | Requerido      | A escala de e/s, em tiques por segundo.                                                                                                                                                                                                       |


### <a name="332-example-mpeg-dash-manifest-mpd-signaling-of-scte-35-using-eventstream"></a>exemplo de 3.3.2 do manifesto MPEG DASH (MPD) de SCTE-35 usando EventStream

~~~ xml
<!-- Example MPD using xml+bin style signaling per [SCTE-214-1] -->
  <EventStream schemeIdUri="urn:scte:scte35:2014:xml+bin" value="scte35_track_001_000" timescale="10000000">
        <Event presentationTime="15447165200227600" duration="300000000" id="1026">
            <scte35:Signal>
                <scte35:Binary>
                    /DAlAAAAAAAAAP/wFAUAAAQDf+//KaeGwP4AKTLgAAAAAAAAn75a3g==
                </scte35:Binary>
            </scte35:Signal>
        </Event>
        <Event presentationTime="15447166250227600" duration="300000000" id="1027">
           <scte35:Signal>
                <scte35:Binary>
                    /DAlAAAAAAAAAP/wFAUAAAQDf+//KaeGwP4AKTLgAAAAAAAAn75a3g==
                </scte35:Binary>
            </scte35:Signal>
        </Event>
    </EventStream>
~~~

> [!IMPORTANT]
> Observe que presentationtime é o tempo de apresentação do evento [SCTE-35] traduzido para ser relativo à hora de início do período, não à hora de chegada da mensagem.
> [MPEGDASH] define o Event@presentationTime como "especifica a hora da apresentação do evento em relação ao início do período.
> O valor do tempo de apresentação em segundos é a divisão do valor desse atributo e o valor do EventStream@timescale atributo.
> Se não estiver presente, o valor da hora da apresentação será 0.


### <a name="333-mpeg-dash-in-band-event-message-box-signaling"></a>Sinalização da caixa de mensagem de evento em banda do 3.3.3 MPEG DASH

Um fluxo de eventos em banda requer que o MPD tenha um elemento InbandEventStream no nível do conjunto de adaptação.  Esse elemento tem um atributo schemeIdUri obrigatório e um atributo de TIMESCALE opcional, que também aparecem na caixa de mensagem de evento (' EMSG ').  Caixas de mensagem de evento com identificadores de esquema que não estão definidos no MPD não **devem** estar presentes.

Para o carro em banda [SCTE-35], os sinais **devem** usar schemeid = "urn: SCTE: scte35:2013: bin".
As definições normativas de carro de [SCTE-35] mensagens em banda são definidas em [SCTE-214-3] s 7.3.2 (carro de SCTE mensagens de sinalização 35).

Os detalhes a seguir descrevem os valores específicos que o cliente deve esperar em ' EMSG ' em conformidade com [SCTE-214-3]:

| **Nome do campo**          | **Tipo de campo**          | **Necessário?** | **Descrição**                                                                                                                                                                                                                                                                                                                                                    |
|-------------------------|-------------------------|---------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| scheme_id_uri           | Cadeia de caracteres                  | Requerido      | Identifica o esquema da mensagem. O esquema é definido como o valor do atributo de esquema na caixa do manifesto do Live Server. O valor **deve** ser um urn que identifica o esquema de mensagem. Para mensagens [SCTE-35], **deve** ser "urn: SCTE: scte35:2013: bin" em conformidade com [SCTE-214-3] |
| Value                   | Cadeia de caracteres                  | Requerido      | Um valor de cadeia de caracteres adicional usado pelos proprietários do esquema para personalizar a semântica da mensagem. Para diferenciar vários fluxos de eventos com o mesmo esquema, o valor será definido como o nome do fluxo de eventos (trackname para ingestão suave ou nome da mensagem AMF para ingestão RTMP).                                                                  |
| Timescale               | inteiro de 32 bits sem sinal | Requerido      | A escala de tempo, em tiques por segundo, dos campos horas e duração na caixa ' EMSG '.                                                                                                                                                                                                                                                                        |
| Presentation_time_delta | inteiro de 32 bits sem sinal | Requerido      | O intervalo de tempo de apresentação de mídia do tempo de apresentação do evento e o tempo de apresentação mais antigo neste segmento. A hora e a duração da apresentação **devem** alinhar-se com os pontos de acesso do fluxo (SAP) do tipo 1 ou 2, conforme definido em [ISO-14496-12] anexo I.                                                                                            |
| event_duration          | inteiro de 32 bits sem sinal | Requerido      | A duração do evento ou 0xFFFFFFFF para indicar uma duração desconhecida.                                                                                                                                                                                                                                                                                          |
| ID                      | inteiro de 32 bits sem sinal | Requerido      | Identifica essa instância da mensagem. As mensagens com semântica equivalente devem ter o mesmo valor. Se a ID não for especificada quando a mensagem for ingerida, os serviços de mídia do Azure irão gerar uma ID exclusiva.                                                                                                                                                    |
| Message_data            | matriz de bytes              | Requerido      | A mensagem do evento. Para mensagens [SCTE-35], os dados da mensagem são o splice_info_section binário () em conformidade com [SCTE-214-3] |

### <a name="334-dash-message-handling"></a>Manipulação de mensagens do 3.3.4 DASH

Os eventos são sinalizados em banda, dentro da caixa ' EMSG ', para faixas de vídeo e áudio.  A sinalização ocorre para todas as solicitações de segmento para as quais o presentation_time_delta é 15 segundos ou menos. 

Quando uma janela de apresentação deslizante estiver habilitada, as mensagens de evento serão removidas do MPD quando a soma da hora e da duração da mensagem do evento for menor do que a hora dos dados de mídia no manifesto.  Em outras palavras, as mensagens de evento são removidas do manifesto quando o tempo de mídia para o qual elas se referem foi distribuído da janela apresentação deslizante.

## <a name="4-scte-35-ingest-implementation-guidance-for-encoder-vendors"></a>4. Diretrizes de implementação de ingestão de SCTE-35 para fornecedores de codificador

As diretrizes a seguir são problemas comuns que podem afetar a implementação de um fornecedor de codificador dessa especificação.  As diretrizes a seguir foram coletadas com base nos comentários do parceiro real para facilitar a implementação dessa especificação para outras pessoas. 

[SCTE-35] as mensagens são ingeridas no formato binário usando o esquema **"urn: SCTE: scte35:2013: bin"** para [MS-SSTR-ingestão] e o tipo **"scte35"** para ingestão [RTMP]. Para facilitar a conversão de tempo [SCTE-35], que se baseia em carimbos de data/hora de apresentação do fluxo de transporte MPEG-2 (PTS), um mapeamento entre PTS (pts_time + pts_adjustment do splice_time ()) e a linha do tempo da mídia são fornecidos pelo tempo de apresentação do evento ( o campo fragment_absolute_time para ingestão suave e o campo de hora para ingestão RTMP). O mapeamento é necessário porque o valor de PTS de 33 bits se acumula aproximadamente a cada 26,5 horas.

Smooth Streaming ingestão [MS-SSTR-ingestão] requer que o Data Box de mídia (' mdat ') **deva** conter o **splice_info_section ()** definido em [SCTE-35]. 

Para ingestão RTMP, o atributo Cue da mensagem AMF é definido como o splice_info_section codificado na base64 **()** definido em [SCTE-35].  

Quando as mensagens têm o formato descrito acima, elas são enviadas aos clientes HLS, Smooth e DASH, conforme definido acima.  

Ao testar sua implementação com a plataforma de serviços de mídia do Azure, comece testando com um LiveEvent de "passagem" primeiro, antes de passar para o teste em um LiveEvent de codificação.

---

## <a name="next-steps"></a>Passos Seguintes
Exibir os roteiros de aprendizagem dos serviços de mídia.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
