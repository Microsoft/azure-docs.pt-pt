---
title: Analise o vídeo ao vivo utilizando o OpenVINO™ Model Server – Extensão de IA da Intel
description: Neste tutorial, utilizará um servidor de modelo de IA fornecido pela Intel para analisar o feed de vídeo ao vivo a partir de uma câmara IP (simulada).
ms.topic: tutorial
ms.date: 09/08/2020
titleSuffix: Azure
ms.openlocfilehash: 5751184493fffeeaf647507e9e9b00834f63ab5e
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105557263"
---
# <a name="tutorial-analyze-live-video-by-using-openvino-model-server--ai-extension-from-intel"></a>Tutorial: Analise o vídeo ao vivo utilizando o OpenVINO™ Model Server – Extensão de IA da Intel 

Este tutorial mostra-lhe como utilizar o OpenVINO™ Model Server – Extensão de IA da Intel para analisar um feed de vídeo ao vivo a partir de uma câmara IP (simulada). Você verá como este servidor de inferência lhe dá acesso a modelos para detetar objetos (uma pessoa, um veículo ou uma bicicleta) e um modelo para classificar veículos. Um subconjunto dos quadros do feed de vídeo ao vivo é enviado para este servidor de inferência, e os resultados são enviados para ioT Edge Hub.

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

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LUbN]

Neste arranque rápido, utilizará o Live Video Analytics no IoT Edge juntamente com o OpenVINO™ Model Server – Extensão de IA da Intel para detetar objetos como veículos ou classificá-los. Publicará os eventos de inferência resultantes no IoT Edge Hub.

## <a name="overview"></a>Descrição Geral

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/use-intel-openvino-tutorial/http-extension-with-vino.svg" alt-text="Descrição geral":::

Este diagrama mostra como os sinais fluem neste arranque rápido. Um [módulo de borda](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simula uma câmara IP que hospeda um servidor Real-Time de Protocolo de Streaming (RTSP). Um nó [de origem RTSP](media-graph-concept.md#rtsp-source) puxa o feed de vídeo deste servidor e envia quadros de vídeo para o nó [do processador de extensão HTTP.](media-graph-concept.md#http-extension-processor) 

O nó de extensão HTTP desempenha o papel de um representante. Ele mostra os quadros de vídeo de entrada definidos pelo `samplingOptions` campo e também converte os quadros de vídeo para o tipo de imagem especificado. Em seguida, transmite a imagem sobre REST para outro módulo de borda que executa os modelos de IA atrás de um ponto final HTTP. Neste exemplo, este módulo de borda é o OpenVINO™ Model Server – Extensão de IA da Intel. O nó do processador de extensão HTTP recolhe os resultados de deteção e publica eventos no nó [de pia IoT Hub.](media-graph-concept.md#iot-hub-message-sink) O nó envia então esses eventos para [ioT Edge Hub](../../iot-fundamentals/iot-glossary.md#iot-edge-hub).

Neste tutorial, vai:

1. Crie e implemente o gráfico de mídia, modificando-o.
1. Interprete os resultados.
1. Limpe os recursos.

## <a name="about-openvino-model-server--ai-extension-from-intel"></a>Sobre o OpenVINO™ Model Server – Extensão de IA da Intel

O Intel® Distribution of [OpenVINO™ toolkit](https://software.intel.com/content/www/us/en/develop/tools/openvino-toolkit.html) (inferência visual aberta e otimização da rede neural) é um kit de software gratuito que ajuda desenvolvedores e cientistas de dados a acelerar as cargas de trabalho da visão computacional, agilizar inferências e implementações de aprendizagem profunda, e permitir uma execução fácil e heterogénea através de plataformas ® Intel de ponta a nuvem. Inclui o Conjunto de Ferramentas de Implementação de Deep Learning ® Intel com motor de otimização de modelos e inferência, e o repositório [Open Model Zoo](https://github.com/openvinotoolkit/open_model_zoo) que inclui mais de 40 modelos pré-treinados otimizados.

Para construir soluções complexas e de vídeo ao vivo de alta performance, o módulo Live Video Analytics no IoT Edge deve ser emparelhado com um poderoso motor de inferência que pode alavancar a balança na borda. Neste tutorial, os pedidos de inferência são enviados para o [OpenVINO™ Model Server – Extensão AI da Intel](https://aka.ms/lva-intel-ovms), um módulo Edge que foi projetado para trabalhar com o Live Video Analytics no IoT Edge. Este módulo de servidor de inferência contém o OpenVINO™ Model Server (OVMS), um servidor de inferência alimentado pelo conjunto de ferramentas OpenVINO™, altamente otimizado para cargas de trabalho de visão computacional e desenvolvido para arquiteturas intel®. Foi adicionada uma extensão ao OVMS para uma fácil troca de quadros de vídeo e resultados de inferência entre o servidor de inferência e o módulo Live Video Analytics no IoT Edge, capacitando-o assim a executar qualquer modelo suportado por conjunto de ferramentas OpenVINO™ (pode personalizar o módulo do servidor de inferência modificando o [código).](https://github.com/openvinotoolkit/model_server/tree/master/extras/ams_wrapper) Pode selecionar a partir da grande variedade de mecanismos de aceleração fornecidos pelo hardware ® Intel. Estes incluem CPUs (Átomo, Núcleo, Xeon), FPGAs, VPUs.

Na versão inicial deste servidor de inferência, tem acesso aos [seguintes modelos:](https://github.com/openvinotoolkit/model_server/tree/master/extras/ams_models)

- Deteção do veículo (URL de inferência: http://{módulo-nome}:4000/veículoDetecção)
- Deteção de pessoa/veículo/bicicleta (URL de inferência: http://{módulo-nome}:4000/personVehicleBikeDetection)
- Classificação do veículo (URL de inferência: http://{módulo-nome}:4000/vehicleClassificação)
- Deteção facial (URL de inferência: http://{módulo-nome}:4000/faceDetecção)

> [!NOTE]
> Ao descarregar e utilizar o módulo Edge: OpenVINO™ Model Server – Extensão de IA da Intel, e o software incluído, concorda com os termos e condições previstos no [Contrato de Licença](https://www.intel.com/content/www/us/en/legal/terms-of-use.html).
> A Intel está empenhada em respeitar os direitos humanos e em evitar a cumplicidade em violações dos direitos humanos. Consulte os [Princípios Globais dos Direitos Humanos da Intel.](https://www.intel.com/content/www/us/en/policy/policy-human-rights.html) Os produtos e software da Intel destinam-se apenas a ser utilizados em aplicações que não causem ou contribuam para uma violação de um direito humano reconhecido internacionalmente.

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

        `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtensionOpenVINO/2.0/topology.json"`

    * Em `GraphInstanceSet` , editar o nome da topologia do gráfico para corresponder ao valor no link anterior:

      `"topologyName" : "InferencingWithOpenVINO"`

    * Em `GraphTopologyDelete` , editar o nome:

      `"name": "InferencingWithOpenVINO"`

### <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Gerar e implementar o manifesto de implantação IoT Edge

1. Clique com o botão direito para o *src/edge/deployment.openvino.template.jsno* ficheiro e, em seguida, selecione **Generate IoT Edge Deployment Manifest**.

    ![Gerar manifesto de implantação de borda ioT](./media/use-intel-openvino-tutorial/generate-deployment-manifest.png)  

    A *deployment.openvino.amd64.jsno* ficheiro manifesto é criada na pasta *src/edge/config.*

1. Se tiver concluído o [movimento Detect e emitir eventos](detect-motion-emit-events-quickstart.md) de arranque rápido, então ignore este passo. 

    Caso contrário, perto do painel **AZURE IOT HUB** no canto inferior esquerdo, selecione o ícone **Mais ações** e, em seguida, selecione **set IoT Hub Connection String**. Pode copiar o fio do *appsettings.jsficheiro.* Ou, para garantir que configura o hub IoT adequado dentro do Código do Estúdio Visual, use o [comando do hub Select IoT](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub).
    
    ![Definir cadeia de conexão do hub IoT](./media/quickstarts/set-iotconnection-string.png)

> [!NOTE]
> Pode ser-lhe pedido que forneça informações sobre o ponto final incorporado para o IoT Hub. Para obter essa informação, no portal Azure, navegue até ao seu Hub IoT e procure a opção **de pontos finais incorporados** no painel de navegação esquerdo. Clique lá e procure o **ponto final compatível com o Event Hub** na secção de ponto final **compatível com o Event Hub.** Copie e use o texto na caixa. O ponto final será mais ou menos assim:  
    ```
    Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
    ```

1. Clique à direita *src/edge/config/deployment.openvino.amd64.js* e selecione **Criar Implementação para dispositivo único**. 

    ![Criar implementação para dispositivo único](./media/use-intel-openvino-tutorial/deploy-manifest.png)

1. Quando lhe for solicitado que selecione um dispositivo IoT Hub, selecione **o dispositivo de amostra de Lva**.
1. Após cerca de 30 segundos, no canto inferior esquerdo da janela, refresque o Azure IoT Hub. O dispositivo de borda mostra agora os seguintes módulos implantados:

    * O módulo Live Video Analytics, chamado **LvaEdge**
    * O **módulo rtspsim,** que simula um servidor RTSP e funciona como a fonte de um feed de vídeo ao vivo
    * O módulo **openvino,** que é o OpenVINO™ Model Server – Módulo de Extensão AI da Intel 

### <a name="prepare-to-monitor-events"></a>Preparar para monitorizar eventos

Clique com o botão direito no dispositivo Live Video Analytics e selecione **Start Monitoring Built-in Event Endpoint**. Precisa deste passo para monitorizar os eventos do IoT Hub na janela **OUTPUT** do Código do Estúdio Visual. 

![Iniciar a monitorização](./media/quickstarts/start-monitoring-iothub-events.png) 

### <a name="run-the-sample-program-to-detect-vehicles"></a>Executar o programa de amostragem para detetar veículos
Se abrir a [topologia](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtensionOpenVINO/topology.json) do gráfico para este tutorial num browser, verá que o valor foi definido para , o que significa que o servidor de `inferencingUrl` `http://openvino:4000/vehicleDetection` inferência irá devolver os resultados após a deteção de veículos, caso existam, no vídeo ao vivo.

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
             "topologyName": "InferencingWithOpenVINO",
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
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 nnn.nn.0.6\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/lots_015.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/lots_015.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=Z00AKeKQCgC3YC3AQEBpB4kRUA==,aO48gA==\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/sources/rtspSource",
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-07-24T16:42:18.1280000Z"
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

O nó do processador de extensão HTTP recebe resultados de inferência do módulo OpenVINO™ Model Server – Extensão AI. Em seguida, emite os resultados através do nó de pia IoT Hub como eventos de inferência. 

Nestes eventos, o tipo está definido `entity` para indicar que é uma entidade, como um carro ou um caminhão. O `eventTime` valor é a hora utc quando o objeto foi detetado. 

No exemplo seguinte, foram detetados dois veículos, com valores de confiança superiores a 0,9.

```
[IoTHubMonitor] [9:43:18 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "inferences": [
      {
        "type": "entity",
        "subtype": "vehicleDetection",
        "entity": {
          "tag": {
            "value": "vehicle",
            "confidence": 0.9951713681221008
          },
          "box": {
            "l": 0.042635321617126465,
            "t": 0.4004564881324768,
            "w": 0.10961548984050751,
            "h": 0.07942074537277222
          }
        }
      },
      {
        "type": "entity",
        "subtype": "vehicleDetection",
        "entity": {
          "tag": {
            "value": "vehicle",
            "confidence": 0.928486168384552
          },
          "box": {
            "l": 0.2506900727748871,
            "t": 0.07512682676315308,
            "w": 0.05470699071884155,
            "h": 0.07408371567726135
          }
        }
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/inferenceClient",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-07-24T16:43:18.1280000Z"
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
Para utilizar um modelo diferente, terá de modificar a topologia do gráfico e, bem como `operations.json` o ficheiro.

Copie a [topologia do gráfico](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtensionOpenVINO/topology.json) para um ficheiro local, por `C:\TEMP\topology.json` exemplo. Abra a cópia e edite o valor `inferencingUrl` `http://openvino:4000/personVehicleBikeDetection` de.

Em seguida, no Código do Estúdio Visual, vá para a pasta *src/cloud-to-device-console-app* e `operations.json` abra o ficheiro. Editar a linha `topologyUrl` com:

```
      "topologyFile" : "C:\\TEMP\\topology.json" 
```
Pode agora repetir os passos acima para executar novamente o programa de amostragem, com a nova topologia. Os resultados da inferência serão semelhantes (em esquema) ao do modelo de deteção do veículo, com apenas o `subtype` conjunto para `personVehicleBikeDetection` .

## <a name="run-the-sample-program-to-classify-vehicles"></a>Executar o programa de amostragem para classificar os veículos
No Código do Estúdio Visual, abra a cópia local `topology.json` do passo anterior e edite o valor de `inferencingUrl` `http://openvino:4000/vehicleClassification` . Se tiver executado o exemplo anterior para detetar pessoas, veículos ou bicicletas, não precisa de `operations.json` modificar novamente o ficheiro.

Pode agora repetir os passos acima para executar novamente o programa de amostragem, com a nova topologia. Um resultado de classificação da amostra é o seguinte.

```
[IoTHubMonitor] [9:44:18 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "inferences": [
      {
        "type": "classification",
        "subtype": "color",
        "classification": {
          "tag": {
            "value": "black",
            "confidence": 0.9179772138595581
          }
        }
      },
      {
        "type": "classification",
        "subtype": "type",
        "classification": {
          "tag": {
            "value": "truck",
            "confidence": 1
          }
        }
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/inferenceClient",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-07-24T16:44:18.1280000Z"
  }
}
```

## <a name="run-the-sample-program-to-detect-faces"></a>Executar o programa de amostra para detetar rostos
No Código do Estúdio Visual, abra a cópia local `topology.json` do passo anterior e edite o valor de `inferencingUrl` `http://openvino:4000/faceDetection` . Se tiver executado o exemplo anterior para detetar pessoas, veículos ou bicicletas, não precisa de `operations.json` modificar novamente o ficheiro.

Pode agora repetir os passos acima para executar novamente o programa de amostragem, com a nova topologia. Um resultado de deteção de amostras é o seguinte (nota: o vídeo do parque de estacionamento acima utilizado não contém quaisquer faces detetáveis - deve ser outro vídeo para experimentar este modelo).

```
[IoTHubMonitor] [9:54:18 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "inferences": [
      {
        "type": "entity",
        "subtype": "faceDetection",
        "entity": {
          "tag": {
            "value": "face",
            "confidence": 0.9997053742408752
          },
          "box": {
            "l": 0.2559490501880646,
            "t": 0.03403960168361664,
            "w": 0.17685115337371826,
            "h": 0.45835764706134796
          }
        }
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/inferenceClient",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-07-24T16:54:18.1280000Z"
  }
}
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se pretender experimentar outros quickstarts ou tutoriais, mantenha os recursos que criou. Caso contrário, vá ao portal Azure, vá aos seus grupos de recursos, selecione o grupo de recursos onde executou este tutorial e elimine todos os recursos.

## <a name="next-steps"></a>Passos seguintes

Rever desafios adicionais para utilizadores avançados:

* Utilize uma [câmara IP](https://en.wikipedia.org/wiki/IP_camera) que tenha suporte para RTSP em vez de utilizar o simulador RTSP. Pode pesquisar por câmaras IP que suportem RTSP na página de produtos [conformantes ONVIF.](https://www.onvif.org/conformant-products/) Procure dispositivos em conformidade com os perfis G, S ou T.
* Utilize um dispositivo Linux AMD64 ou x64 em vez de um VM Azure Linux. Este dispositivo deve estar na mesma rede que a câmara IP. Pode seguir as instruções no [tempo de funcionamento do Azure IoT Edge no Linux](../../iot-edge/how-to-install-iot-edge.md). Em seguida, registe o dispositivo com o Azure IoT Hub seguindo as instruções no [Implementar o seu primeiro módulo IoT Edge num dispositivo Linux virtual](../../iot-edge/quickstart-linux.md).