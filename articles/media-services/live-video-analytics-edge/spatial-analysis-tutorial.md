---
title: Analise vídeo ao vivo com visão computacional para análise espacial - Azure
description: Este tutorial mostra-lhe como usar o Live Video Analytics juntamente com a funcionalidade de análise espacial de IA da Azure Cognitive Services para analisar um feed de vídeo ao vivo a partir de uma câmara IP (simulada).
ms.topic: tutorial
ms.date: 09/08/2020
ms.openlocfilehash: 1c3fd411e60cabb504f01bece62d497ddeea31d8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105561020"
---
# <a name="analyze-live-video-with-computer-vision-for-spatial-analysis-preview"></a>Analise vídeo ao vivo com visão de computador para análise espacial (pré-visualização)

Este tutorial mostra-lhe como usar o Live Video Analytics juntamente com [a Computer Vision para análise espacial serviço de IA da Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/computer-vision/) para analisar um feed de vídeo ao vivo a partir de uma câmara IP (simulada). Você verá como este servidor de inferência permite-lhe analisar o vídeo de streaming para entender as relações espaciais entre pessoas e movimento no espaço físico.  Um subconjunto dos quadros do feed de vídeo é enviado para este servidor de inferência, e os resultados são enviados para o IoT Edge Hub e quando algumas condições são satisfeitas, os videoclips são gravados e armazenados como ativos do Azure Media Services.

Neste tutorial você:

> [!div class="checklist"]
> * Criar recursos.
> * Examine o código.
> * Executar o código de amostra.
> * Monitorize os acontecimentos.
 
[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
  > [!NOTE]
  > Você precisará de uma subscrição Azure com permissões para criar principais serviços **(a função do proprietário** fornece isso). Se não tiver as permissões certas, contacte o administrador da sua conta para lhe conceder as permissões certas. 
## <a name="suggested-pre-reading"></a>Pré-leitura sugerida

Leia estes artigos antes de começar:

* [Análise de vídeo ao vivo na visão geral do IoT Edge](overview.md)
* [Análise de vídeo ao vivo na terminologia IoT Edge](terminology.md)
* [Conceitos de gráficos de mídia](media-graph-concept.md)
* [Gravação de vídeo baseada em eventos](event-based-video-recording-concept.md)
* [Tutorial: Desenvolvimento de um módulo IoT Edge](../../iot-edge/tutorial-develop-for-linux.md)
* [Implementar vídeo ao vivo analytics no Azure Stack Edge](deploy-azure-stack-edge-how-to.md) 

## <a name="prerequisites"></a>Pré-requisitos

Seguem-se os pré-requisitos para a ligação do módulo de análise espacial ao módulo Live Video Analytics.

* [Código de estúdio visual](https://code.visualstudio.com/) na sua máquina de desenvolvimento. Certifique-se de que tem a [extensão Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
    * Certifique-se de que a rede de que a sua máquina de desenvolvimento está ligada permite o Protocolo avançado de fila de mensagens sobre a porta 5671. Esta configuração permite que as ferramentas Azure IoT se comuniquem com o Azure IoT Hub.
* [Azure Stack Edge](https://azure.microsoft.com/products/azure-stack/edge/) com aceleração gpu.  
    Recomendamos que utilize o Azure Stack Edge com aceleração gpu, no entanto o recipiente funciona em qualquer outro dispositivo com um [GPU NVIDIA Tesla T4](https://www.nvidia.com/en-us/data-center/tesla-t4/). 
* [Recipiente Azure Cognitive Service Computer Vision](https://azure.microsoft.com/services/cognitive-services/computer-vision/) para análise espacial.  
    Para utilizar este recipiente, tem de dispor de um recurso de Visão Computacional para obter a **chave API** associada e um **URI de ponto final.** A chave API está disponível nas páginas visão e chaves do portal Azure. A chave e o ponto final são necessários para iniciar o recipiente.

## <a name="overview"></a>Descrição Geral

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/spatial-analysis-tutorial/overview.png" alt-text="Visão geral da análise espacial":::
 
Este diagrama mostra como os sinais fluem neste tutorial. Um [módulo de borda](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simula uma câmara IP que hospeda um servidor Real-Time de Protocolo de Streaming (RTSP). Um nó [de origem RTSP](media-graph-concept.md#rtsp-source) puxa o feed de vídeo deste servidor e envia quadros de vídeo para o nó do `MediaGraphCognitiveServicesVisionExtension` processador.

O nó de mediaGraphCognitiveServicesVisionExtension desempenha o papel de um representante. Converte os quadros de vídeo para o tipo de imagem especificado. Em seguida, transmite a imagem sobre **memória partilhada** para outro módulo de borda que executa operações de IA atrás de um ponto final gRPC. Neste exemplo, este módulo de borda é o módulo de análise espacial. O nó do processador MediaGraphGraphCognitiveServicesVisionExtension faz duas coisas:

* Reúne os resultados e publica eventos no nó [de pia IoT Hub.](media-graph-concept.md#iot-hub-message-sink) O nó envia então esses eventos para [ioT Edge Hub](../../iot-fundamentals/iot-glossary.md#iot-edge-hub). 
* Também captura um videoclip de 30 segundos da fonte RTSP usando um [processador signal gate](media-graph-concept.md#signal-gate-processor) e armazena-o como um ativo dos Media Services.

## <a name="create-the-computer-vision-resource"></a>Criar o recurso Visão De Computador

É necessário criar um recurso Azure do tipo Visão computacional, quer no [portal Azure,](../../iot-edge/how-to-deploy-modules-portal.md) quer através do Azure CLI. Poderá criar o recurso assim que o seu pedido de acesso ao contentor tiver sido aprovado e o seu ID de assinatura Azure estiver registado. Vá https://aka.ms/csgate para submeter o seu caso de uso e o seu ID de assinatura Azure.  É necessário criar o recurso Azure utilizando a mesma assinatura Azure que foi fornecida no formulário Pedido de Acesso.

### <a name="gathering-required-parameters"></a>Recolha de parâmetros necessários

Existem três parâmetros primários para todos os contentores dos Serviços Cognitivos que são necessários, incluindo o recipiente de análise espacial. O contrato de licença de utilizador final (EULA) deve estar presente com um valor de aceitação. Além disso, tanto uma Chave Endpoint URI como API são necessárias.

### <a name="keys-and-endpoint-uri"></a>Chaves e Endpoint URI

Uma chave é usada para iniciar o recipiente de análise espacial, e está disponível na página do portal Azure `Keys and Endpoint` do recurso correspondente do Serviço Cognitivo. Navegue para a página e encontre as chaves e o ponto final URI.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/spatial-analysis-tutorial/keys-endpoint.png" alt-text="Endpoint URI":::

## <a name="set-up-azure-stack-edge"></a>Configurar Azure Stack Edge

Siga [estes passos](../../databox-online/azure-stack-edge-gpu-deploy-prep.md) para configurar o Azure Stack Edge e continue a seguir os passos abaixo para implementar o Live Video Analytics e os módulos de análise espacial.

## <a name="set-up-your-development-environment"></a>Configurar o ambiente de desenvolvimento

1. Clone o repo a partir deste local: https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp .
1. No Código do Estúdio Visual, abra a pasta onde o repo foi descarregado.
1. No Código do Estúdio Visual, aceda à pasta src/cloud-to-device-console-app. Lá, crie um ficheiro e diga-lhe appsettings.js. Este ficheiro conterá as definições necessárias para executar o programa.
1. Obtenha o IotHubConnectionString a partir do Azure Stack Edge seguindo estes passos:

    * vá ao seu IoT Hub no portal Azure e clique `Shared access policies` no painel de navegação esquerdo.
    * Clique em `iothubowner` obter as teclas de acesso partilhadas.
    * Copie-o  `Connection String – primary key` e cole-o na caixa de entrada do VSCode.
    
        A cadeia de ligação será como: <br/>`HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx`
1. Copie o conteúdo abaixo no ficheiro. Certifique-se de que substitui as variáveis.
    
    ```json
    {
        "IoThubConnectionString" : "HostName=<IoTHubName>.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=<SharedAccessKey>",
        "deviceId" : "<your Azure Stack Edge name>",
        "moduleId" : "lvaEdge"
    } 
    ```
1. Vá à pasta src/edge e crie um ficheiro chamado .env.
1. Copie o conteúdo do ficheiro /clouddrive/lva-sample/edge-deployment/.env. O texto deve parecer o seguinte código.

    ```env
    SUBSCRIPTION_ID="<Subscription ID>"  
    RESOURCE_GROUP="<Resource Group>"  
    AMS_ACCOUNT="<AMS Account ID>"  
    IOTHUB_CONNECTION_STRING="HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx"  
    AAD_TENANT_ID="<AAD Tenant ID>"  
    AAD_SERVICE_PRINCIPAL_ID="<AAD SERVICE_PRINCIPAL ID>"  
    AAD_SERVICE_PRINCIPAL_SECRET="<AAD SERVICE_PRINCIPAL ID>"  
    VIDEO_INPUT_FOLDER_ON_DEVICE="/home/lvaedgeuser/samples/input"  
    VIDEO_OUTPUT_FOLDER_ON_DEVICE="/var/media"
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry password>"   
    ```
    
## <a name="set-up-deployment-template"></a>Configurar o modelo de implementação  

Procure o ficheiro de implantação em /src/edge/deployment.spatialAnalysis.template.jsligado. A partir do modelo, existem módulos LvaEdge, módulo rtspsim e nosso módulo de análise espacial.

Há algumas coisas a que precisa prestar atenção no ficheiro do modelo de implementação:

1. Desa estalades porta.
    
    ```
    "PortBindings": {
        "50051/tcp": [
            {
                "HostPort": "50051"
            }
        ]
    },
    ```
1. `IpcMode` em LvaEdge e módulo de análise espacial criarOptions deve ser o mesmo e definido para hospedar.
1. Para que o simulador RTSP funcione, certifique-se de que configura os Limites de Volume. Para obter mais informações, consulte [configuração estivador volume mounts](deploy-azure-stack-edge-how-to.md#optional-setup-docker-volume-mounts).

    1. [Ligue-se à partilha SMB](../../databox-online/azure-stack-edge-deploy-add-shares.md#connect-to-an-smb-share) e copie o [ficheiro de vídeo bulldozer](https://lvamedia.blob.core.windows.net/public/bulldozer.mkv) da amostra para a partilha local.  
        > [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4Mesi]  
    1. Veja se o módulo rtspsim tem a seguinte configuração:
        ```
        "createOptions": {
                            "HostConfig": {
                              "Mounts": [
                                {
                                  "Target": "/live/mediaServer/media",
                                  "Source": "<your Local Docker Volume Mount name>",
                                  "Type": "volume"
                                }
                              ],
                              "PortBindings": {
                                "554/tcp": [
                                  {
                                    "HostPort": "554"
                                  }
                                ]
                              }
                            }
                          }
        ```
        

## <a name="generate-and-deploy-the-deployment-manifest"></a>Gerar e implementar o manifesto de implantação

O manifesto de implantação define quais os módulos que são implantados num dispositivo de borda. Também define definições de configuração para esses módulos.

Siga estes passos para gerar o manifesto a partir do ficheiro do modelo e, em seguida, implante-o para o dispositivo de borda.

1. Abra o Visual Studio Code.
1. Ao lado do painel AZURE IOT HUB, selecione o ícone Mais ações para definir a cadeia de ligação IoT Hub. Pode copiar o fio do `src/cloud-to-device-console-app/appsettings.json` ficheiro.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/connection-string.png" alt-text="Análise Espacial: cadeia de ligação":::
1. Clique com o botão direito `src/edge/deployment.spatialAnalysis.template.json` e selecione Generate IoT Edge Deployment Manifest.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/deployment-template-json.png" alt-text="Análise Espacial: implantação amd64 json":::
    
    Esta ação deve criar um ficheiro manifesto denominado deployment.amd64.jsna pasta src/edge/config.
1. Clique no botão `src/edge/config/deployment.spatialAnalysis.amd64.json` direito, selecione Criar Implementação para Dispositivo Único e, em seguida, selecione o nome do seu dispositivo de borda.
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/deployment-amd64-json.png" alt-text="Análise espacial: json modelo de implantação":::   
1. Quando lhe for solicitado que selecione um dispositivo IoT Hub, escolha o nome Azure Stack Edge no menu suspenso.
1. Após cerca de 30 segundos, no canto inferior esquerdo da janela, refresque o Azure IoT Hub. O dispositivo de borda mostra agora os seguintes módulos implantados:
    
    * Vídeo ao vivo Analytics no IoT Edge (nome do módulo lvaEdge).
    * Real-Time simulador de Protocolo de Streaming (RTSP) (nome do módulo rtspsim).
    * Análise Espacial (nome do módulo espacialAnalysis).
    
Se implementar com sucesso, haverá uma mensagem no OUTPUT como esta:

```
[Edge] Start deployment to device [<Azure Stack Edge name>]
[Edge] Deployment succeeded.
```

Em seguida, pode `lvaEdge` `rtspsim` encontrar, `spatialAnalysis` e `rtspsim` módulos em Dispositivos/Módulos, e o seu estado deve estar "a funcionar".

## <a name="prepare-to-monitor-events"></a>Preparar para monitorizar eventos

Para ver estes eventos, siga estes passos:

1. No Código do Estúdio Visual, abra o **separador Extensões** (ou prima Ctrl+Shift+X) e procure por Azure IoT Hub.
1. Clique com o botão direito e selecione **Definições de extensão**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Definições de extensão":::
1. Procure e ative "Mostrar Mensagem Verbose".

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Mostrar mensagem verbose":::
1. Abra o painel Explorer e procure o Azure IoT Hub no canto inferior esquerdo.
1. Expandir o nó dispositivos.
1. Clique com o botão direito no seu Azure Stack Edge e selecione Start Monitoring Built-in Event Endpoint.
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/start-monitoring.png" alt-text="Análise Espacial: iniciar a monitorização":::
     
## <a name="run-the-program"></a>Execute o programa

Existe um programa.cs que irá invocar os métodos diretos na src/cloud-to-device-console-app/operations.js. Temos de configurar operations.jse fornecer uma topologia para uso de gráficos de mídia.  

Em operations.jsem:

* Definir a topologia assim:

```json
{
    "opName": "GraphTopologySet",
    "opParams": {
        "topologyUrl": "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/lva-spatial-analysis/2.0/topology.json"
    }
},
```

* Crie exemplos de gráficos como este, deite os parâmetros em topologia aqui:

```json
{
    "opName": "GraphInstanceSet",
    "opParams": {
        "name": "Sample-Graph-1",
        "properties": {
            "topologyName": "InferencingWithCVExtension",
            "description": "Sample graph description",
            "parameters": [
                {
                    "name": "rtspUrl",
                    "value": " rtsp://rtspsim:554/media/bulldozer.mkv"
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
},
```

>[!Note]
Confira a utilização do MediaGraphRealTimeComputerVisionExtension para se conectar com o módulo de análise espacial. Defina o ${grpcUrl} para **tcp://spatialAnalysis:<PORT_NUMBER>**, por exemplo, tcp://spatialAnalysis:50051

```json
{
    "@type": "#Microsoft.Media.MediaGraphCognitiveServicesVisionExtension",
    "name": "computerVisionExtension",
    "endpoint": {
        "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
        "url": "${grpcUrl}",
        "credentials": {
            "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
            "username": "${spatialanalysisusername}",
            "password": "${spatialanalysispassword}"
        }
    },
    "image": {
        "scale": {
            "mode": "pad",
            "width": "1408",
            "height": "786"
        },
        "format": {
            "@type": "#Microsoft.Media.MediaGraphImageFormatRaw",
            "pixelFormat": "bgr24"
        }
    },
    "samplingOptions": {
        "skipSamplesWithoutAnnotation": "false",
        "maximumSamplesPerSecond": "20"
    },
    "inputs": [
        {
            "nodeName": "rtspSource",
            "outputSelectors": [
                {
                    "property": "mediaType",
                    "operator": "is",
                    "value": "video"
                }
            ]
        }
    ]
}
```

Executar uma sessão de depurar e seguir as instruções **TERMINAL,** definirá topologia, definirá a instância do gráfico, ativará a instância do gráfico e, finalmente, eliminará os recursos.

## <a name="interpret-results"></a>Interpretar os resultados

Quando um gráfico mediático é instantâneo, você deve ver o evento "MediaSessionEstablished", aqui uma [amostra do evento MediaSessionEstablished](detect-motion-emit-events-quickstart.md#mediasessionestablished-event).

O módulo de análise espacial também enviará eventos de AI Insight para Live Video Analytics e, em seguida, para ioTHub, que também mostrará em **OUTPUT**. A ENTIDADE é objetos de deteção, e o EVENT é eventos espaciais. Esta saída será transmitida para Live Video Analytics.

Produção de amostra para personZoneEvent (a partir de cognitiveservices.vision.spatialanalysis-personcrossingpolygon.livevideoanalytics):

```
{
  "body": {
    "timestamp": 143810610210090,
    "inferences": [
      {
        "type": "entity",
        "subtype": "",
        "inferenceId": "a895c86824db41a898f776da1876d230",
        "relatedInferences": [],
        "entity": {
          "tag": {
            "value": "person",
            "confidence": 0.66026187
          },
          "attributes": [],
          "box": {
            "l": 0.26559368,
            "t": 0.17887735,
            "w": 0.49247605,
            "h": 0.76629657
          }
        },
        "extensions": {},
        "valueCase": "entity"
      },
      {
        "type": "event",
        "subtype": "",
        "inferenceId": "995fe4206847467f8dfde83a15187d76",
        "relatedInferences": [
          "a895c86824db41a898f776da1876d230"
        ],
        "event": {
          "name": "personZoneEvent",
          "properties": {
            "status": "Enter",
            "metadataVersion": "1.0",
            "zone": "polygon0",
            "trackingId": "a895c86824db41a898f776da1876d230"
          }
        },
        "extensions": {},
        "valueCase": "event"
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/81631a63-f0bd-4f35-8344-5bc541b36bfc/resourceGroups/lva-sample-resources/providers/microsoft.media/mediaservices/lvasamplea4bylcwoqqypi",
    "subject": "/graphInstances/Sample-Graph-1/processors/spatialanalysisExtension",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-08-20T03:54:29.001Z",
    "dataVersion": "1.0"
  }
}
```

## <a name="next-steps"></a>Passos seguintes

Experimente diferentes operações que o `spatialAnalysis` módulo oferece, tais como **personCount** e **personDistance,** toggling a bandeira "ativada" no nó gráfico do seu ficheiro manifesto de implantação.
>[!Tip]
> Utilize um [ficheiro de vídeo de amostra](https://lvamedia.blob.core.windows.net/public/2018-03-07.16-50-00.16-55-00.school.G421.mkv) que tenha mais de uma pessoa na moldura.

> [!NOTE]
> Só pode executar uma operação de cada vez. Por isso, certifique-se de que apenas uma bandeira é **verdadeira** e as outras são **falsas.**