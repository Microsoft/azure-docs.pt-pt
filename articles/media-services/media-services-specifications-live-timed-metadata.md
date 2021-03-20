---
title: Azure Media Services - Sinalização de metadados cronometrados em streaming ao vivo
description: Esta especificação descreve métodos para sinalizar metadados cronometrados ao ingerir e transmitir para a Azure Media Services. Isto inclui suporte para sinais genéricos de metadados cronometrados (ID3), bem como sinalização SCTE-35 para inserção de anúncios e sinalização de condição de emenda.
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
ms.openlocfilehash: f826ee9ef3c9fff0b721a9c79d3c12e0adbd5f7f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91336399"
---
# <a name="signaling-timed-metadata-in-live-streaming"></a>Metadados cronometrados de sinalização em streaming ao vivo 

Última Atualização: 2019-08-22

### <a name="conformance-notation"></a>Notação de conformidade

As palavras-chave "MUST", "MUST NOT", "MUST", "SHALL", "SHOULD", "SHOULD", "SHOULD", "RECOMMENDED", "MAY" e "OPTIONAL" neste documento devem ser interpretadas como descrito no RFC 2119

## <a name="1-introduction"></a>1. Introdução 

Para assinalar a inserção de anúncios ou eventos de metadados personalizados num leitor de clientes, os emissores muitas vezes utilizam metadados cronometrados incorporados no vídeo. Para ativar estes cenários, a Media Services fornece suporte para o transporte de metadados cronometrados desde o ponto de ingestão do canal de streaming ao vivo até à aplicação do cliente.
Esta especificação descreve vários modos que são suportados pelos Media Services para metadados cronometrados dentro dos sinais de streaming ao vivo.

1. [SCTE-35] sinalização que cumpre as normas descritas por [SCTE-35], [SCTE-214-1], [SCTE-214-3] e [RFC8216]

2. [SCTE-35] sinalização que está em conformidade com a especificação legado [Adobe-Primetime] para a sinalização de anúncios RTMP.
   
3. Um modo genérico de sinalização de metadados cronometrados, para mensagens que **NÃO** são [SCTE-35] e que podem transportar [ID3v2] ou outros esquemas personalizados definidos pelo desenvolvedor da aplicação.

## <a name="11-terms-used"></a>1.1 Termos Utilizados

| Termo                | Definição                                                                                                                                                                                                                                    |
| ------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Rutura de anúncio            | Uma localização ou ponto no tempo em que um ou mais anúncios podem ser agendados para entrega; mesmo que a oportunidade de sucesso e colocação.                                                                                                                     |
| Serviço de Decisão de Anúncios | serviço externo que decide quais anúncios e durações serão mostrados ao utilizador. Os serviços são normalmente prestados por um parceiro e estão fora de alcance para este documento.                                                                    |
| Deixa                 | Indicação do tempo e dos parâmetros da próxima rutura de anúncio. Note que as pistas podem indicar um interruptor pendente para uma rutura de anúncio, pendente de comutação para o próximo anúncio dentro de um intervalo de anúncios, e pendente de um interruptor de uma rutura de anúncio para o conteúdo principal.           |
| Embalador            | O Azure Media Services "Streaming Endpoint" fornece capacidades de embalagem dinâmicas para o DASH e o HLS e é referido como um "Packager" na indústria dos media.                                                                              |
| Hora da apresentação   | O tempo que um evento é apresentado a um espectador. O tempo representa o momento na linha temporal mediática que um espectador veria o evento. Por exemplo, o tempo de apresentação de uma mensagem de comando splice_info() SCTE-35 é o splice_time(). |
| Hora da Chegada        | O momento em que uma mensagem de evento chega. O tempo é tipicamente distinto do tempo de apresentação do evento, uma vez que as mensagens de evento são enviadas antes da hora de apresentação do evento.                                                    |
| Pista escassa        | pista de media que não é contínua, e é sincronizada com um pai ou pista de controlo.                                                                                                                                                  |
| Origem              | O Serviço de Streaming Azure Media                                                                                                                                                                                                             |
| Pia do Canal        | O serviço de streaming ao vivo da Azure Media                                                                                                                                                                                                        |
| HLS                 | Protocolo de streaming ao vivo da Apple HTTP                                                                                                                                                                                                            |
| DASH                | Streaming adaptativo dinâmico em HTTP                                                                                                                                                                                                          |
| Suave              | Protocolo de streaming suave                                                                                                                                                                                                                     |
| MPEG2-TS            | FLUXOS de Transporte MPEG 2                                                                                                                                                                                                                      |
| RTMP                | Protocolo multimédia Real-Time                                                                                                                                                                                                                 |
| uimsbf              | Número inteiro não assinado, o mais significativo primeiro.                                                                                                                                                                                                 |

---

## <a name="12-normative-references"></a>1.2 Referências normativas

Os documentos que se seguem contêm disposições que, através de referência neste texto, constituem disposições do presente documento. Todos os documentos estão sujeitos a revisão pelos órgãos de normalização, e os leitores são encorajados a investigar a possibilidade de aplicar as edições mais recentes dos documentos listados abaixo. Os leitores também são lembrados que as edições mais recentes dos documentos referenciados podem não ser compatíveis com esta versão da especificação de metadados cronometrados para a Azure Media Services.


| Standard          | Definição                                                                                                                                                                                                     |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Adobe-Primetime] | [Especificação de sinalização de sinalização de inserção de programa digital primetime 1.2](https://www.adobe.com/content/dam/acom/en/devnet/primetime/PrimetimeDigitalProgramInsertionSignalingSpecification.pdf)                       |
| [Adobe-Flash-AS]  | [Referência do idioma flash ActionScript](https://help.adobe.com/archive/en_US/as2/flashlite_2.x_3.x_aslr.pdf)                                                                                                   |
| [AMF0]            | ["Action Message Format AMF0"](https://download.macromedia.com/pub/labs/amf/amf0_spec_121207.pdf)                                                                                                              |
| [DASH-IF-IOP]     | Orientação interop do Fórum da Indústria DASH v4.2 [https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html](https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html)    |
| [HLS-TMD]         | Metadados cronometrados para http live streaming - [https://developer.apple.com/streaming](https://developer.apple.com/streaming)                                                                                        |
| [CMAF-ID3]        | [Metadados cronometrados no Formato Comum de Aplicação de Meios de Comunicação Social (CMAF)](https://github.com/AOMediaCodec/id3-emsg)                                                                                                        |
| [ID3v2]           | ID3 Tag versão 2.4.0  [http://id3.org/id3v2.4.0-structure](http://id3.org/id3v2.4.0-structure)                                                                                                                |
| [ISO-14496-12]    | ISO/IEC 14496-12: Formato de ficheiro de base ISO da parte 12, Quarta Edição 2012-07-15                                                                                                                                 |
| [MPEGDASH]        | Tecnologia de informação -- Streaming adaptativo dinâmico sobre HTTP (DASH) -- Parte 1: Descrição da apresentação dos meios de comunicação e formatos de segmento. Maio de 2014. Publicado. URL: https://www.iso.org/standard/65274.html         |
| [MPEGCMAF]        | Tecnologia de informação -- Formato de aplicação multimédia (MPEG-A) -- Parte 19: Formato comum de aplicação de mídia (CMAF) para meios segmentados. Janeiro de 2018. Publicado. URL: https://www.iso.org/standard/71975.html |
| [MPEGCENC]        | Tecnologias de informação -- tecnologias de sistemas MPEG -- Parte 7: Encriptação comum em ficheiros de ficheiros de ficheiros de base ISO. Fevereiro de 2016. Publicado. URL: https://www.iso.org/standard/68042.html                   |
| [MS-SSTR]         | ["Microsoft Smooth Streaming Protocol", 15 de maio de 2014](/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251)                                                     |
| [MS-SSTR-Ingest]  | [Azure Media Services Fragmentado MP4 Live Ingest Specification](./media-services-fmp4-live-ingest-overview.md)                                                      |
| [RFC8216]         | R. Pantos, Ed.; Maio. HTTP Live Streaming. Agosto de 2017. Informativo. [https://tools.ietf.org/html/rfc8216](https://tools.ietf.org/html/rfc8216)                                                            |
| [RFC4648]         | As codificações de dados Base16, Base32 e Base64 - [https://tools.ietf.org/html/rfc4648](https://tools.ietf.org/html/rfc4648)                                                                                     |
| [RTMP]            | ["Protocolo de Mensagens Real-Time da Adobe", 21 de dezembro de 2012](https://www.adobe.com/devnet/rtmp.html)                                                                                                            |
| [SCTE-35-2019]    | SCTE 35: 2019 - Mensagem de inserção de programa digital para cabo - https://www.scte.org/SCTEDocs/Standards/ANSI_SCTE%2035%202019r1.pdf                                                                       |
| [SCTE-214-1]      | SCTE 214-1 2016 - MPEG DASH para IP-Based Serviços de Cabo Parte 1: Restrições e extensões de MPD                                                                                                                 |
| [SCTE-214-3]      | SCTE 214-3 2015 MPEG DASH para IP-Based Serviços de Cabo Parte 3: Perfil DASH/FF                                                                                                                                  |
| [SCTE-224]        | SCTE 224 2018r1 - Interface de Agendamento e Notificação de Eventos                                                                                                                                                  |
| [SCTE-250]        | API de Gestão de Eventos e Sinalização (ESAM)                                                                                                                                                                      |

---


## <a name="2-timed-metadata-ingest"></a>2. Metadados tempores Ingerir

A Azure Media Services suporta metadados em banda em tempo real para os protocolos [RTMP] e Smooth Streaming [MS-SSTR-Ingest]. Os metadados em tempo real podem ser usados para definir eventos personalizados, com os seus próprios esquemas personalizados únicos (JSON, Binary, XML), bem como formatos definidos pela indústria como ID3, ou SCTE-35 para sinalização de anúncios num stream de transmissão. 

Este artigo fornece os detalhes sobre como enviar sinais de metadados cronometrados personalizados usando os protocolos de ingestão suportados da Azure Media Services. O artigo também explica como os manifestos para HLS, DASH e Smooth Streaming são decorados com os sinais de metadados cronometrados, bem como como é transportado em banda quando o conteúdo é entregue usando segmentos CMAF (fragmentos MP4) ou Transport Stream (TS) para HLS. 

Os cenários de caso de utilização comum para metadados cronometrados incluem:

 - Sinais de anúncio SCTE-35 para desencadear quebras de anúncios em um evento ao vivo ou transmissão linear
 - Metadados ID3 personalizados que podem desencadear eventos numa aplicação do cliente (browser, iOS ou Android)
 - Metadados JSON, Binário ou XML definidos por costume para desencadear eventos numa aplicação de cliente
 - Telemetria a partir de um codificar ao vivo, câmara IP ou Drone
 - Eventos de uma câmara IP como Motion, deteção facial, etc.
 - Informações de posição geográfica de uma câmara de ação, drone ou dispositivo móvel
 - Letra da canção
 - Limites do programa em um feed linear ao vivo
 - Imagens ou metadados aumentados a serem exibidos num feed ao vivo
 - Pontuações desportivas ou informações do relógio de jogo
 - Pacotes de publicidade interativa a serem exibidos ao lado do vídeo no navegador
 - Quizzes ou sondagens
  
A Azure Media Services Live Events and Packager são capazes de receber estes sinais de metadados cronometrados e convertê-los num fluxo de metadados que podem chegar a aplicações de clientes usando protocolos baseados em padrões como HLS e DASH.


## <a name="21-rtmp-timed-metadata"></a>2.1 Metadados cronometrados RTMP

O protocolo [RTMP] permite que os sinais de metadados cronometrados sejam enviados para vários cenários, incluindo metadados personalizados e sinais de anúncios SCTE-35. 

Os sinais de publicidade (mensagens de sugestão) são enviados como mensagens de sugestão [AMF0] incorporadas no fluxo [RTMP]. As mensagens de sugestão podem ser enviadas algum tempo antes do evento real ou o sinal de emenda de anúncio [SCTE35] precisa de ocorrer. Para apoiar este cenário, o tempo de apresentação real do evento é enviado dentro da mensagem de sugestão. Para mais informações, consulte [AMF0].

Os seguintes comandos [AMF0] são suportados pela Azure Media Services para a rtmp ingestão:

- **onUserDataEvent** - usado para metadados personalizados ou metadados cronometrados [ID3v2]
- **onAdCue** - usado principalmente para sinalizar uma oportunidade de colocação de publicidade no live stream. Duas formas da deixa são suportadas, um modo simples e um modo "SCTE-35". 
- **onCuePoint** - suportado por certos codificadores de hardware no local, como o codificadora Elemental Live, para sinalizar mensagens [SCTE35]. 
  

A tabela seguinte descreve o formato da carga útil da mensagem AMF que os Media Services irão ingerir tanto para os modos de mensagem "simples" como para os modos de mensagem [SCTE35].

O nome da mensagem [AMF0] pode ser usado para diferenciar vários fluxos de eventos do mesmo tipo.  Tanto para as mensagens [SCTE-35] como para o modo "simples", o nome da mensagem AMF DEVE ser "onAdCue" conforme exigido na especificação [Adobe-Primetime].  Quaisquer campos não listados abaixo serão ignorados pela Azure Media Services na ingestão.

## <a name="211-rtmp-with-custom-metadata-using-onuserdataevent"></a>2.1.1 RTMP com metadados personalizados utilizando "onUserDataEvent"

Se pretender fornecer feeds de metadados personalizados do seu codificador a montante, câmara IP, drone ou dispositivo utilizando o protocolo RTMP, utilize o tipo de comando de mensagem de dados "onUserDataEvent" [AMF0].

O comando de mensagem **de dados "onUserDataEvent"** DEVE transportar uma carga útil de mensagens com a seguinte definição a ser capturada pelos Media Services e embalada no formato de ficheiro em banda, bem como os manifestos para HLS, DASH e Smooth Streaming.
Recomenda-se o envio de mensagens de metadados cronometrados não mais frequentemente do que uma vez a cada 0,5 segundos (500ms) ou podem ocorrer problemas de estabilidade com o fluxo ao vivo. Cada mensagem pode agregar metadados de vários quadros se precisar de fornecer metadados de nível de quadro. Se estiver a enviar streams multi-bitrate, recomenda-se que também forneça os metadados numa única bitrate apenas para reduzir a largura de banda e evitar interferências no processamento de vídeo/áudio. 

A carga útil para o **"onUserDataEvent"** deve ser uma mensagem de formato XML [MPEGDASH]. Isto facilita a passagem em esquemas definidos personalizados que podem ser transportados em 'emsg' em banda para conteúdo CMAF [MPEGCMAF] que é entregue ao longo de protocolos HLS ou DASH. Cada mensagem DASH Event Stream contém um esquemaIdUri que funciona como um identificador de esquema de mensagem URN e define a carga útil da mensagem. Alguns esquemas como " https://aomedia.org/emsg/ID3 para [ID3v2], ou **urna:scte:scte35:2013:bin** para [SCTE-35] são padronizados por consórcios da indústria para interoperabilidade. Qualquer fornecedor de aplicações pode definir o seu próprio esquema personalizado usando um URL que controlam (domínio próprio) e pode fornecer uma especificação nesse URL se assim o desejarem. Se um jogador tem um manipulador para o esquema definido, então este é o único componente que precisa entender a carga útil e o protocolo.

O esquema para a carga de XML do EventStream XML [MPEG-DASH] é definido como (excerto da DASH ISO-IEC-23009-1-3rd Edition). Note que apenas um "EventType" por "EventStream" é suportado neste momento. Apenas o primeiro elemento **do Evento** será processado se vários eventos forem fornecidos no **EventStream**.

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


### <a name="example-xml-event-stream-with-id3-schema-id-and-base64-encoded-data-payload"></a>Exemplo XML Event Stream com ID3 schema ID e base64-codificado carga de dados.  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="https://aomedia.org/emsg/ID3">
         <Event contentEncoding="Base64">
          -- base64 encoded ID3v2 full payload here per [CMAF-TMD] --
         </Event>
   <EventStream>
```

### <a name="example-event-stream-with-custom-schema-id-and-base64-encoded-binary-data"></a>Exemplo Fluxo de Eventos com iD de esquema personalizado e dados binários codificados de base64  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="urn:example.org:custom:binary">
         <Event contentEncoding="Base64">
          -- base64 encoded custom binary data message --
         </Event>
   <EventStream>
```

### <a name="example-event-stream-with-custom-schema-id-and-custom-json"></a>Exemplo Fluxo de eventos com iD de esquema personalizado e JSON personalizado  
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

### <a name="built-in-supported-scheme-id-uris"></a>ID IIs do regime suportado incorporado
| ID ID do esquema                 | Description                                                                                                                                                                                                                                          |
| ----------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| https: \/ /aomedia.org/emsg/ID3 | Descreve como os metadados [ID3v2] podem ser transportados como metadados cronometrados num MP4 fragmentado compatível com CMAF [MPEGCMAF]. Para mais informações consulte os [metadados cronometrados no Formato Comum de Aplicação de Meios de Comunicação Social (CMAF)](https://github.com/AOMediaCodec/id3-emsg) |

### <a name="event-processing-and-manifest-signaling"></a>Processamento de eventos e sinalização manifesta

Ao receber um evento **válido "onUserDataEvent",** a Azure Media Services procurará uma carga útil XML válida que corresponda ao EventStreamType (definido em [MPEGDASH]), analise a carga útil XML e converta-a numa [MPEGCMAF] mp4 fragmento 'emsg' versão 1 caixa para armazenamento no arquivo ao vivo e transmissão para o Media Services Packager.   O Embalador detetará a caixa 'emsg' no fluxo ao vivo e:

- a "pacote dinamicamente" nos segmentos TS para entrega aos clientes HLS, em conformidade com a especificação de metadados cronometrados HLS [HLS-TMD];
- b Passá-lo para entrega em fragmentos CMAF via HLS ou DASH; 
- c Convertê-lo num sinal de via escasso para entrega através de streaming liso [MS-SSTR].

Para além do formato 'emsg' em banda CMAF ou pacotes PES TS para HLS, os manifestos para DASH (MPD) e Smooth Streaming conterão uma referência aos streams de eventos em banda (também conhecido como pista de streaming escasso em Smooth Streaming).

Os eventos individuais ou as suas cargas de dados NÃO são output diretamente nos manifestos HLS, DASH ou Smooth. 

### <a name="additional-informational-constraints-and-defaults-for-onuserdataevent-events"></a>Restrições informativas adicionais e incumprimentos para eventos onUserDataEvent

- Se o prazo não for definido no elemento EventStream, o calendário RTMP 1 kHz é utilizado por padrão
- A entrega de uma mensagem onUserDataEvent é limitada a uma vez a cada 500ms no máximo. Se enviar eventos com mais frequência, pode afetar a largura de banda e a estabilidade do feed ao vivo

## <a name="212-rtmp-ad-cue-signaling-with-onadcue"></a>2.1.2 Sinal de ad cue RTMP com "onAdCue"

O Azure Media Services pode ouvir e responder a vários tipos de mensagens [AMF0] que podem ser usados para sinalizar vários metadados sincronizados em tempo real no live stream.  A especificação [Adobe-Primetime] define dois tipos de sugestões chamados "simples" e "SCTE-35". Para o modo "simples", os Serviços de Comunicação suportam uma única mensagem de sugestão AMF chamada "onAdCue" utilizando uma carga útil que corresponde à tabela abaixo definida para o sinal "Modo Simples".  

A seguinte secção mostra a carga útil "simples" do modo RTMP, que pode ser usada para sinalizar um sinal de anúncio básico "spliceOut" que será transportado para o manifesto do cliente para HLS, DASH e Microsoft Smooth Streaming. Isto é muito útil para cenários em que o cliente não tem um complexo sistema de sinalização ou inserção de anúncios baseado scTE-35, e está a usar um codificadora básico no local para enviar a mensagem de deixa através de uma API. Normalmente, o codificador no local irá suportar uma API baseada em REST para acionar este sinal, que também irá "emendar" o fluxo de vídeo, inserindo uma moldura IDR no vídeo, e iniciando um novo GOP.

## <a name="213--rtmp-ad-cue-signaling-with-onadcue---simple-mode"></a>2.1.3 Sinal de ad cue RTMP com "onAdCue" - Modo simples

| Nome do Campo | Tipo de Campo | Necessário? | Descrições                                                                                                                                                                                                                                                                        |
| ---------- | ---------- | --------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| tipo       | String     | Necessário  | A mensagem do evento.  Deve ser "SpliceOut" para designar uma ligação de modo simples.                                                                                                                                                                                                         |
| ID         | String     | Necessário  | Um identificador único descrevendo a emenda ou segmento. Identifica esta instância da mensagem                                                                                                                                                                                       |
| duration   | Número     | Necessário  | A duração da liga. As unidades são segundos fracionais.                                                                                                                                                                                                                           |
| elapsed    | Número     | Opcional  | Quando o sinal estiver a ser repetido para apoiar a sintonização, este campo deve ser a quantidade de tempo de apresentação decorrido desde o início da ligação. As unidades são segundos fracionais. Ao utilizar o modo simples, este valor não deve exceder a duração original da ligação. |
| hora       | Número     | Necessário  | Será a hora da emenda, em tempo de apresentação. As unidades são segundos fracionais.                                                                                                                                                                                                |

---
 
#### <a name="example-mpeg-dash-manifest-output-when-using-adobe-rtmp-simple-mode"></a>Exemplo MPEG DASH saída manifesto ao usar o modo simples Adobe RTMP

Veja o exemplo [3.3.2.2. MPEG DASH .mpd EventStream usando o modo simples Adobe](#3321-example-mpeg-dash-mpd-manifest-signaling-of-rtmp-streaming-using-adobe-simple-mode)

Veja o exemplo [3.3.3.1 DASH manifesto com um único período e modo simples Adobe ](#3331-example-mpeg-dash-manifest-mpd-with-single-period-eventstream-using-adobe-simple-mode-signals)

#### <a name="example-hls-manifest-output-when-using-adobe-rtmp-simple-mode"></a>Exemplo HLS manifesta saída ao usar o modo simples Adobe RTMP

Veja o exemplo [3.2.2 HLS manifesto usando o modo simples Adobe e a etiqueta EXT-X-CUE](#322-apple-hls-with-adobe-primetime-ext-x-cue)

## <a name="214-rtmp-ad-cue-signaling-with-onadcue---scte-35-mode"></a>2.1.4 Sinal de ad cue RTMP com "onAdCue" - Modo SCTE-35

Quando estiver a trabalhar com um fluxo de trabalho de produção de transmissão mais avançado que requer que a mensagem de carga útil completa scte-35 seja transportada para o manifesto HLS ou DASH, o melhor é utilizar o "Modo SCTE-35" da especificação [Adobe-Primetime].  Este modo suporta que os sinais SCTE-35 em banda sejam enviados diretamente para um codificadora ao vivo no local, que codifica os sinais para o fluxo RTMP utilizando o "Modo SCTE-35" especificado na especificação [Adobe-Primetime]. 

Normalmente, as mensagens SCTE-35 só podem aparecer nas entradas do fluxo de transporte MPEG-2 (TS) num codificadora no local. Consulte o fabricante do seu codificador para obter mais informações sobre como configurar uma ingestão de fluxo de transporte que contenha SCTE-35 e ative-o para passar para RTMP no modo Adobe SCTE-35.

Neste cenário, a seguinte carga útil DEVE ser enviada do codificadora no local utilizando o tipo de mensagem **"onAdCue"** [AMF0].

| Nome do Campo | Tipo de Campo | Necessário? | Descrições                                                                                                                                                                                                                                                                                                                                                                                                        |
| ---------- | ---------- | --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| deixa        | String     | Necessário  | A mensagem do evento.  Para as mensagens [SCTE-35], esta DEVE ser a base de 64-codificadas [RFC4648] binária splice_info_section() para que as mensagens sejam enviadas aos clientes HLS, Smooth e Dash.                                                                                                                                                                                                                               |
| tipo       | String     | Necessário  | Uma URN ou URL identificando o esquema de mensagem. Para as mensagens [SCTE-35], esta **deve** ser **"scte35"** para que as mensagens sejam enviadas aos clientes HLS, Smooth e Dash, em conformidade com [Adobe-Primetime]. Opcionalmente, a URN "urn:scte:scte35:2013:bin" também pode ser usada para sinalizar uma mensagem [SCTE-35].                                                                                                        |
| ID         | String     | Necessário  | Um identificador único descrevendo a emenda ou segmento. Identifica este caso da mensagem.  As mensagens com semântica equivalente devem ter o mesmo valor.                                                                                                                                                                                                                                                       |
| duration   | Número     | Necessário  | A duração do evento ou do segmento de ad, se conhecido. Se desconhecido, o valor **deve** ser 0.                                                                                                                                                                                                                                                                                                                    |
| elapsed    | Número     | Opcional  | Quando o sinal de anúncio [SCTE-35] estiver a ser repetido para sintonizar, este campo será a quantidade de tempo de apresentação decorrido desde o início da ligação. As unidades são segundos fracionais. No modo [SCTE-35], este valor pode exceder a duração original especificada da ligação ou segmento.                                                                                                                   |
| hora       | Número     | Necessário  | A hora de apresentação do evento ou ad splice.  O tempo e duração da apresentação **devem** alinhar-se com os Pontos de Acesso ao Fluxo (SAP) do tipo 1 ou 2, tal como definidos no anexo I [ISO-14496-12]. Para a saída do HLS, o tempo e a duração **devem** alinhar-se com os limites do segmento. O tempo de apresentação e a duração de diferentes mensagens de evento dentro do mesmo fluxo de eventos NÃO se sobrepõem. As unidades são segundos fracionais. |

---

<!---
#### Example MPEG DASH .mpd manifest with SCTE-35 mode
See [Section 3.3.3.2 example DASH manifest with SCTE-35](#3332-example-mpeg-dash-manifest-mpd-with-multi-period-eventstream-using-adobe-scte35-mode-signaling)
--->

#### <a name="example-hls-manifest-m3u8-with-scte-35-mode-signal"></a>Exemplo HLS manifesto .m3u8 com sinal de modo SCTE-35
Ver [secção 3.2.1.1 exemplo HLS manifesto com SCTE-35](#3211-example-hls-manifest-m3u8-showing-ext-x-cue-signaling-of-scte-35)



## <a name="215-rtmp-ad-signaling-with-oncuepoint-for-elemental-live"></a>2.1.5 Anúncio RTMP com "onCuePoint" para Elemental Live

O codificante Elemental Live on-in suporta marcadores de anúncios no sinal RTMP. A Azure Media Services suporta atualmente apenas o tipo de marcador de anúncios "onCuePoint" para RTMP.  Isto pode ser ativado nas Definições do Grupo Adobe RTMP nas definições de codificadores Elemental Media Live ou API, definindo o "**ad_markers**" para "onCuePoint".  Consulte a documentação Elemental Live para mais detalhes. Ao permitir que esta funcionalidade no Grupo RTMP passe os sinais SCTE-35 para as saídas do Adobe RTMP a serem processadas pela Azure Media Services.

O tipo de mensagem "onCuePoint" é definido em [Adobe-Flash-AS] e tem a seguinte estrutura de carga útil quando enviado a partir da saída Elemental Live RTMP.


| Propriedade   | Descrição                                                                                                                                                                                                                     |
| ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| name       | O nome deve ser '**scte35**' por Elemental Live.                                                                                                                                                                              |
| hora       | O tempo em segundos em que o ponto de deixa ocorreu no ficheiro de vídeo durante a linha do tempo                                                                                                                                           |
| tipo       | O tipo de ponto de sugestão deve ser definido para "**evento**".                                                                                                                                                                             |
| parâmetros | Uma matriz associativa de cordas de par de nome/valor contendo as informações da mensagem SCTE-35, incluindo ID e duração. Estes valores são analisados pela Azure Media Services e incluídos na etiqueta de decoração manifesto. |


Quando este modo de marcador de anúncios é utilizado, a saída manifesto HLS é semelhante ao modo Adobe "Simple".


#### <a name="example-mpeg-dash-mpd-single-period-adobe-simple-mode-signals"></a>Exemplo MPEG DASH MPD, período único, sinais de modo Adobe Simple

```xml
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
```

#### <a name="example-hls-playlist-adobe-simple-mode-signals-using-ext-x-cue-tag-truncated--for-brevity"></a>Exemplo lista de reprodução HLS, sinais de modo Adobe Simple usando tag EXT-X-CUE (truncado "..." para a brevidade)

O exemplo a seguir mostra a saída do pacote dinâmico dos Media Services para um fluxo de ingestão RTMP utilizando sinais de modo "simples" da Adobe e a etiqueta ext-X-CUE do legado [Adobe-Primetime].  

```
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
```

### <a name="216-cancellation-and-updates"></a>2.1.6 Cancelamentos e Atualizações

As mensagens podem ser canceladas ou atualizadas enviando várias mensagens com o mesmo tempo de apresentação e ID. O tempo de apresentação e o ID identificam exclusivamente o evento, e a última mensagem recebida para um tempo específico de apresentação que atenda às restrições pré-roll é a mensagem que é atuada. O evento atualizado substitui quaisquer mensagens previamente recebidas. A restrição pré-roll é de quatro segundos. As mensagens recebidas pelo menos quatro segundos antes do tempo de apresentação serão agidas.

## <a name="22-fragmented-mp4-ingest-smooth-streaming"></a>2.2 MP4 Ingestão fragmentada (Streaming suave)

Consulte [MS-SSTR-Ingest] para obter requisitos em live stream ingest. As seguintes secções fornecem detalhes sobre a ingestão de metadados de apresentação cronometrados.  Os metadados de apresentação cronometrados são ingeridos como uma faixa escassa, que é definida tanto na Caixa Manifesto do Servidor Ao Vivo (ver MS-SSTR) como na Movie Box ('moov').  

Cada fragmento escasso consiste numa Caixa de Fragmento de Filme ('moof') e na Media Data Box ('mdat'), onde a caixa 'mdat' é a mensagem binária.

Para conseguir a inserção precisa de quadros de anúncios, o codificadora MUST divide o fragmento no momento de apresentação onde a deixa é necessária para ser inserida.  Um novo fragmento DEVE ser criado que começa com uma moldura IDR recém-criada, ou Pontos de Acesso ao Fluxo (SAP) do tipo 1 ou 2, tal como definido no anexo I [ISO-14496-12].
<!--- This allows the Azure Media Packager to properly generate an HLS manifest and a DASH multi-period manifest where the new Period begins at the frame-accurate splice conditioned presentation time. --->

### <a name="221-live-server-manifest-box"></a>2.2.1 Caixa manifesto do servidor ao vivo

A faixa de sparse **MUST** deve ser declarada na caixa Live Server Manifest com uma **\<textstream\>** entrada e **DEVE** ter os seguintes atributos definidos:

| **Nome do atributo** | **Tipo de campo** | **Necessário?** | **Descrição**                                                                                                                                                                                                              |
| ------------------ | -------------- | ------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| sistemaAbitar      | Número         | Necessário      | **DEVE** ser "0", indicando uma faixa com bitrate desconhecido e variável.                                                                                                                                                          |
| nome parentTrackName    | String         | Necessário      | **DEVE** ser o nome da faixa-mãe, à qual os códigos de tempo de pista escassos estão alinhados. A pista dos pais não pode ser uma pista escassa.                                                                             |
| manifestoOutput     | Booleano        | Necessário      | **DEVE** ser "verdadeiro", para indicar que a pista escassa será incorporada no manifesto do cliente Smooth.                                                                                                                        |
| Subtipo            | String         | Necessário      | **DEVE** ser o código de quatro caracteres "DATA".                                                                                                                                                                                  |
| Scheme             | String         | Necessário      | **DEVE** ser uma URN ou URL que identifique o esquema de mensagem. Para as mensagens [SCTE-35], este **DEVE** ser "urn:scte:scte35:2013:bin" para que as mensagens sejam enviadas aos clientes HLS, Smooth e Dash em conformidade com [SCTE-35]. |
| trackName          | String         | Necessário      | **Deve** ser o nome da pista escassa. O trackName pode ser usado para diferenciar vários streams de eventos com o mesmo esquema. Cada fluxo de evento único **DEVE** ter um nome de pista único.                                |
| escala de tempo          | Número         | Opcional      | **Deve** ser o calendário da pista dos pais.                                                                                                                                                                               |

---

### <a name="222-movie-box"></a>2.2.2 Caixa de Filme

A Movie Box ('moov') segue a Caixa manifesto do servidor ao vivo como parte do cabeçalho de fluxo para uma faixa escassa.

A caixa 'moov' **DEVE** conter uma caixa **TrackHeaderBox ('tkhd')** tal como definida em [ISO-14496-12] com os seguintes constrangimentos:

| **Nome do campo** | **Tipo de campo**          | **Necessário?** | **Descrição**                                                                                                    |
| -------------- | ----------------------- | ------------- | ------------------------------------------------------------------------------------------------------------------ |
| duration       | 64 bits inteiro não assinado | Necessário      | **Deve** ser 0, uma vez que a caixa de pista tem zero amostras e a duração total das amostras na caixa de pista é 0. |

---

A caixa 'moov' **DEVE** conter uma **HandlerBox ('hdlr')** tal como definida em [ISO-14496-12] com os seguintes constrangimentos:

| **Nome do campo** | **Tipo de campo**          | **Necessário?** | **Descrição**       |
| -------------- | ----------------------- | ------------- | --------------------- |
| handler_type   | 32 bits inteiro não assinado | Necessário      | **Deve** ser "meta". |

---

A caixa 'stsd' **DEVE** conter uma caixa MetaDataSampleEntry com um nome de codificação tal como definido em [ISO-14496-12].  Por exemplo, para as mensagens SCTE-35 o nome de codificação **deve** ser 'scte'.

### <a name="223-movie-fragment-box-and-media-data-box"></a>2.2.3 Caixa de Fragmento de Filme e Caixa de Dados de Mídia

Fragmentos de faixas escassas consistem numa Caixa de Fragmento de Filme ('moof') e numa Caixa de Dados de Media ('mdat').

> [!NOTE]
> Para conseguir a inserção precisa de quadros de anúncios, o codificadora MUST divide o fragmento no momento de apresentação onde a deixa é necessária para ser inserida.  Um novo fragmento DEVE ser criado que começa com uma moldura IDR recém-criada, ou Pontos de Acesso ao Fluxo (SAP) do tipo 1 ou 2, tal como definido no anexo I [ISO-14496-12]
> 

A caixa MovieFragmentBox ('moof') **DEVE** conter uma caixa **TrackFragmentExtendedHeaderBox ('uuid')** tal como definido em [MS-SSTR] com os seguintes campos:

| **Nome do campo**         | **Tipo de campo**          | **Necessário?** | **Descrição**                                                                                           |
| ---------------------- | ----------------------- | ------------- | --------------------------------------------------------------------------------------------------------- |
| fragment_absolute_time | 64 bits inteiro não assinado | Necessário      | **Deve** ser a hora de chegada do evento. Este valor alinha a mensagem com a faixa dos pais.           |
| fragment_duration      | 64 bits inteiro não assinado | Necessário      | **DEVE** ser a duração do evento. A duração pode ser zero para indicar que a duração é desconhecida. |

---


A caixa MediaDataBox ('mdat') **TEM** o seguinte formato:

| **Nome do campo**          | **Tipo de campo**                   | **Necessário?** | **Descrição**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ----------------------- | -------------------------------- | ------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| versão                 | 32 bits inteiro não assinado (uimsbf) | Necessário      | Determina o formato do conteúdo da caixa 'mdat'. Versões não reconhecidas serão ignoradas. Atualmente, a única versão suportada é 1.                                                                                                                                                                                                                                                                                                                                                                      |
| ID                      | 32 bits inteiro não assinado (uimsbf) | Necessário      | Identifica este caso da mensagem. As mensagens com semântica equivalente devem ter o mesmo valor; isto é, processar qualquer caixa de mensagens de evento com o mesmo id é suficiente.                                                                                                                                                                                                                                                                                                                            |
| presentation_time_delta | 32 bits inteiro não assinado (uimsbf) | Necessário      | A soma do fragment_absolute_time, especificada na TrackFragmentExtendedHeaderBox, e o presentation_time_delta **MUST** é o tempo de apresentação do evento. O tempo e duração da apresentação **devem** alinhar-se com os Pontos de Acesso ao Fluxo (SAP) do tipo 1 ou 2, tal como definidos no anexo I [ISO-14496-12]. Para a saída do HLS, o tempo e a duração **devem** alinhar-se com os limites do segmento. O tempo de apresentação e a duração de diferentes mensagens de evento dentro do mesmo fluxo de **eventos NÃO** se sobrepõem. |
| message                 | matriz de bytes                       | Necessário      | A mensagem do evento. Para as mensagens [SCTE-35], a mensagem é a splice_info_section binário(). Para as mensagens [SCTE-35], este **DEVE** ser o splice_info_section() a fim de que as mensagens sejam enviadas aos clientes HLS, Smooth e Dash em conformidade com [SCTE-35]. Para as mensagens [SCTE-35], o splice_info_section binário é a carga útil da caixa 'mdat', e **NÃO** está codificada.                                                                                                                     |

---


### <a name="224-cancellation-and-updates"></a>2.2.4 Cancelamentos e Atualizações

As mensagens podem ser canceladas ou atualizadas enviando várias mensagens com o mesmo tempo de apresentação e ID.  O tempo de apresentação e o ID identificam exclusivamente o evento. A última mensagem recebida por um tempo específico de apresentação, que satisfaz as restrições pré-roll, é a mensagem que é atuada. A mensagem atualizada substitui quaisquer mensagens previamente recebidas.  A restrição pré-roll é de quatro segundos. As mensagens recebidas pelo menos quatro segundos antes do tempo de apresentação serão agidas. 


## <a name="3-timed-metadata-delivery"></a>3 Entrega de metadados cronometrados

Os dados de fluxo de eventos são opacos para os Media Services. Os Serviços de Comunicação Social apenas passam três informações entre o ponto final do ingestão e o ponto final do cliente. As seguintes propriedades são entregues ao cliente, em conformidade com [SCTE-35] e/ou o protocolo de streaming do cliente:

1.  Scheme – uma URN ou URL que identifica o esquema da mensagem.
2.  Hora da Apresentação – o tempo de apresentação do evento na cronologia dos media.
3.  Duração – duração do evento.
4.  ID – um identificador exclusivo opcional para o evento.
5.  Mensagem – os dados do evento.

## <a name="31-microsoft-smooth-streaming-manifest"></a>3.1 Microsoft Smooth Streaming Manifest  

Consulte o manuseamento de faixas escassas [MS-SSTR] para obter detalhes sobre como formatar uma faixa de mensagem escassa. Para as mensagens [SCTE35], o Smooth Streaming irá transformar a splice_info_section codificada com base64 num fragmento escasso.
O StreamIndex **DEVE** ter um subtipo de "DATA", e os CustomAttributes **DEVEM** conter um Atributo com Nome="Schema" e Value="urn:scte:scte35:2013:bin".

#### <a name="smooth-client-manifest-example-showing-base64-encoded-scte35-splice_info_section"></a>Exemplo de manifesto de cliente liso mostrando base64 codificado [SCTE35] splice_info_section()
```xml
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
```

## <a name="32-apple-hls-manifest-decoration"></a>3.2 Decoração manifesto Apple HLS

A Azure Media Services suporta as seguintes tags manifesto HLS para sinalizar informações de ad-ad durante um evento ao vivo ou a pedido. 

<!--- EXT-X-DATERANGE as defined in Apple HLS [RFC8216] --->
- EXT-X-CUE como definido em [Adobe-Primetime]
<!--- this mode is considered "legacy".  Customers should adopt the EXT-X-DATERANGE tag when possible. --->

A saída de dados para cada tag variará em função do modo de sinal de ingestão utilizado. Por exemplo, a ingestão de RTMP com o modo Adobe Simple não contém a carga completa codificada scte-35 base64.

<!---
## 3.2.1 Apple HLS with EXT-X-DATERANGE (recommended)

The Apple HTTP Live Streaming [RFC8216] specification allows for signaling of [SCTE-35] messages. The messages are inserted into the segment playlist in an EXT-X-DATERANGE tag per [RFC8216] section titled "Mapping SCTE-35 into EXT-X-DATERANGE".  The client application layer can parse the M3U playlist and process M3U tags, or receive the events through the Apple player framework.  

The **RECOMMENDED** approach in Azure Media Services (version 3 API) is to follow [RFC8216] and use the EXT-X_DATERANGE tag for [SCTE35] ad avail decoration in the manifest.
--->


## <a name="3211-example-hls-manifest-m3u8-showing-ext-x-cue-signaling-of-scte-35"></a>3.2.1.1 Exemplo Manifesto HLS .m3u8 mostrando a sinalização EXT-X-CUE de SCTE-35

O exemplo seguinte, a produção manifesta do pacote dinâmico dos Media Services mostra a etiqueta EXT-X-CUE para [Adobe-Primetime] no modo SCTE35. 

```
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
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=0.000022
#EXTINF:0.250244,no-desc
Fragments(video=23355833,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=0.250267
#EXTINF:0.850856,no-desc
Fragments(video=23378355,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.101122
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=0.000000,TIME=260.610344,CUE="/DAgAAAAAAXdAP/wDwUAAAPqf0/+AWXk0wABAQEAAGB86Fo="
#EXTINF:0.650644,no-desc
Fragments(video=23454932,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.751767
#EXTINF:0.050044,no-desc
Fragments(video=23513490,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.801811
#EXTINF:1.451456,no-desc
Fragments(video=23517994,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=3.253267
#EXTINF:1.501500,no-desc
Fragments(video=23648625,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=4.754767
#EXTINF:1.501500,no-desc
Fragments(video=23783760,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=6.256267
#EXTINF:1.501500,no-desc
Fragments(video=23918895,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=7.757767
#EXTINF:1.501500,no-desc
Fragments(video=24054030,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=9.259267
#EXTINF:1.501500,no-desc
Fragments(video=24189165,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=10.760767
#EXTINF:1.501500,no-desc
Fragments(video=24324300,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=12.262267
#EXTINF:1.501500,no-desc
Fragments(video=24459435,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=13.763767
#EXTINF:1.501500,no-desc
Fragments(video=24594570,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=15.265267
#EXTINF:1.501500,no-desc
Fragments(video=24729705,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=16.766767
#EXTINF:1.501500,no-desc
Fragments(video=24864840,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=18.268267
#EXTINF:1.501500,no-desc
Fragments(video=24999975,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=19.769767
#EXTINF:1.501500,no-desc
Fragments(video=25135110,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=21.271267
#EXTINF:1.501500,no-desc
Fragments(video=25270245,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=22.772767
#EXTINF:1.501500,no-desc
Fragments(video=25405380,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=24.274267
#EXTINF:1.501500,no-desc
Fragments(video=25540515,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=25.775767
#EXTINF:1.501500,no-desc
Fragments(video=25675650,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=27.277267
#EXTINF:1.501500,no-desc
Fragments(video=25810785,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=28.778767
#EXTINF:1.501500,no-desc
Fragments(video=25945920,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=30.280267
#EXTINF:1.501500,no-desc
Fragments(video=26081055,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=31.781767
#EXTINF:1.501500,no-desc
Fragments(video=26216190,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=33.283267
#EXTINF:1.501500,no-desc
Fragments(video=26351325,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=34.784767
#EXTINF:1.501500,no-desc
Fragments(video=26486460,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=36.286267
#EXTINF:1.501500,no-desc
Fragments(video=26621595,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=37.787767
#EXTINF:1.501500,no-desc
Fragments(video=26756730,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=39.289267
#EXTINF:1.501500,no-desc
Fragments(video=26891865,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=40.790767
#EXTINF:1.501500,no-desc
Fragments(video=27027000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=42.292267
#EXTINF:1.501500,no-desc
Fragments(video=27162135,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=43.793767
#EXTINF:1.501500,no-desc
Fragments(video=27297270,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=45.295267
#EXTINF:1.501500,no-desc
Fragments(video=27432405,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=46.796767
#EXTINF:1.501500,no-desc
Fragments(video=27567540,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=48.298267
#EXTINF:1.501500,no-desc
Fragments(video=27702675,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=49.799767
#EXTINF:1.501500,no-desc
Fragments(video=27837810,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=51.301267
#EXTINF:1.501500,no-desc
Fragments(video=27972945,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=52.802767
#EXTINF:1.501500,no-desc
Fragments(video=28108080,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=54.304267
#EXTINF:1.501500,no-desc
Fragments(video=28243215,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=55.805767
#EXTINF:1.501500,no-desc
Fragments(video=28378350,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=57.307267
#EXTINF:1.501500,no-desc
Fragments(video=28513485,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=58.808767
#EXTINF:1.501500,no-desc
Fragments(video=28648620,format=m3u8-aapl-v8)

```


<!---
THIS VERSION HAS THE HLSv8 DATERANGE Tags in it
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

--->

## <a name="322-apple-hls-with-adobe-primetime-ext-x-cue"></a>3.2.2 Apple HLS com Adobe Primetime EXT-X-CUE

Os Serviços de Mídia (versão 2 e 3 API) suportam a saída da etiqueta EXT-X-CUE, tal como definida no modo SCTE-35 [Adobe-Primetime]. Neste modo, a Azure Media Services incorporará splice_info_section codificados com base64 [SCTE-35) na etiqueta EXT-X-CUE.  

A etiqueta "legado" EXT-X-CUE é definida como abaixo e também pode ser referenciada normativa na especificação [Adobe-Primetime]. Isto só deve ser utilizado para a sinalização SCTE35, sempre que necessário, caso contrário a etiqueta recomendada é definida em [RFC8216] como EXT-X-DATERANGE. 

| **Nome do atributo** | **Tipo**                      | **Necessário?**                             | **Descrição**                                                                                                                                                                                                                                                                          |
| ------------------ | ----------------------------- | ----------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| CUE                | corda citada                 | Necessário                                  | A mensagem codificada como uma cadeia codificada de base64, tal como descrita em [RFC4648]. Para as mensagens [SCTE-35], esta é a base de 64 splice_info_section codificada().                                                                                                                                      |
| TIPO               | corda citada                 | Necessário                                  | Uma URN ou URL identificando o esquema de mensagem. Para as mensagens [SCTE-35], o tipo leva o valor especial "scte35".                                                                                                                                                                          |
| ID                 | corda citada                 | Necessário                                  | Um identificador único para o evento. Se o ID não for especificado quando a mensagem for ingerida, a Azure Media Services gerará um id único.                                                                                                                                              |
| DURATION           | número de ponto flutuante decimal | Necessário                                  | A duração do evento. Se desconhecido, o valor **deve** ser 0. As unidades são segundos de fação.                                                                                                                                                                                           |
| DECORRIDO            | número de ponto flutuante decimal | Opcional, mas requerido para janela deslizante | Quando o sinal está a ser repetido para suportar uma janela de apresentação deslizante, este campo **DEVE** ser a quantidade de tempo de apresentação que tem decorrido desde o início do evento. As unidades são segundos fracionais. Este valor pode exceder a duração original especificada da liga ou segmento. |
| TIME               | número de ponto flutuante decimal | Necessário                                  | A hora de apresentação do evento. As unidades são segundos fracionais.                                                                                                                                                                                                                        |

A camada de aplicação do leitor HLS utilizará o TYPE para identificar o formato da mensagem, descodificar a mensagem, aplicar as conversões de tempo necessárias e processar o evento.  Os eventos são sincronizados no tempo na lista de resumos do segmento da faixa dos pais, de acordo com o cronografo do evento.  São inseridos antes do segmento mais próximo (#EXTINF etiqueta).


### <a name="323-hls-m3u8-manifest-example-using-adobe-primetime-ext-x-cue"></a>3.2.3 HLS .m3u8 exemplo manifesto usando Adobe Primetime EXT-X-CUE

O exemplo a seguir mostra a decoração manifesto HLS utilizando a etiqueta Adobe Primetime EXT-X-CUE.  O parâmetro "CUE" contém apenas as propriedades TYPE e Duração, o que significa que esta era uma fonte RTMP utilizando a sinalização "simples" do modo Adobe.  
<!---If this was a SCTE-35 mode signal, the tag would include the base64 encoded binary SCTE-35 payload as seen in the [3.2.1.1 example](#3211-example-hls-manifest-m3u8-showing-ext-x-daterange-signaling-of-scte-35).
--->


```
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

```

### <a name="324-hls-message-handling-for-adobe-primetime-ext-x-cue"></a>3.2.4 Tratamento de mensagens HLS para Adobe Primetime EXT-X-CUE

Os eventos são sinalizados na lista de resumos de cada vídeo e faixa áudio. A posição da tag EXT-X-CUE **DEVE** ser sempre imediatamente antes do primeiro segmento HLS (para o arranque do segmento) ou imediatamente após o último segmento HLS (para a ligação no segmento ou final do segmento) a que os seus atributos TIME e DURAÇÃO se referem, conforme exigido por [Adobe-Primetime].

Quando uma janela de apresentação deslizante está ativada, a etiqueta EXT-X-CUE **DEVE** ser repetida frequentemente o suficiente para que a ligação ou segmento esteja sempre totalmente descrito na lista de reprodução do segmento, e o atributo ELAPSED **DEVE** ser utilizado para indicar a quantidade de tempo em que a ligação ou segmento esteve ativa, conforme exigido por [Adobe-Primetime].

Quando uma janela de apresentação deslizante está ativada, as tags EXT-X-CUE são removidas da lista de reprodução do segmento quando a hora de mídia a que se referem ter sido lançada da janela de apresentação deslizante.

## <a name="33-dash-manifest-decoration-mpd"></a>3.3 Dash Manifesta decoração (MPD)

[MPEGDASH] fornece três formas de sinalizar eventos:

1.  Eventos assinalados no MPD EventStream
2.  Eventos sinalizados na banda usando a Caixa de Mensagens de Evento ('emsg')
3.  Uma combinação de 1 e 2

Os eventos sinalizados no MPD EventStream são úteis para o streaming de VOD porque os clientes têm acesso a todos os eventos, imediatamente quando o MPD é descarregado. Também é útil para a sinalização SSAI, onde o fornecedor de SSAI a jusante precisa analisar os sinais do manifesto mpd, e inserir o conteúdo de anúncios dinamicamente.  A solução in-band ('emsg')é útil para o streaming ao vivo onde os clientes não precisam de descarregar o MPD novamente, ou não há nenhuma manipulação manifesto SSAI acontecendo entre o cliente e a origem. 

O comportamento padrão do Azure Media Services para o DASH é sinalizar tanto no MPD EventStream como na banda utilizando a Caixa de Mensagens de Evento ('emsg').

As mensagens de sugestão ingeridas sobre [RTMP] ou [MS-SSTR-Ingest] são mapeadas em eventos DASH, utilizando caixas 'emsg' em banda e/ou em-MPD EventStreams. 

A sinalização em banda SCTE-35 para o DASH segue a definição e os requisitos definidos em [SCTE-214-3] e também na secção [DASH-IF-IOP] 13.12.2 ('EVENTOS SCTE35'). 

Para o transporte em banda [SCTE-35], a caixa de mensagem de evento ('emsg') utiliza o esquemaId = "urn:scte:scte35:2013:bin". Para a decoração manifesto MPD, o esquema EventStreamId utiliza "urn:scte:scte35:2014:xml+bin".  Este formato é uma representação XML do evento que inclui uma saída binária de base64 codificada da mensagem completa SCTE-35 que chegou à ingestão. 

As definições normativas de referência do transporte de mensagens de deixa [SCTE-35] no DASH estão disponíveis em [SCTE-214-1] sec 6.7.4 (MPD) e [SCTE-214-3] sec 7.3.2 (Transporte de mensagens de sugestão SCTE 35).

### <a name="331-mpeg-dash-mpd-eventstream-signaling"></a>3.3.1 MPEG DASH (MPD) Sinalização EventStream

A decoração manifesta (MPD) de eventos será sinalizada no MPD utilizando o elemento EventStream, que aparece dentro do elemento Period. O esquemaId usado é "urna:scte:scte35:2014:xml+bin".


> [!NOTE]
> Para efeitos de brevidade [SCTE-35] permite a utilização da secção codificada de base64 em signal.binary element (em vez do elemento Signal.SpliceInfoSection) como alternativa ao transporte de uma mensagem de sugestão completamente analisada.
> A Azure Media Services utiliza esta abordagem 'xml+bin' para a sinalização no manifesto mpd.
> Este é também o método recomendado utilizado na secção [DASH-IF-IOP] - ver secção intitulada ["Fluxos de eventos de inserção de anúncios" da orientação DO DASH IF IOP](https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html#ads-insertion-event-streams)
> 


O elemento EventStream tem os seguintes atributos:

| **Nome do atributo** | **Tipo**                | **Necessário?** | **Descrição**                                                                                                                                                                                                                                                                                                                                                                         |
| ------------------ | ----------------------- | ------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| scheme_id_uri      | string                  | Necessário      | Identifica o esquema da mensagem. O esquema está definido para o valor do atributo Esquema na caixa Live Server Manifesto. O valor **deve** ser uma URN ou URL que identifique o esquema de mensagens; O esquema de saída suportadoId deve ser "urn:scte:scte35:2014:xml+bin" per [SCTE-214-1] sec 6.7.4 (MPD), uma vez que o serviço suporta apenas "xml+bin" neste momento para a brevidade no MPD. |
| valor              | string                  | Opcional      | Um valor adicional de corda usado pelos proprietários do esquema para personalizar a semântica da mensagem. De forma a diferenciar vários fluxos de eventos com o mesmo esquema, o valor **DEVE** ser definido para o nome do stream de eventos (trackName for [MS-SSTR-Ingest] ou nome de mensagem AMF para [RTMP] ingestão).                                                                         |
| Escala temporal          | 32 bits inteiro não assinado | Necessário      | O calendário, em tiques por segundo.                                                                                                                                                                                                                                                                                                                                                     |


### <a name="332-example-event-streams-for-mpeg-dash"></a>3.3.2 Exemplo Fluxos de eventos para MPEG DASH

#### <a name="3321-example-mpeg-dash-mpd-manifest-signaling-of-rtmp-streaming-using-adobe-simple-mode"></a>3.3.2.1 Exemplo MPEG DASH .mpd sinalização manifesto de streaming RTMP usando o modo simples Adobe

O exemplo a seguir mostra um excerto EventStream do pacote dinâmico dos Media Services para um fluxo RTMP utilizando a sinalização "simples" do modo Adobe.

```xml
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
```


#### <a name="3322-example-mpeg-dash-mpd-manifest-signaling-of-an-rtmp-stream-using-adobe-scte-35-mode"></a>3.3.2.2 Exemplo MPEG DASH .mpd sinalização manifesto de um fluxo RTMP utilizando o modo Adobe SCTE-35

O exemplo a seguir mostra um excerto EventStream do pacote dinâmico dos Media Services para um fluxo RTMP utilizando a sinalização do modo Adobe SCTE-35.

Exemplo Elemento EventStream usando a sinalização de estilo xml+bin por [SCTE-214-1]

```xml

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
```



> [!IMPORTANT]
> Note que a apresentaçãoTime é o tempo de apresentação do evento [SCTE-35] traduzido para ser relativo à hora de início do período, não à hora de chegada da mensagem.
> [MPEGDASH] define o Event@presentationTime como "Especifica o tempo de apresentação do evento em relação ao início do Período.
> O valor do tempo de apresentação em segundos é a divisão do valor deste atributo e o valor do EventStream@timescale atributo.
> Se não estiver presente, o valor do tempo de apresentação é 0.


#### <a name="3331-example-mpeg-dash-manifest-mpd-with-single-period-eventstream-using-adobe-simple-mode-signals"></a>3.3.3.1 Exemplo MPEG DASH manifesto (MPD) com um período único, EventStream, utilizando sinais de modo simples adobe

O exemplo a seguir mostra a saída do pacote dinâmico dos Media Services para um fluxo RTMP de origem utilizando o método de sinal de anúncio "simples" do modo Adobe. A saída é um manifesto de período único que mostra um EventStream usando o esquemaId Uri definido para "urn:com:adobe:dpi:simple:2015" e valor de propriedade definida para "simplesignal".
Cada sinal simples é fornecido num elemento do Evento com o @presentationTime @duration , e propriedades @id povoadas com base nos sinais simples de entrada.

```xml
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

```

<!---
#### 3.3.3.2 Example MPEG DASH manifest (MPD) with multi-period, EventStream, using Adobe SCTE35 mode signaling

The following example shows the output from the Media Services dynamic packager for a source RTMP stream using the Adobe SCTE35 mode signaling.
In this case, the output manifest is a multi-period DASH .mpd with an EventStream element, and @schemeIdUri property set to "urn:scte:scte35:2014:xml+bin" and a @value property set to "scte35". Each Event element in the EventStream contains the full base64 encoded binary SCTE35 signal 

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

--->

### <a name="334-mpeg-dash-in-band-event-message-box-signaling"></a>3.3.4 MPEG DASH In-band Event Message Box Signaling

Um fluxo de eventos em banda requer que o MPD tenha um elemento InbandEventStream ao nível de Conjunto de Adaptação.  Este elemento tem um atributo esquema obrigatórioIdUri e atributo de calendário opcional, que também aparecem na Caixa de Mensagens de Evento ('emsg').  As caixas de mensagens de evento com identificadores de esquema que não estejam definidas no MPD não **devem** estar presentes.

Para o transporte em banda [SCTE-35], os sinais **DEVEM** utilizar o esquemaId = "urn:scte:scte35:2013:bin".
As definições normativas de transporte de mensagens em banda [SCTE-35] são definidas em [SCTE-214-3] sec 7.3.2 (Transporte de mensagens de sugestão SCTE 35).

Os seguintes detalhes descrevem os valores específicos que o cliente deve esperar no 'emsg' em conformidade com [SCTE-214-3]:

| **Nome do campo**          | **Tipo de campo**          | **Necessário?** | **Descrição**                                                                                                                                                                                                                                                                                        |
| ----------------------- | ----------------------- | ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| scheme_id_uri           | string                  | Necessário      | Identifica o esquema da mensagem. O esquema está definido para o valor do atributo Esquema na caixa Live Server Manifesto. O valor **DEVE** ser uma URN que identifica o esquema de mensagens. Para as mensagens [SCTE-35], esta **DEVE** ser "urn:scte:scte35:2013:bin" em conformidade com [SCTE-214-3]          |
| Valor                   | string                  | Necessário      | Um valor adicional de corda usado pelos proprietários do esquema para personalizar a semântica da mensagem. De forma a diferenciar vários streams de eventos com o mesmo esquema, o valor será definido para o nome do stream do evento (trackName for Smooth ingest ou nome de mensagem AMF para ingestão DE RTMP). |
| Escala temporal               | 32 bits inteiro não assinado | Necessário      | O calendário, em tiques por segundo, dos campos de tempos e de duração dentro da caixa 'emsg'.                                                                                                                                                                                                            |
| Presentation_time_delta | 32 bits inteiro não assinado | Necessário      | O tempo de apresentação mediática delta do tempo de apresentação do evento e o primeiro tempo de apresentação neste segmento. O tempo e duração da apresentação **devem** alinhar-se com os Pontos de Acesso ao Fluxo (SAP) do tipo 1 ou 2, tal como definidos no anexo I [ISO-14496-12].                                  |
| event_duration          | 32 bits inteiro não assinado | Necessário      | A duração do evento, ou 0xFFFFFFFF indicar uma duração desconhecida.                                                                                                                                                                                                                              |
| Id                      | 32 bits inteiro não assinado | Necessário      | Identifica este caso da mensagem. As mensagens com semântica equivalente devem ter o mesmo valor. Se o ID não for especificado quando a mensagem for ingerida, a Azure Media Services gerará um id único.                                                                                        |
| Message_data            | matriz de bytes              | Necessário      | A mensagem do evento. Para as mensagens [SCTE-35], os dados da mensagem são o splice_info_section binário() em conformidade com [SCTE-214-3]                                                                                                                                                                        |


#### <a name="example-inbandevenstream-entity-for-adobe-simple-mode"></a>Exemplo InBandEvenStream entidade para o modo Adobe Simple
```xml

      <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
```

### <a name="335-dash-message-handling"></a>3.3.5 Tratamento de Mensagens DASH

Os eventos são sinalizados em banda, dentro da caixa 'emsg', tanto para faixas de vídeo como áudio.  A sinalização ocorre para todos os pedidos de segmento para os quais o presentation_time_delta é de 15 segundos ou menos. 

Quando uma janela de apresentação deslizante é ativada, as mensagens de evento são removidas do MPD quando a soma da hora e duração da mensagem do evento é inferior à hora dos dados dos meios de comunicação no manifesto.  Por outras palavras, as mensagens do evento são removidas do manifesto quando o tempo de comunicação a que se referem saiu da janela de apresentação deslizante.

## <a name="4-scte-35-ingest-implementation-guidance-for-encoder-vendors"></a>4. SCTE-35 Ingesti orientação de implementação para fornecedores de codificadores

As seguintes orientações são questões comuns que podem ter impacto na implementação desta especificação por parte de um vendedor de codificadores.  As diretrizes abaixo foram recolhidas com base no feedback dos parceiros do mundo real para facilitar a implementação desta especificação para outros. 

As mensagens [SCTE-35] são ingeridas em formato binário utilizando o Esquema **"urn:scte:scte35:2013:bin"** para [MS-SSTR-Ingest] e o tipo **"scte35"** para a ingestão [RTMP]. Para facilitar a conversão do tempo [SCTE-35], que se baseia nos selos de tempo de apresentação do fluxo de transporte MPEG-2 (PTS), um mapeamento entre PTS (pts_time + pts_adjustment do splice_time()) e o cronograma mediático é fornecido pelo tempo de apresentação do evento (o campo fragment_absolute_time para ingestão suave e o campo de tempo para ingestão de RTMP). O mapeamento é necessário porque o valor de PTS de 33 bits rola aproximadamente a cada 26,5 horas.

O Smooth Streaming ingest [MS-SSTR-Ingest] requer que a Caixa  de Dados de Mídia ('mdat') contenha o **splice_info_section()** definido em [SCTE-35]. 

Para a ingestão de RTMP, o atributo de sugestão da mensagem AMF é definido para o splice_info_section codificado de **base64()** definido em [SCTE-35].  


Quando as mensagens têm o formato acima descrito, são enviadas para clientes HLS, Smooth e DASH, conforme definido acima.  

Ao testar a sua implementação com a plataforma Azure Media Services, comece a testar com um LiveEvent "pass-through" primeiro, antes de passar a testar num LiveEvent codificante.

---

## <a name="change-history"></a>Histórico de Alterações

| Data     | Alterações                                                                                                             |
| -------- | ------------------------------------------------------------------------------------------------------------------- |
| 07/2/19  | Suporte rtmp ingerido revisto, acrescentou RTMP "onCuePoint" para Elemental Live                                            |
| 08/22/19 | Atualizado para adicionar OnUserDataEvent ao RTMP para metadados personalizados                                                          |
| 1/08/20  | Erro fixo no modo RTMP Simple e RTMP SCTE35. Passou de "onCuePoint" para "onAdCue". Tabela de modo simples atualizada. |
| 08/4/20  | Suporte removido para tag DATERANGE para corresponder à implementação no serviço de produção.    |

## <a name="next-steps"></a>Passos seguintes
Ver caminhos de aprendizagem dos Serviços de Comunicação Social.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
