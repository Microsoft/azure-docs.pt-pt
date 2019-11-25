---
title: Tutorial develop C# module for Linux - Azure IoT Edge| Microsoft Docs
description: This tutorial shows you how to create an IoT Edge module with C# code and deploy it to a Linux IoT Edge device.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/23/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: d1a1a610c5cb198bd4df47033f4b8359b3d93007
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74452547"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-linux-devices"></a>Tutorial: Develop a C# IoT Edge module for Linux devices

Use Visual Studio Code to develop C# code and deploy it to a Linux device running Azure IoT Edge. 

Pode utilizar os módulos do Azure IoT Edge para implementar código que aplica a sua lógica de negócio diretamente aos seus dispositivos IoT Edge. Este tutorial explica-lhe como criar e implementar um módulo do IoT Edge que filtra dados de sensores. Vai utilizar o dispositivo IoT Edge simulado que criou na Implementação do Azure IoT Edge num dispositivo simulado em inícios rápidos do [Windows](quickstart.md) ou do [Linux](quickstart-linux.md). Neste tutorial, ficará a saber como:    

> [!div class="checklist"]
> * Use Visual Studio Code to create an IoT Edge module that's based on the .NET Core 2.1 SDK.
> * Utilize o Visual Studio Code e o Docker para criar uma imagem do Docker e publicá-la no seu registo.
> * Implemente o módulo no seu dispositivo IoT Edge.
> * Veja os dados gerados.

O módulo do IoT Edge que criou neste tutorial filtra os dados de temperatura que são gerados pelo seu dispositivo. Envia apenas mensagens de origem caso a temperatura seja superior a um limiar especificado. Este tipo de análise no Edge é útil para reduzir a quantidade de dados que são comunicados e armazenados na cloud. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Solution scope

This tutorial demonstrates how to develop a module in **C#** using **Visual Studio Code**, and how to deploy it to a **Linux device**. If you're developing modules for Windows devices, go to [Develop a C# IoT Edge module for Windows devices](tutorial-csharp-module-windows.md) instead.

Use the following table to understand your options for developing and deploying C# modules to Linux: 

| C# | Visual Studio Code | Visual Studio | 
| -- | ------------------ | ------------- |
| **Linux AMD64** | ![C# modules for LinuxAMD64 in VS Code](./media/tutorial-c-module/green-check.png) | ![C# modules for LinuxAMD64 in Visual Studio](./media/tutorial-c-module/green-check.png) |
| **Linux ARM32** | ![C# modules for LinuxARM32 in VS Code](./media/tutorial-c-module/green-check.png) | ![C# modules for LinuxARM64 in Visual Studio](./media/tutorial-c-module/green-check.png) |

>[!NOTE]
>Support for Linux ARM64 devices is available in [public preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). For more information, see [Develop and debug ARM64 IoT Edge modules in Visual Studio Code (preview)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

## <a name="prerequisites"></a>Pré-requisitos

Before beginning this tutorial, you should have gone through the previous tutorial to set up your development environment, [Develop an IoT Edge module for a Linux device](tutorial-develop-for-linux.md). After completing that tutorial, you already should have the following prerequisites: 

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) no escalão gratuito ou standard no Azure.
* A [Linux device running Azure IoT Edge](quickstart-linux.md).
* A container registry, like [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio Code](https://code.visualstudio.com/) configured with the [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) configured to run Linux containers.

To complete these tutorials, prepare the following additional prerequisites on your development machine: 

* [Extensão C# para Visual Studio Code (com tecnologia da OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).
* [SDK de .NET Core 2.1](https://www.microsoft.com/net/download).

## <a name="create-a-module-project"></a>Create a module project

The following steps create an IoT Edge module project for C# by using Visual Studio Code and the Azure IoT Tools extension. Once you have a project template created, add new code so that the module filters out messages based on their reported properties. 

### <a name="create-a-new-project"></a>Criar um novo projeto

Crie um modelo de solução C# que pode personalizar com o seu próprio código. 

1. No Visual Studio Code, selecione **Ver** > **Paleta de Comandos** para abrir a paleta de comandos do VS Code. 

2. Na paleta de comandos, introduza e execute o comando **Azure: Iniciar sessão** e siga as instruções para iniciar sessão na sua conta do Azure. Se já iniciou sessão, pode ignorar este passo.

3. Na paleta de comandos, introduza e execute o comando **Azure IoT Edge: Nova solução do IoT Edge**. Siga as instruções na paleta de comandos para criar a sua solução.

   | Campo | Valor |
   | ----- | ----- |
   | Selecionar pasta | Escolha a localização no computador de desenvolvimento na qual o VS Code vai criar os ficheiros da solução. |
   | Indicar um nome para a solução | Enter a descriptive name for your solution or accept the default **EdgeSolution**. |
   | Selecionar modelo de módulo | Choose **C# Module**. |
   | Indicar um nome para o módulo | Atribua o nome **CSharpModule** ao módulo. |
   | Indicar o repositório de imagens do Docker para o módulo | Os repositórios de imagens incluem o nome do seu registo de contentor e o nome da sua imagem de contentor. Your container image is prepopulated from the name you provided in the last step. Substitua **localhost:5000** pelo valor do servidor de início de sessão do registo de contentor do Azure Container Registry. Pode obter o servidor de início de sessão na página Overview (Descrição Geral) do registo de contentor no portal do Azure. <br><br>The final image repository looks like \<registry name\>.azurecr.io/csharpmodule. |
 
   ![Fornecer repositório de imagens do Docker](./media/tutorial-csharp-module/repository.png)


### <a name="add-your-registry-credentials"></a>Adicionar as credenciais do registo

O ficheiro de ambiente armazena as credenciais do seu registo de contentor e partilha-as com o runtime do IoT Edge. O runtime precisa destas credenciais para solicitar as imagens privadas para o dispositivo IoT Edge. Use the credentials from the **Access keys** section of your Azure container registry. 

1. No explorador do VS Code, abra o ficheiro **.env**. 
2. Update the fields with the **username** and **password** values from your Azure container registry. 
3. Guarde este ficheiro. 

### <a name="select-your-target-architecture"></a>Select your target architecture

Currently, Visual Studio Code can develop C# modules for Linux AMD64 and Linux ARM32v7 devices. You need to select which architecture you're targeting with each solution, because the container is built and run differently for each architecture type. The default is Linux AMD64. 

1. Open the command palette and search for **Azure IoT Edge: Set Default Target Platform for Edge Solution**, or select the shortcut icon in the side bar at the bottom of the window. 

2. In the command palette, select the target architecture from the list of options. For this tutorial, we're using an Ubuntu virtual machine as the IoT Edge device, so will keep the default **amd64**. 

### <a name="update-the-module-with-custom-code"></a>Atualizar o módulo com o código personalizado

1. No explorador do VS Code, abra **módulos** > **CSharpModule** > **Program.cs**.

2. Na parte superior do espaço de nomes **CSharpModule**, adicione três declarações **em utilização** para os tipos que são utilizados mais tarde:

    ```csharp
    using System.Collections.Generic;     // For KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // For TwinCollection
    using Newtonsoft.Json;                // For JsonConvert
    ```

3. Adicione a variável **temperatureThreshold** à classe **Programa**. Esta variável define o valor que a temperatura medida deve exceder, para que os dados sejam enviados para o hub IoT. 

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

4. Adicione as classes **MessageBody**, **Machine** e **Ambient** à classe **Program**. Estas classes definem o esquema esperado para o corpo das mensagens a receber.

    ```csharp
    class MessageBody
    {
        public Machine machine {get;set;}
        public Ambient ambient {get; set;}
        public string timeCreated {get; set;}
    }
    class Machine
    {
        public double temperature {get; set;}
        public double pressure {get; set;}         
    }
    class Ambient
    {
        public double temperature {get; set;}
        public int humidity {get; set;}         
    }
    ```

5. Find the **Init** function. This function creates and configures a **ModuleClient** object, which allows the module to connect to the local Azure IoT Edge runtime to send and receive messages. Depois de criar o **ModuleClient**, o código lê o valor **temperatureThreshold** a partir das propriedades pretendidas do módulo duplo. O código regista uma chamada de retorno para receber mensagens de um hub do IoT Edge através do ponto final **input1**. Substitua o método **SetInputMessageHandlerAsync** por um novo e adicione um método **SetDesiredPropertyUpdateCallbackAsync** para obter atualizações para as propriedades pretendidas. Para fazer esta alteração, substitua a última linha do método **Init** pelo seguinte código:

    ```csharp
    // Register a callback for messages that are received by the module.
    // await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Read the TemperatureThreshold value from the module twin's desired properties
    var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
    await OnDesiredPropertiesUpdate(moduleTwin.Properties.Desired, ioTHubModuleClient);
    
    // Attach a callback for updates to the module twin's desired properties.
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertiesUpdate, null);

    // Register a callback for messages that are received by the module.
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

6. Adicione o método **onDesiredPropertiesUpdate** à classe **Programa**. Este método recebe atualizações nas propriedades pretendidas do módulo duplo e atualiza a variável **temperatureThreshold** para corresponder. Todos os módulos têm o seu próprio módulo duplo, que lhe permite configurar o código em execução no interior de um módulo diretamente a partir da cloud.

    ```csharp
    static Task OnDesiredPropertiesUpdate(TwinCollection desiredProperties, object userContext)
    {
        try
        {
            Console.WriteLine("Desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

            if (desiredProperties["TemperatureThreshold"]!=null)
                temperatureThreshold = desiredProperties["TemperatureThreshold"];

        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error when receiving desired property: {0}", exception);
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error when receiving desired property: {0}", ex.Message);
        }
        return Task.CompletedTask;
    }
    ```

7. Substitua o método **PipeMessage** pelo método **FilterMessages**. Este método é chamado sempre que o módulo recebe uma mensagem do hub do IoT Edge. Ele filtra as mensagens que comunicam temperaturas inferiores ao limiar de temperatura definido através do módulo duplo. Também adiciona a propriedade **MessageType** à mensagem com o valor definido para **Alerta**. 

    ```csharp
    static async Task<MessageResponse> FilterMessages(Message message, object userContext)
    {
        var counterValue = Interlocked.Increment(ref counter);
        try
        {
            ModuleClient moduleClient = (ModuleClient)userContext;
            var messageBytes = message.GetBytes();
            var messageString = Encoding.UTF8.GetString(messageBytes);
            Console.WriteLine($"Received message {counterValue}: [{messageString}]");

            // Get the message body.
            var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

            if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
            {
                Console.WriteLine($"Machine temperature {messageBody.machine.temperature} " +
                    $"exceeds threshold {temperatureThreshold}");
                using (var filteredMessage = new Message(messageBytes))
                {
                    foreach (KeyValuePair<string, string> prop in message.Properties)
                    {
                        filteredMessage.Properties.Add(prop.Key, prop.Value);
                    }

                    filteredMessage.Properties.Add("MessageType", "Alert");
                    await moduleClient.SendEventAsync("output1", filteredMessage);
                }
            }

            // Indicate that the message treatment is completed.
            return MessageResponse.Completed;
        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", exception);
            }
            // Indicate that the message treatment is not completed.
            var moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
            // Indicate that the message treatment is not completed.
            ModuleClient moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
    }
    ```

8. Guarde o ficheiro Program.cs.

9. No explorador do VS Code, abra o ficheiro **deployment.template.json** na área de trabalho da solução do IoT Edge. 

10. Adicione o módulo duplo **CSharpModule** ao manifesto de implementação. Insira o seguinte conteúdo JSON na parte inferior da secção **modulesContent**, após o módulo duplo **$edgeHub**: 

    ```json
       "CSharpModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
    ```

    ![Add module twin to deployment template](./media/tutorial-csharp-module/module-twin.png)

11. Save the deployment.template.json file.


## <a name="build-and-push-your-module"></a>Build and push your module

In the previous section, you created an IoT Edge solution and added code to the CSharpModule that will filter out messages where the reported machine temperature is within the acceptable limits. Agora, tem de criar a solução como uma imagem de contentor e enviá-la para o registo de contentor.

1. Abra o terminal integrado do VS Code ao selecionar **Ver** > **Terminal**.

1. Sign in to Docker by entering the following command in the terminal. Sign in with the username, password, and login server from your Azure container registry. You can retrieve these values from the **Access keys** section of your registry in the Azure portal.
     
   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   You may receive a security warning recommending the use of `--password-stdin`. While that best practice is recommended for production scenarios, it's outside the scope of this tutorial. For more information, see the [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) reference.

2. No explorador do VS Code, clique com o botão direito do rato no ficheiro **deployment.template.json** e selecione **Criar e Emitir solução do IoT Edge**.

   The build and push command starts three operations. First, it creates a new folder in the solution called **config** that holds the full deployment manifest, built out of information in the deployment template and other solution files. Second, it runs `docker build` to build the container image based on the appropriate dockerfile for your target architecture. Then, it runs `docker push` to push the image repository to your container registry.

## <a name="deploy-and-run-the-solution"></a>Implementar e executar a solução

Use the Visual Studio Code explorer and the Azure IoT Tools extension to deploy the module project to your IoT Edge device. You already have a deployment manifest prepared for your scenario, the **deployment.json** file in the config folder. Agora tudo o que precisa de fazer é selecionar um dispositivo para receber a implementação.

Make sure that your IoT Edge device is up and running.

1. In the Visual Studio Code explorer, expand the **Azure IoT Hub Devices** section to see your list of IoT devices.

2. Carregue com o botão direito do rato no nome do seu dispositivo do IoT Edge e, em seguida, selecione **Criar Implementação para o Dispositivo Único**. 

5. Selecione o ficheiro **deployment.json** na pasta **config** e, em seguida, clique em **Selecionar Manifesto de Implementação do Edge**. Não utilize o ficheiro deployment.template.json. 

6. Clique no botão Atualizar. You should see the new **CSharpModule** running along with the **SimulatedTemperatureSensor** module and the **$edgeAgent** and **$edgeHub**.  

## <a name="view-generated-data"></a>Ver os dados gerados

Depois de aplicar o manifesto de implementação no seu dispositivo IoT Edge, o runtime do IoT Edge no dispositivo recolhe as novas informações de implementação e começa a ser executado no mesmo. Quaisquer módulos em execução no dispositivo que não estão incluídos no manifesto de implementação são parados. Quaisquer módulos em falta do dispositivo são iniciados.

Pode ver o estado do seu dispositivo do IoT Edge com a secção **Dispositivos do Hub IoT do Azure** do explorador do Visual Studio Code. Expanda os detalhes do seu dispositivo para ver uma lista de módulos implementados e em execução.

1. In the Visual Studio Code explorer, right-click the name of your IoT Edge device and select **Start Monitoring Built-in Event Endpoint**.

2. View the messages arriving at your IoT Hub. It may take a while for the messages to arrive, because the IoT Edge device has to receive its new deployment and start all the modules. Then, the changes we made to the CModule code wait until the machine temperature reaches 25 degrees before sending messages. It also adds the message type **Alert** to any messages that reach that temperature threshold. 

   ![View messages arriving at IoT Hub](./media/tutorial-csharp-module/view-d2c-message.png)
 
## <a name="edit-the-module-twin"></a>Edit the module twin

We used the CSharpModule module twin in the deployment manifest to set the temperature threshold at 25 degrees. You can use the module twin to change the functionality without having to update the module code.

1. In Visual Studio Code, expand the details under your IoT Edge device to see the running modules. 

2. Right-click **CSharpModule** and select **Edit module twin**. 

3. Find **TemperatureThreshold** in the desired properties. Change its value to a new temperature 5 degrees to 10 degrees higher than the latest reported temperature. 

4. Save the module twin file.

5. Right-click anywhere in the module twin editing pane and select **Update module twin**. 

5. Monitor the incoming device-to-cloud messages. You should see the messages stop until the new temperature threshold is reached. 

## <a name="clean-up-resources"></a>Limpar recursos 

Se planeia avançar para o próximo artigo recomendado, pode manter os recursos e as configurações que criou e reutilizá-los. Também pode continuar a utilizar o mesmo dispositivo IoT Edge como um dispositivo de teste. 

Otherwise, you can delete the local configurations and the Azure resources that you used in this article to avoid charges. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou uma função do módulo do IoT Edge que contém código para filtrar dados não processados gerados pelo seu dispositivo IoT Edge. When you're ready to build your own modules, you can learn more about [developing your own IoT Edge modules](module-development.md) or how to [develop modules with Visual Studio Code](how-to-vs-code-develop-module.md). You can continue on to the next tutorials to learn how Azure IoT Edge can help you deploy Azure cloud services to process and analyze data at the edge.

> [!div class="nextstepaction"]
> [Functions](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Custom Vision Service](tutorial-deploy-custom-vision.md)
