---
title: Serviços de multimédia do Azure - sinalização metadados temporizados na transmissão em direto | Documentos da Microsoft
description: Essa especificação descreve dois modos que são suportados pelos serviços de multimédia de sinalização metadados temporizados na transmissão em direto. Isto inclui suporte para sinais de metadados temporizados genérica, bem como SCTE 35 sinalização para inserção de fusão de publicidade.
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
ms.date: 03/20/2019
ms.author: johndeu;
ms.openlocfilehash: 10dbf7e8cf67ab721cf525d4a1e7594473592bd4
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58295181"
---
# <a name="signaling-timed-metadata-in-live-streaming"></a>Assinalar metadados temporizados na transmissão em direto 


## <a name="1-introduction"></a>1 Introdução 
Para facilitar a inserção de anúncios ou eventos personalizados num leitor de cliente, emissoras, muitas vezes, faça uso de metadados temporizados incorporado no vídeo. Para ativar estes cenários, os Media Services fornecem suporte para o transporte de metadados temporizados juntamente com o suporte de dados, do ponto de ingestão do canal de transmissão em fluxo em direto para a aplicação cliente.
Esta especificação contornos dois modos que são suportados pelos serviços de multimédia para os metadados temporizados dentro do live sinais de transmissão em fluxo:

1. [SCTE-35] sinalizando que heeds as práticas recomendadas descritas por [SCTE 67]

2. Excedeu o tempo limite a um genérico metadados modo, para as mensagens que não são de sinalização [SCTE-35]

### <a name="12-conformance-notation"></a>1.2 a notação de conformidade de
As palavras-chave "tem", "Não deve", "Obrigatório", "SHALL", "Não deve", "SHOULD", "Não deve", "Recomendados", "Mai" e "Opcional" neste documento devem ser interpretados conforme descrito em RFC 2119

### <a name="13-terms-used"></a>1.3 termos de utilizados

| Termo              | Definição                                                                                                                                                                                                                       |
|-------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Tempo de apresentação | O tempo que um evento é apresentado a um Visualizador de mensagens em fila. O tempo representa o momento em que a linha cronológica de suporte de dados que um visualizador veria o evento. Por exemplo, o tempo de apresentação de uma mensagem de comando de splice_info() SCTE 35 é o splice_time(). |
| Hora da chegada      | O tempo que uma mensagem de evento é recebido. O tempo é normalmente distinto desde o momento de apresentação do evento, uma vez que as mensagens de eventos são enviadas antes do tempo a apresentação do evento.                                     |
| Track disperso      | controlar o suporte de dados que não é contínuo e é hora sincronizada com um Roteiro de encarregado de educação ou controle.                                                                                                                                    |
| Origem            | O serviço de transmissão em fluxo de multimédia do Azure                                                                                                                                                                                                |
| Sink de canal      | O suporte de dados do Azure em serviço de transmissão em direto                                                                                                                                                                                           |
| HLS               | Protocolo Apple HTTP Live Streaming                                                                                                                                                                                               |
| TRAÇO              | Dynamic adaptável de transmissão em fluxo através de HTTP                                                                                                                                                                                             |
| Suave            | Protocolo de transmissão em fluxo uniforme                                                                                                                                                                                                        |
| MPEG2-TS          | Fluxos de 2 de transporte MPEG                                                                                                                                                                                                         |
| RTMP              | Protocolo de multimídia em tempo real                                                                                                                                                                                                    |
| uimsbf            | Número inteiro sem sinal, mais significativas bit pela primeira vez.                                                                                                                                                                                    |

-----------------------

## <a name="2-timed-metadata-ingest"></a>Ingestão de metadados temporizados 2
## <a name="21-rtmp-ingest"></a>2.1 de ingestão RTMP
RTMP suporta sinais de metadados temporizados enviados como mensagens de indicação AMF incorporadas dentro do fluxo RTMP. As mensagens de indicação podem ser enviadas algum tempo antes do evento real ou a inserção de fusão ad tem de ocorrer. Para suportar este cenário, o tempo em questão da fusão ou segmento é enviado na mensagem de remediar. Para obter mais informações, consulte [AMF0].

A tabela seguinte descreve o formato do payload da mensagem AMF que será ingerir dos serviços de multimédia.

O nome da mensagem AMF pode ser utilizado para diferenciar vários fluxos de eventos do mesmo tipo.  Para mensagens de [SCTE-35], o nome da mensagem AMF tem de ser "onAdCue" conforme recomendado nas [SCTE 67].  Quaisquer campos não listados abaixo têm de ser ignorados, para que a inovação deste design não é inibida no futuro.

### <a name="signal-syntax"></a>Sintaxe de sinal
Para o modo simple de RTMP, serviços de multimédia suportam uma única mensagem de indicação AMF chamada "onAdCue" com o seguinte formato:

### <a name="simple-mode"></a>Modo Simple

| Nome do Campo | Tipo de campo | Necessário? | Descrições                                                                                                             |
|------------|------------|----------|--------------------------------------------------------------------------------------------------------------------------|
| indicação        | String     | Necessário | A mensagem de evento.  Deve ser "SpliceOut" para designar um modo simple fusão.                                              |
| ID         | String     | Necessário | Um identificador exclusivo que descrevem a fusão ou segmento. Identifica esta instância da mensagem                            |
| duração   | Number     | Necessário | Durante o processo de fusão. As unidades são segundos fracionais.                                                                |
| elapsed    | Number     | Opcional | Quando o sinal é que está a ser repetido para oferecer suporte à Sintonize, este campo deve ser a quantidade de tempo de apresentação que tiver sido decorrido desde a fusão início. As unidades são segundos fracionais. Ao utilizar o modo simple, este valor não deve exceder o período original da fusão.                                                  |
| hora       | Number     | Necessário | Será o momento da fusão, em tempo de apresentação. As unidades são segundos fracionais.                                     |

---------------------------

### <a name="scte-35-mode"></a>Modo de SCTE 35

| Nome do Campo | Tipo de campo | Necessário? | Descrições                                                                                                             |
|------------|------------|----------|--------------------------------------------------------------------------------------------------------------------------|
| indicação        | String     | Necessário | A mensagem de evento.  Para mensagens de [SCTE-35], tem de ser o base64 (4648 de RFC da IETF) binário codificado splice_info_section() mensagens a serem enviados para HLS, uniforme, Dash clientes e em conformidade com [SCTE 67].                                              |
| tipo       | String     | Necessário | Um URN ou uma URL, identificando o esquema de mensagem. Para mensagens de [SCTE-35], tem de ser "urn: scte:scte35:2013a:bin" para que as mensagens sejam enviadas para HLS, uniforme, Dash clientes e em conformidade com [SCTE 67].  |
| ID         | String     | Necessário | Um identificador exclusivo que descrevem a fusão ou segmento. Identifica esta instância da mensagem.  As mensagens com uma semântica equivalente deverá ter o mesmo valor.|
| duração   | Number     | Necessário | A duração do evento ou ad fusão-segmento, se conhecidos. Se desconhecido, o valor deve ser 0.                                                                 |
| elapsed    | Number     | Opcional | Quando o sinal de ad [SCTE-35] está a ser repetido para Prepare-se, este campo deve ter a quantidade de tempo de apresentação que tiver sido decorrido desde a fusão início. As unidades são segundos fracionais. No modo de [SCTE-35], este valor pode exceder a duração especificada original da fusão ou segmento.                                                  |
| hora       | Number     | Necessário | A hora de apresentação do evento ou ad fusão.  O tempo de apresentação e a duração devem alinhar com pontos de acesso de Stream (SAP) do tipo 1 ou 2, conforme definido na [ISO-14496-12] Annex I. Saída HLS, de tempo e a duração devem alinhar com os limites de segmento. O tempo de apresentação e a duração de mensagens de eventos diferentes no mesmo fluxo de eventos não se podem sobrepor. As unidades são segundos fracionais.

---------------------------

#### <a name="211-cancellation-and-updates"></a>2.1.1 cancelamento e atualizações

As mensagens podem ser canceladas ou atualizadas através do envio de várias mensagens com o mesmo tempo de apresentação e o mesmo ID. O tempo de apresentação e o ID de identificam de forma exclusiva o evento e a última mensagem recebida por um período de apresentação específico que cumpre as restrições de pré-roll é a mensagem de ação. O evento atualizado substitui todas as mensagens recebidas anteriormente. A restrição de pré-agregação de é de quatro segundos. Serão influenciadas mensagens recebidas, pelo menos, quatro segundos antes do tempo de apresentação.

## <a name="22-fragmented-mp4-ingest-smooth-streaming"></a>2.2 fragmentados de MP4 de ingestão (transmissão em fluxo uniforme)
Consulte [LIVE-FMP4] para ingerir os requisitos de transmissão em direto. As secções seguintes fornecem informações detalhadas acerca de ingestão de metadados temporizados apresentação.  Metadados de apresentação temporizados são ingeridos como um Roteiro disperso, o que é definido em ambos os Live caixa do servidor de manifesto (consulte MS SSTR) e a caixa de filmes ('moov').  Cada fragmento disperso consiste numa caixa de fragmento de filmes ('moof") e o suporte de dados Data Box ('mdat"), onde a caixa de 'mdat' está a mensagem binária.

#### <a name="221-live-server-manifest-box"></a>2.2.1 caixa de manifesto do servidor ao vivo
A faixa dispersa tem de ser declarada na caixa de manifesto de servidor ao vivo com um \<textstream\> entrada e ele tem de ter os seguintes atributos definido:

| **Nome de atributo** | **Tipo de campo** | **Necessário?** | **Descrição**                                                                                                                                                                                                                                                 |
|--------------------|----------------|---------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| systemBitrate      | Number         | Necessário      | TEM de ser "0", que indica uma faixa com velocidade de transmissão desconhecida, a variável.                                                                                                                                                                                                 |
| parentTrackName    | String         | Necessário      | TEM de ser o nome da faixa principal, para que os códigos de tempo de dispersas track são escala temporal alinhado. A faixa de principal não pode ser um Roteiro disperso.                                                                                                                    |
| manifestOutput     | Booleano        | Necessário      | TEM de ser "true", para indicar que a faixa dispersa será incorporada no manifesto do cliente Smooth.                                                                                                                                                               |
| Subtipo            | String         | Necessário      | DEVE ser "Dados de" código de quatro caracteres.                                                                                                                                                                                                                         |
| Esquema             | String         | Necessário      | TEM de ser um URN ou uma URL identificando o esquema de mensagem. Para mensagens de [SCTE-35], tem de ser "urn: scte:scte35:2013a:bin" para que as mensagens sejam enviadas para HLS, uniforme, Dash clientes e em conformidade com [SCTE 67]. |
| trackName          | String         | Necessário      | TEM de ser o nome da faixa dispersa. O trackName pode ser utilizado para diferenciar vários fluxos de eventos com o mesmo esquema. Cada fluxo de eventos exclusivo tem de ter um nome exclusivo.                                                                           |
| escala temporal          | Number         | Opcional      | TEM de ser a escala temporal da faixa principal.                                                                                                                                                                                                                      |

-------------------------------------

### <a name="222-movie-box"></a>2.2.2 caixa de filme

A caixa de filmes ('moov') segue Live manifesto caixa servidor como parte do cabeçalho do stream para uma faixa dispersa.

A caixa "moov" deve conter uma **TrackHeaderBox ('tkhd')** caixa conforme definido na [ISO-14496-12] com as seguintes restrições:

| **Nome do campo** | **Tipo de campo**          | **Necessário?** | **Descrição**                                                                                                |
|----------------|-------------------------|---------------|----------------------------------------------------------------------------------------------------------------|
| duração       | número inteiro não assinado de 64 bits | Necessário      | DEVE ser 0, uma vez que a caixa de controle tem zero exemplos e a duração total dos exemplos na caixa de controle é 0. |

-------------------------------------

A caixa "moov" deve conter uma **HandlerBox ('hdlr')** conforme definido na [ISO-14496-12] com as seguintes restrições:

| **Nome do campo** | **Tipo de campo**          | **Necessário?** | **Descrição**   |
|----------------|-------------------------|---------------|-------------------|
| handler_type   | número inteiro sem sinal de 32 bits | Necessário      | DEVE ser "metadados". |

-------------------------------------

A caixa "stsd" deve conter uma caixa de MetaDataSampleEntry com um nome de codificação, tal como definido em [ISO-14496-12].  Por exemplo, para mensagens de SCTE 35 o nome de codificação deve ser 'scte'.

### <a name="223-movie-fragment-box-and-media-data-box"></a>2.2.3 caixa de fragmento de filme e suporte de dados Data Box

Fragmentos de controlar dispersas consistem numa caixa de fragmento de filmes ('moof") e uma caixa de dados de suporte de dados ('mdat').

A caixa de MovieFragmentBox ('moof') tem de conter um **TrackFragmentExtendedHeaderBox ('uuid')** caixa conforme definido na [MS-SSTR] com os seguintes campos:

| **Nome do campo**         | **Tipo de campo**          | **Necessário?** | **Descrição**                                                                               |
|------------------------|-------------------------|---------------|-----------------------------------------------------------------------------------------------|
| fragment_absolute_time | número inteiro não assinado de 64 bits | Necessário      | TEM de ser o tempo de chegada do evento. Este valor se Alinha a mensagem com o controle pai.   |
| fragment_duration      | número inteiro não assinado de 64 bits | Necessário      | TEM de ser a duração do evento. A duração pode ser zero para indicar que a duração é desconhecida. |

------------------------------------


A caixa de MediaDataBox ('mdat') tem de ter o seguinte formato:

| **Nome do campo**          | **Tipo de campo**                   | **Necessário?** | **Descrição**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|-------------------------|----------------------------------|---------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| versão                 | número inteiro sem sinal de 32 bits (uimsbf) | Necessário      | Determina o formato do conteúdo da caixa de 'mdat'. Versões não reconhecidas serão ignoradas. Atualmente a única versão suportada é 1.                                                                                                                                                                                                                                                                                                                                                      |
| ID                      | número inteiro sem sinal de 32 bits (uimsbf) | Necessário      | Identifica esta instância da mensagem. Mensagens com uma semântica equivalente deverá ter o mesmo valor; ou seja, qualquer caixa de mensagem de um evento com o mesmo id de processamento é suficiente.                                                                                                                                                                                                                                                                                                            |
| presentation_time_delta | número inteiro sem sinal de 32 bits (uimsbf) | Necessário      | A soma de fragment_absolute_time, especificada a TrackFragmentExtendedHeaderBox e o presentation_time_delta tem de ser o tempo de apresentação do evento. O tempo de apresentação e a duração devem alinhar com pontos de acesso de Stream (SAP) do tipo 1 ou 2, conforme definido na [ISO-14496-12] Annex I. Saída HLS, de tempo e a duração devem alinhar com os limites de segmento. O tempo de apresentação e a duração de mensagens de eventos diferentes no mesmo fluxo de eventos não se podem sobrepor. |
| message                 | matriz de bytes                       | Necessário      | A mensagem de evento. Para mensagens de [SCTE-35], a mensagem é o splice_info_section() binária, embora [SCTE 67] recomenda outra coisa. Para mensagens de [SCTE-35], tem de ser splice_info_section() mensagens a serem enviados para HLS, uniforme, Dash clientes e em conformidade com [SCTE 67]. Para mensagens de [SCTE-35], o binário splice_info_section() é o payload da caixa de 'mdat' e não é codificado em base64.                                                            |

------------------------------


### <a name="224-cancellation-and-updates"></a>2.2.4 cancelamento e atualizações
As mensagens podem ser canceladas ou atualizadas através do envio de várias mensagens com o mesmo tempo de apresentação e o mesmo ID.  O tempo de apresentação e o ID de identificam de forma exclusiva o evento. A última mensagem recebida por um período de apresentação específico que cumpre as restrições de pré-implementação, é a mensagem de ação. A mensagem atualizada substitui todas as mensagens recebidas anteriormente.  A restrição de pré-agregação de é de quatro segundos. Serão influenciadas mensagens recebidas, pelo menos, quatro segundos antes do tempo de apresentação. 


## <a name="3-timed-metadata-delivery"></a>3 atingiu o tempo de entrega de metadados

Dados de fluxo de eventos são opacos para serviços de multimédia. Serviços de multimédia simplesmente passa três partes de informações entre o ponto final de ingestão e o ponto de extremidade do cliente. As seguintes propriedades são entregues ao cliente, em conformidade com [SCTE 67] e/ou o cliente do protocolo de streaming:

1.  Esquema – um URN ou o URL de identificar o esquema da mensagem.

2.  Hora de apresentação – o tempo de apresentação do evento na linha do tempo de suporte de dados.

3.  Duração – a duração do evento.

4.  ID – um identificador exclusivo do opcional para o evento.

5.  Mensagem – os dados do evento.


## <a name="31-smooth-streaming-delivery"></a>3.1 entrega de transmissão em fluxo uniforme de

Consulte a controlar dispersa processar detalhes na [MS-SSTR].

#### <a name="smooth-client-manifest-example"></a>Exemplo de manifesto do cliente Smooth
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
  <StreamIndex Type=”text” Name=”scte35-event-stream” Subtype=”DATA” Chunks=”0” TimeScale=”10000000”
    ParentStreamIndex=”video” ManifestOutput=”true” 
    Url=”QualityLevels({bitrate})/Fragments(captions={start time})”>
    <QualityLevel Index=”0” Bitrate=”0” CodecPrivateData=”” FourCC=””>
      <CustomAttributes>
        <Attribute Name=”Scheme” Value=”urn:scte:scte35:2013a:bin”/>
      </CustomAttributes>
    </QualityLevel>
    <!-- <f> contains base-64 encoded splice_info_section() message 
    <c t=”600000000” d=”300000000”>
<f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTUwMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
    <c t=”1200000000” d=”400000000”>      <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTYyMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
  </StreamIndex>
</SmoothStreamingMedia> 
~~~ 

## <a name="32--apple-hls-delivery"></a>3.2 Apple HLS entrega
Metadados temporizados para Apple HTTP Live Streaming (HLS) podem ser incorporado na lista de reprodução do segmento numa marca de M3U personalizada.  A camada de aplicativo pode analisar a lista de reprodução M3U e processar M3U etiquetas. Serviços de multimédia do Azure irá incorporar metadados temporizados na marca de EXT-X-indicação definida em [HLS].  A marca X-EXT-indicação está atualmente utilizada por DynaMux para mensagens do tipo ADI3.  Para oferecer suporte a mensagens de SCTE 35 SCTE 35 e não, defina os atributos da etiqueta EXT-X-indicação como definido abaixo:

| **Nome de atributo** | **Tipo**                      | **Necessário?**                             | **Descrição**                                                                                                                                                                                                                                                                      |
|--------------------|-------------------------------|-------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| INDICAÇÃO                | cadeia de caracteres com aspas                 | Necessário                                  | A mensagem codificada como uma cadeia base64, conforme descrito em [IETF RFC 4648](https://tools.ietf.org/html/rfc4648). Para mensagens de [SCTE-35], este é o splice_info_section() codificada em base64.                                                                                                |
| TIPO               | cadeia de caracteres com aspas                 | Necessário                                  | Um URN ou uma URL, identificando o esquema de mensagem. Para mensagens de [SCTE-35], o tipo usa o valor especial "scte35".                                                                                                                                |
| ID                 | cadeia de caracteres com aspas                 | Necessário                                  | Um identificador exclusivo para o evento. Se o ID não for especificado, quando a mensagem é ingerida, serviços de multimédia do Azure irá gerar um id exclusivo.                                                                                                                                          |
| DURAÇÃO           | número de ponto flutuante decimal | Necessário                                  | A duração do evento. Se desconhecido, o valor deve ser 0. As unidades são factional segundos.                                                                                                                                                                                           |
| DECORRIDOS            | número de ponto flutuante decimal | Opcional, mas necessário para a janela deslizante | Quando o sinal é que está a ser repetido para oferecer suporte a uma janela deslizante de apresentação, este campo tem de ser a quantidade de tempo de apresentação que tiver sido decorrido, uma vez que o evento começou. As unidades são segundos fracionais. Este valor pode exceder a duração especificada original da fusão ou segmento. |
| HORA               | número de ponto flutuante decimal | Necessário                                  | O tempo de apresentação do evento. As unidades são segundos fracionais.                                                                                                                                                                                                                    |


A camada de aplicação de leitor HLS irá utilizar o tipo para identificar o formato da mensagem, decodificar a mensagem, aplique as conversões de tempo necessário e processar o evento.  Os eventos são sincronizado na lista de reprodução da segmento do controle pai, de momento, de acordo com o carimbo de hora do evento.  Eles são inseridos antes do mais próximo segmento (etiqueta #EXTINF).

#### <a name="hls-segment-playlist-example"></a>Exemplo de lista de reprodução do segmento HLS
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

#### <a name="hls-message-handling"></a>Manipulação de mensagens de HLS

Eventos são sinalizados na lista de reprodução da segmento de cada faixa de áudio e vídeo. A posição da etiqueta EXT-X-indicação sempre tem de ser imediatamente antes dos primeiro HLS segmentar (para unir horizontalmente ou início de segmento) ou imediatamente após os último HLS segmentar (para unir no ou fim do segmento), para que se referem seus atributos de tempo e a duração, conforme requerido pelo [ HLS].

Quando uma janela deslizante de apresentação está ativada, a marca X-EXT-indicação deve ser repetida de com frequência suficiente que o fusão ou segmento sempre é totalmente descrito na lista de reprodução da segmento e o atributo DECORRIDO deve ser usado para indicar a quantidade de tempo a fusão ou segmento tem foi Active Directory, conforme exigido pelo [HLS].

Quando uma janela deslizante de apresentação está ativada, as etiquetas de EXT-X-indicação são removidas da lista de reprodução do segmento, quando o tempo de suporte de dados que dizem respeito ao foi revertida fora da janela deslizante de apresentação.

## <a name="33--dash-delivery"></a>3.3 entrega de DASH
[DASH] oferece três formas de eventos de sinal:

1.  Eventos sinalizados no MPD
2.  Eventos sinalizado em banda na representação (usando a caixa de mensagem de evento ("emsg")
3.  Uma combinação de 1 e 2

Eventos sinalizados no MPD são úteis para transmissão em fluxo VOD porque os clientes tenham acesso a todos os eventos, imediatamente quando o MPD é transferida. A solução em banda é útil para transmissão em direto porque os clientes não precisam de transferir o MPD novamente. Para segmentação baseados no tempo, o cliente determina o URL para o segmento seguinte ao adicionar a duração e o timestamp do segmento atual. Se que pedido falhar, o cliente pressupõe uma descontinuidade e transfere o MPD, mas caso contrário, continua a transmissão em fluxo sem baixar o MPD.

Serviços de multimédia do Azure fará ambos os sinalização no MPD e sinalização usando a caixa de mensagem de evento em banda.

#### <a name="mpd-signaling"></a>Sinalização de MPD

Eventos sinalizará no MPD usando o elemento de EventStream, que aparece dentro do elemento de período.

O elemento de EventStream tem os seguintes atributos:

| **Nome de atributo** | **Tipo**                | **Necessário?** | **Descrição**                                                                                                                                                                                                                                                                                   |
|--------------------|-------------------------|---------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| scheme_id_uri      | string                  | Necessário      | Identifica o esquema da mensagem. O esquema está definido para o valor do atributo de esquema na caixa de manifesto de servidor ao vivo. O valor deve ser um URN ou um URL, identificando o esquema de mensagem; Por exemplo, "urn: scte:scte35:2013a:bin".                                                                |
| valor              | string                  | Opcional      | Um valor de cadeia de caracteres adicionais utilizado pelos proprietários do esquema para personalizar a semântica da mensagem. Para diferenciar os vários fluxos de eventos com o mesmo esquema, o valor tem de ser definido para o nome do fluxo de eventos (trackName para uniforme de ingestão ou o nome de mensagem AMF para RTMP ingerir). |
| Escala temporal          | número inteiro sem sinal de 32 bits | Necessário      | Escala da temporal, em tiques por segundo, os tempos e campos de duração na caixa 'emsg'.                                                                                                                                                                                                       |


Zero ou mais elementos de evento estão contidos dentro do elemento de EventStream e têm os seguintes atributos:

| **Nome de atributo**  | **Tipo**                | **Necessário?** | **Descrição**                                                                                                                                                                                                             |
|---------------------|-------------------------|---------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| presentation_time   | número inteiro não assinado de 64 bits | Opcional      | TEM de ser o suporte de dados de tempo de apresentação do evento relativo ao início do período. O tempo de apresentação e a duração devem alinhar com pontos de acesso de Stream (SAP) do tipo 1 ou 2, conforme definido na [ISO-14496-12] Annex I. |
| duração            | número inteiro sem sinal de 32 bits | Opcional      | A duração do evento. Isso tem de ser omitido se a duração é desconhecida.                                                                                                                                                 |
| ID                  | número inteiro sem sinal de 32 bits | Opcional      | Identifica esta instância da mensagem. As mensagens com uma semântica equivalente deverá ter o mesmo valor. Se o ID não for especificado, quando a mensagem é ingerida, serviços de multimédia do Azure irá gerar um id exclusivo.             |
| Valor do elemento do evento | string                  | Necessário      | A mensagem de evento como uma cadeia base64, conforme descrito em [IETF RFC 4648](https://tools.ietf.org/html/rfc4648).                                                                                                                   |

#### <a name="xml-syntax-and-example-for-dash-manifest-mpd-signaling"></a>Sintaxe do XML e de exemplo para DASH manifesto Signaling (MPD)

~~~ xml
<!-- XML Syntax -->
<xs:complexType name=”EventStreamType”>
  <xs:sequence>
    <xs:element name=”Event” type=”EventType” minOccurs=”0” maxOccurs=”unbounded”/>
    <xs:any namespace=”##other” processContents=”lax” minOccurs=”0” maxOccurs=”unbounded”/>
  </xs:sequence>
  <xs:attribute ref=”xlink:href”/>
  <xs:attribute ref=”xlink:actuate” default=”onRequest”/>
  <xs:attribute name=”schemeIdUri” type=”xs:anyURI” use=”required”/>
  <xs:attribute name=”value” type=”xs:string”/>
  <xs:attribute name=”timescale” type=”xs:unsignedInt”/>
</xs:complexType>
<!-- Event -->
<xs:complexType name=”EventType”>
  <xs:sequence>
    <xs:any namespace=”##other” processContents=”lax” minOccurs=”0” maxOccurs=”unbounded”/>
  </xs:sequence>
  <xs:attribute name=”presentationTime” type=”xs:unsignedLong” default=”0”/>
  <xs:attribute name=”duration” type=”xs:unsignedLong”/>
  <xs:attribute name=”id” type=”xs:unsignedInt”/>
  <xs:anyAttribute namespace=”##other” processContents=”lax”/>
</xs:complexType><?xml version=”1.0” encoding=”utf-8”?>


<!-- Example Section in MPD -->
  <EventStream schemeIdUri="urn:scte:scte35:2013a:bin" value="scte35_track_001_000" timescale="10000000">
        <Event presentationTime="15447165200227600" duration="300000000" id="1026">/DAlAAAAAAAAAP/wFAUAAAQCf+//KRjAfP4AKTLgAAAAAAAAVYsh2w==</Event>
        <Event presentationTime="15447166250227600" duration="300000000" id="1027">/DAlAAAAAAAAAP/wFAUAAAQDf+//KaeGwP4AKTLgAAAAAAAAn75a3g==</Event>
        <Event presentationTime="15447167300227600" duration="600000000" id="1028">/DAlAAAAAAAAAP/wFAUAAAQEf+//KjkknP4AUmXAAAAAAAAAWcEldA==</Event>
        <Event presentationTime="15447168350227600" duration="600000000" id="1029">/DAlAAAAAAAAAP/wFAUAAAQFf+//KslyqP4AUmXAAAAAAAAAvKNt0w==</Event>
        <Event presentationTime="15447169400227600" duration="300000000" id="1030">/DAlAAAAAAAAAP/wFAUAAAQGf+//K1mIvP4AKTLgAAAAAAAAt2zEbw==</Event>
        <Event presentationTime="15447170450227600" duration="600000000" id="1031">/DAlAAAAAAAAAP/wFAUAAAQHf+//K+hc/v4AUmXAAAAAAAAANNRzVw==</Event>
    </EventStream>
~~~

>[!NOTE]
>Tenha em atenção que presentationTime é o tempo de apresentação do evento, não o tempo de chegada da mensagem.
>

### <a name="431-in-band-event-message-box-signaling"></a>4.3.1 em banda eventos sinalização de caixa de mensagem
Um fluxo de eventos em banda requer MPD ter um elemento de InbandEventStream ao nível do conjunto de adaptação.  Este elemento tem um atributo obrigatório schemeIdUri e o atributo de escala temporal opcional, que também são apresentados no caso a caixa de mensagem ('emsg').  Caixas de mensagens de eventos com os identificadores de esquema que não estão definidas no MPD não devem estar presentes. Se um cliente DASH Deteta uma caixa de mensagem de evento com um esquema que não está definido no MPD, o cliente é esperado para ignorá-lo.
A caixa de mensagem de evento ("emsg") fornece a sinalização de eventos genéricos relacionados com o tempo de apresentação do suporte de dados. Se estiver presente, qualquer caixa 'emsg' deverá ser colocada antes de qualquer caixa 'moof'.

### <a name="dash-event-message-box-emsg"></a>Caixa de mensagem de evento de travessão 'emsg'
~~~
Box Type: `emsg’
Container: Segment
Mandatory: No
Quantity: Zero or more
~~~

~~~ c
aligned(8) class DASHEventMessageBox extends FullBox(‘emsg’, version = 0, flags =0) 
{
    string scheme_id_uri;
    string value;
    unsigned int(32) timescale;
    unsigned int(32) presentation_time_delta;
    unsigned int(32) event_duration;
    unsigned int(32) id;
    unsigned int(8) message_data[];
}
~~~

Os campos do DASHEventMessageBox são definidos abaixo:

| **Nome do campo**          | **Tipo de campo**          | **Necessário?** | **Descrição**                                                                                                                                                                                                                                                                                                                                                    |
|-------------------------|-------------------------|---------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| scheme_id_uri           | string                  | Necessário      | Identifica o esquema da mensagem. O esquema está definido para o valor do atributo de esquema na caixa de manifesto de servidor ao vivo. O valor deve ser um URN ou um URL, identificando o esquema de mensagem. Para mensagens de [SCTE-35], isso leva o valor especial "urn: scte:scte35:2013a:bin", embora [SCTE 67] recomenda outra coisa. |
| Value                   | string                  | Necessário      | Um valor de cadeia de caracteres adicionais utilizado pelos proprietários do esquema para personalizar a semântica da mensagem. Para diferenciar os vários fluxos de eventos com o mesmo esquema, o valor será definido como o nome do fluxo de eventos (trackName para uniforme de ingestão ou o nome de mensagem AMF para RTMP ingerir).                                                                  |
| Escala temporal               | número inteiro sem sinal de 32 bits | Necessário      | Escala da temporal, em tiques por segundo, os tempos e campos de duração na caixa 'emsg'.                                                                                                                                                                                                                                                                        |
| Presentation_time_delta | número inteiro sem sinal de 32 bits | Necessário      | O suporte de dados apresentação intervalo de tempo do tempo de apresentação do evento e a primeira hora de apresentação esse segmento. O tempo de apresentação e a duração devem alinhar com pontos de acesso de Stream (SAP) do tipo 1 ou 2, conforme definido na [ISO-14496-12] Annex I.                                                                                            |
| event_duration          | número inteiro sem sinal de 32 bits | Necessário      | A duração do evento ou 0xFFFFFFFF para indicar uma duração de desconhecido.                                                                                                                                                                                                                                                                                          |
| Id                      | número inteiro sem sinal de 32 bits | Necessário      | Identifica esta instância da mensagem. As mensagens com uma semântica equivalente deverá ter o mesmo valor. Se o ID não for especificado, quando a mensagem é ingerida, serviços de multimédia do Azure irá gerar um id exclusivo.                                                                                                                                                    |
| Message_data            | matriz de bytes              | Necessário      | A mensagem de evento. Para mensagens de [SCTE-35], os dados da mensagem são o splice_info_section() binária, embora [SCTE 67] recomenda outra coisa.                                                                                                                                                                                                                                 |

### <a name="332-dash-message-handling"></a>3.3.2 travessão processamento de mensagens

Eventos são sinalizados em banda, na caixa "emsg", para as faixas de áudio e vídeos.  A sinalização ocorre para segmentar todos os pedidos para o qual o presentation_time_delta é 15 segundos ou menos. Quando uma janela deslizante de apresentação está ativada, as mensagens de eventos são removidas do MPD, quando a soma do tempo e a duração da mensagem de evento são inferior ao tempo dos dados de suporte de dados no manifesto.  Em outras palavras, as mensagens de eventos são removidas do manifesto quando o tempo de suporte de dados que dizem respeito foi revertida fora da janela deslizante de apresentação.

## <a name="4-scte-35-ingest"></a>Ingestão de 4 SCTE-35

Mensagens de [SCTE-35] são ingeridas no formato binário, utilizando o esquema **"urn: scte:scte35:2013a:bin"** para uniforme de ingestão e o tipo **"scte35"** para RTMP de ingestão. Para facilitar a conversão de tempo de [SCTE-35], que se baseia em MPEG-2 transporte stream apresentação tempo carimbos (PTS), um mapeamento entre PTS (pts_time + pts_adjustment do splice_time()) e a linha cronológica de suporte de dados é fornecido pelo (de tempo de apresentação do evento o campo de fragment_absolute_time para uniforme de ingestão e o campo de tempo para RTMP ingerir). O mapeamento é necessário porque o valor PTS 33 bits é agregado ao longo de aproximadamente a cada 26.5 horas.

Ingestão de transmissão em fluxo uniforme requer que a caixa de dados de suporte de dados ('mdat') tem de conter o **splice_info_section()** definida na tabela 8-1, de [SCTE-35]. Para RTMP de ingestão, o atributo de indicação da mensagem AMF está definido como o base64encoded **splice_info_section()**. Quando as mensagens têm o formato descrito acima, eles são enviados para HLS, uniforme, Dash clientes e em conformidade com [SCTE 67].


## <a name="5-references"></a>5 referências

**[SCTE-35]**  ANSI/SCTE 35 2013a – programa Digital inserção Cueing mensagem para o cabo, 2013a

**[SCTE 67]**  ANSI/SCTE 67 2014 – recomendado prática para SCTE 35: Mensagem de Cueing de inserção de programa digital de cabo

**[DASH]**  ISO/IEC 23009-1 2014 – tecnologia da informação – dinâmico através de HTTP (DASH) – parte 1 de transmissão em fluxo adaptável: Suporte de dados descrição e o segmento de formatos de apresentação, 2ª edição

**[HLS]**  ["HTTP transmissão em direto", draft-pantos-http-live-streaming-14, 14 de Outubro de 2014](http://tools.ietf.org/html/draft-pantos-http-live-streaming-14)

**[MS-SSTR]**  ["Microsoft Smooth Streaming protocolo", 15 de Maio de 2014](https://download.microsoft.com/download/9/5/E/95EF66AF-9026-4BB0-A41D-A4F81802D92C/%5bMS-SSTR%5d.pdf)

**[AMF0]**  ["AMF0 de formato de mensagem de ação"](https://download.macromedia.com/pub/labs/amf/amf0_spec_121207.pdf)

**[LIVE FMP4]**  [Especificação de ingestão de MP4 fragmentado em direto de serviços de multimédia do azure](https://docs.microsoft.com/azure/media-services/media-services-fmp4-live-ingest-overview)

**[ISO-14496-12]** ISO/IEC 14496-12: O ficheiro de multimédia de base de ISO de parte 12 formato, quarta edição 2012 a 07-15.

**[RTMP]**  ["Da adobe em tempo real protocolo de mensagens", 21 de Dezembro de 2012](https://www.adobe.com/devnet/rtmp.html) 

------------------------------------------

## <a name="next-steps"></a>Passos Seguintes
Serviços de multimédia de modo de exibição percursos de aprendizagem.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
