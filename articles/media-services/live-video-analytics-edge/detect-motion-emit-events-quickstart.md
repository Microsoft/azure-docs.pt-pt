---
title: Detetar movimento e emitir eventos - Azure
description: Este quickstart mostra-lhe como usar o Live Video Analytics no IoT Edge para detetar movimentos e emitir eventos, chamando programáticamente métodos diretos.
ms.topic: quickstart
ms.date: 05/29/2020
ms.openlocfilehash: 4986ea13bec5382a8e0ef791e75442e4333e4356
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/01/2020
ms.locfileid: "84262027"
---
# <a name="quickstart-detect-motion-and-emit-events"></a>Quickstart: Detetar movimento e emitir eventos

Este quickstart acompanha-o através dos passos para começar com live video analytics em IoT Edge. Utiliza um Azure VM como um dispositivo IoT Edge e um vídeo simulado ao vivo. Após completar os passos de configuração, poderá executar um vídeo simulado ao vivo através de um gráfico de mídia que detete e reporte qualquer movimento nesse fluxo. O diagrama abaixo mostra uma representação gráfica desse gráfico mediático.

![Análise de vídeo ao vivo baseada na deteção de movimentos](./media/analyze-live-video/motion-detection.png) 

Este artigo baseia-se no [código de amostra](https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp) escrito em C#.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Código de Estúdio Visual](https://code.visualstudio.com/) na sua máquina com as seguintes extensões:
    1. [Ferramentas do Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    2. [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1) instalado no seu sistema

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

    Se o script estiver concluído com sucesso, deverá ver todos os recursos acima mencionados na sua subscrição.

1. Assim que o script terminar, clique nos suportes encaracolados para expor a estrutura da pasta. Você verá alguns ficheiros criados sob o diretório de amostras ~/clouddrive/lva. De interesse neste arranque rápido são:

     * ~/clouddrive/lva-sample/edge-deployment/.env - contém propriedades que o Código do Estúdio Visual utiliza para implantar módulos num dispositivo de borda
     * ~/clouddrive/lva-sample/appsetting.json - usado pelo Código do Estúdio Visual para executar o código de amostra
     
Necessitará destes para atualizar os ficheiros no Código do Estúdio Visual mais tarde no arranque rápido. Talvez queira copiá-los num ficheiro local por enquanto.


 ![Definições da aplicação](./media/quickstarts/clouddrive.png)

## <a name="set-up-your-development-environment"></a>Configurar o ambiente de desenvolvimento

1. Clone o repo https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp daqui.
1. Lance o Código do Estúdio Visual e abra a pasta onde o repo foi descarregado.
1. No Visual Studio Code, navegue para a pasta "src/cloud-to-device-console-app" e crie um ficheiro chamado "appsettings.json". Este ficheiro conterá as definições necessárias para executar o programa.
1. Copie o conteúdo do ficheiro ~/clouddrive/lva-sample/appsettings.json gerado na secção anterior (ver passo 5)

    O texto deve parecer:

    ```
    {  
        "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",  
        "deviceId" : "lva-sample-device",  
        "moduleId" : "lvaEdge"  
    }
    ```
1. Em seguida, navegue na pasta "src/edge" e crie um ficheiro chamado ".env".
1. Copie o conteúdo a partir de "/clouddrive/lva-sample/edge-deployment/.env file. O texto deve parecer:

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

1. No Código do Estúdio Visual, navegue por "src/edge". Verá o ficheiro .env que criou juntamente com alguns ficheiros de modelos de implementação.

    O modelo de implantação refere-se ao manifesto de implantação do dispositivo de borda com alguns valores de espaço reservado. O ficheiro .env tem os valores para essas variáveis.
1. Em seguida, navegue para a pasta "src/cloud-to-device-console-app". Aqui verá o ficheiro appsettings.json que criou juntamente com alguns outros ficheiros:

    * c2d-console-app.csproj - o ficheiro do projeto para Visual Studio Code.
    * operations.json - este ficheiro lista as diferentes operações que gostaria que o programa fosse executado.
    * Program.cs - o código do programa de amostra, que faz o seguinte:
    
        * Carrega as definições da aplicação
        * Invoca métodos diretos expostos pelo live video analytics no módulo IoT Edge. Pode utilizar o módulo para analisar streams de vídeo ao vivo invocando os seus [métodos diretos](direct-methods.md) 
        * Pausas para examinar a saída do programa na janela TERMINAL e os eventos gerados pelo módulo na janela OUTPUT
        * Invoca métodos diretos para limpar recursos   

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Gerar e implementar o manifesto de implantação IoT Edge

O manifesto de implantação define quais os módulos que são implantados num dispositivo de borda e configurações de configuração para esses módulos. Siga estes passos para gerar tal manifesto a partir do ficheiro do modelo e, em seguida, implante-o para o dispositivo de borda.

1. Código de estúdio visual aberto
1. Desagre a cadeia de ligação IoTHub clicando no ícone "Mais ações" ao lado do painel AZURE IOT HUB no canto inferior esquerdo. Pode copiar o string a partir do ficheiro src/cloud-to-device-console-app/appsettings.json. 

    ![Definir cadeia de conexão IOT](./media/quickstarts/set-iotconnection-string.png)
1. Em seguida, clique à direita no ficheiro "src/edge/deployment.template.json" e clique em "Generate IoT Edge Deployment Manifest".
    ![Gerar manifesto de implantação IoT Edge](./media/quickstarts/generate-iot-edge-deployment-manifest.png)

    Isto deve criar um ficheiro manifesto na pasta src/edge/config chamada "deployment.amd64.json".
1. Clique no botão direito em "src/edge/config/deployment.amd64.json" e clique em "Create Deployment for Single Device" e selecione o nome do seu dispositivo de borda.

    ![Criar implementação para dispositivo único](./media/quickstarts/create-deployment-single-device.png)
1. Em seguida, será solicitado que "Selecione um dispositivo IoT Hub". Selecione o dispositivo de amostra de Lva a partir da queda para baixo.
1. Em cerca de 30 segundos, refresque o Azure IOT Hub na secção inferior esquerda e deve ver que o dispositivo de borda tem os seguintes módulos implantados:

    * Vídeo ao vivo analytics no IoT Edge (nome do módulo "lvaEdge")
    * Simulador RTSP (nome do módulo "rtspsim")

O módulo simulador RTSP simula um fluxo de vídeo ao vivo utilizando um ficheiro de vídeo armazenado que foi copiado para o seu dispositivo de borda quando executou o [script de configuração de recursos live video analytics](https://github.com/Azure/live-video-analytics/tree/master/edge/setup). Nesta fase, tem os módulos implantados, mas nenhum gráfico de mídia está ativo.

## <a name="prepare-for-monitoring-events"></a>Preparar para eventos de monitorização

Você estará usando o live video analytics no módulo IoT Edge para detetar movimento na transmissão de vídeo ao vivo e enviar eventos para o IoT Hub. Para ver estes eventos, siga estes passos:

1. Abra o painel Explorer no Código do Estúdio Visual e procure o Azure IoT Hub no canto inferior esquerdo.
1. Expandir o nó dispositivos.
1. Clink direito no dispositivo de amostra de Lva e escolheu a opção "Iniciar a monitorização de eventos incorporados".

    ![Comece a monitorizar o ponto final do evento incorporado](./media/quickstarts/start-monitoring-iothub-events.png)

## <a name="run-the-sample-program"></a>Executar o programa de amostragem

Siga os passos abaixo para executar o código de amostra.
1. No Código do Estúdio Visual, navegue para "src/cloud-to-device-console-app/operations.json".
1. Sob o nó GráficoTopologiaSet, certifique-se do seguinte:

    ` "topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/motion-detection/topology.json"`
1. Em seguida, sob os nosmos GraphInstanceSet e GraphTopologyDelete, certifique-se de que o valor da topologiaName corresponde ao valor da propriedade "nome" na topologia do gráfico acima:

    `"topologyName" : "MotionDetection"`
    
1. Inicie uma sessão de depurar (acerte F5). Começará a ver algumas mensagens impressas na janela TERMINAL.
1. O operations.json começa com chamadas para GraphTopologyList e GraphInstanceList. Se tiver limpo os recursos após os rápidos anteriores, isto retornará as listas vazias e, em seguida, fará uma pausa para que possa entrar.

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
     
     * Uma chamada para o GraphInstanceActivate para iniciar a instância do gráfico e iniciar o fluxo de vídeo.
     * Uma segunda chamada para o GraphInstanceList para mostrar que a instância do gráfico está, de facto, no estado de execução.
1. A saída na janela TERMINAL para agora numa introdução de "Press Enter para continuar". Não bata em "Enter" neste momento. Pode rolar para cima para ver as cargas de resposta JSON para os métodos diretos que invocou
1. Se agora mudar para a janela OUTPUT no Código do Estúdio Visual, verá mensagens que estão a ser enviadas para o IoT Hub, pelo módulo Live Video Analytics no módulo IoT Edge.
     * Estas mensagens são discutidas na secção abaixo
1. O gráfico de mídia continuará a ser executado e os resultados de impressão – o simulador RTSP continuará a fazer loop no vídeo de origem. Para parar o gráfico mediático, volte para a janela TERMINAL e bata em "Enter". A próxima série de chamadas são feitas para limpar recursos:
     * Uma chamada para GraphInstanceDeactivar para desativar a instância do gráfico
     * Uma chamada para o GraphInstanceDelete para apagar o caso
     * Uma chamada para GraphTopologyDelete para apagar a topologia
     * Uma chamada final para a GraphTopologyList para mostrar que a lista está agora vazia

## <a name="interpret-results"></a>Interpretar os resultados

Quando executam o gráfico de mídia, os resultados do nó do processador de movimento são enviados através do nó de pia IoT Hub para o Hub IoT. As mensagens que vê na janela OUTPUT do Código do Estúdio Visual contêm uma secção "body" e uma secção "aplicaçõesProperties". Para entender o que estas secções representam, leia [este](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct) artigo.

Nas mensagens abaixo, as propriedades da aplicação e o conteúdo do corpo são definidos pelo módulo Live Video Analytics.

## <a name="mediasession-established-event"></a>Evento MediaSession Established

Quando um gráfico de mídia é instantâneo, o nó de origem RTSP tenta ligar-se ao servidor RTSP em execução no recipiente rtspsim-live555. Se for bem sucedido, imprimirá este evento:

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

* A mensagem é um evento de Diagnóstico, MediaSessionEstablished, indica que o nó de origem RTSP (o sujeito) foi capaz de estabelecer a ligação com o simulador RTSP, e começar a receber um feed ao vivo (simulado).
* O "sujeito" na aplicaçãoDeproperias refere o nó na topologia do gráfico a partir do qual a mensagem foi gerada. Neste caso, a mensagem é originária do nó de origem RTSP.
* "eventType" na aplicaçãoProperties indica que este é um evento de Diagnóstico.
* "eventTime" indica a hora em que o evento ocorreu.
* "body" contém dados sobre o evento de diagnóstico, que, neste caso, é o [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) detalhes.


## <a name="motion-detection-event"></a>Evento de Deteção de Movimentos

Quando o movimento é detetado, o módulo Live Video Analytics Edge envia um evento de inferência. O tipo está definido para "movimento" para indicar que é um resultado do Processador de Deteção de Movimento, e o eventTime diz-lhe a que altura ocorreu o movimento (UTC). Abaixo está um exemplo:

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

* "sujeito" na aplicaçãoDeproperties refere o nó no gráfico de mídia a partir do qual a mensagem foi gerada. Neste caso, a mensagem é originária do nó do processador de deteção de movimentos.
* "eventType" na aplicaçãoProperties indica que este é um evento Analytics.
* "eventTime" indica a hora em que o evento ocorreu.
"body" contém dados sobre o evento de análise. Neste caso, o evento é um evento de Inferência e, por isso, o corpo contém dados de "timetamp" e "inferências".
* Os dados de "inferências" indicam que o "tipo" é "movimento" e tem dados adicionais sobre esse evento de "movimento".
* A secção "caixa" contém as coordenadas de uma caixa de delimitação em torno do objeto em movimento. Os valores são normalizados pela largura e altura do vídeo em pixels (por exemplo. largura de 1920 e altura de 1080).

    ```
    l - distance from left of image
    t - distance from top of image
    w - width of bounding box
    h - height of bounding box
    ```
    
## <a name="cleanup-resources"></a>Recursos de limpeza

Se pretende experimentar os outros quickstarts, deve manter os recursos criados. Caso contrário, vá ao portal Azure, navegue pelos seus grupos de recursos, selecione o grupo de recursos sob o qual executou este arranque rápido e elimine todos os recursos.

## <a name="next-steps"></a>Passos seguintes

Executar os outros Quickstarts, tais como detetar um objeto em um feed de vídeo ao vivo.        
