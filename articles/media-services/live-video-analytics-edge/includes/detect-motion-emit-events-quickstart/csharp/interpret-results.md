---
ms.openlocfilehash: 8bcef40dad9c67e9e2c6d6c4a051045999487027
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99531518"
---
Quando executar o gráfico de mídia, os resultados do nó do processador de processador de movimento passam pelo nó da pia IoT Hub para o hub IoT. As mensagens que vê na janela **OUTPUT** do Código do Estúdio Visual contêm uma `body` secção e uma `applicationProperties` secção. Para obter mais informações, consulte [Criar e ler mensagens IoT Hub](../../../../../iot-hub/iot-hub-devguide-messages-construct.md).

Nas seguintes mensagens, o módulo Live Video Analytics define as propriedades da aplicação e o conteúdo do corpo.

### <a name="mediasessionestablished-event"></a>Evento mediaSessionEstablished

Quando um gráfico de mídia é instantâneo, o nó de origem RTSP tenta ligar-se ao servidor RTSP que funciona no recipiente rtspsim-live555. Se a ligação for bem sucedida, o seguinte evento é impresso.

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:  
{  
"body": {
"sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 xxx.xxx.xxx.xxx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets={SPS}\r\na=control:track1\r\n"  
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

Na saída anterior: 

* A mensagem é um evento de diagnóstico, `MediaSessionEstablished` . Indica que o nó de origem RTSP (o sujeito) ligado ao simulador RTSP e começou a receber um feed ao vivo (simulado).
* Em `applicationProperties` , refere o nó na `subject` topologia do gráfico a partir do qual a mensagem foi gerada. Neste caso, a mensagem provém do nó de origem RTSP.
* Em `applicationProperties` , indica que este evento é um evento de `eventType` diagnóstico.
* O `eventTime` valor indica a hora em que o evento ocorreu.
* A `body` secção contém dados sobre o evento de diagnóstico. Neste caso, os dados compreendem os detalhes do [Protocolo de Descrição da Sessão (SDP).](https://en.wikipedia.org/wiki/Session_Description_Protocol)

### <a name="motiondetection-event"></a>Evento MotionDetection

Quando o movimento é detetado, o módulo Live Video Analytics no IoT Edge envia um evento de inferência. O `type` conjunto indica que é um resultado do processador de `motion` deteção de movimentos. O `eventTime` valor diz-lhe quando (na UTC) ocorreu o movimento. 

Aqui está um exemplo desta mensagem:

```
  {  
  "body": {  
    "timestamp": 142843967343090,
    "inferences": [  
      {  
        "type": "motion",  
        "motion": {  
          "box": {  
            "l": 0.573222,  
            "t": 0.492537,  
            "w": 0.141667,  
            "h": 0.074074  
          }  
        }  
      }  
    ]  
  } 
}  
```

Neste exemplo: 

* O `eventTime` valor é o momento em que o evento ocorreu.
* O `body` valor são dados sobre o evento de análise. Neste caso, o evento é um evento de inferência, pelo que o corpo contém `timestamp` e `inferences` dados.
* Os `inferences` dados indicam que `type` o é `motion` . Tem dados adicionais sobre o `motion` evento.
* A `box` secção contém as coordenadas de uma caixa de delimitação em torno do objeto em movimento. Os valores são normalizados pela largura e altura do vídeo, em pixels. Por exemplo, a largura é de 1920 e a altura é de 1080.

    ```
    l - distance from left of image
    t - distance from top of image
    w - width of bounding box
    h - height of bounding box
    ```
