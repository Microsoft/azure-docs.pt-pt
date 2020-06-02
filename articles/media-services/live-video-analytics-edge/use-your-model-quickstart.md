---
title: Analise o vídeo ao vivo usando o seu próprio modelo - Azure
description: Neste arranque rápido, aplicará visão computacional para analisar o feed de vídeo ao vivo a partir de uma câmara IP (simulada).
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: 0b502fb4bcfa3a11890167c5ef297463a3c51cdc
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/01/2020
ms.locfileid: "84261973"
---
# <a name="quickstart-analyze-live-video-with-your-own-model"></a>Quickstart: Analise o vídeo ao vivo com o seu próprio modelo

Este quickstart mostra-lhe como usar o Live Video Analytics no IoT Edge para analisar o feed de vídeo ao vivo a partir de uma câmara IP (simulada) aplicando um modelo de visão computacional para detetar objetos. Um subconjunto dos quadros do feed de vídeo ao vivo é enviado para um serviço de inferência, e os seus resultados são enviados para o IoT Edge Hub. Utiliza um Azure VM como um dispositivo IoT Edge e um vídeo simulado ao vivo. Este artigo baseia-se no código de amostra escrito em C#.

Este artigo baseia-se [neste](detect-motion-emit-events-quickstart.md) arranque rápido. 

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Código de Estúdio Visual](https://code.visualstudio.com/) na sua máquina com as seguintes extensões:
    * [Ferramentas do Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1) instalado no seu sistema
* Se ainda não tiver concluído [este](detect-motion-emit-events-quickstart.md) arranque rápido, termine os seguintes passos:
     * [Configurar recursos do Azure](detect-motion-emit-events-quickstart.md#set-up-azure-resources)

> [!TIP]
> Ao instalar as Ferramentas Azure IoT, poderá ser solicitado a instalar o estivador. Sinta-se livre para ignorá-lo.

## <a name="review-the-sample-video"></a>Reveja o vídeo da amostra
Como parte dos passos acima para configurar os recursos Azure, um (curto) vídeo de um tráfego de autoestrada será copiado para o Linux VM em Azure sendo usado como o dispositivo IoT Edge. Este ficheiro de vídeo será usado para simular um live stream para este tutorial.

Pode utilizar uma aplicação como [vLC Player,](https://www.videolan.org/vlc/)lançá-la, acertar Control+N e colar [este](https://lvamedia.blob.core.windows.net/public/camera-300s.mkv) link ao vídeo para iniciar a reprodução. Verá que as imagens são de trânsito numa autoestrada, com muitos veículos a moverem-se sobre ela.

Quando completar os passos abaixo, terá usado o Live Video Analytics no IoT Edge para detetar objetos como veículos, pessoas, etc., e publicar eventos de inferência associados ao IoT Edge Hub.

## <a name="overview"></a>Descrição geral

![Descrição geral](./media/quickstarts/overview-qs5.png)

O diagrama acima mostra como os sinais fluem neste arranque rápido. Um módulo de borda (detalhado [aqui)](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)simula uma câmara IP que hospeda um servidor RTSP. Um nó [de fonte RTSP](media-graph-concept.md#rtsp-source) puxa o feed de vídeo deste servidor e envia quadros de vídeo para o nó do processador do filtro do destino do [quadro.](media-graph-concept.md#frame-rate-filter-processor) Este processador limita a taxa de fotogramas do fluxo de vídeo que atinge o nó [do processador de extensão HTTP.](media-graph-concept.md#http-extension-processor) 

O nó de extensão HTTP desempenha o papel de um proxy, convertendo os quadros de vídeo para o tipo de imagem especificado e retransmitindo a imagem sobre REST para outro módulo Edge que executa um modelo de IA atrás de um ponto final HTTP. Neste exemplo, este módulo Edge é construído usando o modelo [YOLOv3,](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) que é capaz de detetar muitos tipos de objetos. O nó do processador de extensão HTTP recolhe os resultados de deteção e publica eventos no nó [de pia IoT Hub,](media-graph-concept.md#iot-hub-message-sink ) que depois envia esses eventos para o [IoT Edge Hub](../../iot-edge/iot-edge-glossary.md#iot-edge-hub).

Neste arranque rápido, você vai:

1. Criar e implementar o gráfico de mídia.
1. Interprete os resultados.
1. Limpar recursos.



## <a name="create-and-deploy-the-media-graph"></a>Criar e implementar o gráfico de mídia
    
### <a name="examine-and-edit-the-sample-files"></a>Examine e edite os ficheiros de amostra

Como parte dos requisitos prévios, teria descarregado o código de amostra para uma pasta. Lançar Código de Estúdio Visual e abrir a pasta.

1. No Código do Estúdio Visual, navegue por "src/edge". Verá o ficheiro .env que criou juntamente com alguns ficheiros de modelos de implementação.

    * O modelo de implantação refere-se ao manifesto de implantação do dispositivo de borda com alguns valores de espaço reservado. O ficheiro .env tem os valores para essas variáveis.
1. Em seguida, navegue para a pasta "src/cloud-to-device-console-app". Aqui verá o ficheiro appsettings.json que criou juntamente com alguns outros ficheiros:

    * c2d-console-app.csproj - Este é o ficheiro do projeto para Visual Studio Code.
    * operations.json - Este ficheiro irá listar as diferentes operações que gostaria que o programa fosse executado.
    * Program.cs - Este é o código do programa de amostra, que faz o seguinte:

        * Carrega as definições da aplicação.
        *  Invoca métodos diretos expostos pelo live video analytics no módulo IoT Edge. Pode utilizar o módulo para analisar streams de vídeo ao vivo invocando os seus [métodos diretos](direct-methods.md) 
        * Pausas para examinar a saída do programa na janela TERMINAL e os eventos gerados pelo módulo na janela OUTPUT
        * Invoca métodos diretos para limpar recursos   


1. Faça as seguintes edições para o ficheiro operations.json
    * Altere a ligação para a topologia do gráfico:`"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtension/topology.json"`
    * Em GraphInstanceSet, edite o nome da topologia do gráfico para corresponder ao valor no link acima`"topologyName" : "InferencingWithHttpExtension"`
    * Sob GraphTopologyDelete, edite o nome`"name": "InferencingWithHttpExtension"`

### <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Gerar e implementar o manifesto de implantação IoT Edge

1. Clique no ficheiro "src/edge/ deployment.yolov3.template.json" e clique no Manifesto de Implementação de Arestas de IoT.

    ![Gerar manifesto de implantação de borda ioT](./media/quickstarts/generate-iot-edge-deployment-manifest-yolov3.png)  
1. Isto deve criar um ficheiro manifesto na pasta src/edge/config chamada " deployment.yolov3.amd64.json".
1. Se já tiver completado o [quickstart,](detect-motion-emit-events-quickstart.md)então salte este passo. Caso contrário, desagreda a cadeia de ligação IoTHub clicando no ícone "Mais ações" ao lado do painel AZURE IOT HUB no canto inferior esquerdo. Pode copiar a cadeia a partir do ficheiro appsettings.json. (Aqui está outra abordagem recomendada para garantir que você tem o hub IoT adequado configurado dentro do Código de Estúdio Visual através do [comando Select Iot Hub](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub)).
    
    ![Cadeia de conexão IoTHub](./media/quickstarts/set-iotconnection-string.png)
1. Em seguida, clique à direita em "src/edge/config/ deployment.yolov3.amd64.json" e clique em "Criar implementação para dispositivo único". 

    ![Criar implementação para dispositivo único](./media/quickstarts/create-deployment-single-device.png)
1. Em seguida, ser-lhe-á pedido que selecione um dispositivo IoT Hub. Selecione o dispositivo de amostra de Lva a partir do drop-down.
1. Em cerca de 30 segundos, refresque o Azure IOT Hub na secção inferior esquerda e deverá ter o dispositivo de borda com os seguintes módulos implantados:

    1. O módulo Live Video Analytics, nomeado como "LvaEdge".
    1. Um módulo chamado "rtspsim" que simula um Servidor RTSP, atuando como a fonte de um feed de vídeo ao vivo.
    1. Um módulo chamado "yolov3" que, como o nome sugere, é o modelo de deteção de objetos YOLOv3 que aplica visão computacional às imagens e devolve várias classes de tipos de objetos.
 
        ![Modelo de deteção de objetos YOLOv3](./media/quickstarts/yolov3.png)

### <a name="prepare-for-monitoring-events"></a>Preparar para eventos de monitorização

Clique à direita no dispositivo Live Video Analytics e clique em "Iniciar monitorização do ponto final do evento incorporado". Este passo é necessário para monitorizar os eventos do IoT Hub e vê-lo na janela de saída do Código do Estúdio Visual. 

![Iniciar a monitorização](./media/quickstarts/start-monitoring-iothub-events.png) 

### <a name="run-the-sample-program"></a>Executar o programa de amostragem

1. Inicie uma sessão de depurar (acerte F5). Começará a ver algumas mensagens impressas na janela TERMINAL.
1. O operations.json começa com chamadas para os métodos diretos GraphTopologyList e GraphInstanceList. Se tiver limpo recursos após o início rápido anterior, isto devolverá listas vazias e, em seguida, fará uma pausa para que você possa entrar
   ```
   --------------------------------------------------------------------------
   Executing operation GraphTopologyList
   -----------------------  Request: GraphTopologyList  --------------------------------------------------
   {
   "@apiVersion": "1.0"
   }
   ---------------  Response: GraphTopologyList - Status: 200  ---------------
   {
   "value": []
   }
   --------------------------------------------------------------------------
   Executing operation WaitForInput
   Press Enter to continue
   ```

1. Quando premir a tecla "Entrar" na janela TERMINAL, é feito o próximo conjunto de chamadas de métodos diretos
     * Uma chamada para GraphTopologySet usando o topologyUrl acima
     * Uma chamada para o GraphInstanceSet usando o seguinte corpo
     ```
     {
       "@apiVersion": "1.0",
       "name": "Sample-Graph-1",
       "properties": {
         "topologyName": "InferencingWithHttpExtension",
         "description": "Sample graph description",
         "parameters": [
           {
             "name": "rtspUrl",
             "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
           },
           {
             "name": "rtspUserName",
             "value": "testuser"
           },
           {
             "name": "rtspPassword",
             "value": "testpassword"
           }
         ]
       }
     }
     ```
     * Uma chamada para o GraphInstanceActivate para iniciar a instância do gráfico e iniciar o fluxo de vídeo
     * Uma segunda chamada para o GraphInstanceList para mostrar que a instância do gráfico está, de facto, no estado de execução
1. A saída na janela TERMINAL para agora numa introdução de "Press Enter para continuar". Não bata em "Enter" neste momento. Pode rolar para cima para ver as cargas de resposta JSON para os métodos diretos que invocou
1. Se agora mudar para a janela OUTPUT no Código do Estúdio Visual, verá mensagens que estão a ser enviadas para o IoT Hub, pelo módulo Live Video Analytics no módulo IoT Edge.
     * Estas mensagens são discutidas na secção abaixo
1. O gráfico de mídia continuará a ser executado e os resultados de impressão – o simulador RTSP continuará a fazer loop no vídeo de origem. Para parar o gráfico mediático, volte para a janela TERMINAL e bata em "Enter". A próxima série de chamadas são feitas para limpar recursos:
     * Uma chamada para GraphInstanceDeactivar para desativar a instância do gráfico
     * Uma chamada para o GraphInstanceDelete para apagar o caso
     * Uma chamada para GraphTopologyDelete para apagar a topologia
     * Uma chamada final para a GraphTopologyList para mostrar que a lista está agora vazia

## <a name="interpret-results"></a>Interpretar os resultados

Quando executou o gráfico de mídia, os resultados do nó do processador de extensão http são enviados através do nó de pia IoT Hub para o IoT Hub. As mensagens que vê na janela OUTPUT do Código do Estúdio Visual contêm uma secção "body" e uma secção "aplicaçõesProperties". Para entender o que estas secções representam, leia [este](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct) artigo.

Nas mensagens abaixo, as propriedades da aplicação e o conteúdo do corpo são definidos pelo módulo Live Video Analytics. 



### <a name="mediasession-established-event"></a>Evento MediaSession Established

Quando um gráfico de mídia é instantâneo, o nó de origem RTSP tenta ligar-se ao servidor RTSP em execução no recipiente rtspsim-live55. Se for bem sucedido, vai imprimir este evento. O tipo de evento é Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished.

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

Note o seguinte na mensagem acima:
* A mensagem é um evento de Diagnóstico, MediaSessionEstablished, indica que o nó de origem RTSP (o sujeito) foi capaz de estabelecer a ligação com o simulador RTSP, e começar a receber um feed ao vivo (simulado).
* "sujeito" na aplicação As propriedades indicam que a mensagem foi gerada a partir do nó de origem RTSP no gráfico de mídia.
* "eventType" na aplicaçãoProperties indica que este é um evento de Diagnóstico.
* "eventTime" indica a hora em que o evento ocorreu.
* "body" contém dados sobre o evento de diagnóstico, que, neste caso, é o [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) detalhes.

### <a name="inference-event"></a>Evento de inferência

O nó do processador de extensão HTTP recebe resultados de inferência do módulo yolov3, e emite-os através do nó de pia IoT Hub como eventos de Inferência. Nestes eventos, o tipo é definido para "entidade" para indicar que é uma entidade como um carro ou caminhão, e o eventTime diz-lhe a que hora (UTC) o objeto foi detetado. Abaixo está um exemplo onde dois carros foram detetados com diferentes níveis de confiança na mesma moldura de vídeo.

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
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/inferenceClient",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-04-23T06:37:16.097Z"
  }
}
```

Note o seguinte nas mensagens acima:

* "sujeito" na aplicaçãoDeproperias refere o nó na topologia do gráfico a partir do qual a mensagem foi gerada. 
* "eventType" na aplicaçãoProperties indica que este é um evento Analytics.
* "eventTime" indica a hora em que o evento ocorreu.
* "body" contém dados sobre o evento de análise. Neste caso, o evento é um evento de Inferência e, portanto, o corpo contém dados de "inferências".
* A secção "inferências" indica que o "tipo" é "entidade" e tem dados adicionais sobre a "entidade".

## <a name="clean-up-resources"></a>Limpar recursos

Se pretende experimentar os outros quickstarts, deve manter os recursos criados. Caso contrário, vá ao portal Azure, navegue pelos seus grupos de recursos, selecione o grupo de recursos sob o qual executou este arranque rápido e elimine todos os recursos.

## <a name="next-steps"></a>Passos seguintes

Rever desafios adicionais para utilizadores avançados:

* Utilize uma [câmara IP](https://en.wikipedia.org/wiki/IP_camera) com suporte para RTSP em vez de utilizar o simulador RTSP. Pode pesquisar por câmaras IP com suporte RTSP na página de produtos [conformantes ONVIF,](https://www.onvif.org/conformant-products/) procurando dispositivos que estejam em conformidade com os perfis G, S ou T.
* Utilize um dispositivo Linux AMD64 ou X64 (vs. utilizando um VM Azure Linux). Este dispositivo deve estar na mesma rede que a câmara IP. Pode seguir as instruções no [tempo de funcionamento do Azure IoT Edge no Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) e, em seguida, seguir as instruções [neste, implementar o seu primeiro módulo IoT Edge para um dispositivo Linux virtual](https://docs.microsoft.com/azure/iot-edge/quickstart-linux) para registar o dispositivo com o Azure IoT Hub.

