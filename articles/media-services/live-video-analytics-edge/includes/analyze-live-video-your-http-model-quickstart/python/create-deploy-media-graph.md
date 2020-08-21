---
ms.openlocfilehash: 6be3da1e031a1ea5b32f58343639d6e7fbf2425e
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684331"
---
### <a name="examine-and-edit-the-sample-files"></a>Examine e edite os ficheiros de amostra

Como parte dos pré-requisitos, descarregou o código de amostra para uma pasta. Siga estes passos para examinar e editar os ficheiros de amostra.

1. No Código do Estúdio Visual, vá ao *src/edge*. Você vê o seu ficheiro *.env* e alguns ficheiros de modelo de implementação.

    O modelo de implantação refere-se ao manifesto de implantação do dispositivo de borda. Inclui alguns valores de espaço reservado. O ficheiro *.env* inclui os valores para essas variáveis.

1. Aceda à pasta *src/cloud-to-device-app.* Aqui vê a sua *appsettings.jsno* ficheiro e alguns outros ficheiros:

    * ***c2d-console-app.csproj*** - O ficheiro do projeto para Visual Studio Code.
    * ***operations.jsem*** - Uma lista das operações que quer que o programa seja executado.
    * ***Program.cs*** - O código do programa de amostra. Este código:

        * Carrega as definições da aplicação.
        * Invoca métodos diretos que o live video analytics no módulo IoT Edge expõe. Pode utilizar o módulo para analisar streams de vídeo ao vivo invocando os seus [métodos diretos.](../../../direct-methods.md)
        * Pausas para que possa examinar a saída do programa na janela **TERMINAL** e examinar os eventos que foram gerados pelo módulo na janela **OUTPUT.**
        * Invoca métodos diretos para limpar recursos.


1. Editar o *operations.jsno* ficheiro:
    * Altere a ligação para a topologia do gráfico:

        `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtension/topology.json"`

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

1. Clique à direita *src/edge/config/ deployment.yolov3.amd64.js* e selecione **Criar Implementação para dispositivo único**. 

    ![Criar implementação para dispositivo único](../../../media/quickstarts/create-deployment-single-device.png)

1. Quando lhe for solicitado que selecione um dispositivo IoT Hub, selecione **o dispositivo de amostra de Lva**.
1. Após cerca de 30 segundos, no canto inferior esquerdo da janela, refresque o Azure IoT Hub. O dispositivo de borda mostra agora os seguintes módulos implantados:

    * O módulo Live Video Analytics, chamado **LvaEdge**
    * O **módulo rtspsim,** que simula um servidor RTSP e funciona como a fonte de um feed de vídeo ao vivo
    * O módulo **yolov3,** que é o modelo de deteção de objetos YOLOv3 que aplica visão computacional às imagens e devolve várias classes de tipos de objetos
 
      ![Módulos que são implantados no dispositivo de borda](../../../media/quickstarts/yolov3.png)

### <a name="prepare-to-monitor-events"></a>Preparar para monitorizar eventos

Clique com o botão direito no dispositivo Live Video Analytics e selecione **Start Monitoring Built-in Event Endpoint**. Precisa deste passo para monitorizar os eventos do IoT Hub na janela **OUTPUT** do Código do Estúdio Visual. 

![Iniciar a monitorização](../../../media/quickstarts/start-monitoring-iothub-events.png) 

### <a name="run-the-sample-program"></a>Executar o programa de amostragem

1. Para iniciar uma sessão de depuragem, selecione a tecla F5. Vê mensagens impressas na janela **TERMINAL.**
1. A *operations.jsno* código começa com chamadas para os métodos diretos e `GraphTopologyList` `GraphInstanceList` . Se limpou os recursos depois de ter concluído os quickstarts anteriores, então este processo irá devolver listas vazias e, em seguida, fazer uma pausa. Para continuar, selecione a tecla 'Entrar'.

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

     * Uma chamada para `GraphTopologySet` que usa o anterior `topologyUrl`
     * Uma chamada para `GraphInstanceSet` o seguinte corpo:

         ```
         {
           "@apiVersion": "1.0",
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
