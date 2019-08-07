---
title: Tutorial implementar classificador de visão personalizada a um dispositivo - Azure IoT Edge | Documentos da Microsoft
description: Neste tutorial, saiba como fazer com que um modelo de imagem digitalizada executado como um contêiner com visão personalizada e IoT Edge.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/25/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 63485a41016033b00f787fc8c938b8da7135d657
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840150"
---
# <a name="tutorial-perform-image-classification-at-the-edge-with-custom-vision-service"></a>Tutorial: Classificar imagens na periferia com o Serviço de Visão Personalizada

O Azure IoT Edge pode mover as suas cargas de trabalho da cloud para a periferia e tornar a sua solução IoT Edge mais eficiente. Esta capacidade é particularmente útil para serviços que processam muitos dados, como modelos de imagem digitalizada. O [Serviço de Visão Personalizada](../cognitive-services/custom-vision-service/home.md) permite-lhe compilar classificadores de imagens personalizados e implementá-los como contentores nos dispositivos. Em conjunto, estes dois serviços permitem-lhe obter informações de imagens ou de transmissões em fluxo de vídeos sem que seja necessário transferir todos os dados do local primeiro. A Visão Personalizada oferece um classificador que compara uma imagem com um modelo preparado para gerar informações.

Por exemplo, a Visão Personalizada num dispositivo IoT Edge pode determinar se o tráfego numa autoestrada é superior ou inferior ao normal ou se um parque de estacionamento tem lugares disponíveis numa fila. Essas informações podem ser partilhadas com outro serviço, o que possibilita realizar ações.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Compilar um classificador de imagens com a Visão Personalizada.
> * Desenvolver um módulo do IoT Edge que consulta o servidor Web da Visão Personalizada no seu dispositivo.
> * Enviar os resultados do classificador de imagens para o Hub IoT.

<center>

![Diagrama-arquitetura do tutorial, preparar e implantar classificador](./media/tutorial-deploy-custom-vision/custom-vision-architecture.png)
</center>

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, você deve ter passado pelo tutorial anterior para configurar seu ambiente de desenvolvimento para o desenvolvimento de contêiner do Linux: [Desenvolva módulos IOT Edge para dispositivos Linux](tutorial-develop-for-linux.md). Ao concluir esse tutorial, você deve ter os seguintes pré-requisitos em vigor: 

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) no escalão gratuito ou standard no Azure.
* Um [dispositivo Linux executando o Azure IOT Edge](quickstart-linux.md)
* Um registro de contêiner, como o [registro de contêiner do Azure](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio Code](https://code.visualstudio.com/) configurado com as [ferramentas de IOT do Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) configurado para executar contêineres do Linux.

Para desenvolver um módulo IoT Edge com o serviço Visão Personalizada, instale os seguintes pré-requisitos adicionais em seu computador de desenvolvimento: 

* [Python](https://www.python.org/downloads/)
* [Git](https://git-scm.com/downloads)
* Extensão [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) para o Visual Studio Code

## <a name="build-an-image-classifier-with-custom-vision"></a>Compilar um classificador de imagens com a Visão Personalizada

Para compilar um classificador de imagens, tem de criar um projeto de Visão Personalizada e fornecer imagens de preparação. Para obter mais informações sobre os passos que vai seguir nesta secção, veja [How to build a classifier with Custom Vision](../cognitive-services/custom-vision-service/getting-started-build-a-classifier.md) (Como compilar um classificador com a Visão Personalizada).

Quando o classificador de imagens estiver compilado e preparado, pode exportá-lo como um contentor do Docker e importá-lo num dispositivo IoT Edge. 

### <a name="create-a-new-project"></a>Criar um novo projeto

1. No browser, navegue para a [página Web Visão Personalizada](https://customvision.ai/).

2. Selecione **Sign in** (Iniciar sessão) e inicie sessão na mesma conta que utiliza para aceder aos recursos do Azure. 

3. Selecione **New project** (Projeto novo).

4. Crie o projeto com os valores seguintes:

   | Campo | Valor |
   | ----- | ----- |
   | Nome | Indique um nome para o projeto, como **EdgeTreeClassifier**. |
   | Descrição | Descrição do projeto opcional. |
   | Grupo de Recursos | Selecione um dos grupos de recursos do Azure que inclui um recurso Serviço de Visão Personalizada ou **crie um novo** se você ainda não tiver adicionado um. |
   | Project Types (Tipos de Projetos) | **Classification** (Classificação) |
   | Classification Types (Tipos de Classificações) | **Multiclass (single tag per image)** (Multiclasses [etiqueta individual por imagem]) |
   | Domínios | **General (compact)** (Geral [compacto]) |
   | Recursos de exportação | **Plataformas básicas (Tensorflow, do coreml, ONNX,...)** |

5. Selecione **Create project** (Criar projeto).

### <a name="upload-images-and-train-your-classifier"></a>Carregar imagens e preparar o classificador

A criação de um classificador de imagens precisa de um conjunto de imagens de preparação, bem como de imagens de teste. 

1. Clone ou transfira as imagens de exemplo do repositório [Cognitive-CustomVision-Windows](https://github.com/Microsoft/Cognitive-CustomVision-Windows) para o seu computador de desenvolvimento local. 

   ```cmd/sh
   git clone https://github.com/Microsoft/Cognitive-CustomVision-Windows.git
   ```

2. Regresse ao projeto de Visão Personalizada e selecione **Add images** (Adicionar imagens). 

3. Navegue para o repositório do git que clonou localmente e aceda à primeira pasta de imagens, **Cognitive-CustomVision-Windows / Samples / Images / Hemlock**. Selecione as 10 imagens na pasta e selecione **Open** (Abrir). 

4. Adicione a etiqueta **hemlock** a este grupo de imagens e prima **enter** para a aplicar. 

5. Selecione **Upload 10 files** (Até 10 ficheiros). 

   ![Carregar ficheiros de hemlock etiquetado de visão personalizada](./media/tutorial-deploy-custom-vision/upload-hemlock.png)

6. Quando as imagens estiverem carregadas, selecione **Done** (Concluído).

7. Selecione novamente **Add images** (Adicionar imagens).

8. Navegue para a segunda pasta de imagens, **Cognitive-CustomVision-Windows / Samples / Images / Japanese Cherry**. Selecione as 10 imagens na pasta e selecione **Open** (Abrir). 

9. Adicione a etiqueta **japanese cherry** a este grupo de imagens e prima **enter** para a aplicar. 

10. Selecione **Upload 10 files** (Até 10 ficheiros). Quando as imagens estiverem carregadas, selecione **Done** (Concluído). 

11. Quando ambos os conjunto de imagens estiverem etiquetados e carregados, selecione **Train** (Preparar) para preparar o classificador. 

### <a name="export-your-classifier"></a>Exportar o classificador

1. Depois de preparar o classificador, selecione **Export** (Exportar), na página Performance (Desempenho) do mesmo. 

   ![Exportar o seu classificador de preparação de imagem](./media/tutorial-deploy-custom-vision/export.png)

2. Selecione **DockerFile** para a plataforma. 

3. Selecione **Linux** para a versão.  

4. Selecione **Export** (Exportar). 

   ![Exportar como DockerFile com contentores do Linux](./media/tutorial-deploy-custom-vision/export-2.png)

5. Após a conclusão da exportação, selecione **Download** (Transferir) e guarde o pacote .zip localmente no computador. Extraia todos os ficheiros do pacote. Estes ficheiros vão ser utilizados para criar um módulo do IoT Edge que contém o servidor de classificação de imagens. 

Quando chegar a esta fase, terá concluído a criação e a preparação do projeto de Visão Personalizada. Vai precisar dos ficheiros exportados na próxima secção, mas já não vai utilizar a página Web da Visão Personalizada. 

## <a name="create-an-iot-edge-solution"></a>Criar uma solução do IoT Edge

Agora, tem os ficheiros para uma versão de contentor do classificador de imagens no seu computador de desenvolvimento. Nesta secção, vai configurar o contentor do classificador de imagens para ser executado como um módulo do IoT Edge. Também vai criar um segundo módulo que será implementado juntamente com o classificador de imagens. Este segundo módulo publica pedidos no classificador e envia os resultados como mensagens para o Hub IoT. 

### <a name="create-a-new-solution"></a>Criar uma nova solução

Uma solução é uma forma lógica de desenvolver e organizar vários módulos para uma implementação do IoT Edge individual. As soluções contêm código para um ou mais módulos, bem como o manifesto de implementação que declara como os mesmos vão ser configurados num dispositivo IoT Edge. 

1. No Visual Studio Code, selecione **Vista** > **Terminal** para abrir o terminal integrado do VS Code.

1. Selecione **Ver** > **Paleta de Comandos** para abrir a paleta de comandos do VS Code. 

1. Na paleta de comandos, insira e execute o comando **Azure IOT Edge: Nova solução**de IOT Edge. Na paleta de comandos, indique as seguintes informações para criar a sua solução: 

   | Campo | Valor |
   | ----- | ----- |
   | Selecionar pasta | Escolha a localização no computador de desenvolvimento na qual o VS Code vai criar os ficheiros da solução. |
   | Indicar um nome para a solução | Introduza um nome descritivo para a solução, como **CustomVisionSolution**, ou aceite o predefinido. |
   | Selecionar modelo de módulo | Escolha **Python Module** (Módulo de Python). |
   | Indicar um nome para o módulo | Dê o nome **classifier** ao módulo.<br><br>É importante que o nome do módulo esteja em minúsculas. Quando referencia módulos, o IoT Edge é sensível a maiúsculas e minúsculas e esta solução utiliza uma biblioteca que formata todos os pedidos em minúsculas. |
   | Indicar o repositório de imagens do Docker para o módulo | Os repositórios de imagens incluem o nome do seu registo de contentor e o nome da sua imagem de contentor. A imagem de contentor é pré-preenchida no passo anterior. Substitua **localhost:5000** pelo valor do servidor de início de sessão do registo de contentor do Azure Container Registry. Pode obter o servidor de início de sessão na página Overview (Descrição Geral) do registo de contentor no portal do Azure. A cadeia final é semelhante a \<registry name\>.azurecr.io/classifier. |
 
   ![Fornecer repositório de imagens do Docker](./media/tutorial-deploy-custom-vision/repository.png)

A janela do Visual Studio Code carrega a sua área de trabalho da solução do IoT Edge.

### <a name="add-your-registry-credentials"></a>Adicionar as credenciais do registo

O ficheiro de ambiente armazena as credenciais do seu registo de contentor e partilha-as com o runtime do IoT Edge. O runtime precisa destas credenciais para solicitar as imagens privadas para o dispositivo IoT Edge.

1. No explorador do VS Code, abra o ficheiro .env.
2. Atualize os campos com os valores **nome de utilizador** e **palavra-passe** que copiou do seu Azure Container Registry.
3. Guarde este ficheiro.

### <a name="select-your-target-architecture"></a>Selecione sua arquitetura de destino

Atualmente, Visual Studio Code pode desenvolver módulos para dispositivos Linux AMD64 e Linux ARM32v7. Você precisa selecionar qual arquitetura está sendo direcionada a cada solução, porque o contêiner é compilado e executado de forma diferente para cada tipo de arquitetura. O padrão é o Linux AMD64. 

1. Abra a paleta de comandos e procure **Azure IOT Edge: Defina a plataforma de destino padrão para**a solução de borda ou selecione o ícone de atalho na barra lateral na parte inferior da janela. 

2. Na paleta de comandos, selecione a arquitetura de destino na lista de opções. Para este tutorial, estamos usando uma máquina virtual Ubuntu como o dispositivo IoT Edge, portanto, manterá o **AMD64**padrão. 

### <a name="add-your-image-classifier"></a>Adicionar o classificador de imagens

O modelo de módulo de Python no Visual Studio Code contém um código de exemplo que é possível executar para testar o IoT Edge. Esse código não vai ser utilizado neste cenário. Em vez disso, utilize os passos nesta secção para substituir o código de exemplo pelo contentor do classificador de imagens que exportou anteriormente. 

1. No explorador de ficheiros, navegue para o pacote da Visão Personalizada que transferiu e extraiu. Copie todos os conteúdos do pacote extraído. Deverá haver duas pastas, **app** e **azureml**, e dois ficheiros, **Dockerfile** e **README**. 

2. No explorador de ficheiros, navegue para o diretório que instruiu o Visual Studio Code para criar a solução do IoT Edge. 

3. Abra a pasta do módulo do classificador. Se tiver utilizado os nomes sugeridos na secção anterior, a estrutura da pasta será semelhante a **CustomVisionSolution / modules / classifier**. 

4. Cole os ficheiros na pasta **classifier**. 

5. Regresse à janela do Visual Studio Code. A área de trabalho da solução deverá agora mostrar os ficheiros do classificador de imagens na pasta do módulo. 

   ![Área de trabalho da solução com os ficheiros do classificador de imagens](./media/tutorial-deploy-custom-vision/workspace.png)

6. Abra o ficheiro **module.json** na pasta do classificador. 

7. Atualize o parâmetro **platforms** (plataformas) para apontar para o Dockerfile novo que foi adicionado e remova as opções de arquitetura ARM32 e AMD64.debug, que o módulo da Visão Personalizada não suporta atualmente. 

   ```json
   "platforms": {
       "amd64": "./Dockerfile"
   }
   ```

8. Guarde as alterações. 

### <a name="create-a-simulated-camera-module"></a>Criar um módulo de câmara simulada

Numa implementação da Visão Personalizada real, as imagens ou as transmissões em fluxo de vídeo em direto seriam fornecidas por uma câmera. Neste cenário, a câmara vai ser simulada mediante a compilação de um módulo que envia uma imagem de teste para o classificador de imagens. 

#### <a name="add-and-configure-a-new-module"></a>Adicionar e configurar um módulo novo

Nesta secção, vai adicionar um módulo novo à mesma CustomVisionSolution e fornecer código para criar a câmara simulada. 

1. Na mesma janela de Visual Studio Code, use a paleta de comandos para **executar Azure IOT Edge: Adicionar IoT Edge módulo**. Na paleta de comandos, indique as informações seguintes relativas ao módulo novo: 

   | Mensagem | Valor | 
   | ------ | ----- |
   | Selecione o ficheiro de modelo da implementação | Selecione o ficheiro deployment.template.json na pasta CustomVisionSolution. |
   | Selecionar modelo de módulo | Selecione **Python Module** (Módulo de Python). |
   | Indicar um nome para o módulo | Dê ao módulo o nome **cameraCapture** |
   | Indicar o repositório de imagens do Docker para o módulo | Substitua **localhost:5000** pelo valor do servidor de início de sessão do registo de contentor do Azure Container Registry. A cadeia final é semelhante a **\<registryname\>.azurecr.io/cameracapture**. |

   A janela do VS Code carrega o módulo novo na área de trabalho da solução e atualiza o ficheiro deployment.template.json. Deverá ver agora duas pastas do módulo, classifier e cameraCapture. 

2. Abra o ficheiro **main.py** na pasta **modules** / **cameraCapture**. 

3. Substitua todo o ficheiro pelo código abaixo: Este código de exemplo envia pedidos POST para o serviço de processamento de imagens que está a ser executado no módulo do classificador. Este contentor de módulo é fornecido com uma imagem de exemplo que vai ser utilizada nos pedidos. Depois, empacota a resposta como uma mensagem do Hub IoT e envia-a para uma fila de saída.  

    ```python
    # Copyright (c) Microsoft. All rights reserved.
    # Licensed under the MIT license. See LICENSE file in the project root for
    # full license information.

    import time
    import sys
    import os
    import requests
    import json

    import iothub_client
    # pylint: disable=E0611
    from iothub_client import IoTHubModuleClient, IoTHubClientError, IoTHubTransportProvider
    from iothub_client import IoTHubMessage, IoTHubMessageDispositionResult, IoTHubError
    # pylint: disable=E0401

    # messageTimeout - the maximum time in milliseconds until a message times out.
    # The timeout period starts at IoTHubModuleClient.send_event_async.
    MESSAGE_TIMEOUT = 10000

    # Choose HTTP, AMQP or MQTT as transport protocol.  
    PROTOCOL = IoTHubTransportProvider.MQTT

    # global counters
    SEND_CALLBACKS = 0

    # Send a message to IoT Hub
    # Route output1 to $upstream in deployment.template.json
    def send_to_hub(strMessage):
        message = IoTHubMessage(bytearray(strMessage, 'utf8'))
        hubManager.send_event_to_output("output1", message, 0)

    # Callback received when the message that we send to IoT Hub is processed.
    def send_confirmation_callback(message, result, user_context):
        global SEND_CALLBACKS
        SEND_CALLBACKS += 1
        print ( "Confirmation received for message with result = %s" % result )
        print ( "   Total calls confirmed: %d \n" % SEND_CALLBACKS )

    # Send an image to the image classifying server
    # Return the JSON response from the server with the prediction result
    def sendFrameForProcessing(imagePath, imageProcessingEndpoint):
        headers = {'Content-Type': 'application/octet-stream'}

        with open(imagePath, mode="rb") as test_image:
            try:
                response = requests.post(imageProcessingEndpoint, headers = headers, data = test_image)
                print("Response from classification service: (" + str(response.status_code) + ") " + json.dumps(response.json()) + "\n")
            except Exception as e:
                print(e)
                print("Response from classification service: (" + str(response.status_code))

        return json.dumps(response.json())

    class HubManager(object):
        def __init__(self, protocol, message_timeout):
            self.client_protocol = protocol
            self.client = IoTHubModuleClient()
            self.client.create_from_environment(protocol)
            # set the time until a message times out
            self.client.set_option("messageTimeout", message_timeout)
            
        # Sends a message to an output queue, to be routed by IoT Edge hub. 
        def send_event_to_output(self, outputQueueName, event, send_context):
            self.client.send_event_async(
                outputQueueName, event, send_confirmation_callback, send_context)

    def main(imagePath, imageProcessingEndpoint):
        try:
            print ( "Simulated camera module for Azure IoT Edge. Press Ctrl-C to exit." )

            try:
                global hubManager 
                hubManager = HubManager(PROTOCOL, MESSAGE_TIMEOUT)
            except IoTHubError as iothub_error:
                print ( "Unexpected error %s from IoTHub" % iothub_error )
                return

            print ( "The sample is now sending images for processing and will indefinitely.")

            while True:
                classification = sendFrameForProcessing(imagePath, imageProcessingEndpoint)
                send_to_hub(classification)
                time.sleep(10)

        except KeyboardInterrupt:
            print ( "IoT Edge module sample stopped" )

    if __name__ == '__main__':
        try:
            # Retrieve the image location and image classifying server endpoint from container environment
            IMAGE_PATH = os.getenv('IMAGE_PATH', "")
            IMAGE_PROCESSING_ENDPOINT = os.getenv('IMAGE_PROCESSING_ENDPOINT', "")
        except ValueError as error:
            print ( error )
            sys.exit(1)

        if ((IMAGE_PATH and IMAGE_PROCESSING_ENDPOINT) != ""):
            main(IMAGE_PATH, IMAGE_PROCESSING_ENDPOINT)
        else: 
            print ( "Error: Image path or image-processing endpoint missing" )
    ```

4. Guarde o ficheiro **main.py**. 

5. Abra o ficheiro **requrements.txt**. 

6. Adicione uma linha nova para uma biblioteca, para inclusão no contentor.

   ```Text
   requests
   ```

7. Guarde o ficheiro **requirements.txt**.


#### <a name="add-a-test-image-to-the-container"></a>Adicionar uma imagem de teste ao contentor

Neste cenário, em vez de utilizarmos uma câmara real para fornecer um feed de imagens, vamos utilizar uma imagem de teste individual. Está incluída uma imagem de teste no repositório do GitHub que transferiu para as imagens de preparação no início do tutorial. 

1. Navegue para a imagem de teste, localizada em **Cognitive-CustomVision-Windows** / **Samples** / **Images** / **Test**. 

2. Copie **test_image.jpg** 

3. Navegue para o diretório da solução do IoT Edge e cole a imagem de teste na pasta **modules** / **cameraCapture**. A imagem deverá estar na mesma pasta que o ficheiro main.py que foi editado na última secção. 

3. No Visual Studio Code, abra o ficheiro **Dockerfile.amd64** relativo ao módulo cameraCapture. (Atualmente, o módulo da Visão Personalizada não suporta o ARM32). 

4. Adicione a linha de código abaixo a seguir à linha que estabelece o diretório de trabalho, `WORKDIR /app`: 

   ```Dockerfile
   ADD ./test_image.jpg .
   ```

5. Guarde o Dockerfile. 

### <a name="prepare-a-deployment-manifest"></a>Preparar um manifesto de implementação

Até esta fase do tutorial, preparou um modelo da Visão Personalizada para classificar imagens de árvores e empacotou-o como um módulo do IoT Edge. Depois, criou um segundo módulo que pode consultar o servidor de classificação de imagens e comunicar os resultados ao Hub IoT. Agora, está pronto para criar o manifesto de implementação que vai dizer a um dispositivo IoT Edge como deve iniciar e executar esses dois módulos em conjunto. 

A extensão IoT Edge para o Visual Studio Code disponibiliza um modelo em cada solução do IoT Edge que o ajuda a criar um manifesto de implementação. 

1. Abra o ficheiro **deployment.template.json** na pasta da solução. 

2. Localize a seção **módulos** , que deve conter três módulos: os dois que você criou, classificador e cameraCapture e um terceiro que está incluído por padrão, SimulatedTemperatureSensor. 

3. Exclua o módulo **SimulatedTemperatureSensor** com todos os seus parâmetros. Este módulo é incluído para fornecer os dados de exemplo para cenários de teste, mas não precisamos do mesmo nesta implementação. 

4. Se tiver dado ao módulo de classificação de imagens outro nome que não **classifier**, verifique o nome e confirme que está em minúsculas. O módulo cameraCapture chama o módulo classifier com uma biblioteca de pedidos que formata todos os pedidos em minúsculas e o IoT Edge é sensível a maiúsculas e minúsculas. 

5. Atualize o parâmetro **createOptions** para o módulo cameraCapture com o JSON seguinte. Esta informação cria variáveis de ambiente no contentor do módulo que são obtidas no processo main.py. A inclusão dessa informação no manifesto da implementação permite-lhe alterar a imagem ou o ponto final sem ter de recompilar a imagem do módulo. 

    ```json
    "createOptions": "{\"Env\":[\"IMAGE_PATH=test_image.jpg\",\"IMAGE_PROCESSING_ENDPOINT=http://classifier/image\"]}"
    ```

    Se tiver dado ao módulo da Visão Personalizada outro nome que não *classifier*, atualize o valor do ponto final do processamento de imagens para corresponder ao mesmo. 

5. Na parte inferior do ficheiro, atualize o parâmetro **routes** para o módulo $edgeHub. Os resultados da predição devem ser encaminhados de cameraCapture para o Hub IoT. 

    ```json
        "routes": {
          "CameraCaptureToIoTHub": "FROM /messages/modules/cameraCapture/outputs/* INTO $upstream"
        },
    ```

    Se tiver dado ao segundo módulo outro nome que não *cameraCapture*, atualize o valor da rota para corresponder ao mesmo. 

7. Guarde o ficheiro **deployment.template.json**.

## <a name="build-and-deploy-your-iot-edge-solution"></a>Criar e implementar a sua solução do IoT Edge

Tendo ambos os módulos criados e o modelo do manifesto da implementação configurado, está pronto para compilar as imagens do contentor e enviá-las para o seu registo. 

Quando as imagens estiverem no registo, pode implementar a solução num dispositivo IoT Edge. Pode definir módulos num dispositivo através do Hub IoT, mas também pode aceder o seu Hub IoT e dispositivos através do Visual Studio Code. Nesta secção, pode configurar o acesso ao Hub IoT e, em seguida, utilizar o VS Code para implementar a solução no seu dispositivo IoT Edge.

Primeiro, compile e envie a solução para o registo de contentor. 

1. No explorador do VS Code, clique com o botão direito do rato no ficheiro **deployment.template.json** e selecione **Build and push IoT Edge solution** (Compilar e enviar solução do IoT Edge). Pode ver o progresso da operação no terminal integrado do VS Code. 
2. Repare que foi adicionada uma pasta nova à solução, **config**. Expanda essa pasta e abra o ficheiro **deployment.json** no interior.
3. Reveja as informações no ficheiro deployment.json. O ficheiro deployment.json é criado (ou atualizado) automaticamente com base no ficheiro do modelo de implementação que foi configurado e nas informações da solução, incluindo o ficheiro .env e os ficheiros module.json. 

Em seguida, selecione seu dispositivo e implante sua solução.

1. No explorador do VS Code, expanda a secção **Dispositivos do Hub IoT do Azure**. 
2. Clique com o botão direito do rato no dispositivo destinado à implementação e selecione **Criar Implementação para o único dispositivo**. 
3. No explorador de ficheiros, navegue para a pasta **config** na solução e selecione **deployment.json**. Clique em **Selecionar manifesto de implementação do Edge**. 

Se a implementação for bem-sucedida, é apresentada uma mensagem de confirmação no resultado do VS Code. No explorador do VS Code, expanda os detalhes do dispositivo IoT Edge que utilizou para esta implementação. Se os módulos não aparecem imediatamente, passe o cursor sobre o cabeçalho **Azure IoT Hub Devices** (Dispositivos do Hub IoT do Azure) para ativar o botão de atualização. Os módulos podem demorar alguns momentos a serem iniciados e a comunicarem ao Hub IoT. 

Também pode verificar se todos os módulos estão em execução no seu dispositivo. No dispositivo IoT Edge, execute o seguinte comando para ver o estado dos módulos. Os módulos podem demorar alguns momentos a serem iniciados.

   ```bash
   iotedge list
   ```

## <a name="view-classification-results"></a>Ver os resultados da classificação

Existem duas formas de ver os resultados dos seus módulos. Ou no próprio dispositivo, à medida que as mensagens são geradas e enviadas, ou do Visual Studio Code, à medida que as mensagens chegam ao Hub IoT. 

No dispositivo, veja os registos do módulo cameraCapture para visualizar as mensagens que estão a ser enviadas e a confirmação de que o Hub IoT as recebeu. 

   ```bash
   iotedge logs cameraCapture
   ```

Em Visual Studio Code, clique com o botão direito do mouse no nome do dispositivo de IoT Edge e selecione **Iniciar Monitoramento de ponto de extremidade de evento interno**. 

Os resultados do módulo da Visão Personalizada, que são enviados como mensagens a partir do módulo cameraCapture, incluem a probabilidade de a imagem ser de uma cicuta ou de uma cerejeira. Uma vez que a imagem é de uma cicuta, a probabilidade deverá aparecer como 1,0. 


## <a name="clean-up-resources"></a>Limpar recursos

Se planeia avançar para o próximo artigo recomendado, pode manter os recursos e as configurações que criou e reutilizá-los. Também pode continuar a utilizar o mesmo dispositivo IoT Edge como um dispositivo de teste. 

Caso contrário, você pode excluir as configurações locais e os recursos do Azure que você usou neste artigo para evitar cobranças. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]


## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, preparou um modelo de Visão Personalizada e implementou-o como um módulo num dispositivo IoT Edge. Depois, compilou um módulo que pode consultar o servidor de classificação de imagens e comunicar os resultados ao Hub IoT. 

Se quiser experimentar uma versão mais aprofundada deste cenário com um feed de câmera em direto, veja o projeto do GitHub[Custom Vision and Azure IoT Edge on a Raspberry Pi 3](https://github.com/Azure-Samples/Custom-vision-service-iot-edge-raspberry-pi) (A Visão Personalizada e o Azure IoT Edge num Raspberry Pi 3). 

Continue para os próximos tutoriais para saber mais sobre outras formas em que o Azure IoT Edge o pode ajudar a transformar os dados em informações empresariais na periferia.

> [!div class="nextstepaction"]
> [Armazenar dados na periferia com bases de dados do SQL Server](tutorial-store-data-sql-server.md)
