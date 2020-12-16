---
ms.openlocfilehash: 766dd13f58268c044435a22fb30c1de816d4d151
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/15/2020
ms.locfileid: "97531893"
---
Siga estes passos para executar o código de amostra:

1. No Código do Estúdio Visual, abra o **separador Extensões** (ou prima Ctrl+Shift+X) e procure por Azure IoT Hub.
1. Clique no direito e selecione **Definições de extensão**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/run-program/extensions-tab.png" alt-text="Definições de extensão":::
1. Procure e ative "Mostrar Mensagem Verbose".

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/run-program/show-verbose-message.png" alt-text="Mostrar mensagem verbose":::
1. No Código do Estúdio Visual, aceda a *src/cloud-to-device-console-app/operations.jsem*.
1. No nó **GraphTopologySet,** certifique-se de que vê o seguinte valor:

    `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/motion-detection/2.0/topology.json"`
1. Nos nódos **GraphInstanceSet** e **GraphTopologyDelete,**  certifique-se de que o valor da `topologyName` propriedade corresponde ao valor da propriedade na `name` topologia do gráfico:

    `"topologyName" : "MotionDetection"`
    
1. Inicie uma sessão de depurar selecionando a tecla F5. A janela **TERMINAL** apresentará algumas mensagens.
1. A *operations.jsno* ficheiro começa com chamadas de e `GraphTopologyList` `GraphInstanceList` . Se limpou os recursos depois de ter terminado os quickstarts anteriores, então este processo irá devolver listas vazias e, em seguida, fazer uma pausa. Para continuar, selecione a tecla 'Entrar'.

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
     
    * Uma chamada para `GraphInstanceActivate` isso começa a instância do gráfico e o fluxo de vídeo.
    * Uma segunda chamada para `GraphInstanceList` isso mostra que a instância do gráfico está no estado de execução.
1. A saída na janela **TERMINAL** para em `Press Enter to continue` . Não selecione Enter ainda. Percorra para ver as cargas de resposta JSON para os métodos diretos que invocou.
1. Mude para a janela **OUTPUT** no Código do Estúdio Visual. Vê mensagens que o modusão IoT Edge está a enviar para o hub IoT. A secção seguinte deste quickstart discute estas mensagens.
1. O gráfico mediático continua a correr e a imprimir resultados. O simulador RTSP continua a dar a volta ao vídeo de origem. Para parar o gráfico de mídia, volte à janela **TERMINAL** e selecione Enter. 

    A próxima série de chamadas limpa recursos:

    * Uma chamada para `GraphInstanceDeactivate` desativar a instância do gráfico.
    * Uma chamada para `GraphInstanceDelete` apagar o caso.
    * Uma chamada para `GraphTopologyDelete` apagar a topologia.
    * Uma chamada final para `GraphTopologyList` mostrar que a lista está vazia.
