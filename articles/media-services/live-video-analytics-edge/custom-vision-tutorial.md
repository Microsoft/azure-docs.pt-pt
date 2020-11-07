---
title: Analise o vídeo ao vivo com o Live Video Analytics no IoT Edge e Azure Custom Vision
description: Aprenda a usar a Azure Custom Vision para construir um modelo contentorizado que possa detetar um caminhão de brinquedo e usar a capacidade de extensibilidade da IA do Azure Live Video Analytics no Azure IoT Edge para implantar o modelo na borda para detetar camiões de brinquedo a partir de um stream de vídeo ao vivo.
ms.topic: tutorial
ms.date: 09/08/2020
zone_pivot_groups: ams-lva-edge-programming-languages
ms.openlocfilehash: 685aab603b2589a97b4c80ef0f8c5860617f1147
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358320"
---
# <a name="tutorial-analyze-live-video-with-live-video-analytics-on-iot-edge-and-azure-custom-vision"></a>Tutorial: Analise vídeo ao vivo com vídeo ao vivo analíticos em IoT Edge e Azure Custom Vision

Neste tutorial, você vai aprender a usar a Azure [Custom Vision](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/) para construir um modelo contentorizado que pode detetar um caminhão de brinquedo e usar a capacidade de [extensibilidade](analyze-live-video-concept.md#analyzing-video-using-a-custom-vision-model) da IA do Azure Live Video Analytics em Azure IoT Edge para implantar o modelo na borda para detetar camiões de brinquedo a partir de um fluxo de vídeo ao vivo.

Vamos mostrar-lhe como reunir o poder da Visão Personalizada para construir e treinar um modelo de visão computacional, carregando e rotulando algumas imagens. Não precisa de conhecimentos sobre ciência de dados, aprendizagem automática ou IA. Você também vai aprender sobre as capacidades do Live Video Analytics para facilmente implementar um modelo personalizado como um recipiente no borda e analisar um feed de vídeo simulado ao vivo.

::: zone pivot="programming-language-csharp"
[!INCLUDE [header](includes/custom-vision-tutorial/csharp/header.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [header](includes/custom-vision-tutorial/python/header.md)]
::: zone-end

Este tutorial mostra-lhe como:

> [!div class="checklist"]
> * Criar os recursos relevantes.
> * Construa um modelo de Visão Personalizada na nuvem para detetar camiões de brinquedo e implantá-lo na borda.
> * Crie e implemente um gráfico de mídia com uma extensão HTTP a um modelo de Visão Personalizada.
> * Executar o código de amostra.
> * Examine e interprete os resultados.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Pré-leitura sugerida  

Leia os seguintes artigos antes de começar:

* [Análise de vídeo ao vivo na visão geral do IoT Edge](overview.md)
* [Visão Personalizada Azure](../../cognitive-services/custom-vision-service/overview.md)
* [Análise de vídeo ao vivo na terminologia IoT Edge](terminology.md)
* [Conceitos de gráficos de mídia](media-graph-concept.md)
* [Live Video Analytics sem gravação de vídeo](analyze-live-video-concept.md)
* [Executando vídeo ao vivo analytics com o seu próprio modelo](use-your-model-quickstart.md)
* [Tutorial: Desenvolvimento de um módulo IoT Edge](../../iot-edge/tutorial-develop-for-linux.md)
* [Como editar a implementação.*.template.jsem](https://github.com/microsoft/vscode-azure-iot-edge/wiki/How-to-edit-deployment.*.template.json)

## <a name="prerequisites"></a>Pré-requisitos


::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/custom-vision-tutorial/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/custom-vision-tutorial/python/prerequisites.md)]
::: zone-end
## <a name="review-the-sample-video"></a>Reveja o vídeo da amostra


Este tutorial usa um ficheiro [de vídeo de inferência de carro de brinquedo](https://lvamedia.blob.core.windows.net/public/t2.mkv) para simular um fluxo ao vivo. Pode examinar o vídeo através de uma aplicação como o [leitor de mídia VLC](https://www.videolan.org/vlc/). Selecione **Ctrl+N** , e, em seguida, cole um link para o [vídeo de inferência](https://lvamedia.blob.core.windows.net/public/t2.mkv) do carro de brinquedo para iniciar a reprodução. Ao ver o vídeo, note que no marcador de 36 segundos um caminhão de brinquedo aparece no vídeo. O modelo personalizado foi treinado para detetar este caminhão de brinquedo específico. Neste tutorial, você usará live video analytics no IoT Edge para detetar tais caminhões de brinquedo e publicar eventos de inferência associados ao hub IoT Edge.

## <a name="overview"></a>Descrição geral

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/topology-custom-vision.svg" alt-text="Diagrama que mostra uma visão geral personalizada.":::

Este diagrama mostra como os sinais fluem neste tutorial. Um [módulo de borda](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simula uma câmara IP que hospeda um servidor Real-Time de Protocolo de Streaming (RTSP). Um nó [de origem RTSP](media-graph-concept.md#rtsp-source) puxa o feed de vídeo deste servidor e envia quadros de vídeo para o nó do [processador do processador da taxa de fotogramas.](media-graph-concept.md#frame-rate-filter-processor) Este processador limita a taxa de fotogramas do fluxo de vídeo que atinge o nó [do processador de extensão HTTP.](media-graph-concept.md#http-extension-processor)

O nó de extensão HTTP desempenha o papel de um representante. Converte os quadros de vídeo para o tipo de imagem especificado. Em seguida, transmite a imagem sobre REST para outro módulo de borda que executa um modelo de IA atrás de um ponto final HTTP. Neste exemplo, este módulo de borda é o modelo de detetor de caminhões de brinquedo construído utilizando a Visão Personalizada. O nó do processador de extensão HTTP recolhe os resultados da deteção e publica eventos no nó [de pia Azure IoT Hub.](media-graph-concept.md#iot-hub-message-sink) O nó envia então esses eventos para o [hub IoT Edge.](../../iot-edge/iot-edge-glossary.md#iot-edge-hub)

## <a name="build-and-deploy-a-custom-vision-toy-detection-model"></a>Construa e implemente um modelo de deteção de brinquedos de visão personalizada 

Como o nome Custom Vision sugere, pode usá-lo para construir o seu próprio detetor de objetos personalizado ou classificador na nuvem. Fornece uma interface simples, fácil de usar e intuitiva para construir modelos de Visão Personalizada que podem ser implantados na nuvem ou na borda através de contentores.

Para construir um detetor de camiões de brinquedo, siga os passos em [Quickstart: Construa um detetor de objetos com o site Custom Vision](../../cognitive-services/custom-vision-service/get-started-build-detector.md).

Notas adicionais:
 
* Para este tutorial, não utilize as imagens de amostra fornecidas na [secção Pré-requisitos](../../cognitive-services/custom-vision-service/get-started-build-detector.md#prerequisites)do artigo quickstart . Em vez disso, usamos um certo conjunto de imagens para construir um modelo de detetor de brinquedos Custom Vision. Use [estas imagens](https://lvamedia.blob.core.windows.net/public/ToyCarTrainingImages.zip) quando lhe for pedido que [escolha as suas imagens de treino](../../cognitive-services/custom-vision-service/get-started-build-detector.md#choose-training-images) no arranque rápido.
* Na secção de imagem de marcação do arranque rápido, certifique-se de que está a marcar o caminhão de brinquedo visto na imagem com a etiqueta "caminhão de entrega".

Depois de terminar, pode exportar o modelo para um recipiente Docker utilizando o botão **Exportação** no **separador Performance.** Certifique-se de que escolhe o Linux como tipo de plataforma de contentores. Esta é a plataforma em que o contentor vai funcionar. A máquina em que descarrega o recipiente pode ser o Windows ou o Linux. As instruções que se seguem basearam-se no ficheiro do contentor descarregado numa máquina Windows.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/docker-file.png" alt-text="Ecrã que mostra Dockerfile selecionado.":::
 
1. Você deve ter um ficheiro zip descarregado na sua máquina local chamado `<projectname>.DockerFile.Linux.zip` . 
1. Verifique se tem o Docker instalado. Caso contrário, instale [o Docker](https://docs.docker.com/get-docker/) para o seu ambiente de trabalho do Windows.
1. Desaperte o ficheiro descarregado num local à sua escolha. Utilize a linha de comando para ir para o diretório de pastas desapertado.
    
    Execute os seguintes comandos:
    
    1. `docker build -t cvtruck` 
    
        Este comando descarrega muitos pacotes, constrói a imagem do Docker, e marca-a como `cvtruck:latest` .
    
        > [!NOTE]
        > Se for bem sucedido, deverá ver as seguintes mensagens: `Successfully built <docker image id>` e `Successfully tagged cvtruck:latest` . Se o comando de construção falhar, tente de novo. Às vezes, os pacotes de dependência não descarregam da primeira vez.
    1. `docker  image ls`

        Este comando verifica se a nova imagem está no seu registo local.
    1. `docker run -p 127.0.0.1:80:80 -d cvtruck`
    
        Este comando deve publicar a porta exposta do Docker (80) na porta da sua máquina local (80).
    1. `docker container ls`
    
        Este comando verifica os mapeamentos da porta e se o contentor Docker estiver a funcionar com sucesso na sua máquina. A saída deve ser algo como:

        ```
        CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                      NAMES
        8b7505398367        cvtruck             "/bin/sh -c 'python …"   13 hours ago        Up 25 seconds       127.0.0.1:80->80/tcp   practical_cohen
        ```
      1. `curl -X POST http://127.0.0.1:80/image -F imageData=@<path to any image file that has the toy delivery truck in it>`
            
            Este comando testa o contentor na máquina local. Se a imagem tiver o mesmo caminhão de entrega que treinamos o modelo, a saída deve ser algo como o exemplo seguinte. Sugere que o camião de entrega foi detetado com 90,12% de probabilidade.
    
            ```
            {"created":"2020-03-20T07:10:47.827673","id":"","iteration":"","predictions":[{"boundingBox":{"height":0.66167289,"left":-0.03923762,"top":0.12781593,"width":0.70003178},"probability":0.90128148,"tagId":0,"tagName":"delivery truck"},{"boundingBox":{"height":0.63733053,"left":0.25220079,"top":0.0876643,"width":0.53331227},"probability":0.59745145,"tagId":0,"tagName":"delivery truck"}],"project":""}
            ```

<!--## Create and deploy a media graph with http extension to custom vision model-->

## <a name="examine-the-sample-files"></a>Examinar os ficheiros de amostras


::: zone pivot="programming-language-csharp"
[!INCLUDE [examine-sample-files](includes/custom-vision-tutorial/csharp/examine-sample-files.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [examine-sample-files](includes/custom-vision-tutorial/python/examine-sample-files.md)]
::: zone-end

## <a name="generate-and-deploy-the-deployment-manifest"></a>Gerar e implementar o manifesto de implantação

1. No Código do Estúdio Visual, aceda a src/cloud-to-device-console-app/operations.js.

1. Em `GraphTopologySet` , assegurar que o seguinte é verdadeiro:<br/>`"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtension/topology.json"`
1. Em `GraphInstanceSet` , assegurar:
    1. `"topologyName" : "InferencingWithHttpExtension"`
    1. Adicione o seguinte à parte superior da matriz de parâmetros: `{"name": "inferencingUrl","value": "http://cv:80/image"},`
    1. Altere o valor do `rtspUrl` parâmetro para `"rtsp://rtspsim:554/media/t2.mkv"` .
1. Em `GraphTopologyDelete` , assegurar `"name": "InferencingWithHttpExtension"` .
1. Clique com o botão direito para o src/edge/ deployment.customvision.template.jsno ficheiro e selecione **Generate IoT Edge Deployment Manifest**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/deployment-template-json.png" alt-text="Screenshot que mostra Manifesto de Implantação de Aresta ioT.":::
  
    Esta ação deve criar um ficheiro manifesto na pasta src/edge/config chamada deployment.customvision.amd64.js.
1. Abra o src/edge/ deployment.customvision.template.jsno ficheiro e encontre o `registryCredentials` bloco JSON. Neste bloco, encontrará o endereço do seu registo de contentores Azure juntamente com o seu nome de utilizador e senha.
1. Empurre o recipiente local de visão personalizada para a sua instância de registo do contentor Azure seguindo estes passos na linha de comando:

    1. Inscreva-se no registo executando o seguinte comando:
    
        `docker login <address>`
    
        Introduza o nome de utilizador e a palavra-passe quando solicitado a autenticação.
        
        > [!NOTE]
        > A senha não está visível na linha de comando.
    1. Marque a sua imagem utilizando este comando: <br/>`docker tag cvtruck   <address>/cvtruck`.
    1. Empurre a sua imagem utilizando este comando: <br/>`docker push <address>/cvtruck`.

        Se for bem sucedido, deverá ver `Pushed` na linha de comando juntamente com a SHA para a imagem.
    1. Também pode confirmar verificando a sua instância de Registo de Contentores Azure no portal Azure. Aqui você verá o nome do repositório junto com a etiqueta.
1. Desagre a cadeia de ligação IoT Hub selecionando o ícone **Mais ações** ao lado do painel **AZURE IOT HUB** no canto inferior esquerdo. Pode copiar o fio do appsettings.jsficheiro. (Aqui está outra abordagem recomendada para garantir que você tem o hub IoT adequado configurado dentro do Código do Estúdio Visual através do [comando Select IoT Hub](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub).)

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/connection-string.png" alt-text="Screenshot que mostra set IoT Hub Connection String.":::
1. Em seguida, clique à direita src/edge/config/ deployment.customvision.amd64.jsligado e selecione **Criar Implementação para dispositivo único**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/deployment-amd64-json.png" alt-text="Screenshot que mostra criar implementação para dispositivo único.":::
1. Em seguida, ser-lhe-á pedido que selecione um dispositivo IoT Hub. Selecione **o dispositivo de amostra de Lva** da lista de drop-down.
1. Em cerca de 30 segundos, refresque o hub Azure IoT na secção inferior esquerda. Deve ter o dispositivo de borda com os seguintes módulos implantados:

    * O módulo Live Video Analytics no IoT Edge denominado `lvaEdge` .
    * Um módulo chamado `rtspsim` , que simula um servidor RTSP que funciona como a fonte de um feed de vídeo ao vivo.
    * Um módulo chamado `cv` , que como o nome sugere é o modelo de deteção de caminhões de brinquedo Visão Personalizada que aplica Visão Personalizada às imagens e devolve vários tipos de tags. (O nosso modelo foi treinado apenas com uma etiqueta, caminhão de entrega.)

## <a name="prepare-for-monitoring-events"></a>Preparar para eventos de monitorização

Clique com o botão direito no dispositivo Live Video Analytics e selecione **Start Monitoring Built-in Event Endpoint**. Precisa deste passo para monitorizar os eventos do IoT Hub na janela **OUTPUT** do Código do Estúdio Visual.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/start-monitoring.png" alt-text="Screenshot que mostra Start Monitoring Built-in Event Endpoint.":::

## <a name="run-the-sample-program"></a>Executar o programa de amostragem

Se abrir a topologia do gráfico para este tutorial num browser, verá que o valor foi `inferencingUrl` definido para `http://cv:80/image` . Esta definição significa que o servidor de inferência retornará os resultados após a deteção de camiões de brinquedo, se houver, no vídeo ao vivo.

1. No Código do Estúdio Visual, abra o **separador Extensões** (ou selecione **Ctrl+Shift+X)** e procure por Azure IoT Hub.
1. Clique com o botão direito e selecione **Definições de extensão**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Screenshot que mostra definições de extensão.":::
1. Procure e ative **a Mensagem Verbose do Show**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Screenshot que mostra Mostrar Mensagem Verbose.":::
1. Para iniciar uma sessão de depuragem, selecione a tecla **F5.** Vê mensagens impressas na janela **TERMINAL.**
1. A operations.jsno código começa com chamadas para os métodos diretos `GraphTopologyList` e `GraphInstanceList` . Se tiver limpo os recursos depois de ter concluído os quickstarts anteriores, este processo devolverá listas vazias e, em seguida, fará uma pausa. Para continuar, selecione a tecla **'Entrar'.**
    
   A janela **TERMINAL** mostra o próximo conjunto de chamadas de métodos diretos:
    
   * Uma chamada para `GraphTopologySet` que usa o `topologyUrl` anterior.
   * Uma chamada para `GraphInstanceSet` o seguinte corpo:
        
   ```
        {
          "@apiVersion": "1.0",
          "name": "Sample-Graph-1",
          "properties": {
            "topologyName": "CustomVisionWithHttpExtension",
            "description": "Sample graph description",
            "parameters": [
              { 
                "name": "inferencingUrl",
                "value": "http://cv:80/image"
              },
              {
                "name": "rtspUrl",
                "value": "rtsp://rtspsim:554/media/t2.mkv"
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
    
1. A saída na janela **TERMINAL** para numa **Entrada de Imprensa para continuar a** ser solicitada. Não selecione **Enter** ainda. Percorra para ver as cargas de resposta JSON para os métodos diretos que invocou.
1. Mude para a janela **OUTPUT** no Código do Estúdio Visual. Vê mensagens que o modusão IoT Edge está a enviar para o hub IoT. A secção seguinte deste tutorial discute estas mensagens.
1. O gráfico mediático continua a correr e a imprimir resultados. O simulador RTSP continua a dar a volta ao vídeo de origem. Para parar o gráfico de mídia, volte à janela **TERMINAL** e selecione **Enter**.
A próxima série de chamadas limpa recursos:
    
   * Uma chamada para `GraphInstanceDeactivate` desativar a instância do gráfico.
   * Uma chamada para `GraphInstanceDelete` apagar o caso.
   * Uma chamada para `GraphTopologyDelete` apagar a topologia.
   * Uma chamada final para `GraphTopologyList` mostrar que a lista está vazia.
    
## <a name="interpret-the-results"></a>Interpretar os resultados

Quando executou o gráfico de mídia, os resultados do nó do processador de extensão HTTP passam pelo nó da pia IoT Hub para o hub IoT. As mensagens que vê na janela **OUTPUT** contêm uma secção do corpo e uma `applicationProperties` secção. Para obter mais informações, consulte [Criar e ler mensagens IoT Hub](../../iot-hub/iot-hub-devguide-messages-construct.md).

Nas seguintes mensagens, o módulo Live Video Analytics define as propriedades da aplicação e o conteúdo do corpo.

### <a name="mediasessionestablished-event"></a>Evento mediaSessionEstablished

Quando um gráfico de mídia é instantâneo, o nó de origem RTSP tenta ligar-se ao servidor RTSP que funciona no recipiente rtspsim-live555. Se a ligação for bem sucedida, o seguinte evento é impresso. O tipo de evento é `Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished` .

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

* A mensagem é um evento de diagnóstico. `MediaSessionEstablished` indica que o nó de origem RTSP (o sujeito) ligado ao simulador RTSP e começou a receber um feed simulado ao vivo.
* Em `applicationProperties` , indica que a `subject` mensagem foi gerada a partir do nó de origem RTSP no gráfico de mídia.
* Em `applicationProperties` , o tipo de evento indica que este evento é um evento de diagnóstico.
* A hora do evento indica a hora em que o evento ocorreu.
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

Note as seguintes informações nas mensagens anteriores:

* O sujeito em `applicationProperties` referências ao nó no MediaGraph a partir do qual a mensagem foi gerada. Neste caso, a mensagem é originária do processador de extensão HTTP.
* O tipo de evento `applicationProperties` indica que este é um evento de inferência analítica.
* A hora do evento indica a hora em que o evento ocorreu.
* O corpo contém dados sobre o evento de análise. Neste caso, o evento é um evento de inferência, por isso o corpo contém uma série de inferências chamadas previsões.
* A secção de previsões contém uma lista de previsões em que um caminhão de entrega de brinquedos (tag é "caminhão de entrega") é encontrado na moldura. Como se lembra, "caminhão de entrega" é a etiqueta personalizada que forneceu ao seu modelo personalizado treinado para o caminhão de brinquedo. O modelo inferências e identifica o caminhão de brinquedo no vídeo de entrada com diferentes pontuações de confiança de probabilidade.

## <a name="clean-up-resources"></a>Limpar recursos

Se pretende experimentar os outros tutoriais ou quickstarts, a guarde os recursos que criou. Caso contrário, vá ao portal Azure, navegue pelos seus grupos de recursos, selecione o grupo de recursos sob o qual executou este tutorial e elimine todos os recursos.

## <a name="next-steps"></a>Passos seguintes

Rever desafios adicionais para utilizadores avançados:

* Utilize uma [câmara IP](https://en.wikipedia.org/wiki/IP_camera) que tenha suporte para RTSP em vez de utilizar o simulador RTSP. Pode pesquisar por câmaras IP que suportem RTSP na página de produtos [conformantes ONVIF.](https://www.onvif.org/conformant-products/) Procure dispositivos em conformidade com os perfis G, S ou T.
* Utilize um dispositivo Linux AMD64 ou x64 em vez de um VM Azure Linux. Este dispositivo deve estar na mesma rede que a câmara IP. Pode seguir as instruções no [tempo de funcionamento do Azure IoT Edge no Linux](../../iot-edge/how-to-install-iot-edge-linux.md).

Em seguida, registe o dispositivo com o Azure IoT Hub seguindo as instruções no [Implementar o seu primeiro módulo IoT Edge num dispositivo Linux virtual](../../iot-edge/quickstart-linux.md).
