---
title: Detete o movimento & gravar vídeo em dispositivos de borda - Azure
description: Este quickstart mostra-lhe como usar o Live Video Analytics no IoT Edge para analisar o feed de vídeo ao vivo a partir de uma câmara IP (simulada), detetar se algum movimento está presente e, em caso afirmativo, gravar um videoclip MP4 para o sistema de ficheiros local no dispositivo de borda.
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: 14dcc7b298244a1d53a9b820c641ea87c4f9a016
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87091866"
---
# <a name="quickstart-detect-motion-and-record-video-on-edge-devices"></a>Quickstart: Detetar movimento e gravar vídeo em dispositivos de borda
 
Este quickstart mostra-lhe como usar o Live Video Analytics no IoT Edge para analisar o feed de vídeo ao vivo a partir de uma câmara IP (simulada). Mostra como detetar se algum movimento está presente e, em caso afirmativo, grava um videoclip MP4 para o sistema de ficheiros local no dispositivo de borda. O quickstart usa um Azure VM como um dispositivo IoT Edge e também usa um stream de vídeo simulado ao vivo. 

Este artigo baseia-se no código de amostra escrito em C#. Baseia-se no [movimento Detect e emite eventos](detect-motion-emit-events-quickstart.md) rápidos. 

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita se](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) ainda não tiver uma.
* [Código do Estúdio Visual,](https://code.visualstudio.com/)com as seguintes extensões:
    * [Ferramentas do Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* Se ainda não tiver concluído o [movimento Detect e emitir eventos](detect-motion-emit-events-quickstart.md) de rapidez, siga estes passos:
     1. [Configurar recursos do Azure](detect-motion-emit-events-quickstart.md#set-up-azure-resources)
     1. [Configurar o ambiente de desenvolvimento](detect-motion-emit-events-quickstart.md#set-up-your-development-environment)
     1. [Gerar e implementar o manifesto de implantação IoT Edge](detect-motion-emit-events-quickstart.md#generate-and-deploy-the-deployment-manifest)
     1. [Preparar para monitorizar eventos](detect-motion-emit-events-quickstart.md#prepare-to-monitor-events)

> [!TIP]
> Ao instalar ferramentas Azure IoT, poderá ser solicitado a instalar o Docker. Sinta-se livre para ignorar o pedido.

## <a name="review-the-sample-video"></a>Reveja o vídeo da amostra
Ao configurar os recursos Azure para este quickstart, um pequeno vídeo de um estacionamento é copiado para o Linux VM em Azure que é usado como o dispositivo IoT Edge. Este ficheiro de vídeo será usado para simular um live stream para este tutorial.

Abra uma aplicação como [o leitor de mídia VLC](https://www.videolan.org/vlc/), selecione Ctrl+N e cole [esta ligação](https://lvamedia.blob.core.windows.net/public/lots_015.mkv) ao vídeo do estacionamento para iniciar a reprodução. Com cerca de 5 segundos, um carro branco move-se pelo estacionamento.

Complete os seguintes passos para utilizar o Live Video Analytics no IoT Edge para detetar o movimento do carro e gravar um videoclip a partir da marca de 5 segundos.

## <a name="overview"></a>Descrição geral

![descrição geral](./media/quickstarts/overview-qs4.png)

O diagrama anterior mostra como os sinais fluem neste arranque rápido. [Um módulo de borda](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simula uma câmara IP que acolhe um servidor de Protocolo de Streaming em Tempo Real (RTSP). Um nó [de origem RTSP](media-graph-concept.md#rtsp-source) puxa o feed de vídeo deste servidor e envia quadros de vídeo para o nó do [processador de deteção de movimento.](media-graph-concept.md#motion-detection-processor) A fonte RTSP envia os mesmos quadros de vídeo para um nó [do processador do portão de sinal,](media-graph-concept.md#signal-gate-processor) que permanece fechado até ser desencadeado por um evento.

Quando o processador de deteção de movimento deteta movimento no vídeo, envia um evento para o nó do processador do portão de sinal, desencadeando-o. O portão abre-se durante a duração configurada do tempo, enviando molduras de vídeo para o nó da pia do [ficheiro.](media-graph-concept.md#file-sink) Este nó da pia grava o vídeo como um ficheiro MP4 no sistema de ficheiros local do seu dispositivo de borda. O ficheiro é guardado no local configurado.

Neste arranque rápido, você vai:

1. Criar e implementar o gráfico de mídia.
1. Interprete os resultados.
1. Limpe os recursos.

## <a name="examine-and-edit-the-sample-files"></a>Examine e edite os ficheiros de amostra
Como parte dos pré-requisitos para este arranque rápido, descarregou o código de amostra para uma pasta. Siga estes passos para examinar e editar o código de amostra.

1. No Código do Estúdio Visual, vá ao *src/edge*. Você vê o seu ficheiro *.env* e alguns ficheiros de modelo de implementação.

    O modelo de implantação refere-se ao manifesto de implantação do dispositivo de borda, onde são utilizadas variáveis para algumas propriedades. O ficheiro *.env* inclui os valores para essas variáveis.
1. Aceda à pasta *src/cloud-to-device-app.* Aqui vê-se a *appsettings.jsficheiro* e alguns outros ficheiros:
    * ***c2d-console-app.csproj*** - O ficheiro do projeto para Visual Studio Code.
    * ***operations.jsem*** - A lista de operações que quer que o programa seja executado.
    * ***Program.cs*** - O código do programa de amostra. Este código:

        * Carrega as definições da aplicação.
        * Invoca métodos diretos que o live video analytics no módulo IoT Edge expõe. Pode utilizar o módulo para analisar streams de vídeo ao vivo invocando os seus [métodos diretos.](direct-methods.md) 
        * Pausa para que possa examinar a saída do programa na janela **TERMINAL** e examinar os eventos gerados pelo módulo na janela **OUTPUT.**
        * Invoca métodos diretos para limpar recursos.

1. Editar o *operations.jsno* ficheiro:
    * Altere a ligação para a topologia do gráfico:

        `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-motion-files/topology.json"`
    * Em `GraphInstanceSet` , editar o nome da topologia do gráfico para corresponder ao valor no link anterior:
    
      `"topologyName" : "EVRToFilesOnMotionDetection"`

    * Edite o URL RTSP para apontar para o ficheiro de vídeo:

        `"value": "rtsp://rtspsim:554/media/lots_015.mkv"`

    * Em `GraphTopologyDelete` , editar o nome:

        `"name": "EVRToFilesOnMotionDetection"`

## <a name="review---check-the-modules-status"></a>Revisão - Verifique o estado dos módulos

No [Modo de Geração e implementação do passo manifesto de implantação IoT Edge,](detect-motion-emit-events-quickstart.md#generate-and-deploy-the-deployment-manifest) no Código do Estúdio Visual, expanda o nó **lva-sample-device** sob **AZURE IOT HUB** (na secção inferior esquerda). Deve ver os seguintes módulos implantados:

* O módulo Live Video Analytics, chamado **LvaEdge**
* O **módulo rtspsim,** que simula um servidor RTSP que funciona como a fonte de um feed de vídeo ao vivo

  ![Módulos](./media/quickstarts/lva-sample-device-node.png)


## <a name="review---prepare-for-monitoring-events"></a>Revisão - Preparar para eventos de monitorização
Certifique-se de que completou os passos para [preparar para monitorizar os eventos.](detect-motion-emit-events-quickstart.md#prepare-to-monitor-events)

![Iniciar monitorização do ponto final do evento incorporado](./media/quickstarts/start-monitoring-iothub-events.png)

## <a name="run-the-sample-program"></a>Executar o programa de amostragem

1. Inicie uma sessão de depurar selecionando a tecla F5. A janela **TERMINAL** imprime algumas mensagens.
1. O *operations.jsno* código chama os métodos diretos e `GraphTopologyList` `GraphInstanceList` . Se você limpou os recursos após o início rápido anterior, então este processo irá devolver listas vazias e, em seguida, fazer uma pausa. Selecione a tecla 'Entrar'.

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

     * Uma chamada para `GraphTopologySet` que usa o`topologyUrl` 
     * Uma chamada para `GraphInstanceSet` o seguinte corpo:

         ```
         {
           "@apiVersion": "1.0",
           "name": "Sample-Graph",
           "properties": {
             "topologyName": "EVRToFilesOnMotionDetection",
             "description": "Sample graph description",
             "parameters": [
               {
                 "name": "rtspUrl",
                 "value": "rtsp://rtspsim:554/media/lots_015.mkv"
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
1. A saída na janela **TERMINAL** para em `Press Enter to continue` . Não selecione Enter ainda. Percorra para ver as cargas de resposta do JSON para os métodos diretos que invocou.
1. Mude para a janela **OUTPUT** no Código do Estúdio Visual. Vê as mensagens que o modusão IoT Edge está a enviar para o hub IoT. A secção seguinte deste quickstart discute estas mensagens.

1. O gráfico mediático continua a correr e a imprimir resultados. O simulador RTSP continua a dar a volta ao vídeo de origem. Para parar o gráfico de mídia, volte à janela **TERMINAL** e selecione Enter. 

    A próxima série de chamadas limpa os recursos:
     * Uma chamada para `GraphInstanceDeactivate` desativar a instância do gráfico.
     * Uma chamada para `GraphInstanceDelete` apagar o caso.
     * Uma chamada para `GraphTopologyDelete` apagar a topologia.
     * Uma última chamada para `GraphTopologyList` mostrar que a lista está agora vazia.

## <a name="interpret-results"></a>Interpretar os resultados 
Quando executar o gráfico de mídia, os resultados do nó do processador de processador de movimento passam pelo nó da pia IoT Hub para o hub IoT. As mensagens que vê na janela **OUTPUT** do Código do Estúdio Visual contêm uma `body` secção e uma `applicationProperties` secção. Para obter mais informações, consulte [Criar e ler mensagens IoT Hub](../../iot-hub/iot-hub-devguide-messages-construct.md).

Nas seguintes mensagens, o módulo Live Video Analytics define as propriedades da aplicação e o conteúdo do corpo.

### <a name="mediasessionestablished-event"></a>Evento mediaSessionEstablished

Quando um gráfico de mídia é instantâneo, o nó de origem RTSP tenta ligar-se ao servidor RTSP que funciona no recipiente rtspsim-live555. Se a ligação for bem sucedida, o seguinte evento é impresso.

```
[IoTHubMonitor] [05:37:21 AM] Message received from [lva-sample-device/lvaEdge]:
{  
"body": {
"sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 xxx.xxx.xxx.xxx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets={SPS}\r\na=control:track1\r\n"  
},  
"applicationProperties": {  
    "dataVersion": "1.0",  
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",  
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",  
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",  
    "eventTime": "2020-05-21T05:37:21.398Z",
    }  
}
```

Na saída anterior: 

* A mensagem é um evento de diagnóstico, `MediaSessionEstablished` . Indica que o nó de origem RTSP (o sujeito) estabeleceu uma ligação com o simulador RTSP e começou a receber um feed ao vivo (simulado).
* Em `applicationProperties` , refere o nó na `subject` topologia do gráfico a partir do qual a mensagem foi gerada. Neste caso, a mensagem provém do nó de origem RTSP.
* Em `applicationProperties` , indica que este evento é um evento de `eventType` diagnóstico.
* O `eventTime` valor é o momento em que o evento ocorreu.
* A `body` secção contém dados sobre o evento de diagnóstico. Neste caso, os dados compreendem os detalhes do [Protocolo de Descrição da Sessão (SDP).](https://en.wikipedia.org/wiki/Session_Description_Protocol)

### <a name="recordingstarted-event"></a>Evento "RecordingStarted"

Quando o movimento é detetado, o nó do processador do portão de sinal é ativado e o nó da pia do ficheiro no gráfico de mídia começa a escrever um ficheiro MP4. O nó da pia envia um evento operacional. O `type` conjunto indica que é um resultado do processador de `motion` deteção de movimentos. O `eventTime` valor é o tempo UTC em que a moção ocorreu. Para obter mais informações sobre este processo, consulte a secção ['Vista Geral'](#overview) neste arranque rápido.

Aqui está um exemplo desta mensagem:

```
[IoTHubMonitor] [05:37:27 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "filePath",
    "outputLocation": "/var/media/sampleFilesFromEVR-filesinkOutput-20200521T053726Z.mp4"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",  
    "subject": "/graphInstances/Sample-Graph-1/sinks/fileSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": "2020-05-21T05:37:27.713Z",
    "dataVersion": "1.0"
  }
}
```

Na mensagem anterior: 

* Em `applicationProperties` , refere o nó no gráfico mediático a partir do qual a `subject` mensagem foi gerada. Neste caso, a mensagem provém do nó da pia do ficheiro.
* Em `applicationProperties` , indica que este evento está `eventType` operacional.
* O `eventTime` valor é o momento em que o evento ocorreu. Desta vez é de 5 a 6 segundos depois `MediaSessionEstablished` e depois de o vídeo começar a fluir. Desta vez corresponde à marca de 5 a 6 segundos quando o [carro começou a mover-se para](#review-the-sample-video) o estacionamento.
* A `body` secção contém dados sobre o evento operacional. Neste caso, os dados compreendem `outputType` e `outputLocation` .
* A `outputType` variável indica que esta informação é sobre o caminho do ficheiro.
* O `outputLocation` valor é a localização do ficheiro MP4 no módulo de borda.

### <a name="recordingstopped-and-recordingavailable-events"></a>Gravações Eventos e Registos Disponíveis

Se examinar as propriedades do nó do processador do portão de sinal na topologia do [gráfico,](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-motion-files/topology.json)verá que os tempos de ativação estão definidos para 5 segundos. Então, cerca de 5 segundos após a recebida do `RecordingStarted` evento, você recebe:

* Um `RecordingStopped` evento, indicando que a gravação parou.
* Um `RecordingAvailable` evento, indicando que o ficheiro MP4 pode agora ser usado para visualização.

Os dois eventos são normalmente emitidos em segundos um do outro.

## <a name="play-the-mp4-clip"></a>Toque o clipe MP4

Os ficheiros MP4 são escritos num diretório no dispositivo de borda que configuraste no ficheiro *.env* utilizando a tecla OUTPUT_VIDEO_FOLDER_ON_DEVICE. Se utilizou o valor predefinido, os resultados devem estar na pasta */home/lvaadmin/samples/output/.*

Para reproduzir o clipe MP4:

1. Vá ao seu grupo de recursos, encontre o VM e, em seguida, conecte-se utilizando O Bastião Azure.

    ![Grupo de recursos](./media/quickstarts/resource-group.png)
    
    ![VM](./media/quickstarts/virtual-machine.png)

1. Faça o sômedísmos utilizando as credenciais que foram geradas quando [configurar os seus recursos Azure.](detect-motion-emit-events-quickstart.md#set-up-azure-resources) 
1. No comando, vá ao diretório relevante. A localização predefinida é */home/lvaadmin/samples/output*. Devia ver os ficheiros MP4 no diretório.

    ![Saída](./media/quickstarts/samples-output.png) 

1. Utilize [a Cópia Segura (SCP)](../../virtual-machines/linux/copy-files-to-linux-vm-using-scp.md) para copiar os ficheiros para a sua máquina local. 
1. Reproduz os ficheiros utilizando o [leitor de mídia VLC](https://www.videolan.org/vlc/) ou qualquer outro leitor de MP4.

## <a name="clean-up-resources"></a>Limpar recursos

Se pretende experimentar os outros quickstarts, então mantenha os recursos que criou. Caso contrário, no portal Azure, vá aos seus grupos de recursos, selecione o grupo de recursos onde executou este arranque rápido e, em seguida, elimine todos os recursos.

## <a name="next-steps"></a>Passos seguintes

* Siga o [Run Live Video Analytics com o seu próprio modelo](use-your-model-quickstart.md) quickstart para aplicar IA em feeds de vídeo ao vivo.
* Rever desafios adicionais para utilizadores avançados:

    * Utilize uma [câmara IP](https://en.wikipedia.org/wiki/IP_camera) que suporte o RTSP em vez de utilizar o simulador RTSP. Pode encontrar câmaras IP que suportam RTSP na página de [produtos conformantes ONVIF.](https://www.onvif.org/conformant-products) Procure dispositivos em conformidade com os perfis G, S ou T.
    * Utilize um dispositivo Linux AMD64 ou x64 em vez de utilizar um Linux VM em Azure. Este dispositivo deve estar na mesma rede que a câmara IP. Siga as instruções no [tempo de funcionamento do Azure IoT Edge no Linux](../../iot-edge/how-to-install-iot-edge-linux.md). Em seguida, siga as instruções no [Implementar o seu primeiro módulo IoT Edge num dispositivo Linux virtual](../../iot-edge/quickstart-linux.md) para registar o dispositivo com o Azure IoT Hub.
