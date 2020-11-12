---
title: 'Tutorial: Implementar funções de Azure como módulos - Azure IoT Edge'
description: Neste tutorial, desenvolve-se uma Função Azure como módulo IoT Edge e, em seguida, insumou-a num dispositivo de borda.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/29/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: 45f990e5426516db5537319d07c11aa705e462e7
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94534862"
---
# <a name="tutorial-deploy-azure-functions-as-iot-edge-modules"></a>Tutorial: Implementar funções de Azure como módulos IoT Edge

Pode utilizar as Funções do Azure para implementar código que aplica a sua lógica de negócio diretamente nos seus dispositivos Azure IoT Edge. Este tutorial acompanha-o através da criação e implementação de uma Função Azure que filtra dados do sensor no dispositivo IoT Edge simulado. Vai utilizar o dispositivo IoT Edge simulado que criou na Implementação do Azure IoT Edge num dispositivo simulado em inícios rápidos do [Windows](quickstart.md) ou do [Linux](quickstart-linux.md). Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Utilize o Código do Estúdio Visual para criar uma Função Azure.
> * Utilizar o VS Code e o Docker para criar uma imagem do Docker e publicá-la num registo de contentor.
> * Implementar o módulo a partir do registo de contentor no seu dispositivo IoT Edge.
> * Ver dados filtrados.

<center>

![Diagrama - Arquitetura tutorial: módulo de função de palco e implantação](./media/tutorial-deploy-function/functions-architecture.png)
</center>

A Função Azure que cria neste tutorial filtra os dados de temperatura gerados pelo seu dispositivo. A Função só envia mensagens a montante para O Hub IoT Azure quando a temperatura está acima de um limiar especificado.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, deveria ter passado pelo tutorial anterior para configurar o seu ambiente de desenvolvimento para o desenvolvimento de recipientes Linux: [Desenvolver módulos IoT Edge para dispositivos Linux](tutorial-develop-for-linux.md). Ao completar este tutorial, deverá ter os seguintes pré-requisitos no lugar:

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) no escalão gratuito ou standard no Azure.
* Um [dispositivo Linux executando Azure IoT Edge](quickstart-linux.md)
* Um registo de contentores, como [o Registo de Contentores de Azure.](../container-registry/index.yml)
* [Código de Estúdio Visual](https://code.visualstudio.com/) configurado com as [Ferramentas Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) configurado para executar contentores Linux.

Para desenvolver um módulo IoT Edge com Funções Azure, instale os seguintes pré-requisitos adicionais na sua máquina de desenvolvimento:

* [Extensão C# para Visual Studio Code (com tecnologia da OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).
* [O SDK .NET Core 2.1](https://www.microsoft.com/net/download).

## <a name="create-a-function-project"></a>Criar um projeto de função

As Ferramentas Azure IoT para Código de Estúdio Visual que instalou nos pré-requisitos fornecem capacidades de gestão, bem como alguns modelos de código. Nesta secção, utiliza o Código do Estúdio Visual para criar uma solução IoT Edge que contém uma Função Azure.

### <a name="create-a-new-project"></a>Criar um novo projeto

Crie um modelo de solução de função C# que possa personalizar com o seu próprio código.

1. Abra o Visual Studio Code no computador de desenvolvimento.

2. Abra a paleta de comandos VS Code selecionando **a**  >  **Paleta de Comando ver .**

3. Na paleta de comandos, introduza e execute o comando **Azure IoT Edge: Nova solução do IoT Edge**. Siga as instruções na paleta de comandos para criar a sua solução.

   | Campo | Valor |
   | ----- | ----- |
   | Selecionar pasta | Escolha a localização no computador de desenvolvimento na qual o VS Code vai criar os ficheiros da solução. |
   | Indicar um nome para a solução | Introduza um nome descritivo para a sua solução, como **a FunÇãoSolution,** ou aceite o padrão. |
   | Selecionar modelo de módulo | Escolha **funções Azure - C#**. . |
   | Indicar um nome para o módulo | Atribua o nome **CSharpFunction** ao módulo. |
   | Indicar o repositório de imagens do Docker para o módulo | Os repositórios de imagens incluem o nome do seu registo de contentor e o nome da sua imagem de contentor. A imagem de contentor é pré-preenchida no passo anterior. Substitua **o local:5000** pelo valor do **servidor login** do registo do seu contentor Azure. Pode recuperar o servidor 'Iniciar sessão' a partir da página de visão geral do seu registo de contentores no portal Azure. A corda final parece \<registry name\> .azurecr.io/CSharpFunction. |

   ![Fornecer repositório de imagens do Docker](./media/tutorial-deploy-function/repository.png)

### <a name="add-your-registry-credentials"></a>Adicionar as credenciais do registo

O ficheiro de ambiente armazena as credenciais do seu registo de contentor e partilha-as com o runtime do IoT Edge. O runtime precisa destas credenciais para solicitar as imagens privadas para o dispositivo IoT Edge.

A extensão IoT Edge tenta retirar as credenciais de registo do seu contentor do Azure e povoá-las no ficheiro ambiente. Verifique se as suas credenciais já estão incluídas. Caso contrário, adicione-os agora:

1. No explorador do VS Code, abra o ficheiro .env.
2. Atualize os campos com os valores **nome de utilizador** e **palavra-passe** que copiou do seu Azure Container Registry.
3. Guarde este ficheiro.

### <a name="select-your-target-architecture"></a>Selecione a sua arquitetura-alvo

Atualmente, o Visual Studio Code pode desenvolver módulos C para dispositivos Linux AMD64 e Linux ARM32v7. Você precisa selecionar que arquitetura você está dirigindo com cada solução, porque o recipiente é construído e executado de forma diferente para cada tipo de arquitetura. O padrão é Linux AMD64.

1. Abra a paleta de comando e procure **por Azure IoT Edge: Definir Plataforma-alvo padrão para solução de borda** , ou selecione o ícone de atalho na barra lateral na parte inferior da janela.

2. Na paleta de comando, selecione a arquitetura-alvo da lista de opções. Para este tutorial, estamos a usar uma máquina virtual Ubuntu como dispositivo IoT Edge, por isso manteremos o **amd64** padrão.

### <a name="update-the-module-with-custom-code"></a>Atualizar o módulo com o código personalizado

Vamos adicionar algum código adicional para que o módulo processe as mensagens na borda antes de encaminhá-las para o IoT Hub.

1. No Código do Estúdio Visual, abra **os módulos**  >  **CSharpFunction**  >  **CSharpFunction.cs**.

1. Substitua o conteúdo do ficheiro **CSharpFunction.cs** pelo seguinte código. Este código recebe telemetria sobre temperatura ambiente e máquina, e apenas encaminha a mensagem para o Hub IoT se a temperatura da máquina estiver acima de um limiar definido.

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

## <a name="build-and-push-your-iot-edge-solution"></a>Construa e empurre a sua solução IoT Edge

Na secção anterior, criou uma solução IoT Edge e modificou o **CSharpFunction** para filtrar mensagens com temperaturas de máquina reportadas abaixo do limiar aceitável. Agora, tem de criar a solução como uma imagem de contentor e enviá-la para o registo de contentor.

1. Abra o terminal integrado do Código VS selecionando o Terminal **de Visualização**  >  **Terminal**.

2. Inscreva-se no Docker introduzindo o seguinte comando no terminal. Inicie sessão com o nome de utilizador, palavra-passe e servidor de login do seu registo de contentores Azure. Pode recuperar estes valores a partir da secção de **teclas de acesso** do seu registo no portal Azure.

   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Pode receber um aviso de segurança recomendando a utilização de `--password-stdin` . Embora essa melhor prática seja recomendada para cenários de produção, está fora do âmbito deste tutorial. Para mais informações, consulte a referência de login do [estivador.](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin)

3. No explorador de código VS, clique com o botão direito **no ficheirodeployment.template.js** e selecione a **Solução de Borda De Construção e Pressão IoT**.

   O comando de construção e pressão inicia três operações. Em primeiro lugar, cria uma nova pasta na solução chamada **config** que detém o manifesto de implantação completo, que é construído a partir de informações no modelo de implementação e outros ficheiros de solução. Em segundo lugar, funciona `docker build` para construir a imagem do contentor com base no arquivo apropriado para a arquitetura do seu alvo. Em seguida, corre `docker push` para empurrar o repositório de imagem para o seu registo de contentores.

   Este processo pode demorar vários minutos na primeira vez, mas é mais rápido da próxima vez que executar os comandos.

## <a name="view-your-container-image"></a>Ver a imagem de contentor

O Visual Studio Code apresenta uma mensagem de êxito quando a imagem de contentor é enviada para o seu registo de contentor. Se quiser confirmar a operação bem-sucedida, pode ver a imagem no registo.

1. No portal do Azure, procure o registo de contentor do Azure.
2. Selecione **Repositórios**.
3. Deverá ver o repositório **csharpfunction** na lista. Selecione este repositório para ver mais detalhes.
4. Na secção **Etiquetas** , deverá ver a etiqueta **0.0.1-amd64**. Esta etiqueta indica a versão e a plataforma da imagem criada. Estes valores estão definidos no ficheiro module.json na pasta CSharpFunction.

## <a name="deploy-and-run-the-solution"></a>Implementar e executar a solução

Pode utilizar o portal Azure para implantar o seu módulo De função num dispositivo IoT Edge, como fez nos arranques rápidos. Também pode implementar e monitorizar os módulos a partir do Visual Studio Code. As secções seguintes utilizam as ferramentas Azure IoT para código VS que foram listadas nos pré-requisitos. Instale a extensão agora, se ainda não o tiver feito.

1. No explorador visual Studio Code, na secção **Azure IoT Hub,** expanda **os Dispositivos** para ver a sua lista de dispositivos IoT.

2. Clique com o botão direito no nome do seu dispositivo IoT Edge e, em seguida, selecione **Criar Implementação para dispositivo único**.

3. Procure a pasta de solução que contém a **CSharpFunction**. Abra a pasta config, selecione a **deployment.amd64.jsno** ficheiro e, em seguida, escolha Select Edge **Deployment Manifest**.

4. Sob o seu dispositivo, expanda **os Módulos** para ver uma lista de módulos implantados e em funcionamento. Clique no botão Atualizar. Deverá ver o novo **CSharpFunction** a funcionar juntamente com o módulo **SimuladoTemperatureSensor** e o **$edgeAgent** e **$edgeHub**.

    Pode levar alguns momentos para os novos módulos aparecerem. O seu dispositivo IoT Edge tem de recuperar as suas novas informações de implantação a partir do IoT Hub, iniciar os novos contentores e, em seguida, reportar o estado de volta ao IoT Hub.

   ![Ver os módulos implementados no VS Code](./media/tutorial-deploy-function/view-modules.png)

## <a name="view-the-generated-data"></a>Ver os dados gerados

Pode ver todas as mensagens que chegam ao seu hub IoT executando **Azure IoT Hub: Iniciar a monitorização do ponto final do evento incorporado** na paleta de comando.

Também pode filtrar para ver todas as mensagens que chegam ao seu hub IoT a partir de um dispositivo específico. Clique com o botão direito no dispositivo na secção **Azure IoT Hub Devices** e selecione **Start Monitoring Built-in Event Endpoint**.

Para parar de monitorizar as mensagens, executar o comando **Azure IoT Hub: Parar de monitorizar** o ponto final do evento incorporado na paleta de comando.

## <a name="clean-up-resources"></a>Limpar recursos

Se planeia avançar para o próximo artigo recomendado, pode manter os recursos e as configurações que criou e reutilizá-los. Também pode continuar a utilizar o mesmo dispositivo IoT Edge como um dispositivo de teste.

Caso contrário, pode eliminar as configurações locais e os recursos do Azure que criou neste artigo para evitar custos.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou um módulo Azure Function com código para filtrar dados brutos gerados pelo seu dispositivo IoT Edge. Quando estiver pronto para construir os seus próprios módulos, poderá aprender mais sobre como [desenvolver com Azure IoT Edge para Código de Estúdio Visual.](how-to-vs-code-develop-module.md)

Continue para os próximos tutoriais para saber mais sobre outras formas em que o Azure IoT Edge o pode ajudar a transformar os dados em informações empresariais na periferia.

> [!div class="nextstepaction"]
> [Localizar médias com um período flutuante no Azure Stream Analytics](tutorial-deploy-stream-analytics.md)
