---
title: Gravação de vídeo baseada em eventos para nuvem e reprodução de tutorial em nuvem - Azure
description: Neste tutorial, você aprenderá a usar live video analytics no IoT Edge para executar uma gravação de vídeo baseada em eventos para cloud e reprodução a partir da nuvem.
ms.topic: tutorial
ms.date: 05/27/2020
ms.openlocfilehash: daab1f06d8950aa7710c7e808ea6362ee3bfd626
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/01/2020
ms.locfileid: "84261931"
---
# <a name="tutorial-event-based-video-recording-to-cloud-and-playback-from-cloud"></a>Tutorial: Gravação de vídeo baseada em eventos para nuvem e reprodução da nuvem

Neste tutorial, você aprenderá a usar live video analytics no IoT Edge para gravar seletivamente partes de uma fonte de vídeo ao vivo para media Services na nuvem. Este caso de utilização é referido como gravação de vídeo baseada em [eventos](event-based-video-recording-concept.md) (EVR) neste tutorial. Para isso, utilizará um modelo de IA de deteção de objetos para procurar objetos no vídeo e gravará clips de vídeo apenas quando um determinado tipo de objeto for detetado. Também aprenderá como reproduzir os videoclips gravados utilizando os Media Services. Isto é útil para uma variedade de cenários, onde há necessidade de manter um arquivo de videoclips de interesse.

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
<!--* [Quickstart: Event-based recording based on motion events]()-->
* [Tutorial: desenvolvimento de um módulo IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-develop-for-linux)
* [Como editar a implementação.*.template.json](https://github.com/microsoft/vscode-azure-iot-edge/wiki/How-to-edit-deployment.*.template.json)
* Secção sobre [como declarar rotas no manifesto de implantação do IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes)

## <a name="prerequisites"></a>Pré-requisitos

Os pré-requisitos para este tutorial são os seguintes

* Instale [o estivador](https://docs.docker.com/desktop/) na sua máquina de desenvolvimento
* [Código de Estúdio Visual](https://code.visualstudio.com/) na sua máquina de desenvolvimento com extensão [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) e extensão [C#.](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)

    > [!TIP]
    > Pode ser solicitado a instalar o Docker. Pode ignorar esta solicitação.
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/thank-you/sdk-3.1.201-windows-x64-installer) na sua máquina de desenvolvimento.
* Complete [o roteiro de configuração de recursos de vídeo ao vivo](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) e [configurar o ambiente](https://review.docs.microsoft.com/en-us/azure/media-services/live-video-analytics-edge/detect-motion-emit-events-quickstart?branch=release-preview-media-services-lva#set-up-the-environment)

No final dos passos acima, terá certos recursos Azure implantados na subscrição do Azure, incluindo:

* IoT Hub
* Conta de armazenamento
* Conta dos Media Services
* Uma máquina virtual Linux

## <a name="concepts"></a>Conceitos

![Gráfico de mídia](./media/event-based-video-recording-tutorial/overview.png)

A gravação de vídeo baseada em eventos (EVR) refere-se ao processo de gravação de vídeo desencadeado por um evento. O evento em questão, pode ter origem no processamento do próprio sinal de vídeo (por exemplo, ao detetar um objeto em movimento no vídeo) ou pode ser de uma fonte independente (por exemplo, abertura de uma porta). Em alternativa, só pode ativar a gravação quando um serviço de inferenculação externo detetar que ocorreu um evento específico.  Neste tutorial você usará um vídeo de veículos movendo-se em uma autoestrada, e gravar vídeos de vídeo sempre que um caminhão é detetado.

O diagrama acima é uma representação pictórica de um [gráfico mediático](media-graph-concept.md) e módulos adicionais que realizam o cenário desejado. Existem quatro módulos IoT Edge envolvidos:

* Vídeo ao vivo Analytics no módulo IoT Edge
* Um módulo de IA construído com o [modelo YOLO v3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx)
* Um módulo personalizado para contar e filtrar objetos (referido como Contador de Objetos no diagrama acima) que irá construir e implantar neste tutorial
* Um [módulo de simulador RTSP](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) para simular uma câmara RTSP
    
Como mostra o diagrama, usará um nó [de origem RTSP](media-graph-concept.md#rtsp-source) no gráfico de mídia para capturar o vídeo ao vivo e enviará esse vídeo para dois caminhos.

* O primeiro caminho é para um nó [de processador de filtro de taxa de fotogramas](media-graph-concept.md#frame-rate-filter-processor) que produz quadros de vídeo à taxa de fotogramas especificada. Estes quadros de vídeo cortam como entrada para um nó de extensão HTTP. O nó de extensão HTTP envia quadros (como imagens) para o módulo AI (YOLO v3 – que é um detetor de objetos) e recebe resultados – que serão os objetos detetados pelo modelo. O nó de extensão HTTP publica então os resultados através do IoT Hub Message Sink para o IoT Edge Hub
* O módulo de contador de objetos está configurado para receber mensagens do IoT Edge Hub – que incluem os resultados de deteção de objetos (veículos no trânsito). Verifica as mensagens que procuram objetos de um determinado tipo (configurados através de uma propriedade dupla) e faz uma mensagem para ioT Edge Hub. Essas mensagens são então encaminhada de volta para o nó de origem IoT Hub do gráfico de mídia. Ao receber uma mensagem, o nó de origem IoT Hub no gráfico de mídia aciona o nó do [processador do portão](media-graph-concept.md#signal-gate-processor) de sinal para abrir o portão durante um período de tempo configurado. O vídeo flui através do portão para o nó da pia do ativo durante esse período. Essa parte do live stream é então gravada através do nó [da pia](media-graph-concept.md#asset-sink) do ativo para um [ativo](terminology.md#asset) na sua conta Azure Media Service.

## <a name="set-up-the-environment"></a>Configurar o ambiente

1. Clone o repo https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp daqui.
2. Lance o Código do Estúdio Visual (VSCode) e abra a pasta onde o repo é descarregado.
3. No VSCode, navegue para a pasta "src/cloud-to-device-console-app" e crie um ficheiro chamado "appsettings.json". Este ficheiro conterá as definições necessárias para executar o programa.
3. Copie o conteúdo do ficheiro clouddrive/lva-sample/appsettings.json após a execução do script de configuração de recursos . Consulte [o Pré-Requisito #4](event-based-video-recording-tutorial.md#prerequisites). Assim que o script de configuração do recurso terminar, clique nos suportes encaracolados para expor a estrutura da pasta. Verá três ficheiros criados sob a amostra de clouddrive/Lva. De interesse atualmente são os ficheiros .env e appsetting.json. Necessitará destes para atualizar os ficheiros no Código do Estúdio Visual mais tarde no arranque rápido. Talvez queira copiá-los num ficheiro local por enquanto.

    ![Definições da aplicação](./media/quickstarts/clouddrive.png)

    O texto do ficheiro clouddrive/lva-sample/appsettings.json deve ser semelhante:

    ```
    {  
        "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",  
        "deviceId" : "lva-sample-device",  
        "moduleId" : "lvaEdge"  
    }
    ```
1. Em seguida, navegue na pasta "src/edge" e crie um ficheiro chamado ".env".
1. Copie o conteúdo do ficheiro clouddrive/lva-sample/.env. O texto deve parecer:

    ```
    SUBSCRIPTION_ID="<Subscription ID>"  
    RESOURCE_GROUP="<Resource Group>"  
    AMS_ACCOUNT="<AMS Account ID>"  
    IOTHUB_CONNECTION_STRING="HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx"  
    AAD_TENANT_ID="<AAD Tenant ID>"  
    AAD_SERVICE_PRINCIPAL_ID="<AAD SERVICE_PRINCIPAL ID>"  
    AAD_SERVICE_PRINCIPAL_SECRET="<AAD SERVICE_PRINCIPAL ID>"  
    INPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"  
    OUTPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"  
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry username>"      
    ```
## <a name="examine-the-template-file"></a>Examine o arquivo do modelo 

Durante a configuração do ambiente, terá lançado o Visual Studio Code e aberto a pasta que contém o código de amostra.

No Código do Estúdio Visual, navegue por "src/edge". Verá o ficheiro .env que criou, bem como alguns ficheiros de modelos de implementação. Estes modelos definem quais os módulos Edge que irá implementar no Linux VM. O ficheiro .env tem os valores para as variáveis utilizadas nestes modelos, como a cadeia de ligação IoT Hub que permite enviar comandos para os módulos Edge via Azure IoT Hub.

Abra "src/edge/deployment.objectCounter.template.json". Note que existem quatro entradas na secção "módulos" – correspondentes aos itens acima indicados (na secção Conceitos):

* IvaEdge – este é o live video analytics no módulo IoT Edge
* yolov3 – este é o serviço de inferência construído com o modelo YOLO v3
* rtspsim – este é o simulador RTSP
* objectCounter – este é o módulo que procura objetos específicos nas mensagens devolvidas por yolov3

Para o módulo objectCounter, consulte a cadeia utilizada para o valor "imagem" – baseia-se no tutorial no desenvolvimento de um módulo IoT Edge. O Código do Estúdio Visual reconhecerá automaticamente que o código do módulo contador de objetos está em "src/edge/modules/objectCounter". Leia a secção sobre como declarar rotas no manifesto de implantação IoT Edge e, em seguida, examine as rotas no ficheiro JSON do modelo. Note como:

* LVAToObjectCounter é usado para enviar eventos específicos para um ponto final específico no módulo objectCounter
* ObjectCounterToLVA é utilizado para enviar um evento de gatilho para um ponto final específico (que deve ser o nó IoT Hub Source) no módulo LvaEdge
* objectCounterToIoTHub é usado como uma ferramenta de depurador – para ajudá-lo a ver a saída do objectCounter quando executar este tutorial

> [!NOTE]
> As propriedades desejadas para o módulo objectCounter – está configurado para procurar objetos que são marcados como "caminhão", com um nível de confiança de pelo menos 50%.

## <a name="deploy-the-edge-modules"></a>Implementar os módulos Edge

Utilizando o Código do Estúdio Visual, siga as instruções para iniciar sessão em estivador e "Construa e empurre a solução IoT Edge" mas utilize src/edge/deployment.objectCounter.template.json para este passo.

![Construa e empurre a solução IoT Edge](./media/event-based-video-recording-tutorial/build-push.png)

Isto irá construir o módulo objectCounter para a contagem de objetos e empurrar a imagem para o seu Registo de Contentores Azure (ACR)

* Verifique se tem as variáveis ambientais CONTAINER_REGISTRY_USERNAME_myacr e CONTAINER_REGISTRY_PASSWORD_myacr definidas em ficheiro .env

O passo acima irá criar o manifesto de implantação IoT Edge em src/edge/config/deployment.objectCounter.amd64.json.

No Código do Estúdio Visual, navegue para src/edge/config/deployment.objectCounter.amd64.json, clique à direita no ficheiro e selecione "Criar Implementação para Dispositivo Único". 

Se este for o seu primeiro tutorial com Live Video Analytics no IoT Edge, o Visual Studio Code irá instruí-lo a inserir a cadeia de ligação IoTHub. Pode copiá-lo a partir do ficheiro appsettings.json.

Em seguida, o Código do Estúdio Visual irá pedir-lhe para selecionar um dispositivo de hub IoT. Selecione o seu dispositivo IoT Edge (deve ser "lva-sample-device").

Nesta fase, iniciou-se a implantação de módulos de borda para o seu dispositivo IoT Edge.
Em cerca de 30 segundos, refresque o Azure IOT Hub na secção inferior esquerda no Código do Estúdio Visual, e deve ver que existem 4 módulos implantados (note novamente os nomes: lvaEdge, rtspsim, yolov3 e objectCounter)

![4 módulos implantados](./media/event-based-video-recording-tutorial/iot-hub.png)

## <a name="prepare-for-monitoring-events"></a>Preparar para eventos de monitorização

Clique à direita no dispositivo Edge ("lva-sample-device") e clique em "Iniciar monitorização do ponto final do evento incorporado". O módulo Live Video Analytics on IoT Edge emitirá eventos [operacionais](#operational-events) e [de diagnóstico](#diagnostic-events) para o IoT Edge Hub, e pode ver esses eventos na janela "OUTPUT" no Código do Estúdio Visual.

## <a name="run-the-program"></a>Execute o programa

1. Código do Estúdio Visual, navegue para "src/cloud-to-device-console-app/operations.json"
1. Sob o nó GráficoToplogiaSet, definir o seguinte: "topologyUrl" : https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json " 
1. Em seguida, sob o nó GraphInstanceSet, edite "topologyName" : "EVRtoAssetsOnObjDetect"
1. Prima "F5". Isto vai começar a sessão de depurar.
1. Na janela TERMINAL, você verá as respostas às chamadas [de método direto](direct-methods.md) feitas pelo programa para o live video analytics no módulo IoT Edge, que é:

    1. GraphTopologyList – recupera uma lista de Topologias de Gráficos que foram adicionadas ao módulo, se houver

        Hit Enter para continuar
    1. GraphInstanceList – recupera uma lista de Instâncias de Gráficos que foram criadas, se houver

        Hit Enter para continuar
    1. GraphTopologySet – adiciona a topologia acima, denominada "EVRtoAssetsOnObjDetect" ao módulo
    1. GraphInstanceSet – cria uma instância da topologia acima, substituindo parâmetros
    1. De interesse é o parâmetro rtspUrl. Aponta para o ficheiro MKV que foi descarregado para o Linux VM, para um local a partir do qual o simulador RTSP o lê
    1. GraphInstanceActivate – inicia o gráfico mediático, fazendo com que o vídeo flua através
    1. GraphInstanceList – para mostrar que agora tem uma instância no módulo que está em execução

        Neste ponto, você deve parar, e *não* bater Enter
1. Na janela OUTPUT, verá mensagens operacionais e de diagnóstico que estão a ser enviadas para o IoT Hub, pelo módulo Live Video Analytics no módulo IoT Edge
1. O gráfico de mídia continuará a ser executado e imprimirá eventos – o simulador RTSP continuará a fazer loop no vídeo de origem. Para parar o gráfico de mídia, pode bater enter novamente na janela TERMINAL. O programa enviará:

    1. GraphInstanceDeactivate - para parar a Instância do Gráfico, e parar a gravação de vídeo
    1. GraphInstanceDelete – para apagar a instância do módulo
    1. GraphInstanceList – para mostrar que agora não existem casos no módulo

> [!NOTE]
> A Topologia do Gráfico não foi eliminada. Se precisar de o fazer, desaça este passo com o seguinte corpo JSON:

```
{
    "@apiVersion" : "1.0",
    "name" : "EVRtoAssetsOnObjDetect"
}
```

## <a name="examine-the-output"></a>Examinar o ficheiro de saída
 
O módulo Live Video Analytics on IoT Edge emite eventos [operacionais](#operational-events) e [de diagnóstico](#diagnostic-events) para o IoT Edge Hub, que é o texto que vê na janela OUTPUT do Código do Estúdio Visual, seguindo o formato de mensagens de streaming estabelecido para comunicações dispositivo-nuvem pelo IoT Hub:

* Um conjunto de propriedades de aplicação. Um dicionário de propriedades de cordas que uma aplicação pode definir e aceder, sem precisar de desseializar o corpo da mensagem. IoT Hub nunca modifica estas propriedades
* Um corpo binário opaco

Nas mensagens abaixo, as propriedades da aplicação e o conteúdo do corpo são definidos pelo live video analytics no módulo IoT Edge. Para obter mais informações, consulte [Monitorização e registo.](monitoring-logging.md) 

## <a name="diagnostic-events"></a>Eventos de diagnóstico

### <a name="mediasessionestablished-event"></a>Evento mediaSessionEstablished 

Quando um gráfico de mídia é instantâneo, o nó de origem RTSP tenta ligar-se ao servidor RTSP em execução no recipiente do simulador RTSP. Se for bem sucedido, vai imprimir este evento. Note que o tipo de evento é Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished.

```
[IoTHubMonitor] [2:02:54 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1589749373980489 1 IN IP4 172.18.0.4\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=XXXXXXXXXXX\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-05-17T21:02:53.981Z",
    "dataVersion": "1.0"
  }
}
```

Tenha em atenção o seguinte:

* O "sujeito" na aplicaçãoDeproperias refere o nó no MediaGraph a partir do qual a mensagem foi gerada. Neste caso, a mensagem é originária do nó rtsp source.
* "eventType" na aplicaçãoProperties indica que este é um evento de Diagnóstico
* "eventTime" indica a hora em que o evento ocorreu.
* "body" contém dados sobre o evento de diagnóstico - é a mensagem SDP

Escreva o eventTime – esta é a altura em que o vídeo de tráfego (ficheiro MKV) começou a chegar ao módulo como transmissão em direto.

## <a name="operational-events"></a>Eventos operacionais

Depois de o gráfico de mídia funcionar durante algum tempo, eventualmente obterá um evento a partir do módulo Contador de Objetos. 

```
[IoTHubMonitor] [2:03:21 PM] Message received from [lva-sample-device/objectCounter]:
{
  "body": {
    "count": 2
  },
  "applicationProperties": {
    "eventTime": "2020-05-17T21:03:21.062Z"
  }
}
```

A aplicaçãoProperties contém apenas o eventTime, que é o momento em que o módulo observou que os resultados do módulo YOLO v3 continham objetos de interesse (camiões).

Pode ver que mais destes eventos aparecem à medida que outros camiões são detetados no vídeo.

### <a name="recordingstarted-event"></a>Evento "RecordingStarted"

Quase imediatamente após o envio do evento do Contador de Objetos, verá um evento do tipo Microsoft.Media.Graph.Operational.RecordingStarted

```
[IoTHubMonitor] [2:03:22 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T210321Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-2/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": " 2020-05-17T21:03:22.532Z",
    "dataVersion": "1.0"
  }
}
```

O "sujeito" na aplicaçãoDeproperias refere o nó do lavatório do ativo no gráfico, que gerou esta mensagem.

O corpo contém informações sobre a localização da saída, que neste caso é o nome do ativo Azure Media Service no qual o vídeo é gravado. Deve anotar este valor.

### <a name="recordingavailable-event"></a>Evento disponível de gravação

Quando o nó da pia do ativo tiver carregado o vídeo para o ativo, emite este evento do tipo Microsoft.Media.Graph.Operational.RecordingAvailable

```
[IoTHubMonitor] [2:03:31 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T210321Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-2/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingAvailable",
    "eventTime": "2020-05-17T21:03:31.808Z",
    "dataVersion": "1.0"
  }
}
```

Este evento indica que foram escritos dados suficientes para que os jogadores/clientes iniciem a reprodução do vídeo.

O "sujeito" na aplicaçãoProperties refere-se ao nó AssetSink no gráfico, que gerou esta mensagem.

O corpo contém informações sobre a localização da saída, que neste caso é o nome do Ativo Azure Media Service no qual o vídeo é gravado.

### <a name="recordingstopped-event"></a>Evento de cobertura de Gravações

Se examinar as definições de ativação (tempo máximo de ativação) para o nó do processador signal Gate na topologia), verá que o portão está configurado para fechar após 30 segundos de vídeo. Assim, cerca de 30 segundos após o evento RecordingStarted, deverá ver um evento do tipo Microsoft.Media.Graph.Operational.RecordingS com cobertura, indicando que o nó de Sink de Ativo parou de gravar o vídeo para o Ativo.

```
[IoTHubMonitor] [2:03:52 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T210321Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-2/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStopped",
    "eventTime": "2020-05-17T21:03:52.040Z",
    "dataVersion": "1.0"
  }
}
```

Este evento indica que a gravação parou.

O "sujeito" na aplicaçãoProperties refere-se ao nó AssetSink no gráfico, que gerou esta mensagem.

O corpo contém informações sobre a localização da saída, que neste caso é o nome do ativo Azure Media Service no qual o vídeo é gravado.

## <a name="media-services-asset"></a>Ativo de Serviços de Mídia  

Pode examinar o ativo Dos Serviços de Comunicação que foi criado pelo gráfico, iniciando sessão no portal Azure e visualizando o vídeo.

1. Abra o seu navegador web e vá ao [portal Azure.](https://portal.azure.com/) Introduza as suas credenciais para iniciar sessão no portal. A vista predefinida é o dashboard de serviço.
1. Localize a sua conta de Serviços de Mídia entre os recursos que tem na sua subscrição e abra a lâmina da conta
1. Clique em Ativos na listagem de Serviços de Mídia

    ![Elementos](./media/continuous-video-recording-tutorial/assets.png)
1. Encontrará um ativo listado com o nome da amostraAssetFromCVR-LVAEdge-{DateTime} – este é o padrão de nomeação escolhido no seu ficheiro de topologia de gráficos de mídia.
1. Clique no Ativo.
1. Na página de detalhes do ativo, clique na **nova caixa** de texto de URL de streaming.

    ![Novo ativo](./media/continuous-video-recording-tutorial/new-asset.png)

1. No assistente que abre, aceite as opções predefinindo e acerte em "Adicionar". Para mais informações, consulte, [reprodução de vídeo](video-playback-concept.md).

    > [!TIP]
    > Certifique-se de que o seu [ponto final de streaming está a funcionar](../latest/streaming-endpoint-concept.md).
1. O leitor deve carregar o vídeo e deverá ser capaz de acertar **play**>** para vê-lo.

> [!NOTE]
> Uma vez que a origem do vídeo era um contentor que simulava uma transmissão de câmara, os postes de tempo no vídeo estão relacionados com quando ativou a Instância do Gráfico, e quando o desativou. Para obter mais informações, consulte [gravações de vários dias](playback-multi-day-recordings-tutorial.md) de Reprodução sobre como navegar numa gravação de vários dias e ver partes desse arquivo. Nesse tutorial, também é possível ver os tempos no vídeo exibido no ecrã.

## <a name="clean-up-resources"></a>Limpar recursos

Se pretende experimentar os outros tutoriais, deve manter os recursos criados. Caso contrário, vá ao portal Azure, navegue pelos seus grupos de recursos, selecione o grupo de recursos sob o qual executou este tutorial e elimine o grupo de recursos.

## <a name="next-steps"></a>Passos seguintes

* Utilize uma [câmara IP](https://en.wikipedia.org/wiki/IP_camera) com suporte para RTSP em vez de utilizar o simulador RTSP. Pode pesquisar por câmaras IP com suporte RTSP na página de [produtos conformantes ONVIF,](https://www.onvif.org/conformant-products/) procurando dispositivos que estejam em conformidade com os perfis G, S ou T.
* Utilize um dispositivo Linux AMD64 ou X64 (vs. utilizando um VM Azure Linux). Este dispositivo deve estar na mesma rede que a câmara IP. Pode seguir as instruções no [tempo de funcionamento do Azure IoT Edge no Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) e, em seguida, seguir as instruções no [módulo IoT Edge para um dispositivo Linux virtual](https://docs.microsoft.com/azure/iot-edge/quickstart-linux) para registar o dispositivo com o Azure IoT Hub.
