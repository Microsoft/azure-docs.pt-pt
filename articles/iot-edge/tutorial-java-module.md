---
title: Custom Java module tutorial - Azure IoT Edge | Microsoft Docs
description: Este tutorial mostra como criar um módulo do IoT Edge com código Java e implementá-lo num dispositivo Edge
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/04/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 8f5b34d3195ec6807f6eea6388e24d283f403c0c
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457498"
---
# <a name="tutorial-develop-a-java-iot-edge-module-for-linux-devices"></a>Tutorial: Develop a Java IoT Edge module for Linux devices

Pode utilizar os módulos do Azure IoT Edge para implementar código que aplica a sua lógica de negócio diretamente aos seus dispositivos IoT Edge. Este tutorial explica-lhe como criar e implementar um módulo do IoT Edge que filtra dados de sensores. You'll use the simulated IoT Edge device that you created in the Deploy Azure IoT Edge on a simulated device in [Linux](quickstart-linux.md) quickstart. Neste tutorial, ficará a saber como:    

> [!div class="checklist"]
> * Usar o Visual Studio Code para criar um módulo Java IoT Edge baseado no pacote de modelos maven Azure IoT Edge e no SDK de dispositivos Java Azure IoT.
> * Utilize o Visual Studio Code e o Docker para criar uma imagem do Docker e publicá-la no seu registo.
> * Implemente o módulo no seu dispositivo IoT Edge.
> * Veja os dados gerados.


O módulo do IoT Edge que criou neste tutorial filtra os dados de temperatura que são gerados pelo seu dispositivo. Envia apenas mensagens de origem caso a temperatura seja superior a um limiar especificado. Este tipo de análise no Edge é útil para reduzir a quantidade de dados que são comunicados e armazenados na cloud. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Solution scope

This tutorial demonstrates how to develop a module in **Java** using **Visual Studio Code**, and how to deploy it to a **Linux device**. IoT Edge does not support Java modules for Windows devices. 

Use the following table to understand your options for developing and deploying Java modules: 

| Java | Visual Studio Code | Visual Studio 2017/2019 | 
| - | ------------------ | ------------------ |
| **Linux AMD64** | ![Use VS Code for Java modules on Linux AMD64](./media/tutorial-c-module/green-check.png) |  |
| **Linux ARM32** | ![Use VS Code for Java modules on Linux ARM32](./media/tutorial-c-module/green-check.png) |  |

## <a name="prerequisites"></a>Pré-requisitos

Before beginning this tutorial, you should have gone through the previous tutorial to set up your development environment for Linux container development: [Develop IoT Edge modules for Linux devices](tutorial-develop-for-linux.md). By completing either of those tutorials, you should have the following prerequisites in place: 

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) no escalão gratuito ou standard no Azure.
* A [Linux device running Azure IoT Edge](quickstart-linux.md)
* A container registry, like [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio Code](https://code.visualstudio.com/) configured with the [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) configured to run Linux containers.

To develop an IoT Edge module in Java, install the following additional prerequisites on your development machine: 

* [Pacote de Extensão do Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack) para o Visual Studio Code.
* [Java SE Development Kit 10](https://aka.ms/azure-jdks) e [defina a variável de ambiente `JAVA_HOME`](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) para apontar para a sua instalação do JDK.
* [Maven](https://maven.apache.org/)

## <a name="create-a-module-project"></a>Create a module project
The following steps create an IoT Edge module project that's based on the Azure IoT Edge maven template package and Azure IoT Java device SDK. You create the project by using Visual Studio Code and the Azure IoT Tools.

### <a name="create-a-new-project"></a>Criar um novo projeto

Crie um modelo de solução Java que pode personalizar com o seu próprio código. 

1. No Visual Studio Code, selecione **Ver** > **Paleta de Comandos** para abrir a paleta de comandos do VS Code. 

2. Na paleta de comandos, introduza e execute o comando **Azure IoT Edge: Nova solução do IoT Edge**. Siga as instruções na paleta de comandos para criar a sua solução.

   | Campo | Valor |
   | ----- | ----- |
   | Selecionar pasta | Escolha a localização no computador de desenvolvimento na qual o VS Code vai criar os ficheiros da solução. |
   | Indicar um nome para a solução | Enter a descriptive name for your solution or accept the default **EdgeSolution**. |
   | Selecionar modelo de módulo | Choose **Java Module**. |
   | Provide value for groupId | Enter a group ID value or accept the default **com.edgemodule**. |
   | Indicar um nome para o módulo | Name your module **JavaModule**. |
   | Indicar o repositório de imagens do Docker para o módulo | Os repositórios de imagens incluem o nome do seu registo de contentor e o nome da sua imagem de contentor. Your container image is prepopulated from the name you provided in the last step. Substitua **localhost:5000** pelo valor do servidor de início de sessão do registo de contentor do Azure Container Registry. Pode obter o servidor de início de sessão na página Overview (Descrição Geral) do registo de contentor no portal do Azure. <br><br>The final image repository looks like \<registry name\>.azurecr.io/javamodule. |
 
   ![Fornecer repositório de imagens do Docker](./media/tutorial-java-module/repository.png)
   
Se é a primeira vez que cria o módulo Java, poderão ser necessários vários minutos para transferir os pacotes maven. A janela do VS Code carrega a área de trabalho da solução do IoT Edge. A área de trabalho da solução contém cinco componentes de nível superior. The **modules** folder contains the Java code for your module as well as Docker files for building your module as a container image. O ficheiro **\.env** armazena as credenciais do registo de contentor. O ficheiro **deployment.template.json** contém as informações que o runtime do IoT Edge utiliza para implementar módulos num dispositivo. And **deployment.debug.template.json** file containers the debug version of modules. Não irá editar a pasta **\.vscode** ou o ficheiro **\.gitignore** neste tutorial. 

Se não tiver especificado um registo de contentor ao criar a sua solução, mas aceitou o valor do localhost:5000 predefinido, não terá um ficheiro \.env. 

### <a name="add-your-registry-credentials"></a>Adicionar as credenciais do registo

O ficheiro de ambiente armazena as credenciais do seu registo de contentor e partilha-as com o runtime do IoT Edge. O runtime precisa destas credenciais para solicitar as imagens privadas para o dispositivo IoT Edge. 

1. No explorador do VS Code, abra o ficheiro .env. 
2. Atualize os campos com os valores **nome de utilizador** e **palavra-passe** que copiou do seu Azure Container Registry. 
3. Guarde este ficheiro. 

### <a name="select-your-target-architecture"></a>Select your target architecture

Currently, Visual Studio Code can develop Java modules for Linux AMD64 and Linux ARM32v7 devices. You need to select which architecture you're targeting with each solution, because the container is built and run differently for each architecture type. The default is Linux AMD64. 

1. Open the command palette and search for **Azure IoT Edge: Set Default Target Platform for Edge Solution**, or select the shortcut icon in the side bar at the bottom of the window. 

2. In the command palette, select the target architecture from the list of options. For this tutorial, we're using an Ubuntu virtual machine as the IoT Edge device, so will keep the default **amd64**. 

### <a name="update-the-module-with-custom-code"></a>Atualizar o módulo com o código personalizado

1. No explorador do VS Code, abra **módulos** > **JavaModule** > **src** > **main** > **java** > **com** > **edgemodule** > **App.java**.

2. Adicione o seguinte código na parte superior do ficheiro para importar novas as novas classes referenciadas.

    ```java
    import java.io.StringReader;
    import java.util.concurrent.atomic.AtomicLong;
    import java.util.HashMap;
    import java.util.Map;
    
    import javax.json.Json;
    import javax.json.JsonObject;
    import javax.json.JsonReader;
    
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.Pair;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.Property;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.TwinPropertyCallBack;
    ```

3. Adicione a seguinte definição à classe **App**. This variable sets a temperature threshold. The measured machine temperature won't be reported to IoT Hub until it goes over this value. 

    ```java
    private static final String TEMP_THRESHOLD = "TemperatureThreshold";
    private static AtomicLong tempThreshold = new AtomicLong(25);
    ```

4. Substitua o método de execução de **MessageCallbackMqtt** pelo seguinte código. Este método é chamado sempre que o módulo recebe uma mensagem MQTT do hub do IoT Edge. Ele filtra as mensagens que comunicam temperaturas inferiores ao limiar de temperatura definido através do módulo duplo.

    ```java
    protected static class MessageCallbackMqtt implements MessageCallback {
        private int counter = 0;
        @Override
        public IotHubMessageResult execute(Message msg, Object context) {
            this.counter += 1;
 
            String msgString = new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET);
            System.out.println(
                   String.format("Received message %d: %s",
                            this.counter, msgString));
            if (context instanceof ModuleClient) {
                try (JsonReader jsonReader = Json.createReader(new StringReader(msgString))) {
                    final JsonObject msgObject = jsonReader.readObject();
                    double temperature = msgObject.getJsonObject("machine").getJsonNumber("temperature").doubleValue();
                    long threshold = App.tempThreshold.get();
                    if (temperature >= threshold) {
                        ModuleClient client = (ModuleClient) context;
                        System.out.println(
                            String.format("Temperature above threshold %d. Sending message: %s",
                            threshold, msgString));
                        client.sendEventAsync(msg, eventCallback, msg, App.OUTPUT_NAME);
                    }
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
            return IotHubMessageResult.COMPLETE;
        }
    }
    ```

5. Adicione as seguintes duas classes internas estáticas à classe **App**. These classes update the tempThreshold variable when the module twin's desired property changes. Todos os módulos têm o seu próprio módulo duplo, que lhe permite configurar o código em execução no interior de um módulo diretamente a partir da cloud.

    ```java
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
        @Override
        public void execute(IotHubStatusCode status, Object context) {
            System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }
 
    protected static class OnProperty implements TwinPropertyCallBack {
        @Override
        public void TwinPropertyCallBack(Property property, Object context) {
            if (!property.getIsReported()) {
                if (property.getKey().equals(App.TEMP_THRESHOLD)) {
                    try {
                        long threshold = Math.round((double) property.getValue());
                        App.tempThreshold.set(threshold);
                    } catch (Exception e) {
                        System.out.println("Faile to set TemperatureThread with exception");
                        e.printStackTrace();
                    }
                }
            }
        }
    }
    ```

6. Adicione as seguintes linhas ao método **main** após **client.open()** para subscrever as atualizações do módulo duplo.

    ```java
    client.startTwin(new DeviceTwinStatusCallBack(), null, new OnProperty(), null);
    Map<Property, Pair<TwinPropertyCallBack, Object>> onDesiredPropertyChange = new HashMap<Property, Pair<TwinPropertyCallBack, Object>>() {
        {
            put(new Property(App.TEMP_THRESHOLD, null), new Pair<TwinPropertyCallBack, Object>(new OnProperty(), null));
        }
    };
    client.subscribeToTwinDesiredProperties(onDesiredPropertyChange);
    client.getTwin();
    ```

7. Save the App.java file.

8. No explorador do VS Code, abra o ficheiro **deployment.template.json** na área de trabalho da solução do IoT Edge.

9. Adicione o módulo **JavaModule** ao manifesto da implementação. Insira o seguinte conteúdo JSON na parte inferior da secção **moduleContent**, após o módulo duplo **$edgeHub**: 

   ```json
     "JavaModule": {
         "properties.desired":{
             "TemperatureThreshold":25
         }
     }
   ```

   ![Add module twin to deployment template](./media/tutorial-java-module/module-twin.png)

10. Save the deployment.template.json file.

## <a name="build-and-push-your-module"></a>Build and push your module

In the previous section, you created an IoT Edge solution and added code to the **JavaModule** to filter out messages where the reported machine temperature is below the acceptable limit. Now, build the solution as a container image and push it to your container registry. 

1. Abra o terminal integrado do VS Code ao selecionar **Ver** > **Terminal**.

1. Sign in to Docker by entering the following command in the terminal. Sign in with the username, password, and login server from your Azure container registry. You can retrieve these values from the **Access keys** section of your registry in the Azure portal.
     
   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   You may receive a security warning recommending the use of `--password-stdin`. While that best practice is recommended for production scenarios, it's outside the scope of this tutorial. For more information, see the [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) reference.

2. No explorador do VS Code, clique com o botão direito do rato no ficheiro **deployment.template.json** e selecione **Criar e Emitir solução do IoT Edge**.

   The build and push command starts three operations. First, it creates a new folder in the solution called **config** that holds the full deployment manifest, built out of information in the deployment template and other solution files. Second, it runs `docker build` to build the container image based on the appropriate dockerfile for your target architecture. Then, it runs `docker push` to push the image repository to your container registry.

## <a name="deploy-modules-to-device"></a>Deploy modules to device

Use the Visual Studio Code explorer and the Azure IoT Tools extension to deploy the module project to your IoT Edge device. You already have a deployment manifest prepared for your scenario, the **deployment.json** file in the config folder. Agora tudo o que precisa de fazer é selecionar um dispositivo para receber a implementação.

Make sure that your IoT Edge device is up and running.

1. In the Visual Studio Code explorer, expand the **Azure IoT Hub Devices** section to see your list of IoT devices.

2. Carregue com o botão direito do rato no nome do seu dispositivo do IoT Edge e, em seguida, selecione **Criar Implementação para o Dispositivo Único**.

3. Selecione o ficheiro **deployment.json** na pasta **config** e, em seguida, clique em **Selecionar Manifesto de Implementação do Edge**. Não utilize o ficheiro deployment.template.json.

4. Clique no botão Atualizar. You should see the new **JavaModule** running along with the **SimulatedTemperatureSensor** module and the **$edgeAgent** and **$edgeHub**.  

## <a name="view-generated-data"></a>Ver os dados gerados

Depois de aplicar o manifesto de implementação no seu dispositivo IoT Edge, o runtime do IoT Edge no dispositivo recolhe as novas informações de implementação e começa a ser executado no mesmo. Quaisquer módulos em execução no dispositivo que não estão incluídos no manifesto de implementação são parados. Quaisquer módulos em falta do dispositivo são iniciados.

Pode ver o estado do seu dispositivo do IoT Edge com a secção **Dispositivos do Hub IoT do Azure** do explorador do Visual Studio Code. Expanda os detalhes do seu dispositivo para ver uma lista de módulos implementados e em execução.

1. In the Visual Studio Code explorer, right-click the name of your IoT Edge device and select **Start Monitoring Built-in Event Endpoint**.

2. View the messages arriving at your IoT Hub. It may take a while for the messages to arrive, because the IoT Edge device has to receive its new deployment and start all the modules. Then, the changes we made to the JavaModule code wait until the machine temperature reaches 25 degrees before sending messages. It also adds the message type **Alert** to any messages that reach that temperature threshold. 

## <a name="edit-the-module-twin"></a>Edit the module twin

We used the JavaModule module twin in the deployment manifest to set the temperature threshold at 25 degrees. You can use the module twin to change the functionality without having to update the module code.

1. In Visual Studio Code, expand the details under your IoT Edge device to see the running modules. 

2. Right-click **JavaModule** and select **Edit module twin**. 

3. Find **TemperatureThreshold** in the desired properties. Change its value to a new temperature 5 degrees to 10 degrees higher than the latest reported temperature. 

4. Save the module twin file.

5. Right-click anywhere in the module twin editing pane and select **Update module twin**. 

6. Monitor the incoming device-to-cloud messages. You should see the messages stop until the new temperature threshold is reached. 

## <a name="clean-up-resources"></a>Limpar recursos 

Se planeia avançar para o próximo artigo recomendado, pode manter os recursos e as configurações que criou e reutilizá-los. Também pode continuar a utilizar o mesmo dispositivo IoT Edge como um dispositivo de teste. 

Caso contrário, pode eliminar as configurações locais e os recursos do Azure que criou neste artigo para evitar custos. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou uma função do módulo do IoT Edge que contém código para filtrar dados não processados gerados pelo seu dispositivo IoT Edge. When you're ready to build your own modules, you can learn more about [developing your own IoT Edge modules](module-development.md) or how to [develop modules with Visual Studio Code](how-to-vs-code-develop-module.md). You can continue on to the next tutorials to learn how Azure IoT Edge can help you deploy Azure cloud services to process and analyze data at the edge.

> [!div class="nextstepaction"]
> [Functions](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Custom Vision Service](tutorial-deploy-custom-vision.md)
