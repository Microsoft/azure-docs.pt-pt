---
title: Detetar movimento e emitir eventos - Azure
description: Este quickstart mostra-lhe como usar o Live Video Analytics no IoT Edge para detetar movimentos e emitir eventos, chamando programáticamente métodos diretos.
ms.topic: quickstart
ms.date: 05/29/2020
ms.openlocfilehash: fca773d0583bee3bef4e7254bcca95866b2205e9
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87091917"
---
# <a name="quickstart-detect-motion-and-emit-events"></a>Quickstart: Detetar movimento e emitir eventos

Este quickstart acompanha-o através dos passos para começar com live video analytics em IoT Edge. Utiliza uma VM do Azure como um dispositivo IoT Edge e a transmissão de vídeo em direto simulada. Depois de completar os passos de configuração, poderá executar um vídeo simulado ao vivo através de um gráfico de mídia que detete e reporte qualquer movimento nesse fluxo. O diagrama seguinte mostra uma representação gráfica desse gráfico mediático.

![Análise de vídeo ao vivo baseada na deteção de movimentos](./media/analyze-live-video/motion-detection.png) 

Este artigo baseia-se no [código de amostra](https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp) escrito em C#.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure que tem uma subscrição ativa. [Crie uma conta gratuita se](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) ainda não tiver uma.
* [Código do Estúdio Visual](https://code.visualstudio.com/) com as seguintes extensões:
    * [Ferramentas do Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1). 

> [!TIP]
> Pode ser solicitado que instale o Docker enquanto instala a extensão Azure IoT Tools. Pode ignorar a solicitação.

## <a name="set-up-azure-resources"></a>Configurar recursos do Azure

Este tutorial requer os seguintes recursos Azure:

* Hub IoT
* Conta de armazenamento
* Conta Azure Media Services
* Linux VM em Azure, com [tempo de execução IoT Edge](../../iot-edge/how-to-install-iot-edge-linux.md) instalado

Para este arranque rápido, recomendamos que utilize o [script de configuração de recursos live video analytics](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) para implementar os recursos necessários na sua subscrição Azure. Para o fazer, siga estes passos:

1. Abra o [Azure Cloud Shell](https://shell.azure.com).
1. Se estiver a utilizar a Cloud Shell pela primeira vez, será solicitado que selecione uma subscrição para criar uma conta de armazenamento e uma partilha de Ficheiros Microsoft Azure. Selecione **Criar armazenamento** para criar uma conta de armazenamento para as informações da sessão Cloud Shell. Esta conta de armazenamento é separada da conta que o script criará para utilizar com a sua conta Azure Media Services.
1. No menu suspenso no lado esquerdo da janela Cloud Shell, selecione **Bash** como seu ambiente.

    ![Selecionador de ambiente](./media/quickstarts/env-selector.png)

1. Execute o seguinte comando.

    ```
    bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
    ```

    Se o script terminar com sucesso, deverá ver todos os recursos necessários na sua subscrição.

1. Depois de terminar o script, selecione os suportes encaracolados para expor a estrutura da pasta. Você verá alguns ficheiros no *diretório de amostras ~/clouddrive/Lva.* De interesse neste arranque rápido são:

     * ***~/clouddrive/lva-sample/edge-deployment/.env*** - Este ficheiro contém propriedades que o Código do Estúdio Visual utiliza para implantar módulos num dispositivo de borda.
     * ***~/clouddrive/lva-sample/appsetting.json*** - Visual Studio Code usa este ficheiro para executar o código de amostra.
     
Vai precisar destes ficheiros quando configurar o seu ambiente de desenvolvimento no Código do Estúdio Visual na secção seguinte. Talvez queira copiá-los num ficheiro local por enquanto.

 ![Definições da aplicação](./media/quickstarts/clouddrive.png)

## <a name="set-up-your-development-environment"></a>Configurar o ambiente de desenvolvimento

1. Clone o repo a partir deste local: https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp .
1. No Código do Estúdio Visual, abra a pasta onde o repo foi descarregado.
1. No Código do Estúdio Visual, aceda à pasta *src/cloud-to-device-console-app.* Lá, crie um ficheiro e diga-lhe *appsettings.js.* Este ficheiro conterá as definições necessárias para executar o programa.
1. Copie o conteúdo da *amostra /lva/lva/appsettings.jsno* ficheiro que gerou anteriormente neste arranque rápido.

    O texto deve parecer a seguinte saída.

    ```
    {  
        "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",  
        "deviceId" : "lva-sample-device",  
        "moduleId" : "lvaEdge"  
    }
    ```
1. Vá à pasta *src/edge* e crie um ficheiro chamado *.env*.
1. Copie o conteúdo do ficheiro */clouddrive/lva-sample/edge-deployment/.env.* O texto deve parecer o seguinte código.

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
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry username>"      
    ```

## <a name="examine-the-sample-files"></a>Examinar os ficheiros de amostras

1. No Código do Estúdio Visual, vá ao *src/edge*. Você verá o ficheiro *.env* e alguns ficheiros de modelo de implementação.

    O modelo de implantação refere-se ao manifesto de implantação do dispositivo de borda, onde são utilizadas variáveis para algumas propriedades. O ficheiro *.env* contém os valores para essas variáveis.
1. Aceda à pasta *src/cloud-to-device-app.* Aqui vê-se a *appsettings.jsficheiro* e alguns outros ficheiros:

    * ***c2d-console-app.csproj*** - O ficheiro do projeto para Visual Studio Code.
    * ***operations.jsem*** - Uma lista das operações que quer que o programa seja executado.
    * ***Program.cs*** - O código do programa de amostra. Este código:
    
      * Carrega as definições da aplicação.
      * Invoca métodos diretos que são expostos pelo Live Video Analytics no módulo IoT Edge. Pode utilizar o módulo para analisar streams de vídeo ao vivo invocando os seus [métodos diretos.](direct-methods.md)
      * Pausa para que possa examinar a saída do programa na janela **TERMINAL** e examinar os eventos gerados pelo módulo na janela **OUTPUT.**
      * Invoca métodos diretos para limpar recursos.   

## <a name="generate-and-deploy-the-deployment-manifest"></a>Gerar e implementar o manifesto de implantação

O manifesto de implantação define quais os módulos que são implantados num dispositivo de borda. Também define definições de configuração para esses módulos. 

Siga estes passos para gerar o manifesto a partir do ficheiro do modelo e, em seguida, implante-o para o dispositivo de borda.

1. Abra o Visual Studio Code.
1. Ao lado do painel **AZURE IOT HUB,** selecione o ícone **Mais ações** para definir a cadeia de ligação IoT Hub. Pode copiar o string a partir da *src/cloud-to-device-console-app/appsettings.jsno* ficheiro. 

    ![Definir cadeia de conexão IOT](./media/quickstarts/set-iotconnection-string.png)

1. Clique à direita **src/edge/deployment.template.js** e **selecione Generate IoT Edge Deployment Manifest**.

    ![Gere o manifesto de implantação IoT Edge](./media/quickstarts/generate-iot-edge-deployment-manifest.png)

    Esta ação deve criar um ficheiro manifesto denominado *deployment.amd64.jsna* pasta *src/edge/config.*
1. Clique com o **botão direito src/edge/config/deployment.amd64.jsligado**, selecione **Criar Implementação para Dispositivo Único**e, em seguida, selecione o nome do seu dispositivo de borda.

    ![Criar uma implementação para um único dispositivo](./media/quickstarts/create-deployment-single-device.png)

1. Quando lhe for solicitado que selecione um dispositivo IoT Hub, escolha o **dispositivo de amostra de Lva** no menu suspenso.
1. Após cerca de 30 segundos, no canto inferior esquerdo da janela, refresque o Azure IoT Hub. O dispositivo de borda mostra agora os seguintes módulos implantados:

    * Vídeo ao vivo Analytics no IoT Edge (nome do `lvaEdge` módulo)
    * Simulador de protocolo de streaming em tempo real (RTSP) (nome do `rtspsim` módulo)

O módulo simulador RTSP simula uma transmissão de vídeo ao vivo utilizando um ficheiro de vídeo que foi copiado para o seu dispositivo de borda quando executou o [script de configuração de recursos live video analytics](https://github.com/Azure/live-video-analytics/tree/master/edge/setup). 

Nesta fase, os módulos são implantados, mas nenhum gráfico de mídia está ativo.

## <a name="prepare-to-monitor-events"></a>Preparar para monitorizar eventos

Você usará o live video analytics no módulo IoT Edge para detetar movimento na transmissão de vídeo ao vivo e enviar eventos para ioT Hub. Para ver estes eventos, siga estes passos:

1. Abra o painel Explorer no Código do Estúdio Visual e procure o Azure IoT Hub no canto inferior esquerdo.
1. Expandir o nó **dispositivos.**
1. Clique com o botão direito **do dispositivo de amostra de lva** e selecione Start **Monitoring Built-in Event Endpoint**.

    ![Comece a monitorizar um ponto final de evento incorporado](./media/quickstarts/start-monitoring-iothub-events.png)

## <a name="run-the-sample-program"></a>Executar o programa de amostragem

Siga estes passos para executar o código de amostra:

1. No Código do Estúdio Visual, aceda a *src/cloud-to-device-console-app/operations.jsem*.
1. No nó **GraphTopologySet,** certifique-se de que vê o seguinte valor:

    `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/motion-detection/topology.json"`
1. Nos nódos **GraphInstanceSet** e **GraphTopologyDelete,** certifique-se de que o valor da `topologyName` propriedade corresponde ao valor da propriedade na `name` topologia do gráfico:

    `"topologyName" : "MotionDetection"`
    
1. Inicie uma sessão de depurar selecionando a tecla F5. A janela **TERMINAL** apresentará algumas mensagens.
1. A *operations.jsno* ficheiro começa com chamadas de e `GraphTopologyList` `GraphInstanceList` . Se limpou os recursos depois de ter terminado os quickstarts anteriores, então este processo irá devolver listas vazias e, em seguida, fazer uma pausa. Para continuar, selecione a tecla 'Entrar'.

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

    A janela **TERMINAL** mostra o próximo conjunto de chamadas de métodos diretos:
     
     * Uma chamada para `GraphTopologySet` que usa o anterior`topologyUrl`
     * Uma chamada para `GraphInstanceSet` o seguinte corpo:
     
         ```
         {
           "@apiVersion": "1.0",
           "name": "Sample-Graph",
           "properties": {
             "topologyName": "MotionDetection",
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
     
     * Uma chamada para `GraphInstanceActivate` que inicia a instância do gráfico e o fluxo de vídeo
     * Uma segunda chamada para `GraphInstanceList` que mostra que a instância do gráfico está no estado de execução
1. A saída na janela **TERMINAL** para em `Press Enter to continue` . Não selecione Enter ainda. Percorra para ver as cargas de resposta JSON para os métodos diretos que invocou.
1. Mude para a janela **OUTPUT** no Código do Estúdio Visual. Vê mensagens que o modusão IoT Edge está a enviar para o hub IoT. A secção seguinte deste quickstart discute estas mensagens.
1. O gráfico mediático continua a correr e a imprimir resultados. O simulador RTSP continua a dar a volta ao vídeo de origem. Para parar o gráfico de mídia, volte à janela **TERMINAL** e selecione Enter. 

    A próxima série de chamadas limpa recursos:
     * Uma chamada para `GraphInstanceDeactivate` desativar a instância do gráfico.
     * Uma chamada para `GraphInstanceDelete` apagar o caso.
     * Uma chamada para `GraphTopologyDelete` apagar a topologia.
     * Uma chamada final para `GraphTopologyList` mostrar que a lista está vazia.

## <a name="interpret-results"></a>Interpretar os resultados

Quando executar o gráfico de mídia, os resultados do nó do processador de processador de movimento passam pelo nó da pia IoT Hub para o hub IoT. As mensagens que vê na janela **OUTPUT** do Código do Estúdio Visual contêm uma `body` secção e uma `applicationProperties` secção. Para obter mais informações, consulte [Criar e ler mensagens IoT Hub](../../iot-hub/iot-hub-devguide-messages-construct.md).

Nas seguintes mensagens, o módulo Live Video Analytics define as propriedades da aplicação e o conteúdo do corpo.

### <a name="mediasessionestablished-event"></a>Evento mediaSessionEstablished

Quando um gráfico de mídia é instantâneo, o nó de origem RTSP tenta ligar-se ao servidor RTSP que funciona no recipiente rtspsim-live555. Se a ligação for bem sucedida, o seguinte evento é impresso.

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:  
{  
"body": {
"sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 xxx.xxx.xxx.xxx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets={SPS}\r\na=control:track1\r\n"  
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

Na saída anterior: 
* A mensagem é um evento de diagnóstico, `MediaSessionEstablished` . Indica que o nó de origem RTSP (o sujeito) ligado ao simulador RTSP e começou a receber um feed ao vivo (simulado).
* Em `applicationProperties` , refere o nó na `subject` topologia do gráfico a partir do qual a mensagem foi gerada. Neste caso, a mensagem provém do nó de origem RTSP.
* Em `applicationProperties` , indica que este evento é um evento de `eventType` diagnóstico.
* O `eventTime` valor indica a hora em que o evento ocorreu.
* A `body` secção contém dados sobre o evento de diagnóstico. Neste caso, os dados compreendem os detalhes do [Protocolo de Descrição da Sessão (SDP).](https://en.wikipedia.org/wiki/Session_Description_Protocol)


### <a name="motiondetection-event"></a>Evento MotionDetection

Quando o movimento é detetado, o módulo Live Video Analytics no IoT Edge envia um evento de inferência. O `type` conjunto indica que é um resultado do processador de `motion` deteção de movimentos. O `eventTime` valor diz-lhe quando (na UTC) ocorreu o movimento. 

Aqui está um exemplo desta mensagem:

```
  {  
  "body": {  
    "timestamp": 142843967343090,
    "inferences": [  
      {  
        "type": "motion",  
        "motion": {  
          "box": {  
            "l": 0.573222,  
            "t": 0.492537,  
            "w": 0.141667,  
            "h": 0.074074  
          }  
        }  
      }  
    ]  
  },  
  "applicationProperties": {  
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",  
    "subject": "/graphInstances/GRAPHINSTANCENAME/processors/md",  
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",  
    "eventTime": "2020-04-17T20:26:32.7010000Z",
    "dataVersion": "1.0"  
  }  
}  
```

Neste exemplo: 

* Em `applicationProperties` , refere o nó no gráfico mediático a partir do qual a `subject` mensagem foi gerada. Neste caso, a mensagem provém do nó do processador de deteção de movimento.
* Em `applicationProperties` , indica que este evento é um evento de `eventType` análise.
* O `eventTime` valor é o momento em que o evento ocorreu.
* O `body` valor são dados sobre o evento de análise. Neste caso, o evento é um evento de inferência, pelo que o corpo contém `timestamp` e `inferences` dados.
* Os `inferences` dados indicam que `type` o é `motion` . Tem dados adicionais sobre o `motion` evento.
* A `box` secção contém as coordenadas de uma caixa de delimitação em torno do objeto em movimento. Os valores são normalizados pela largura e altura do vídeo, em pixels. Por exemplo, a largura é de 1920 e a altura é de 1080.

    ```
    l - distance from left of image
    t - distance from top of image
    w - width of bounding box
    h - height of bounding box
    ```
    
## <a name="clean-up-resources"></a>Limpar recursos

Se pretende experimentar os outros quickstarts, então deve manter os recursos que criou. Caso contrário, no portal Azure, vá aos seus grupos de recursos, selecione o grupo de recursos onde executou este arranque rápido e, em seguida, elimine todos os recursos.

## <a name="next-steps"></a>Passos seguintes

Executar os outros quickstarts, tais como detetar um objeto em um feed de vídeo ao vivo.        
