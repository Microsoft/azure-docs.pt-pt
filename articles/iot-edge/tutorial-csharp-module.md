---
title: Tutorial - Desenvolver módulo C# para Linux usando Azure IoT Edge
description: Este tutorial mostra-lhe como criar um módulo IoT Edge com código C# e implantá-lo num dispositivo Linux IoT Edge.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/30/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: b7695c825dbdd2c207c87799ea801026f7506bcb
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2021
ms.locfileid: "106219455"
---
# <a name="tutorial-develop-a-c-iot-edge-module-using-linux-containers"></a>Tutorial: Desenvolver um módulo C# IoT Edge utilizando recipientes Linux

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Utilize o Código do Estúdio Visual para desenvolver o código C# e implemente-o num dispositivo em execução Azure IoT Edge.

Pode utilizar os módulos do Azure IoT Edge para implementar código que aplica a sua lógica de negócio diretamente aos seus dispositivos IoT Edge. Este tutorial explica-lhe como criar e implementar um módulo do IoT Edge que filtra dados de sensores. Vai utilizar o dispositivo IoT Edge simulado que criou nos inícios rápidos. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Utilize o Código do Estúdio Visual para criar um módulo IoT Edge baseado no .NET Core 2.1 SDK.
> * Utilize o Visual Studio Code e o Docker para criar uma imagem do Docker e publicá-la no seu registo.
> * Implemente o módulo no seu dispositivo IoT Edge.
> * Veja os dados gerados.

O módulo do IoT Edge que criou neste tutorial filtra os dados de temperatura que são gerados pelo seu dispositivo. Envia apenas mensagens de origem caso a temperatura seja superior a um limiar especificado. Este tipo de análise no Edge é útil para reduzir a quantidade de dados que são comunicados e armazenados na cloud.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial demonstra como desenvolver um módulo em **C#** utilizando **o Código do Estúdio Visual** e implantá-lo num dispositivo IoT Edge. Se estiver a desenvolver módulos utilizando recipientes Windows, vá para [desenvolver um módulo C# IoT Edge utilizando recipientes Windows.](tutorial-csharp-module-windows.md)

Utilize a seguinte tabela para compreender as suas opções de desenvolvimento e implantação de módulos C# utilizando recipientes Linux:

| C# | Visual Studio Code | Visual Studio |
| -- | ------------------ | ------------- |
| **Linux AMD64** | ![Módulos C# para LinuxAMD64 em Código VS](./media/tutorial-c-module/green-check.png) | ![Módulos C# para LinuxAMD64 em Estúdio Visual](./media/tutorial-c-module/green-check.png) |
| **Linux ARM32** | ![Módulos C# para LinuxARM32 em Código VS](./media/tutorial-c-module/green-check.png) | ![Módulos C# para LinuxARM64 em Estúdio Visual](./media/tutorial-c-module/green-check.png) |

>[!NOTE]
>O suporte para dispositivos Linux ARM64 está disponível em [visualização pública](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Para obter mais informações, consulte [os módulos Develop and debug ARM64 IoT Edge no Código do Estúdio Visual (pré-visualização)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

Antes de iniciar este tutorial, deveria ter passado pelo tutorial anterior para configurar o seu ambiente de desenvolvimento, [desenvolver um módulo IoT Edge utilizando recipientes Linux.](tutorial-develop-for-linux.md) Depois de completar este tutorial, já deve ter os seguintes pré-requisitos:

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) no escalão gratuito ou standard no Azure.
* Um dispositivo que funciona Azure IoT Edge com recipientes Linux. Pode utilizar os arranques rápidos para configurar um [dispositivo Linux](quickstart-linux.md) ou [dispositivo Windows](quickstart.md).
* Um registo de contentores, como [o Registo de Contentores de Azure.](../container-registry/index.yml)
* [Código de Estúdio Visual](https://code.visualstudio.com/) configurado com as [Ferramentas Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) configurado para executar contentores Linux.

Para completar estes tutoriais, prepare os seguintes pré-requisitos adicionais na sua máquina de desenvolvimento:

* [Extensão C# para Visual Studio Code (com tecnologia da OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).
* [SDK de .NET Core 2.1](https://www.microsoft.com/net/download).

## <a name="create-a-module-project"></a>Criar um projeto de módulo

Os passos seguintes criam um projeto de módulo IoT Edge para C# utilizando o Código de Estúdio Visual e a extensão Azure IoT Tools. Assim que tiver um modelo de projeto criado, adicione um novo código para que o módulo filtra as mensagens com base nas suas propriedades relatadas.

### <a name="create-a-new-project"></a>Criar um novo projeto

Crie um modelo de solução C# que pode personalizar com o seu próprio código.

1. No Código do Estúdio Visual, selecione **'Vise a**  >  **Paleta de Comando'** para abrir a paleta de comandos VS Code.

2. Na paleta de comandos, introduza e execute o comando **Azure: Iniciar Sessão** e siga as instruções para iniciar sessão na sua conta do Azure. Se já iniciou sessão, pode ignorar este passo.

3. Na paleta de comandos, introduza e execute o comando **Azure IoT Edge: Nova solução do IoT Edge**. Siga as instruções na paleta de comandos para criar a sua solução.

   | Campo | Valor |
   | ----- | ----- |
   | Selecionar pasta | Escolha a localização no computador de desenvolvimento na qual o VS Code vai criar os ficheiros da solução. |
   | Indicar um nome para a solução | Introduza um nome descritivo para a sua solução ou aceite a **solução edges padrão**. |
   | Selecionar modelo de módulo | Escolha **o módulo C. .** |
   | Indicar um nome para o módulo | Atribua o nome **CSharpModule** ao módulo. |
   | Indicar o repositório de imagens do Docker para o módulo | Os repositórios de imagens incluem o nome do seu registo de contentor e o nome da sua imagem de contentor. A sua imagem de contentor está pré-apovoada do nome que forneceu no último passo. Substitua **o local:5000** pelo valor do **servidor login** do registo do seu contentor Azure. Pode recuperar o servidor 'Iniciar sessão' a partir da página de visão geral do seu registo de contentores no portal Azure. <br><br>O repositório de imagem final parece \<registry name\> .azurecr.io/csharpmodule. |

   ![Fornecer repositório de imagens do Docker](./media/tutorial-csharp-module/repository.png)

### <a name="add-your-registry-credentials"></a>Adicionar as credenciais do registo

O ficheiro de ambiente armazena as credenciais do seu registo de contentor e partilha-as com o runtime do IoT Edge. O runtime precisa destas credenciais para solicitar as imagens privadas para o dispositivo IoT Edge. Utilize as credenciais da secção de **chaves de acesso** do seu registo de contentores Azure.

A extensão IoT Edge tenta retirar as credenciais de registo do seu contentor do Azure e povoá-las no ficheiro ambiente. Verifique se as suas credenciais já estão incluídas. Caso contrário, adicione-os agora:

1. No explorador de código VS, abra o ficheiro **.env.**
2. Atualize os campos com o nome de **utilizador** e os valores de **senha** do seu registo de contentores Azure.
3. Guarde este ficheiro.

### <a name="select-your-target-architecture"></a>Selecione a sua arquitetura-alvo

Atualmente, o Visual Studio Code pode desenvolver módulos C# para dispositivos Linux AMD64 e Linux ARM32v7. Você precisa selecionar que arquitetura você está dirigindo com cada solução, porque o recipiente é construído e executado de forma diferente para cada tipo de arquitetura. O padrão é Linux AMD64.

1. Abra a paleta de comando e procure **por Azure IoT Edge: Definir Plataforma-alvo padrão para solução de borda**, ou selecione o ícone de atalho na barra lateral na parte inferior da janela.

2. Na paleta de comando, selecione a arquitetura-alvo da lista de opções. Para este tutorial, estamos a usar uma máquina virtual Ubuntu como dispositivo IoT Edge, por isso manteremos o **amd64** padrão.

### <a name="update-the-module-with-custom-code"></a>Atualizar o módulo com o código personalizado

1. No explorador de código VS, abra **os módulos**  >  **CSharpModule**  >  **Program.cs**.

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

5. Encontre a função **Init.** Esta função cria e configura um objeto **ModuleClient,** que permite ao módulo ligar-se ao tempo de funcionamento do Azure IoT Edge local para enviar e receber mensagens. Depois de criar o **ModuleClient**, o código lê o valor **temperatureThreshold** a partir das propriedades pretendidas do módulo duplo. O código regista uma chamada de retorno para receber mensagens de um hub do IoT Edge através do ponto final **input1**. Substitua o método **SetInputMessageHandlerAsync** por um novo e adicione um método **SetDesiredPropertyUpdateCallbackAsync** para obter atualizações para as propriedades pretendidas. Para fazer esta alteração, substitua a última linha do método **Init** pelo seguinte código:

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

9. No explorador de código VS, abra o **deployment.template.jsno** ficheiro no seu espaço de trabalho de solução IoT Edge.

10. Adicione o módulo duplo **CSharpModule** ao manifesto de implementação. Insira o seguinte conteúdo JSON na parte inferior da secção **modulesContent**, após o módulo duplo **$edgeHub**:

    ```json
       "CSharpModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
    ```

    ![Adicione módulo twin ao modelo de implementação](./media/tutorial-csharp-module/module-twin.png)

11. Guarde o ficheiro deployment.template.json.

## <a name="build-and-push-your-module"></a>Construa e empurre o seu módulo

Na secção anterior, criou uma solução IoT Edge e adicionou código ao CSharpModule. O novo código filtra as mensagens em que a temperatura da máquina reportada se encontra dentro dos limites aceitáveis. Agora, tem de criar a solução como uma imagem de contentor e enviá-la para o registo de contentor.

1. Abra o terminal integrado do Código VS selecionando o Terminal **de Visualização**  >  .

1. Inscreva-se no Docker introduzindo o seguinte comando no terminal. Inicie sessão com o nome de utilizador, palavra-passe e servidor de login do seu registo de contentores Azure. Pode recuperar estes valores a partir da secção de **teclas de acesso** do seu registo no portal Azure.

   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Pode receber um aviso de segurança recomendando a utilização de `--password-stdin` . Embora essa melhor prática seja recomendada para cenários de produção, está fora do âmbito deste tutorial. Para mais informações, consulte a referência de login do [estivador.](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin)

1. No explorador de código VS, clique com o botão direito **no ficheirodeployment.template.js** e selecione a **Solução de Borda De Construção e Pressão IoT**.

   O comando de construção e pressão inicia três operações. Em primeiro lugar, cria uma nova pasta na solução chamada **config** que detém o manifesto de implantação completo, construído a partir de informações no modelo de implementação e outros ficheiros de solução. Em segundo lugar, funciona `docker build` para construir a imagem do contentor com base no arquivo apropriado para a arquitetura do seu alvo. Em seguida, corre `docker push` para empurrar o repositório de imagem para o seu registo de contentores.

   Este processo pode demorar vários minutos na primeira vez, mas é mais rápido da próxima vez que executar os comandos.

## <a name="deploy-and-run-the-solution"></a>Implementar e executar a solução

Utilize o explorador visual Studio Code e a extensão Azure IoT Tools para implantar o projeto do módulo no seu dispositivo IoT Edge. Já tem um manifesto de implantação preparado para o seu cenário, o **deployment.amd64.jsficheiro na** pasta config. Agora tudo o que precisa de fazer é selecionar um dispositivo para receber a implementação.

Certifique-se de que o seu dispositivo IoT Edge está a funcionar.

1. No explorador visual Studio Code, na secção **Azure IoT Hub,** expanda **os Dispositivos** para ver a sua lista de dispositivos IoT.

2. Carregue com o botão direito do rato no nome do seu dispositivo do IoT Edge e, em seguida, selecione **Criar Implementação para o Dispositivo Único**.

3. Selecione a **deployment.amd64.jsno** ficheiro na pasta **config** e, em seguida, clique em **Select Edge Deployment Manifest**. Não utilize o ficheiro deployment.template.json.

4. Sob o seu dispositivo, expanda **os Módulos** para ver uma lista de módulos implantados e em funcionamento. Clique no botão Atualizar. Deverá ver o novo **CSharpModule** a funcionar juntamente com o módulo **SimuladoSteperatureSensor** e o **$edgeAgent** e **$edgeHub**.

    Pode levar alguns minutos para os módulos começarem. O tempo de execução IoT Edge precisa de receber o seu novo manifesto de implantação, retirar as imagens do módulo do tempo de funcionamento do contentor e, em seguida, iniciar cada novo módulo.

## <a name="view-generated-data"></a>Ver os dados gerados

Depois de aplicar o manifesto de implementação no seu dispositivo IoT Edge, o runtime do IoT Edge no dispositivo recolhe as novas informações de implementação e começa a ser executado no mesmo. Quaisquer módulos em execução no dispositivo que não estão incluídos no manifesto de implementação são parados. Quaisquer módulos em falta do dispositivo são iniciados.

1. No explorador visual Studio Code, clique com o botão direito no nome do seu dispositivo IoT Edge e selecione **Start Monitoring Built-in Event Endpoint**.

2. Veja as mensagens que chegam ao seu Hub IoT. Pode demorar algum tempo até que as mensagens cheguem, porque o dispositivo IoT Edge tem de receber a sua nova implementação e iniciar todos os módulos. Em seguida, as alterações que fizemos ao código CModule aguardem até que a temperatura da máquina atinja os 25 graus antes de enviar mensagens. Adiciona também o tipo de mensagem **Alerta** a todas as mensagens que atinjam esse limiar de temperatura.

   ![Ver mensagens que chegam ao IoT Hub](./media/tutorial-csharp-module/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Editar o módulo twin

Usamos o módulo CSharpModule twin no manifesto de implantação para definir o limiar de temperatura em 25 graus. Pode utilizar o módulo twin para alterar a funcionalidade sem ter de atualizar o código do módulo.

1. No Código do Estúdio Visual, expanda os detalhes no seu dispositivo IoT Edge para ver os módulos de execução.

2. Clique com razão **no CSharpModule** e selecione **Editar módulo twin**.

3. Encontre **temperaturaSRestém nas** propriedades desejadas. Mude o seu valor para uma nova temperatura de 5 graus a 10 graus mais alto do que a última temperatura reportada.

4. Guarde o ficheiro twin do módulo.

5. Clique com o botão direito em qualquer lugar do painel de edição twin do módulo e selecione **Update module twin**.

6. Monitorize as mensagens de entrada de dispositivo para nuvem. Deve ver as mensagens paradas até que o novo limiar de temperatura seja atingido.

## <a name="clean-up-resources"></a>Limpar os recursos

Se planeia avançar para o próximo artigo recomendado, pode manter os recursos e as configurações que criou e reutilizá-los. Também pode continuar a utilizar o mesmo dispositivo IoT Edge como um dispositivo de teste.

Caso contrário, pode eliminar as configurações locais e os recursos Azure que utilizou neste artigo para evitar encargos.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou uma função do módulo do IoT Edge que contém código para filtrar dados não processados gerados pelo seu dispositivo IoT Edge.

Pode continuar nos próximos tutoriais para saber como o Azure IoT Edge pode ajudá-lo a implementar serviços em nuvem Azure para processar e analisar dados no limite.

> [!div class="nextstepaction"]
> [Funções](tutorial-deploy-function.md) 
>  [Stream Analytics](tutorial-deploy-stream-analytics.md) 
>  [Aprendizagem automática](tutorial-deploy-machine-learning.md) 
>  [Serviço de Visão Personalizada](tutorial-deploy-custom-vision.md)