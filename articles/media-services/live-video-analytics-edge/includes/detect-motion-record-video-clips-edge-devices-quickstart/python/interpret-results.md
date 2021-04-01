---
ms.openlocfilehash: 870b5b54c4a5afb39b43063bd00d3cb73e8ee0f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88682334"
---
Quando executar o gráfico de mídia, os resultados do nó do processador de processador de movimento passam pelo nó da pia IoT Hub para o hub IoT. As mensagens que vê na janela **OUTPUT** do Código do Estúdio Visual contêm uma `body` secção e uma `applicationProperties` secção. Para obter mais informações, consulte [Criar e ler mensagens IoT Hub](../../../../../iot-hub/iot-hub-devguide-messages-construct.md).

Nas seguintes mensagens, o módulo Live Video Analytics define as propriedades da aplicação e o conteúdo do corpo.

### <a name="mediasessionestablished-event"></a>Evento mediaSessionEstablished

Quando um gráfico de mídia é instantâneo, o nó de origem RTSP tenta ligar-se ao servidor RTSP que funciona no recipiente rtspsim-live555. Se a ligação for bem sucedida, o seguinte evento é impresso.

```
[IoTHubMonitor] [05:37:21 AM] Message received from [lva-sample-device/lvaEdge]:
{  
"body": {
"sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 xxx.xxx.xxx.xxx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets={SPS}\r\na=control:track1\r\n"  
},  
"applicationProperties": {  
    "dataVersion": "1.0",  
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",  
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",  
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",  
    "eventTime": "2020-05-21T05:37:21.398Z",
    }  
}
```

Na saída anterior: 

* A mensagem é um evento de diagnóstico, `MediaSessionEstablished` . Indica que o nó de origem RTSP (o sujeito) estabeleceu uma ligação com o simulador RTSP e começou a receber um feed ao vivo (simulado).
* Em `applicationProperties` , refere o nó na `subject` topologia do gráfico a partir do qual a mensagem foi gerada. Neste caso, a mensagem provém do nó de origem RTSP.
* Em `applicationProperties` , indica que este evento é um evento de `eventType` diagnóstico.
* O `eventTime` valor é o momento em que o evento ocorreu.
* A `body` secção contém dados sobre o evento de diagnóstico. Neste caso, os dados compreendem os detalhes do [Protocolo de Descrição da Sessão (SDP).](https://en.wikipedia.org/wiki/Session_Description_Protocol)

### <a name="recordingstarted-event"></a>Evento "RecordingStarted"

Quando o movimento é detetado, o nó do processador do portão de sinal é ativado e o nó da pia do ficheiro no gráfico de mídia começa a escrever um ficheiro MP4. O nó da pia envia um evento operacional. O `type` conjunto indica que é um resultado do processador de `motion` deteção de movimentos. O `eventTime` valor é o tempo UTC em que a moção ocorreu. Para obter mais informações sobre este processo, consulte a secção ['Vista Geral'](#overview) neste arranque rápido.

Aqui está um exemplo desta mensagem:

```
[IoTHubMonitor] [05:37:27 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "filePath",
    "outputLocation": "/var/media/sampleFilesFromEVR-filesinkOutput-20200521T053726Z.mp4"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",  
    "subject": "/graphInstances/Sample-Graph-1/sinks/fileSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": "2020-05-21T05:37:27.713Z",
    "dataVersion": "1.0"
  }
}
```

Na mensagem anterior: 

* Em `applicationProperties` , refere o nó no gráfico mediático a partir do qual a `subject` mensagem foi gerada. Neste caso, a mensagem provém do nó da pia do ficheiro.
* Em `applicationProperties` , indica que este evento está `eventType` operacional.
* O `eventTime` valor é o momento em que o evento ocorreu. Desta vez é de 5 a 6 segundos depois `MediaSessionEstablished` e depois de o vídeo começar a fluir. Desta vez corresponde à marca de 5 a 6 segundos quando o [carro começou a mover-se para](#review-the-sample-video) o estacionamento.
* A `body` secção contém dados sobre o evento operacional. Neste caso, os dados compreendem `outputType` e `outputLocation` .
* A `outputType` variável indica que esta informação é sobre o caminho do ficheiro.
* O `outputLocation` valor é a localização do ficheiro MP4 no módulo de borda.

### <a name="recordingstopped-and-recordingavailable-events"></a>Gravações Eventos e Registos Disponíveis

Se examinar as propriedades do nó do processador do portão de sinal na topologia do [gráfico,](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-motion-files/topology.json)verá que os tempos de ativação estão definidos para 5 segundos. Então, cerca de 5 segundos após a recebida do `RecordingStarted` evento, você recebe:

* Um `RecordingStopped` evento, indicando que a gravação parou.
* Um `RecordingAvailable` evento, indicando que o ficheiro MP4 pode agora ser usado para visualização.

Os dois eventos são normalmente emitidos em segundos um do outro.
