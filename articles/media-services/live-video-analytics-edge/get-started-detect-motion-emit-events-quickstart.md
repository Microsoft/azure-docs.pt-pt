---
title: Começar com Live Video Analytics em IoT Edge - Azure
description: Este quickstart mostra como começar com live video analytics em IoT Edge, e detetar movimento em um stream de vídeo ao vivo.
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: 307a81938be3e25b8a6a07bb3696ca3b7647c0aa
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/01/2020
ms.locfileid: "84262015"
---
# <a name="quickstart-get-started---live-video-analytics-on-iot-edge"></a>Quickstart: Get start - Live Video Analytics on IoT Edge

Este quickstart acompanha-o através dos passos para começar com live video analytics em IoT Edge. Utiliza um Azure VM como um dispositivo IoT Edge e um vídeo simulado ao vivo. Após completar os passos de configuração, poderá executar um vídeo simulado ao vivo através de um gráfico de mídia que detete e reporte qualquer movimento nesse fluxo. O diagrama abaixo mostra uma representação gráfica desse gráfico mediático.

![Análise de vídeo ao vivo baseada na deteção de movimentos](./media/analyze-live-video/motion-detection.png)

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Código de estúdio visual](https://code.visualstudio.com/) na sua máquina de desenvolvimento com [extensão Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* A rede, à qual a sua máquina de desenvolvimento está ligada, deve permitir o protocolo AMQP sobre a porta 5671 (para que as Ferramentas Azure IoT possam comunicar com o Azure IoT Hub).

> [!TIP]
> Pode ser solicitado que instale o estivador durante a instalação da extensão Azure IoT Tools. Sinta-se livre para ignorá-lo.

## <a name="set-up-azure-resources"></a>Configurar recursos do Azure

Os seguintes recursos Azure são necessários para este tutorial.

* IoT Hub
* Conta de armazenamento
* Conta Azure Media Services
* Linux VM em Azure, com [tempo de execução IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) instalado

Para este arranque rápido recomendamos que utilize o [script de configuração de recursos live video analytics](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) para implementar os recursos Azure mencionados acima na sua subscrição Azure. Para tal, siga os passos abaixo:

1. Navegue para https://shell.azure.com.
1. Se esta for a primeira vez que utiliza a Cloud Shell, irá selecionar uma subscrição para criar uma conta de armazenamento e partilhar ficheiros Microsoft Azure. Selecione "Criar armazenamento" para criar uma conta de armazenamento para armazenar as informações da sessão Cloud Shell. Esta conta de armazenamento é separada da que o script irá criar para usar com a sua conta Azure Media Services.
1. Selecione "Bash" como o seu ambiente no drop-down no lado esquerdo da janela da concha.

    ![Seletor de Ambiente](./media/quickstarts/env-selector.png)

1. Execute o seguinte comando

    ```
    bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
    ```
    
Se o script estiver concluído com sucesso, deverá ver todos os recursos acima mencionados na sua subscrição. Como parte da saída do script, será gerada uma tabela de recursos que irá listar o nome do hub IoT. Procure o tipo de recurso **"Microsoft.Devices/IotHubs"** e anota o nome. Vai precisar disto no próximo passo. O script também irá gerar alguns ficheiros de configuração no ~/clouddrive/lva-sample/direy - você precisará destes mais tarde no arranque rápido.

## <a name="deploy-modules-on-your-edge-device"></a>Implementar módulos no seu dispositivo de borda

Executar o seguinte comando da Cloud Shell

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content ~/clouddrive/lva-sample/edge-deployment/deployment.amd64.json
```

O comando acima irá implantar os seguintes módulos no dispositivo de borda (o Linux VM):

* Vídeo ao vivo analytics no IoT Edge (nome do módulo "lvaEdge")
* Simulador RTSP (nome do módulo "rtspsim")

O módulo simulador RTSP simula um fluxo de vídeo ao vivo utilizando um ficheiro de vídeo armazenado que foi copiado para o seu dispositivo de borda quando executou o [script de configuração de recursos live video analytics](https://github.com/Azure/live-video-analytics/tree/master/edge/setup). Nesta fase, tem os módulos implantados, mas nenhum gráfico de mídia está ativo.

## <a name="configure-azure-iot-tools-extension-in-visual-studio-code"></a>Configurar extensão de ferramentas IoT IoT em Código de Estúdio Visual

Inicie o Código do Estúdio Visual e siga as instruções abaixo para ligar ao seu Hub Azure IoT utilizando a extensão Azure IoT Tools.

1. Navegue para o separador Explorer no Código do Estúdio Visual via **View**  >  **Explorer** ou simplesmente prima (Ctrl+Shift+E).
1. No separador Explorer, clique em "Azure IoT Hub" no canto inferior esquerdo.
1. Clique no ícone Mais Opções para ver o menu de contexto e selecione a opção "set IoT Hub Connection String".
1. Uma caixa de entrada aparecerá e, em seguida, introduzirá a sua cadeia de ligação IoT Hub. Pode obter a cadeia de ligação para o seu IoT Hub a partir de ~/clouddrive/lva-sample/appsettings.json em Cloud Shell.
1. Se a ligação for bem sucedida, será mostrada a lista de dispositivos de borda. Deve haver pelo menos um dispositivo, chamado "lva-sample-device".
1. Agora pode gerir os seus dispositivos IoT Edge e interagir com o Azure IoT Hub através do menu de contexto.
1. Pode visualizar os módulos implantados no dispositivo de borda, expandindo o nó módulos em "lva-sample-device".

    ![Nó de dispositivo de amostra de lva](./media/quickstarts/lva-sample-device-node.png)

## <a name="use-direct-methods"></a>Utilizar métodos diretos

Pode utilizar o módulo para analisar streams de vídeo ao vivo invocando métodos diretos. Leia [Métodos Diretos para Análise de Vídeo Ao Vivo no IoT Edge](direct-methods.md) para compreender todos os métodos diretos fornecidos pelo módulo. 

### <a name="invoke-graphtopologylist"></a>Invocar a Lista de Graftopologia
Isto enumera todas as [topologias de gráficos](media-graph-concept.md#media-graph-topologies-and-instances) do módulo.

1. Clique com o botão direito no módulo "LvaEdge" e selecione "Invocar o Método Direto do Módulo" a partir do menu de contexto.
1. Você verá uma caixa de edição pop no meio da janela visual Studio Code. Introduza "GraphTopologyList" na caixa de edição e prima para introduzir.
1. Em seguida, copie e cole a carga útil abaixo do JSON na caixa de edição e prima para entrar.

    ```
    {
        "@apiVersion" : "1.0"
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

Utilizando os mesmos passos que os descritos para invocar a GraphTopologyList, pode invocar o GraphTopologySet para definir uma [topologia de gráficos usando](media-graph-concept.md#media-graph-topologies-and-instances) o seguinte JSON como carga útil.

```
{
    "@apiVersion": "1.0",
    "name": "MotionDetection",
    "properties": {
        "description": "Analyzing live video to detect motion and emit events",
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
                "sensitivity": "medium",
                "inputs": [
                    {
                        "nodeName": "rtspSource"
                    }
                ]
            }
        ],
        "sinks": [
            {
                "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
                "name": "hubSink",
                "hubOutputName": "inferenceOutput",
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


A carga útil JSON acima resulta na criação de uma topologia de gráfico que define três parâmetros (dois dos quais têm valores predefinidos). A topologia tem um nó de origem (fonte RTSP), um nó de processador (processador de deteção de movimento) e um nó de pia (pia IoT Hub).

Dentro de poucos segundos, verá a seguinte resposta na janela OUTPUT:

```
[DirectMethod] Invoking Direct Method [GraphTopologySet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 201,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-19T07:41:34.507Z",
      "lastModifiedAt": "2020-05-19T07:41:34.507Z"
    },
    "name": "MotionDetection",
    "properties": {
      "description": "Analyzing live video to detect motion and emit events",
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
              "username": "${rtspUserName}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
          "sensitivity": "medium",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "hubOutputName": "inferenceOutput",
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

O estatuto devolvido é 201, indicando que foi criada uma nova topologia. Experimente os seguintes passos:

* Volte a invocar o GraphTopologySet e note que o código de estado devolvido é de 200. O código de estado 200 indica que uma topologia existente foi atualizada com sucesso.
* Volte a invocar o GraphTopologySet, mas altere a cadeia de descrição. Note que o código de estado na resposta é 200 e a descrição é atualizada para o novo valor.
* Invoque a GraphTopologyList como delineado na secção anterior e note que agora pode ver a topologia "MotionDetection" na carga útil devolvida.

### <a name="invoke-graphtopologyget"></a>Invocar GrafTopologyGet

Agora invoque o GraphTopologyGet com a seguinte carga útil

```

{
    "@apiVersion" : "1.0",
    "name" : "MotionDetection"
}
```

Dentro de alguns segundos, deverá ver a seguinte resposta na janela OUTPUT:

```
[DirectMethod] Invoking Direct Method [GraphTopologyGet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-19T07:41:34.507Z",
      "lastModifiedAt": "2020-05-19T07:41:34.507Z"
    },
    "name": "MotionDetection",
    "properties": {
      "description": "Analyzing live video to detect motion and emit events",
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
              "username": "${rtspUserName}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
          "sensitivity": "medium",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "hubOutputName": "inferenceOutput",
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

Note o seguinte na carga útil de resposta:

* O código de estado é 200, indicando sucesso.
* A carga útil tem a "criação" e a última meta de tempo "última Modificada".

### <a name="invoke-graphinstanceset"></a>Invocar GraphInstanceSet

Em seguida, crie uma instância de gráfico que faz referência à topologia do gráfico acima. Como [explicado aqui](media-graph-concept.md#media-graph-topologies-and-instances), os exemplos de gráficos permitem analisar streams de vídeo ao vivo de muitas câmaras com a mesma topologia de gráficos.

Invoque o método direto GraphInstanceSet com a seguinte carga útil.

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1",
    "properties" : {
        "topologyName" : "MotionDetection",
        "description" : "Sample graph description",
        "parameters" : [
            { "name" : "rtspUrl", "value" : "rtsp://rtspsim:554/media/camera-300s.mkv" }
        ]
    }
}
```

Tenha em atenção o seguinte:

* A carga útil acima especifica o nome de topologia (MotionDetection) para o qual o caso precisa de ser criado.
* A carga útil contém o valor do parâmetro para "rtspUrl", que não tinha um valor padrão na carga útil da topologia do gráfico.

Em poucos segundos, verá a seguinte resposta na janela OUTPUT:

```
[DirectMethod] Invoking Direct Method [GraphInstanceSet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 201,
  "payload": {
    "name": "Sample-Graph-1",
    "properties": {
      "created": "2020-05-19T07:44:33.868Z",
      "lastModified": "2020-05-19T07:44:33.868Z",
      "state": "Inactive",
      "description": "Sample graph description",
      "topologyName": "MotionDetection",
      "parameters": [
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
        }
      ]
    }
  }
}
```

Note o seguinte na carga útil de resposta:

* O código de estado é 201, indicando que foi criado um novo caso.
* O Estado é "Inativo", indicando que a instância do gráfico foi criada mas não ativada. Para mais informações, consulte [os estados do gráfico de mídia.](media-graph-concept.md)

Experimente os seguintes passos:

* Invoque o GraphInstanceSet novamente com a mesma carga útil e note que o código de estado devolvido é agora de 200.
* Volte a invocar o GraphInstanceSet, mas com uma descrição diferente e note que a descrição atualizada na carga útil de resposta, indicando que a instância do gráfico foi atualizada com sucesso.
* Invoque o GraphInstanceSet mas altere o nome para "Sample-Graph-2" e observe a carga útil de resposta. Note que é criada uma nova instância de gráfico (isto é, código de estado é 201).

### <a name="invoke-graphinstanceactivate"></a>Invocar GraphInstanceActivate

Agora ative a instância do gráfico - que inicia o fluxo de vídeo ao vivo através do módulo. Invoque o método direto GraphInstanceActivar com a seguinte carga útil.

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1"
}
```

Em poucos segundos, deverá ver a seguinte resposta na janela OUTPUT:

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
     "@apiVersion" : "1.0",
     "name" : "Sample-Graph-1"
 }
 ```

Em poucos segundos, deverá ver a seguinte resposta na janela OUTPUT:

```
[DirectMethod] Invoking Direct Method [GraphInstanceGet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": {
    "name": "Sample-Graph-1",
    "properties": {
      "created": "2020-05-19T07:44:33.868Z",
      "lastModified": "2020-05-19T07:44:33.868Z",
      "state": "Active",
      "description": "graph description",
      "topologyName": "MotionDetection",
      "parameters": [
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
        }
      ]
    }
  }
}
```

Note o seguinte na carga útil de resposta:

* O código de estado é 200, indicando sucesso.
* O Estado é "Ativo", indicando que a instância do gráfico está agora no estado "Ativo".

## <a name="observe-results"></a>Observar resultados

A instância de gráfico que criamos e ativamos acima usa o nó do processador de deteção de movimento para detetar movimento na transmissão de vídeo ao vivo e envia eventos para o nó de pia IoT Hub. Estes eventos são então retransmitidos para o seu IoT Edge Hub, que agora pode ser observado. Para tal, siga estes passos.

1. Abra o painel Explorer no Código do Estúdio Visual e procure o Azure IoT Hub no canto inferior esquerdo.
2. Expandir o nó dispositivos.
3. Clink direito no dispositivo de amostra de Lva e escolheu a opção "Iniciar a monitorização de eventos incorporados".

![Comece a monitorizar eventos do Iot Hub](./media/quickstarts/start-monitoring-iothub-events.png)

Verá as seguintes mensagens na janela OUTPUT:

```
[IoTHubMonitor] [7:44:33 AM] Message received from [lva-sample-device/lvaEdge]:
{
    "body": {
    "timestamp": 143005362606360,
    "inferences": [
        {
        "type": "motion",
        "motion": {
            "box": {
            "l": 0.828452,
            "t": 0.455224,
            "w": 0.1,
            "h": 0.088889
            }
        }
        },
        {
        "type": "motion",
        "motion": {
            "box": {
            "l": 0.661088,
            "t": 0.597015,
            "w": 0.0625,
            "h": 0.051852
            }
        }
        }
    ]
    },
    "applicationProperties": {
    "topic": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/microsoft.media/mediaservices/{amsAccountName}",
    "subject": "/graphInstances/Sample-Graph-1/processors/motionDetection",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-05-19T07:45:34.404Z",
    "dataVersion": "1.0"
    }
}
```

Note o seguinte na mensagem acima

* A mensagem contém uma secção "body" e uma secção "aplicaçõesProperties". Para compreender o que estas secções representam, leia o artigo [Criar e Ler mensagem IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct).
* "sujeito" na aplicaçãoDeproperias refere o nó no MediaGraph a partir do qual a mensagem foi gerada. Neste caso, a mensagem é originária do processador de deteção de movimentos.
* "eventType" na aplicaçãoProperties indica que este é um evento Analytics.
* "eventTime" indica a hora em que o evento ocorreu.
* "body" contém dados sobre o evento de análise. Neste caso, o evento é um evento de Inferência e, por isso, o corpo contém dados de "timetamp" e "inferências".
* A secção "inferências" indica que o "tipo" é "movimento" e tem dados adicionais sobre o evento "movimento".

Se deixar o MediaGraph funcionar durante algum tempo, verá também a seguinte mensagem na janela saída:

```
[IoTHubMonitor] [7:47:45 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1588948185746703 1 IN IP4 172.xx.xx.xx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets={SPS}\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-05-19T07:47:45.747Z"
  }
}
```

Note o seguinte na mensagem acima

* "sujeito" na aplicação As propriedades indicam que a mensagem foi gerada a partir do nó de origem RTSP no gráfico de mídia.
* "eventType" na aplicaçãoProperties indica que este é um evento de Diagnóstico.
* "body" contém dados sobre o evento de diagnóstico. Neste caso, o evento é MediaSessionEstablished e, portanto, o corpo.

## <a name="invoke-additional-direct-methods-to-clean-up"></a>Invocar métodos diretos adicionais para limpar

Agora, invocar métodos diretos para desativar e apagar a instância do gráfico (por esta ordem).

### <a name="invoke-graphinstancedeactivate"></a>Invocar GraphInstanceDeactivo

Invocar o método direto GraphInstanceDeactivar com a seguinte carga útil.

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1"
}
```

Em poucos segundos, deverá ver a seguinte resposta na janela OUTPUT:

```
[DirectMethod] Invoking Direct Method [GraphInstanceDeactivate] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

O código de estado de 200 indica que a instância do gráfico foi desativada com sucesso.

Tente o seguinte, como próximos passos.

* Invocar o GraphInstanceGet como indicado nas secções anteriores e observar o valor "estado".

### <a name="invoke-graphinstancedelete"></a>Invocar GraphInstanceDelete

Invocar o método direto GraphInstanceDelete com a seguinte carga útil

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1"
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
    "@apiVersion" : "1.0",
    "name" : "MotionDetection"
}
```

Em poucos segundos, deverá ver a seguinte resposta na janela OUTPUT:

```
[DirectMethod] Invoking Direct Method [GraphTopologyDelete] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

O código de estado de 200 indica que a topologia do gráfico foi eliminada com sucesso.

Tente os próximos passos.

* Invoque a GraphTopologyList e observe que não existem topologias de gráficos no módulo.
* Invoque a GraphInstanceList com a mesma carga útil que a GraphTopologyList e observe que não existem instâncias de gráfico enumeradas.

## <a name="clean-up-resources"></a>Limpar recursos

Se não vai continuar a utilizar esta aplicação, elimine os recursos criados neste quickstart.

## <a name="next-steps"></a>Passos seguintes

* Saiba como gravar vídeo usando live video analytics no IoT Edge
* Saiba mais sobre mensagens de diagnóstico.
