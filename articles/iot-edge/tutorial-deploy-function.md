---
title: Tutorial implementar uma função do Azure para um dispositivo - Azure IoT Edge | Documentos da Microsoft
description: Neste tutorial, vai desenvolver do Azure funcionar como um módulo do IoT Edge, em seguida, implementá-la para um dispositivo de limite.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/04/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 1fba2c4e5191d4c827035362a8eb6876fcbb67cc
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58081751"
---
# <a name="tutorial-deploy-azure-functions-as-iot-edge-modules"></a>Tutorial: Implementar o Azure funciona como módulos do IoT Edge

Pode utilizar as Funções do Azure para implementar código que aplica a sua lógica de negócio diretamente nos seus dispositivos Azure IoT Edge. Este tutorial explica-lhe como criar e implementar uma função do Azure que filtra dados de sensores no dispositivo IoT Edge simulado. Vai utilizar o dispositivo IoT Edge simulado que criou na Implementação do Azure IoT Edge num dispositivo simulado em inícios rápidos do [Windows](quickstart.md) ou do [Linux](quickstart-linux.md). Neste tutorial, ficará a saber como:     

> [!div class="checklist"]
> * Utilize o Visual Studio Code para criar uma função do Azure.
> * Utilizar o VS Code e o Docker para criar uma imagem do Docker e publicá-la num registo de contentor.
> * Implementar o módulo a partir do registo de contentor no seu dispositivo IoT Edge.
> * Ver dados filtrados.

<center>

![Diagrama - arquitetura do Tutorial, testar e implementar módulo de função](./media/tutorial-deploy-function/functions-architecture.png)
</center>

>[!NOTE]
>Os módulos de Funções do Azure no Azure IoT Edge estão em [pré-visualização pública](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

A função do Azure que criou neste tutorial filtra os dados de temperatura que são gerados pelo seu dispositivo. A função envia apenas mensagens de origem para o Hub IoT do Azure quando a temperatura é superior a um limiar especificado. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Um dispositivo Azure IoT Edge:

* Pode configurar o computador de desenvolvimento ou uma máquina virtual como um dispositivo do Edge ao seguir os passos no guia de introdução para [Linux](quickstart-linux.md) ou [dispositivos Windows](quickstart.md).

Recursos da cloud:

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) no escalão gratuito ou standard no Azure. 

Recursos de desenvolvimento:

* [Visual Studio Code](https://code.visualstudio.com/). 
* [Extensão C# para Visual Studio Code (com tecnologia da OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).
* [Ferramentas de IoT do Azure para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools). 
* [O SDK .NET Core 2.1](https://www.microsoft.com/net/download).
* [Docker CE](https://docs.docker.com/install/). 

## <a name="create-a-container-registry"></a>Criar um registo de contentores

Neste tutorial, vai utilizar as ferramentas de IoT do Azure para Visual Studio Code para criar um módulo e criar um **imagem de contentor** dos arquivos. Em seguida, vai enviar essa imagem para um **registo** que armazena e gere as suas imagens. Por fim, vai implementar a imagem a partir do registo para ser executada no seu dispositivo IoT Edge.  

Pode utilizar qualquer registo compatível com o Docker para armazenar as imagens de contentor. São dois populares serviços de registo de Docker [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) e [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). Este tutorial utiliza o Azure Container Registry. 

1. No [Portal do Azure](https://portal.azure.com), selecione **Criar um recurso** > **Contentores** > **Registo de Contentor**.

    ![Criar um registo de contentor no portal do Azure](./media/tutorial-deploy-function/create-container-registry.png)

2. Indique os valores seguintes para criar o seu registo de contentor:

   | Campo | Valor | 
   | ----- | ----- |
   | Nome de registo | Indique um nome exclusivo. |
   | Subscrição | Selecione uma subscrição na lista pendente. |
   | Grupo de recursos | Recomendamos que utilize o mesmo grupo de recursos para todos os recursos de teste que criou durante os inícios rápidos e tutoriais do IoT Edge. Por exemplo, **IoTEdgeResources**. |
   | Localização | Escolha uma localização perto de si. |
   | Utilizador admin | Defina para **Ativar**. |
   | SKU | Selecione **Básico**. | 

5. Selecione **Criar**.

6. Depois de o registo de contentores ser criado, navegue para o mesmo e selecione **Chaves de acesso**. 

7. Copie os valores de **Servidor de início de sessão**, **Nome de utilizador** e **Palavra-passe**. Utilize estes valores mais tarde no tutorial para fornecer acesso ao registo de contentor. 

## <a name="create-a-function-project"></a>Criar um projeto de função

As ferramentas de IoT do Azure para Visual Studio Code instalado nos pré-requisitos fornece capacidades de gestão, bem como alguns modelos de código. Nesta secção, vai utilizar o Visual Studio Code para criar uma solução do IoT Edge que contém uma função do Azure. 

1. Abra o Visual Studio Code no computador de desenvolvimento.

2. Abra a paleta de comandos do VS Code ao selecionar **Ver** > **Paleta de Comandos**.

3. Na paleta de comandos, introduza e execute o comando **Azure IoT Edge: Nova solução de IoT Edge**. Siga as instruções na paleta de comandos para criar a sua solução.

   | Campo | Valor |
   | ----- | ----- |
   | Selecionar pasta | Escolha a localização no computador de desenvolvimento na qual o VS Code vai criar os ficheiros da solução. |
   | Indicar um nome para a solução | Introduza um nome descritivo para a sua solução, como **FunctionSolution**, ou aceite a predefinição. |
   | Selecionar modelo de módulo | Escolher **as funções do Azure - C#** . |
   | Indicar um nome para o módulo | Atribua o nome **CSharpFunction** ao módulo. |
   | Indicar o repositório de imagens do Docker para o módulo | Os repositórios de imagens incluem o nome do seu registo de contentor e o nome da sua imagem de contentor. A imagem de contentor é pré-preenchida no passo anterior. Substitua **localhost:5000** pelo valor do servidor de início de sessão do registo de contentor do Azure Container Registry. Pode obter o servidor de início de sessão na página Overview (Descrição Geral) do registo de contentor no portal do Azure. A cadeia final se parece com \<nome do registo\>.azurecr.io/CSharpFunction. |

   ![Fornecer repositório de imagens do Docker](./media/tutorial-deploy-function/repository.png)

4. A janela do VS Code carrega a sua área de trabalho da solução do IoT Edge: uma \.pasta do vscode, uma pasta de módulos, um ficheiro modelo do manifesto de implementação. e um \.ficheiro env. No explorador do VS Code, abra **módulos** > **CSharpFunction** > **CSharpFunction.cs**.

5. Substitua os conteúdos do **CSharpFunction.cs** ficheiro com o código a seguir. Esse código recebe telemetria sobre o ambiente e temperatura da máquina e apenas encaminha a mensagem para o IoT Hub, se a temperatura de máquina for superior a um limiar definido.

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
                       // Send the message to the output as the temperature value is greater than the threashold.
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

6. Guarde o ficheiro.

## <a name="build-your-iot-edge-solution"></a>Criar a sua solução do IoT Edge

Na secção anterior, criou uma solução do IoT Edge e adicionou código ao **CSharpFunction** para filtrar mensagens onde a temperatura comunicada da máquina é inferior ao limiar aceitável. Agora, tem de criar a solução como uma imagem de contentor e enviá-la para o registo de contentor.

Nesta secção, vai apresentar as credenciais para o seu registo de contentor duas vezes. A primeira é para iniciar sessão localmente no seu computador de desenvolvimento, para que o Visual Studio Code possa enviar imagens para o registo. O segundo é no ficheiro **.env** da sua solução de IoT Edge, o que lhe dá permissões do dispositivo do IoT Edge para pedir imagens do seu registo. 

1. Abra o terminal integrado do VS Code ao selecionar **Ver** > **Terminal**. 

2. Introduza o comando seguinte no terminal integrado e inicie sessão no seu registo de contentor. Utilize o nome do utilizador e o servidor de início de sessão que copiou do registo de contentor do Azure anteriormente.
     
    ```csh/sh
    docker login -u <ACR username> <ACR login server>
    ```

    Quando lhe for pedida a palavra-passe, cole-a no seu registo de contentor e prima **Enter**.

    ```csh/sh
    Password: <paste in the ACR password and press enter>
    Login Succeeded
    ```

2. No explorador do VS Code, abra o ficheiro **deployment.template.json** na área de trabalho da solução do IoT Edge. Este ficheiro indica ao runtime do IoT Edge que módulos deve implementar num dispositivo. Tenha em atenção que o módulo de Função **CSharpFunction** está listado juntamente com o módulo **tempSensor** que apresenta dados de teste. Para saber mais sobre os manifestos de implementação, veja [Compreender como os módulos do IoT Edge podem ser utilizados, configurados e reutilizados](module-composition.md).

   ![Veja o seu módulo no manifesto de implementação](./media/tutorial-deploy-function/deployment-template.png)

3. Abra o ficheiro **.env** na sua área de trabalho de solução do IoT Edge. Este ficheiro git-ignored armazena as suas credenciais de registo de contentor, para que não tenha de as colocar no modelo de manifesto de implementação. Indique o **nome de utilizador** e a **palavra-passe** para o seu registo de contentor. 

5. Guarde este ficheiro.

6. No explorador do VS Code, clique com o botão direito do rato no ficheiro deployment.template.json e selecione **Criar e Emitir solução do IoT Edge**. 

Quando indicar ao Visual Studio Code para criar a solução, este utiliza primeiro as informações no modelo de implementação e gera um ficheiro deployment.json numa nova pasta com o nome **config**. Em seguida, executa dois comandos no terminal integrado: `docker build` e `docker push`. Estes dois comandos criam o código, colocam as funções em contentores e enviam-no para o registo de contentor que especificou quando inicializou a solução. 

## <a name="view-your-container-image"></a>Ver a imagem de contentor

O Visual Studio Code apresenta uma mensagem de êxito quando a imagem de contentor é enviada para o seu registo de contentor. Se quiser confirmar a operação bem-sucedida, pode ver a imagem no registo. 

1. No portal do Azure, procure o registo de contentor do Azure. 
2. Selecione **Repositórios**.
3. Deverá ver o repositório **csharpfunction** na lista. Selecione este repositório para ver mais detalhes.
4. Na secção **Etiquetas**, deverá ver a etiqueta **0.0.1-amd64**. Esta etiqueta indica a versão e a plataforma da imagem criada. Estes valores estão definidos no ficheiro module.json na pasta CSharpFunction. 

## <a name="deploy-and-run-the-solution"></a>Implementar e executar a solução

Pode utilizar o portal do Azure para implementar o seu módulo de função num dispositivo IoT Edge, como fez nos inícios rápidos. Também pode implementar e monitorizar os módulos a partir do Visual Studio Code. As secções seguintes utilizam as ferramentas de IoT do Azure para VS Code, que foi descritas nos pré-requisitos. Instale a extensão agora, se ainda não o tiver feito. 

1. Abra a paleta de comandos do VS Code ao selecionar **Ver** > **Paleta de Comandos**.

2. Procure e execute o comando **Azure: Inicie sessão no**. Siga as instruções para iniciar sessão na conta do Azure. 

3. Na paleta de comandos, procure e execute o comando **IoT Hub do Azure: Selecione o IoT Hub**. 

4. Selecione a subscrição que tem o seu hub IoT e, em seguida, selecione o hub IoT ao qual quer aceder.

5. No explorador do VS Code, expanda a secção **Dispositivos do Hub IoT do Azure**. 

6. Carregue com o botão direito do rato no nome do seu dispositivo do IoT Edge e, em seguida, selecione **Criar Implementação para o dispositivo único**. 

7. Procure a pasta de solução que contém a **CSharpFunction**. Abra a pasta de configuração, selecione o **deployment.json** do ficheiro e, em seguida, escolha **selecione o manifesto de implantação do Edge**.

8. Atualize a secção **Dispositivos do Hub IoT do Azure**. Deverá ver o novo **CSharpFunction** em execução, juntamente com o módulo **TempSensor**, bem como **$edgeAgent** e **$edgeHub**. Pode demorar alguns momentos para que os novos módulos sejam apresentados. Dispositivo IoT Edge tem-se para obter novas informações de implementação do IoT Hub, iniciar novos contentores e, em seguida, voltar a comunicar o estado para o IoT Hub. 

   ![Ver os módulos implementados no VS Code](./media/tutorial-deploy-function/view-modules.png)

## <a name="view-generated-data"></a>Ver os dados gerados

Pode ver todas as mensagens que chegam ao IoT hub através da execução **IoT Hub do Azure: Iniciar a monitorização de mensagem de D2C** na paleta de comandos.

Também pode filtrar para ver todas as mensagens que chegam ao seu hub IoT a partir de um dispositivo específico. Clique com o botão direito do rato na secção **Dispositivos do Hub IoT do Azure** e selecione **Iniciar Monitorização de Mensagens D2C**.

Para parar a monitorização de mensagens, execute o comando **IoT Hub do Azure: Parar a monitorização de mensagens D2C** na paleta de comandos. 


## <a name="clean-up-resources"></a>Limpar recursos

Se planeia avançar para o próximo artigo recomendado, pode manter os recursos e as configurações que criou e reutilizá-los. Também pode continuar a utilizar o mesmo dispositivo IoT Edge como um dispositivo de teste. 

Caso contrário, pode eliminar as configurações locais e os recursos do Azure que criou neste artigo para evitar custos. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, criou um módulo da função do Azure com código para filtrar os dados não processados que são gerados pelo seu dispositivo IoT Edge. Quando estiver pronto para criar os seus próprios módulos, pode saber mais sobre como [Desenvolver funções do Azure com o Azure IoT Edge para Visual Studio Code](how-to-develop-csharp-function.md). 

Continue para os próximos tutoriais para saber mais sobre outras formas em que o Azure IoT Edge o pode ajudar a transformar os dados em informações empresariais na periferia.

> [!div class="nextstepaction"]
> [Localizar médias com um período flutuante no Azure Stream Analytics](tutorial-deploy-stream-analytics.md)

