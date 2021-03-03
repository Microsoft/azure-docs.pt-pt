---
title: Começar com Live Video Analytics em IoT Edge - Azure
description: Este quickstart mostra como começar com live video analytics em IoT Edge. Aprenda a detetar movimento num vídeo transmitido ao vivo.
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: 57edf1721249f839f5c781756b3e09bf59888dab
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101730291"
---
# <a name="quickstart-get-started---live-video-analytics-on-iot-edge"></a>Quickstart: Get start - Live Video Analytics on IoT Edge

Este quickstart acompanha-o através dos passos para começar com live video analytics em IoT Edge. Utiliza um Azure VM como dispositivo IoT Edge. Também usa um vídeo simulado ao vivo. 

Depois de completar os passos de configuração, poderá executar um vídeo simulado ao vivo através de um gráfico de mídia que detete e reporte qualquer movimento nesse fluxo. O diagrama seguinte representa graficamente o gráfico mediático.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/analyze-live-video/motion-detection.svg" alt-text="Análise de vídeo ao vivo baseada na deteção de movimentos":::

Pode ver o seguinte vídeo com passos detalhados sobre como começar com o Live Video Analytics no IoT Edge:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4Hcax]

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure que tem uma subscrição ativa. [Crie uma conta gratuita se](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) ainda não tiver uma.

  > [!NOTE]
  > Você precisará de uma subscrição Azure com permissões para criar principais serviços **(a função do proprietário** fornece isso). Se não tiver as permissões certas, contacte o administrador da sua conta para lhe conceder as permissões certas.  

* [Código de estúdio visual](https://code.visualstudio.com/) na sua máquina de desenvolvimento. Certifique-se de que tem a [extensão Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* Certifique-se de que a rede de que a sua máquina de desenvolvimento está ligada permite o Protocolo avançado de Fila de Mensagens (AMQP) sobre a porta 5671 para o tráfego de saída. Esta configuração permite que as ferramentas Azure IoT se comuniquem com o Azure IoT Hub.

> [!TIP]
> Pode ser solicitado que instale o Docker enquanto instala a extensão Azure IoT Tools. Sinta-se livre para ignorar o pedido.

## <a name="set-up-azure-resources"></a>Configurar recursos do Azure

Este tutorial requer os seguintes recursos Azure:

* IoT Hub
* Conta de armazenamento
* Conta Azure Media Services
* Um Linux VM em Azure, com [tempo de execução IoT Edge](../../iot-edge/how-to-install-iot-edge.md) instalado

Para este arranque rápido, recomendamos que utilize o [script de configuração de recursos live video analytics](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) para implementar os recursos necessários na sua subscrição Azure. Para tal, siga estes passos:

1. Vá ao [portal Azure](https://portal.azure.com) e selecione o ícone Cloud Shell.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstarts/cloud-shell.png" alt-text="Cloud Shell":::
1. Se estiver a utilizar a Cloud Shell pela primeira vez, será solicitado que selecione uma subscrição para criar uma conta de armazenamento e uma partilha de Ficheiros Microsoft Azure. Selecione **Criar armazenamento** para criar uma conta de armazenamento para as informações da sessão Cloud Shell. Esta conta de armazenamento é separada da conta que o script criará para utilizar com a sua conta Azure Media Services.
1. No menu suspenso no lado esquerdo da janela Cloud Shell, selecione **Bash** como seu ambiente.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstarts/env-selector.png" alt-text="Selecionador de ambiente":::
1. Execute o seguinte comando.

    ```
    bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
    ```
    
    Após a conclusão com sucesso do script, deverá ver todos os recursos necessários na sua subscrição. Um total de 12 recursos serão configurados pelo script:
    1. **Streaming Endpoint** - Isto ajudará na reprodução do ativo AMS gravado.
    1. **Máquina virtual** - Esta é uma máquina virtual que funcionará como o seu dispositivo de borda.
    1. **Disco** - Este é um disco de armazenamento que está ligado à máquina virtual para armazenar meios e artefactos.
    1. **Grupo de segurança de rede** - Isto é usado para filtrar o tráfego de rede de e para os recursos Azure numa rede virtual Azure.
    1. **Interface de rede** - Isto permite que uma Máquina Virtual Azure comunique com internet, Azure e outros recursos.
    1. **Ligação de bastião** - Isto permite-lhe ligar-se à sua máquina virtual utilizando o seu navegador e o portal Azure.
    1. **Endereço IP público** - Isto permite que os recursos do Azure comuniquem à Internet e aos serviços Azure virados para o público
    1. **Rede virtual** - Isto permite que muitos tipos de recursos Azure, como a sua máquina virtual, comuniquem de forma segura entre si, a internet e as redes no local. Saiba mais sobre [redes Virtuais.](../../virtual-network/virtual-networks-overview.md)
    1. **IoT Hub** - Este funciona como um centro de mensagens central para comunicação bidis entre a sua aplicação IoT, os módulos IoT Edge e os dispositivos que gere.
    1. **Conta de serviço de** mídia - Isto ajuda na gestão e streaming de conteúdos de mídia em Azure.
    1. **Conta de armazenamento** - Tem de ter uma conta de armazenamento primário e pode ter qualquer número de contas de armazenamento secundária associadas à sua conta de Serviços de Comunicação Social. Para mais informações, consulte [as contas do Azure Storage com as contas da Azure Media Services](../latest/storage-account-concept.md).
    1. **Registo de contentores** - Isto ajuda a armazenar e gerir as imagens privadas do seu contentor Docker e artefactos relacionados.

Na saída do script, uma tabela de recursos lista o nome do hub IoT. Procure o tipo de recurso **`Microsoft.Devices/IotHubs`** e anotar o nome. Vai precisar deste nome no próximo passo.  

> [!NOTE]
> O script também gera alguns ficheiros de configuração no ***diretório ~/clouddrive/lva-sample/.*** Vai precisar destes ficheiros mais tarde no início rápido.

> [!TIP]
> Se encontrar problemas com os recursos da Azure que são criados, por favor, consulte o nosso **[guia de resolução de problemas](troubleshoot-how-to.md#common-error-resolutions)** para resolver alguns problemas comumente encontrados.

## <a name="deploy-modules-on-your-edge-device"></a>Implementar módulos no seu dispositivo de borda

Executar o seguinte comando de Cloud Shell.

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content ~/clouddrive/lva-sample/edge-deployment/deployment.amd64.json
```

Este comando implanta os seguintes módulos para o dispositivo de borda, que é o Linux VM neste caso.

* Vídeo ao vivo Analytics no IoT Edge (nome do `lvaEdge` módulo)
* Real-Time simulador de protocolo de streaming (RTSP) (nome do `rtspsim` módulo)

O módulo simulador RTSP simula uma transmissão de vídeo ao vivo utilizando um ficheiro de vídeo que foi copiado para o seu dispositivo de borda quando executou o [script de configuração de recursos live video analytics](https://github.com/Azure/live-video-analytics/tree/master/edge/setup). 

Agora os módulos estão implantados, mas nenhum gráfico de mídia está ativo.

## <a name="configure-the-azure-iot-tools-extension"></a>Configure a extensão Azure IoT Tools

Siga estas instruções para ligar ao seu hub IoT utilizando a extensão Azure IoT Tools.

1. No Código do Estúdio Visual, abra o **separador Extensões** (ou prima Ctrl+Shift+X) e procure por Azure IoT Hub.
1. Clique com o botão direito e selecione **Definições de extensão**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Definições de extensão":::
1. Procure e ative "Mostrar Mensagem Verbose".

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Mostrar mensagem verbose":::
1. Selecione **Ver**  >  **Explorador**. Ou, selecione Ctrl+Shift+E.
1. No canto inferior esquerdo do **separador Explorer,** selecione **Azure IoT Hub**.
1. Selecione o ícone **Mais Opções** para ver o menu de contexto. Em seguida, selecione **set IoT Hub Connection String**.
1. Quando aparecer uma caixa de entrada, introduza a sua cadeia de ligação IoT Hub. Na Cloud Shell, pode obter a cadeia de ligação a partir de *~/clouddrive/lva-sample/appsettings.js.*

> [!NOTE]
> Pode ser-lhe pedido que forneça informações sobre o ponto final incorporado para o IoT Hub. Para obter essa informação, no portal Azure, navegue até ao seu Hub IoT e procure a opção **de pontos finais incorporados** no painel de navegação esquerdo. Clique lá e procure o **ponto final compatível com o Event Hub** na secção de ponto final **compatível com o Event Hub.** Copie e use o texto na caixa. O ponto final será mais ou menos assim:  
    ```
    Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
    ```

Se a ligação for bem sucedida, aparece a lista de dispositivos de borda. Deve ver pelo menos um dispositivo chamado **lva-sample-device**. Agora pode gerir os seus dispositivos IoT Edge e interagir com o Azure IoT Hub através do menu de contexto. Para visualizar os módulos implantados no dispositivo de borda, sob **o dispositivo de amostra de Lva,** expanda o nó de **Módulos.**

![Nó de dispositivo de amostra de lva](./media/quickstarts/lva-sample-device-node.png)

> [!TIP]
> Se tiver [implantado manualmente o Live Video Analytics no IoT Edge](deploy-iot-edge-device.md) num dispositivo de borda (como um dispositivo ARM64), então verá o módulo aparecer por baixo desse dispositivo, sob o Azure IoT Hub. Pode selecionar esse módulo e seguir o resto dos passos abaixo.

## <a name="use-direct-method-calls"></a>Use chamadas de métodos diretos

Pode utilizar o módulo para analisar streams de vídeo ao vivo invocando métodos diretos. Para obter mais informações, consulte [métodos diretos para análise de vídeo ao vivo no IoT Edge](direct-methods.md). 

### <a name="invoke-graphtopologylist"></a>Invocar a Lista de Graftopologia

Para enumerar todas as [topologias de gráficos](media-graph-concept.md#media-graph-topologies-and-instances) do módulo:

1. No Código do Estúdio Visual, clique à direita no módulo **LvaEdge** e **selecione Invoke Module Direct Method**.
1. Na caixa que aparece, insira *a GraphTopologyList*.
1. Copie a carga json seguinte e, em seguida, cole-a na caixa. Em seguida, selecione a tecla 'Entrar'.

    ```
    {
        "@apiVersion" : "2.0"
    }
    ```

    Em poucos segundos, a janela **OUTPUT** mostra a seguinte resposta.

    ```
    [DirectMethod] Invoking Direct Method [GraphTopologyList] to [lva-sample-device/lvaEdge] ...
    [DirectMethod] Response from [lva-sample-device/lvaEdge]:
    {
      "status": 200,
      "payload": {
        "value": []
      }
    }
    ```
    
    Esta resposta é esperada porque não foram criadas topologias de gráficos.
    

### <a name="invoke-graphtopologyset"></a>Invocar o GraphTopologySet

Como fizemos antes, pode agora invocar `GraphTopologySet` para definir uma [topologia de gráficos.](media-graph-concept.md#media-graph-topologies-and-instances) Utilize o seguinte JSON como carga útil.

```
{
    "@apiVersion": "2.0",
    "name": "MotionDetection",
    "properties": {
        "description": "Analyzing live video to detect motion and emit events",
        "parameters": [
            {
                "name": "rtspUserName",
                "type": "String",
                "description": "rtsp source user name.",
                "default": "dummyUserName"
            },
            {
                "name": "rtspPassword",
                "type": "String",
                "description": "rtsp source password.",
                "default": "dummyPassword"
            },
            {
                "name": "rtspUrl",
                "type": "String",
                "description": "rtsp Url"
            }
        ],
        "sources": [
            {
                "@type": "#Microsoft.Media.MediaGraphRtspSource",
                "name": "rtspSource",
                "endpoint": {
                    "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
                    "url": "${rtspUrl}",
                    "credentials": {
                        "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
                        "username": "${rtspUserName}",
                        "password": "${rtspPassword}"
                    }
                }
            }
        ],
        "processors": [
            {
                "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
                "name": "motionDetection",
                "sensitivity": "medium",
                "inputs": [
                    {
                        "nodeName": "rtspSource"
                    }
                ]
            }
        ],
        "sinks": [
            {
                "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
                "name": "hubSink",
                "hubOutputName": "inferenceOutput",
                "inputs": [
                    {
                        "nodeName": "motionDetection"
                    }
                ]
            }
        ]
    }
}

```

Esta carga útil JSON cria uma topologia de gráfico que define três parâmetros. Dois desses parâmetros têm valores padrão. A topologia tem um nó de origem (fonte RTSP), um nó de processador (processador de deteção de movimento) e um nó de pia (pia IoT Hub).

Dentro de alguns segundos, vê-se a seguinte resposta na janela **OUTPUT.**

```
[DirectMethod] Invoking Direct Method [GraphTopologySet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 201,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-19T07:41:34.507Z",
      "lastModifiedAt": "2020-05-19T07:41:34.507Z"
    },
    "name": "MotionDetection",
    "properties": {
      "description": "Analyzing live video to detect motion and emit events",
      "parameters": [
        {
          "name": "rtspUserName",
          "type": "String",
          "description": "rtsp source user name.",
          "default": "dummyUserName"
        },
        {
          "name": "rtspPassword",
          "type": "String",
          "description": "rtsp source password.",
          "default": "dummyPassword"
        },
        {
          "name": "rtspUrl",
          "type": "String",
          "description": "rtsp Url"
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.Media.MediaGraphRtspSource",
          "name": "rtspSource",
          "transport": "Tcp",
          "endpoint": {
            "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
              "username": "${rtspUserName}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
          "sensitivity": "medium",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "hubOutputName": "inferenceOutput",
          "name": "hubSink",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            }
          ]
        }
      ]
    }
  }
}
```

O estado de de volta é 201. Este estatuto indica que foi criada uma nova topologia. 

Experimente os próximos passos:

1. Invoque `GraphTopologySet` de novo. O código de estado devolvido é 200. Este código indica que uma topologia existente foi atualizada com sucesso.
1. Invoque `GraphTopologySet` novamente, mas mude a cadeia de descrição. O código de estado devolvido é de 200, e a descrição é atualizada para o novo valor.
1. Invocar `GraphTopologyList` como descrito na secção anterior. Agora pode ver a `MotionDetection` topologia na carga devolvida.

### <a name="invoke-graphtopologyget"></a>Invocar GrafTopologyGet

Invoque `GraphTopologyGet` utilizando a seguinte carga útil.

```
{
    "@apiVersion" : "2.0",
    "name" : "MotionDetection"
}
```

Em poucos segundos, vê a seguinte resposta na janela **OUTPUT:**

```
[DirectMethod] Invoking Direct Method [GraphTopologyGet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-19T07:41:34.507Z",
      "lastModifiedAt": "2020-05-19T07:41:34.507Z"
    },
    "name": "MotionDetection",
    "properties": {
      "description": "Analyzing live video to detect motion and emit events",
      "parameters": [
        {
          "name": "rtspUserName",
          "type": "String",
          "description": "rtsp source user name.",
          "default": "dummyUserName"
        },
        {
          "name": "rtspPassword",
          "type": "String",
          "description": "rtsp source password.",
          "default": "dummyPassword"
        },
        {
          "name": "rtspUrl",
          "type": "String",
          "description": "rtsp Url"
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.Media.MediaGraphRtspSource",
          "name": "rtspSource",
          "transport": "Tcp",
          "endpoint": {
            "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
              "username": "${rtspUserName}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
          "sensitivity": "medium",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "hubOutputName": "inferenceOutput",
          "name": "hubSink",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            }
          ]
        }
      ]
    }
  }
}
```

Na carga útil de resposta, note estes detalhes:

* O código de estado é 200, indicando sucesso.
* A carga útil inclui a `created` hora e a `lastModified` hora.

### <a name="invoke-graphinstanceset"></a>Invocar GraphInstanceSet

Crie uma instância de gráfico que faz referência à topologia do gráfico anterior. As instâncias de gráfico permitem analisar streams de vídeo ao vivo de muitas câmaras usando a mesma topologia de gráficos. Para obter mais informações, consulte [topologias e instâncias de gráficos de mídia](media-graph-concept.md#media-graph-topologies-and-instances).

Invoque o método direto `GraphInstanceSet` utilizando a seguinte carga útil.

```
{
    "@apiVersion" : "2.0",
    "name" : "Sample-Graph-1",
    "properties" : {
        "topologyName" : "MotionDetection",
        "description" : "Sample graph description",
        "parameters" : [
            { "name" : "rtspUrl", "value" : "rtsp://rtspsim:554/media/camera-300s.mkv" }
        ]
    }
}
```

Note que esta carga útil:

* Especifica o nome de topologia `MotionDetection` ( ) para o qual o exemplo precisa ser criado.
* Contém um valor de parâmetro para `rtspUrl` , que não tinha um valor padrão na carga útil da topologia do gráfico. Este valor é um link para o vídeo da amostra abaixo:
    > [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LTY4]
Em poucos segundos, vê a seguinte resposta na janela **OUTPUT:**

```
[DirectMethod] Invoking Direct Method [GraphInstanceSet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 201,
  "payload": {
    "name": "Sample-Graph-1",
    "properties": {
      "created": "2020-05-19T07:44:33.868Z",
      "lastModified": "2020-05-19T07:44:33.868Z",
      "state": "Inactive",
      "description": "Sample graph description",
      "topologyName": "MotionDetection",
      "parameters": [
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
        }
      ]
    }
  }
}
```

Na carga útil da resposta, note que:

* O código de estado é 201, indicando que foi criado um novo caso.
* O Estado `Inactive` está, indicando que a instância do gráfico foi criada, mas não ativada. Para mais informações, consulte [os estados do gráfico de mídia.](media-graph-concept.md)

Experimente os próximos passos:

1. Invoque `GraphInstanceSet` novamente usando a mesma carga útil. Note que o código de estado devolvido é 200.
1. Invoque `GraphInstanceSet` de novo, mas use uma descrição diferente. Note a descrição atualizada na carga útil da resposta, indicando que a instância do gráfico foi atualizada com sucesso.
1. Invoque, `GraphInstanceSet` mas mude o nome para `Sample-Graph-2` . Na carga útil de resposta, note a instância de gráfico recém-criada (isto é, código de estado 201).

### <a name="invoke-graphinstanceactivate"></a>Invocar GraphInstanceActivate

Agora ative a instância do gráfico para iniciar o fluxo de vídeo ao vivo através do módulo. Invoque o método direto `GraphInstanceActivate` utilizando a seguinte carga útil.

```
{
    "@apiVersion" : "2.0",
    "name" : "Sample-Graph-1"
}
```

Dentro de alguns segundos, vê-se a seguinte resposta na janela **OUTPUT.**

```
[DirectMethod] Invoking Direct Method [GraphInstanceActivate] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

O código de estado de 200 indica que a instância do gráfico foi ativada com sucesso.

### <a name="invoke-graphinstanceget"></a>Invocar o GraphInstanceGet

Agora invoque o método direto `GraphInstanceGet` utilizando a seguinte carga útil.

```
 {
     "@apiVersion" : "2.0",
     "name" : "Sample-Graph-1"
 }
 ```

Dentro de alguns segundos, vê-se a seguinte resposta na janela **OUTPUT.**

```
[DirectMethod] Invoking Direct Method [GraphInstanceGet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": {
    "name": "Sample-Graph-1",
    "properties": {
      "created": "2020-05-19T07:44:33.868Z",
      "lastModified": "2020-05-19T07:44:33.868Z",
      "state": "Active",
      "description": "graph description",
      "topologyName": "MotionDetection",
      "parameters": [
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
        }
      ]
    }
  }
}
```

Na carga útil de resposta, note os seguintes detalhes:

* O código de estado é 200, indicando sucesso.
* O Estado `Active` está, indicando que a instância do gráfico está agora ativa.

## <a name="observe-results"></a>Observar resultados

A instância de gráfico que criamos e ativamos utiliza o nó do processador de deteção de movimento para detetar movimento na transmissão de vídeo ao vivo. Envia eventos para o nó da pia IoT Hub. Estes eventos são transmitidos ao IoT Edge Hub. 

Para observar os resultados, siga estes passos.

1. No Código do Estúdio Visual, abra o painel **Explorer.** No canto inferior esquerdo, procure **o Azure IoT Hub.**
2. Expandir o nó **dispositivos.**
3. Clique com o botão direito **do dispositivo de amostra de lva** e, em seguida, selecione Iniciar a **monitorização do evento incorporado**.

    ![Comece a monitorizar eventos do Iot Hub](./media/quickstarts/start-monitoring-iothub-events.png)

    > [!NOTE]
    > Pode ser-lhe pedido que forneça informações sobre o ponto final incorporado para o IoT Hub. Para obter essa informação, no portal Azure, navegue até ao seu Hub IoT e procure a opção **de pontos finais incorporados** no painel de navegação esquerdo. Clique lá e procure o **ponto final compatível com o Event Hub** na secção de ponto final **compatível com o Event Hub.** Copie e use o texto na caixa. O ponto final será mais ou menos assim:  
        ```
        Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
        ```
    
A janela **OUTPUT** apresenta a seguinte mensagem:

```
[IoTHubMonitor] [7:44:33 AM] Message received from [lva-sample-device/lvaEdge]:
{
    "body": {
    "timestamp": 143005362606360,
    "inferences": [
        {
        "type": "motion",
        "motion": {
            "box": {
            "l": 0.828452,
            "t": 0.455224,
            "w": 0.1,
            "h": 0.088889
            }
        }
        },
        {
        "type": "motion",
        "motion": {
            "box": {
            "l": 0.661088,
            "t": 0.597015,
            "w": 0.0625,
            "h": 0.051852
            }
        }
        }
    ]
    }
}
```

Note estes detalhes:

* A mensagem contém uma `body` secção e uma `applicationProperties` secção. Para obter mais informações, consulte [Criar e ler mensagens IoT Hub](../../iot-hub/iot-hub-devguide-messages-construct.md).
* Em `applicationProperties` , refere o nó no a partir do qual a `subject` `MediaGraph` mensagem foi gerada. Neste caso, a mensagem é originária do processador de deteção de movimentos.
* Em `applicationProperties` , indica que este evento é um evento de `eventType` análise.
* O `eventTime` valor é o momento em que o evento ocorreu.
* A `body` secção contém dados sobre o evento de análise. Neste caso, o evento é um evento de inferência, pelo que o corpo contém `timestamp` e `inferences` dados.
* A `inferences` secção indica que o é `type` `motion` . Fornece dados adicionais sobre o `motion` evento.

Se deixar o gráfico de mídia funcionar durante algum tempo, vê a seguinte mensagem na janela **OUTPUT.**

```
[IoTHubMonitor] [7:47:45 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1588948185746703 1 IN IP4 172.xx.xx.xx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets={SPS}\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-05-19T07:47:45.747Z"
  }
}
```

Nesta mensagem, note os seguintes detalhes:

* Em `applicationProperties` , indica que a `subject` mensagem foi gerada a partir do nó de origem RTSP no gráfico de mídia.
* Em `applicationProperties` , indica que este evento é `eventType` diagnóstico.
* Contém `body` dados sobre o evento de diagnóstico. Neste caso, a mensagem contém o corpo porque o evento é `MediaSessionEstablished` .

## <a name="invoke-additional-direct-methods-to-clean-up"></a>Invocar métodos diretos adicionais para limpar

Invoque métodos diretos para primeiro desativar a instância do gráfico e depois eliminá-la.

### <a name="invoke-graphinstancedeactivate"></a>Invocar GraphInstanceDeactivo

Invoque o método direto `GraphInstanceDeactivate` utilizando a seguinte carga útil.

```
{
    "@apiVersion" : "2.0",
    "name" : "Sample-Graph-1"
}
```

Em poucos segundos, vê a seguinte resposta na janela **OUTPUT:**

```
[DirectMethod] Invoking Direct Method [GraphInstanceDeactivate] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

O código de estado de 200 indica que a instância do gráfico foi desativada com sucesso.

Em seguida, tente invocar `GraphInstanceGet` como indicado anteriormente neste artigo. Observe o `state` valor.

### <a name="invoke-graphinstancedelete"></a>Invocar GraphInstanceDelete

Invoque o método direto `GraphInstanceDelete` utilizando a seguinte carga útil.

```
{
    "@apiVersion" : "2.0",
    "name" : "Sample-Graph-1"
}
```

Em poucos segundos, vê a seguinte resposta na janela **OUTPUT:**

```
[DirectMethod] Invoking Direct Method [GraphInstanceDelete] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Um código de estado de 200 indica que a instância do gráfico foi eliminada com sucesso.

### <a name="invoke-graphtopologydelete"></a>Invocar GraphTopologyDelete

Invoque o método direto `GraphTopologyDelete` utilizando a seguinte carga útil.

```
{
    "@apiVersion" : "2.0",
    "name" : "MotionDetection"
}
```

Dentro de alguns segundos, vê-se a seguinte resposta na janela **OUTPUT.**

```
[DirectMethod] Invoking Direct Method [GraphTopologyDelete] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Um código de estado de 200 indica que a topologia do gráfico foi eliminada com sucesso.

Experimente os próximos passos:

1. Invoque `GraphTopologyList` e observe que o módulo não contém topologias de gráficos.
1. Invoque `GraphInstanceList` utilizando a mesma carga útil que `GraphTopologyList` . Observe que não são enumeradas instâncias de gráficos.

## <a name="clean-up-resources"></a>Limpar os recursos

Se não vai continuar a utilizar esta aplicação, elimine os recursos que criou neste arranque rápido.

## <a name="next-steps"></a>Passos seguintes

* Aprenda a [gravar vídeo usando Live Video Analytics no IoT Edge](continuous-video-recording-tutorial.md).
* Saiba mais sobre [mensagens de diagnóstico.](monitoring-logging.md)