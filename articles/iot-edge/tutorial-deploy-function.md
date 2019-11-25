---
title: Tutorial deploy an Azure function to a device - Azure IoT Edge | Microsoft Docs
description: In this tutorial, you develop an Azure function as an IoT Edge module, then deploy it to an edge device.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/25/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 1db64c2cc68dc9c47d4f2cf5f63eb0667907d737
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456712"
---
# <a name="tutorial-deploy-azure-functions-as-iot-edge-modules"></a>Tutorial: Deploy Azure functions as IoT Edge modules

Pode utilizar as Funções do Azure para implementar código que aplica a sua lógica de negócio diretamente nos seus dispositivos Azure IoT Edge. Este tutorial explica-lhe como criar e implementar uma função do Azure que filtra dados de sensores no dispositivo IoT Edge simulado. Vai utilizar o dispositivo IoT Edge simulado que criou na Implementação do Azure IoT Edge num dispositivo simulado em inícios rápidos do [Windows](quickstart.md) ou do [Linux](quickstart-linux.md). Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Utilize o Visual Studio Code para criar uma função do Azure.
> * Utilizar o VS Code e o Docker para criar uma imagem do Docker e publicá-la num registo de contentor.
> * Implementar o módulo a partir do registo de contentor no seu dispositivo IoT Edge.
> * Ver dados filtrados.

<center>

![Diagram - Tutorial architecture, stage and deploy function module](./media/tutorial-deploy-function/functions-architecture.png)
</center>

>[!NOTE]
>Os módulos de Funções do Azure no Azure IoT Edge estão em [pré-visualização pública](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

A função do Azure que criou neste tutorial filtra os dados de temperatura que são gerados pelo seu dispositivo. A função envia apenas mensagens de origem para o Hub IoT do Azure quando a temperatura é superior a um limiar especificado. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Before beginning this tutorial, you should have gone through the previous tutorial to set up your development environment for Linux container development: [Develop IoT Edge modules for Linux devices](tutorial-develop-for-linux.md). By completing that tutorial, you should have the following prerequisites in place: 

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) no escalão gratuito ou standard no Azure.
* A [Linux device running Azure IoT Edge](quickstart-linux.md)
* A container registry, like [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio Code](https://code.visualstudio.com/) configured with the [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) configured to run Linux containers.

To develop an IoT Edge module in with Azure Functions, install the following additional prerequisites on your development machine: 

* [Extensão C# para Visual Studio Code (com tecnologia da OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).
* [O SDK .NET Core 2.1](https://www.microsoft.com/net/download).

## <a name="create-a-function-project"></a>Criar um projeto de função

The Azure IoT Tools for Visual Studio Code that you installed in the prerequisites provides management capabilities as well as some code templates. Nesta secção, vai utilizar o Visual Studio Code para criar uma solução do IoT Edge que contém uma função do Azure. 

### <a name="create-a-new-project"></a>Criar um novo projeto

Create a C# Function solution template that you can customize with your own code.

1. Abra o Visual Studio Code no computador de desenvolvimento.

2. Abra a paleta de comandos do VS Code ao selecionar **Ver** > **Paleta de Comandos**.

3. Na paleta de comandos, introduza e execute o comando **Azure IoT Edge: Nova solução do IoT Edge**. Siga as instruções na paleta de comandos para criar a sua solução.

   | Campo | Valor |
   | ----- | ----- |
   | Selecionar pasta | Escolha a localização no computador de desenvolvimento na qual o VS Code vai criar os ficheiros da solução. |
   | Indicar um nome para a solução | Enter a descriptive name for your solution, like **FunctionSolution**, or accept the default. |
   | Selecionar modelo de módulo | Choose **Azure Functions - C#** . |
   | Indicar um nome para o módulo | Atribua o nome **CSharpFunction** ao módulo. |
   | Indicar o repositório de imagens do Docker para o módulo | Os repositórios de imagens incluem o nome do seu registo de contentor e o nome da sua imagem de contentor. A imagem de contentor é pré-preenchida no passo anterior. Substitua **localhost:5000** pelo valor do servidor de início de sessão do registo de contentor do Azure Container Registry. Pode obter o servidor de início de sessão na página Overview (Descrição Geral) do registo de contentor no portal do Azure. The final string looks like \<registry name\>.azurecr.io/CSharpFunction. |

   ![Fornecer repositório de imagens do Docker](./media/tutorial-deploy-function/repository.png)

### <a name="add-your-registry-credentials"></a>Adicionar as credenciais do registo

O ficheiro de ambiente armazena as credenciais do seu registo de contentor e partilha-as com o runtime do IoT Edge. O runtime precisa destas credenciais para solicitar as imagens privadas para o dispositivo IoT Edge.

1. No explorador do VS Code, abra o ficheiro .env.
2. Atualize os campos com os valores **nome de utilizador** e **palavra-passe** que copiou do seu Azure Container Registry.
3. Guarde este ficheiro.

### <a name="select-your-target-architecture"></a>Select your target architecture

Currently, Visual Studio Code can develop C modules for Linux AMD64 and Linux ARM32v7 devices. You need to select which architecture you're targeting with each solution, because the container is built and run differently for each architecture type. The default is Linux AMD64. 

1. Open the command palette and search for **Azure IoT Edge: Set Default Target Platform for Edge Solution**, or select the shortcut icon in the side bar at the bottom of the window. 

2. In the command palette, select the target architecture from the list of options. For this tutorial, we're using an Ubuntu virtual machine as the IoT Edge device, so will keep the default **amd64**. 

### <a name="update-the-module-with-custom-code"></a>Atualizar o módulo com o código personalizado

Let's add some additional code so that the module processes the messages at the edge before forwarding them to IoT Hub.

1. In Visual Studio Code, open **modules** > **CSharpFunction** > **CSharpFunction.cs**.

1. Replace the contents of the **CSharpFunction.cs** file with the following code. This code receives telemetry about ambient and machine temperature, and only forwards the message on to IoT Hub if the machine temperature is above a defined threshold.

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.IO;
   using System.Text;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Client;
   using Microsoft.Azure.WebJobs;
   using Microsoft.Azure.WebJobs.Extensions.EdgeHub;
   using Microsoft.Azure.WebJobs.Host;
   using Microsoft.Extensions.Logging;
   using Newtonsoft.Json;

   namespace Functions.Samples
   {
       public static class CSharpFunction
       {
           [FunctionName("CSharpFunction")]
           public static async Task FilterMessageAndSendMessage(
               [EdgeHubTrigger("input1")] Message messageReceived,
               [EdgeHub(OutputName = "output1")] IAsyncCollector<Message> output,
               ILogger logger)
           {
               const int temperatureThreshold = 20;
               byte[] messageBytes = messageReceived.GetBytes();
               var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

               if (!string.IsNullOrEmpty(messageString))
               {
                   logger.LogInformation("Info: Received one non-empty message");
                   // Get the body of the message and deserialize it.
                   var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

                   if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
                   {
                       // Send the message to the output as the temperature value is greater than the threshold.
                       using (var filteredMessage = new Message(messageBytes))
                       {
                            // Copy the properties of the original message into the new Message object.
                            foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
                            {filteredMessage.Properties.Add(prop.Key, prop.Value);}
                            // Add a new property to the message to indicate it is an alert.
                            filteredMessage.Properties.Add("MessageType", "Alert");
                            // Send the message.       
                            await output.AddAsync(filteredMessage);
                            logger.LogInformation("Info: Received and transferred a message with temperature above the threshold");
                       }
                   }
               }
           }
       }
       //Define the expected schema for the body of incoming messages.
       class MessageBody
       {
           public Machine machine {get; set;}
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
   }
   ```

1. Guarde o ficheiro.

## <a name="build-your-iot-edge-solution"></a>Criar a sua solução do IoT Edge

Na secção anterior, criou uma solução do IoT Edge e adicionou código ao **CSharpFunction** para filtrar mensagens onde a temperatura comunicada da máquina é inferior ao limiar aceitável. Agora, tem de criar a solução como uma imagem de contentor e enviá-la para o registo de contentor.

In this section, you provide the credentials for your container registry for the second time (the first was in the **.env** file of your IoT Edge solution) by signing in locally from your development machine so that Visual Studio Code can push images to your registry.

1. Abra o terminal integrado do VS Code ao selecionar **Ver** > **Terminal**. 

2. Introduza o comando seguinte no terminal integrado e inicie sessão no seu registo de contentor. Utilize o nome do utilizador e o servidor de início de sessão que copiou do registo de contentor do Azure anteriormente.

    ```csh/sh
    docker login -u <ACR username> <ACR login server>
    ```

    When you're prompted for the password, paste the password (it won't be visible in the terminal window) for your container registry and press **Enter**.

    ```csh/sh
    Password: <paste in the ACR password and press enter>
    Login Succeeded
    ```

3. No explorador do VS Code, clique com o botão direito do rato no ficheiro deployment.template.json e selecione **Criar e Emitir solução do IoT Edge**. 

When you tell Visual Studio Code to build your solution, it first takes the information in the deployment template and generates a deployment.json file in a new folder named **config**. Then it runs two commands in the integrated terminal: `docker build` and `docker push`. Estes dois comandos criam o código, colocam as funções em contentores e enviam-no para o registo de contentor que especificou quando inicializou a solução. 

## <a name="view-your-container-image"></a>Ver a imagem de contentor

O Visual Studio Code apresenta uma mensagem de êxito quando a imagem de contentor é enviada para o seu registo de contentor. Se quiser confirmar a operação bem-sucedida, pode ver a imagem no registo. 

1. No portal do Azure, procure o registo de contentor do Azure. 
2. Selecione **Repositórios**.
3. Deverá ver o repositório **csharpfunction** na lista. Selecione este repositório para ver mais detalhes.
4. Na secção **Etiquetas**, deverá ver a etiqueta **0.0.1-amd64**. Esta etiqueta indica a versão e a plataforma da imagem criada. Estes valores estão definidos no ficheiro module.json na pasta CSharpFunction. 

## <a name="deploy-and-run-the-solution"></a>Implementar e executar a solução

Pode utilizar o portal do Azure para implementar o seu módulo de função num dispositivo IoT Edge, como fez nos inícios rápidos. Também pode implementar e monitorizar os módulos a partir do Visual Studio Code. The following sections use the Azure IoT Tools for VS Code that was listed in the prerequisites. Instale a extensão agora, se ainda não o tiver feito. 

1. No explorador do VS Code, expanda a secção **Dispositivos do Hub IoT do Azure**. 

2. Carregue com o botão direito do rato no nome do seu dispositivo do IoT Edge e, em seguida, selecione **Criar Implementação para o dispositivo único**. 

3. Procure a pasta de solução que contém a **CSharpFunction**. Open the config folder, select the **deployment.json** file, and then choose **Select Edge Deployment Manifest**.

4. Atualize a secção **Dispositivos do Hub IoT do Azure**. You should see the new **CSharpFunction** running along with the **SimulatedTemperatureSensor** module and the **$edgeAgent** and **$edgeHub**. It may take a few moments for the new modules to show up. Your IoT Edge device has to retrieve its new deployment information from IoT Hub, start the new containers, and then report the status back to IoT Hub. 

   ![Ver os módulos implementados no VS Code](./media/tutorial-deploy-function/view-modules.png)

## <a name="view-generated-data"></a>Ver os dados gerados

You can see all of the messages that arrive at your IoT hub by running **Azure IoT Hub: Start Monitoring Built-in Event Endpoint** in the command palette.

Também pode filtrar para ver todas as mensagens que chegam ao seu hub IoT a partir de um dispositivo específico. Right-click the device in the **Azure IoT Hub Devices** section and select **Start Monitoring Built-in Event Endpoint**.

To stop monitoring messages, run the command **Azure IoT Hub: Stop Monitoring Built-in Event Endpoint** in the command palette. 

## <a name="clean-up-resources"></a>Limpar recursos

Se planeia avançar para o próximo artigo recomendado, pode manter os recursos e as configurações que criou e reutilizá-los. Também pode continuar a utilizar o mesmo dispositivo IoT Edge como um dispositivo de teste. 

Caso contrário, pode eliminar as configurações locais e os recursos do Azure que criou neste artigo para evitar custos. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou um módulo da função do Azure com código para filtrar os dados não processados que são gerados pelo seu dispositivo IoT Edge. When you're ready to build your own modules, you can learn more about how to [Develop with Azure IoT Edge for Visual Studio Code](how-to-vs-code-develop-module.md). 

Continue para os próximos tutoriais para saber mais sobre outras formas em que o Azure IoT Edge o pode ajudar a transformar os dados em informações empresariais na periferia.

> [!div class="nextstepaction"]
> [Localizar médias com um período flutuante no Azure Stream Analytics](tutorial-deploy-stream-analytics.md)
