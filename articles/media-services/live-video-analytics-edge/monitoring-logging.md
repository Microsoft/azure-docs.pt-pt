---
title: Monitorização e exploração madeireira - Azure
description: Este artigo fornece uma visão geral do Live Video Analytics na monitorização e registo de IoT Edge.
ms.topic: reference
ms.date: 04/27/2020
ms.openlocfilehash: 807b0623159e0b50285b89da2835e9dd6cb037aa
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/01/2020
ms.locfileid: "84261213"
---
# <a name="monitoring-and-logging"></a>Monitorização e registos

Neste artigo, você vai aprender sobre como você pode receber eventos do live video analytics no módulo IoT Edge para monitorização remota. 

Também aprenderá como pode controlar os registos que o módulo gera.

## <a name="taxonomy-of-events"></a>Taxonomia dos acontecimentos

Live Video Analytics on IoT Edge emite eventos ou dados de telemetria de acordo com a taxonomia seguinte.

![Análise de vídeo ao vivo no esquema de telemetria IoT Edge](./media/telemetry-schema/taxonomy.png)

* Operacionais: eventos que são gerados no âmbito de ações tomadas por um utilizador, ou durante a execução de um [gráfico de mídia](media-graph-concept.md).
   
   * Volume: espera-se que seja baixo (algumas vezes por minuto, ou mesmo uma taxa mais baixa).
   * Exemplos:

      A gravação começou (abaixo), a gravação parou
      
      ```
      {
        "body": {
          "outputType": "assetName",
          "outputLocation": "sampleAssetFromEVR-LVAEdge-20200512T233309Z"
        },
        "applicationProperties": {
          "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
          "subject": "/graphInstances/Sample-Graph-2/sinks/assetSink",
          "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
          "eventTime": "2020-05-12T23:33:10.392Z",
          "dataVersion": "1.0"
        }
      }
      ```
* Diagnósticos: eventos que ajudam a diagnosticar problemas e/ou problemas com o desempenho.

   * Volume: pode ser alto (várias vezes por minuto).
   * Exemplos:
   
      Informações [rtsp SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) (abaixo), ou lacunas no feed de vídeo de entrada.

      ```
      {
        "body": {
          "sdp": "SDP:\nv=0\r\no=- 1589326384077235 1 IN IP4 XXX.XX.XX.XXX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/lots_015.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-73.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/lots_015.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=640028;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
        },
        "applicationProperties": {
          "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
          "subject": "/graphInstances/Sample-Graph-2/sources/rtspSource",
          "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
          "eventTime": "2020-05-12T23:33:04.077Z",
          "dataVersion": "1.0"
        }
      }
      ```
* Analytics: eventos que são gerados como parte da análise de vídeo.

   * Volume: pode ser alto (várias vezes por minuto ou mais frequentemente).
   * Exemplos:
      
      Movimento detetado (abaixo), Resultado da inferência.
   ```      
   {
     "body": {
       "timestamp": 143039375044290,
       "inferences": [
         {
           "type": "motion",
           "motion": {
             "box": {
               "l": 0.48954,
               "t": 0.140741,
               "w": 0.075,
               "h": 0.058824
             }
           }
         }
       ]
     },
     "applicationProperties": {
       "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
       "subject": "/graphInstances/Sample-Graph-2/processors/md",
       "eventType": "Microsoft.Media.Graph.Analytics.Inference",
       "eventTime": "2020-05-12T23:33:09.381Z",
       "dataVersion": "1.0"
     }
   }
   ```
Os eventos emitidos pelo módulo são enviados para o [IoT Edge Hub,](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime#iot-edge-hub)e a partir daí pode ser encaminhado para outros destinos. 

## <a name="controlling-events"></a>Controlar eventos

Pode utilizar as seguintes propriedades gémeas do módulo, como documentado no [esquema JSON do módulo gémeo,](module-twin-configuration-schema.md)para controlar os eventos operacionais e de diagnóstico que são publicados pelo live video analytics no módulo IoT Edge.

`diagnosticsEventsOutputName`– incluir e fornecer (qualquer) valor para esta propriedade, de forma a obter eventos de diagnóstico a partir do módulo. Omita-o, ou deixe-o vazio para impedir que o módulo publique eventos de diagnóstico.
   
`operationalEventsOutputName`– incluir e fornecer (qualquer) valor para este imóvel, de forma a obter eventos operacionais a partir do módulo. Omita-o, ou deixe-o vazio para impedir que o módulo publique eventos operacionais.
   
Os eventos de análise são gerados por nós como o processador de deteção de movimentos, ou o processador de extensão HTTP, e a pia do hub IoT é usada para enviá-los para o IoT Edge Hub. 

Pode controlar o [encaminhamento de todos os eventos acima referidos](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes) através de uma propriedade desejada do módulo $edgeHub twin (no manifesto de implantação):

```
 "$edgeHub": {
   "properties.desired": {
     "schemaVersion": "1.0",
     "routes": {
       "moduleToHub": "FROM /messages/modules/lvaEdge/outputs/* INTO $upstream"
     },
     "storeAndForwardConfiguration": {
       "timeToLiveSecs": 7200
     }
   }
 }
```

No acima, lvaEdge é o nome para o Live Video Analytics no módulo IoT Edge, e a regra de encaminhamento segue o esquema definido nas [rotas de declaração](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes).

> [!NOTE]
> Para garantir que os eventos de análise cheguem ao IoT Edge Hub, é necessário que exista um nó de afundanço do hub IoT a jusante de qualquer nó do processador de deteção de movimento e/ou qualquer nó do processador de extensão HTTP.

## <a name="event-schema"></a>Esquema de eventos

Os eventos têm origem no dispositivo Edge, e podem ser consumidos na Borda ou na nuvem. Os eventos gerados pelo Live Video Analytics no IoT Edge estão em conformidade com o [padrão de mensagens de streaming](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct) estabelecido pelo Azure IoT Hub, com propriedades do sistema, propriedades de aplicações e um corpo.

### <a name="summary"></a>Resumo

Cada evento, quando observado através do IoT Hub, terá um conjunto de propriedades comuns como descrito abaixo.

|Propriedade   |Tipo de Propriedade| Tipo de Dados   |Descrição|
|---|---|---|---|
|mensagem id |sistema |guid|  Identificação única do evento.|
|tópico| aplicaçãoProperty |string|    Caminho do Gestor de Recursos Azure para a conta dos Serviços de Comunicação Social.|
|Assunto|   aplicaçãoProperty |string|    Sub-caminho para a entidade que emite o evento.|
|eventTime| aplicaçãoProperty|    string| Hora do evento ser gerado.|
|eventType| aplicaçãoProperty |string|    Identificador de tipo de evento (ver abaixo).|
|body|body  |objeto|    Dados particulares do evento.|
|dataVersion    |aplicaçãoProperty|   string  |{Major}. {Menor}|

### <a name="properties"></a>Propriedades

#### <a name="message-id"></a>mensagem id

Identificador globalmente único (GUID)

#### <a name="topic"></a>tópico

Representa a conta Azure Media Service associada ao gráfico.

`/subscriptions/{subId}/resourceGroups/{rgName}/providers/Microsoft.Media/mediaServices/{accountName}`

#### <a name="subject"></a>Assunto

Entidade que está a emitir o evento:

`/graphInstances/{graphInstanceName}`<br/>
`/graphInstances/{graphInstanceName}/sources/{sourceName}`<br/>
`/graphInstances/{graphInstanceName}/processors/{processorName}`<br/>
`/graphInstances/{graphInstanceName}/sinks/{sinkName}`

A propriedade do sujeito permite que eventos genéricos sejam mapeados para o seu módulo gerador. Por exemplo, em caso de nome de utilizador ou palavra-passe RTSP inválido, o evento gerado estaria `Microsoft.Media.Graph.Diagnostics.ProtocolError` no `/graphInstances/myGraph/sources/myRtspSource` nó.

#### <a name="event-types"></a>Tipos de evento

Os tipos de eventos são atribuídos a um espaço de nome de acordo com o seguinte esquema:

`Microsoft.Media.Graph.{EventClass}.{EventType}`

#### <a name="event-classes"></a>Aulas de eventos

|Nome da classe|Descrição|
|---|---|
|Análise  |Eventos gerados como parte da análise de conteúdo.|
|Diagnóstico    |Eventos que ajudam com diagnósticos de problemas e desempenho.|
|Operacional    |Eventos gerados como parte da operação de recursos.|

Os tipos de eventos são específicos de cada classe de evento.

Exemplos:

* Microsoft.Media.Graph.Analytics.Inference
* Microsoft.Media.Graph.Diagnostics.AuthorizationError
* Microsoft.Media.Graph.Operational.GraphInstanceStarted

### <a name="event-time"></a>Hora do evento

A hora do evento é descrita na cadeia ISO8601 e a hora em que o evento ocorreu.

## <a name="logging"></a>Registo

Tal como acontece com outros módulos IoT Edge, também pode [examinar os registos de contentores](https://docs.microsoft.com/azure/iot-edge/troubleshoot#check-container-logs-for-issues) no dispositivo Edge. As informações escritas para os registos podem ser controladas pelas seguintes propriedades gémeas do [módulo:](module-twin-configuration-schema.md)

* logLevel

   * Os valores permitidos são Verbose, Informação, Aviso, Erro, Nenhum.
   * O valor predefinido é Informação – os registos contêm erro, aviso e informação. mensagens.
   * Se definir o valor para Aviso, os registos conterão mensagens de erro e aviso
   * Se definir o valor de Erro, os registos apenas contêm mensagens de erro.
   * Se definir o valor a Nenhum, não serão gerados registos (isto não é recomendado).
   * Só deve utilizar o Verbose se precisar de partilhar registos com o Suporte Azure para diagnosticar um problema.
* logCategorias

   * Uma lista separada por vírgula de um ou mais dos seguintes: Aplicação, Eventos, MediaPipeline.
   * Padrão: Aplicação, Eventos.
   * Aplicação – esta é uma informação de alto nível do módulo, como mensagens de arranque de módulos, erros ambientais e chamadas de métodos diretos.
   * Eventos – estes são todos os eventos que foram descritos anteriormente neste artigo.
   * MediaPipeline – estes são alguns registos de baixo nível que podem oferecer informações sobre problemas de resolução de problemas, tais como dificuldades em estabelecer uma ligação com uma câmara capaz de RTSP.
   
### <a name="generating-debug-logs"></a>Gerar registos de depurg

Em certos casos, poderá ter de gerar registos mais detalhados do que os descritos acima, para ajudar o suporte do Azure a resolver um problema. Há dois passos para conseguir isto.

Em primeiro lugar, [liga-se o armazenamento do módulo ao armazenamento do dispositivo](https://docs.microsoft.com/azure/iot-edge/how-to-access-host-storage-from-module#link-module-storage-to-device-storage) através do createOptions. Se examinar um modelo de manifesto de [implantação](https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp/blob/master/src/edge/deployment.template.json) a partir do início rápido, verá:

```
"createOptions": {
   …
   "Binds": [
     "/var/local/mediaservices/:/var/lib/azuremediaservices/"
   ]
 }
```

Acima permite que o módulo Edge escreva registos para o (dispositivo) caminho de armazenamento "/var/local/mediaservices/". Se adicionar a seguinte propriedade desejada ao módulo:

`"debugLogsDirectory": "/var/lib/azuremediaservices/debuglogs/",`

Em seguida, o módulo escreverá registos de depuração num formato binário para o (dispositivo) caminho de armazenamento /var/local/mediaservices/debuglogs/, que pode partilhar com o Suporte Azure.

## <a name="faq"></a>FAQ

[FAQs](faq.md#monitoring-and-metrics)

## <a name="next-steps"></a>Passos seguintes

[Gravação contínua de vídeo](continuous-video-recording-tutorial.md)
