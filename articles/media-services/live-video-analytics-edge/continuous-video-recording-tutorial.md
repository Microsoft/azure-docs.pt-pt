---
title: Gravação contínua de vídeo para nuvem e reprodução de tutorial em nuvem - Azure
description: Neste tutorial, você aprenderá a usar o Live Video Analytics no IoT Edge para gravar continuamente o vídeo para a nuvem, e transmitir qualquer parte desse vídeo usando a Azure Media Services.
ms.topic: tutorial
ms.date: 05/27/2020
ms.openlocfilehash: a69d3f5db9dd8cbe25bbf79f44921f26258005cc
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/01/2020
ms.locfileid: "84261859"
---
# <a name="tutorial-continuous-video-recording-to-cloud-and-playback-from-cloud"></a>Tutorial: Gravação contínua de vídeo para nuvem e reprodução da nuvem  

Neste tutorial, você aprenderá a usar live video analytics no IoT Edge para executar [gravação contínua](continuous-video-recording-concept.md) de vídeo (CVR) para a nuvem, e transmitir qualquer parte desse vídeo usando Media Services. Isto é útil para cenários como segurança, conformidade, e outros, onde é necessário manter um arquivo das imagens de uma câmara durante vários dias (ou semanas).

> [!div class="checklist"]
> * Configurar os recursos relevantes
> * Examine o código que executa o CVR
> * Executar o código de amostra
> * Examine os resultados e veja o vídeo

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Pré-leitura sugerida  

Recomenda-se que leia as seguintes páginas de documentação

* [Análise de vídeo ao vivo na visão geral do IoT Edge](overview.md)
* [Análise de vídeo ao vivo na terminologia IoT Edge](terminology.md)
* [Conceitos de gráficos de mídia](media-graph-concept.md) 
* [Cenários contínuos de gravação de vídeo](continuous-video-recording-concept.md)

## <a name="prerequisites"></a>Pré-requisitos

Os pré-requisitos para este tutorial são os seguintes

* [Código de Estúdio Visual](https://code.visualstudio.com/) na sua máquina de desenvolvimento com extensão [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) e extensão [C#.](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)

    > [!TIP]
    > Pode ser solicitado a instalar o Docker. Pode ignorar esta solicitação.
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/thank-you/sdk-3.1.201-windows-x64-installer) na sua máquina de desenvolvimento.
* Roteiro completo [de configuração de recursos de vídeo ao vivo](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)

No final dos passos acima, terá certos recursos Azure implantados na subscrição do Azure, incluindo:

* IoT Hub
* Conta de armazenamento
* Conta Azure Media Services
* Linux VM em Azure, com [tempo de execução IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) instalado

## <a name="concepts"></a>Conceitos

Como [explicado aqui](media-graph-concept.md), um gráfico mediático permite definir de onde os meios de comunicação devem ser capturados, como deve ser processado e onde os resultados devem ser entregues. Para realizar o CVR, é necessário capturar o vídeo a partir de uma câmara capaz de RTSP e gravá-lo continuamente para um [ativo da Azure Media Services](terminology.md#asset). O diagrama abaixo mostra uma representação gráfica desse gráfico mediático.

![Gráfico de mídia](./media/continuous-video-recording-tutorial/continuous-video-recording-overview.png)

Neste tutorial, utilizará um módulo Edge construído utilizando o [Live555 Media Server](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) para simular uma câmara RTSP. Dentro do gráfico de mídia, você usará um nó [de fonte RTSP](media-graph-concept.md#rtsp-source) para obter o feed ao vivo, e enviará esse vídeo para o [nó da pia](media-graph-concept.md#asset-sink) do ativo que irá gravar o vídeo para um ativo.

## <a name="set-up-your-development-environment"></a>Configurar o ambiente de desenvolvimento

Antes de começar, verifique se completou a 3ª bala em [Pré-requisitos.](#prerequisites) Assim que o script de configuração do recurso terminar, clique nos suportes encaracolados para expor a estrutura da pasta. Você verá alguns ficheiros criados sob o diretório de amostras ~/clouddrive/lva.

![Definições da aplicação](./media/quickstarts/clouddrive.png)

De interesse neste tutorial são:

     * ~/clouddrive/lva-sample/edge-deployment/.env  - contains properties that Visual Studio Code uses to deploy modules to an edge device
     * ~/clouddrive/lva-sample/appsettings.json - used by Visual Studio Code for running the sample code

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

## <a name="examine-the-sample-files"></a>Examinar os ficheiros de amostras

No Código do Estúdio Visual abra "src/edge/deployment.template.json". Este modelo define quais os módulos de borda que irá implantar no dispositivo de borda (o Azure Linux VM). Note que existem duas entradas na secção "módulos", com os seguintes nomes:

* IvaEdge – este é o live video analytics no módulo IoT Edge
* rtspsim – este é o simulador RTSP

Em seguida, navegue para a pasta "src/cloud-to-device-console-app". Aqui verá o ficheiro appsettings.json que criou juntamente com alguns outros ficheiros:

* c2d-console-app.csproj - o ficheiro do projeto para Visual Studio Code.
* operations.json - este ficheiro lista as diferentes operações que iria executar
* Program.cs - o código do programa de amostra que faz o seguinte:
    * Carrega as definições da aplicação
    * Invoca métodos diretos expostos pelo live video analytics no módulo IoT Edge. Pode utilizar o módulo para analisar streams de vídeo ao vivo invocando os seus [métodos diretos](direct-methods.md)
    * Pausas para examinar a saída do programa na janela TERMINAL e os eventos gerados pelo módulo na janela OUTPUT
    * Invoca métodos diretos para limpar recursos

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Gerar e implementar o manifesto de implantação IoT Edge 

O manifesto de implantação define quais os módulos que são implantados num dispositivo de borda e configurações de configuração para esses módulos. Siga estes passos para gerar tal manifesto a partir do ficheiro do modelo e, em seguida, implante-o para o dispositivo de borda.

1. Abra o Visual Studio Code.
1. Desagre a cadeia de ligação IoTHub clicando no ícone "Mais ações" ao lado do painel AZURE IOT HUB no canto inferior esquerdo. Pode copiar o string a partir do ficheiro src/cloud-to-device-console-app/appsettings.json. 

    ![Definir cadeia de conexão IOT](./media/quickstarts/set-iotconnection-string.png)
1. Em seguida, clique à direita no ficheiro "src/edge/deployment.template.json" e clique em "Generate IoT Edge Deployment Manifest". O Código do Estúdio Visual utiliza os valores do ficheiro .env para substituir as variáveis encontradas no ficheiro do modelo de implantação. Isto deve criar um ficheiro manifesto na pasta src/edge/config chamada "deployment.amd64.json".

   ![Gerar manifesto de implantação IoT Edge](./media/quickstarts/generate-iot-edge-deployment-manifest.png)
1. Clique no "src/edge/config/deployment.amd64.json" e clique em "Criar Implementação para Dispositivo Único".

   ![Criar implementação para dispositivo único](./media/quickstarts/create-deployment-single-device.png)
1. Em seguida, será solicitado que "Selecione um dispositivo IoT Hub". Selecione o dispositivo de amostra de Lva a partir da queda para baixo.
1. Em cerca de 30 segundos, refresque o Hub Azure IoT na secção inferior esquerda e deverá ver que o dispositivo de borda tem os seguintes módulos implantados:
    * Vídeo ao vivo analytics no IoT Edge (nome do módulo "lvaEdge")
    * Simulador RTSP (nome do módulo "rtspsim")
 
    ![Hub IoT](./media/continuous-video-recording-tutorial/iot-hub.png)

## <a name="prepare-to-monitor-the-modules"></a>Preparar para monitorizar os módulos 

Quando utilizar o live video analytics no módulo IoT Edge para gravar o stream de vídeo ao vivo, enviará eventos para o IoT Hub. Para ver estes eventos, siga estes passos:

1. Abra o painel Explorer no Código do Estúdio Visual e procure o Azure IoT Hub no canto inferior esquerdo.
1. Expandir o nó dispositivos.
1. Clique no dispositivo lva-sample e escolheu a opção "Iniciar a monitorização de eventos incorporados".

    ![Comece a monitorizar o ponto final do evento incorporado](./media/quickstarts/start-monitoring-iothub-events.png)

## <a name="run-the-program"></a>Execute o programa 

1. Código do Estúdio Visual, navegue para "src/cloud-to-device-console-app/operations.json"
1. Sob o nó GráficoToplogiaSet, edite o seguinte:

    `"topologyUrl" : "https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/cvr-asset/topology.json" `
1. Em seguida, sob os nosmos GraphInstanceSet e GraphTopologyDelete, certifique-se de que o valor da topologiaName corresponde ao valor da propriedade "nome" na topologia do gráfico acima:

    `"topologyName" : "CVRToAMSAsset"`  
1. Abra a [topologia](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/cvr-asset/topology.json) num browser e veja o assetNamePattern. Para se certificar de que tem um ativo com um nome único, pode querer alterar o nome da instância do gráfico no ficheiro operations.json (a partir do valor predefinido de "Sample-Graph-1").

    `"assetNamePattern": "sampleAsset-${System.GraphTopologyName}-${System.GraphInstanceName}"`    
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
1. Quando premir a tecla "Entrar" na janela TERMINAL, é feito o próximo conjunto de chamadas de métodos diretos
     * Uma chamada para o GraphTopologySet usando o topologyUrl acima.
     * Uma chamada para o GraphInstanceSet utilizando o seguinte corpo.
     
     ```
     {
       "@apiVersion": "1.0",
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
     * Uma chamada para o GraphInstanceActivate para iniciar a instância do gráfico e iniciar o fluxo de vídeo
     * Uma segunda chamada para o GraphInstanceList para mostrar que a instância do gráfico está, de facto, no estado de execução  
1. A saída na janela TERMINAL para agora numa introdução de "Press Enter para continuar". Não bata em "Enter" neste momento. Pode rolar para cima para ver as cargas de resposta JSON para os métodos diretos que invocou
1. Se agora mudar para a janela OUTPUT no Código do Estúdio Visual, verá mensagens que estão a ser enviadas para o IoT Hub, pelo módulo Live Video Analytics no módulo IoT Edge.

     * Estas mensagens são discutidas na secção abaixo
1. A instância do gráfico continuará a ser executada e gravará o vídeo – o simulador RTSP continuará a fazer loop no vídeo de origem. Para parar de gravar, volte à janela TERMINAL e acerte "Enter". A próxima série de chamadas são feitas para limpar recursos:

     * Uma chamada para GraphInstanceDeactivar para desativar a instância do gráfico
     * Uma chamada para o GraphInstanceDelete para apagar o caso
     * Uma chamada para GraphTopologyDelete para apagar a topologia
     * Uma chamada final para a GraphTopologyList para mostrar que a lista está agora vazia

## <a name="interpret-the-results"></a>Interpretar os resultados 

Quando executam o gráfico de mídia, o módulo Live Video Analytics no IoT Edge envia certos eventos de diagnóstico e operacionais para o IoT Edge Hub. Estes eventos são as mensagens que vê na janela OUTPUT do Código do Estúdio Visual, que contém uma secção "body" e uma secção "aplicaçõesProperties". Para entender o que estas secções representam, leia [este](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct) artigo.

Nas mensagens abaixo, as propriedades da aplicação e o conteúdo do corpo são definidos pelo módulo Live Video Analytics.

## <a name="diagnostic-events"></a>Eventos de diagnóstico 

### <a name="mediasession-established-event"></a>Evento MediaSession Established

Quando a instância do gráfico é ativada, o nó de origem RTSP tenta ligar-se ao servidor RTSP em execução no módulo rtspsim. Se for bem sucedido, imprimirá este evento:

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

* A mensagem é um evento de Diagnóstico, MediaSessionEstablished, indica que o nó de origem RTSP (o sujeito) foi capaz de estabelecer a ligação com o simulador RTSP, e começar a receber um feed ao vivo (simulado).
* O "sujeito" na aplicaçãoDeproperias refere o nó na topologia do gráfico a partir do qual a mensagem foi gerada. Neste caso, a mensagem é originária do nó de origem RTSP.
* "eventType" na aplicaçãoProperties indica que este é um evento de Diagnóstico.
* "eventTime" indica a hora em que o evento ocorreu.
* "body" contém dados sobre o evento de diagnóstico, que, neste caso, é o [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) detalhes.

## <a name="operational-events"></a>Eventos operacionais 

### <a name="recordingstarted-event"></a>Evento "RecordingStarted"

Quando o nó da pia do ativo começa a gravar vídeo, emite este evento do tipo Microsoft.Media.Graph.Operational.RecordingStarted

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

O "sujeito" na aplicaçãoDeproperias refere o nó do lavatório do ativo no gráfico, que gerou esta mensagem.

O corpo contém informações sobre a localização da saída, que neste caso é o nome do ativo Azure Media Service no qual o vídeo é gravado. Deve anotar este valor.

### <a name="recordingavailable-event"></a>Evento disponível de gravação

Como o nome sugere, o evento RecordingStarted é enviado quando a gravação começou - mas os dados de vídeo podem ainda não ter sido enviados para o Ativo. Quando o nó da pia do ativo tiver enviado dados de vídeo para o ativo, emite este evento do tipo Microsoft.Media.Graph.Operational.RecordingAvailable

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

Este evento indica que foram escritos dados suficientes para que os jogadores/clientes iniciem a reprodução do vídeo.

O "sujeito" na aplicaçãoProperties refere-se ao nó AssetSink no gráfico, que gerou esta mensagem.

O corpo contém informações sobre a localização da saída, que neste caso é o nome do ativo Azure Media Service no qual o vídeo é gravado.

### <a name="recordingstopped-event"></a>Evento de cobertura de Gravações

Quando desativa o Graph Instance, o nó da pia do ativo para de gravar o vídeo para o ativo, emite este evento do tipo Microsoft.Media.Graph.Operational.RecordingS com cobertura.

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

O "sujeito" na aplicaçãoProperties refere-se ao nó AssetSink no gráfico, que gerou esta mensagem.

O corpo contém informações sobre a localização da saída, que neste caso é o nome do ativo Azure Media Service no qual o vídeo é gravado.

## <a name="media-services-asset"></a>Ativo de Serviços de Mídia  

Pode examinar o ativo Dos Serviços de Comunicação que foi criado pelo gráfico mediático, iniciando sessão no portal Azure e visualizando o vídeo.

1. Abra o seu navegador web e vá ao [portal Azure.](https://portal.azure.com/) Introduza as suas credenciais para iniciar sessão no portal. A vista predefinida é o dashboard de serviço.
1. Localize a sua conta de Serviços de Mídia entre os recursos que tem na sua subscrição e abra a lâmina da conta
1. Clique em Ativos na listagem de Serviços de Mídia

    ![Elementos](./media/continuous-video-recording-tutorial/assets.png)
1. Encontrará um Ativo listado com o nome da amostraAsset-CVRToAMSAsset-Sample-Graph-1 – este é o padrão de nomeação escolhido no seu ficheiro de topologia de gráficos.
1. Clique no Ativo.
1. Na página de detalhes do ativo, clique na **nova caixa** de texto de URL de streaming.

    ![Novo ativo](./media/continuous-video-recording-tutorial/new-asset.png)

1. No assistente que abre, aceite as opções predefinindo e acerte em "Adicionar". Para mais informações, veja a [reprodução de vídeo](video-playback-concept.md).

    > [!TIP]
    > Certifique-se de que o seu [ponto final de streaming está a funcionar](../latest/streaming-endpoint-concept.md).
1. O leitor deve carregar o vídeo e deverá ser capaz de acertar **play**>** para vê-lo.

> [!NOTE]
> Uma vez que a origem do vídeo era um contentor que simulava uma transmissão de câmara, os postes de tempo no vídeo estão relacionados com quando ativou a instância do gráfico, e quando o desativou. Você pode ver [este](playback-multi-day-recordings-tutorial.md) tutorial para ver como navegar numa gravação de vários dias, e ver partes desse arquivo. Nesse tutorial, também poderá ver os tempos no vídeo exibido no ecrã.

## <a name="clean-up-resources"></a>Limpar recursos

Se pretende experimentar os outros tutoriais, deve manter os recursos criados. Caso contrário, vá ao portal Azure, navegue pelos seus grupos de recursos, selecione o grupo de recursos sob o qual executou este tutorial e elimine o grupo de recursos.

## <a name="next-steps"></a>Passos seguintes

* Utilize uma [câmara IP](https://en.wikipedia.org/wiki/IP_camera) com suporte para RTSP em vez de utilizar o simulador RTSP. Pode pesquisar por câmaras IP com suporte RTSP na página de [produtos conformantes ONVIF,](https://www.onvif.org/conformant-products/) procurando dispositivos que estejam em conformidade com os perfis G, S ou T.
* Utilize um dispositivo Linux AMD64 ou X64 (vs. utilizando um VM Azure Linux). Este dispositivo deve estar na mesma rede que a câmara IP. Pode seguir as instruções no [tempo de funcionamento do Azure IoT Edge no Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) e, em seguida, seguir as instruções no [módulo IoT Edge para um dispositivo Linux virtual](https://docs.microsoft.com/azure/iot-edge/quickstart-linux) para registar o dispositivo com o Azure IoT Hub.
