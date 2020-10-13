---
title: Gravação de vídeo baseada em eventos para a nuvem e reprodução do tutorial em nuvem - Azure
description: Neste tutorial, você vai aprender a usar Azure Live Video Analytics em Azure IoT Edge para gravar uma gravação de vídeo baseada em eventos para a nuvem e reproduzi-lo de volta da nuvem.
ms.topic: tutorial
ms.date: 05/27/2020
ms.openlocfilehash: a2388a01544d2158e7ca6f1692df07b14ec03a93
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91773557"
---
# <a name="tutorial-event-based-video-recording-to-the-cloud-and-playback-from-the-cloud"></a>Tutorial: Gravação de vídeo baseada em eventos para a nuvem e reprodução da nuvem

Neste tutorial, você vai aprender a usar Azure Live Video Analytics em Azure IoT Edge para gravar seletivamente partes de uma fonte de vídeo ao vivo para a Azure Media Services na nuvem. Este caso de utilização é referido como gravação de vídeo baseada em [eventos](event-based-video-recording-concept.md) (EVR) neste tutorial. Para gravar partes de um vídeo ao vivo, utilizará um modelo de IA de deteção de objetos para procurar objetos no vídeo e gravar vídeos apenas quando um determinado tipo de objeto for detetado. Também aprenderá como reproduzir os videoclips gravados utilizando os Media Services. Esta capacidade é útil para uma variedade de cenários onde há necessidade de manter um arquivo de videoclips de interesse. 

Neste tutorial, irá:

> [!div class="checklist"]
> * Criar os recursos relevantes.
> * Examine o código que executa o EVR.
> * Executar o código de amostra.
> * Examine os resultados e veja o vídeo.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Pré-leitura sugerida  

Leia estes artigos antes de começar:

* [Análise de vídeo ao vivo na visão geral do IoT Edge](overview.md)
* [Análise de vídeo ao vivo na terminologia IoT Edge](terminology.md)
* [Conceitos de gráficos de mídia](media-graph-concept.md) 
* [Gravação de vídeo baseada em eventos](event-based-video-recording-concept.md)
* [Tutorial: Desenvolvimento de um módulo IoT Edge](../../iot-edge/tutorial-develop-for-linux.md)
* [Como editar a implementação.*.template.jsem](https://github.com/microsoft/vscode-azure-iot-edge/wiki/How-to-edit-deployment.*.template.json)
* Secção sobre [como declarar rotas no manifesto de implantação do IoT Edge](../../iot-edge/module-composition.md#declare-routes)

## <a name="prerequisites"></a>Pré-requisitos

Os pré-requisitos para este tutorial são:

* [Código de Estúdio Visual](https://code.visualstudio.com/) na sua máquina de desenvolvimento com as [ferramentas Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) e extensões [C#.](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)

    > [!TIP]
    > Pode ser solicitado a instalar o Docker. Ignore este pedido.
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/thank-you/sdk-3.1.201-windows-x64-installer) na sua máquina de desenvolvimento.
* Complete o [roteiro de configuração de recursos de vídeo ao vivo](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)e crie o [ambiente](https://review.docs.microsoft.com/en-us/azure/media-services/live-video-analytics-edge/detect-motion-emit-events-quickstart?branch=release-preview-media-services-lva#set-up-the-environment)

No final destes passos, terá recursos Azure relevantes implantados na sua subscrição Azure:

* Hub IoT do Azure
* Conta de armazenamento do Azure
* Conta Azure Media Services
* Linux VM em Azure, com o [tempo de execução IoT Edge](../../iot-edge/how-to-install-iot-edge-linux.md) instalado

## <a name="concepts"></a>Conceitos

A gravação de vídeo baseada em eventos refere-se ao processo de gravação de vídeo desencadeado por um evento. Este evento pode ser gerado a partir de:
- O processamento do próprio sinal de vídeo, por exemplo, ao detetar um objeto em movimento no vídeo.
- Uma fonte independente, por exemplo, a abertura de uma porta. 

Em alternativa, só pode ativar a gravação quando um serviço de inferenculação detetar que ocorreu um evento específico. Neste tutorial, você usará um vídeo de veículos movendo-se em uma autoestrada e gravar videoclips sempre que um caminhão é detetado.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording-tutorial/overview.svg" alt-text="Grafo do suporte de dados&quot;:::

O diagrama é uma representação pictórica de um [gráfico mediático](media-graph-concept.md) e módulos adicionais que realizam o cenário desejado. Estão envolvidos quatro módulos IoT Edge:

* Vídeo ao vivo analítico num módulo IoT Edge.
* Um módulo de borda a executar um modelo de IA atrás de um ponto final HTTP. Este módulo AI utiliza o modelo [YOLO v3,](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) que pode detetar muitos tipos de objetos.
* Um módulo personalizado para contar e filtrar objetos, que é referido como um Contador de Objetos no diagrama. Você vai construir um contador de objetos e implantá-lo neste tutorial.
* Um [módulo de simulador RTSP](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) para simular uma câmara RTSP.
    
Como mostra o diagrama, você usará um nó [de origem RTSP](media-graph-concept.md#rtsp-source) no gráfico de mídia para capturar o vídeo simulado ao vivo do tráfego numa autoestrada e enviar esse vídeo para dois caminhos:

* O primeiro caminho é para um nó [de processador de filtro de taxa de fotograma](media-graph-concept.md#frame-rate-filter-processor) que produz quadros de vídeo à taxa de fotogramas especificada (reduzida). Estes quadros de vídeo são enviados para um nó de extensão HTTP. O nó retransmite então os quadros, como imagens, para o módulo AI YOLO v3, que é um detetor de objetos. O nó recebe os resultados, que são os objetos (veículos no trânsito) detetados pelo modelo. O nó de extensão HTTP publica então os resultados através do nó de pia de mensagem IoT Hub para o hub IoT Edge.
* O módulo objectCounter está configurado para receber mensagens do hub IoT Edge, que incluem os resultados de deteção de objetos (veículos no tráfego). O módulo verifica estas mensagens e procura objetos de um determinado tipo, que foram configurados através de uma definição. Quando tal objeto é encontrado, este módulo envia uma mensagem para o hub IoT Edge. Essas mensagens &quot;objeto encontrado" são então encaminhada para o nó de origem IoT Hub do gráfico de mídia. Ao receber tal mensagem, o nó de origem IoT Hub no gráfico de mídia aciona o nó do processador do portão de [sinal.](media-graph-concept.md#signal-gate-processor) O nó do processador do portão de sinal abre-se durante um período de tempo configurado. O vídeo flui através do portão para o nó da pia do ativo durante esse período. Essa parte do live stream é então gravada através do nó [de pia](media-graph-concept.md#asset-sink) de ativo para um [ativo](terminology.md#asset) na sua conta Azure Media Services.

## <a name="set-up-your-development-environment"></a>Configurar o ambiente de desenvolvimento

Antes de começar, verifique se completou a terceira bala em [Pré-requisitos.](#prerequisites) Depois de terminar o script de configuração do recurso, selecione os suportes encaracolados para expor a estrutura da pasta. Você verá alguns ficheiros criados sob o diretório de amostras ~/clouddrive/lva.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstarts/clouddrive.png" alt-text="Grafo do suporte de dados&quot;:::

O diagrama é uma representação pictórica de um [gráfico mediático](media-graph-concept.md) e módulos adicionais que realizam o cenário desejado. Estão envolvidos quatro módulos IoT Edge:

* Vídeo ao vivo analítico num módulo IoT Edge.
* Um módulo de borda a executar um modelo de IA atrás de um ponto final HTTP. Este módulo AI utiliza o modelo [YOLO v3,](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) que pode detetar muitos tipos de objetos.
* Um módulo personalizado para contar e filtrar objetos, que é referido como um Contador de Objetos no diagrama. Você vai construir um contador de objetos e implantá-lo neste tutorial.
* Um [módulo de simulador RTSP](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) para simular uma câmara RTSP.
    
Como mostra o diagrama, você usará um nó [de origem RTSP](media-graph-concept.md#rtsp-source) no gráfico de mídia para capturar o vídeo simulado ao vivo do tráfego numa autoestrada e enviar esse vídeo para dois caminhos:

* O primeiro caminho é para um nó [de processador de filtro de taxa de fotograma](media-graph-concept.md#frame-rate-filter-processor) que produz quadros de vídeo à taxa de fotogramas especificada (reduzida). Estes quadros de vídeo são enviados para um nó de extensão HTTP. O nó retransmite então os quadros, como imagens, para o módulo AI YOLO v3, que é um detetor de objetos. O nó recebe os resultados, que são os objetos (veículos no trânsito) detetados pelo modelo. O nó de extensão HTTP publica então os resultados através do nó de pia de mensagem IoT Hub para o hub IoT Edge.
* O módulo objectCounter está configurado para receber mensagens do hub IoT Edge, que incluem os resultados de deteção de objetos (veículos no tráfego). O módulo verifica estas mensagens e procura objetos de um determinado tipo, que foram configurados através de uma definição. Quando tal objeto é encontrado, este módulo envia uma mensagem para o hub IoT Edge. Essas mensagens &quot;objeto encontrado" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",  
        "deviceId" : "lva-sample-device",  
        "moduleId" : "lvaEdge"  
    }
    ```

    A cadeia de ligação IoT Hub permite-lhe utilizar o Código do Estúdio Visual para enviar comandos para os módulos de borda através do Azure IoT Hub.
    
1. Em seguida, navegue na pasta src/edge e crie um ficheiro chamado **.env**.
1. Copie o conteúdo do ficheiro ~/clouddrive/lva-sample/.env. O texto deve parecer:

    ```
    SUBSCRIPTION_ID="<Subscription ID>"  
    RESOURCE_GROUP="<Resource Group>"  
    AMS_ACCOUNT="<AMS Account ID>"  
    IOTHUB_CONNECTION_STRING="HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx"  
    AAD_TENANT_ID="<AAD Tenant ID>"  
    AAD_SERVICE_PRINCIPAL_ID="<AAD SERVICE_PRINCIPAL ID>"  
    AAD_SERVICE_PRINCIPAL_SECRET="<AAD SERVICE_PRINCIPAL ID>"  
    INPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"  
    OUTPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/output"  
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry username>"      
    ```

## <a name="examine-the-template-file"></a>Examine o arquivo do modelo 

No passo anterior, iniciou o Visual Studio Code e abriu a pasta que contém o código de amostra.

No Código do Estúdio Visual, navegue para src/edge. Verá o ficheiro .env que criou e alguns ficheiros de modelos de implementação. Este modelo define quais os módulos de borda que irá implantar no dispositivo de borda (o Azure Linux VM). O ficheiro .env contém valores para as variáveis utilizadas nestes modelos, como as credenciais de Serviços de Mídia.

Abra src/edge/deployment.objectCounter.template.jsligado. Existem quatro entradas na secção de **módulos** que correspondem aos itens listados na secção "Conceitos" anteriores:

* **LvaEdge**: Este é o vídeo ao vivo analítico no módulo IoT Edge.
* **yolov3**: Este é o módulo de IA construído utilizando o modelo YOLO v3.
* **rtspsim**: Este é o simulador RTSP.
* **objectCounter**: Este é o módulo que procura objetos específicos nos resultados do yolov3.

Para o módulo objectCounter, consulte a cadeia (${MODULES.objectCounter}) utilizada para o valor "imagem". Isto baseia-se no [tutorial](../../iot-edge/tutorial-develop-for-linux.md) sobre o desenvolvimento de um módulo IoT Edge. O Código do Estúdio Visual reconhece automaticamente que o código do módulo objectCounter está em src/edge/modules/objectCounter. 

Leia [esta secção](../../iot-edge/module-composition.md#declare-routes) sobre como declarar rotas no manifesto de implantação IoT Edge. Em seguida, examine as rotas no ficheiro JSON do modelo. Note como:

* O LVAToObjectCounter é utilizado para enviar eventos específicos para um ponto final específico no módulo objectCounter.
* ObjectCounterToLVA é utilizado para enviar um evento de gatilho para um ponto final específico (que deve ser o nó de origem IoT Hub) no módulo LvaEdge.
* objectCounterToIoTHub é usado como uma ferramenta de depurador para ajudá-lo a ver a saída do objectCounter quando executar este tutorial.

> [!NOTE]
> Verifique as propriedades desejadas para o módulo objectCounter, que são configurados para procurar objetos que são marcados como "caminhão" com um nível de confiança de pelo menos 50%.

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Gerar e implementar o manifesto de implantação IoT Edge 

O manifesto de implantação define quais os módulos que são implantados num dispositivo de borda e as definições de configuração desses módulos. Siga estes passos para gerar um manifesto a partir do ficheiro do modelo e, em seguida, desloque-o para o dispositivo de borda.

Utilizando o Código do Estúdio Visual, siga [estas instruções](../../iot-edge/tutorial-develop-for-linux.md#build-and-push-your-solution) para iniciar sinsus no Docker. Em seguida, **selecione Build and Push IoT Edge Solution**. Utilize src/edge/deployment.objectCounter.template.jspara este passo.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording-tutorial/build-push.png" alt-text="Grafo do suporte de dados&quot;:::

O diagrama é uma representação pictórica de um [gráfico mediático](media-graph-concept.md) e módulos adicionais que realizam o cenário desejado. Estão envolvidos quatro módulos IoT Edge:

* Vídeo ao vivo analítico num módulo IoT Edge.
* Um módulo de borda a executar um modelo de IA atrás de um ponto final HTTP. Este módulo AI utiliza o modelo [YOLO v3,](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) que pode detetar muitos tipos de objetos.
* Um módulo personalizado para contar e filtrar objetos, que é referido como um Contador de Objetos no diagrama. Você vai construir um contador de objetos e implantá-lo neste tutorial.
* Um [módulo de simulador RTSP](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) para simular uma câmara RTSP.
    
Como mostra o diagrama, você usará um nó [de origem RTSP](media-graph-concept.md#rtsp-source) no gráfico de mídia para capturar o vídeo simulado ao vivo do tráfego numa autoestrada e enviar esse vídeo para dois caminhos:

* O primeiro caminho é para um nó [de processador de filtro de taxa de fotograma](media-graph-concept.md#frame-rate-filter-processor) que produz quadros de vídeo à taxa de fotogramas especificada (reduzida). Estes quadros de vídeo são enviados para um nó de extensão HTTP. O nó retransmite então os quadros, como imagens, para o módulo AI YOLO v3, que é um detetor de objetos. O nó recebe os resultados, que são os objetos (veículos no trânsito) detetados pelo modelo. O nó de extensão HTTP publica então os resultados através do nó de pia de mensagem IoT Hub para o hub IoT Edge.
* O módulo objectCounter está configurado para receber mensagens do hub IoT Edge, que incluem os resultados de deteção de objetos (veículos no tráfego). O módulo verifica estas mensagens e procura objetos de um determinado tipo, que foram configurados através de uma definição. Quando tal objeto é encontrado, este módulo envia uma mensagem para o hub IoT Edge. Essas mensagens &quot;objeto encontrado":::

Esta ação constrói o módulo objectCounter para a contagem de objetos e empurra a imagem para o registo do seu contentor Azure.

* Verifique se tem as variáveis ambientais CONTAINER_REGISTRY_USERNAME_myacr e CONTAINER_REGISTRY_PASSWORD_myacr definidas no ficheiro .env.

Este passo cria o manifesto de implantação IoT Edge em src/edge/config/deployment.objectCounter.amd64.jsligado. Clique com o botão direito nesse ficheiro e selecione **Criar Implementação para um único dispositivo**.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstarts/create-deployment-single-device.png" alt-text="Grafo do suporte de dados&quot;:::

O diagrama é uma representação pictórica de um [gráfico mediático](media-graph-concept.md) e módulos adicionais que realizam o cenário desejado. Estão envolvidos quatro módulos IoT Edge:

* Vídeo ao vivo analítico num módulo IoT Edge.
* Um módulo de borda a executar um modelo de IA atrás de um ponto final HTTP. Este módulo AI utiliza o modelo [YOLO v3,](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) que pode detetar muitos tipos de objetos.
* Um módulo personalizado para contar e filtrar objetos, que é referido como um Contador de Objetos no diagrama. Você vai construir um contador de objetos e implantá-lo neste tutorial.
* Um [módulo de simulador RTSP](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) para simular uma câmara RTSP.
    
Como mostra o diagrama, você usará um nó [de origem RTSP](media-graph-concept.md#rtsp-source) no gráfico de mídia para capturar o vídeo simulado ao vivo do tráfego numa autoestrada e enviar esse vídeo para dois caminhos:

* O primeiro caminho é para um nó [de processador de filtro de taxa de fotograma](media-graph-concept.md#frame-rate-filter-processor) que produz quadros de vídeo à taxa de fotogramas especificada (reduzida). Estes quadros de vídeo são enviados para um nó de extensão HTTP. O nó retransmite então os quadros, como imagens, para o módulo AI YOLO v3, que é um detetor de objetos. O nó recebe os resultados, que são os objetos (veículos no trânsito) detetados pelo modelo. O nó de extensão HTTP publica então os resultados através do nó de pia de mensagem IoT Hub para o hub IoT Edge.
* O módulo objectCounter está configurado para receber mensagens do hub IoT Edge, que incluem os resultados de deteção de objetos (veículos no tráfego). O módulo verifica estas mensagens e procura objetos de um determinado tipo, que foram configurados através de uma definição. Quando tal objeto é encontrado, este módulo envia uma mensagem para o hub IoT Edge. Essas mensagens &quot;objeto encontrado":::

Se este for o seu primeiro tutorial com Live Video Analytics no IoT Edge, o Código do Estúdio Visual pede-lhe para inserir a cadeia de ligação IoT Hub. Pode copiá-lo a partir do appsettings.jsarquivado.

Em seguida, o Código do Estúdio Visual pede-lhe para selecionar um dispositivo IoT Hub. Selecione o seu dispositivo IoT Edge, que deve ser o dispositivo de amostra de Lva.

Nesta fase, iniciou-se a implantação de módulos de borda para o seu dispositivo IoT Edge.
Em cerca de 30 segundos, refresque o Azure IoT Hub na secção inferior esquerda no Código do Estúdio Visual. Deve ver que existem quatro módulos implantados chamados lvaEdge, rtspsim, yolov3 e objectCounter.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording-tutorial/iot-hub.png" alt-text="Grafo do suporte de dados&quot;:::

O diagrama é uma representação pictórica de um [gráfico mediático](media-graph-concept.md) e módulos adicionais que realizam o cenário desejado. Estão envolvidos quatro módulos IoT Edge:

* Vídeo ao vivo analítico num módulo IoT Edge.
* Um módulo de borda a executar um modelo de IA atrás de um ponto final HTTP. Este módulo AI utiliza o modelo [YOLO v3,](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) que pode detetar muitos tipos de objetos.
* Um módulo personalizado para contar e filtrar objetos, que é referido como um Contador de Objetos no diagrama. Você vai construir um contador de objetos e implantá-lo neste tutorial.
* Um [módulo de simulador RTSP](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) para simular uma câmara RTSP.
    
Como mostra o diagrama, você usará um nó [de origem RTSP](media-graph-concept.md#rtsp-source) no gráfico de mídia para capturar o vídeo simulado ao vivo do tráfego numa autoestrada e enviar esse vídeo para dois caminhos:

* O primeiro caminho é para um nó [de processador de filtro de taxa de fotograma](media-graph-concept.md#frame-rate-filter-processor) que produz quadros de vídeo à taxa de fotogramas especificada (reduzida). Estes quadros de vídeo são enviados para um nó de extensão HTTP. O nó retransmite então os quadros, como imagens, para o módulo AI YOLO v3, que é um detetor de objetos. O nó recebe os resultados, que são os objetos (veículos no trânsito) detetados pelo modelo. O nó de extensão HTTP publica então os resultados através do nó de pia de mensagem IoT Hub para o hub IoT Edge.
* O módulo objectCounter está configurado para receber mensagens do hub IoT Edge, que incluem os resultados de deteção de objetos (veículos no tráfego). O módulo verifica estas mensagens e procura objetos de um determinado tipo, que foram configurados através de uma definição. Quando tal objeto é encontrado, este módulo envia uma mensagem para o hub IoT Edge. Essas mensagens &quot;objeto encontrado":::

## <a name="prepare-for-monitoring-events"></a>Preparar para eventos de monitorização

Para ver os eventos do módulo objectCounter e do live video analytics no módulo IoT Edge, siga estes passos:

1. Abra o painel Explorer no Código do Estúdio Visual e procure **o Azure IoT Hub** no canto inferior esquerdo.
1. Expandir o nó **dispositivos.**
1. Clique com o botão direito no ficheiro do dispositivo de amostra de Lva e selecione **Start Monitoring Built-in Event Endpoint**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstarts/start-monitoring-iothub-events.png" alt-text="Grafo do suporte de dados&quot;:::

O diagrama é uma representação pictórica de um [gráfico mediático](media-graph-concept.md) e módulos adicionais que realizam o cenário desejado. Estão envolvidos quatro módulos IoT Edge:

* Vídeo ao vivo analítico num módulo IoT Edge.
* Um módulo de borda a executar um modelo de IA atrás de um ponto final HTTP. Este módulo AI utiliza o modelo [YOLO v3,](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) que pode detetar muitos tipos de objetos.
* Um módulo personalizado para contar e filtrar objetos, que é referido como um Contador de Objetos no diagrama. Você vai construir um contador de objetos e implantá-lo neste tutorial.
* Um [módulo de simulador RTSP](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) para simular uma câmara RTSP.
    
Como mostra o diagrama, você usará um nó [de origem RTSP](media-graph-concept.md#rtsp-source) no gráfico de mídia para capturar o vídeo simulado ao vivo do tráfego numa autoestrada e enviar esse vídeo para dois caminhos:

* O primeiro caminho é para um nó [de processador de filtro de taxa de fotograma](media-graph-concept.md#frame-rate-filter-processor) que produz quadros de vídeo à taxa de fotogramas especificada (reduzida). Estes quadros de vídeo são enviados para um nó de extensão HTTP. O nó retransmite então os quadros, como imagens, para o módulo AI YOLO v3, que é um detetor de objetos. O nó recebe os resultados, que são os objetos (veículos no trânsito) detetados pelo modelo. O nó de extensão HTTP publica então os resultados através do nó de pia de mensagem IoT Hub para o hub IoT Edge.
* O módulo objectCounter está configurado para receber mensagens do hub IoT Edge, que incluem os resultados de deteção de objetos (veículos no tráfego). O módulo verifica estas mensagens e procura objetos de um determinado tipo, que foram configurados através de uma definição. Quando tal objeto é encontrado, este módulo envia uma mensagem para o hub IoT Edge. Essas mensagens &quot;objeto encontrado":::
    
## <a name="run-the-program"></a>Execute o programa

1. No Código do Estúdio Visual, abra o **separador Extensões** (ou prima Ctrl+Shift+X) e procure por Azure IoT Hub.
1. Clique no direito e selecione **Definições de extensão**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Grafo do suporte de dados&quot;:::

O diagrama é uma representação pictórica de um [gráfico mediático](media-graph-concept.md) e módulos adicionais que realizam o cenário desejado. Estão envolvidos quatro módulos IoT Edge:

* Vídeo ao vivo analítico num módulo IoT Edge.
* Um módulo de borda a executar um modelo de IA atrás de um ponto final HTTP. Este módulo AI utiliza o modelo [YOLO v3,](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) que pode detetar muitos tipos de objetos.
* Um módulo personalizado para contar e filtrar objetos, que é referido como um Contador de Objetos no diagrama. Você vai construir um contador de objetos e implantá-lo neste tutorial.
* Um [módulo de simulador RTSP](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) para simular uma câmara RTSP.
    
Como mostra o diagrama, você usará um nó [de origem RTSP](media-graph-concept.md#rtsp-source) no gráfico de mídia para capturar o vídeo simulado ao vivo do tráfego numa autoestrada e enviar esse vídeo para dois caminhos:

* O primeiro caminho é para um nó [de processador de filtro de taxa de fotograma](media-graph-concept.md#frame-rate-filter-processor) que produz quadros de vídeo à taxa de fotogramas especificada (reduzida). Estes quadros de vídeo são enviados para um nó de extensão HTTP. O nó retransmite então os quadros, como imagens, para o módulo AI YOLO v3, que é um detetor de objetos. O nó recebe os resultados, que são os objetos (veículos no trânsito) detetados pelo modelo. O nó de extensão HTTP publica então os resultados através do nó de pia de mensagem IoT Hub para o hub IoT Edge.
* O módulo objectCounter está configurado para receber mensagens do hub IoT Edge, que incluem os resultados de deteção de objetos (veículos no tráfego). O módulo verifica estas mensagens e procura objetos de um determinado tipo, que foram configurados através de uma definição. Quando tal objeto é encontrado, este módulo envia uma mensagem para o hub IoT Edge. Essas mensagens &quot;objeto encontrado":::
1. Procure e ative "Mostrar Mensagem Verbose".

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Grafo do suporte de dados&quot;:::

O diagrama é uma representação pictórica de um [gráfico mediático](media-graph-concept.md) e módulos adicionais que realizam o cenário desejado. Estão envolvidos quatro módulos IoT Edge:

* Vídeo ao vivo analítico num módulo IoT Edge.
* Um módulo de borda a executar um modelo de IA atrás de um ponto final HTTP. Este módulo AI utiliza o modelo [YOLO v3,](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) que pode detetar muitos tipos de objetos.
* Um módulo personalizado para contar e filtrar objetos, que é referido como um Contador de Objetos no diagrama. Você vai construir um contador de objetos e implantá-lo neste tutorial.
* Um [módulo de simulador RTSP](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) para simular uma câmara RTSP.
    
Como mostra o diagrama, você usará um nó [de origem RTSP](media-graph-concept.md#rtsp-source) no gráfico de mídia para capturar o vídeo simulado ao vivo do tráfego numa autoestrada e enviar esse vídeo para dois caminhos:

* O primeiro caminho é para um nó [de processador de filtro de taxa de fotograma](media-graph-concept.md#frame-rate-filter-processor) que produz quadros de vídeo à taxa de fotogramas especificada (reduzida). Estes quadros de vídeo são enviados para um nó de extensão HTTP. O nó retransmite então os quadros, como imagens, para o módulo AI YOLO v3, que é um detetor de objetos. O nó recebe os resultados, que são os objetos (veículos no trânsito) detetados pelo modelo. O nó de extensão HTTP publica então os resultados através do nó de pia de mensagem IoT Hub para o hub IoT Edge.
* O módulo objectCounter está configurado para receber mensagens do hub IoT Edge, que incluem os resultados de deteção de objetos (veículos no tráfego). O módulo verifica estas mensagens e procura objetos de um determinado tipo, que foram configurados através de uma definição. Quando tal objeto é encontrado, este módulo envia uma mensagem para o hub IoT Edge. Essas mensagens &quot;objeto encontrado"
              }
            ]
          }
        }
        ```
    
   * Uma chamada para o GraphInstanceActivate para iniciar a instância do gráfico e iniciar o fluxo de vídeo
   * Uma segunda chamada para o GraphInstanceList para mostrar que a instância do gráfico está no estado de execução
     
1. A saída na janela **TERMINAL** para agora numa **Entrada de Imprensa para continuar a** carregar. Não selecione **Enter** neste momento. Percorra para ver as cargas de resposta JSON para os métodos diretos que invocou.
1. Se agora mudar para a janela **OUTPUT** no Código do Estúdio Visual, verá mensagens a serem enviadas para o IoT Hub pelo módulo Live Video Analytics no módulo IoT Edge.

   Estas mensagens são discutidas na secção seguinte.
1. A instância do gráfico continua a correr e a gravar o vídeo. O simulador RTSP continua a dar a volta ao vídeo de origem. Reveja as mensagens como discutido na secção seguinte. Em seguida, para parar o caso, volte para a janela **TERMINAL** e selecione **Enter**. A próxima série de chamadas são feitas para limpar recursos utilizando:

   * Uma chamada para o GraphInstanceDeactivar para desativar a instância do gráfico.
   * Uma chamada para o GraphInstanceDelete para apagar o caso.
   * Uma chamada para a GraphTopologyDelete para apagar a topologia.
   * Uma chamada final para a GraphTopologyList para mostrar que a lista está agora vazia.

## <a name="interpret-the-results"></a>Interpretar os resultados 

Quando executam o gráfico de mídia, o módulo Live Video Analytics no IoT Edge envia certos eventos de diagnóstico e operacionais para o hub IoT Edge. Estes eventos são as mensagens que vê na janela **OUTPUT** do Código do Estúdio Visual. Contêm uma secção do corpo e uma secção de aplicações. Para compreender o que estas secções representam, consulte [Criar e ler mensagens IoT Hub](../../iot-hub/iot-hub-devguide-messages-construct.md).

Nas seguintes mensagens, as propriedades da aplicação e o conteúdo do corpo são definidos pelo módulo Live Video Analytics.

## <a name="diagnostics-events"></a>Eventos de diagnóstico

### <a name="mediasessionestablished-event"></a>Evento mediaSessionEstablished 

Quando um gráfico de mídia é instantâneo, o nó de origem RTSP tenta ligar-se ao servidor RTSP em execução no recipiente do simulador RTSP. Se for bem sucedido, imprime este evento. O tipo de evento é Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished.

```
[IoTHubMonitor] [5:53:17 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 XXX.XX.XX.XX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-05-17T17:53:16.981Z",
    "dataVersion": "1.0"
  }
}
```


* A mensagem é um evento de Diagnóstico (MediaSessionEstablished). Indica que o nó de origem RTSP (o sujeito) estabeleceu uma ligação com o simulador RTSP e começou a receber um feed ao vivo (simulado).
* A secção de assunto na aplicaçãoDeproperties refere o nó na topologia do gráfico a partir do qual a mensagem foi gerada. Neste caso, a mensagem provém do nó de origem RTSP.
* A secção de 'eventType' na aplicaçãoProperties indica que se trata de um evento de Diagnóstico.
* A secção EventTime indica a hora em que ocorreu o evento. Este é o momento em que o vídeo de tráfego (ficheiro MKV) começou a chegar ao módulo como um live stream.
* A secção do corpo contém dados sobre o evento de Diagnóstico, que neste caso são os detalhes do [SDP.](https://en.wikipedia.org/wiki/Session_Description_Protocol)


## <a name="operational-events"></a>Eventos operacionais

Depois de o gráfico de mídia ser executado por um tempo, eventualmente você receberá um evento a partir do módulo objectCounter. 

```
[IoTHubMonitor] [5:53:44 PM] Message received from [lva-sample-device/objectCounter]:
{
  "body": {
    "count": 2
  },
  "applicationProperties": {
    "eventTime": "2020-05-17T17:53:44.062Z"
  }
}
```

A secção de aplicaçõesProperties contém a hora do evento. Esta é a altura em que o módulo objectCounter observou que os resultados do módulo yolov3 continham objetos de interesse (camiões).

Pode ver-se que mais destes eventos aparecem à medida que outros camiões são detetados no vídeo.

### <a name="recordingstarted-event"></a>Evento "RecordingStarted"

Quase imediatamente após o contador de objetos enviar o evento, verá um evento do tipo Microsoft.Media.Graph.Operational.RecordingStarted:

```
[IoTHubMonitor] [5:53:46 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T175346Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": " 2020-05-17T17:53:46.132Z",
    "dataVersion": "1.0"
  }
}
```

A secção de assunto na aplicaçãoProperties refere o nó do lavatório do ativo no gráfico, que gerou esta mensagem. A secção do corpo contém informações sobre a localização da saída. Neste caso, é o nome do ativo Azure Media Services no qual o vídeo é gravado. Tome nota deste valor.

### <a name="recordingavailable-event"></a>Evento disponível de gravação

Quando o nó da pia do ativo tiver carregado o vídeo para o ativo, emite este evento do tipo Microsoft.Media.Graph.Operational.RecordingAvailable:

```
[IoTHubMonitor] [5:54:15 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T175346Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingAvailable",
    "eventTime": "2020-05-17T17:54:15.808Z",
    "dataVersion": "1.0"
  }
}
```

Este evento indica que foram escritos dados suficientes para que jogadores ou clientes iniciassem a reprodução do vídeo. A secção de assunto na aplicaçãoProperties refere o nó Desafundado do ActivoSink no gráfico, que gerou esta mensagem. A secção do corpo contém informações sobre a localização da saída. Neste caso, é o nome do ativo Azure Media Services no qual o vídeo é gravado.

### <a name="recordingstopped-event"></a>Evento de cobertura de Gravações

Se examinar as definições de ativação (tempo máximo de ativação) para o nó do processador do portão de sinal na [topologia,](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json)verá que o portão está configurado para fechar após 30 segundos de vídeo. Cerca de 30 segundos após o evento RecordingStarted, deverá ver um evento do tipo Microsoft.Media.Graph.Operational.RecordingS. Este evento indica que o nó da pia do ativo parou de gravar o vídeo para o ativo.

```
[IoTHubMonitor] [5:54:15 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T175346Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStopped",
    "eventTime": "2020-05-17T17:54:15.040Z",
    "dataVersion": "1.0"
  }
}
```

Este evento indica que a gravação parou. A secção de assunto na aplicaçãoProperties refere o nó Desafundado do ActivoSink no gráfico, que gerou esta mensagem. A secção do corpo contém informações sobre a localização da saída. Neste caso, é o nome do ativo Azure Media Services no qual o vídeo é gravado.

## <a name="media-services-asset"></a>Ativo de Serviços de Mídia  

Pode examinar o ativo Dos Serviços de Comunicação que foi criado pelo gráfico iniciando sessão no portal Azure e visualizando o vídeo.

1. Abra o seu navegador web e vá ao [portal Azure.](https://portal.azure.com/) Introduza as suas credenciais para iniciar sessão no portal. A vista predefinida é o dashboard de serviço.
1. Localize a sua conta de Media Services entre os recursos que tem na sua subscrição. Abra o painel de contas.
1. Selecione **Ativos** na lista **de Serviços de Mídia.**

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/continuous-video-recording-tutorial/assets.png" alt-text="Grafo do suporte de dados&quot;:::

O diagrama é uma representação pictórica de um [gráfico mediático](media-graph-concept.md) e módulos adicionais que realizam o cenário desejado. Estão envolvidos quatro módulos IoT Edge:

* Vídeo ao vivo analítico num módulo IoT Edge.
* Um módulo de borda a executar um modelo de IA atrás de um ponto final HTTP. Este módulo AI utiliza o modelo [YOLO v3,](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) que pode detetar muitos tipos de objetos.
* Um módulo personalizado para contar e filtrar objetos, que é referido como um Contador de Objetos no diagrama. Você vai construir um contador de objetos e implantá-lo neste tutorial.
* Um [módulo de simulador RTSP](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) para simular uma câmara RTSP.
    
Como mostra o diagrama, você usará um nó [de origem RTSP](media-graph-concept.md#rtsp-source) no gráfico de mídia para capturar o vídeo simulado ao vivo do tráfego numa autoestrada e enviar esse vídeo para dois caminhos:

* O primeiro caminho é para um nó [de processador de filtro de taxa de fotograma](media-graph-concept.md#frame-rate-filter-processor) que produz quadros de vídeo à taxa de fotogramas especificada (reduzida). Estes quadros de vídeo são enviados para um nó de extensão HTTP. O nó retransmite então os quadros, como imagens, para o módulo AI YOLO v3, que é um detetor de objetos. O nó recebe os resultados, que são os objetos (veículos no trânsito) detetados pelo modelo. O nó de extensão HTTP publica então os resultados através do nó de pia de mensagem IoT Hub para o hub IoT Edge.
* O módulo objectCounter está configurado para receber mensagens do hub IoT Edge, que incluem os resultados de deteção de objetos (veículos no tráfego). O módulo verifica estas mensagens e procura objetos de um determinado tipo, que foram configurados através de uma definição. Quando tal objeto é encontrado, este módulo envia uma mensagem para o hub IoT Edge. Essas mensagens &quot;objeto encontrado":::
1. Encontrará um ativo listado com o nome de AmostraAssetFromEVR-LVAEdge-{DateTime}. Este é o nome fornecido na propriedade outputLocation do evento RecordingStarted. O activoNamePattern na topologia determina como este nome foi gerado.
1. Selecione o elemento.
1. Na página de detalhes do ativo, **selecione Criar novo** na caixa de texto URL de **streaming.**

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/continuous-video-recording-tutorial/new-asset.png" alt-text="Grafo do suporte de dados&quot;:::

O diagrama é uma representação pictórica de um [gráfico mediático](media-graph-concept.md) e módulos adicionais que realizam o cenário desejado. Estão envolvidos quatro módulos IoT Edge:

* Vídeo ao vivo analítico num módulo IoT Edge.
* Um módulo de borda a executar um modelo de IA atrás de um ponto final HTTP. Este módulo AI utiliza o modelo [YOLO v3,](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) que pode detetar muitos tipos de objetos.
* Um módulo personalizado para contar e filtrar objetos, que é referido como um Contador de Objetos no diagrama. Você vai construir um contador de objetos e implantá-lo neste tutorial.
* Um [módulo de simulador RTSP](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) para simular uma câmara RTSP.
    
Como mostra o diagrama, você usará um nó [de origem RTSP](media-graph-concept.md#rtsp-source) no gráfico de mídia para capturar o vídeo simulado ao vivo do tráfego numa autoestrada e enviar esse vídeo para dois caminhos:

* O primeiro caminho é para um nó [de processador de filtro de taxa de fotograma](media-graph-concept.md#frame-rate-filter-processor) que produz quadros de vídeo à taxa de fotogramas especificada (reduzida). Estes quadros de vídeo são enviados para um nó de extensão HTTP. O nó retransmite então os quadros, como imagens, para o módulo AI YOLO v3, que é um detetor de objetos. O nó recebe os resultados, que são os objetos (veículos no trânsito) detetados pelo modelo. O nó de extensão HTTP publica então os resultados através do nó de pia de mensagem IoT Hub para o hub IoT Edge.
* O módulo objectCounter está configurado para receber mensagens do hub IoT Edge, que incluem os resultados de deteção de objetos (veículos no tráfego). O módulo verifica estas mensagens e procura objetos de um determinado tipo, que foram configurados através de uma definição. Quando tal objeto é encontrado, este módulo envia uma mensagem para o hub IoT Edge. Essas mensagens &quot;objeto encontrado":::
1. No assistente que abre, aceite as opções predefinitivas e **selecione Adicionar**. Para obter mais informações, consulte [a reprodução de vídeo.](video-playback-concept.md)

    > [!TIP]
    > Certifique-se de que o seu [ponto final de streaming está a funcionar](../latest/streaming-endpoint-concept.md).
1. O jogador deve carregar o vídeo. Selecione **Reproduzir** para vê-lo.

> [!NOTE]
> Como a fonte do vídeo era um contentor que simulava uma ração de câmara, as datas do vídeo estão relacionadas com a ativação da instância do gráfico e quando o desativou. Se utilizar os controlos de reprodução incorporados na [Reprodução de gravações de vários dias,](playback-multi-day-recordings-tutorial.md) pode ver as estações de tempo no vídeo exibida no ecrã.

## <a name="clean-up-resources"></a>Limpar os recursos

Se pretende experimentar os outros tutoriais, guarde os recursos que criou. Caso contrário, vá ao portal Azure, navegue pelos seus grupos de recursos, selecione o grupo de recursos sob o qual executou este tutorial e elimine o grupo de recursos.

## <a name="next-steps"></a>Passos seguintes

* Utilize uma [câmara IP](https://en.wikipedia.org/wiki/IP_camera) com suporte para RTSP em vez de utilizar o simulador RTSP. Pode pesquisar por câmaras IP com suporte RTSP na página de [produtos conformantes ONVIF,](https://www.onvif.org/conformant-products/) procurando dispositivos que estejam em conformidade com os perfis G, S ou T.
* Utilize um dispositivo Linux AMD64 ou X64 (vs. utilizando um VM Azure Linux). Este dispositivo deve estar na mesma rede que a câmara IP. Siga as instruções no [tempo de funcionamento do Azure IoT Edge no Linux](../../iot-edge/how-to-install-iot-edge-linux.md). Em seguida, siga as instruções no implante do [seu primeiro módulo IoT Edge para um dispositivo Linux virtual](../../iot-edge/quickstart-linux.md) para registar o dispositivo com O Azure IoT Hub.
