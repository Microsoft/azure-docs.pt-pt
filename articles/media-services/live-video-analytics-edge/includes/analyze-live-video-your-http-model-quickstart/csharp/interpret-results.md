---
ms.openlocfilehash: 373b7b8dbf2ccc516edbbb3b87e284d88dfaf0c2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99508494"
---
Quando executou o gráfico de mídia, os resultados do nó do processador de extensão HTTP passam pelo nó da pia IoT Hub para o hub IoT. As mensagens que vê na janela **OUTPUT** contêm uma `body` secção e uma `applicationProperties` secção. Para obter mais informações, consulte [Criar e ler mensagens IoT Hub](../../../../../iot-hub/iot-hub-devguide-messages-construct.md).

Nas seguintes mensagens, o módulo Live Video Analytics define as propriedades da aplicação e o conteúdo do corpo. 

### <a name="mediasessionestablished-event"></a>Evento mediaSessionEstablished

Quando um gráfico de mídia é instantâneo, o nó de origem RTSP tenta ligar-se ao servidor RTSP que funciona no recipiente rtspsim-live555. Se a ligação for bem sucedida, o seguinte evento é impresso. O tipo de evento é `Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished` .

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 nnn.nn.0.6\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=Z00AKeKQCgC3YC3AQEBpB4kRUA==,aO48gA==\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/sources/rtspSource",
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-04-09T16:42:18.1280000Z"
  }
}
```

Nesta mensagem, note estes detalhes:

* A mensagem é um evento de diagnóstico. `MediaSessionEstablished` indica que o nó de origem RTSP (o sujeito) ligado ao simulador RTSP e começou a receber um feed ao vivo (simulado).
* Em `applicationProperties` , indica que a `subject` mensagem foi gerada a partir do nó de origem RTSP no gráfico de mídia.
* Em `applicationProperties` , indica que este evento é um evento de `eventType` diagnóstico.
* O `eventTime` indica a hora em que o evento ocorreu.
* Contém `body` dados sobre o evento de diagnóstico. Neste caso, os dados compreendem os detalhes do [Protocolo de Descrição da Sessão (SDP).](https://en.wikipedia.org/wiki/Session_Description_Protocol)

### <a name="inference-event"></a>Evento de inferência

O nó do processador de extensão HTTP recebe resultados de inferência do módulo yolov3. Em seguida, emite os resultados através do nó de pia IoT Hub como eventos de inferência. 

Nestes eventos, o tipo está definido `entity` para indicar que é uma entidade, como um carro ou um caminhão. O `eventTime` valor é a hora utc quando o objeto foi detetado. 

No exemplo seguinte, foram detetados dois carros na mesma moldura de vídeo, com diferentes níveis de confiança.

```
[IoTHubMonitor] [11:37:17 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "inferences": [
      {
        "entity": {
          "box": {
            "h": 0.0344108157687717,
            "l": 0.5756940841674805,
            "t": 0.5929375966389974,
            "w": 0.04484643936157227
          },
          "tag": {
            "confidence": 0.8714089393615723,
            "value": "car"
          }
        },
        "type": "entity"
      },
      {
        "entity": {
          "box": {
            "h": 0.03960910373263889,
            "l": 0.2750667095184326,
            "t": 0.6102327558729383,
            "w": 0.031027007102966308
          },
          "tag": {
            "confidence": 0.7042660713195801,
            "value": "car"
          }
        },
        "type": "entity"
      }
    ]
  }
}
```

Nas mensagens, note os seguintes detalhes:

* O `eventTime` valor é o momento em que o evento ocorreu.
* A `body` secção contém dados sobre o evento de análise. Neste caso, o evento é um evento de inferência, pelo que o corpo contém `inferences` dados.
* A `inferences` secção indica que o é `type` `entity` . Esta secção inclui dados adicionais sobre a entidade.
