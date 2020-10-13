---
title: Analise o vídeo ao vivo com o Live Video Analytics no IoT Edge e Azure Custom Vision
description: Aprenda a usar a Visão Personalizada para construir um modelo contentorizado que possa detetar um caminhão de brinquedo e usar a capacidade de extensibilidade de IA do Live Video Analytics no IoT Edge (LVA) para implantar o modelo na borda para detetar camiões de brinquedos a partir de um stream de vídeo ao vivo.
ms.topic: tutorial
ms.date: 09/08/2020
ms.openlocfilehash: 7989b3636fe953b8110e356506a5867fefd2d8b6
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2020
ms.locfileid: "91940179"
---
# <a name="tutorial-analyze-live-video-with-live-video-analytics-on-iot-edge-and-azure-custom-vision"></a>Tutorial: Analise vídeo ao vivo com vídeo ao vivo analíticos em IoT Edge e Azure Custom Vision

Neste tutorial, você vai aprender a usar a [Visão Personalizada](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/) para construir um modelo contentorizado que pode detetar um caminhão de brinquedo e usar [a capacidade de extensibilidade](analyze-live-video-concept.md#analyzing-video-using-a-custom-vision-model) de AI do Live Video Analytics no IoT Edge para implantar o modelo na borda para detetar camiões de brinquedo a partir de um stream de vídeo ao vivo.

Vamos mostrar-lhe como reunir o poder da Visão Personalizada - que permite a qualquer pessoa construir e treinar um modelo de visão computacional simplesmente carregando e rotulando algumas imagens, sem qualquer conhecimento da ciência dos dados, ML ou IA - juntamente com as capacidades do Live Video Analytics para facilmente implementar um modelo personalizado como recipiente na borda e analisar um feed de vídeo ao vivo simulado. Este tutorial usa um Azure VM como um dispositivo IoT Edge, é baseado no código de amostra escrito em C#, e baseia-se no [movimento Detect e emite eventos](detect-motion-emit-events-quickstart.md) de quickstart.

Este tutorial mostra-lhe como:

> [!div class="checklist"]
> * Criar os recursos relevantes.
> * Construa um modelo de Visão Personalizada na nuvem para detetar camiões de brinquedo e implantá-lo na borda
> * Criar e implementar um gráfico de mídia com extensão http ao modelo de visão personalizada
> * Executar o código de amostra.
> * Examine e interprete os resultados.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Pré-leitura sugerida  

Recomenda-se que leia os seguintes artigos antes de começar: 

* [Análise de vídeo ao vivo na visão geral do IoT Edge](overview.md)
* [Visão Personalizada Azure](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/home)
* [Análise de vídeo ao vivo na terminologia IoT Edge](terminology.md)
* [Conceitos de gráficos de mídia](media-graph-concept.md)
* [Live Video Analytics sem gravação de vídeo](analyze-live-video-concept.md)
* [Executando vídeo ao vivo analytics com o seu próprio modelo](use-your-model-quickstart.md)
* [Tutorial: Desenvolvimento de um módulo IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-develop-for-linux)
* [Como editar a implementação.*.template.jsem](https://github.com/microsoft/vscode-azure-iot-edge/wiki/How-to-edit-deployment.*.template.json)

## <a name="prerequisites"></a>Pré-requisitos

Os pré-requisitos para este tutorial são:

* [Código de Estúdio Visual](https://code.visualstudio.com/) na sua máquina de desenvolvimento com as [ferramentas Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) e extensões [C#.](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)

    > [!TIP]
    > Pode ser solicitado a instalar o Docker. Ignore este pedido.
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/thank-you/sdk-3.1.201-windows-x64-installer) na sua máquina de desenvolvimento.
* Certifique-se de que tem:
    
    * [Criar Recursos Azure](detect-motion-emit-events-quickstart.md#set-up-azure-resources)
    * [Configurar o ambiente de desenvolvimento](detect-motion-emit-events-quickstart.md#set-up-your-development-environment)

## <a name="review-the-sample-video"></a>Reveja o vídeo da amostra

Este tutorial usa um ficheiro [de vídeo de inferência de carro de brinquedo](https://lvamedia.blob.core.windows.net/public/t2.mkv) para simular um fluxo ao vivo. Pode examinar o vídeo através de uma aplicação como o [leitor de mídia VLC](https://www.videolan.org/vlc/). Selecione Ctrl+N e, em seguida, cole um link para o [vídeo de inferência](https://lvamedia.blob.core.windows.net/public/t2.mkv) do carro de brinquedo para iniciar a reprodução. Ao ver o vídeo nota que no marcador de 36 segundos aparece um caminhão de brinquedo no vídeo. O modelo personalizado foi treinado para detetar este caminhão de brinquedo específico. Neste tutorial, você usará live video analytics no IoT Edge para detetar tais caminhões de brinquedo e publicar eventos de inferência associados ao IoT Edge Hub.

## <a name="overview"></a>Descrição geral

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/topology-custom-vision.svg" alt-text="Visão personalizada":::

Este diagrama mostra como os sinais fluem neste tutorial. Um [módulo de borda](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simula uma câmara IP que hospeda um servidor Real-Time de Protocolo de Streaming (RTSP). Um nó [de origem RTSP](media-graph-concept.md#rtsp-source) puxa o feed de vídeo deste servidor e envia quadros de vídeo para o nó do [processador do processador da taxa de fotogramas.](media-graph-concept.md#frame-rate-filter-processor) Este processador limita a taxa de fotogramas do fluxo de vídeo que atinge o nó [do processador de extensão HTTP.](media-graph-concept.md#http-extension-processor)
O nó de extensão HTTP desempenha o papel de um representante. Converte os quadros de vídeo para o tipo de imagem especificado. Em seguida, transmite a imagem sobre REST para outro módulo de borda que executa um modelo de IA atrás de um ponto final HTTP. Neste exemplo, este módulo de borda é o modelo de detetor de caminhões de brinquedo construído utilizando a Visão Personalizada. O nó do processador de extensão HTTP recolhe os resultados de deteção e publica eventos no nó [de pia IoT Hub.](media-graph-concept.md#iot-hub-message-sink) O nó envia então esses eventos para [ioT Edge Hub](https://docs.microsoft.com/azure/iot-edge/iot-edge-glossary#iot-edge-hub).

## <a name="build-and-deploy-a-custom-vision-toy-detection-model"></a>Construa e implemente um modelo de deteção de brinquedos de visão personalizada 

Como o nome Custom Vision sugere, pode alavancar para construir o seu próprio detetor de objetos personalizado ou classificador na nuvem. Fornece uma interface simples, fácil de usar e intuitiva para construir modelos de visão personalizados que podem ser implantados na nuvem ou na borda através de contentores. 

Para construir um detetor de caminhões de brinquedo, recomendamos que siga este detetor de objetos de visão personalizada através do [artigo de arranque rápido](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/get-started-build-detector) do portal web .

Notas adicionais:
 
* Para este tutorial, não utilize as imagens de amostra fornecidas na [secção de pré-requisitos](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/get-started-build-detector#prerequisites)do artigo de início rápido . Em vez disso, aproveitamos um determinado conjunto de imagens para construir um modelo de visão personalizada do detetor de [brinquedos,](https://lvamedia.blob.core.windows.net/public/ToyCarTrainingImages.zip) sugerimos que use estas imagens quando lhe é pedido que [escolha as suas imagens](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/get-started-build-detector#choose-training-images) de treino no arranque rápido.
* Na secção de imagem de marcação do arranque rápido, certifique-se de que está a marcar o caminhão de brinquedo visto na imagem com a etiqueta – "caminhão de entrega".

Uma vez terminado, se o modelo estiver pronto de acordo com a sua satisfação, pode exportá-lo para um recipiente Docker utilizando o botão Exportação no separador Performance. Certifique-se de que escolhe o Linux como tipo de plataforma de contentores. Esta é a plataforma em que o contentor vai funcionar. A máquina em que descarrega o recipiente pode ser o Windows ou o Linux. As instruções que se seguem basearam-se no ficheiro do contentor descarregado numa máquina Windows.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/docker-file.png" alt-text="Visão personalizada"   13 hours ago        Up 25 seconds       127.0.0.1:80->80/tcp   practical_cohen
        ```
      1. `curl -X POST http://127.0.0.1:80/image -F imageData=@<path to any image file that has the toy delivery truck in it>`
            
            Este comando testa o contentor na máquina local e se a imagem tiver o mesmo caminhão de entrega que treinamos o modelo, então a saída deve ser algo como o seguinte, sugerindo que o caminhão de entrega foi detetado com 90,12% de probabilidade.
    
            ```
            {"created":"2020-03-20T07:10:47.827673","id":"","iteration":"","predictions":[{"boundingBox":{"height":0.66167289,"left":-0.03923762,"top":0.12781593,"width":0.70003178},"probability":0.90128148,"tagId":0,"tagName":"delivery truck"},{"boundingBox":{"height":0.63733053,"left":0.25220079,"top":0.0876643,"width":0.53331227},"probability":0.59745145,"tagId":0,"tagName":"delivery truck"}],"project":""}
            ```

<!--## Create and deploy a media graph with http extension to custom vision model-->

## <a name="examine-the-sample-files"></a>Examinar os ficheiros de amostras

1. No VSCode, navegue para "src/edge". Verá o ficheiro .env que criou juntamente com alguns ficheiros de modelos de implementação.

    O modelo de implantação refere-se ao manifesto de implantação do dispositivo de borda com alguns valores de espaço reservado. O ficheiro .env tem os valores para essas variáveis.
1. Em seguida, navegue para a pasta "src/cloud-to-device-console-app". Aqui verá o appsettings.jsno ficheiro que criou juntamente com outros ficheiros:

    * c2d-console-app.csproj - Este é o ficheiro do projeto para VSCode.
    * operations.js- Este ficheiro irá listar as diferentes operações que gostaria que o programa fosse executado.
    * Program.cs - Este é o código do programa de amostra que faz o seguinte:

        * Carrega as definições da aplicação.
        * Invocar o Live Video Analytics nos Métodos Diretos do módulo IoT Edge para criar topologia, instantaneamente o gráfico e ativar o gráfico.
        * Pausas para examinar a saída do gráfico na janela TERMINAL e os eventos enviados para o hub IoT na janela OUTPUT.
        * Desativar a instância do gráfico, eliminar a instância do gráfico e eliminar a topologia do gráfico.
        
## <a name="generate-and-deploy-the-deployment-manifest"></a>Gerar e implementar o manifesto de implantação

1. No VSCode, navegue para "src/cloud-to-device-console-app/operations.json"

1. No GraphTopologySet, certifique-se de que o seguinte é verdadeiro:<br/>`"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtension/topology.json"`
1. No Âmbito do GraphInstanceSet, certifique-se: 
    1. "TopologyName" : "InferencingWithHttpExtension"
    1. Adicione o seguinte ao topo da matriz de parâmetros - `{"name": "inferencingUrl","value": "http://cv:80/image"},`
    1. Altere o valor do parâmetro rtspUrl para – "rtsp://rtspsim:554/media/t2.mkv"    
1. Sob a GraphTopologyDelete, garanta "nome": "InferencingWithHttpExtension"
1. Clique no ficheiro "src/edge/ deployment.customvision.template.json" e clique no **Manifesto de Implementação de Arestas de IoT**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/deployment-template-json.png" alt-text="Visão personalizada" ao lado do painel AZURE IOT HUB no canto inferior esquerdo. Pode copiar o fio do appsettings.jsficheiro. (Aqui está outra abordagem recomendada para garantir que tem o Hub IoT adequado configurado dentro do VSCode através do [comando Select Iot Hub](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub)).

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/connection-string.png" alt-text="Visão personalizada" e clique em **Criar Implementação para Dispositivo Único**. 

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/deployment-amd64-json.png" alt-text="Visão personalizada":::
1. Em seguida, ser-lhe-á pedido que selecione um dispositivo IoT Hub. Selecione o dispositivo de amostra de Lva a partir do drop-down.
1. Em cerca de 30 segundos, refresque o Azure IOT Hub na secção inferior esquerda e deverá ter o dispositivo de borda com os seguintes módulos implantados:

    * O live video analytics no módulo IoT Edge, nomeado como "LvaEdge".
    * Um módulo nomeado `rtspsim` que simula um Servidor RTSP, agindo como a fonte de um feed de vídeo ao vivo.
    * Um módulo chamado `cv` , que como o nome sugere é o modelo de deteção de caminhões de brinquedo Visão Personalizada que aplica visão personalizada às imagens e devolve vários tipos de tags. (O nosso modelo foi treinado apenas com uma etiqueta – "caminhão de entrega").

## <a name="prepare-for-monitoring-events"></a>Preparar para eventos de monitorização

Clique com o botão direito no dispositivo Live Video Analytics e selecione **Start Monitoring Built-in Event Endpoint**. Precisa deste passo para monitorizar os eventos do IoT Hub na janela OUTPUT do Código do Estúdio Visual.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/start-monitoring.png" alt-text="Visão personalizada":::

## <a name="run-the-sample-program"></a>Executar o programa de amostragem

Se abrir a topologia do gráfico para este tutorial num browser, verá que o valor do inferencingUrl foi definido para , o que significa que o servidor de http://cv:80/image inferência devolverá os resultados após a deteção de camiões de brinquedo, caso existam, no vídeo ao vivo.

1. No Código do Estúdio Visual, abra o **separador Extensões** (ou prima Ctrl+Shift+X) e procure por Azure IoT Hub.
1. Clique no direito e selecione **Definições de extensão**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Visão personalizada":::
1. Procure e ative "Mostrar Mensagem Verbose".

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Visão personalizada"
              }
            ]
          }
        }
   ```
    
   * Uma chamada para o GraphInstanceActivate que inicia a instância do gráfico e o fluxo de vídeo.
   * Uma segunda chamada para o GraphInstanceList que mostra que a instância do gráfico está no estado de execução.
    
1. A saída na janela TERMINAL para numa Entrada de Imprensa para continuar a ser solicitada. Não selecione Enter ainda. Percorra para ver as cargas de resposta JSON para os métodos diretos que invocou.
1. Mude para a janela OUTPUT no Código do Estúdio Visual. Vê mensagens que o modusão IoT Edge está a enviar para o hub IoT. A secção seguinte deste tutorial discute estas mensagens.
1. O gráfico mediático continua a correr e a imprimir resultados. O simulador RTSP continua a dar a volta ao vídeo de origem. Para parar o gráfico de mídia, volte à janela TERMINAL e selecione Enter.
A próxima série de chamadas limpa recursos:
    
   * Uma chamada para o GraphInstanceDeactiva desativa a instância do gráfico.
   * Uma chamada para GraphInstanceDelete elimina o caso.
   * Uma chamada para GraphTopologyDelete elimina a topologia.
   * Uma chamada final para a GraphTopologyList mostra que a lista está vazia.
    
## <a name="interpret-the-results"></a>Interpretar os Resultados

Quando executou o gráfico de mídia, os resultados do nó do processador de extensão HTTP passam pelo nó da pia IoT Hub para o hub IoT. As mensagens que vê na janela OUTPUT contêm uma secção do corpo e uma secção de aplicações. Para obter mais informações, consulte [Criar e ler mensagens IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct).

Nas seguintes mensagens, o módulo Live Video Analytics define as propriedades da aplicação e o conteúdo do corpo.

### <a name="mediasessionestablished-event"></a>Evento mediaSessionEstablished

Quando um gráfico de mídia é instantâneo, o nó de origem RTSP tenta ligar-se ao servidor RTSP que funciona no recipiente rtspsim-live555. Se a ligação for bem sucedida, o seguinte evento é impresso. O tipo de evento é Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished.

```
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1599412849822466 1 IN IP4 172.18.0.3\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/t2.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-78.357\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/t2.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=42C01F;sprop-parameter-sets=Z0LAH9kAUAW6EAAAAwAQAAADAwDxgySA,aMuBcsg=\r\na=control:track1\r\nm=audio 0 RTP/AVP 97\r\nc=IN IP4 0.0.0.0\r\nb=AS:96\r\na=rtpmap:97 MPEG4-GENERIC/44100/2\r\na=fmtp:97 streamtype=5;profile-level-id=1;mode=AAC-hbr;sizelength=13;indexlength=3;indexdeltalength=3;config=121056E500\r\na=control:track2\r\n"
  },
  "applicationProperties": {
    "topic": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/resourceGroups/lvaavi_0827/providers/microsoft.media/mediaservices/lvasampleulf2rv2x5msy2",
    "subject": "/graphInstances/ GRAPHINSTANCENAMEHERE /sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-09-06T17:20:49.823Z",
    "dataVersion": "1.0"
  }
```

Nesta mensagem, note estes detalhes:

* A mensagem é um evento de diagnóstico. MediaSessionEstablished indica que o nó de origem RTSP (o sujeito) ligado ao simulador RTSP e começou a receber um feed ao vivo (simulado).
* Nas aplicações, o sujeito indica que a mensagem foi gerada a partir do nó de origem RTSP no gráfico de mídia.
* Nas aplicaçõesProperties, o eventType indica que este evento é um evento de diagnóstico.
* O eventTime indica a hora em que o evento ocorreu.
* O corpo contém dados sobre o evento de diagnóstico. Neste caso, os dados compreendem os detalhes do [Protocolo de Descrição da Sessão (SDP).](https://en.wikipedia.org/wiki/Session_Description_Protocol)

### <a name="inference-event"></a>Evento de inferência

O nó do processador de extensão HTTP recebe resultados de inferência do recipiente Visão Personalizada e emite os resultados através do nó de lavatório IoT Hub como eventos de inferência.

```
{
  "body": {
    "created": "2020-05-18T01:08:34.483Z",
    "id": "",
    "iteration": "",
    "predictions": [
      {
        "boundingBox": {
          "height": 0.06219418,
          "left": 0.14977954,
          "top": 0.65847444,
          "width": 0.01879117
        },
        "probability": 0.69806677,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.1148454,
          "left": 0.77430711,
          "top": 0.54488315,
          "width": 0.11315252
        },
        "probability": 0.29394844,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.03187029,
          "left": 0.62644471,
          "top": 0.59640052,
          "width": 0.01582896
        },
        "probability": 0.14435098,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.11421723,
          "left": 0.72737214,
          "top": 0.55907888,
          "width": 0.12627538
        },
        "probability": 0.1141128,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.04717135,
          "left": 0.66516746,
          "top": 0.34617995,
          "width": 0.03802613
        },
        "probability": 0.10461298,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.20650843,
          "left": 0.56349564,
          "top": 0.51482571,
          "width": 0.35045707
        },
        "probability": 0.10056595,
        "tagId": 0,
        "tagName": "delivery truck"
      }
    ],
    "project": ""
  },
  "applicationProperties": {
    "topic": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/resourceGroups/lsravi/providers/microsoft.media/mediaservices/lvasamplerc3he6a7uhire",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/httpExtension",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-05-18T01:08:34.038Z"
  }
}
```

Note o seguinte nas mensagens acima:

* O sujeito na aplicaçãoDeproperias refere o nó no MediaGraph a partir do qual a mensagem foi gerada. Neste caso, a mensagem é originária do processador De extensão http.
* O eventType na aplicaçãoProperties indica que se trata de um evento de Inferência analítica.
* O eventTime indica a hora em que o evento ocorreu.
* "body" contém dados sobre o evento de análise. Neste caso, o evento é um evento de Inferência e, portanto, o corpo contém uma série de inferências chamadas "previsões".
* A secção "previsões" contém uma lista de previsões onde o caminhão de entrega de brinquedos (tag=caminhão de entrega) é encontrado na moldura. Como se lembraria, o caminhão de entrega é a etiqueta personalizada que forneceu ao seu modelo personalizado treinado para o caminhão de brinquedo e o modelo está a inferenizar e identificar o caminhão de brinquedo no vídeo de entrada com diferentes pontuações de confiança de probabilidade.

## <a name="clean-up-resources"></a>Limpar os recursos

Se pretende experimentar os outros tutoriais ou quickstarts, deve manter os recursos criados. Caso contrário, vá ao portal Azure, navegue pelos seus grupos de recursos, selecione o grupo de recursos sob o qual executou este tutorial e elimine todos os recursos.

## <a name="next-steps"></a>Passos seguintes

Rever desafios adicionais para utilizadores avançados:

* Utilize uma [câmara IP](https://en.wikipedia.org/wiki/IP_camera) que tenha suporte para RTSP em vez de utilizar o simulador RTSP. Pode pesquisar por câmaras IP que suportem RTSP na página de produtos [conformantes ONVIF.](https://www.onvif.org/conformant-products/) Procure dispositivos em conformidade com os perfis G, S ou T.
* Utilize um dispositivo Linux AMD64 ou x64 em vez de um VM Azure Linux. Este dispositivo deve estar na mesma rede que a câmara IP. Pode seguir as instruções no [tempo de funcionamento do Azure IoT Edge no Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux). 

Em seguida, registe o dispositivo com o Azure IoT Hub seguindo as instruções no [Implementar o seu primeiro módulo IoT Edge num dispositivo Linux virtual](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).

