---
title: Analise o vídeo ao vivo utilizando o Intel OpenVINO™ DL Streamer – Extensão Edge AI via gRPC
description: Este tutorial mostra-lhe como utilizar o Intel OpenVINO™ DL Streamer – Extensão Edge AI da Intel para analisar um feed de vídeo ao vivo a partir de uma câmara IP (simulada).
ms.topic: tutorial
ms.date: 02/04/2021
ms.service: media-services
ms.author: faneerde
author: fvneerden
ms.openlocfilehash: 20a22d11973f5bb01e2c1345538d5d94ce311dc7
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/15/2021
ms.locfileid: "103465764"
---
# <a name="tutorial-analyze-live-video-by-using-intel-openvino-dl-streamer--edge-ai-extension"></a>Tutorial: Analise o vídeo ao vivo utilizando Intel OpenVINO™ DL Streamer – Extensão Edge AI 

Este tutorial mostra-lhe como utilizar o Intel OpenVINO™ DL Streamer – Extensão Edge AI da Intel para analisar um feed de vídeo ao vivo a partir de uma câmara IP (simulada). Você verá como este servidor de inferência lhe dá acesso a diferentes modelos para detetar objetos (uma pessoa, um veículo, ou uma bicicleta), classificação de objetos (atribuições de veículos) e um modelo para rastreamento de objetos (pessoa, veículo e bicicleta). A integração com o módulo gRPC permite enviar quadros de vídeo para o servidor de inferência de IA. Os resultados são então enviados para o IoT Edge Hub. Quando executar este serviço de inferência no mesmo nó de computação que o Live Video Analytics, pode aproveitar o envio de dados de vídeo através de memória partilhada. Isto permite-lhe executar inferenculação à taxa de fotogramas do feed de vídeo ao vivo (por exemplo. 30 fotogramas/seg). 

Este tutorial usa um Azure VM como um dispositivo IoT Edge, e usa um stream de vídeo simulado ao vivo. Baseia-se no código de amostra escrito em C#, e baseia-se no [movimento Detect e emite eventos](detect-motion-emit-events-quickstart.md) rápidos.

> [!NOTE]
> Este tutorial requer a utilização de uma máquina x86-64 como dispositivo Edge.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure que inclui uma subscrição ativa. [Crie uma conta gratuita se](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) ainda não tiver uma.
  > [!NOTE]
  > Você precisará de uma subscrição Azure com permissões para criar principais serviços **(a função do proprietário** fornece isso). Se não tiver as permissões certas, contacte o administrador da sua conta para lhe conceder as permissões certas. 
* [Código do Estúdio Visual,](https://code.visualstudio.com/)com as seguintes extensões:
    * [Ferramentas do Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* Se não completou o [movimento Detect e emite eventos](detect-motion-emit-events-quickstart.md) de arranque rápido, então certifique-se de completar os passos para [configurar os recursos Azure](detect-motion-emit-events-quickstart.md#set-up-azure-resources).

> [!TIP]
> Ao instalar ferramentas Azure IoT, poderá ser solicitado a instalar o Docker. Pode ignorar a solicitação.

## <a name="review-the-sample-video"></a>Reveja o vídeo da amostra

Quando configura os recursos Azure, um pequeno vídeo de um parque de estacionamento é copiado para o Linux VM em Azure que está a usar como dispositivo IoT Edge. Este quickstart utiliza o ficheiro de vídeo para simular uma transmissão em direto.

Abra uma aplicação como o [leitor de mídia VLC](https://www.videolan.org/vlc/). Selecione Ctrl+N e, em seguida, cole um link para [o vídeo](https://lvamedia.blob.core.windows.net/public/lots_015.mkv) para iniciar a reprodução. Vê-se as imagens dos veículos num parque de estacionamento, a maioria estacionados, e um a mover-se.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4LUbN]

Neste arranque rápido, utilizará o Live Video Analytics no IoT Edge juntamente com o Intel OpenVINO™ DL Streamer – Edge AI Extension da Intel para detetar objetos como veículos, para classificar veículos ou veículos de pista, pessoa ou bicicletas. Publicará os eventos de inferência resultantes no IoT Edge Hub.

## <a name="overview"></a>Descrição Geral

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/use-intel-openvino-tutorial/grpc-vas-extension-with-vino.svg" alt-text="Visão geral do MediaGraph LVA":::

Este diagrama mostra como os sinais fluem neste arranque rápido. Um [módulo Edge](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simula uma câmara IP que hospeda um servidor Real-Time de Protocolo de Streaming (RTSP). Um nó [de origem RTSP](media-graph-concept.md#rtsp-source) puxa o feed de vídeo deste servidor e envia quadros de vídeo para o nó do [processador de extensão gRPC.](media-graph-concept.md#grpc-extension-processor) 

O nó do processador de extensão gRPC leva quadros de vídeo descodificados como entrada, e retransmite esses quadros para um ponto final [gRPC](terminology.md#grpc) exposto por um servidor gRPC. O nó suporta a transferência de dados utilizando [memória partilhada](https://en.wikipedia.org/wiki/Shared_memory) ou incorporando diretamente o conteúdo no corpo de mensagens gRPC. Além disso, o nó tem um formato de imagem incorporado para dimensionamento e codificação de quadros de vídeo antes de serem retransmitidos para o ponto final gRPC. O scaler tem opções para que a relação de aspeto de imagem seja preservada, acolchoada ou esticada. O codificadores de imagem suporta formatos jpeg, png ou bmp. Saiba mais sobre o processador [aqui.](media-graph-extension-concept.md#grpc-extension-processor)

Neste tutorial, vai:

1. Desdobre o gráfico de mídia.
1. Interprete os resultados.
1. Limpe os recursos.

## <a name="about-intel-openvino-dl-streamer--edge-ai-extension-module"></a>Sobre o Intel OpenVINO™ DL Streamer – Módulo de Extensão Edge AI


O módulo OpenVINO™ DL Streamer - Edge AI Extension é um microserviço baseado no Serviço de Vídeo Analytics da Intel (VA Serving) que serve pipelines de análise de vídeo construídos com OpenVINO™ DL Streamer. Os desenvolvedores podem enviar quadros de vídeo descodificados para o módulo de extensão de IA que realiza deteção, classificação ou rastreio e devolve os resultados. O módulo de extensão de IA expõe APIs gRPC que são compatíveis com plataformas de análise de vídeo como live video analytics em IoT Edge da Microsoft. 

Para construir soluções complexas e de vídeo ao vivo de alta performance, o módulo Live Video Analytics no IoT Edge deve ser emparelhado com um poderoso motor de inferência que pode alavancar a balança na borda. Neste tutorial, os pedidos de inferência são enviados para o [Intel OpenVINO™ DL Streamer – Extensão Edge AI](https://aka.ms/lva-intel-openvino-dl-streamer), um módulo Edge que foi projetado para trabalhar com o Live Video Analytics no IoT Edge. 

Na versão inicial deste servidor de inferência, tem acesso aos [seguintes modelos:](https://github.com/intel/video-analytics-serving/tree/master/samples/lva_ai_extension#edge-ai-extension-module-options)

- object_detection para deteção de ![ objetos person_vehicle_bike_detection para veículo](./media/use-intel-openvino-tutorial/object-detection.png)

- object_classification para classificação de objetos vehicle_attributes_recognition ![ para veículo](./media/use-intel-openvino-tutorial/object-classification.png)

- object_tracking para person_vehicle_bike_tracking ![ rastreio de objeto para veículo de pessoa](./media/use-intel-openvino-tutorial/object-tracking.png)

Utiliza pipelines de deteção de objetos pré-carregados, classificação de objetos e rastreio de objetos para começar rapidamente. Além disso, vem com [modelos pré-carregados de deteção de veículos-bicicleta-crossroad-0078](https://github.com/openvinotoolkit/open_model_zoo/blob/master/models/intel/person-vehicle-bike-detection-crossroad-0078/description/person-vehicle-bike-detection-crossroad-0078.md) e modelos de [reconhecimento-barreira-0039 de atributos do veículo.](https://github.com/openvinotoolkit/open_model_zoo/blob/master/models/intel/vehicle-attributes-recognition-barrier-0039/description/vehicle-attributes-recognition-barrier-0039.md)

> [!NOTE]
> Ao descarregar e utilizar o módulo Edge: OpenVINO™ DL Streamer – Extensão Edge AI da Intel, e o software incluído, concorda com os termos e condições previstos no [Contrato de Licença.](https://www.intel.com/content/www/us/en/legal/terms-of-use.html)
> A Intel está empenhada em respeitar os direitos humanos e em evitar a cumplicidade em violações dos direitos humanos. Consulte os [Princípios Globais dos Direitos Humanos da Intel.](https://www.intel.com/content/www/us/en/policy/policy-human-rights.html) Os produtos e software da Intel destinam-se apenas a ser utilizados em aplicações que não causem ou contribuam para uma violação de um direito humano reconhecido internacionalmente.

Pode utilizar a flexibilidade dos diferentes oleodutos para o seu caso de utilização específica, alterando simplesmente as variáveis ambientais do pipeline no seu modelo de implantação. Isto permite-lhe alterar rapidamente o modelo de pipeline e quando combinado com o Live Video Analytics é uma questão de segundos para alterar o pipeline e o modelo de inferência.  

## <a name="create-and-deploy-the-media-graph"></a>Criar e implementar o gráfico de mídia

### <a name="examine-and-edit-the-sample-files"></a>Examine e edite os ficheiros de amostra

Como parte dos pré-requisitos, descarregou o código de amostra para uma pasta. Siga estes passos para examinar e editar os ficheiros de amostra.

1. No Código do Estúdio Visual, vá ao *src/edge*. Você vê o seu ficheiro *.env* e alguns ficheiros de modelo de implementação.

    O modelo de implantação refere-se ao manifesto de implantação do dispositivo de borda. Inclui alguns valores de espaço reservado. O ficheiro *.env* inclui os valores para essas variáveis.

1. Aceda à pasta *src/cloud-to-device-app.* Aqui vê a sua *appsettings.jsno* ficheiro e alguns outros ficheiros:

    * ***c2d-console-app.csproj*** - O ficheiro do projeto para Visual Studio Code.
    * ***operations.jsem*** - Uma lista das operações que quer que o programa seja executado.
    * ***Programa.cs*** - O código do programa de amostra. Este código:

        * Carrega as definições da aplicação.
        * Invoca métodos diretos que o live video analytics no módulo IoT Edge expõe. Pode utilizar o módulo para analisar streams de vídeo ao vivo invocando os seus [métodos diretos.](direct-methods.md)
        * Pausas para que possa examinar a saída do programa na janela **TERMINAL** e examinar os eventos que foram gerados pelo módulo na janela **OUTPUT.**
        * Invoca métodos diretos para limpar recursos.


1. Editar o *operations.jsno* ficheiro:
    * Altere a ligação para a topologia do gráfico:

        `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/grpcExtensionOpenVINO/2.0/topology.json"`

    * Em `GraphInstanceSet` , editar o nome da topologia do gráfico para corresponder ao valor no link anterior:

      `"topologyName" : "InferencingWithOpenVINOgRPC"`

    * Em `GraphTopologyDelete` , editar o nome:

      `"name": "InferencingWithOpenVINOgRPC"`

### <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Gerar e implementar o manifesto de implantação IoT Edge

1. Clique com o botão direito para o *src/edge/deployment.openvino.grpc.cpu.template.jsno* ficheiro e, em seguida, selecione **Generate IoT Edge Deployment Manifest**.

    ![Gerar manifesto de implantação de borda ioT](./media/use-intel-openvino-tutorial/generate-deployment-manifest.png)  

    A *deployment.openvino.grpc.cpu.amd64.jsno* ficheiro manifesto é criada na pasta *src/edge/config.*

> [!NOTE]
> Também incluímos um *deployment.openvino.grpc.gpu.template.jsno* modelo que permite o suporte de GPU para o módulo intel OpenVINO DL Streamer - Edge AI Extension. Estes modelos apontam para a imagem do hub do Docker da Intel.

Os modelos acima mencionados apontam para a imagem do hub do Intel Docker. Se preferir hospedar uma cópia no seu próprio Registo de Contentores Azure, pode seguir os passos 1 e 2 abaixo:
1. SSH num dispositivo com ferramentas CLI estivas instaladas (isto é, o seu dispositivo de aresta) e puxe/tag/empurre o recipiente com estes passos:
    * Puxe a imagem da Intel do centro de Docker:

        `sudo docker pull intel/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension`
    
    * Marque a imagem da Intel com o seu próprio nome de registo de contentores Azure. Substitua {YOUR ACR NAME} pelo nome ACR que pode encontrar no ficheiro .env:

        `sudo docker image tag intel/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension {YOUR ACR NAME/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension}`
    
    * Empurre a sua imagem marcada para o registo do seu contentor Azure:

        `sudo docker push {YOUR ACR NAME/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension}`
    
2. Agora precisa editar os modelos para fazer referência à sua nova imagem hospedada no Registo do Contentor Azure.
    * Clique com odeployment.openvino.grpc.cpu.template.jse navegue *na* parte do módulo *lavExtension* e substitua:

        `intel/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension`

        Por:

        `{YOUR ACR NAME/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension}`
    * Repita o passo 2 para o *deployment.openvino.grpc.gpu.template.jsem*


3. Se tiver concluído o [movimento Detect e emitir eventos](detect-motion-emit-events-quickstart.md) de arranque rápido, então ignore este passo. 

    Caso contrário, perto do painel **AZURE IOT HUB** no canto inferior esquerdo, selecione o ícone **Mais ações** e, em seguida, selecione **set IoT Hub Connection String**. Pode copiar o fio do *appsettings.jsficheiro.* Ou, para garantir que configura o hub IoT adequado dentro do Código do Estúdio Visual, use o [comando do hub Select IoT](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub).
    
    ![Definir cadeia de conexão do hub IoT](./media/quickstarts/set-iotconnection-string.png)

> [!NOTE]
> Pode ser-lhe pedido que forneça informações sobre o ponto final incorporado para o IoT Hub. Para obter essa informação, no portal Azure, navegue até ao seu Hub IoT e procure a opção **de pontos finais incorporados** no painel de navegação esquerdo. Clique lá e procure o **ponto final compatível com o Event Hub** na secção de ponto final **compatível com o Event Hub.** Copie e use o texto na caixa. O ponto final será mais ou menos assim:  
    ```
    Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
    ```

1. Clique à direita *src/edge/config/deployment.openvino.grpc.cpu.template.js* e selecione **Criar Implementação para dispositivo único**. 

    ![Criar implementação para dispositivo único](./media/use-intel-openvino-tutorial/deploy-manifest.png)

1. Quando lhe for solicitado que selecione um dispositivo IoT Hub, selecione **o dispositivo de amostra de Lva**.
1. Após cerca de 30 segundos, no canto inferior esquerdo da janela, refresque o Azure IoT Hub. O dispositivo de borda mostra agora os seguintes módulos implantados:

    * O módulo Live Video Analytics, chamado **LvaEdge**
    * O **módulo rtspsim,** que simula um servidor RTSP e funciona como a fonte de um feed de vídeo ao vivo
    * O módulo **LvaExtension,** que é o Intel OpenVINO™ DL Streamer – Extensão Edge AI 

### <a name="prepare-to-monitor-events"></a>Preparar para monitorizar eventos

Clique com o botão direito no dispositivo Live Video Analytics e selecione **Start Monitoring Built-in Event Endpoint**. Precisa deste passo para monitorizar os eventos do IoT Hub na janela **OUTPUT** do Código do Estúdio Visual. 

![Iniciar a monitorização](./media/quickstarts/start-monitoring-iothub-events.png) 

### <a name="run-the-sample-program-to-detect-vehicles-persons-or-bike"></a>Executar o programa de amostragem para detetar veículos, pessoas ou bicicletas
Se abrir a [topologia](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/grpcExtensionOpenVINO/2.0/topology.json) do gráfico para este tutorial num browser, verá que o valor foi `grpcExtensionAddress` definido para , em `tcp://lvaExtension:5001` comparação com a amostra *httpExtensionOpenVINO* que não precisa de alterar o url para o servidor gRPC. Em vez disso, instrua o módulo a executar um gasoduto específico pelas variáveis ambientais, como mencionado anteriormente. No modelo padrão definimos isto para: "object_detection" para "person_vehicle_bike_detection". Pode experimentar outros oleodutos suportados. 

1. No Código do Estúdio Visual, abra o **separador Extensões** (ou prima Ctrl+Shift+X) e procure por Azure IoT Hub.
1. Clique no direito e selecione **Definições de extensão**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Definições de extensão":::
1. Procure e ative "Mostrar Mensagem Verbose".

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Mostrar mensagem verbose":::
1. Para iniciar uma sessão de depuragem, selecione a tecla F5. Vê mensagens impressas na janela **TERMINAL.**
1. A *operations.jsno* código começa com chamadas para os métodos diretos e `GraphTopologyList` `GraphInstanceList` . Se limpou os recursos depois de ter concluído os quickstarts anteriores, então este processo irá devolver listas vazias e, em seguida, fazer uma pausa. Para continuar, selecione a tecla 'Entrar'.

    A janela **TERMINAL** mostra o próximo conjunto de chamadas de métodos diretos:

     * Uma chamada para `GraphTopologySet` que usa o anterior `topologyUrl`
     * Uma chamada para `GraphInstanceSet` o seguinte corpo:

         ```
         {
           "@apiVersion": "2.0",
           "name": "Sample-Graph-1",
           "properties": {
             "topologyName": "InferencingWithOpenVINOgRPC",
             "description": "Sample graph description",
             "parameters": [
               {
                 "name": "rtspUrl",
                 "value": "rtsp://rtspsim:554/media/lots_015.mkv"
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

     * Uma chamada para `GraphInstanceActivate` que inicia a instância do gráfico e o fluxo de vídeo
     * Uma segunda chamada para `GraphInstanceList` que mostra que a instância do gráfico está no estado de execução
1. A saída na janela **TERMINAL** para num `Press Enter to continue` momento em que. Não selecione Enter ainda. Percorra para ver as cargas de resposta JSON para os métodos diretos que invocou.
1. Mude para a janela **OUTPUT** no Código do Estúdio Visual. Vê mensagens que o modusão IoT Edge está a enviar para o hub IoT. A secção seguinte deste quickstart discute estas mensagens.
1. O gráfico mediático continua a correr e a imprimir resultados. O simulador RTSP continua a dar a volta ao vídeo de origem. Para parar o gráfico de mídia, volte à janela **TERMINAL** e selecione Enter. 

    A próxima série de chamadas limpa recursos:
      * Uma chamada para `GraphInstanceDeactivate` desativar a instância do gráfico.
      * Uma chamada para `GraphInstanceDelete` apagar o caso.
      * Uma chamada para `GraphTopologyDelete` apagar a topologia.
      * Uma chamada final para `GraphTopologyList` mostrar que a lista está vazia.

## <a name="interpret-results"></a>Interpretar os resultados

Quando executou o gráfico de mídia, os resultados do nó do processador de extensão HTTP passam pelo nó da pia IoT Hub para o hub IoT. As mensagens que vê na janela **OUTPUT** contêm uma `body` secção e uma `applicationProperties` secção. Para obter mais informações, consulte [Criar e ler mensagens IoT Hub](../../iot-hub/iot-hub-devguide-messages-construct.md).

Nas seguintes mensagens, o módulo Live Video Analytics define as propriedades da aplicação e o conteúdo do corpo. 

### <a name="mediasessionestablished-event"></a>Evento mediaSessionEstablished

Quando um gráfico de mídia é instantâneo, o nó de origem RTSP tenta ligar-se ao servidor RTSP que funciona no recipiente rtspsim-live555. Se a ligação for bem sucedida, o seguinte evento é impresso. O tipo de evento é **Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished**.

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:
{
  "sdp": "SDP:\nv=0\r\no=- 1612432131600584 1 IN IP4 172.18.0.6\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/homes_00425.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.08.19\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-214.166\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/homes_00425.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=64001F;sprop-parameter-sets=Z2QAH6zZQFAFuwFsgAAAAwCAAAAeB4wYyw==,aOvhEsiw\r\na=control:track1\r\n"
}
```

Nesta mensagem, note estes detalhes:

* A mensagem é um evento de diagnóstico. `MediaSessionEstablished` indica que o nó de origem RTSP (o sujeito) ligado ao simulador RTSP e começou a receber um feed ao vivo (simulado).
* Em `applicationProperties` , indica que a `subject` mensagem foi gerada a partir do nó de origem RTSP no gráfico de mídia.
* Em `applicationProperties` , indica que este evento é um evento de `eventType` diagnóstico.
* O `eventTime` indica a hora em que o evento ocorreu.
* Contém `body` dados sobre o evento de diagnóstico. Neste caso, os dados compreendem os detalhes do [Protocolo de Descrição da Sessão (SDP).](https://en.wikipedia.org/wiki/Session_Description_Protocol)

### <a name="inference-event"></a>Evento de inferência

O nó do processador de extensão gRPC recebe resultados de inferência da Extensão Intel OpenVINO™ DL Streamer – Edge AI. Em seguida, emite os resultados através do nó de pia IoT Hub como eventos de inferência. 

Nestes eventos, o tipo está definido `entity` para indicar que é uma entidade, como um carro ou um caminhão. O `eventTime` valor é a hora utc quando o objeto foi detetado. 

No exemplo seguinte vê-se identificado um veículo, o tipo de veículo (carrinha) e a cor (branca), todos com um nível de confiança superior a 0,9, também atribuiu um ID à entidade quando utilizamos o modelo de rastreio de objetos.

```
[IoTHubMonitor] [9:43:18 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "timestamp": 145118912223221,
  "inferences": [
    {
      "type": "entity",
      "entity": {
        "tag": {
          "value": "vehicle",
          "confidence": 0.9605301
        },
        "attributes": [
          {
            "name": "color",
            "value": "white",
            "confidence": 0.9605301
          },
          {
            "name": "type",
            "value": "car",
            "confidence": 0.9605301
          }
        ],
        "box": {
          "l": 0.3958135,
          "t": 0.078730375,
          "w": 0.48403296,
          "h": 0.94352424
        },
        "id": "1"
      }
    }
}
```

Nas mensagens, note os seguintes detalhes:

* Em `applicationProperties` , refere o nó na `subject` topologia do gráfico a partir do qual a mensagem foi gerada. 
* Em `applicationProperties` , indica que este evento é um evento de `eventType` análise.
* O `eventTime` valor é o momento em que o evento ocorreu.
* A `body` secção contém dados sobre o evento de análise. Neste caso, o evento é um evento de inferência, pelo que o corpo contém `inferences` dados.
* A `inferences` secção indica que o é `type` `entity` . Esta secção inclui dados adicionais sobre a entidade.

## <a name="run-the-sample-program-to-detect-persons-or-vehicles-or-bikes"></a>Executar o programa de amostragem para detetar pessoas ou veículos ou bicicletas
Para utilizar um modelo diferente, terá de alterar o modelo de implementação. Para alternar entre os modelos suportados pode alterar as variáveis ambientais localizadas no módulo de extensão de LvaExtenstion. Consulte este [documento no GitHub](https://github.com/intel/video-analytics-serving/tree/master/samples/lva_ai_extension#edge-ai-extension-module-options) para obter os valores e combinações suportados para os modelos.

```
"Env":[
"PIPELINE_NAME=object_classification",
"PIPELINE_VERSION=vehicle_attributes_recognition"
],
```
> [!TIP]
> Copie o modelo e guarde-o com um novo nome para cada possível pipeline. Desta forma pode alternar entre modelos criando uma nova implementação com base num destes modelos.

Uma vez alteradas as variáveis, pode voltar a colocar o modelo no dispositivo. Pode agora repetir os passos acima para executar novamente o programa de amostragem, com o novo oleoduto. Os resultados da inferência serão semelhantes (em esquema) mas mostrarão mais ou menos informações dependendo do modelo de pipeline que escolheu.

## <a name="clean-up-resources"></a>Limpar os recursos

Se pretender experimentar outros quickstarts ou tutoriais, mantenha os recursos que criou. Caso contrário, vá ao portal Azure, vá aos seus grupos de recursos, selecione o grupo de recursos onde executou este tutorial e elimine todos os recursos.

## <a name="next-steps"></a>Passos seguintes

Rever desafios adicionais para utilizadores avançados:

* Utilize uma [câmara IP](https://en.wikipedia.org/wiki/IP_camera) que tenha suporte para RTSP em vez de utilizar o simulador RTSP. Pode pesquisar por câmaras IP que suportem RTSP na página de produtos [conformantes ONVIF.](https://www.onvif.org/conformant-products/) Procure dispositivos em conformidade com os perfis G, S ou T.
* Utilize um dispositivo Intel x64 Linux em vez de um VM Azure Linux. Este dispositivo deve estar na mesma rede que a câmara IP. Pode seguir as instruções no [tempo de funcionamento do Azure IoT Edge no Linux](../../iot-edge/how-to-install-iot-edge.md). Em seguida, registe o dispositivo com o Azure IoT Hub seguindo as instruções no [Implementar o seu primeiro módulo IoT Edge num dispositivo Linux virtual](../../iot-edge/quickstart-linux.md).
