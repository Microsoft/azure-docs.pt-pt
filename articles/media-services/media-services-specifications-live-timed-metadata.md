---
title: Azure Media Services - Signaling Metadados Cronometrados em Streaming Ao Vivo
description: Esta especificação descreve métodos para sinalizar metadados cronometrados ao ingerir e transmitir para o Azure Media Services. Isto inclui suporte para sinais genéricos de metadados cronometrados (ID3), bem como sinalização SCTE-35 para inserção de anúncios e sinalização de condição de splice.
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
ms.date: 08/22/2019
ms.author: johndeu
ms.openlocfilehash: 551fb0cb9f3745a62d5d84f2c4878bbbbe5ad9a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137327"
---
# <a name="signaling-timed-metadata-in-live-streaming"></a>Sinalizar metadados cronometrados em streaming ao vivo 

Última Atualização: 2019-08-22

### <a name="conformance-notation"></a>Notação de Conformidade

As palavras-chave "MUST", "MUST NOT", "REQUIRED", "DEVE", "NÃO DEVE", "NÃO DEVE", "RECOMENDADO", "MAIO" e "OPCIONAL" neste documento devem ser interpretadas como descrito no RFC 2119

## <a name="1-introduction"></a>1. Introdução 

De forma a sinalizar a inserção de anúncios ou eventos de metadados personalizados num leitor de clientes, os emissores muitas vezes utilizam metadados cronometrados incorporados no vídeo. Para permitir estes cenários, a Media Services presta suporte ao transporte de metadados cronometrados desde o ponto ingerir do canal de streaming ao vivo até à aplicação do cliente.
Esta especificação descreve vários modos que são suportados pelos Media Services para metadados cronometrados dentro de sinais de streaming ao vivo.

1. [SCTE-35] sinalização que cumpre as normas delineadas por [SCTE-35], [SCTE-214-1], [SCTE-214-3] e [RFC8216]

2. [SCTE-35] sinalização que cumpre com a especificação do legado [Adobe-Primetime] para a sinalização de anúncios RTMP.
   
3. Um modo genérico de sinalização de metadados cronometrados, para mensagens que **não** sejam [SCTE-35] e que possam transportar [ID3v2] ou outros esquemas personalizados definidos pelo desenvolvedor da aplicação.

## <a name="11-terms-used"></a>1.1 Termos Utilizados

| Termo                | Definição                                                                                                                                                                                                                                    |
| ------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Pausa de anúncio            | Um local ou um ponto no tempo em que um ou mais anúncios podem ser programados para entrega; mesmo que oportunidade de aditadinha e de colocação.                                                                                                                     |
| Serviço de Decisão de Anúncios | serviço externo que decida quais anúncios e durações serão mostrados ao utilizador. Os serviços são normalmente prestados por um parceiro e estão fora de âmbito para este documento.                                                                    |
| Deixa                 | Indicação do tempo e dos parâmetros da próxima pausa do anúncio. Note que as pistas podem indicar um interruptor pendente para uma rutura de anúncio, pendente de mudança para o anúncio seguinte dentro de uma pausa de anúncio, e alternar pendente de uma rutura de anúncio para o conteúdo principal.           |
| Embalador            | O Azure Media Services "Streaming Endpoint" fornece capacidades dinâmicas de embalagem para O DASH e o HLS e é referido como um "Embalador" na indústria dos media.                                                                              |
| Tempo de apresentação   | O tempo que um evento é apresentado a um espectador. O momento representa o momento na linha temporal mediática que um espectador veria o evento. Por exemplo, o tempo de apresentação de uma mensagem de comando sCTE-35 splice_info() é o splice_time(). |
| Hora de Chegada        | O tempo que uma mensagem de evento chega. O tempo é tipicamente distinto do tempo de apresentação do evento, uma vez que as mensagens de evento são enviadas antes da hora de apresentação do evento.                                                    |
| Pista escassa        | faixa dos meios de comunicação que não é contínua, e é sincronizada com o tempo sincronizado com um pai ou pista de controlo.                                                                                                                                                  |
| Origem              | O Serviço de Streaming Azure Media                                                                                                                                                                                                             |
| Sink do Canal        | O Serviço de Streaming Ao Vivo Azure Media                                                                                                                                                                                                        |
| HLS                 | Protocolo apple HTTP Live Streaming                                                                                                                                                                                                            |
| TRAÇO                | Streaming adaptativo dinâmico sobre HTTP                                                                                                                                                                                                          |
| Suave              | Protocolo de Streaming Suave                                                                                                                                                                                                                     |
| MPEG2-TS            | Fluxos de transporte MPEG 2                                                                                                                                                                                                                      |
| RTMP                | Protocolo Multimédia em tempo real                                                                                                                                                                                                                 |
| uimsbf              | Inteiro não assinado, a parte mais significativa primeiro.                                                                                                                                                                                                 |

---

## <a name="12-normative-references"></a>1.2 Referências Normativas

Os seguintes documentos contêm disposições que, através de referência no presente texto, constituem disposições do presente documento. Todos os documentos estão sujeitos a revisão pelos órgãos de normalização, e os leitores são encorajados a investigar a possibilidade de aplicar as edições mais recentes dos documentos listados abaixo. Os leitores são também lembrados que as edições mais recentes dos documentos referenciados podem não ser compatíveis com esta versão da especificação de metadados cronometrados para os Serviços de Mídia Azure.


| Standard          | Definição                                                                                                                                                                                                     |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Adobe-Primetime] | [Especificação de sinalização de inserção de programa digital Primetime 1.2](https://www.adobe.com/content/dam/acom/en/devnet/primetime/PrimetimeDigitalProgramInsertionSignalingSpecification.pdf)                       |
| [Adobe-Flash-AS]  | [Referência de linguagem flash actionscript](https://help.adobe.com/archive/en_US/as2/flashlite_2.x_3.x_aslr.pdf)                                                                                                   |
| [AMF0]            | ["Formato de Mensagem de Ação AMF0"](https://download.macromedia.com/pub/labs/amf/amf0_spec_121207.pdf)                                                                                                              |
| [DASH-IF-IOP]     | Dash Industry Forum Interop Guidance v 4.2[https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html](https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html)    |
| [HLS-TMD]         | Metadados cronometrados para HTTP Live Streaming -[https://developer.apple.com/streaming](https://developer.apple.com/streaming)                                                                                        |
| [CMAF-ID3]        | [Metadados cronometrados no formato de aplicação de mídia comum (CMAF)](https://github.com/AOMediaCodec/id3-emsg)                                                                                                        |
| [ID3v2]           | ID3 Tag versão 2.4.0[http://id3.org/id3v2.4.0-structure](http://id3.org/id3v2.4.0-structure)                                                                                                                |
| [ISO-14496-12]    | ISO/IEC 14496-12: Formato de ficheiro base ISO parte 12, Quarto Edition 2012-07-15                                                                                                                                 |
| [MPEGDASH]        | Tecnologia da informação -- Streaming adaptativo dinâmico sobre HTTP (DASH) -- Parte 1: Descrição da apresentação dos meios de comunicação e formatos de segmento. Maio de 2014. Publicado. URL:https://www.iso.org/standard/65274.html         |
| [MPEGCMAF]        | Tecnologias da informação -- Formato de aplicação multimédia (MPEG-A) -- Parte 19: Formato comum de aplicação de mídia (CMAF) para meios segmentados. Janeiro de 2018. Publicado. URL:https://www.iso.org/standard/71975.html |
| [MPEGCENC]        | Tecnologias de informação -- tecnologias de sistemas MPEG -- Parte 7: Encriptação comum em ficheiros de formato de ficheiros base iso. Fevereiro de 2016. Publicado. URL:https://www.iso.org/standard/68042.html                   |
| [MS-SSTR]         | ["Microsoft Smooth Streaming Protocol", 15 de maio de 2014](https://docs.microsoft.com/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251)                                                     |
| [MS-SSTR-Ingest]  | [Especificação de ingest de mídia azure fragmentada MP4](https://docs.microsoft.com/azure/media-services/media-services-fmp4-live-ingest-overview)                                                      |
| [RFC8216]         | R. Pantos, Ed. W. maio. HTTP Live Streaming. Agosto de 2017. Informação. [https://tools.ietf.org/html/rfc8216](https://tools.ietf.org/html/rfc8216)                                                            |
| [RFC4648]         | As Bases16, Base32 e Base64 Data Encodings -[https://tools.ietf.org/html/rfc4648](https://tools.ietf.org/html/rfc4648)                                                                                     |
| [RTMP]            | ["Protocolo de Mensagens em Tempo Real da Adobe", 21 de dezembro de 2012](https://www.adobe.com/devnet/rtmp.html)                                                                                                            |
| [SCTE-35-2019]    | SCTE 35: 2019 - Mensagem de Inserção de Programa Digital para Cabo -https://www.scte.org/SCTEDocs/Standards/ANSI_SCTE%2035%202019r1.pdf                                                                       |
| [SCTE-214-1]      | SCTE 214-1 2016 – MPEG DASH para serviços de cabo baseados em IP Parte 1: Restrições e Extensões de MPD                                                                                                                 |
| [SCTE-214-3]      | SCTE 214-3 2015 MPEG DASH para serviços de cabo baseados em IP Parte 3: Perfil DASH/FF                                                                                                                                  |
| [SCTE-224]        | SCTE 224 2018r1 – Agendamento de eventos e interface de notificação                                                                                                                                                  |
| [SCTE-250]        | API de Gestão de Eventos e Sinalização (ESAM)                                                                                                                                                                      |

---


## <a name="2-timed-metadata-ingest"></a>2. Incesto de metadados cronometrados

A Azure Media Services suporta metadados em tempo real em banda para os protocolos [RTMP] e Smooth Streaming [MS-SSTR-Ingest]. Os metadados em tempo real podem ser usados para definir eventos personalizados, com os seus próprios schemas personalizados exclusivos (JSON, Binary, XML), bem como formatos definidos pela indústria como ID3, ou SCTE-35 para sinalização de anúncios num fluxo de transmissão. 

Este artigo fornece os detalhes de como enviar sinais de metadados cronometrados personalizados usando os protocolos de intenção suportados dos Serviços De Mídia Azure. O artigo também explica como os manifestos para HLS, DASH e Smooth Streaming são decorados com os sinais de metadados cronometrados, bem como como é transportado em banda quando o conteúdo é entregue usando segmentos CMAF (fragmentos MP4) ou Transport Stream (TS) para HLS. 

Os cenários de caso de utilização comum para metadados cronometrados incluem:

 - Sinais de anúncioS SCTE-35 para desencadear quebras de anúncios em evento ao vivo ou transmissão linear
 - Metadados personalizados ID3 que podem desencadear eventos numa aplicação de cliente (navegador, iOS ou Android)
 - Metadados JSON, Binary ou XML definidos personalizados para desencadear eventos numa aplicação de cliente
 - Telemetria de um codificador ao vivo, câmera IP ou drone
 - Eventos de uma câmara IP como Movimento, deteção facial, etc.
 - Informações de posição geográfica de uma câmara de ação, drone ou dispositivo em movimento
 - Letra da canção
 - Limites do programa em um feed linear ao vivo
 - Imagens ou metadados aumentados a serem exibidos em feed ao vivo
 - Pontuações desportivas ou informações sobre o relógio de jogo
 - Pacotes de publicidade interativa saem ao lado do vídeo no navegador
 - Quizzes ou sondagens
  
Os Eventos e Pacoteres de Serviços de Mídia Azure são capazes de receber estes sinais de metadados cronometrados e convertê-los num fluxo de metadados que pode chegar a aplicações de clientes usando protocolos baseados em padrões como HLS e DASH.


## <a name="21-rtmp-timed-metadata"></a>2.1 Metadados Cronometrados RTMP

O protocolo [RTMP] permite que sejam enviados sinais de metadados cronometrados para vários cenários, incluindo metadados personalizados, e sinais de anúncios SCTE-35. 

Os sinais de publicidade (mensagens de sugestão) são enviados como mensagens de deixa [AMF0] incorporadas no fluxo [RTMP]. As mensagens de deixa podem ser enviadas algum tempo antes do evento real ou o sinal de splice [SCTE35] precisa de ocorrer. Para apoiar este cenário, o carimbo de tempo de apresentação real do evento é enviado dentro da mensagem de sugestão. Para mais informações, consulte [AMF0].

Os seguintes comandos [AMF0] são apoiados pela Azure Media Services para a ingestão RTMP:

- **onUserDataEvent** - usado para metadados personalizados ou metadados cronometrados [ID3v2]
- **onAdCue** - usado principalmente para sinalizar uma oportunidade de colocação de anúncios no live stream. São suportadas duas formas da deixa, um modo simples e um modo "SCTE-35". 
- **onCuePoint** - suportado por certos codificadores de hardware no local, como o codificador Elemental Live, para sinalizar mensagens [SCTE35]. 
  

A tabela seguinte descreve o formato da carga de mensagens AMF que os Serviços de Media irão ingerir tanto para os modos de mensagem "simples" como para [SCTE35].

O nome da mensagem [AMF0] pode ser usado para diferenciar vários fluxos de eventos do mesmo tipo.  Tanto para as mensagens [SCTE-35] como para o modo "simples", o nome da mensagem AMF DEVE ser "onAdCue", conforme exigido na especificação [Adobe-Primetime].  Os campos que não listados abaixo serão ignorados pela Azure Media Services na ingestão.

## <a name="211-rtmp-with-custom-metadata-using-onuserdataevent"></a>2.1.1 RTMP com metadados personalizados utilizando "onUserDataEvent"

Se pretender fornecer feeds de metadados personalizados do seu codificador a montante, câmara IP, drone ou dispositivo utilizando o protocolo RTMP, utilize o tipo de comando de mensagem de dados "onUserDataEvent" [AMF0].

O comando de mensagem de dados **"onUserDataEvent"** DEVE transportar uma carga útil de mensagem com a seguinte definição a ser capturada pelos Media Services e embalada no formato de ficheiro sin-line, bem como os manifestos para HLS, DASH e Smooth Streaming.
Recomenda-se que envie mensagens de metadados cronometrados não mais frequentemente do que uma vez a cada 0,5 segundos (500 ms) ou podem ocorrer problemas de estabilidade com o fluxo ao vivo. Cada mensagem pode agregar metadados de vários quadros se precisar fornecer metadados de nível de quadro. Se estiver a enviar fluxos multibitantes, recomenda-se que também forneça os metadados numa única bitrate apenas para reduzir a largura de banda e evitar interferências no processamento de vídeo/áudio. 

A carga útil para o **"onUserDataEvent"** deve ser uma mensagem de formato EventStream XML [MPEGDASH]. Isto facilita a passagem em esquemas definidos personalizados que podem ser transportados em cargas 'emsg' em banda para o conteúdo cmaf [MPEGCMAF] que é entregue através de protocolos HLS ou DASH. Cada mensagem dash Event Stream contém um esquemaIdUri que funciona como um identificador de sistema de mensagens URN e define a carga útil da mensagem. Alguns esquemashttps://aomedia.org/emsg/ID3como " para [ID3v2], ou **urn:scte:scte35:2013:bin** para [SCTE-35] são padronizados por consórcios da indústria para interoperabilidade. Qualquer fornecedor de aplicação pode definir o seu próprio esquema personalizado usando um URL que controla (domínio próprio) e pode fornecer uma especificação nesse URL, se assim o desejar. Se um jogador tem um manipulador para o esquema definido, então este é o único componente que precisa entender a carga útil e o protocolo.

O esquema para a carga útil Do Evento Stream XML é definido como (excerto do DASH ISO-IEC-23009-1-3ª Edição). Note que apenas um "EventType" por "EventStream" é suportado neste momento. Apenas o primeiro elemento **evento** será processado se vários eventos forem fornecidos no **EventStream**.

```xml
  <!-- Event Stream -->
  <xs:complexType name="EventStreamType">
    <xs:sequence>
      <xs:element name="Event" type="EventType" minOccurs="0" maxOccurs="unbounded"/>
      <xs:any namespace="##other" processContents="lax" minOccurs="0" maxOccurs="unbounded"/>
    </xs:sequence>
    <xs:attribute ref="xlink:href"/>
    <xs:attribute ref="xlink:actuate" default="onRequest"/>
    <xs:attribute name="schemeIdUri" type="xs:anyURI" use="required"/>
    <xs:attribute name="value" type="xs:string"/>
    <xs:attribute name="timescale" type="xs:unsignedInt"/>
  </xs:complexType>
  <!-- Event  -->
  <xs:complexType name="EventType">
    <xs:sequence>
      <xs:any namespace="##other" processContents="lax" minOccurs="0" maxOccurs="unbounded"/>
    </xs:sequence>
    <xs:attribute name="presentationTime" type="xs:unsignedLong" default="0"/>
    <xs:attribute name="duration" type="xs:unsignedLong"/>
    <xs:attribute name="id" type="xs:unsignedInt"/>
    <xs:attribute name="contentEncoding" type="ContentEncodingType"/>
    <xs:attribute name="messageData" type="xs:string"/>
    <xs:anyAttribute namespace="##other" processContents="lax"/>
  </xs:complexType>
```


### <a name="example-xml-event-stream-with-id3-schema-id-and-base64-encoded-data-payload"></a>Example XML Event Stream with ID3 schema ID and base64-encoded data payload.  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="https://aomedia.org/emsg/ID3">
         <Event contentEncoding="Base64">
          -- base64 encoded ID3v2 full payload here per [CMAF-TMD] --
         </Event>
   <EventStream>
```

### <a name="example-event-stream-with-custom-schema-id-and-base64-encoded-binary-data"></a>Exemplo Fluxo de Eventos com ID de esquema personalizado e dados binários codificados base64  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="urn:example.org:custom:binary">
         <Event contentEncoding="Base64">
          -- base64 encoded custom binary data message --
         </Event>
   <EventStream>
```

### <a name="example-event-stream-with-custom-schema-id-and-custom-json"></a>Fluxo de eventos de exemplo com ID de esquema personalizado e JSON personalizado  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="urn:example.org:custom:JSON">
         <Event>
          [
            {"key1" : "value1"},
            {"key2" : "value2"}
          ]
         </Event>
   <EventStream>
```

### <a name="built-in-supported-scheme-id-uris"></a>URIs de ID do esquema apoiado sem suporte
| Id do esquema URI                 | Descrição                                                                                                                                                                                                                                          |
| ----------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| https:\//aomedia.org/emsg/ID3 | Descreve como os metadados [ID3v2] podem ser transportados como metadados cronometrados num MP4 fragmentado compatível com CMAF [MPEGCMAF]. Para mais informações consulte os [Metadados Cronometrados no Formato de Aplicação de Meios Comuns (CMAF)](https://github.com/AOMediaCodec/id3-emsg) |

### <a name="event-processing-and-manifest-signaling"></a>Processamento de eventos e sinalização manifesta

Ao receber um evento válido **"onUserDataEvent",** a Azure Media Services procurará uma carga útil XML válida que corresponda ao EventStreamType (definido em [MPEGDASH]), analisar a carga útil xML e convertê-la numa caixa de fragmentação MP4 'emsg' [MPEGCMAF] para armazenamento no arquivo ao vivo e transmissão para o Media Services Packager.   O Embalador detetará a caixa 'emsg' no live stream e:

- a "pacote dinâmico" nos segmentos TS para entrega aos clientes hls em conformidade com a especificação de metadados cronometrados HLS [HLS-TMD], ou
- b Passá-lo para entrega em fragmentos cmaf via HLS ou DASH, ou 
- c Convertê-lo num sinal de faixa escassa para entrega através do Smooth Streaming [MS-SSTR].

Além dos pacotes CMAF ou TS PES de formação em banda cmaf ou TS PES para HLS, os manifestos para DASH (MPD) e Smooth Streaming conterão uma referência aos streams de eventos em banda (também conhecido como faixa de fluxo escasso em Smooth Streaming). 

Os eventos individuais ou as suas cargas de dados NÃO são saídas diretamente nos manifestos HLS, DASH ou Smooth. 

### <a name="additional-informational-constraints-and-defaults-for-onuserdataevent-events"></a>Restrições e incumprimentos informais adicionais para eventos no UserDataEvent

- Se o calendário não estiver definido no elemento EventStream, o calendário RTMP 1 kHz é utilizado por padrão
- A entrega de uma mensagem onUserDataEvent limita-se a uma vez a cada 500ms no máximo. Se enviar eventos com mais frequência, pode afetar a largura de banda e a estabilidade do feed ao vivo

## <a name="212-rtmp-ad-cue-signaling-with-onadcue"></a>2.1.2 Sinal de sinal de sinal de ad

A Azure Media Services pode ouvir e responder a vários tipos de mensagens [AMF0] que podem ser usados para sinalizar vários metadados sincronizados em tempo real no live stream.  A especificação [Adobe-Primetime] define dois tipos de sugestões chamados modo "simples" e "SCTE-35". Para o modo "simples", a Media Services suporta uma única mensagem de deixa AMF chamada "onAdCue" utilizando uma carga útil que corresponde à tabela abaixo definida para o sinal "Modo Simples".  

A secção seguinte mostra a carga útil "simple" do modo RTMP, que pode ser usada para sinalizar um sinal de anúncio básico "spliceOut" que será transportado para o manifesto do cliente para HLS, DASH e Microsoft Smooth Streaming. Isto é muito útil para cenários em que o cliente não tenha um complexo sistema de sinalização ou inserção de sinalização de anúncios baseado em SCTE-35, e está a usar um codificador básico no local para enviar a mensagem de sugestão através de uma API. Normalmente, o codificador no local irá suportar uma API baseada em REST para desencadear este sinal, que também irá "splice-condition" o fluxo de vídeo inserindo uma moldura IDR no vídeo, e iniciando um novo GOP.

## <a name="213--rtmp-ad-cue-signaling-with-onadcue---simple-mode"></a>2.1.3 Sinal de sinal de sinal de ad com "onAdCue" - Modo Simples

| Nome do Campo | Tipo de Campo | Necessário? | Descrições                                                                                                                                                                                                                                                                        |
| ---------- | ---------- | --------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| tipo       | Cadeia     | Necessário  | A mensagem do evento.  Deve ser "SpliceOut" para designar uma combinação de modo simples.                                                                                                                                                                                                         |
| ID         | Cadeia     | Necessário  | Um identificador único descrevendo a combinação ou segmento. Identifica este exemplo da mensagem                                                                                                                                                                                       |
| duration   | Número     | Necessário  | A duração da liga. As unidades são segundos fracionados.                                                                                                                                                                                                                           |
| elapsed    | Número     | Opcional  | Quando o sinal estiver a ser repetido para suportar a sintonia, este campo deve ser a quantidade de tempo de apresentação que decorreu desde o início da splice. As unidades são segundos fracionados. Ao utilizar um modo simples, este valor não deve exceder a duração original da ligação. |
| hora       | Número     | Necessário  | Deve ser o tempo da ligação, no tempo de apresentação. As unidades são segundos fracionados.                                                                                                                                                                                                |

---
 
#### <a name="example-mpeg-dash-manifest-output-when-using-adobe-rtmp-simple-mode"></a>Exemplo MPEG DASH manifesta saída ao utilizar modo simples Adobe RTMP

Consulte o exemplo [3.3.2.1 MPEG DASH .mpd EventStream utilizando o modo adobe simples](#3321-example-mpeg-dash-mpd-manifest-signaling-of-rtmp-streaming-using-adobe-simple-mode)

Consulte o exemplo [3.3.3.1 Manifesto DASH com um período único e modo adobe simples](#3331-example-mpeg-dash-manifest-mpd-with-single-period-eventstream-using-adobe-simple-mode-signals)

#### <a name="example-hls-manifest-output-when-using-adobe-rtmp-simple-mode"></a>Exemplo HLS manifesta saída ao utilizar modo simples Adobe RTMP

Consulte o manifesto [3.2.2 HLS usando o modo simples Adobe e a tag EXT-X-CUE](#322-apple-hls-with-adobe-primetime-ext-x-cue-legacy)

## <a name="214-rtmp-ad-cue-signaling-with-onadcue---scte-35-mode"></a>2.1.4 Sinal de sinal de sinal de ad com "onAdCue" - Modo SCTE-35

Quando estiver a trabalhar com um fluxo de trabalho de produção de difusão mais avançado que exija que a mensagem de carga útil SCTE-35 completa seja transportada para o manifesto HLS ou DASH, o melhor é utilizar o "Modo SCTE-35" da especificação [Adobe-Primetime].  Este modo suporta que os sinais SCTE-35 da banda sejam enviados diretamente para um codificador ao vivo no local, que codifica os sinais para o fluxo RTMP utilizando o "Modo SCTE-35" especificado na especificação [Adobe-Primetime]. 

Normalmente, as mensagens SCTE-35 só podem aparecer nas inputs de fluxo de transporte MPEG-2 (TS) num codificador no local. Consulte o fabricante do codificador para obter mais informações sobre como configurar uma ingerir o fluxo de transporte que contenha SCTE-35 e ative-a para passar para RTMP no modo Adobe SCTE-35.

Neste cenário, a seguinte carga útil DEVE ser enviada do codificador no local utilizando o tipo de mensagem **"onAdCue"** [AMF0].

| Nome do Campo | Tipo de Campo | Necessário? | Descrições                                                                                                                                                                                                                                                                                                                                                                                                        |
| ---------- | ---------- | --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| deixa        | Cadeia     | Necessário  | A mensagem do evento.  Para as mensagens [SCTE-35], esta deve ser a base64 codificada [RFC4648] splice_info_section() para que as mensagens sejam enviadas aos clientes HLS, Smooth e Dash.                                                                                                                                                                                                                               |
| tipo       | Cadeia     | Necessário  | Uma URN ou URL identificando o esquema de mensagem. Para as mensagens [SCTE-35], este **deve** ser **"scte35"** para que as mensagens sejam enviadas para os clientes HLS, Smooth e Dash, em conformidade com [Adobe-Primetime]. Opcionalmente, a URN "urn:scte:scte35:2013:bin" também pode ser usada para sinalizar uma mensagem [SCTE-35].                                                                                                        |
| ID         | Cadeia     | Necessário  | Um identificador único descrevendo a combinação ou segmento. Identifica esta instância da mensagem.  As mensagens com semântica equivalente devem ter o mesmo valor.                                                                                                                                                                                                                                                       |
| duration   | Número     | Necessário  | A duração do evento ou do segmento de splice de anúncios, se conhecido. Se desconhecido, o valor **deve** ser 0.                                                                                                                                                                                                                                                                                                                    |
| elapsed    | Número     | Opcional  | Quando o sinal de anúncio [SCTE-35] estiver a ser repetido para sintonizar, este campo deve ser a quantidade de tempo de apresentação que decorreu desde o início da splice. As unidades são segundos fracionados. No modo [SCTE-35], este valor pode exceder a duração especificada original da splice ou segmento.                                                                                                                   |
| hora       | Número     | Necessário  | O tempo de apresentação do evento ou splice anúncio.  O tempo e a duração da apresentação **devem** alinhar-se com os pontos de acesso ao fluxo (SAP) do tipo 1 ou 2, tal como definidos no anexo I [ISO-14496-12]. Para a saída de HLS, o tempo e a duração **devem** alinhar-se com os limites do segmento. O tempo de apresentação e a duração de diferentes mensagens de evento dentro do mesmo fluxo de eventos NÃO DEVE sobrepor-se. As unidades são segundos fracionados. |

---

#### <a name="example-mpeg-dash-mpd-manifest-with-scte-35-mode"></a>Exemplo MPEG DASH .mpd manifesto com modo SCTE-35
Ver [Secção 3.3.3.2 exemplo manifesto DASH com SCTE-35](#3332-example-mpeg-dash-manifest-mpd-with-multi-period-eventstream-using-adobe-scte35-mode-signaling)

#### <a name="example-hls-manifest-m3u8-with-scte-35-mode-signal"></a>Exemplo HLS manifesto .m3u8 com sinal de modo SCTE-35
Ver [Secção 3.2.1.1 exemplo HLS manifesto com SCTE-35](#3211-example-hls-manifest-m3u8-showing-ext-x-daterange-signaling-of-scte-35)

## <a name="215-rtmp-ad-signaling-with-oncuepoint-for-elemental-live"></a>2.1.5 RtMP Ad sinalização com "onCuePoint" para Elemental Live

O codificador Elementar Live on-local suporta marcadores de anúncios no sinal RTMP. Atualmente, a Azure Media Services apenas suporta o tipo de marcador de anúncio "onCuePoint" para RTMP.  Isto pode ser ativado nas Definições do Grupo Adobe RTMP nas definições do codificador Elemental Media Live ou API, definindo o "**ad_markers**" para "onCuePoint".  Consulte a documentação do Elemental Live para mais detalhes. Ativar esta funcionalidade no Grupo RTMP passará sinais SCTE-35 para as saídas adobe RTMP a serem processadas pela Azure Media Services.

O tipo de mensagem "onCuePoint" é definido em [Adobe-Flash-AS] e tem a seguinte estrutura de carga útil quando enviada a partir da saída De RTMP Ao Vivo Elementar.


| Propriedade   | Descrição                                                                                                                                                                                                                     |
| ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| nome       | O nome deve ser '**scte35**' de Elemental Live.                                                                                                                                                                              |
| hora       | O tempo em segundos em que o ponto de sugestão ocorreu no ficheiro de vídeo durante a linha do tempo                                                                                                                                           |
| tipo       | O tipo de ponto de deixa deve ser definido para "**evento**".                                                                                                                                                                             |
| parâmetros | Uma gama associativa de cordas de par de nome/valor contendo as informações da mensagem SCTE-35, incluindo id e duração. Estes valores são analisados pela Azure Media Services e incluídos na etiqueta de decoração manifesto. |


Quando este modo de marcação de anúncios é utilizado, a saída de manifesto HLS é semelhante ao modo Adobe "Simple".


#### <a name="example-mpeg-dash-mpd-single-period-adobe-simple-mode-signals"></a>Exemplo MPEG DASH MPD, período único, sinais de modo Adobe Simple

~~~ xml
<?xml version="1.0" encoding="utf-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="dynamic" publishTime="2020-01-07T18:58:03Z" minimumUpdatePeriod="PT0S" timeShiftBufferDepth="PT58M56S" availabilityStartTime="2020-01-07T17:44:47Z" minBufferTime="PT7S">
    <Period start="PT0S">
        <EventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="scte35" timescale="10000000">
            <Event presentationTime="1583497601000000" duration="300000000" id="1085900"/>
            <Event presentationTime="1583500901666666" duration="300000000" id="1415966"/>
            <Event presentationTime="1583504202333333" duration="300000000" id="1746033"/>
            <Event presentationTime="1583507502666666" duration="300000000" id="2076066"/>
            <Event presentationTime="1583510803333333" duration="300000000" id="2406133"/>
            <Event presentationTime="1583514104000000" duration="300000000" id="2736200"/>
            <Event presentationTime="1583517404666666" duration="300000000" id="3066266"/>
            <Event presentationTime="1583520705333333" duration="300000000" id="3396333"/>
            <Event presentationTime="1583524006000000" duration="300000000" id="3726400"/>
            <Event presentationTime="1583527306666666" duration="300000000" id="4056466"/>
            <Event presentationTime="1583530607333333" duration="300000000" id="4386533"/>
        </EventStream>
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.4D400C" maxWidth="256" maxHeight="144" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="scte35"/>
            <SegmentTemplate timescale="10000000" presentationTimeOffset="1583486678426666" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="1583495318000000" d="64000000" r="34"/>
                    <S d="43000000"/>
                    <S d="21000000"/>
                    <!-- ... Truncated for brevity of sample-->

                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="1583495318000000" type="0" wallClockTime="2020-01-07T17:59:10.957Z" presentationTime="1583495318000000"/>
            <Representation id="1_V_video_3750956353252827751" bandwidth="149952" width="256" height="144"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.2" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="scte35"/>
            <Label>ambient</Label>
            <SegmentTemplate timescale="10000000" presentationTimeOffset="1583486678426666" media="QualityLevels($Bandwidth$)/Fragments(ambient=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(ambient=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="1583495254426666" d="64000000" r="35"/>
                    <S d="43093334"/>
                    <S d="20906666"/>
                    <!-- ... Truncated for brevity of sample-->

                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="1583495254426666" type="0" wallClockTime="2020-01-07T17:59:04.600Z" presentationTime="1583495254426666"/>
            <Representation id="5_A_ambient_9125670592623055209" bandwidth="96000" audioSamplingRate="48000"/>
        </AdaptationSet>
    </Period>
</MPD>
~~~

#### <a name="example-hls-playlist-adobe-simple-mode-signals-using-ext-x-cue-tag-truncated--for-brevity"></a>Exemplo de lista de reprodução HLS, sinais de modo Adobe Simples utilizando tag EXT-X-CUE (truncado "..." para a brevidade)

O exemplo seguinte mostra a saída do pacote dinâmico dos Media Services para um fluxo de ingestão RTMP utilizando sinais de modo "simples" da Adobe e o legado [Adobe-Primetime] EXT-X-CUE tag.  

~~~
#EXTM3U
#EXT-X-VERSION:8
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:7
#EXT-X-INDEPENDENT-SEGMENTS
#EXT-X-PROGRAM-DATE-TIME:2020-01-07T17:44:47Z
#EXTINF:6.400000,no-desc
Fragments(video=1583486742000000,format=m3u8-aapl-v8)
#EXTINF:6.400000,no-desc
Fragments(video=1583486806000000,format=m3u8-aapl-v8)
...
#EXTINF:6.166667,no-desc
Fragments(video=1583487638000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667
#EXTINF:0.233333,no-desc
Fragments(video=1583487699666666,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=0.233333
#EXTINF:6.400000,no-desc
Fragments(video=1583487702000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=6.633333
#EXTINF:6.400000,no-desc
Fragments(video=1583487766000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=13.033333
#EXTINF:6.400000,no-desc
Fragments(video=1583487830000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=19.433333
#EXTINF:6.400000,no-desc
Fragments(video=1583487894000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=25.833333
#EXTINF:4.166667,no-desc
Fragments(video=1583487958000000,format=m3u8-aapl-v8)
#EXTINF:2.233333,no-desc
Fragments(video=1583487999666666,format=m3u8-aapl-v8)
#EXTINF:6.400000,no-desc
Fragments(video=1583488022000000,format=m3u8-aapl-v8)
...

~~~

### <a name="216-cancellation-and-updates"></a>2.1.6 Cancelamento e Atualizações

As mensagens podem ser canceladas ou atualizadas enviando várias mensagens com o mesmo tempo de apresentação e ID. O tempo de apresentação e id identificam exclusivamente o evento, e a última mensagem recebida para um tempo específico de apresentação que satisfaz os constrangimentos pré-roll é a mensagem que é acionada. O evento atualizado substitui quaisquer mensagens recebidas anteriormente. A restrição pré-roll é de quatro segundos. As mensagens recebidas pelo menos quatro segundos antes do tempo de apresentação serão acionadas.

## <a name="22-fragmented-mp4-ingest-smooth-streaming"></a>2.2 Ingest MP4 fragmentado (Streaming Suave)

Consulte [MS-SSTR-Ingest] para obter requisitos na ingestão de fluxo sinuoso ao vivo. As seguintes secções fornecem detalhes sobre a ingestão de metadados de apresentação cronometrada.  Os metadados de apresentação cronometrados são ingeridos como uma faixa escassa, que é definida tanto na Caixa de Manifesto do Servidor Vivo (ver MS-SSTR) como na Caixa de Cinema ('moov').  

Cada fragmento escasso é composto por uma Caixa de Fragmentos de Filme ('moof') e Media Data Box ('mdat'), onde a caixa 'mdat' é a mensagem binária.

Para conseguir uma inserção de anúncios com precisão de quadros, o codificador DEVE dividir o fragmento no momento de apresentação em que a deixa é necessária para ser inserida.  É criado um novo fragmento que comece com uma moldura IDR recém-criada, ou Pontos de Acesso fluviais (SAP) do tipo 1 ou 2, tal como definido no anexo I [ISO-14496-12]. Isto permite ao Pacote Azure Media gerar corretamente um manifesto HLS e um manifesto de vários períodos DASH onde o novo Período começa no tempo de apresentação condicionado de encaixe preciso.

### <a name="221-live-server-manifest-box"></a>2.2.1 Caixa de manifesto do servidor ao vivo

A faixa escassa **DEVE** ser declarada na caixa Live Server Manifest com uma ** \<\> ** entrada textstream e **DEVE** ter os seguintes atributos definidos:

| **Nome do atributo** | **Tipo de campo** | **Necessário?** | **Descrição**                                                                                                                                                                                                              |
| ------------------ | -------------- | ------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| sistemaBitrate      | Número         | Necessário      | **Deve** ser "0", indicando uma faixa com bitrate variável desconhecido.                                                                                                                                                          |
| parentTrackName    | Cadeia         | Necessário      | **Deve** ser o nome da faixa-mãe, à qual os códigos de tempo escassos estão alinhados. A pista dos pais não pode ser uma pista escassa.                                                                             |
| manifestaçãoOutput     | Booleano        | Necessário      | **Deve** ser "verdadeiro", para indicar que a faixa escassa será incorporada no manifesto do cliente Smooth.                                                                                                                        |
| Subtipo            | Cadeia         | Necessário      | **Deve** ser o código de quatro caracteres "DATA".                                                                                                                                                                                  |
| Scheme             | Cadeia         | Necessário      | **Deve** ser uma URN ou URL identificando o esquema de mensagem. Para as mensagens [SCTE-35], esta **DEVE** ser "urn:scte:scte35:2013:bin" para que as mensagens sejam enviadas para os clientes HLS, Smooth e Dash em conformidade com [SCTE-35]. |
| trackName          | Cadeia         | Necessário      | **Deve** ser o nome da faixa escassa. O trackName pode ser usado para diferenciar vários fluxos de eventos com o mesmo esquema. Cada fluxo único de **eventomust** tem um nome de pista único.                                |
| escala de tempo          | Número         | Opcional      | **Deve** ser o calendário da faixa dos pais.                                                                                                                                                                               |

---

### <a name="222-movie-box"></a>2.2.2 Caixa de Cinema

A Caixa de Filme ('moov') segue a Caixa de Manifesto do Servidor Ao Vivo como parte do cabeçalho de fluxo para uma faixa escassa.

A caixa 'moov' **deve** conter uma caixa **TrackHeaderBox ('tkhd')** tal como definida em [ISO-14496-12] com os seguintes constrangimentos:

| **Nome de campo** | **Tipo de campo**          | **Necessário?** | **Descrição**                                                                                                    |
| -------------- | ----------------------- | ------------- | ------------------------------------------------------------------------------------------------------------------ |
| duration       | 64 bits não assinado sem assinatura inteiro | Necessário      | **Deve** ser 0, uma vez que a caixa de pista tem zero amostras e a duração total das amostras na caixa de pista é de 0. |

---

A caixa 'moov' **deve** conter uma **HandlerBox ('hdlr')** tal como definida em [ISO-14496-12] com os seguintes constrangimentos:

| **Nome de campo** | **Tipo de campo**          | **Necessário?** | **Descrição**       |
| -------------- | ----------------------- | ------------- | --------------------- |
| handler_type   | Inteiro sem assinatura de 32 bits | Necessário      | **Deve** ser "meta". |

---

A caixa 'stsd' **deve** conter uma caixa MetaDataSampleEntry com um nome de codificação definido em [ISO-14496-12].  Por exemplo, para as mensagens SCTE-35, o nome de codificação **deve** ser "scte".

### <a name="223-movie-fragment-box-and-media-data-box"></a>2.2.3 Caixa de fragmentode filme e caixa de dados dos media

Fragmentos de faixa escassaconsistem de uma Caixa de Fragmentode de Filme ('moof') e uma Media Data Box ('mdat').

> [!NOTE]
> Para conseguir uma inserção de anúncios com precisão de quadros, o codificador DEVE dividir o fragmento no momento de apresentação em que a deixa é necessária para ser inserida.  Um novo fragmento DEVE ser criado que comece com uma moldura IDR recém-criada, ou Pontos de Acesso fluviais (SAP) do tipo 1 ou 2, tal como definido no anexo I [ISO-14496-12].
> 

A caixa MovieFragmentBox ('moof') **DEVE** conter uma caixa **TrackFragmentExtendedHeaderBox ('uuid')** tal como definida em [MS-SSTR] com os seguintes campos:

| **Nome de campo**         | **Tipo de campo**          | **Necessário?** | **Descrição**                                                                                           |
| ---------------------- | ----------------------- | ------------- | --------------------------------------------------------------------------------------------------------- |
| fragment_absolute_time | 64 bits não assinado sem assinatura inteiro | Necessário      | **Deve** ser a hora de chegada do evento. Este valor alinha a mensagem com a faixa dos pais.           |
| fragment_duration      | 64 bits não assinado sem assinatura inteiro | Necessário      | **Deve** ser a duração do evento. A duração pode ser zero para indicar que a duração é desconhecida. |

---


A caixa MediaDataBox ('mdat') **DEVE** ter o seguinte formato:

| **Nome de campo**          | **Tipo de campo**                   | **Necessário?** | **Descrição**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ----------------------- | -------------------------------- | ------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| versão                 | Inteiro sem assinatura de 32 bits (uimsbf) | Necessário      | Determina o formato do conteúdo da caixa 'mdat'. Versões não reconhecidas serão ignoradas. Atualmente, a única versão suportada é 1.                                                                                                                                                                                                                                                                                                                                                                      |
| ID                      | Inteiro sem assinatura de 32 bits (uimsbf) | Necessário      | Identifica esta instância da mensagem. As mensagens com semântica equivalente devem ter o mesmo valor; isto é, processar qualquer caixa de mensagem de um evento com o mesmo id é suficiente.                                                                                                                                                                                                                                                                                                                            |
| presentation_time_delta | Inteiro sem assinatura de 32 bits (uimsbf) | Necessário      | A soma do fragment_absolute_time, especificada no TrackFragmentExtendedHeaderBox, e a presentation_time_delta **DEVE** ser o momento de apresentação do evento. O tempo e a duração da apresentação **devem** alinhar-se com os pontos de acesso ao fluxo (SAP) do tipo 1 ou 2, tal como definidos no anexo I [ISO-14496-12]. Para a saída de HLS, o tempo e a duração **devem** alinhar-se com os limites do segmento. O tempo de apresentação e a duração de diferentes mensagens de evento dentro do mesmo fluxo de eventos **NÃO DEVE** sobrepor-se. |
| message                 | matriz de bytes                       | Necessário      | A mensagem do evento. Para as mensagens [SCTE-35], a mensagem é o splice_info_section binário(). Para as mensagens [SCTE-35], esta **deve** ser a splice_info_section() para que as mensagens sejam enviadas aos clientes HLS, Smooth e Dash em conformidade com [SCTE-35]. Para as mensagens [SCTE-35], o splice_info_section binário é a carga útil da caixa 'mdat', e **NÃO** é codificada na base64.                                                                                                                     |

---


### <a name="224-cancellation-and-updates"></a>2.2.4 Cancelamento e Atualizações

As mensagens podem ser canceladas ou atualizadas enviando várias mensagens com o mesmo tempo de apresentação e ID.  O tempo de apresentação e id identificam exclusivamente o evento. A última mensagem recebida para um tempo específico de apresentação, que satisfaz os constrangimentos pré-roll, é a mensagem que é acionada. A mensagem atualizada substitui quaisquer mensagens recebidas anteriormente.  A restrição pré-roll é de quatro segundos. As mensagens recebidas pelo menos quatro segundos antes do tempo de apresentação serão acionadas. 


## <a name="3-timed-metadata-delivery"></a>3 Entrega de metadados cronometrados

Os dados de fluxo de eventos são opacos para os Serviços de Media. A Media Services apenas passa três informações entre o ponto final ingerir e o ponto final do cliente. As seguintes propriedades são entregues ao cliente, em conformidade com [SCTE-35] e/ou o protocolo de streaming do cliente:

1.  Esquema – URN ou URL que identifique o esquema da mensagem.
2.  Hora da Apresentação – o tempo de apresentação do evento na linha temporal dos media.
3.  Duração – duração do evento.
4.  ID – um identificador único opcional para o evento.
5.  Mensagem – os dados do evento.

## <a name="31-microsoft-smooth-streaming-manifest"></a>3.1 Microsoft Smooth Streaming Manifesto  

Consulte o manuseamento escasso da faixa [MS-SSTR] para obter detalhes sobre como formatar uma faixa de mensagem escassa. Para as mensagens [SCTE35], o Smooth Streaming irá transformar o splice_info_section codificado base64 num fragmento escasso.
O StreamIndex **MUST** tem um subtipo de "DATA", e os CustomATributos **DEVEM** conter um Atributo com Name="Schema" e Value="urn:scte:scte35:2013:bin".

#### <a name="smooth-client-manifest-example-showing-base64-encoded-scte35-splice_info_section"></a>Exemplo de manifesto de cliente suave mostrando base64 codificado [SCTE35] splice_info_section()
~~~ xml
<?xml version="1.0" encoding="utf-8"?>
<SmoothStreamingMedia MajorVersion="2" MinorVersion="0" TimeScale="10000000" IsLive="true" Duration="0"
  LookAheadFragmentCount="2" DVRWindowLength="6000000000">

  <StreamIndex Type="video" Name="video" Subtype="" Chunks="0" TimeScale="10000000"
    Url="QualityLevels({bitrate})/Fragments(video={start time})">
    <QualityLevel Index="0" Bitrate="230000"
      CodecPrivateData="250000010FC3460B50878A0B5821FF878780490800704704DC0000010E5A67F840" FourCC="WVC1"
      MaxWidth="364" MaxHeight="272"/>
    <QualityLevel Index="1" Bitrate="305000"
      CodecPrivateData="250000010FC3480B50878A0B5821FF87878049080894E4A7640000010E5A67F840" FourCC="WVC1"
      MaxWidth="364" MaxHeight="272"/>
    <c t="0" d="20000000" r="300" />
  </StreamIndex>
  <StreamIndex Type="audio" Name="audio" Subtype="" Chunks="0" TimeScale="10000000"
    Url="QualityLevels({bitrate})/Fragments(audio={start time})">
    <QualityLevel Index="0" Bitrate="96000" CodecPrivateData="1000030000000000000000000000E00042C0"
      FourCC="WMAP" AudioTag="354" Channels="2" SamplingRate="44100" BitsPerSample="16" PacketSize="4459"/>
    <c t="0" d="20000000" r="300" />
  </StreamIndex>
  <StreamIndex Type="text" Name="scte35-sparse-stream" Subtype="DATA" Chunks="0" TimeScale="10000000"
    ParentStreamIndex="video" ManifestOutput="true" 
    Url="QualityLevels({bitrate})/Fragments(captions={start time})">
    <QualityLevel Index="0" Bitrate="0" CodecPrivateData="" FourCC="">
      <CustomAttributes>
        <Attribute Name="Scheme" Value="urn:scte:scte35:2013:bin"/>
      </CustomAttributes>
    </QualityLevel>
    <!-- The following <c> and <f> fragments contains the base64-encoded [SCTE35] splice_info_section() message -->
    <c t="600000000" d="300000000">    <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTUwMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
    <c t="1200000000" d="400000000">      <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTYyMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
  </StreamIndex>
</SmoothStreamingMedia>
~~~

## <a name="32-apple-hls-manifest-decoration"></a>3.2 Apple HLS Manifesta Decoração

A Azure Media Services apoia as seguintes tags de manifesto hls para sinalizar informações de ad ad ad ad ad durante um evento ao vivo ou a pedido. 

- EXT-X-DATERANGE conforme definido no Apple HLS [RFC8216]
- EXT-X-CUE tal como definido em [Adobe-Primetime] - este modo é considerado "legado". Os clientes devem adotar a etiqueta EXT-X-DATERANGE quando possível.

A saída de dados de cada etiqueta variará em função do modo de sinal ingerir utilizado. Por exemplo, a ingestão RTMP com o modo Adobe Simple não contém a carga útil codificada por SCTE-35.

## <a name="321-apple-hls-with-ext-x-daterange-recommended"></a>3.2.1 Apple HLS com EXT-X-DATERANGE (recomendado)

A especificação Apple HTTP Live Streaming [RFC8216] permite a sinalização de mensagens [SCTE-35]. As mensagens são inseridas na lista de reprodução do segmento numa etiqueta EXT-X-DATERANGE por secção [RFC8216] intitulada "Mapping SCTE-35 in EXT-X-DATERANGE".  A camada de aplicação do cliente pode analisar a lista de reprodução m3U e processar etiquetas M3U, ou receber os eventos através da estrutura do jogador da Apple.  

A abordagem **RECOMENDADA** nos Serviços De Mídia Azure (versão 3 API) é seguir [RFC8216] e utilizar a etiqueta EXT-X_DATERANGE para a decoração de ad ad [SCTE35] no manifesto.

## <a name="3211-example-hls-manifest-m3u8-showing-ext-x-daterange-signaling-of-scte-35"></a>3.2.1.1 Manifesto HLS .m3u8 mostrando sinalização EXT-X-DATERANGE de SCTE-35

O exemplo seguinte, a saída manifesta do pacote dinâmico dos Media Services mostra a utilização da etiqueta EXT-X-DATERANGE de [RFC8216] que assinala os eventos SCTE-35 no fluxo. Além disso, este fluxo contém a etiqueta "legacy" EXT-X-CUE para [Adobe-Primetime].

~~~
#EXTM3U
#EXT-X-VERSION:8
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:2
#EXT-X-INDEPENDENT-SEGMENTS
#EXT-X-PROGRAM-DATE-TIME:2020-01-07T19:40:50Z
#EXTINF:1.501500,no-desc
Fragments(video=22567545,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22702680,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22837815,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22972950,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=23108085,format=m3u8-aapl-v8)
#EXTINF:1.234567,no-desc
Fragments(video=23243220,format=m3u8-aapl-v8)
#EXTINF:0.016689,no-desc
Fragments(video=23354331,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=0.000022
#EXTINF:0.250244,no-desc
Fragments(video=23355833,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=0.250267
#EXTINF:0.850856,no-desc
Fragments(video=23378355,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.101122
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:10.610Z",SCTE35-IN=0xFC30200000000005DD00FFF00F05000003EA7F4FFE0165E4D3000101010000607CE85A
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=0.000000,TIME=260.610344,CUE="/DAgAAAAAAXdAP/wDwUAAAPqf0/+AWXk0wABAQEAAGB86Fo="
#EXTINF:0.650644,no-desc
Fragments(video=23454932,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.751767
#EXTINF:0.050044,no-desc
Fragments(video=23513490,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.801811
#EXTINF:1.451456,no-desc
Fragments(video=23517994,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=3.253267
#EXTINF:1.501500,no-desc
Fragments(video=23648625,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=4.754767
#EXTINF:1.501500,no-desc
Fragments(video=23783760,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=6.256267
#EXTINF:1.501500,no-desc
Fragments(video=23918895,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=7.757767
#EXTINF:1.501500,no-desc
Fragments(video=24054030,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=9.259267
#EXTINF:1.501500,no-desc
Fragments(video=24189165,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=10.760767
#EXTINF:1.501500,no-desc
Fragments(video=24324300,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=12.262267
#EXTINF:1.501500,no-desc
Fragments(video=24459435,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=13.763767
#EXTINF:1.501500,no-desc
Fragments(video=24594570,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=15.265267
#EXTINF:1.501500,no-desc
Fragments(video=24729705,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=16.766767
#EXTINF:1.501500,no-desc
Fragments(video=24864840,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=18.268267
#EXTINF:1.501500,no-desc
Fragments(video=24999975,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=19.769767
#EXTINF:1.501500,no-desc
Fragments(video=25135110,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=21.271267
#EXTINF:1.501500,no-desc
Fragments(video=25270245,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=22.772767
#EXTINF:1.501500,no-desc
Fragments(video=25405380,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=24.274267
#EXTINF:1.501500,no-desc
Fragments(video=25540515,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=25.775767
#EXTINF:1.501500,no-desc
Fragments(video=25675650,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=27.277267
#EXTINF:1.501500,no-desc
Fragments(video=25810785,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=28.778767
#EXTINF:1.501500,no-desc
Fragments(video=25945920,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=30.280267
#EXTINF:1.501500,no-desc
Fragments(video=26081055,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=31.781767
#EXTINF:1.501500,no-desc
Fragments(video=26216190,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=33.283267
#EXTINF:1.501500,no-desc
Fragments(video=26351325,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=34.784767
#EXTINF:1.501500,no-desc
Fragments(video=26486460,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=36.286267
#EXTINF:1.501500,no-desc
Fragments(video=26621595,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=37.787767
#EXTINF:1.501500,no-desc
Fragments(video=26756730,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=39.289267
#EXTINF:1.501500,no-desc
Fragments(video=26891865,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=40.790767
#EXTINF:1.501500,no-desc
Fragments(video=27027000,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=42.292267
#EXTINF:1.501500,no-desc
Fragments(video=27162135,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=43.793767
#EXTINF:1.501500,no-desc
Fragments(video=27297270,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=45.295267
#EXTINF:1.501500,no-desc
Fragments(video=27432405,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=46.796767
#EXTINF:1.501500,no-desc
Fragments(video=27567540,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=48.298267
#EXTINF:1.501500,no-desc
Fragments(video=27702675,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=49.799767
#EXTINF:1.501500,no-desc
Fragments(video=27837810,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=51.301267
#EXTINF:1.501500,no-desc
Fragments(video=27972945,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=52.802767
#EXTINF:1.501500,no-desc
Fragments(video=28108080,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=54.304267
#EXTINF:1.501500,no-desc
Fragments(video=28243215,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=55.805767
#EXTINF:1.501500,no-desc
Fragments(video=28378350,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=57.307267
#EXTINF:1.501500,no-desc
Fragments(video=28513485,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=58.808767
#EXTINF:1.501500,no-desc
Fragments(video=28648620,format=m3u8-aapl-v8)

~~~


## <a name="322-apple-hls-with-adobe-primetime-ext-x-cue-legacy"></a>3.2.2 Apple HLS com Adobe Primetime EXT-X-CUE (legado)

Existe também uma implementação "legado" fornecida no Azure Media Services (versão 2 e 3 API) que utiliza a tag EXT-X-CUE conforme definido no "Modo SCTE-35" [Adobe-Primetime]. Neste modo, a Azure Media Services incorporará splice_info_section [SCTE-35] codificadas com base64 [SCTE-35] na etiqueta EXT-X-CUE.  

A etiqueta "legacy" EXT-X-CUE é definida como abaixo e também pode ser referenciada normativa na especificação [Adobe-Primetime]. Isto só deve ser utilizado para a sinalização sCTE35 legado sempre que necessário, caso contrário a etiqueta recomendada é definida em [RFC8216] como EXT-X-DATERANGE. 

| **Nome do atributo** | **Tipo**                      | **Necessário?**                             | **Descrição**                                                                                                                                                                                                                                                                          |
| ------------------ | ----------------------------- | ----------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| DEIXA                | corda citada                 | Necessário                                  | A mensagem codificada como uma cadeia codificada base64, tal como descrita em [RFC4648]. Para as mensagens [SCTE-35], esta é a base64 codificada splice_info_section().                                                                                                                                      |
| TIPO               | corda citada                 | Necessário                                  | Uma URN ou URL identificando o esquema de mensagem. Para as mensagens [SCTE-35], o tipo tem o valor especial "scte35".                                                                                                                                                                          |
| ID                 | corda citada                 | Necessário                                  | Um identificador único para o evento. Se o ID não for especificado quando a mensagem for ingerida, a Azure Media Services gerará um id único.                                                                                                                                              |
| DURAÇÃO           | número de ponto flutuante decimal | Necessário                                  | A duração do evento. Se desconhecido, o valor **deve** ser 0. As unidades são segundos de fação.                                                                                                                                                                                           |
| Decorrido            | número de ponto flutuante decimal | Opcional, mas necessário para deslizar janela | Quando o sinal está a ser repetido para suportar uma janela de apresentação deslizante, este campo **DEVE** ser a quantidade de tempo de apresentação que decorreu desde o início do evento. As unidades são segundos fracionados. Este valor pode exceder a duração especificada original da splice ou segmento. |
| HORA               | número de ponto flutuante decimal | Necessário                                  | O momento de apresentação do evento. As unidades são segundos fracionados.                                                                                                                                                                                                                        |


A camada de aplicação do leitor HLS utilizará o TYPE para identificar o formato da mensagem, descodificar a mensagem, aplicar as conversões de tempo necessárias e processar o evento.  Os eventos são sincronizados no segmento da lista de reprodução da faixa-mãe, de acordo com o carimbo temporal do evento.  São inseridos antes do segmento mais próximo (etiqueta #EXTINF).

### <a name="323-hls-m3u8-manifest-example-using-legacy-adobe-primetime-ext-x-cue"></a>3.2.3 HLS .m3u8 exemplo manifesto usando "Legacy" Adobe Primetime EXT-X-CUE

O exemplo que se segue mostra a decoração manifesta do HLS utilizando a etiqueta Adobe Primetime EXT-X-CUE.  O parâmetro "CUE" contém apenas as propriedades TYPE e Duration, o que significa que se trata de uma fonte RTMP utilizando a sinalização "simples" do modo Adobe.  Se se tratasse de um sinal de modo SCTE-35, a etiqueta incluiria a carga útil binária SCTE-35 codificada com base64, tal como se vê no [exemplo 3.2.1.1](#3211-example-hls-manifest-m3u8-showing-ext-x-daterange-signaling-of-scte-35).

~~~
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-PLAYLIST-TYPE:VOD
#EXT-X-ALLOW-CACHE:NO
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:11
#EXT-X-PROGRAM-DATE-TIME:2019-12-10T09:18:14Z
#EXTINF:10.010000,no-desc
Fragments(video=4011540820,format=m3u8-aapl)
#EXTINF:10.010000,no-desc
Fragments(video=4011550830,format=m3u8-aapl)
#EXTINF:10.010000,no-desc
Fragments(video=4011560840,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000
#EXTINF:8.008000,no-desc
Fragments(video=4011570850,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=0.593000
#EXTINF:4.170000,no-desc
Fragments(video=4011578858,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=4.763000
#EXTINF:9.844000,no-desc
Fragments(video=4011583028,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=14.607000
#EXTINF:10.010000,no-desc
Fragments(video=4011592872,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=24.617000
#EXTINF:10.010000,no-desc
Fragments(video=4011602882,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=34.627000
#EXTINF:10.010000,no-desc
Fragments(video=4011612892,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=44.637000
#EXTINF:10.010000,no-desc
Fragments(video=4011622902,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=54.647000
#EXTINF:10.010000,no-desc
Fragments(video=4011632912,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=64.657000
#EXTINF:10.010000,no-desc
Fragments(video=4011642922,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=74.667000
#EXTINF:10.010000,no-desc
Fragments(video=4011652932,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=84.677000
#EXTINF:10.010000,no-desc
Fragments(video=4011662942,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=94.687000
#EXTINF:10.010000,no-desc
Fragments(video=4011672952,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=104.697000
#EXTINF:10.010000,no-desc
Fragments(video=4011682962,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=114.707000
#EXTINF:10.010000,no-desc
Fragments(video=4011692972,format=m3u8-aapl)
#EXTINF:8.008000,no-desc
Fragments(video=4011702982,format=m3u8-aapl)

~~~

### <a name="324-hls-message-handling-for-legacy-adobe-primetime-ext-x-cue"></a>3.2.4 MANIPULAção de mensagens HLS para "Legacy" Adobe Primetime EXT-X-CUE

Os eventos são assinalados na lista de reprodução de cada vídeo e áudio. A posição da tag EXT-X-CUE **DEVE** ser sempre imediatamente antes do primeiro segmento HLS (para splice fora ou arranque de segmento) ou imediatamente após o último segmento HLS (para splice em ou extremidade de segmento) a que se referem os seus atributos TIME e DURATION, conforme exigido por [Adobe-Primetime].

Quando uma janela de apresentação deslizante está ativada, a etiqueta EXT-X-CUE **DEVE** ser repetida com frequência suficiente para que a combinação ou segmento seja sempre totalmente descrito na lista de reprodução do segmento, e o atributo ELAPSED **DEVE** ser utilizado para indicar a quantidade de tempo em que a splice ou segmento tem estado ativo, conforme exigido por [Adobe-Primetime].

Quando uma janela de apresentação deslizante é ativada, as tags EXT-X-CUE são removidas da lista de reprodução do segmento quando o tempo de mídia a que se referem saiu da janela de apresentação deslizante.

## <a name="33-dash-manifest-decoration-mpd"></a>3.3 Decoração manifesto DASH (MPD)

[MPEGDASH] fornece três formas de sinalizar eventos:

1.  Eventos assinalados no MPD EventStream
2.  Eventos sinalizados em banda utilizando a Caixa de Mensagens de Evento ('emsg')
3.  Uma combinação de 1 e 2

Os eventos sinalizados no MPD EventStream são úteis para o streaming vod porque os clientes têm acesso a todos os eventos, imediatamente quando o MPD é descarregado. Também é útil para a sinalização SSAI, onde o fornecedor SSAI a jusante precisa analisar os sinais de um manifesto MPD de vários períodos, e inserir conteúdo de anúncio dinamicamente.  A solução in-band ('emsg')é útil para o streaming ao vivo onde os clientes não precisam de baixar o MPD novamente, ou não há nenhuma manipulação manifesta SSAI acontecendo entre o cliente e a origem. 

O comportamento predefinido da Azure Media Services para o DASH é sinalizar tanto no MPD EventStream como na banda utilizando a Caixa de Mensagens de Evento ('emsg').

As mensagens de sugestão ingeridas sobre [RTMP] ou [MS-SSTR-Ingest] são mapeadas em eventos DASH, utilizando caixas 'emsg' da banda e/ou em MPD EventStreams. 

A sinalização SCTE-35 em banda para o DASH segue a definição e os requisitos definidos no [SCTE-214-3] e também na secção 13.12.2 ('SCTE35 Events'). 

Para o transporte em banda [SCTE-35], a caixa de mensagens de evento ('emsg') utiliza o esquemaId = "urn:scte:scte35:2013:bin". Para a decoração manifesto MPD o esquema EventStreamId utiliza "urn:scte:scte35:2014:xml+bin".  Este formato é uma representação XML do evento que inclui uma saída binária de base 64 codificada da mensagem Completa SCTE-35 que chegou à ingécentrada. 

As definições normativas de referência do transporte de mensagens de deixa [SCTE-35] no DASH estão disponíveis em [SCTE-214-1] sec 6.7.4 (MPD) e [SCTE-214-3] sec 7.3.2 (Transporte de scte 35 mensagens de deixa).

### <a name="331-mpeg-dash-mpd-eventstream-signaling"></a>3.3.1 MPEG DASH (MPD) Sinalização de fluxo de eventos

A decoração manifesta (MPD) dos eventos será assinalada no MPD utilizando o elemento EventStream, que aparece dentro do elemento Period. O esquema Utilizado é "urn:scte:scte35:2014:xml+bin".

> [!NOTE]
> Para efeitos de brevidade [SCTE-35] permite a utilização da secção codificada base64 no elemento Signal.Binary (em vez do elemento Signal.SpliceInfoSection) como alternativa ao transporte de uma mensagem de deixa completamente analisada.
> A Azure Media Services utiliza esta abordagem 'xml+bin' para sinalizar no manifesto MPD.
> Este é também o método recomendado utilizado na orientação [DASH-IF-IOP] - ver secção intitulada ["Fluxos de eventos de inserção de anúncios" da orientação DOPI IF IOP](https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html#ads-insertion-event-streams)
> 

O elemento EventStream tem os seguintes atributos:

| **Nome do atributo** | **Tipo**                | **Necessário?** | **Descrição**                                                                                                                                                                                                                                                                                                                                                                         |
| ------------------ | ----------------------- | ------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| scheme_id_uri      | string                  | Necessário      | Identifica o esquema da mensagem. O esquema está definido para o valor do atributo do Esquema na caixa De Manifesto do Servidor Vivo. O valor **deve** ser uma URN ou URL que identifique o esquema de mensagem; O regime de saída suportadoId deve ser "urn:scte:scte35:2014:xml+bin" por [SCTE-214-1] sec 6.7.4 (MPD), uma vez que o serviço suporta apenas "xml+bin" neste momento para brevidade no MPD. |
| valor              | string                  | Opcional      | Um valor de cadeia adicional utilizado pelos proprietários do esquema para personalizar a semântica da mensagem. A fim de diferenciar vários fluxos de eventos com o mesmo esquema, o valor **DEVE** ser definido para o nome do fluxo de eventos (trackName para [MS-SSTR-Ingest] ou nome de mensagem AMF para ingerir [RTMP]).                                                                         |
| Escala temporal          | Inteiro sem assinatura de 32 bits | Necessário      | O calendário, em carrapatos por segundo.                                                                                                                                                                                                                                                                                                                                                     |


### <a name="332-example-event-streams-for-mpeg-dash"></a>3.3.2 Fluxos de eventos de exemplo para MPEG DASH

#### <a name="3321-example-mpeg-dash-mpd-manifest-signaling-of-rtmp-streaming-using-adobe-simple-mode"></a>3.3.2.1 Exemplo MPEG DASH .mpd sinal de manifesto de streaming RTMP utilizando o modo adobe simples

O exemplo seguinte mostra um excerto do EventStream do pacote dinâmico media services para um fluxo RTMP utilizando a sinalização do modo Adobe "simples".

~~~ xml
<!-- Example EventStream element using "urn:com:adobe:dpi:simple:2015" Adobe simple signaling per [Adobe-Primetime] -->
    <EventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal" timescale="10000000">
        <Event presentationTime="1583497601000000" duration="300000000" id="1085900"/>
        <Event presentationTime="1583500901666666" duration="300000000" id="1415966"/>
        <Event presentationTime="1583504202333333" duration="300000000" id="1746033"/>
        <Event presentationTime="1583507502666666" duration="300000000" id="2076066"/>
        <Event presentationTime="1583510803333333" duration="300000000" id="2406133"/>
        <Event presentationTime="1583514104000000" duration="300000000" id="2736200"/>
        <Event presentationTime="1583517404666666" duration="300000000" id="3066266"/>
        <Event presentationTime="1583520705333333" duration="300000000" id="3396333"/>
        <Event presentationTime="1583524006000000" duration="300000000" id="3726400"/>
        <Event presentationTime="1583527306666666" duration="300000000" id="4056466"/>
        <Event presentationTime="1583530607333333" duration="300000000" id="4386533"/>
    </EventStream>
~~~

#### <a name="3322-example-mpeg-dash-mpd-manifest-signaling-of-an-rtmp-stream-using-adobe-scte-35-mode"></a>3.3.2.2 Exemplo MPEG DASH .mpd sinal de manifesto de um fluxo RTMP utilizando o modo Adobe SCTE-35

O exemplo seguinte mostra um excerto do Pacote Dinâmico De Serviços de Media para um fluxo RTMP utilizando a sinalização do modo Adobe SCTE-35.

~~~ xml
<!-- Example EventStream element using xml+bin style signaling per [SCTE-214-1] -->

      <EventStream schemeIdUri="urn:scte:scte35:2014:xml+bin" value="scte35" timescale="10000000">
        <Event presentationTime="2595092444" duration="11011000" id="1002">
            <Signal xmlns="http://www.scte.org/schemas/35/2016">
                <Binary>/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==</Binary>
            </Signal>
        </Event>
        <Event presentationTime="2606103444" id="1002">
            <Signal xmlns="http://www.scte.org/schemas/35/2016">
                <Binary>/DAgAAAAAAXdAP/wDwUAAAPqf0/+AWXk0wABAQEAAGB86Fo=</Binary>
            </Signal>
        </Event>
      </EventStream>
~~~

> [!IMPORTANT]
> Note que apresentaçãoO tempo de apresentação do evento [SCTE-35] traduzido para ser relativo à hora de início do período, e não a hora de chegada da mensagem.
> [MPEGDASH] define Event@presentationTime o como "Especifica o tempo de apresentação do evento em relação ao início do Período.
> O valor do tempo de apresentação em segundos é a divisão EventStream@timescale do valor deste atributo e o valor do atributo.
> Se não estiver presente, o valor do tempo de apresentação é 0.

#### <a name="3331-example-mpeg-dash-manifest-mpd-with-single-period-eventstream-using-adobe-simple-mode-signals"></a>3.3.3.1 Manifesto MPEG DASH (MPD) com um único período, EventStream, utilizando sinais de modo simples Adobe

O exemplo seguinte mostra a saída do pacote dinâmico media services para um fluxo RTMP de origem utilizando o método de sinal de anúncio "simples" do modo Adobe. A saída é um manifesto de um único período que mostra um EventStream utilizando o esquemaId Uri definido para "urn:com:adobe:dpi:simple:2015" e valor da propriedade definida como "simplesignal".
Cada sinal simples é fornecido num @presentationTime @durationelemento @id evento com o , e propriedades povoadas com base nos sinais simples de entrada.

~~~ xml
<?xml version="1.0" encoding="utf-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="static" mediaPresentationDuration="PT28M1.680S" minBufferTime="PT3S">
    <Period>
        <EventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal" timescale="1000">
            <Event presentationTime="4011578265" duration="119987" id="4011578265"/>
        </EventStream>
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.4D4028" maxWidth="1920" maxHeight="1080" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal"/>
            <ProducerReferenceTime id="4011460740" type="0" wallClockTime="2020-01-25T19:35:54.740Z" presentationTime="4011460740"/>
            <SegmentTemplate timescale="1000" presentationTimeOffset="4011460740" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="4011460740" d="2002" r="57"/>
                    <S d="1401"/>
                    <S d="601"/>
                    <S d="2002"/>

                     <!--> ... video segments truncated for sample brevity </-->

                </SegmentTimeline>
            </SegmentTemplate>
            <Representation id="1_V_video_14759481473095519504" bandwidth="6000000" width="1920" height="1080"/>
            <Representation id="1_V_video_1516803357996956148" bandwidth="4000000" codecs="avc1.4D401F" width="1280" height="720"/>
            <Representation id="1_V_video_5430608182379669372" bandwidth="2600000" codecs="avc1.4D401F" width="960" height="540"/>
            <Representation id="1_V_video_3780180650986497347" bandwidth="1000000" codecs="avc1.4D401E" width="640" height="360"/>
            <Representation id="1_V_video_13759117363700265707" bandwidth="699000" codecs="avc1.4D4015" width="480" height="270"/>
            <Representation id="1_V_video_6140004908920393176" bandwidth="400000" codecs="avc1.4D4015" width="480" height="270"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.4D400D" width="320" height="180"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.2">
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal"/>
            <ProducerReferenceTime id="4011460761" type="0" wallClockTime="2020-01-25T19:35:54.761Z" presentationTime="4011460761"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="1000" presentationTimeOffset="4011460740" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="4011460761" d="1984"/>
                    <S d="2005" r="1"/>
                    <S d="2006"/>

                    <!--> ... audio segments truncated for example brevity </-->

                </SegmentTimeline>
            </SegmentTemplate>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="48000"/>
        </AdaptationSet>
    </Period>
</MPD>

~~~

#### <a name="3332-example-mpeg-dash-manifest-mpd-with-multi-period-eventstream-using-adobe-scte35-mode-signaling"></a>3.3.3.2 Manifesto MPEG DASH (MPD) com multi-período, EventStream, utilizando a sinalização do modo Adobe SCTE35

O exemplo seguinte mostra a saída do pacote dinâmico media services para uma fonte de fluxo RTMP utilizando a sinalização do modo Adobe SCTE35.
Neste caso, o manifesto de saída é um DASH de vários @schemeIdUri períodos (MPD) com um elemento EventStream, e propriedade definida para "urn:scte:scte35:2014:xml+bin" e uma @value propriedade definida para "scte35". Cada elemento Evento no EventStream contém o sinal sCTE35 binário codificado com base 64 

~~~ xml
<?xml version="1.0" encoding="utf-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="dynamic" publishTime="2020-01-07T19:42:44Z" minimumUpdatePeriod="PT0S" timeShiftBufferDepth="PT58M56S" availabilityStartTime="2020-01-07T19:40:50Z" minBufferTime="PT4S">
    <Period start="PT2M48.168S" id="main-content_0">
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <SegmentTemplate timescale="90000" presentationTimeOffset="15135120" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="15135120" d="135135" r="59"/>
                    <S d="111111"/>
                    <S d="1502"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="15135120" type="0" wallClockTime="2020-01-07T19:40:50Z" presentationTime="15135120"/>
            <Representation id="1_V_video_5322324134428436312" bandwidth="3500000" width="1280" height="720"/>
            <Representation id="1_V_video_16981495139092747609" bandwidth="2200000" width="960" height="540"/>
            <Representation id="1_V_video_1384718563016940751" bandwidth="1350000" codecs="avc1.64001F" width="704" height="396"/>
            <Representation id="1_V_video_4425970933904124207" bandwidth="850000" codecs="avc1.64001E" width="512" height="288"/>
            <Representation id="1_V_video_11952982975776937431" bandwidth="550000" codecs="avc1.640016" width="384" height="216"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.640015" width="340" height="192"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.5" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="44100" presentationTimeOffset="7416208" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="7417856" d="133120"/>
                    <S d="132096" r="1"/>
                    
                    <!--> ... aduio segments truncated for sample brevity </-->

                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="7417856" type="0" wallClockTime="2020-01-07T19:40:50.037Z" presentationTime="7417856"/>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="44100"/>
        </AdaptationSet>
    </Period>
    <Period start="PT4M19.509S" id="scte-35_0">
        <EventStream schemeIdUri="urn:scte:scte35:2014:xml+bin" value="scte35" timescale="10000000">
            <Event presentationTime="2595092444" duration="11011000" id="1002">
                <Signal xmlns="http://www.scte.org/schemas/35/2016">
                    <Binary>/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==</Binary>
                </Signal>
            </Event>
            <Event presentationTime="2606103444" id="1002">
                <Signal xmlns="http://www.scte.org/schemas/35/2016">
                    <Binary>/DAgAAAAAAXdAP/wDwUAAAPqf0/+AWXk0wABAQEAAGB86Fo=</Binary>
                </Signal>
            </Event>
        </EventStream>
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <SegmentTemplate timescale="90000" presentationTimeOffset="23355832" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="23355833" d="22522"/>
                    <S d="76577"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="23355833" type="0" wallClockTime="2020-01-07T19:42:21.341Z" presentationTime="23355833"/>
            <Representation id="1_V_video_5322324134428436312" bandwidth="3500000" width="1280" height="720"/>
            <Representation id="1_V_video_16981495139092747609" bandwidth="2200000" width="960" height="540"/>
            <Representation id="1_V_video_1384718563016940751" bandwidth="1350000" codecs="avc1.64001F" width="704" height="396"/>
            <Representation id="1_V_video_4425970933904124207" bandwidth="850000" codecs="avc1.64001E" width="512" height="288"/>
            <Representation id="1_V_video_11952982975776937431" bandwidth="550000" codecs="avc1.640016" width="384" height="216"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.640015" width="340" height="192"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.5" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="44100" presentationTimeOffset="11444358" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="11446272" d="49152"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="11446272" type="0" wallClockTime="2020-01-07T19:42:21.384Z" presentationTime="11446272"/>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="44100"/>
        </AdaptationSet>
    </Period>
    <Period start="PT4M20.610S" id="main-content_1">
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <SegmentTemplate timescale="90000" presentationTimeOffset="23454931" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="23454932" d="58558"/>
                    <S d="4504"/>
                    <S d="130631"/>
                    <S d="135135" r="12"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="23454932" type="0" wallClockTime="2020-01-07T19:42:22.442Z" presentationTime="23454932"/>
            <Representation id="1_V_video_5322324134428436312" bandwidth="3500000" width="1280" height="720"/>
            <Representation id="1_V_video_16981495139092747609" bandwidth="2200000" width="960" height="540"/>
            <Representation id="1_V_video_1384718563016940751" bandwidth="1350000" codecs="avc1.64001F" width="704" height="396"/>
            <Representation id="1_V_video_4425970933904124207" bandwidth="850000" codecs="avc1.64001E" width="512" height="288"/>
            <Representation id="1_V_video_11952982975776937431" bandwidth="550000" codecs="avc1.640016" width="384" height="216"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.640015" width="340" height="192"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.5" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="44100" presentationTimeOffset="11492916" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="11495424" d="28672"/>
                    <S d="1024"/>
                    <S d="131072"/>
                    <S d="132096"/>
                    <S d="133120"/>
                    <S d="132096" r="1"/>
                    <S d="133120"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="11495424" type="0" wallClockTime="2020-01-07T19:42:22.499Z" presentationTime="11495424"/>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="44100"/>
        </AdaptationSet>
    </Period>
</MPD>

~~~
### <a name="334-mpeg-dash-in-band-event-message-box-signaling"></a>3.3.4 MPEG DASH Na banda De caixa de mensagens de evento

Um fluxo de eventos em banda requer que o MPD tenha um elemento InbandEventStream ao nível do Conjunto de Adaptação.  Este elemento tem um esquema obrigatório IdUri atributo e atributo de prazo opcional, que também aparecem na Caixa de Mensagens de Evento ('emsg').  As caixas de mensagens de evento com identificadores de esquema que não estejam definidos no MPD não **devem** estar presentes.

Para o transporte em banda [SCTE-35], os sinais **DEVEM** utilizar o esquemaId = "urn:scte:scte35:2013:bin".
As definições normativas de transporte de mensagens em banda [SCTE-35] são definidas em [SCTE-214-3] sec 7.3.2 (Transporte de mensagens de sinalização SCTE 35).

Os seguintes detalhes descrevem os valores específicos que o cliente deve esperar no 'emsg' em conformidade com [SCTE-214-3]:

| **Nome de campo**          | **Tipo de campo**          | **Necessário?** | **Descrição**                                                                                                                                                                                                                                                                                        |
| ----------------------- | ----------------------- | ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| scheme_id_uri           | string                  | Necessário      | Identifica o esquema da mensagem. O esquema está definido para o valor do atributo do Esquema na caixa De Manifesto do Servidor Vivo. O valor **DEVE** ser um URN que identifique o sistema de mensagens. Para as mensagens [SCTE-35], esta **DEVE** ser "urn:scte:scte35:2013:bin" em conformidade com [SCTE-214-3]          |
| Valor                   | string                  | Necessário      | Um valor de cadeia adicional utilizado pelos proprietários do esquema para personalizar a semântica da mensagem. Para diferenciar vários fluxos de eventos com o mesmo esquema, o valor será definido para o nome do fluxo de eventos (trackName para ingerir suavemente ou nome de mensagem AMF para ingerir RTMP). |
| Escala temporal               | Inteiro sem assinatura de 32 bits | Necessário      | O calendário, em carrapatos por segundo, dos campos de tempo e duração dentro da caixa 'emsg'.                                                                                                                                                                                                            |
| Presentation_time_delta | Inteiro sem assinatura de 32 bits | Necessário      | O tempo de apresentação mediática delta do tempo de apresentação do evento e o mais antigo tempo de apresentação neste segmento. O tempo e a duração da apresentação **devem** alinhar-se com os pontos de acesso ao fluxo (SAP) do tipo 1 ou 2, tal como definidos no anexo I [ISO-14496-12].                                  |
| event_duration          | Inteiro sem assinatura de 32 bits | Necessário      | A duração do evento, ou 0xFFFFFFFF para indicar uma duração desconhecida.                                                                                                                                                                                                                              |
| Id                      | Inteiro sem assinatura de 32 bits | Necessário      | Identifica esta instância da mensagem. As mensagens com semântica equivalente devem ter o mesmo valor. Se o ID não for especificado quando a mensagem for ingerida, a Azure Media Services gerará um id único.                                                                                        |
| Message_data            | matriz de bytes              | Necessário      | A mensagem do evento. Para as mensagens [SCTE-35], os dados da mensagem são o splice_info_section binário() em conformidade com [SCTE-214-3]                                                                                                                                                                        |


#### <a name="example-inbandevenstream-entity-for-adobe-simple-mode"></a>Exemplo InBandEvenStream entidade para o modo Adobe Simple
~~~ xml

      <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
~~~

### <a name="335-dash-message-handling"></a>3.3.5 Manipulação de Mensagens DASH

Os eventos são sinalizados em banda, dentro da caixa 'emsg', tanto para faixas de vídeo como de áudio.  A sinalização ocorre para todos os pedidos de segmento para os quais o presentation_time_delta é de 15 segundos ou menos. 

Quando uma janela de apresentação deslizante é ativada, as mensagens de evento são removidas do MPD quando a soma da hora e duração da mensagem do evento for inferior ao tempo dos dados dos meios de comunicação no manifesto.  Por outras palavras, as mensagens do evento são removidas do manifesto quando o tempo mediático a que se referem saiu da janela de apresentação deslizante.

## <a name="4-scte-35-ingest-implementation-guidance-for-encoder-vendors"></a>4. Orientação de implementação ingérea sCTE-35 para vendedores codificadores

As seguintes orientações são questões comuns que podem afetar a implementação desta especificação por parte de um fornecedor codificador.  As diretrizes abaixo foram recolhidas com base no feedback dos parceiros do mundo real para facilitar a implementação desta especificação para outros. 

As mensagens [SCTE-35] são ingeridas em formato binário utilizando o Regime **"urn:scte:scte35:2013:bin"** para [MS-SSTR-Ingest] e o tipo **"scte35"** para ingerir [RTMP]. Para facilitar a conversão do tempo [SCTE-35], que se baseia nos selos de tempo de apresentação do fluxo de transporte MPEG-2 (PTS), um mapeamento entre PTS (pts_time + pts_adjustment do splice_time()) e a linha temporal dos meios de comunicação é fornecido pelo tempo de apresentação do evento ( o campo fragment_absolute_time para a ingestão suave e o campo de tempo para a ingestão RTMP). O mapeamento é necessário porque o valor pts de 33 bits rola aproximadamente a cada 26,5 horas.

A ingestão de streaming suave [MS-SSTR-Ingest] requer que a Caixa de Dados dos Meios de Comunicação ('mdat') **deve** conter o **splice_info_section()** definido em [SCTE-35]. 

Para a ingestão RTMP,o atributo da mensagem AMF é definido para o **splice_info_section** codificado com base64() definido em [SCTE-35].  

Quando as mensagens têm o formato descrito acima, são enviadas para os clientes HLS, Smooth e DASH, tal como definidos acima.  

Ao testar a sua implementação com a plataforma Azure Media Services, comece a testar primeiro com um LiveEvent "pass-through", antes de passar a testar um LiveEvent codificante.

---

## <a name="change-history"></a>Histórico de Alterações

| Date     | Alterações                                                                                                             |
| -------- | ------------------------------------------------------------------------------------------------------------------- |
| 07/2/19  | Revê rtmp revisto para suporte SCTE35, acrescentou RTMP "onCuePoint" para Elemental Live                                  |
| 08/22/19 | Atualizado para adicionar OnUserDataEvent ao RTMP para metadados personalizados                                                          |
| 1/08/20  | Erro fixo no modo RTMP Simple e RTMP SCTE35. Passou de "onCuePoint" para "onAdCue". Tabela de modo simples atualizada. |

## <a name="next-steps"></a>Passos seguintes
Ver caminhos de aprendizagem dos Serviços de Media.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
