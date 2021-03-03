---
ms.openlocfilehash: 8a9149119bc754ff0715f2841925da01301faecd
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101749910"
---
### <a name="examine-and-edit-the-sample-files"></a>Examine e edite os ficheiros de amostra

Como parte dos pré-requisitos, descarregou o código de amostra para uma pasta. Siga estes passos para examinar e editar os ficheiros de amostra.

1. No Código do Estúdio Visual, vá ao *src/edge*. Você vê o seu ficheiro *.env* e alguns ficheiros de modelo de implementação.

    O deployment.grpcyolov3icpu.template.jsrefere-se ao manifesto de implantação do dispositivo de borda. Inclui alguns valores de espaço reservado. O ficheiro .env inclui os valores para essas variáveis.
1. Aceda à pasta *src/cloud-to-device-app.* Aqui vê a sua *appsettings.jsno* ficheiro e alguns outros ficheiros:
    
    * c2d-console-app.csproj - O ficheiro do projeto para Visual Studio Code.
    * operations.jsem - Uma lista das operações que quer que o programa seja executado.
    * Program.cs - O código do programa de amostra. Este código:

        * Carrega as definições da aplicação.
        * Invoca métodos diretos que o live video analytics no módulo IoT Edge expõe. Pode utilizar o módulo para analisar streams de vídeo ao vivo invocando os seus métodos diretos.
        * Pausas para que possa examinar a saída do programa na janela **TERMINAL** e examinar os eventos que foram gerados pelo módulo na janela **OUTPUT.**
        * Invoca métodos diretos para limpar recursos.
1. Editar o *operations.jsno* ficheiro:
 
    * Altere a ligação para a topologia do gráfico:
    * `"topologyUrl"` : `"https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/motion-with-grpcExtension/2.0/topology.json"`
    * Em GraphInstanceSet, edite o nome da topologia do gráfico para corresponder ao valor no link anterior:
    * `"topologyName"` : `"EVROnMotionPlusGrpcExtension"`
    * Sob a GraphTopologyDelete, edite o nome:
    * `"name"` : `"EVROnMotionPlusGrpcExtension"`

> [!NOTE]
> <p>
> <details>
> <summary>Expanda isto e confira como o nó de conservação MediaGraphGrpcExtension é implementado na topologia</summary>
> <pre><code>
> {
>   "@type": "#Microsoft.Media.MediaGraphGrpcExtension",
>   "name": "grpcExtension",
>   "endpoint": {
>       "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
>       "url": "${grpcExtensionAddress}",
>       "credentials": {
>           "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
>           "username": "${grpcExtensionUserName}",
>           "password": "${grpcExtensionPassword}"
>       }
>   },
>   "dataTransfer": {
>       "mode": "sharedMemory",
>       "SharedMemorySizeMiB": "256"
>   },
>   "image": {
>       "scale": {
>           "mode": "${imageScaleMode}",
>           "width": "${frameWidth}",
>           "height": "${frameHeight}"
>       },
>       "format": {
>           "@type": "#Microsoft.Media.MediaGraphImageFormatEncoded",
>           "encoding": "${imageEncoding}",
>           "quality": "${imageQuality}"
>       }
>   },
>   "inputs": [
>       {
>           "nodeName": "motionDetection"
>       }
>   ]
> }          
> </code></pre>
> </details>    
> </p>

### <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Gerar e implementar o manifesto de implantação IoT Edge

1. Clique com o botão direito para o *src/edge/* *deployment.grpcyolov3icpu.template.jsno* ficheiro e, em seguida, selecione Generate **IoT Edge Deployment Manifest**.

    ![Gerar manifesto de implantação de borda ioT](../../../media/quickstarts/generate-iot-edge-deployment-manifest-grpc.png)

    A *deployment.grpcyolov3icpu.amd64.jsno* ficheiro manifesto é criada na pasta *src/edge/config.*
1. Se tiver concluído o [movimento Detect e emitir eventos](../../../detect-motion-emit-events-quickstart.md) de arranque rápido, então ignore este passo.

    Caso contrário, perto do painel **AZURE IOT HUB** no canto inferior esquerdo, selecione o ícone **Mais ações** e, em seguida, selecione **set IoT Hub Connection String**. Pode copiar o fio do *appsettings.jsficheiro.* Ou, para garantir que configura o hub IoT adequado dentro do Código do Estúdio Visual, use o [comando do hub Select IoT](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub).

    ![Cadeia de conexão IoT Hub](../../../media/quickstarts/iot-hub-connection-string-grpc.png)

    > [!NOTE]
    > Pode ser-lhe pedido que forneça informações sobre o ponto final incorporado para o IoT Hub. Para obter essa informação, no portal Azure, navegue até ao seu Hub IoT e procure a opção **de pontos finais incorporados** no painel de navegação esquerdo. Clique lá e procure o **ponto final compatível com o Event Hub** na secção de ponto final **compatível com o Event Hub.** Copie e use o texto na caixa. O ponto final será mais ou menos assim:  
        ```
        Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
        ```

1. Clique à direita *src/edge/config/* *deployment.grpcyolov3icpu.amd64.js* e selecione **Criar Implementação para dispositivo único**.

    ![criar dispositivo único de implantação](../../../media/quickstarts/create-deployment-single-device-grpc.png)
1. Quando lhe for solicitado que selecione um dispositivo IoT Hub, selecione **o dispositivo de amostra de Lva**.
1. Após cerca de 30 segundos, no canto inferior esquerdo da janela, refresque o Azure IoT Hub. O dispositivo de borda mostra agora os seguintes módulos implantados:

    * O módulo Live Video Analytics, chamado **LvaEdge.**
    * O **módulo rtspsim,** que simula um servidor RTSP e funciona como a fonte de um feed de vídeo ao vivo.

        > [!NOTE]
        > Os passos acima estão assumindo que está a usar a máquina virtual criada pelo script de configuração. Se estiver a utilizar o seu próprio dispositivo de borda, vá ao seu dispositivo de borda e execute os seguintes comandos com **direitos de administração**, para puxar e armazenar o ficheiro de vídeo de amostra utilizado para este arranque rápido:  

        ```
        mkdir /home/lvaedgeuser/samples
        mkdir /home/lvaedgeuser/samples/input    
        curl https://lvamedia.blob.core.windows.net/public/camera-300s.mkv > /home/lvaedgeuser/samples/input/camera-300s.mkv  
        chown -R lvalvaedgeuser:localusergroup /home/lvaedgeuser/samples/  
        ```
    * O módulo **lvaExtension,** que é o modelo de deteção de objetos YOLOv3 que usa o gRPC como método de comunicação e aplica visão computacional às imagens e devolve várias classes de tipos de objetos.
    
    ![Extensão da Lva](../../../media/quickstarts/lvaextension-grpc.png)

### <a name="prepare-to-monitor-events"></a>Preparar para monitorizar eventos

1. No Código do Estúdio Visual, abra o **separador Extensões** (ou prima Ctrl+Shift+X) e procure por Azure IoT Hub.
1. Clique no direito e selecione **Definições de extensão**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/run-program/extensions-tab.png" alt-text="Definições de extensão":::
1. Procure e ative "Mostrar Mensagem Verbose".

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/run-program/show-verbose-message.png" alt-text="Mostrar mensagem verbose":::
1. Clique com o botão direito no dispositivo Live Video Analytics e selecione **Start Monitoring Built-in Event Endpoint**. Precisa deste passo para monitorizar os eventos do IoT Hub na janela **OUTPUT** do Código do Estúdio Visual.

   ![Iniciar a monitorização](../../../media/quickstarts/start-monitoring-built-event-endpoint-grpc.png)

    > [!NOTE]
    > Pode ser-lhe pedido que forneça informações sobre o ponto final incorporado para o IoT Hub. Para obter essa informação, no portal Azure, navegue até ao seu Hub IoT e procure a opção **de pontos finais incorporados** no painel de navegação esquerdo. Clique lá e procure o **ponto final compatível com o Event Hub** na secção de ponto final **compatível com o Event Hub.** Copie e use o texto na caixa. O ponto final será mais ou menos assim:  
        ```
        Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
        ```
### <a name="run-the-sample-program"></a>Executar o programa de amostragem

1. Para iniciar uma sessão de depuragem, selecione a tecla F5. Vê mensagens impressas na janela TERMINAL.
1. A *operations.jsno* código começa com chamadas para os métodos diretos e `GraphTopologyList` `GraphInstanceList` . Se limpou os recursos depois de ter concluído os quickstarts anteriores, então este processo irá devolver listas vazias e, em seguida, fazer uma pausa. Para continuar, selecione a tecla 'Entrar'.
    
    ```
    -------------------------------Executing operation GraphTopologyList-----------------------  
    Request: GraphTopologyList  --------------------------------------------------
    {
    "@apiVersion": "2.0"
    }
    ---------------  
    Response: GraphTopologyList - Status: 200  ---------------
    {
    "value": []
    }
    --------------------------------------------------------------------------
    Executing operation WaitForInput
    Press Enter to continue
    ```
    
    A janela **TERMINAL** mostra o próximo conjunto de chamadas de métodos diretos:
    
    * Uma chamada para `GraphTopologySet` que usa a topologia anteriorUrl
    * Uma chamada para `GraphInstanceSet` o seguinte corpo:
    
    ```
    {
      "@apiVersion": "2.0",
      "name": "Sample-Graph-1",
      "properties": {
        "topologyName": "EVROnMotionPlusGrpcExtension",
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
    
    * Uma chamada para `GraphInstanceActivate` isso começa a instância do gráfico e o fluxo de vídeo.
    * Uma segunda chamada para `GraphInstanceList` isso mostra que a instância do gráfico está no estado de execução.
1. A saída na janela **TERMINAL** para numa Entrada de Imprensa para continuar a ser solicitada. Não selecione Enter ainda. Percorra para ver as cargas de resposta JSON para os métodos diretos que invocou.
1. Mude para a janela **OUTPUT** no Código do Estúdio Visual. Vê mensagens que o modusão IoT Edge está a enviar para o hub IoT. A secção seguinte deste quickstart discute estas mensagens.
1. O gráfico mediático continua a correr e a imprimir resultados. O simulador RTSP continua a dar a volta ao vídeo de origem. Para parar o gráfico de mídia, volte à janela **TERMINAL** e selecione Enter.
1. A próxima série de chamadas limpa recursos:
    
    * Uma chamada para `GraphInstanceDeactivate` desativar a instância do gráfico.
    * Uma chamada para `GraphInstanceDelete` apagar o caso.
    * Uma chamada para `GraphTopologyDelete` apagar a topologia.
    * Uma chamada final para `GraphTopologyList` mostrar que a lista está vazia.

