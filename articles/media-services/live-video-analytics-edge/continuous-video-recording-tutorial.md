---
title: Gravação contínua de vídeo para a nuvem e reprodução do tutorial de nuvem - Azure
description: Neste tutorial, você vai aprender a usar Azure Live Video Analytics em Azure IoT Edge para gravar continuamente o vídeo para a nuvem e transmitir qualquer parte desse vídeo usando a Azure Media Services.
ms.topic: tutorial
ms.date: 05/27/2020
ms.openlocfilehash: 8659bd2e029da13870b50dd6535e959bc90c81a7
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99551064"
---
# <a name="tutorial-continuous-video-recording-to-the-cloud-and-playback-from-the-cloud"></a>Tutorial: Gravação contínua de vídeo para a nuvem e reprodução da nuvem

Neste tutorial, você aprenderá a usar a Azure Live Video Analytics em Azure IoT Edge para executar [gravação contínua](continuous-video-recording-concept.md) de vídeo (CVR) para a nuvem e transmitir qualquer parte desse vídeo usando a Azure Media Services. Esta capacidade é útil para cenários como segurança e conformidade onde há necessidade de manter um arquivo das imagens de uma câmara durante dias ou semanas. 

Neste tutorial você:

> [!div class="checklist"]
> * Criar os recursos relevantes.
> * Examine o código que executa o CVR.
> * Executar o código de amostra.
> * Examine os resultados e veja o vídeo.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Pré-leitura sugerida  

Leia estes artigos antes de começar:

* [Análise de vídeo ao vivo na visão geral do IoT Edge](overview.md)
* [Análise de vídeo ao vivo na terminologia IoT Edge](terminology.md)
* [Conceitos de gráficos de mídia](media-graph-concept.md) 
* [Cenários contínuos de gravação de vídeo](continuous-video-recording-concept.md)

## <a name="prerequisites"></a>Pré-requisitos

Os pré-requisitos para este tutorial são:

* [Código de Estúdio Visual](https://code.visualstudio.com/) na sua máquina de desenvolvimento com as [ferramentas Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) e as extensões [C#.](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)

    > [!TIP]
    > Pode ser solicitado a instalar o Docker. Ignore este pedido.
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/thank-you/sdk-3.1.201-windows-x64-installer) na sua máquina de desenvolvimento.
* Preencha o [roteiro de configuração de recursos de vídeo ao vivo](https://github.com/Azure/live-video-analytics/tree/master/edge/setup).

No final destes passos, terá recursos Azure relevantes implantados na sua subscrição Azure:

* Azure IoT Hub
* Conta de armazenamento do Azure
* Conta Azure Media Services
* Linux VM em Azure, com o [tempo de execução IoT Edge](../../iot-edge/how-to-install-iot-edge.md) instalado

> [!TIP]
> Se encontrar problemas com os recursos da Azure que são criados, por favor, consulte o nosso **[guia de resolução de problemas](troubleshoot-how-to.md#common-error-resolutions)** para resolver alguns problemas comumente encontrados.

## <a name="concepts"></a>Conceitos

Como explicado no artigo de [conceito de gráfico de mídia,](media-graph-concept.md) um gráfico de mídia permite definir:

- De onde os meios de comunicação devem ser capturados.
- Como deve ser processado.
- Onde os resultados devem ser entregues. 
 
 Para realizar o CVR, é necessário capturar o vídeo a partir de uma câmara capaz de RTSP e gravá-lo continuamente para um [ativo da Azure Media Services](terminology.md#asset). Este diagrama mostra uma representação gráfica do gráfico mediático.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/continuous-video-recording-tutorial/continuous-video-recording-overview.svg" alt-text="Grafo do suporte de dados":::

Neste tutorial, utilizará um módulo de uma borda construído utilizando o [Live555 Media Server](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) para simular uma câmara RTSP. Dentro do gráfico de mídia, você usará um nó [de fonte RTSP](media-graph-concept.md#rtsp-source) para obter o feed ao vivo e enviar esse vídeo para o [nó da pia do ativo](media-graph-concept.md#asset-sink), que grava o vídeo para um ativo. O vídeo que será usado neste tutorial é [um vídeo de amostra de intersecção rodoviária.](https://lvamedia.blob.core.windows.net/public/camera-300s.mkv)
<iframe src="https://www.microsoft.com/en-us/videoplayer/embed/RE4LTY4" width="640" height="320" allowFullScreen="true" frameBorder="0"></iframe>
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LTY4]

## <a name="set-up-your-development-environment"></a>Configurar o ambiente de desenvolvimento

Antes de começar, verifique se completou a terceira bala em [Pré-requisitos.](#prerequisites) Depois de terminar o script de configuração do recurso, selecione os suportes encaracolados para expor a estrutura da pasta. Você verá alguns ficheiros criados sob o diretório de amostras ~/clouddrive/lva.

![Definições da aplicação](./media/quickstarts/clouddrive.png)

De interesse neste tutorial estão os ficheiros:

* **~/clouddrive/lva-sample/edge-deployment/.env**: Contém propriedades que o Código do Estúdio Visual utiliza para implantar módulos num dispositivo de borda.
* **~/clouddrive/lva-sample/appsettings.jsem**: Usado pelo Código do Estúdio Visual para executar o código de amostra.

Vai precisar dos ficheiros para estes passos:

1. Clone o repo a partir da ligação https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp GitHub.
1. Inicie o Código do Estúdio Visual e abra a pasta onde descarregou o repo.
1. No Código do Estúdio Visual, navegue na pasta src/cloud-to-device-console-app e crie um ficheiro com o nome **appsettings.jsligado .** Este ficheiro contém as definições necessárias para executar o programa.
1. Copie o conteúdo da amostra ~/clouddrive/lva-sample/appsettings.jsno ficheiro. O texto deve parecer:
    ```
    {  
        "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",  
        "deviceId" : "lva-sample-device",  
        "moduleId" : "lvaEdge"  
    }
    ```
    A cadeia de ligação IoT Hub permite-lhe utilizar o Código do Estúdio Visual para enviar comandos para os módulos de borda através do Azure IoT Hub.
    
1. Em seguida, navegue na pasta src/edge e crie um ficheiro chamado **.env**.
1. Copie o conteúdo do ficheiro ~/clouddrive/lva-sample/edge-deployment/.env. O texto deve parecer:

    ```
    SUBSCRIPTION_ID="<Subscription ID>"  
    RESOURCE_GROUP="<Resource Group>"  
    AMS_ACCOUNT="<AMS Account ID>"  
    IOTHUB_CONNECTION_STRING="HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx"  
    AAD_TENANT_ID="<AAD Tenant ID>"  
    AAD_SERVICE_PRINCIPAL_ID="<AAD SERVICE_PRINCIPAL ID>"  
    AAD_SERVICE_PRINCIPAL_SECRET="<AAD SERVICE_PRINCIPAL ID>"  
    VIDEO_INPUT_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"  
    VIDEO_OUTPUT_FOLDER_ON_DEVICE="/home/lvaadmin/samples/output"  
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry username>"      
    ```

## <a name="examine-the-sample-files"></a>Examinar os ficheiros de amostras

No Código do Estúdio Visual, abra src/edge/deployment.template.jsligado. Este modelo define quais os módulos de borda que irá implantar no dispositivo de borda (o Azure Linux VM). Existem duas entradas na secção de **módulos** com os seguintes nomes:

* **LvaEdge**: Este é o vídeo ao vivo analítico no módulo IoT Edge.
* **rtspsim**: Este é o simulador RTSP.

Em seguida, navegue na pasta src/cloud-to-device-console-app. Aqui verá o appsettings.jsno ficheiro que criou juntamente com outros ficheiros:

* **c2d-console-app.csproj**: O ficheiro do projeto para Visual Studio Code.
* **operations.jsem:** Este ficheiro lista as diferentes operações que executaria.
* **Program.cs**: O código do programa de amostra, que:
    * Carrega as definições da aplicação.
    * Invoca métodos diretos expostos pelo live video analytics no módulo IoT Edge. Pode utilizar o módulo para analisar streams de vídeo ao vivo invocando os seus [métodos diretos.](direct-methods.md)
    * Pausa para examinar a saída do programa na janela **TERMINAL** e os eventos gerados pelo módulo na janela **OUTPUT.**
    * Invoca métodos diretos para limpar recursos.

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Gerar e implementar o manifesto de implantação IoT Edge 

O manifesto de implantação define quais os módulos que são implantados num dispositivo de borda e as definições de configuração desses módulos. Siga estes passos para gerar um manifesto a partir do ficheiro do modelo e, em seguida, desloque-o para o dispositivo de borda.

1. Inicie o Visual Studio Code.
1. Desagre a cadeia de ligação IoT Hub selecionando o ícone **Mais ações** ao lado do painel **AZURE IOT HUB** no canto inferior esquerdo. Copie o fio da src/cloud-to-device-console-app/appsettings.jsno ficheiro. 

    ![Definir cadeia de conexão IoT Hub](./media/quickstarts/set-iotconnection-string.png)
    > [!NOTE]
    > Pode ser-lhe pedido que forneça informações sobre o ponto final incorporado para o IoT Hub. Para obter essa informação, no portal Azure, navegue até ao seu Hub IoT e procure a opção **de pontos finais incorporados** no painel de navegação esquerdo. Clique lá e procure o **ponto final compatível com o Event Hub** na secção de ponto final **compatível com o Event Hub.** Copie e use o texto na caixa. O ponto final será mais ou menos assim:  
        ```
        Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
        ```

1. Clique com o botão direito para o src/edge/deployment.template.jsno ficheiro e selecione **Generate IoT Edge Deployment Manifest**. O Código do Estúdio Visual utiliza os valores do ficheiro .env para substituir as variáveis encontradas no ficheiro do modelo de implantação. Esta ação cria um ficheiro manifesto na pasta src/edge/config chamada **deployment.amd64.jsem**.

   ![Gerar manifesto de implantação IoT Edge](./media/quickstarts/generate-iot-edge-deployment-manifest.png)
1. Clique com o botão direito para o src/edge/config/deployment.amd64.jsno ficheiro e selecione **Criar Implementação para um único dispositivo**.

   ![Criar implementação para dispositivo único](./media/quickstarts/create-deployment-single-device.png)
1. Em seguida, é-lhe pedido que **selecione um dispositivo IoT Hub**. Selecione o dispositivo de amostra de Lva da lista de drop-down.
1. Em cerca de 30 segundos, refresque o Azure IoT Hub na secção inferior esquerda. Deve ver que o dispositivo de borda tem os seguintes módulos implantados:
    * Vídeo ao vivo analytics no IoT Edge (nome do módulo **lvaEdge)**
    * Simulador RTSP (nome do módulo **rtspsim)**
 
    ![IoT Hub](./media/continuous-video-recording-tutorial/iot-hub.png)

## <a name="prepare-to-monitor-the-modules"></a>Preparar para monitorizar os módulos 

Quando utiliza o módulo Live Video Analytics no IoT Edge para gravar o stream de vídeo ao vivo, envia eventos para o IoT Hub. Para ver estes eventos, siga estes passos:

1. Abra o painel Explorer no Código do Estúdio Visual e procure **o Azure IoT Hub** no canto inferior esquerdo.
1. Expandir o nó **dispositivos.**
1. Clique com o botão direito no ficheiro do dispositivo de amostra de Lva e selecione **Start Monitoring Built-in Event Endpoint**.

    ![Iniciar monitorização do ponto final do evento incorporado](./media/quickstarts/start-monitoring-iothub-events.png)

    > [!NOTE]
    > Pode ser-lhe pedido que forneça informações sobre o ponto final incorporado para o IoT Hub. Para obter essa informação, no portal Azure, navegue até ao seu Hub IoT e procure a opção **de pontos finais incorporados** no painel de navegação esquerdo. Clique lá e procure o **ponto final compatível com o Event Hub** na secção de ponto final **compatível com o Event Hub.** Copie e use o texto na caixa. O ponto final será mais ou menos assim:  
        ```
        Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
        ```

## <a name="run-the-program"></a>Execute o programa 

1. No Código do Estúdio Visual, abra o **separador Extensões** (ou prima Ctrl+Shift+X) e procure por Azure IoT Hub.
1. Clique no direito e selecione **Definições de extensão**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Definições de extensão":::
1. Procure e ative "Mostrar Mensagem Verbose".

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Mostrar mensagem verbose":::
1. Aceda a src/cloud-to-device-console-app/operations.js.
1. Sob o nó **GraphTopologySet,** edite o seguinte:

    `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/cvr-asset/2.0/topology.json" `
1. Em seguida, sob os nódos **GraphInstanceSet** e **GraphTopologyDelete,** certifique-se de que o valor da **topologiaName** corresponde ao valor da propriedade do **nome** na topologia do gráfico anterior:

    `"topologyName" : "CVRToAMSAsset"`  
1. Abra a [topologia](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/cvr-asset/2.0/topology.json) num browser e veja o assetNamePattern. Para se certificar de que tem um ativo com um nome único, pode querer alterar o nome da instância do gráfico no operations.jsno ficheiro (a partir do valor predefinido do Sample-Graph-1).

    `"assetNamePattern": "sampleAsset-${System.GraphTopologyName}-${System.GraphInstanceName}"`    
1. Inicie uma sessão de depurar selecionando F5. Verá algumas mensagens impressas na janela **TERMINAL.**
1. A operations.jsno ficheiro começa com chamadas para GraphTopologyList e GraphInstanceList. Se tiver limpo recursos após inícios ou tutoriais anteriores, esta ação retorna listas vazias e, em seguida, faz uma pausa para que você **selecione Enter**, como mostrado:

    ```
    --------------------------------------------------------------------------
    Executing operation GraphTopologyList
    -----------------------  Request: GraphTopologyList  --------------------------------------------------
    {
      "@apiVersion": "2.0"
    }
    ---------------  Response: GraphTopologyList - Status: 200  ---------------
    {
      "value": []
    }
    --------------------------------------------------------------------------
    Executing operation WaitForInput
    Press Enter to continue
    ```

1. Depois de selecionar **Enter** in the **TERMINAL** window, é feito o próximo conjunto de chamadas de métodos diretos:
   * Uma chamada para o GraphTopologySet usando o topologia anteriorUrl
   * Uma chamada para o GraphInstanceSet utilizando o seguinte corpo
     
     ```
     {
       "@apiVersion": "2.0",
       "name": "Sample-Graph-1",
       "properties": {
         "topologyName": "CVRToAMSAsset",
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
   * Uma chamada para o GraphInstanceActivate para iniciar a instância do gráfico e para iniciar o fluxo de vídeo
   * Uma segunda chamada para o GraphInstanceList para mostrar que a instância do gráfico está no estado de execução 
1. A saída na janela **TERMINAL** para agora numa **Entrada de Imprensa para continuar a** carregar. Não selecione **Enter** neste momento. Percorra para ver as cargas de resposta JSON para os métodos diretos que invocou.
1. Se agora mudar para a janela **OUTPUT** no Código do Estúdio Visual, verá mensagens a serem enviadas para o IoT Hub pelo módulo Live Video Analytics no módulo IoT Edge.

   Estas mensagens são discutidas na secção seguinte.
1. A instância do gráfico continua a correr e a gravar o vídeo. O simulador RTSP continua a dar a volta ao vídeo de origem. Para parar a gravação, volte à janela **TERMINAL** e selecione **Enter**. A próxima série de chamadas são feitas para limpar recursos utilizando:

   * Uma chamada para o GraphInstanceDeactivar para desativar a instância do gráfico.
   * Uma chamada para o GraphInstanceDelete para apagar o caso.
   * Uma chamada para a GraphTopologyDelete para apagar a topologia.
   * Uma chamada final para a GraphTopologyList para mostrar que a lista está agora vazia.

## <a name="interpret-the-results"></a>Interpretar os resultados 

Quando executam o gráfico de mídia, o módulo Live Video Analytics no IoT Edge envia certos eventos de diagnóstico e operacionais para o hub IoT Edge. Estes eventos são as mensagens que vê na janela **OUTPUT** do Código do Estúdio Visual. Contêm uma secção do corpo e uma secção de aplicações. Para compreender o que estas secções representam, consulte [Criar e ler mensagens IoT Hub](../../iot-hub/iot-hub-devguide-messages-construct.md).

Nas seguintes mensagens, as propriedades da aplicação e o conteúdo do corpo são definidos pelo módulo Live Video Analytics.

## <a name="diagnostics-events"></a>Eventos de diagnóstico 

### <a name="mediasession-established-event"></a>Evento MediaSession Established

Quando a instância do gráfico é ativada, o nó de origem RTSP tenta ligar-se ao servidor RTSP em execução no módulo rtspsim. Se for bem sucedido, imprime este evento:

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 XXX.XX.XX.XX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-04-09T09:42:18.1280000Z"
  }
}
```

* A mensagem é um evento de Diagnóstico (MediaSessionEstablished). Indica que o nó de origem RTSP (o sujeito) estabeleceu uma ligação com o simulador RTSP e começou a receber um feed ao vivo (simulado).
* A secção de assunto na aplicaçãoDeproperties refere o nó na topologia do gráfico a partir do qual a mensagem foi gerada. Neste caso, a mensagem provém do nó de origem RTSP.
* A secção de 'eventType' na aplicaçãoProperties indica que se trata de um evento de Diagnóstico.
* A secção EventTime indica a hora em que ocorreu o evento.
* A secção do corpo contém dados sobre o evento de Diagnóstico, que neste caso são os detalhes do [SDP.](https://en.wikipedia.org/wiki/Session_Description_Protocol)

## <a name="operational-events"></a>Eventos operacionais 

### <a name="recordingstarted-event"></a>Evento "RecordingStarted"

Quando o nó da pia do ativo começa a gravar o vídeo, emite este evento do tipo **Microsoft.Media.Graph.Operational.RecordingStarted**:

```
[IoTHubMonitor] [9:42:38 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAsset-CVRToAMSAsset-Sample-Graph-1"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": "2020-04-09T09:42:38.1280000Z",
    "dataVersion": "1.0"
  }
}
```

A secção de assunto na aplicaçãoProperties refere o nó do lavatório do ativo no gráfico, que gerou esta mensagem.

A secção do corpo contém informações sobre a localização da saída. Neste caso, é o nome do ativo Azure Media Services no qual o vídeo é gravado. Tome nota deste valor.

### <a name="recordingavailable-event"></a>Evento disponível de gravação

Como o nome sugere, o evento RecordingStarted é enviado quando a gravação foi iniciada, mas os dados de vídeo podem ainda não ter sido enviados para o ativo. Quando o nó da pia do ativo tiver enviado dados de vídeo para o ativo, emite este evento do tipo **Microsoft.Media.Graph.Operational.RecordingAvailable**:

```
[IoTHubMonitor] [[9:43:38 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAsset-CVRToAMSAsset-Sample-Graph-1"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingAvailable",
    "eventTime": "2020-04-09T09:43:38.1280000Z",
    "dataVersion": "1.0"
  }
}
```

Este evento indica que foram escritos dados suficientes para que jogadores ou clientes iniciassem a reprodução do vídeo.

A secção de assunto na aplicaçãoProperties refere o nó Desafundado do ActivoSink no gráfico, que gerou esta mensagem.

A secção do corpo contém informações sobre a localização da saída. Neste caso, é o nome do ativo Azure Media Services no qual o vídeo é gravado.

### <a name="recordingstopped-event"></a>Evento de cobertura de Gravações

Quando desativa o Graph Instance, o nó da pia do ativo para de gravar o vídeo para o ativo. Emite este evento do tipo **Microsoft.Media.Graph.Operational.RecordingS com:**

```
[IoTHubMonitor] [11:33:31 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAsset-CVRToAMSAsset-Sample-Graph-1"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStopped",
    "eventTime": "2020-04-10T11:33:31.051Z",
    "dataVersion": "1.0"
  }
}
```

Este evento indica que a gravação parou.

A secção de assunto na aplicaçãoProperties refere o nó Desafundado do ActivoSink no gráfico, que gerou esta mensagem.

A secção do corpo contém informações sobre a localização da saída, que neste caso é o nome do ativo Azure Media Services no qual o vídeo é gravado.

## <a name="media-services-asset"></a>Ativo de Serviços de Mídia  

Pode examinar o ativo Media Services que foi criado pelo gráfico mediático iniciando sessão no portal Azure e visualizando o vídeo.

1. Abra o seu navegador web e vá ao [portal Azure.](https://portal.azure.com/) Introduza as suas credenciais para iniciar sessão no portal. A vista predefinida é o dashboard de serviço.
1. Localize a sua conta de Serviços de Comunicação social entre os recursos que tem na sua subscrição e abra o painel de conta.
1. Selecione **Ativos** na lista **de Serviços de Mídia.**

    ![Ativos de Serviços de Mídia](./media/continuous-video-recording-tutorial/assets.png)
1. Encontrará um ativo listado com o nome de amostraAsset-CVRToAMSAsset-Sample-Graph-1. Este é o padrão de nomeação escolhido no seu arquivo de topologia de gráficos.
1. Selecione o elemento.
1. Na página de detalhes do ativo, **selecione Criar novo** na caixa de texto URL de **streaming.**

    ![Novo ativo](./media/continuous-video-recording-tutorial/new-asset.png)

1. No assistente que abre, aceite as opções predefinitivas e **selecione Adicionar**. Para obter mais informações, consulte [a reprodução de Vídeo.](video-playback-concept.md)

    > [!TIP]
    > Certifique-se de que o seu [ponto final de streaming está a funcionar](../latest/streaming-endpoint-concept.md).
1. O jogador deve carregar o vídeo. Selecione **Reproduzir** para vê-lo.

> [!NOTE]
> Como a fonte do vídeo era um contentor que simulava uma ração de câmara, as datas do vídeo estão relacionadas com a ativação da instância do gráfico e quando o desativou. Para ver como navegar numa gravação multi-dia e ver partes desse arquivo, consulte a [Reprodução de gravações de vários dias](playback-multi-day-recordings-tutorial.md) tutorial. Nesse tutorial, também pode ver as marcações de tempo no vídeo exibido no ecrã.

## <a name="clean-up-resources"></a>Limpar os recursos

Se pretende experimentar os outros tutoriais, guarde os recursos que criou. Caso contrário, vá ao portal Azure, navegue pelos seus grupos de recursos, selecione o grupo de recursos sob o qual executou este tutorial e elimine o grupo de recursos.

## <a name="next-steps"></a>Passos seguintes

* Utilize uma [câmara IP](https://en.wikipedia.org/wiki/IP_camera) com suporte para RTSP em vez de utilizar o simulador RTSP. Pode pesquisar por câmaras IP com suporte RTSP na página de [produtos conformantes ONVIF,](https://www.onvif.org/conformant-products/) procurando dispositivos que estejam em conformidade com os perfis G, S ou T.
* Utilize um dispositivo Linux AMD64 ou X64 (vs. utilizando um VM Azure Linux). Este dispositivo deve estar na mesma rede que a câmara IP. Siga as instruções no [tempo de funcionamento do Azure IoT Edge no Linux](../../iot-edge/how-to-install-iot-edge.md). Em seguida, siga as instruções no implante do [seu primeiro módulo IoT Edge para um dispositivo Linux virtual](../../iot-edge/quickstart-linux.md) para registar o dispositivo com O Azure IoT Hub.