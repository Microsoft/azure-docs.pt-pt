---
title: Tutorial implementar uma função do Azure para um dispositivo - Azure IoT Edge | Documentos da Microsoft
description: Neste tutorial, vai desenvolver do Azure funcionar como um módulo do IoT Edge, em seguida, implementá-la para um dispositivo de limite.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/25/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 592aac7e66e1f0720a203c24ac9f000951cfe6f6
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68839523"
---
# <a name="tutorial-deploy-azure-functions-as-iot-edge-modules"></a>Tutorial: Implantar o Azure Functions como módulos IoT Edge

Pode utilizar as Funções do Azure para implementar código que aplica a sua lógica de negócio diretamente nos seus dispositivos Azure IoT Edge. Este tutorial explica-lhe como criar e implementar uma função do Azure que filtra dados de sensores no dispositivo IoT Edge simulado. Vai utilizar o dispositivo IoT Edge simulado que criou na Implementação do Azure IoT Edge num dispositivo simulado em inícios rápidos do [Windows](quickstart.md) ou do [Linux](quickstart-linux.md). Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Utilize o Visual Studio Code para criar uma função do Azure.
> * Utilizar o VS Code e o Docker para criar uma imagem do Docker e publicá-la num registo de contentor.
> * Implementar o módulo a partir do registo de contentor no seu dispositivo IoT Edge.
> * Ver dados filtrados.

<center>

![Diagrama-arquitetura do tutorial, estágio e implantação do módulo de função](./media/tutorial-deploy-function/functions-architecture.png)
</center>

>[!NOTE]
>Os módulos de Funções do Azure no Azure IoT Edge estão em [pré-visualização pública](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

A função do Azure que criou neste tutorial filtra os dados de temperatura que são gerados pelo seu dispositivo. A função envia apenas mensagens de origem para o Hub IoT do Azure quando a temperatura é superior a um limiar especificado. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, você deve ter passado pelo tutorial anterior para configurar seu ambiente de desenvolvimento para o desenvolvimento de contêiner do Linux: [Desenvolva módulos IOT Edge para dispositivos Linux](tutorial-develop-for-linux.md). Ao concluir esse tutorial, você deve ter os seguintes pré-requisitos em vigor: 

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) no escalão gratuito ou standard no Azure.
* Um [dispositivo Linux executando o Azure IOT Edge](quickstart-linux.md)
* Um registro de contêiner, como o [registro de contêiner do Azure](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio Code](https://code.visualstudio.com/) configurado com as [ferramentas de IOT do Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) configurado para executar contêineres do Linux.

Para desenvolver um módulo IoT Edge no com o Azure Functions, instale os seguintes pré-requisitos adicionais em seu computador de desenvolvimento: 

* [Extensão C# para Visual Studio Code (com tecnologia da OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).
* [O SDK .NET Core 2.1](https://www.microsoft.com/net/download).

## <a name="create-a-function-project"></a>Criar um projeto de função

As ferramentas do Azure IoT para Visual Studio Code que você instalou nos pré-requisitos fornecem recursos de gerenciamento, bem como alguns modelos de código. Nesta secção, vai utilizar o Visual Studio Code para criar uma solução do IoT Edge que contém uma função do Azure. 

### <a name="create-a-new-project"></a>Criar um novo projeto

Crie um C# modelo de solução de função que você pode personalizar com seu próprio código.

1. Abra o Visual Studio Code no computador de desenvolvimento.

2. Abra a paleta de comandos do VS Code ao selecionar **Ver** > **Paleta de Comandos**.

3. Na paleta de comandos, insira e execute o comando **Azure IOT Edge: Nova solução**de IOT Edge. Siga as instruções na paleta de comandos para criar a sua solução.

   | Campo | Valor |
   | ----- | ----- |
   | Selecionar pasta | Escolha a localização no computador de desenvolvimento na qual o VS Code vai criar os ficheiros da solução. |
   | Indicar um nome para a solução | Introduza um nome descritivo para a sua solução, como **FunctionSolution**, ou aceite a predefinição. |
   | Selecionar modelo de módulo | Escolher **as funções do Azure - C#** . |
   | Indicar um nome para o módulo | Atribua o nome **CSharpFunction** ao módulo. |
   | Indicar o repositório de imagens do Docker para o módulo | Os repositórios de imagens incluem o nome do seu registo de contentor e o nome da sua imagem de contentor. A imagem de contentor é pré-preenchida no passo anterior. Substitua **localhost:5000** pelo valor do servidor de início de sessão do registo de contentor do Azure Container Registry. Pode obter o servidor de início de sessão na página Overview (Descrição Geral) do registo de contentor no portal do Azure. A cadeia final se parece com \<nome do registo\>.azurecr.io/CSharpFunction. |

   ![Fornecer repositório de imagens do Docker](./media/tutorial-deploy-function/repository.png)

### <a name="add-your-registry-credentials"></a>Adicionar as credenciais do registo

O ficheiro de ambiente armazena as credenciais do seu registo de contentor e partilha-as com o runtime do IoT Edge. O runtime precisa destas credenciais para solicitar as imagens privadas para o dispositivo IoT Edge.

1. No explorador do VS Code, abra o ficheiro .env.
2. Atualize os campos com os valores **nome de utilizador** e **palavra-passe** que copiou do seu Azure Container Registry.
3. Guarde este ficheiro.

### <a name="select-your-target-architecture"></a>Selecione sua arquitetura de destino

Atualmente, Visual Studio Code pode desenvolver módulos C para dispositivos Linux AMD64 e Linux ARM32v7. Você precisa selecionar qual arquitetura está sendo direcionada a cada solução, porque o contêiner é compilado e executado de forma diferente para cada tipo de arquitetura. O padrão é o Linux AMD64. 

1. Abra a paleta de comandos e procure **Azure IOT Edge: Defina a plataforma de destino padrão para**a solução de borda ou selecione o ícone de atalho na barra lateral na parte inferior da janela. 

2. Na paleta de comandos, selecione a arquitetura de destino na lista de opções. Para este tutorial, estamos usando uma máquina virtual Ubuntu como o dispositivo IoT Edge, portanto, manterá o **AMD64**padrão. 

### <a name="update-the-module-with-custom-code"></a>Atualizar o módulo com o código personalizado

Vamos adicionar um código adicional para que o módulo processe as mensagens na borda antes de encaminhá-las ao Hub IoT.

1. Em Visual Studio Code, abra **modules** > **CSharpFunction** > **CSharpFunction.cs**.

1. Substitua os conteúdos do **CSharpFunction.cs** ficheiro com o código a seguir. Esse código recebe telemetria sobre o ambiente e temperatura da máquina e apenas encaminha a mensagem para o IoT Hub, se a temperatura de máquina for superior a um limiar definido.

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
                       var filteredMessage = new Message(messageBytes);
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

Nesta seção, você fornece as credenciais para o registro de contêiner pela segunda vez (o primeiro estava no arquivo **. env** da sua solução de IOT Edge), entrando localmente no computador de desenvolvimento para que Visual Studio Code possa enviar imagens por push para o seu Registry.

1. Abra o terminal integrado do VS Code ao selecionar **Ver** > **Terminal**. 

2. Introduza o comando seguinte no terminal integrado e inicie sessão no seu registo de contentor. Utilize o nome do utilizador e o servidor de início de sessão que copiou do registo de contentor do Azure anteriormente.

    ```csh/sh
    docker login -u <ACR username> <ACR login server>
    ```

    Quando a senha for solicitada, Cole a senha (ela não estará visível na janela do terminal) para o registro de contêiner e pressione **Enter**.

    ```csh/sh
    Password: <paste in the ACR password and press enter>
    Login Succeeded
    ```

3. No explorador do VS Code, clique com o botão direito do rato no ficheiro deployment.template.json e selecione **Criar e Emitir solução do IoT Edge**. 

Quando indicar ao Visual Studio Code para criar a solução, este utiliza primeiro as informações no modelo de implementação e gera um ficheiro deployment.json numa nova pasta com o nome **config**. Em seguida, executa dois comandos no terminal integrado: `docker build` e `docker push`. Estes dois comandos criam o código, colocam as funções em contentores e enviam-no para o registo de contentor que especificou quando inicializou a solução. 

## <a name="view-your-container-image"></a>Ver a imagem de contentor

O Visual Studio Code apresenta uma mensagem de êxito quando a imagem de contentor é enviada para o seu registo de contentor. Se quiser confirmar a operação bem-sucedida, pode ver a imagem no registo. 

1. No portal do Azure, procure o registo de contentor do Azure. 
2. Selecione **Repositórios**.
3. Deverá ver o repositório **csharpfunction** na lista. Selecione este repositório para ver mais detalhes.
4. Na secção **Etiquetas**, deverá ver a etiqueta **0.0.1-amd64**. Esta etiqueta indica a versão e a plataforma da imagem criada. Estes valores estão definidos no ficheiro module.json na pasta CSharpFunction. 

## <a name="deploy-and-run-the-solution"></a>Implementar e executar a solução

Pode utilizar o portal do Azure para implementar o seu módulo de função num dispositivo IoT Edge, como fez nos inícios rápidos. Também pode implementar e monitorizar os módulos a partir do Visual Studio Code. As seções a seguir usam as ferramentas de IoT do Azure para VS Code que foram listadas nos pré-requisitos. Instale a extensão agora, se ainda não o tiver feito. 

1. No explorador do VS Code, expanda a secção **Dispositivos do Hub IoT do Azure**. 

2. Carregue com o botão direito do rato no nome do seu dispositivo do IoT Edge e, em seguida, selecione **Criar Implementação para o dispositivo único**. 

3. Procure a pasta de solução que contém a **CSharpFunction**. Abra a pasta de configuração, selecione o **deployment.json** do ficheiro e, em seguida, escolha **selecione o manifesto de implantação do Edge**.

4. Atualize a secção **Dispositivos do Hub IoT do Azure**. Você deve ver o novo **CSharpFunction** em execução junto com o módulo **SimulatedTemperatureSensor** e o **$edgeAgent** e **$edgeHub**. Pode demorar alguns momentos para que os novos módulos sejam apresentados. Dispositivo IoT Edge tem-se para obter novas informações de implementação do IoT Hub, iniciar novos contentores e, em seguida, voltar a comunicar o estado para o IoT Hub. 

   ![Ver os módulos implementados no VS Code](./media/tutorial-deploy-function/view-modules.png)

## <a name="view-generated-data"></a>Ver os dados gerados

Você pode ver todas as mensagens que chegam ao seu hub IOT executando **o Hub IOT do Azure: Comece a monitorar o ponto de extremidade** de evento interno na paleta de comandos.

Também pode filtrar para ver todas as mensagens que chegam ao seu hub IoT a partir de um dispositivo específico. Clique com o botão direito do mouse no dispositivo na seção **dispositivos do Hub IOT do Azure** e selecione **Iniciar Monitoramento do ponto de extremidade do evento interno**.

Para interromper o monitoramento de mensagens, execute **o comando Hub IOT do Azure: Pare de monitorar o ponto de extremidade** de evento interno na paleta de comandos. 

## <a name="clean-up-resources"></a>Limpar recursos

Se planeia avançar para o próximo artigo recomendado, pode manter os recursos e as configurações que criou e reutilizá-los. Também pode continuar a utilizar o mesmo dispositivo IoT Edge como um dispositivo de teste. 

Caso contrário, pode eliminar as configurações locais e os recursos do Azure que criou neste artigo para evitar custos. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, criou um módulo da função do Azure com código para filtrar os dados não processados que são gerados pelo seu dispositivo IoT Edge. Quando estiver pronto para criar seus próprios módulos, você pode aprender mais sobre como [desenvolver com Azure IOT Edge para Visual Studio Code](how-to-vs-code-develop-module.md). 

Continue para os próximos tutoriais para saber mais sobre outras formas em que o Azure IoT Edge o pode ajudar a transformar os dados em informações empresariais na periferia.

> [!div class="nextstepaction"]
> [Localizar médias com um período flutuante no Azure Stream Analytics](tutorial-deploy-stream-analytics.md)
