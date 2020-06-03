---
title: Gravação de vídeo baseada em eventos para nuvem e reprodução de tutorial em nuvem - Azure
description: Neste tutorial, você aprenderá a usar live video analytics no IoT Edge para executar uma gravação de vídeo baseada em eventos para cloud e reprodução a partir da nuvem.
ms.topic: tutorial
ms.date: 05/27/2020
ms.openlocfilehash: 92367634a2f5785ecbb102db1e03f3d5f12d744e
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/02/2020
ms.locfileid: "84300846"
---
# <a name="tutorial-event-based-video-recording-to-cloud-and-playback-from-cloud"></a>Tutorial: Gravação de vídeo baseada em eventos para nuvem e reprodução da nuvem

Neste tutorial, você aprenderá a usar live video analytics no IoT Edge para gravar seletivamente partes de uma fonte de vídeo ao vivo para media Services na nuvem. Este caso de utilização é referido como gravação de vídeo baseada em [eventos](event-based-video-recording-concept.md) (EVR) neste tutorial. Para isso, utilizará um modelo de IA de deteção de objetos para procurar objetos no vídeo e gravará clips de vídeo apenas quando um determinado tipo de objeto for detetado. Também aprenderá como reproduzir os videoclips gravados utilizando os Media Services. Isto é útil para uma variedade de cenários onde há necessidade de manter um arquivo de videoclips de interesse.

> [!div class="checklist"]
> * Configurar os recursos relevantes
> * Examine o código que executa o EVR
> * Executar o código de amostra
> * Examine os resultados e veja o vídeo

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Pré-leitura sugerida  

Recomenda-se que leia as seguintes páginas de documentação

* [Análise de vídeo ao vivo na visão geral do IoT Edge](overview.md)
* [Análise de vídeo ao vivo na terminologia IoT Edge](terminology.md)
* [Conceitos de gráficos de mídia](media-graph-concept.md) 
* [Gravação de vídeo baseada em eventos](event-based-video-recording-concept.md)
* [Tutorial: desenvolvimento de um módulo IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-develop-for-linux)
* [Como editar a implementação.*.template.json](https://github.com/microsoft/vscode-azure-iot-edge/wiki/How-to-edit-deployment.*.template.json)
* Secção sobre [como declarar rotas no manifesto de implantação do IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes)

## <a name="prerequisites"></a>Pré-requisitos

Os pré-requisitos para este tutorial são os seguintes

* [Código de Estúdio Visual](https://code.visualstudio.com/) na sua máquina de desenvolvimento com extensão [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) e extensão [C#.](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)

    > [!TIP]
    > Pode ser solicitado a instalar o Docker. Pode ignorar esta solicitação.
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/thank-you/sdk-3.1.201-windows-x64-installer) na sua máquina de desenvolvimento.
* Complete [o roteiro de configuração de recursos de vídeo ao vivo](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) e [configurar o ambiente](https://review.docs.microsoft.com/en-us/azure/media-services/live-video-analytics-edge/detect-motion-emit-events-quickstart?branch=release-preview-media-services-lva#set-up-the-environment)

No final dos passos acima, terá certos recursos Azure implantados na subscrição do Azure, incluindo:

* IoT Hub
* Conta de armazenamento
* Conta Azure Media Services
* Linux VM em Azure, com [tempo de execução IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) instalado

## <a name="concepts"></a>Conceitos

A gravação de vídeo baseada em eventos (EVR) refere-se ao processo de gravação de vídeo desencadeado por um evento. Este evento poderia ser gerado a partir do processamento do próprio sinal de vídeo (por exemplo, ao detetar um objeto em movimento no vídeo) ou de uma fonte independente (por exemplo, abertura de uma porta). Em alternativa, só pode ativar a gravação quando um serviço de inferenculação detetar que ocorreu um evento específico.  Neste tutorial você usará um vídeo de veículos movendo-se em uma autoestrada, e gravar vídeos de vídeo sempre que um caminhão é detetado.

![Grafo do suporte de dados](./media/event-based-video-recording-tutorial/overview.png)

O diagrama acima é uma representação pictórica de um [gráfico mediático](media-graph-concept.md) e módulos adicionais que realizam o cenário desejado. Existem quatro módulos IoT Edge envolvidos:

* Vídeo ao vivo analytics no módulo IoT Edge.
* Um módulo Edge a executar um modelo de IA atrás de um ponto final HTTP. Este módulo de IA utiliza o modelo [YOLOv3,](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) que é capaz de detetar muitos tipos de objetos.
* Um módulo personalizado para contar e filtrar objetos (designado contador de objetos no diagrama acima) que irá construir e implantar neste tutorial.
* Um [módulo de simulador RTSP](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) para simular uma câmara RTSP.
    
    Como mostra o diagrama, você usará um nó [de fonte RTSP](media-graph-concept.md#rtsp-source) no gráfico de mídia para capturar o vídeo simulado ao vivo (de tráfego numa autoestrada), e enviar esse vídeo para dois caminhos.

* O primeiro caminho é para um nó [de processador de filtro de taxa de fotogramas](media-graph-concept.md#frame-rate-filter-processor) que produz quadros de vídeo à taxa de fotogramas especificada (reduzida). Estes quadros de vídeo são enviados para um nó de extensão HTTP, que depois retransmite os quadros (como imagens) para o módulo AI (YOLO v3 – que é um detetor de objetos) e recebe resultados – que serão os objetos (veículos no trânsito) detetados pelo modelo. O nó de extensão HTTP publica então os resultados através do nó de pia de mensagem IoT Hub para o IoT Edge Hub.
* O módulo de contador de objetos está configurado para receber mensagens do IoT Edge Hub – que incluem os resultados de deteção de objetos (veículos no trânsito). Verifica estas mensagens à procura de objetos de um determinado tipo (configurados através de uma definição). Quando tal objeto é encontrado, este módulo envia uma mensagem para ioT Edge Hub. Essas mensagens "objeto encontrado" são então encaminhada para o nó de origem IoT Hub do gráfico de mídia. Ao receber tal mensagem, o nó de origem IoT Hub no gráfico de mídia aciona o nó do processador do portão de [sinal,](media-graph-concept.md#signal-gate-processor) fazendo com que este abra por um período de tempo configurado. O vídeo flui através do portão para o nó da pia do ativo durante esse período. Essa parte do live stream é então gravada através do nó [da pia](media-graph-concept.md#asset-sink) do ativo para um [ativo](terminology.md#asset) na sua conta Azure Media Service.

## <a name="set-up-your-development-environment"></a>Configurar o ambiente de desenvolvimento

Antes de começar, verifique se completou a 3ª bala em [Pré-requisitos.](#prerequisites) Assim que o script de configuração do recurso terminar, clique nos suportes encaracolados para expor a estrutura da pasta. Você verá alguns ficheiros criados sob o diretório de amostras ~/clouddrive/lva.

![Definições da aplicação](./media/quickstarts/clouddrive.png)

De interesse neste tutorial são:

* ~/clouddrive/lva-sample/edge-deployment/.env - contém propriedades que o Código do Estúdio Visual utiliza para implantar módulos num dispositivo de borda.
* ~/clouddrive/lva-sample/appsetting.json - usado pelo Código do Estúdio Visual para executar o código de amostra.

Vai precisar destes ficheiros para os passos abaixo.

1. Clone o repo https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp daqui.
1. Lance o Código do Estúdio Visual e abra a pasta onde descarregou o repo.
1. No Visual Studio Code, navegue para a pasta "src/cloud-to-device-console-app" e crie um ficheiro chamado "appsettings.json". Este ficheiro conterá as definições necessárias para executar o programa.
1. Copie o conteúdo do ficheiro ~/clouddrive/lva-sample/appsettings.json. O texto deve parecer:

    ```
    {  
        "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",  
        "deviceId" : "lva-sample-device",  
        "moduleId" : "lvaEdge"  
    }
    ```

    A cadeia de ligação IoT Hub permite-lhe utilizar o Código do Estúdio Visual para enviar comandos para os módulos Edge via Azure IoT Hub.
    
1. Em seguida, navegue na pasta "src/edge" e crie um ficheiro chamado ".env".
1. Copie o conteúdo do ficheiro ~/clouddrive/lva-sample/.env. O texto deve parecer:

    ```
    SUBSCRIPTION_ID="<Subscription ID>"  
    RESOURCE_GROUP="<Resource Group>"  
    AMS_ACCOUNT="<AMS Account ID>"  
    IOTHUB_CONNECTION_STRING="HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx"  
    AAD_TENANT_ID="<AAD Tenant ID>"  
    AAD_SERVICE_PRINCIPAL_ID="<AAD SERVICE_PRINCIPAL ID>"  
    AAD_SERVICE_PRINCIPAL_SECRET="<AAD SERVICE_PRINCIPAL ID>"  
    INPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"  
    OUTPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/output"  
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry username>"      
    ```

## <a name="examine-the-template-file"></a>Examine o arquivo do modelo 

No passo anterior, terá lançado o Visual Studio Code e aberto a pasta que contém o código de amostra.

No Código do Estúdio Visual, navegue por "src/edge". Verá o ficheiro .env que criou, bem como alguns ficheiros de modelos de implementação. Este modelo define quais os módulos de borda que irá implantar no dispositivo de borda (o Azure Linux VM). O ficheiro .env contém valores para as variáveis utilizadas nestes modelos, como as credenciais do Media Service.

Abra "src/edge/deployment.objectCounter.template.json". Note que existem quatro entradas na secção "módulos" – correspondentes aos itens acima indicados (na secção Conceitos):

* IvaEdge – este é o live video analytics no módulo IoT Edge
* yolov3 – este é o módulo de IA construído com o modelo YOLO v3
* rtspsim – este é o simulador RTSP
* objectCounter – este é o módulo que procura objetos específicos nos resultados do yolov3

Para o módulo objectCounter, consulte a cadeia (${MODULES.objectCounter}) utilizada para o valor "imagem" – baseia-se no [tutorial](https://docs.microsoft.com/azure/iot-edge/tutorial-develop-for-linux) no desenvolvimento de um módulo IoT Edge. O Código do Estúdio Visual reconhecerá automaticamente que o código do módulo contador de objetos está em "src/edge/modules/objectCounter". 

Leia [esta](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes) secção sobre como declarar rotas no manifesto de implantação IoT Edge e, em seguida, examine as rotas no ficheiro JSON do modelo. Note como:

* O LVAToObjectCounter é utilizado para enviar eventos específicos para um ponto final específico no módulo objectCounter.
* ObjectCounterToLVA é utilizado para enviar um evento de gatilho para um ponto final específico (que deve ser o nó IoT Hub Source) no módulo LvaEdge.
* objectCounterToIoTHub é usado como uma ferramenta de depurador, para ajudá-lo a ver a saída do objectCounter quando executar este tutorial.

> [!NOTE]
> Verifique as propriedades desejadas para o módulo objectCounter, que são configurados para procurar objetos que estejam marcados como "caminhão", com um nível de confiança de pelo menos 50%.

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Gerar e implementar o manifesto de implantação IoT Edge 

O manifesto de implantação define quais os módulos que são implantados num dispositivo de borda e configurações de configuração para esses módulos. Siga estes passos para gerar tal manifesto a partir do ficheiro do modelo e, em seguida, implante-o para o dispositivo de borda.

Utilizando o Código do Estúdio Visual, siga [estas](https://docs.microsoft.com/azure/iot-edge/tutorial-develop-for-linux#build-and-push-your-solution) instruções para iniciar sessão em estivador e "Construa e empurre a solução IoT Edge" mas use src/edge/deployment.objectCounter.template.json para este passo.

![Construa e empurre a solução IoT Edge](./media/event-based-video-recording-tutorial/build-push.png)

Isto irá construir o módulo objectCounter para a contagem de objetos e empurrar a imagem para o seu Registo de Contentores Azure (ACR).

* Verifique se tem as variáveis ambientais CONTAINER_REGISTRY_USERNAME_myacr e CONTAINER_REGISTRY_PASSWORD_myacr definidas no ficheiro .env.

O passo acima irá criar o manifesto de implantação IoT Edge em src/edge/config/deployment.objectCounter.amd64.json. Clique no direito nesse ficheiro e clique em "Criar implementação para dispositivo único".

![Criar implementação para dispositivo único](./media/quickstarts/create-deployment-single-device.png)

Se este for o seu primeiro tutorial com Live Video Analytics no IoT Edge, o Visual Studio Code irá instruí-lo a inserir a cadeia de ligação IoTHub. Pode copiá-lo a partir do ficheiro appsettings.json.

Em seguida, o Código do Estúdio Visual irá pedir-lhe para selecionar um dispositivo de hub IoT. Selecione o seu dispositivo IoT Edge (deve ser "lva-sample-device").

Nesta fase, iniciou-se a implantação de módulos de borda para o seu dispositivo IoT Edge.
Em cerca de 30 segundos, refresque o Azure IoT Hub na secção inferior esquerda no Código do Estúdio Visual, e deve ver que existem 4 módulos implantados (note novamente os nomes: lvaEdge, rtspsim, yolov3 e objectCounter).

![4 módulos implantados](./media/event-based-video-recording-tutorial/iot-hub.png)

## <a name="prepare-for-monitoring-events"></a>Preparar para eventos de monitorização

Para ver os eventos do módulo de contador de objetos e do Live Video Analytics no módulo IoT Edge, siga estes passos:

1. Abra o painel Explorer no Código do Estúdio Visual e procure o Azure IoT Hub no canto inferior esquerdo.
1. Expandir o nó dispositivos.
1. Clink direito no dispositivo de amostra de Lva e escolheu a opção **Iniciar a monitorização de eventos incorporados**.

![Comece a monitorizar o ponto final do evento incorporado](./media/quickstarts/start-monitoring-iothub-events.png)

## <a name="run-the-program"></a>Execute o programa

1. Código do Estúdio Visual, navegue para "src/cloud-to-device-console-app/operations.json"

1. Sob o nó GráficoToplogiaSet, edite o seguinte:

    `"topologyUrl" : "https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json"`
    
1. Em seguida, sob os nosmos GraphInstanceSet e GraphTopologyDelete, edite,

    `"topologyName" : "EVRtoAssetsOnObjDetect"`
1. Inicie uma sessão de depurar (acerte F5). Começará a ver algumas mensagens impressas na janela TERMINAL.

1. O operations.json começa com chamadas para GraphTopologyList e GraphInstanceList. Se tiver limpo recursos após iniciações anteriores ou tutoriais, isto devolverá listas vazias e, em seguida, fará uma pausa para que você possa chegar a Enter, como abaixo:

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
    1. Quando premir a tecla "Enter" na janela TERMINAL, é feito o próximo conjunto de chamadas diretas do método.
     * Uma chamada para o GraphTopologySet usando o topologyUrl acima.
     * Uma chamada para o GraphInstanceSet utilizando o seguinte corpo.
     
        ```
        {
          "@apiVersion": "1.0",
          "name": "Sample-Graph-1",
          "properties": {
            "topologyName": "EVRtoAssetsOnObjDetect",
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
     
1. A saída na janela TERMINAL para agora numa introdução de "Press Enter para continuar". Não bata em "Enter" neste momento. Pode deslocar-se para ver as cargas de resposta do JSON para os métodos diretos que invocou.

1. Se agora mudar para a janela OUTPUT no Código do Estúdio Visual, verá mensagens que estão a ser enviadas para o IoT Hub, pelo módulo Live Video Analytics no módulo IoT Edge.

     * Estas mensagens são discutidas na secção seguinte.
     
1. A instância do gráfico continuará a ser executada e gravará o vídeo — o simulador RTSP continuará a dar a volta ao vídeo de origem. Reveja as mensagens como discutido na secção abaixo e, em seguida, para parar o caso, volte para a janela TERMINAL e acerte "Enter". A próxima série de chamadas são feitas para limpar recursos:

     * Uma chamada para GraphInstanceDeactivar para desativar a instância do gráfico
     * Uma chamada para o GraphInstanceDelete para apagar o caso
     * Uma chamada para GraphTopologyDelete para apagar a topologia
     * Uma chamada final para a GraphTopologyList para mostrar que a lista está agora vazia

## <a name="interpret-the-results"></a>Interpretar os resultados 

Quando executam o gráfico de mídia, o módulo Live Video Analytics no IoT Edge envia certos eventos de diagnóstico e operacionais para o IoT Edge Hub. Estes eventos são as mensagens que vê na janela OUTPUT do Código do Estúdio Visual, que contém uma secção "body" e uma secção "aplicaçõesProperties". Para compreender o que estas secções representam, consulte [Criar e ler mensagens IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct).

Nas mensagens abaixo, as propriedades da aplicação e o conteúdo do corpo são definidos pelo módulo Live Video Analytics.

## <a name="diagnostic-events"></a>Eventos de diagnóstico

### <a name="mediasessionestablished-event"></a>Evento mediaSessionEstablished 

Quando um gráfico de mídia é instantâneo, o nó de origem RTSP tenta ligar-se ao servidor RTSP em execução no recipiente do simulador RTSP. Se for bem sucedido, vai imprimir este evento. Note que o tipo de evento é Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished.

```
[IoTHubMonitor] [5:53:17 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 XXX.XX.XX.XX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-05-17T17:53:16.981Z",
    "dataVersion": "1.0"
  }
}
```


* A mensagem é um evento de Diagnóstico, MediaSessionEstablished, indica que o nó de origem RTSP (o sujeito) foi capaz de estabelecer a ligação com o simulador RTSP, e começar a receber um feed ao vivo (simulado).

* O "sujeito" na aplicaçãoDeproperias refere o nó na topologia do gráfico a partir do qual a mensagem foi gerada. Neste caso, a mensagem é originária do nó de origem RTSP.

* "eventType" na aplicaçãoProperties indica que este é um evento de Diagnóstico.

* "eventTime" indica a hora em que ocorreu o evento, que é a altura em que o vídeo de tráfego (ficheiro MKV) começou a chegar ao módulo como um live stream.

* "body" contém dados sobre o evento de diagnóstico, que, neste caso, é o [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) detalhes.


## <a name="operational-events"></a>Eventos operacionais

Depois de o gráfico de mídia funcionar durante algum tempo, eventualmente obterá um evento a partir do módulo Contador de Objetos. 

```
[IoTHubMonitor] [5:53:44 PM] Message received from [lva-sample-device/objectCounter]:
{
  "body": {
    "count": 2
  },
  "applicationProperties": {
    "eventTime": "2020-05-17T17:53:44.062Z"
  }
}
```

A aplicaçãoProperties contém o eventTime, que é o momento em que o módulo Contador de Objetos observou que os resultados do módulo YOLO v3 continham objetos de interesse (camiões).

Pode ver que mais destes eventos aparecem à medida que outros camiões são detetados no vídeo.

### <a name="recordingstarted-event"></a>Evento "RecordingStarted"

Quase imediatamente após o contador de objetos enviar o evento, verá um evento do tipo Microsoft.Media.Graph.Operational.RecordingStarted

```
[IoTHubMonitor] [5:53:46 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T175346Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": " 2020-05-17T17:53:46.132Z",
    "dataVersion": "1.0"
  }
}
```

O "sujeito" na aplicaçãoDeproperias refere o nó do lavatório do ativo no gráfico, que gerou esta mensagem. O corpo contém informações sobre a localização da saída, que neste caso é o nome do ativo Azure Media Service no qual o vídeo é gravado. Deve anotar este valor.

### <a name="recordingavailable-event"></a>Evento disponível de gravação

Quando o nó da pia do ativo tiver carregado o vídeo para o ativo, emite este evento do tipo Microsoft.Media.Graph.Operational.RecordingAvailable

```
[IoTHubMonitor] [5:54:15 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T175346Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingAvailable",
    "eventTime": "2020-05-17T17:54:15.808Z",
    "dataVersion": "1.0"
  }
}
```

Este evento indica que foram escritos dados suficientes para que os jogadores/clientes iniciem a reprodução do vídeo. O "sujeito" na aplicaçãoProperties refere-se ao nó AssetSink no gráfico, que gerou esta mensagem. O corpo contém informações sobre a localização da saída, que neste caso é o nome do Ativo Azure Media Service no qual o vídeo é gravado.

### <a name="recordingstopped-event"></a>Evento de cobertura de Gravações

Se examinar as definições de ativação (tempo máximo de ativação) para o nó do processador do portão de sinal na [topologia,](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json)verá que o portão está configurado para fechar após 30 segundos de vídeo. Assim, cerca de 30 segundos após o evento RecordingStarted, deverá ver um evento do tipo Microsoft.Media.Graph.Operational.RecordingS com cobertura, indicando que o nó da pia do ativo parou de gravar o vídeo para o ativo.

```
[IoTHubMonitor] [5:54:15 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T175346Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStopped",
    "eventTime": "2020-05-17T17:54:15.040Z",
    "dataVersion": "1.0"
  }
}
```

Este evento indica que a gravação parou. O "sujeito" na aplicaçãoProperties refere-se ao nó AssetSink no gráfico, que gerou esta mensagem. O corpo contém informações sobre a localização da saída, que neste caso é o nome do ativo Azure Media Service no qual o vídeo é gravado.

## <a name="media-services-asset"></a>Ativo de Serviços de Mídia  

Pode examinar o ativo Dos Serviços de Comunicação que foi criado pelo gráfico, iniciando sessão no portal Azure e visualizando o vídeo.

1. Abra o seu navegador web e vá ao [portal Azure.](https://portal.azure.com/) Introduza as suas credenciais para iniciar sessão no portal. A vista predefinida é o dashboard de serviço.
1. Localize a sua conta de Serviços de Mídia entre os recursos que tem na sua subscrição e abra a lâmina da conta
1. Clique em Ativos na listagem de Serviços de Mídia.

    ![Elementos](./media/continuous-video-recording-tutorial/assets.png)
1. Encontrará um ativo listado com o nome sampleAssetFromEVR-LVAEdge-{DateTime} – este é o nome fornecido na propriedade outputLocation do evento RecordingStarted. O activoNamePattern na topologia determina como este nome foi gerado.
1. Clique no Ativo.
1. Na página de detalhes do ativo, clique na **nova caixa** de texto de URL de streaming.

    ![Novo ativo](./media/continuous-video-recording-tutorial/new-asset.png)

1. No assistente que abre, aceite as opções predefinindo e acerte em "Adicionar". Para mais informações, consulte, [reprodução de vídeo](video-playback-concept.md).

    > [!TIP]
    > Certifique-se de que o seu [ponto final de streaming está a funcionar](../latest/streaming-endpoint-concept.md).
1. O leitor deve carregar o vídeo e deverá ser capaz de acertar **no Play** para o ver.

> [!NOTE]
> Uma vez que a origem do vídeo era um contentor que simulava uma transmissão de câmara, os postes de tempo no vídeo estão relacionados com quando ativou a instância do gráfico, e quando o desativou. Se utilizar os controlos de reprodução incorporados no tutorial [de gravações de vários dias da Playback,](playback-multi-day-recordings-tutorial.md) pode ver os tempos no vídeo exibido no ecrã.

## <a name="clean-up-resources"></a>Limpar recursos

Se pretende experimentar os outros tutoriais, deve manter os recursos criados. Caso contrário, vá ao portal Azure, navegue pelos seus grupos de recursos, selecione o grupo de recursos sob o qual executou este tutorial e elimine o grupo de recursos.

## <a name="next-steps"></a>Passos seguintes

* Utilize uma [câmara IP](https://en.wikipedia.org/wiki/IP_camera) com suporte para RTSP em vez de utilizar o simulador RTSP. Pode pesquisar por câmaras IP com suporte RTSP na página de [produtos conformantes ONVIF,](https://www.onvif.org/conformant-products/) procurando dispositivos que estejam em conformidade com os perfis G, S ou T.
* Utilize um dispositivo Linux AMD64 ou X64 (vs. utilizando um VM Azure Linux). Este dispositivo deve estar na mesma rede que a câmara IP. Pode seguir as instruções no [tempo de funcionamento do Azure IoT Edge no Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) e, em seguida, seguir as instruções no [módulo IoT Edge para um dispositivo Linux virtual](https://docs.microsoft.com/azure/iot-edge/quickstart-linux) para registar o dispositivo com o Azure IoT Hub.
