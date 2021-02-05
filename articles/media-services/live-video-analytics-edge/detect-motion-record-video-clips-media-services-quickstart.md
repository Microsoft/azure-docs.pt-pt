---
title: Detete movimento, grave vídeo para a Azure Media Services
description: Este quickstart mostra como usar o Live Video Analytics no IoT Edge de forma a detetar movimentos num stream de vídeo ao vivo e gravar videoclips para a Azure Media Services.
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: 135e68e2630d74dace6c3a6b70bb3666f77aad89
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99575539"
---
# <a name="quickstart-detect-motion-record-video-to-media-services"></a>Quickstart: Detect motion, gravar vídeo para media services

Este artigo acompanha-o através dos passos para usar o Live Video Analytics no IoT Edge para [gravação baseada em eventos.](event-based-video-recording-concept.md) Usa um Linux VM em Azure como um dispositivo IoT Edge e um stream de vídeo simulado ao vivo. Este fluxo de vídeo é analisado para a presença de objetos em movimento. Quando o movimento é detetado, os eventos são enviados para o Azure IoT Hub, e a parte relevante do fluxo de vídeo é registada como um ativo nos Serviços Azure Media.

Este artigo baseia-se no arranque rápido de [Getting Started](get-started-detect-motion-emit-events-quickstart.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
  > [!NOTE]
  > Você precisará de uma subscrição Azure com permissões para criar principais serviços **(a função do proprietário** fornece isso). Se não tiver as permissões certas, contacte o administrador da sua conta para lhe conceder as permissões certas. 
* [Código de estúdio visual](https://code.visualstudio.com/) na sua máquina com [extensão Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* Se ainda não tiver concluído o [quickstart Getting Started](get-started-detect-motion-emit-events-quickstart.md) anteriormente, então passe pelos seguintes passos:
    * [Configurar recursos do Azure](get-started-detect-motion-emit-events-quickstart.md#set-up-azure-resources)
    * [Implementar módulos](get-started-detect-motion-emit-events-quickstart.md#deploy-modules-on-your-edge-device)
    * [Configurar o Visual Studio Code](get-started-detect-motion-emit-events-quickstart.md#configure-the-azure-iot-tools-extension)
    > [!TIP]
    > Se encontrar problemas com os recursos da Azure que são criados, por favor, consulte o nosso **[guia de resolução de problemas](troubleshoot-how-to.md#common-error-resolutions)** para resolver alguns problemas comumente encontrados.
## <a name="review-the-sample-video"></a>Reveja o vídeo da amostra

Como parte dos passos acima para configurar os recursos Azure, um vídeo (curto) de um estacionamento será copiado para o Linux VM em Azure sendo usado como o dispositivo IoT Edge. Este ficheiro de vídeo será usado para simular um live stream para este tutorial.

Você pode usar uma aplicação como [VLC Player](https://www.videolan.org/vlc/), lançá-lo, hit `Ctrl+N` , e colar a ligação de amostra de [vídeo do estacionamento](https://lvamedia.blob.core.windows.net/public/lots_015.mkv) para iniciar a reprodução. Com cerca de 5 segundos, um carro branco move-se pelo estacionamento.
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LUbN]

Quando completar os passos abaixo, terá usado live video analytics no IoT Edge para detetar o movimento do carro, e gravar um videoclip a partir de cerca de 5 segundos. O diagrama abaixo é a representação visual do fluxo global.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstarts/topology.svg" alt-text="Gravação de vídeo baseada em eventos para Ativos com base em eventos de movimento":::

## <a name="use-direct-method-calls"></a>Use chamadas de métodos diretos

Pode utilizar o módulo para analisar streams de vídeo ao vivo invocando métodos diretos. Leia [Métodos Diretos para Análise de Vídeo Ao Vivo no IoT Edge](direct-methods.md) para compreender todos os métodos diretos fornecidos pelo módulo. 

1. No Código do Estúdio Visual, abra o **separador Extensões** (ou prima Ctrl+Shift+X) e procure por Azure IoT Hub.
1. Clique no direito e selecione **Definições de extensão**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Definições de extensão":::
1. Procure e ative "Mostrar Mensagem Verbose".

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Mostrar mensagem verbose":::

### <a name="invoke-graphtopologylist"></a>Invocar a Lista de Graftopologia
Este passo enumera todas as [topologias de gráficos](media-graph-concept.md#media-graph-topologies-and-instances) do módulo.

1. Clique com o botão direito no módulo "LvaEdge" e selecione "Invocar o Método Direto do Módulo" a partir do menu de contexto.
1. Você verá uma caixa de edição pop no meio da janela visual Studio Code. Introduza "GraphTopologyList" na caixa de edição e prima para introduzir.
1. Em seguida, copie e cole a carga útil abaixo do JSON na caixa de edição e prima para entrar.
    
```
{
    "@apiVersion" : "2.0"
}
```

Dentro de poucos segundos, verá a janela OUTPUT no popup Visual Studio Code com a seguinte resposta
    
```
[DirectMethod] Invoking Direct Method [GraphTopologyList] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": {
    "value": []
  }
}
```
    
Espera-se que a resposta acima referida não tenha sido criada nenhuma topologia de gráficos.

### <a name="invoke-graphtopologyset"></a>Invocar o GraphTopologySet

Utilizando os mesmos passos que os descritos para invocar a GraphTopologyList, pode invocar o GraphTopologySet para definir uma [topologia de gráficos usando](media-graph-concept.md#media-graph-topologies-and-instances) o seguinte JSON como carga útil. Você vai criar uma topologia de gráficos chamada "EVRtoAssetsOnMotionDetection".

```
{
    "@apiVersion": "2.0",
    "name": "EVRtoAssetsOnMotionDetection",
    "properties": {
      "description": "Event-based video recording to Assets based on motion events",
      "parameters": [
        {
            "name": "rtspUserName",
            "type": "String",
            "description": "rtsp source user name.",
            "default": "dummyUserName"
        },
        {
            "name": "rtspPassword",
            "type": "String",
            "description": "rtsp source password.",
            "default" : "dummyPassword"
        },
        {
            "name": "rtspUrl",
            "type": "String",
            "description": "rtsp Url"
        },
        {
            "name": "motionSensitivity",
            "type": "String",
            "description": "motion detection sensitivity",
            "default" : "medium"
        },
        {
            "name": "hubSinkOutputName",
            "type": "String",
            "description": "hub sink output name",
            "default" : "iothubsinkoutput"
        }                              
    ],         

      "sources": [
        {
          "@type": "#Microsoft.Media.MediaGraphRtspSource",
          "name": "rtspSource",
          "endpoint": {
            "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
              "username": "${rtspUserName}",
              "password": "${rtspPassword}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
          "name": "motionDetection",
          "sensitivity": "${motionSensitivity}",
          "inputs": [
            {
              "nodeName": "rtspSource"
            }
          ]
        },
        {
          "@type": "#Microsoft.Media.MediaGraphSignalGateProcessor",
          "name": "signalGateProcessor",
          "inputs": [
            {
              "nodeName": "motionDetection"
            },
            {
              "nodeName": "rtspSource"
            }
          ],
          "activationEvaluationWindow": "PT1S",
          "activationSignalOffset": "PT0S",
          "minimumActivationTime": "PT30S",
          "maximumActivationTime": "PT30S"
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.Media.MediaGraphAssetSink",
          "name": "assetSink",
          "assetNamePattern": "sampleAssetFromEVR-LVAEdge-${System.DateTime}",
          "segmentLength": "PT0M30S",
          "localMediaCacheMaximumSizeMiB": "2048",
          "localMediaCachePath": "/var/lib/azuremediaservices/tmp/",
          "inputs": [
            {
              "nodeName": "signalGateProcessor"
            }
          ]
        },
        {
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "name": "hubSink",
          "hubOutputName": "${hubSinkOutputName}",
          "inputs": [
            {
              "nodeName": "motionDetection"
            }
          ]
        }
      ]
    }
}
```

A carga útil JSON acima resulta na criação de uma topologia de gráfico que define cinco parâmetros (quatro dos quais têm valores predefinidos). A topologia tem um nó de origem[(fonte RTSP),](media-graph-concept.md#rtsp-source)dois nóns de processadores[(processador de deteção de movimento](media-graph-concept.md#motion-detection-processor) e [processador de portão de sinal,](media-graph-concept.md#signal-gate-processor)e dois nónáss de pia (pia IoT Hub e [pia de ativo).](media-graph-concept.md#asset-sink) A representação visual da topologia é mostrada acima.

Dentro de alguns segundos, verá a seguinte resposta na janela **OUTPUT.**

```
[DirectMethod] Invoking Direct Method [GraphTopologySet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 201,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-12T22:05:31.603Z",
      "lastModifiedAt": "2020-05-12T22:05:31.603Z"
    },
    "name": "EVRtoAssetsOnMotionDetection",
    "properties": {
      "description": "Event-based video recording to assets based on motion events",
      "parameters": [
        {
          "name": "rtspUserName",
          "type": "String",
          "description": "rtsp source user name.",
          "default": "dummyUserName"
        },
        {
          "name": "rtspPassword",
          "type": "String",
          "description": "rtsp source password.",
          "default": "dummyPassword"
        },
        {
          "name": "rtspUrl",
          "type": "String",
          "description": "rtsp Url"
        },
        {
          "name": "motionSensitivity",
          "type": "String",
          "description": "motion detection sensitivity",
          "default": "medium"
        },
        {
          "name": "hubSinkOutputName",
          "type": "String",
          "description": "hub sink output name",
          "default": "iothubsinkoutput"
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.Media.MediaGraphRtspSource",
          "name": "rtspSource",
          "transport": "Tcp",
          "endpoint": {
            "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
              "username": "${rtspUserName}",
              "password": "${rtspPassword}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
          "sensitivity": "${motionSensitivity}",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        },
        {
          "@type": "#Microsoft.Media.MediaGraphSignalGateProcessor",
          "activationEvaluationWindow": "PT1S",
          "activationSignalOffset": "PT0S",
          "minimumActivationTime": "PT30S",
          "maximumActivationTime": "PT30S",
          "name": "signalGateProcessor",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            },
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.Media.MediaGraphAssetSink",
          "localMediaCachePath": "/var/lib/azuremediaservices/tmp/",
          "localMediaCacheMaximumSizeMiB": "2048",
          "segmentLength": "PT0M30S",
          "assetNamePattern": "sampleAssetFromEVR-LVAEdge-${System.DateTime}",
          "name": "assetSink",
          "inputs": [
            {
              "nodeName": "signalGateProcessor",
              "outputSelectors": []
            }
          ]
        },
        {
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "hubOutputName": "${hubSinkOutputName}",
          "name": "hubSink",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            }
          ]
        }
      ]
    }
  }
}
```

O estatuto devolvido é 201, indicando que foi criada uma nova topologia de gráficos. Experimente os seguintes métodos diretos como próximos passos:

* Volte a invocar o GraphTopologySet e verifique se o código de estado devolvido é de 200. O código de estado 200 indica que uma topologia de gráficos existente foi atualizada com sucesso.
* Volte a invocar o GraphTopologySet, mas altere a cadeia de descrição. Verifique se o código de estado na resposta é de 200 e a descrição é atualizada para o novo valor.
* Invoque a GrafTopologyList como delineada na secção anterior e verifique se agora pode ver a topologia do gráfico "EVRtoAssetsOnMotionDetection" na carga útil devolvida.

### <a name="invoke-graphtopologyget"></a>Invocar GrafTopologyGet

Agora invoque o GraphTopologyGet com a seguinte carga útil
```

{
    "@apiVersion" : "2.0",
    "name" : "EVRtoAssetsOnMotionDetection"
}
```

Dentro de poucos segundos, deverá ver a seguinte resposta na janela de saída

```
[DirectMethod] Invoking Direct Method [GraphTopologyGet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-12T22:05:31.603Z",
      "lastModifiedAt": "2020-05-12T22:05:31.603Z"
    },
    "name": "EVRtoAssetsOnMotionDetection",
    "properties": {
      "description": "Event-based video recording to Assets based on motion events",
      "parameters": [
        {
          "name": "rtspUserName",
          "type": "String",
          "description": "rtsp source user name.",
          "default": "dummyUserName"
        },
        {
          "name": "rtspPassword",
          "type": "String",
          "description": "rtsp source password.",
          "default": "dummyPassword"
        },
        {
          "name": "rtspUrl",
          "type": "String",
          "description": "rtsp Url"
        },
        {
          "name": "motionSensitivity",
          "type": "String",
          "description": "motion detection sensitivity",
          "default": "medium"
        },
        {
          "name": "hubSinkOutputName",
          "type": "String",
          "description": "hub sink output name",
          "default": "iothubsinkoutput"
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.Media.MediaGraphRtspSource",
          "name": "rtspSource",
          "transport": "Tcp",
          "endpoint": {
            "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
              "username": "${rtspUserName}",
              "password": "${rtspPassword}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
          "sensitivity": "${motionSensitivity}",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        },
        {
          "@type": "#Microsoft.Media.MediaGraphSignalGateProcessor",
          "activationEvaluationWindow": "PT1S",
          "activationSignalOffset": "PT0S",
          "minimumActivationTime": "PT30S",
          "maximumActivationTime": "PT30S",
          "name": "signalGateProcessor",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            },
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.Media.MediaGraphAssetSink",
          "localMediaCachePath": "/var/lib/azuremediaservices/tmp/",
          "localMediaCacheMaximumSizeMiB": "2048",
          "segmentLength": "PT0M30S",
          "assetNamePattern": "sampleAssetFromEVR-LVAEdge-${System.DateTime}",
          "name": "assetSink",
          "inputs": [
            {
              "nodeName": "signalGateProcessor",
              "outputSelectors": []
            }
          ]
        },
        {
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "hubOutputName": "${hubSinkOutputName}",
          "name": "hubSink",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            }
          ]
        }
      ]
    }
  }
}
```

Note as seguintes propriedades na carga útil de resposta:

* O código de estado é 200, indicando sucesso.
* A carga útil tem a "criação" e a última meta de tempo "última Modificada".

### <a name="invoke-graphinstanceset"></a>Invocar GraphInstanceSet

Em seguida, crie uma instância de gráfico que faz referência à topologia do gráfico acima. Como [explicado aqui](media-graph-concept.md#media-graph-topologies-and-instances), os exemplos de gráficos permitem analisar streams de vídeo ao vivo de muitas câmaras com a mesma topologia de gráficos.

Agora invoque o método direto GraphInstanceSet com a seguinte carga útil:

```
{
    "@apiVersion" : "2.0",
    "name" : "Sample-Graph-2",
    "properties" : {
        "topologyName" : "EVRtoAssetsOnMotionDetection",
        "description" : "Sample graph description",
        "parameters" : [
            { "name" : "rtspUrl", "value" : "rtsp://rtspsim:554/media/lots_015.mkv" }
        ]
    }
}
```

Tenha em atenção o seguinte:

* A carga útil acima especifica o nome de topologia do gráfico (EVRtoAssetsOnMotionDetection) para o qual a instância do gráfico precisa de ser criada.
* A carga útil contém o valor do parâmetro para "rtspUrl", que não tinha um valor padrão na carga útil da topologia.

Em poucos segundos, verá a seguinte resposta na janela saída:

```
[DirectMethod] Invoking Direct Method [GraphInstanceSet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 201,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-12T23:30:20.666Z",
      "lastModifiedAt": "2020-05-12T23:30:20.666Z"
    },
    "name": "Sample-Graph-2",
    "properties": {
      "state": "Inactive",
      "description": "Sample graph description",
      "topologyName": "EVRtoAssetsOnMotionDetection",
      "parameters": [
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/lots_015.mkv"
        }
      ]
    }
  }
}
```

Note as seguintes propriedades na carga útil de resposta:

* O código de estado é 201, indicando que foi criado um novo caso.
* O Estado é "Inativo", indicando que a instância do gráfico foi criada mas não ativada. Para mais informações, consulte os estados [dos gráficos de mídia.](media-graph-concept.md)

Experimente os seguintes métodos diretos como próximos passos:

* Invoque o GraphInstanceSet novamente com a mesma carga útil e note que o código de estado devolvido é agora de 200.
* Volte a invocar o GraphInstanceSet, mas com uma descrição diferente e note a descrição atualizada na carga útil de resposta, indicando que a instância do gráfico foi atualizada com sucesso.
* Invoque o GraphInstanceSet mas altere o nome para "Sample-Graph-3" e observe a carga útil de resposta. Note que é criada uma nova instância de gráfico (isto é, código de estado é 201). Lembre-se de limpar tais casos duplicados quando terminar com o arranque rápido.

### <a name="prepare-for-monitoring-events"></a>Preparar para eventos de monitorização

O gráfico de mídia que criou usa o nó do processador de deteção de movimentos para detetar movimento, e tais eventos são retransmitidos para o seu IoT Hub. A fim de se preparar para observar tais eventos, siga estes passos

1. Abra o painel Explorer no Código do Estúdio Visual e procure o Azure IoT Hub no canto inferior esquerdo.
1. Expandir o nó dispositivos
1. Clique com o botão direito no dispositivo de amostra de Lva e escolheu a opção "Iniciar a monitorização de eventos incorporados"

    ![Iniciar monitorização de eventos incorporados](./media/quickstarts/start-monitoring-iothub-events.png)
    
    > [!NOTE]
    > Pode ser-lhe pedido que forneça informações sobre o ponto final incorporado para o IoT Hub. Para obter essa informação, no portal Azure, navegue até ao seu Hub IoT e procure a opção **de pontos finais incorporados** no painel de navegação esquerdo. Clique lá e procure o **ponto final compatível com o Event Hub** na secção de ponto final **compatível com o Event Hub.** Copie e use o texto na caixa. O ponto final será mais ou menos assim:  
        ```
        Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
        ```
    Em poucos segundos, verá as seguintes mensagens na janela OUTPUT:

```
[IoTHubMonitor] Start monitoring message arrived in built-in endpoint for all devices ...
[IoTHubMonitor] Created partition receiver [0] for consumerGroup [$Default]
[IoTHubMonitor] Created partition receiver [1] for consumerGroup [$Default]
[IoTHubMonitor] Created partition receiver [2] for consumerGroup [$Default]
[IoTHubMonitor] Created partition receiver [3] for consumerGroup [$Default]
```

### <a name="invoke-graphinstanceactivate"></a>Invocar GraphInstanceActivate

Agora ative a instância do gráfico - que inicia o fluxo de vídeo ao vivo através do módulo. Invocar o método direto GraphInstanceActivate com a seguinte carga útil:

```
{
    "@apiVersion" : "2.0",
    "name" : "Sample-Graph-2"
}
```

Em poucos segundos, deverá ver a seguinte resposta na janela OUTPUT

```
[DirectMethod] Invoking Direct Method [GraphInstanceActivate] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

O código de estado de 200 na carga útil da resposta indica que a instância do gráfico foi ativada com sucesso.

### <a name="invoke-graphinstanceget"></a>Invocar o GraphInstanceGet

Agora invoque o método direto GraphInstanceGet com a seguinte carga útil:

```
{
    "@apiVersion" : "2.0",
    "name" : "Sample-Graph-2"
}
```

Em poucos segundos, deverá ver a seguinte resposta na janela OUTPUT

```
[DirectMethod] Invoking Direct Method [GraphInstanceGet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-12T23:30:20.666Z",
      "lastModifiedAt": "2020-05-12T23:30:20.666Z"
    },
    "name": "Sample-Graph-2",
    "properties": {
      "state": "Active",
      "description": "Sample graph description",
      "topologyName": "EVRtoAssetsOnMotionDetection",
      "parameters": [
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/lots_015.mkv"
        }
      ]
    }
  }
}
```

Note as seguintes propriedades na carga útil de resposta:

* O código de estado é 200, indicando sucesso.
* O Estado é "Ativo", indicando que a instância do gráfico está agora no estado "Ativo".

## <a name="observe-results"></a>Observar resultados

A instância de gráfico que criou e ativou acima utiliza o nó do processador de deteção de movimentos para detetar movimento na transmissão de vídeo ao vivo e envia eventos para a pia IoT Hub. Estes eventos são então retransmitidos para o seu Hub IoT, que agora pode ser observado. Verá as seguintes mensagens na janela OUTPUT

```
[IoTHubMonitor] [4:33:04 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1589326384077235 1 IN IP4 XXX.XX.XX.XXX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/lots_015.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-73.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/lots_015.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=640028;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/microsoft.media/mediaservices/{amsAccountName}",
    "subject": "/graphInstances/Sample-Graph-2/sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-05-12T23:33:04.077Z",
    "dataVersion": "1.0"
  }
}
[IoTHubMonitor] [4:33:09 PM] Message received from [lva-sample-device/lvaEdge]:
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
    "topic": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/microsoft.media/mediaservices/{amsAccountName}",
    "subject": "/graphInstances/Sample-Graph-2/processors/md",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-05-12T23:33:09.381Z",
    "dataVersion": "1.0"
  }
}
```

Note as seguintes propriedades nas mensagens acima

* Cada mensagem contém uma secção "body" e uma secção "aplicaçõesProperties". Para compreender o que estas secções representam, leia o artigo [Criar e Ler mensagem IoT Hub](../../iot-hub/iot-hub-devguide-messages-construct.md).
* A primeira mensagem é um evento de Diagnóstico, mediaSessionEstablished, dizendo que o nó RTSP Source (sujeito) foi capaz de estabelecer conexão com o simulador RTSP, e começar a receber um feed ao vivo (simulado).
* O "sujeito" na aplicaçãoDeproperias refere o nó na topologia do gráfico a partir do qual a mensagem foi gerada. Neste caso, a mensagem é originária do nó de origem RTSP.
* "eventType" na aplicaçãoProperties indica que este é um evento de Diagnóstico.
* "eventTime" indica a hora em que o evento ocorreu.
* "body" contém dados sobre o evento de diagnóstico - é a mensagem [SDP.](https://en.wikipedia.org/wiki/Session_Description_Protocol)
* A segunda mensagem é um evento de Analytics. Pode verificar se é enviada cerca de 5 segundos após a mensagem MediaSessionEstablished, que corresponde ao atraso entre o início do vídeo e quando o carro passa pelo estacionamento.
* O "sujeito" na aplicaçãoDeproperties refere o nó do processador de deteção de movimento no gráfico, que gerou esta mensagem
* O evento é um evento de Inferência e, portanto, o corpo contém dados de "timetamp" e "inferências".
* A secção "inferências" indica que o "tipo" é "movimento" e tem dados adicionais sobre o evento "movimento".

A próxima mensagem que verá é a seguinte.

```
[IoTHubMonitor] [4:33:10 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200512T233309Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/microsoft.media/mediaservices/{amsAccountName}",
    "subject": "/graphInstances/Sample-Graph-2/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": "2020-05-12T23:33:10.392Z",
    "dataVersion": "1.0"
  }
}
```

* A terceira mensagem é um evento operacional. Pode verificar se é enviada quase imediatamente após a mensagem de deteção de movimentos, que agiu como o gatilho para começar a gravar.
* O "sujeito" na aplicaçãoDeproperias refere o nó do lavatório do ativo no gráfico, que gerou esta mensagem.
* O corpo contém informações sobre a localização da saída, que neste caso é o nome do ativo Azure Media Service no qual o vídeo é gravado. Note este valor - irá usá-lo mais tarde no arranque rápido.

Na topologia, o nó do processador do portão de sinal foi configurado com tempos de ativação de 30 segundos, o que significa que a topologia do gráfico irá gravar cerca de 30 segundos de vídeo no ativo. Enquanto o vídeo está a ser gravado, o nó do processador de deteção de movimentos continuará a emitir eventos de Inferência, que aparecerão na janela OUTPUT. Depois de algum tempo, verá a seguinte mensagem.

```
[IoTHubMonitor] [4:33:31 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200512T233309Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/microsoft.media/mediaservices/{amsAccountName}",
    "subject": "/graphInstances/Sample-Graph-2/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingAvailable",
    "eventTime": "2020-05-12T23:33:31.051Z",
    "dataVersion": "1.0"
  }
}
```

* Esta mensagem é também um evento Operacional. O evento, RecordingAvailable, indica que foram escritos dados suficientes ao Ativo para que os jogadores/clientes iniciem a reprodução do vídeo
* O "sujeito" na aplicaçãoDeproperties refere o nó do lavatório do ativo no gráfico, que gerou esta mensagem
* O corpo contém informações sobre a localização da saída, que neste caso é o nome do ativo Azure Media Service no qual o vídeo é gravado.

Se deixar que a instância do gráfico continue a funcionar, verá esta mensagem.

```
[IoTHubMonitor] [4:33:40 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200512T233309Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/microsoft.media/mediaservices/{amsAccountName}",
    "subject": "/graphInstances/Sample-Graph-2/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStopped",
    "eventTime": "2020-05-12T23:33:40.014Z",
    "dataVersion": "1.0"
  }
}
```

* Esta mensagem é também um evento Operacional. O evento, RecordingS, indica que a gravação parou.
* Note que decorreram cerca de 30 segundos desde o evento RecordingStarted, correspondendo aos valores dos tempos de ativação no nó do processador do portão de sinal.
* O "sujeito" na aplicaçãoDeproperias refere o nó do lavatório do ativo no gráfico, que gerou esta mensagem.
* O corpo contém informações sobre a localização da saída, que neste caso é o nome do ativo Azure Media Service no qual o vídeo é gravado.

Se deixar que a instância do gráfico continue a ser executada, o simulador RTSP chegará ao fim do ficheiro de vídeo e para/desliga. O nó de origem RTSP volta a ligar-se ao simulador e o processo repetir-se-á.
    
## <a name="invoke-additional-direct-method-calls-to-clean-up"></a>Invocar chamadas de método direto adicional para limpar

Agora, invocar métodos diretos para desativar e apagar a instância do gráfico (por esta ordem).

### <a name="invoke-graphinstancedeactivate"></a>Invocar GraphInstanceDeactivo

Invocar o método direto GraphInstanceDeactivado com a seguinte carga útil:

```
{
    "@apiVersion" : "2.0",
    "name" : "Sample-Graph-2"
}
```

Dentro de poucos segundos, deverá ver a seguinte resposta na janela OUTPUT.

```
[DirectMethod] Invoking Direct Method [GraphInstanceDeactivate] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

O código de estado de 200 indica que a instância do gráfico foi desativada com sucesso.

Experimente os seguintes passos:

* Invocar o GraphInstanceGet como indicado nas secções anteriores e observar o valor "estado".

### <a name="invoke-graphinstancedelete"></a>Invocar GraphInstanceDelete

Invocar o método direto GraphInstanceDelete com a seguinte carga útil

```
{
    "@apiVersion" : "2.0",
    "name" : "Sample-Graph-2"
}
```

Em poucos segundos, deverá ver a seguinte resposta na janela OUTPUT:

```
[DirectMethod] Invoking Direct Method [GraphInstanceDelete] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

O código de estado de 200 na resposta indica que a instância do gráfico foi eliminada com sucesso.

### <a name="invoke-graphtopologydelete"></a>Invocar GraphTopologyDelete

Invocar o método direto GraphTopologyDelete com a seguinte carga útil:

```
{
    "@apiVersion" : "2.0",
    "name" : "EVRtoAssetsOnMotionDetection"
}
```

Em poucos segundos, deverá ver a seguinte resposta na janela OUTPUT

```
[DirectMethod] Invoking Direct Method [GraphTopologyDelete] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

O código de estado de 200 indica que a topologia do MediaGraph foi eliminada com sucesso.

Experimente os seguintes métodos diretos como próximos passos:

* Invoque a GraphTopologyList e observe que não existem topologias de gráficos no módulo.
* Invoque a GraphInstanceList com a mesma carga útil que a GraphTopologyList e observe que não existem instâncias de gráfico enumeradas.

## <a name="playing-back-the-recorded-video"></a>Reproduzindo o vídeo gravado

Em seguida, pode utilizar o portal Azure para reproduzir o vídeo que gravou.

1. Faça login no [portal Azure,](https://portal.azure.com/)escreva "Media Services" na caixa de pesquisa.
1. Localize a sua conta Azure Media Services e abra-a.
1. Localize e selecione a entrada de Ativos na listagem de Serviços de Mídia.

    ![Entrada de ativos na listagem dos Serviços de Comunicação Social](./media/quickstarts/asset-entity.png)
1. Se este quickstart for o seu primeiro uso da Azure Media Services, apenas os ativos gerados a partir deste quickstart serão listados, e você pode escolher o mais antigo.
1. Caso contrário, utilize o nome do ativo que foi fornecido como saídaLocação nos eventos operacionais acima.
1. Na página de detalhes que abre, clique no link "Criar novo" logo abaixo da caixa de texto URL de streaming.

    ![O URL de streaming](./media/quickstarts/asset-streaming-url.png)
1. No painel que abre para "Adicionar localizador de streaming", aceite os predefinidos e acerte "Add" na parte inferior.
1. Na página de detalhes do Ativo, o leitor de vídeo deve agora carregar para o primeiro quadro do vídeo, e pode carregar no botão de reprodução. Verifique se vê a parte do vídeo onde o carro se move no estacionamento.

    ![Jogar](./media/quickstarts/asset-playback.png)

> [!NOTE]
> Uma vez que o vídeo simulado ao vivo começa quando ativa o gráfico, os valores da hora do dia não são relevantes e não são expostos através deste atalho do jogador. O tutorial sobre gravação e reprodução contínua de vídeo mostra como pode exibir os tempos de tensão.

## <a name="clean-up-resources"></a>Limpar os recursos

Se não vai continuar a utilizar esta aplicação, elimine os recursos criados neste arranque rápido.

## <a name="next-steps"></a>Passos seguintes

* Aprenda a invocar o Live Video Analytics em [métodos diretos](direct-methods.md) IoT Edge programáticoticamente.
* Saiba mais sobre mensagens de diagnóstico.    
