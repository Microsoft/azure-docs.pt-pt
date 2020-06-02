---
title: Detete movimento, grave vídeo em dispositivos de borda - Azure
description: Este quickstart mostra-lhe como usar o Live Video Analytics no IoT Edge para analisar o feed de vídeo ao vivo a partir de uma câmara IP (simulada), detetar se algum movimento está presente e, em caso afirmativo, gravar um videoclip MP4 para o sistema de ficheiros local no dispositivo de borda.
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: d824870ea95922bbbdbf01cf2c95692522936f85
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/01/2020
ms.locfileid: "84262081"
---
# <a name="quickstart-detect-motion-record-video-on-edge-devices"></a>Quickstart: Detete movimento, grave vídeo em dispositivos de borda
 
Este quickstart mostra-lhe como usar o Live Video Analytics no IoT Edge para analisar o feed de vídeo ao vivo a partir de uma câmara IP (simulada), detetar se algum movimento está presente e, em caso afirmativo, gravar um videoclip MP4 para o sistema de ficheiros local no dispositivo de borda. Utiliza um Azure VM como um dispositivo IoT Edge e um vídeo simulado ao vivo. Este artigo baseia-se no código de amostra escrito em C#.

Este artigo baseia-se [neste](detect-motion-emit-events-quickstart.md) arranque rápido. 

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Código de Estúdio Visual](https://code.visualstudio.com/) na sua máquina com as seguintes extensões:
    * [Ferramentas do Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1) instalado no seu sistema
* Se ainda não tiver concluído [este](detect-motion-emit-events-quickstart.md) arranque rápido, termine os seguintes passos:
     * [Configurar recursos do Azure](detect-motion-emit-events-quickstart.md#set-up-azure-resources)
     * [Configurar o ambiente de desenvolvimento](detect-motion-emit-events-quickstart.md#set-up-your-development-environment)
     * [Gerar e implementar o manifesto de implantação IoT Edge](detect-motion-emit-events-quickstart.md#generate-and-deploy-the-iot-edge-deployment-manifest)
     * [Preparar para eventos de monitorização](detect-motion-emit-events-quickstart.md#prepare-for-monitoring-events)

> [!TIP]
> Ao instalar as Ferramentas Azure IoT, poderá ser solicitado a instalar o estivador. Sinta-se livre para ignorá-lo.

## <a name="review-the-sample-video"></a>Reveja o vídeo da amostra
Como parte dos passos acima para configurar os recursos Azure, um vídeo (curto) de um estacionamento será copiado para o Linux VM em Azure sendo usado como o dispositivo IoT Edge. Este ficheiro de vídeo será usado para simular um live stream para este tutorial.

Pode utilizar uma aplicação como [VLC Player,](https://www.videolan.org/vlc/)lançá-la, acertar Control+N e colar [esta](https://lvamedia.blob.core.windows.net/public/lots_015.mkv) ligação ao vídeo do parque de estacionamento para iniciar a reprodução. Com cerca de 5 segundos, um carro branco move-se pelo estacionamento.

Quando completar os passos abaixo, terá usado live video analytics no IoT Edge para detetar o movimento do carro, e gravar um videoclip a partir de cerca de 5 segundos.

## <a name="overview"></a>Descrição geral

![descrição geral](./media/quickstarts/overview-qs4.png)

O diagrama acima mostra como os sinais fluem neste arranque rápido. Um módulo de borda (detalhado [aqui)](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)simula uma câmara IP que hospeda um servidor RTSP. Um nó [de fonte RTSP](media-graph-concept.md#rtsp-source) puxa o feed de vídeo deste servidor e envia quadros de vídeo para o nó do [processador de deteção de movimento.](media-graph-concept.md#motion-detection-processor) A fonte RTSP envia os mesmos quadros de vídeo para um nó [do processador do portão de sinal,](media-graph-concept.md#signal-gate-processor) que permanece fechado até ser acionado por um evento.

Quando o processador de deteção de movimentos determina que o movimento está presente no vídeo, envia um evento para o nó do processador do portão de sinal, desencadeando-o. O portão abre-se durante a duração configurada do tempo, enviando molduras de vídeo para o nó da pia do [ficheiro.](media-graph-concept.md#file-sink) Este nó da pia grava o vídeo como um ficheiro MP4 para o sistema de ficheiros local do seu dispositivo de borda, no local configurado.

Neste arranque rápido, você vai:

1. Criar e implementar o gráfico de mídia
1. Interpretar os resultados
1. Limpar recursos

## <a name="examine-and-edit-the-sample-files"></a>Examine e edite os ficheiros de amostra
Como parte dos requisitos prévios, teria descarregado o código de amostra para uma pasta. Lançar Código de Estúdio Visual e abrir a pasta.

1. No Código do Estúdio Visual, navegue por "src/edge". Verá o ficheiro .env que criou juntamente com alguns ficheiros de modelos de implementação
    * O modelo de implantação refere-se ao manifesto de implantação do dispositivo de borda com alguns valores de espaço reservado. O ficheiro .env tem os valores para essas variáveis.
1. Em seguida, navegue para a pasta "src/cloud-to-device-console-app". Aqui verá o ficheiro appsettings.json que criou juntamente com alguns outros ficheiros:
    * c2d-console-app.csproj - Este é o ficheiro do projeto para Visual Studio Code
    * operations.json - Este ficheiro irá listar as diferentes operações que gostaria que o programa fosse executado
    * Program.cs - Este é o código do programa de amostra, que faz o seguinte:

        * Carrega as definições da aplicação
        * Invoca métodos diretos expostos pelo live video analytics no módulo IoT Edge. Pode utilizar o módulo para analisar streams de vídeo ao vivo invocando os seus [métodos diretos](direct-methods.md) 
        * Pausas para examinar a saída do programa na janela TERMINAL e os eventos gerados pelo módulo na janela OUTPUT
        * Invoca métodos diretos para limpar recursos   

1. Faça as seguintes edições para o ficheiro operations.json
    * Altere a ligação para a topologia do gráfico:`"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-motion-files/topology.json"`
    * Em GraphInstanceSet, edite o nome da topologia do gráfico para corresponder ao valor no link acima`"topologyName" : "EVRToFilesOnMotionDetection"`
    * Edite também o URL RTSP para apontar para o ficheiro de vídeo desejado`"value": "rtsp://rtspsim:554/media/lots_015.mkv"`
    * Sob GraphTopologyDelete, edite o nome`"name": "EVRToFilesOnMotionDetection"`

## <a name="review---check-status-of-the-modules"></a>Revisão - verificar o estado dos módulos
No [Passo manifesto de implantação do IoT Edge,](detect-motion-emit-events-quickstart.md#generate-and-deploy-the-iot-edge-deployment-manifest) no Código do Estúdio Visual, se expandir o nó "Lva-sample-device" sob AZURE IOT HUB (na secção inferior esquerda) deverá ver os seguintes módulos implantados

    1. O módulo Live Video Analytics, nomeado como "LvaEdge"
    1. Um módulo chamado "rtspsim" que simula um Servidor RTSP, agindo como a fonte de um feed de vídeo ao vivo

        ![Módulos](./media/quickstarts/lva-sample-device-node.png)


## <a name="review---prepare-for-monitoring-events"></a>Review - preparar para os eventos de monitorização
Verifique se completou os passos para [preparar eventos de monitorização](detect-motion-emit-events-quickstart.md#prepare-for-monitoring-events)

![Iniciar monitorização do ponto final do evento incorporado](./media/quickstarts/start-monitoring-iothub-events.png)

## <a name="run-the-sample-program"></a>Executar o programa de amostragem

1. Inicie uma sessão de depurar (acerte F5). Começará a ver algumas mensagens impressas na janela TERMINAL.
1. O operations.json começa com chamadas para os métodos diretos GraphTopologyList e GraphInstanceList. Se tiver limpo recursos após o início rápido anterior, isto devolverá listas vazias e, em seguida, fará uma pausa para que você possa entrar
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
     * Uma chamada para o GraphInstanceActivate para iniciar a instância do gráfico e iniciar o fluxo de vídeo
     * Uma segunda chamada para o GraphInstanceList para mostrar que a instância do gráfico está, de facto, no estado de execução
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

* A mensagem é um evento de Diagnóstico, MediaSessionEstablished, indica que o nó de origem RTSP (o sujeito) foi capaz de estabelecer a ligação com o simulador RTSP, e começar a receber um feed ao vivo (simulado).
* O "sujeito" na aplicaçãoDeproperias refere o nó na topologia do gráfico a partir do qual a mensagem foi gerada. Neste caso, a mensagem é originária do nó de origem RTSP.
* "eventType" na aplicaçãoProperties indica que este é um evento de Diagnóstico.
* "eventTime" indica a hora em que o evento ocorreu.
* "body" contém dados sobre o evento de diagnóstico, que, neste caso, é o [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) detalhes.


## <a name="recording-started-event"></a>Início do evento de gravação

Como explicado [aqui](#overview), quando o movimento é detetado, o nó do processador do portão de sinal é ativado e o nó da pia do ficheiro no gráfico de mídia começa a escrever um ficheiro MP4. O nó da pia do ficheiro envia um evento operacional. O tipo está definido para "movimento" para indicar que é um resultado do Processador de Deteção de Movimento, e o eventTime diz-lhe a que altura ocorreu o movimento (UTC). Abaixo está um exemplo:

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

* "sujeito" na aplicaçãoDeproperties refere o nó no gráfico de mídia a partir do qual a mensagem foi gerada. Neste caso, a mensagem é originária do nó da pia do ficheiro.
* "eventType" na aplicaçãoProperties indica que se trata de um evento Operacional.
* "eventTime" indica a hora em que o evento ocorreu. Note que este 5-6 segundos após o MediaSessionEstablished e o vídeo começa a fluir. Isto corresponde à marca de 5-6 segundos quando o [carro começou a mover-se para](#review-the-sample-video) o estacionamento
* "body" contém dados sobre o evento operacional, que neste caso são dados de "outputType" e "outputLocation".
* "OutputType" indica que esta informação é sobre o caminho do ficheiro
* "outputLocation" fornece a localização do ficheiro MP4, a partir do módulo de borda

## <a name="recording-stopped-and-available-events"></a>Gravação de eventos parados e disponíveis

Se examinar as propriedades do nó do processador do portão de sinal na topologia do [gráfico,](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-motion-files/topology.json)verá que os tempos de ativação foram definidos para 5 segundos. Então, cerca de 5 segundos após o evento RecordingStarted ser recebido, você receberá um
* RecordingS evento coberto, indicando que a gravação parou
* Gravação Evento disponível, indicando que o ficheiro MP4 pode agora ser usado para visualização

Os dois eventos são normalmente emitidos com segundos um do outro.

### <a name="playing-back-the-mp4-clip"></a>Reproduzir o clipe MP4

1. Os ficheiros MP4 são escritos para um diretório no dispositivo de borda que configuraste no ficheiro .env através desta tecla - OUTPUT_VIDEO_FOLDER_ON_DEVICE. Se o deixou ao valor predefinido, os resultados devem estar em /home/lvaadmin/samples/output/
1. Vá ao seu grupo de recursos, encontre o VM e conecte-se usando Bastion

    ![Grupo de recursos](./media/quickstarts/resource-group.png)
 
    ![VM](./media/quickstarts/virtual-machine.png)
1. Uma vez assinado (utilizando credenciais que foram geradas durante [este](detect-motion-emit-events-quickstart.md#set-up-azure-resources) passo), na solicitação de comando, vá ao diretório relevante (predefinição: /home/lvaadmin/samples/output) e deverá ver os ficheiros MP4 lá. Pode [passar os ficheiros](https://docs.microsoft.com/azure/virtual-machines/linux/copy-files-to-linux-vm-using-scp) para a máquina local e reproduzi-los através do [leitor de VLC](https://www.videolan.org/vlc/) ou de qualquer outro leitor de MP4.

    ![Saída](./media/quickstarts/samples-output.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se pretende experimentar os outros quickstarts, deve manter os recursos criados. Caso contrário, vá ao portal Azure, navegue pelos seus grupos de recursos, selecione o grupo de recursos sob o qual executou este arranque rápido e elimine todos os recursos.

## <a name="next-steps"></a>Passos seguintes

* Execute o [Run Live Video Analytics com o seu próprio modelo](use-your-model-quickstart.md) quickstart, que mostra como aplicar IA em feeds de vídeo ao vivo.
* Rever desafios adicionais para utilizadores avançados:

    * Utilize uma [câmara IP](https://en.wikipedia.org/wiki/IP_camera) com suporte para RTSP em vez de utilizar o simulador RTSP. Pode pesquisar por câmaras IP com suporte RTSP na página de [produtos conformantes ONVIF,](https://en.wikipedia.org/wiki/IP_camera) procurando dispositivos que estejam em conformidade com os perfis G, S ou T.
    * Utilize um dispositivo Linux AMD64 ou X64 (vs. utilizando um VM Azure Linux). Este dispositivo deve estar na mesma rede que a câmara IP. Pode seguir as instruções no [tempo de funcionamento do Azure IoT Edge no Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) e, em seguida, seguir as instruções no [módulo IoT Edge para um dispositivo Linux virtual](https://docs.microsoft.com/azure/iot-edge/quickstart-linux) para registar o dispositivo com o Azure IoT Hub.
