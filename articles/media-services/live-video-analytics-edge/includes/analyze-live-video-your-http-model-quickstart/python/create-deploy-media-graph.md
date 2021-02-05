---
ms.openlocfilehash: 7b9ed5dda925793eff4f85408c5eb494f5ac0925
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99569650"
---
### <a name="examine-and-edit-the-sample-files"></a>Examine e edite os ficheiros de amostra

Como parte dos pré-requisitos, descarregou o código de amostra para uma pasta. Siga estes passos para examinar e editar os ficheiros de amostra.

1. No Código do Estúdio Visual, vá ao *src/edge*. Você vê o seu ficheiro *.env* e alguns ficheiros de modelo de implementação.

    O modelo de implantação refere-se ao manifesto de implantação do dispositivo de borda. Inclui alguns valores de espaço reservado. O ficheiro *.env* inclui os valores para essas variáveis.

1. Aceda à pasta *src/cloud-to-device-app.* Aqui vê a sua *appsettings.jsno* ficheiro e alguns outros ficheiros:

    * operations.jsem - Uma lista das operações que quer que o programa seja executado.
    * main.py - O código do programa de amostra. Este código:

        * Carrega as definições da aplicação.
        * Invoca métodos diretos que o live video analytics no módulo IoT Edge expõe. Pode utilizar o módulo para analisar streams de vídeo ao vivo invocando os seus métodos diretos.
        * Pausas para que possa examinar a saída do programa na janela **TERMINAL** e examinar os eventos que foram gerados pelo módulo na janela **OUTPUT.**
        * Invoca métodos diretos para limpar recursos.  
1. Editar o *operations.jsno* ficheiro:
    * Altere a ligação para a topologia do gráfico:

        `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtension/2.0/topology.json"`

    * Em `GraphInstanceSet` , editar o nome da topologia do gráfico para corresponder ao valor no link anterior:

      `"topologyName" : "InferencingWithHttpExtension"`

    * Em `GraphTopologyDelete` , editar o nome:

      `"name": "InferencingWithHttpExtension"`

### <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Gerar e implementar o manifesto de implantação IoT Edge

1. Clique com o botão direito para o *src/edge/ deployment.yolov3.template.jsno* ficheiro e, em seguida, selecione **Generate IoT Edge Deployment Manifest**.

    ![Gerar manifesto de implantação de borda ioT](../../../media/quickstarts/generate-iot-edge-deployment-manifest-yolov3.png)  

    A *deployment.yolov3.amd64.jsno* ficheiro manifesto é criada na pasta *src/edge/config.*

1. Se tiver concluído o [movimento Detect e emitir eventos](../../../detect-motion-emit-events-quickstart.md) de arranque rápido, então ignore este passo. 

    Caso contrário, perto do painel **AZURE IOT HUB** no canto inferior esquerdo, selecione o ícone **Mais ações** e, em seguida, selecione **set IoT Hub Connection String**. Pode copiar o fio do *appsettings.jsficheiro.* Ou, para garantir que configura o hub IoT adequado dentro do Código do Estúdio Visual, use o [comando do hub Select IoT](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub).
    
    ![Definir cadeia de conexão do hub IoT](../../../media/quickstarts/set-iotconnection-string.png)

    > [!NOTE]
    > Pode ser-lhe pedido que forneça informações sobre o ponto final incorporado para o IoT Hub. Para obter essa informação, no portal Azure, navegue até ao seu Hub IoT e procure a opção **de pontos finais incorporados** no painel de navegação esquerdo. Clique lá e procure o **ponto final compatível com o Event Hub** na secção de ponto final **compatível com o Event Hub.** Copie e use o texto na caixa. O ponto final será mais ou menos assim:  
        ```
        Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
        ```

1. Clique à direita *src/edge/config/ deployment.yolov3.amd64.js* e selecione **Criar Implementação para dispositivo único**. 

    ![Criar implementação para dispositivo único](../../../media/quickstarts/create-deployment-single-device.png)

1. Quando lhe for solicitado que selecione um dispositivo IoT Hub, selecione **o dispositivo de amostra de Lva**.
1. Após cerca de 30 segundos, no canto inferior esquerdo da janela, refresque o Azure IoT Hub. O dispositivo de borda mostra agora os seguintes módulos implantados:

    * O módulo Live Video Analytics, chamado **LvaEdge**
    * O **módulo rtspsim,** que simula um servidor RTSP e funciona como a fonte de um feed de vídeo ao vivo
        > [!NOTE]
        > Os passos acima estão assumindo que está a usar a máquina virtual criada pelo script de configuração. Se estiver a utilizar o seu próprio dispositivo de borda, vá ao seu dispositivo de borda e execute os seguintes comandos com **direitos de administração**, para puxar e armazenar o ficheiro de vídeo de amostra utilizado para este arranque rápido:  
        
        ```
        mkdir /home/lvaadmin/samples
        mkdir /home/lvaadmin/samples/input    
        curl https://lvamedia.blob.core.windows.net/public/camera-300s.mkv > /home/lvaadmin/samples/input/camera-300s.mkv  
        chown -R lvaadmin /home/lvaadmin/samples/  
        ```
    * O módulo **yolov3,** que é o modelo de deteção de objetos YOLOv3 que aplica visão computacional às imagens e devolve várias classes de tipos de objetos
 
      ![Módulos que são implantados no dispositivo de borda](../../../media/quickstarts/yolov3.png)

### <a name="prepare-to-monitor-events"></a>Preparar para monitorizar eventos

1. No Código do Estúdio Visual, abra o **separador Extensões** (ou prima Ctrl+Shift+X) e procure por Azure IoT Hub.
1. Clique no direito e selecione **Definições de extensão**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/run-program/extensions-tab.png" alt-text="Definições de extensão":::
1. Procure e ative "Mostrar Mensagem Verbose".

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/run-program/show-verbose-message.png" alt-text="Mostrar mensagem verbose":::
1. Clique com o botão direito no dispositivo Live Video Analytics e selecione **Start Monitoring Built-in Event Endpoint**. Precisa deste passo para monitorizar os eventos do IoT Hub na janela **OUTPUT** do Código do Estúdio Visual. 

   ![Iniciar a monitorização](../../../media/quickstarts/start-monitoring-iothub-events.png) 

> [!NOTE]
> Pode ser-lhe pedido que forneça informações sobre o ponto final incorporado para o IoT Hub. Para obter essa informação, no portal Azure, navegue até ao seu Hub IoT e procure a opção **de pontos finais incorporados** no painel de navegação esquerdo. Clique lá e procure o **ponto final compatível com o Event Hub** na secção de ponto final **compatível com o Event Hub.** Copie e use o texto na caixa. O ponto final será mais ou menos assim:  
    ```
    Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
    ```
### <a name="run-the-sample-program"></a>Executar o programa de amostragem

1. Para iniciar uma sessão de depuragem, selecione a tecla F5. Vê mensagens impressas na janela **TERMINAL.**
1. A *operations.jsno* código começa com chamadas para os métodos diretos e `GraphTopologyList` `GraphInstanceList` . Se limpou os recursos depois de ter concluído os quickstarts anteriores, então este processo irá devolver listas vazias e, em seguida, fazer uma pausa. Para continuar, selecione a tecla 'Entrar'.

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

    A janela **TERMINAL** mostra o próximo conjunto de chamadas de métodos diretos:

     * Uma chamada para `GraphTopologySet` que usa o anterior `topologyUrl`
     * Uma chamada para `GraphInstanceSet` o seguinte corpo:

         ```
         {
           "@apiVersion": "2.0",
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
