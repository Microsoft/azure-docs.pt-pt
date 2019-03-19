---
title: Tutorial criar personalizado C# módulo - Azure IoT Edge | Documentos da Microsoft
description: Este tutorial mostra como criar um módulo do IoT Edge com código C# e implementá-lo num dispositivo Edge.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/04/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: f4afd83e31cf724e734b4e86045f8404e65753c5
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58088031"
---
# <a name="tutorial-develop-a-c-iot-edge-module-and-deploy-to-your-simulated-device"></a>Tutorial: Desenvolver um C# módulo do IoT Edge e implementar no seu dispositivo simulado

Pode utilizar os módulos do Azure IoT Edge para implementar código que aplica a sua lógica de negócio diretamente aos seus dispositivos IoT Edge. Este tutorial explica-lhe como criar e implementar um módulo do IoT Edge que filtra dados de sensores. Vai utilizar o dispositivo IoT Edge simulado que criou na Implementação do Azure IoT Edge num dispositivo simulado em inícios rápidos do [Windows](quickstart.md) ou do [Linux](quickstart-linux.md). Neste tutorial, ficará a saber como:    

> [!div class="checklist"]
> * Utilize o Visual Studio Code para criar um módulo do IoT Edge que baseia-se o SDK 2.1 do .NET Core.
> * Utilize o Visual Studio Code e o Docker para criar uma imagem do Docker e publicá-la no seu registo.
> * Implemente o módulo no seu dispositivo IoT Edge.
> * Veja os dados gerados.

>[!NOTE]
>Também pode utilizar [Visual Studio 2017 desenvolver, depurar e implementar módulos IoT Edge](how-to-visual-studio-develop-csharp-module.md).

O módulo do IoT Edge que criou neste tutorial filtra os dados de temperatura que são gerados pelo seu dispositivo. Envia apenas mensagens de origem caso a temperatura seja superior a um limiar especificado. Este tipo de análise no Edge é útil para reduzir a quantidade de dados que são comunicados e armazenados na cloud. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Pré-requisitos

Um dispositivo Azure IoT Edge:

* Pode seguir os passos no início rápido para [dispositivos Linux](quickstart-linux.md) ou [Windows](quickstart.md) para utilizar o seu computador de desenvolvimento ou uma máquina virtual como um dispositivo Edge.

Recursos da cloud:

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) no escalão gratuito ou standard no Azure. 

Recursos de desenvolvimento:

* [Visual Studio Code](https://code.visualstudio.com/). 
* [Extensão C# para Visual Studio Code (com tecnologia da OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).
* [Ferramentas do Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) para Visual Studio Code. 
* [SDK de .NET Core 2.1](https://www.microsoft.com/net/download).
* [Docker CE](https://docs.docker.com/install/)


## <a name="create-a-container-registry"></a>Criar um registo de contentores

Neste tutorial, vai utilizar as ferramentas de IoT do Azure para Visual Studio Code para criar um módulo e criar um **imagem de contentor** dos arquivos. Em seguida, vai enviar essa imagem para um **registo** que armazena e gere as suas imagens. Por fim, vai implementar a imagem a partir do registo para ser executada no seu dispositivo IoT Edge.  

Pode utilizar qualquer registo compatível com o Docker para armazenar as imagens de contentor. São dois populares serviços de registo de Docker [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) e [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). Este tutorial utiliza o Azure Container Registry. 

Se ainda não tiver um registo de contentor, siga estes passos para criar uma nova no Azure:

1. No [Portal do Azure](https://portal.azure.com), selecione **Criar um recurso** > **Contentores** > **Registo de Contentor**.

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

## <a name="create-an-iot-edge-module-project"></a>Criar um projeto do módulo do IoT Edge
Os passos seguintes criam um projeto de módulo do IoT Edge que se baseia em .NET Core 2.0 SDK com o Visual Studio Code e as ferramentas de IoT do Azure.

### <a name="create-a-new-solution"></a>Criar uma nova solução

Crie um modelo de solução C# que pode personalizar com o seu próprio código. 

1. No Visual Studio Code, selecione **Ver** > **Paleta de Comandos** para abrir a paleta de comandos do VS Code. 

2. Na paleta de comandos, introduza e execute o comando **Azure: Inicie sessão no** e siga as instruções para iniciar sessão na sua conta do Azure. Se já iniciou sessão, pode ignorar este passo.

3. Na paleta de comandos, introduza e execute o comando **Azure IoT Edge: Nova solução de IoT Edge**. Siga as instruções na paleta de comandos para criar a sua solução.

   | Campo | Valor |
   | ----- | ----- |
   | Selecionar pasta | Escolha a localização no computador de desenvolvimento na qual o VS Code vai criar os ficheiros da solução. |
   | Indicar um nome para a solução | Introduza um nome descritivo para a sua solução ou aceite a predefinição **EdgeSolution**. |
   | Selecionar modelo de módulo | Escolher  **C# módulo**. |
   | Indicar um nome para o módulo | Atribua o nome **CSharpModule** ao módulo. |
   | Indicar o repositório de imagens do Docker para o módulo | Os repositórios de imagens incluem o nome do seu registo de contentor e o nome da sua imagem de contentor. A imagem de contentor é pré-preenchida do nome que indicou no último passo. Substitua **localhost:5000** pelo valor do servidor de início de sessão do registo de contentor do Azure Container Registry. Pode obter o servidor de início de sessão na página Overview (Descrição Geral) do registo de contentor no portal do Azure. <br><br>O repositório de imagem final se parece com \<nome do registo\>.azurecr.io/csharpmodule. |
 
   ![Fornecer repositório de imagens do Docker](./media/tutorial-csharp-module/repository.png)

A janela do VS Code carrega a área de trabalho da solução do IoT Edge. A área de trabalho da solução contém cinco componentes de nível superior. A pasta **módulos** contém o código C# para o seu módulo, bem como Dockerfiles para criar o seu módulo como uma imagem de contentor. O ficheiro **\.env** armazena as credenciais do registo de contentor. O ficheiro **deployment.template.json** contém as informações que o runtime do IoT Edge utiliza para implementar módulos num dispositivo. E **deployment.debug.template.json** contentores de ficheiros a versão de depuração de módulos. Não irá editar a pasta **\.vscode** ou o ficheiro **\.gitignore** neste tutorial.

Se não tiver especificado um registo de contentor ao criar a sua solução, mas aceitou o valor do localhost:5000 predefinido, não terá um ficheiro \.env. 

<!--
   ![C# solution workspace](./media/tutorial-csharp-module/workspace.png)
-->

### <a name="add-your-registry-credentials"></a>Adicionar as credenciais do registo

O ficheiro de ambiente armazena as credenciais do seu registo de contentor e partilha-as com o runtime do IoT Edge. O runtime precisa destas credenciais para solicitar as imagens privadas para o dispositivo IoT Edge. 

1. No explorador do VS Code, abra o ficheiro .env. 
2. Atualize os campos com os valores **nome de utilizador** e **palavra-passe** que copiou do seu Azure Container Registry. 
3. Guarde este ficheiro. 

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

5. No método **Init**, o código cria e configura um objeto **ModuleClient**. Este objeto permite que o módulo se ligue ao runtime do Azure IoT Edge local para enviar e receber mensagens. A cadeia de ligação utilizada no método **Init** é dada ao módulo através do runtime do IoT Edge. Depois de criar o **ModuleClient**, o código lê o valor **temperatureThreshold** a partir das propriedades pretendidas do módulo duplo. O código regista uma chamada de retorno para receber mensagens de um hub do IoT Edge através do ponto final **input1**. Substitua o método **SetInputMessageHandlerAsync** por um novo e adicione um método **SetDesiredPropertyUpdateCallbackAsync** para obter atualizações para as propriedades pretendidas. Para fazer esta alteração, substitua a última linha do método **Init** pelo seguinte código:

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
                var filteredMessage = new Message(messageBytes);
                foreach (KeyValuePair<string, string> prop in message.Properties)
                {
                    filteredMessage.Properties.Add(prop.Key, prop.Value);
                }

                filteredMessage.Properties.Add("MessageType", "Alert");
                await moduleClient.SendEventAsync("output1", filteredMessage);
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

9. No explorador do VS Code, abra o ficheiro **deployment.template.json** na área de trabalho da solução do IoT Edge. Este ficheiro diz ao agente do IoT Edge para quais módulos para implementar, neste caso **tempSensor** e **CSharpModule**e informa ao hub do IoT Edge como rotear mensagens entre eles. A extensão do Visual Studio Code povoa automaticamente a maior parte das informações que precisa no modelo de implementação, mas certifique-se de que tudo está correta para a sua solução: 

   1. A plataforma padrão do seu IoT Edge está definida como **amd64** no seu estado do VS Code barra, que significa que seu **CSharpModule** está definido como Linux amd64 versão da imagem. Alterar a plataforma de predefinição na barra de status da **amd64** ao **arm32v7** ou **windows-amd64** se de que é arquitetura de seu dispositivo IoT Edge. 

      ![Plataforma de imagem do módulo de atualização](./media/tutorial-csharp-module/image-platform.png)

   2. Verifique se o modelo tem o nome do módulo correto e não o nome predefinido **SampleModule** que alterou quando criou a solução IoT Edge.

   3. O **registryCredentials** secção armazena as suas credenciais do registo de Docker, para que o agente do IoT Edge pode extrair a sua imagem do módulo. Os pares de nome de utilizador e palavra-passe reais são armazenados no ficheiro .env, ignorado pelo git. Se ainda não o fez, adicione as suas credenciais para o ficheiro. env.  

   4. Se quiser obter mais informações sobre manifestos de implantação, consulte [Saiba como implementar módulos e estabelecer as rotas no IoT Edge](module-composition.md).

10. Adicione o módulo duplo **CSharpModule** ao manifesto de implementação. Insira o seguinte conteúdo JSON na parte inferior da secção **modulesContent**, após o módulo duplo **$edgeHub**: 

    ```json
       "CSharpModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
    ```

    ![Adicionar módulo duplo ao modelo de implementação](./media/tutorial-csharp-module/module-twin.png)

11. Guarde o ficheiro de deployment.template.json.


## <a name="build-your-iot-edge-solution"></a>Criar a sua solução do IoT Edge

Na secção anterior, criou uma solução do IoT Edge e adicionou código ao **CSharpModule** para filtrar mensagens onde a temperatura comunicada da máquina é inferior ao limiar aceitável. Agora, tem de criar a solução como uma imagem de contentor e enviá-la para o registo de contentor. 

1. Introduza o comando seguinte no terminal integrado do Visual Studio Code e inicie sessão no Docker. Em seguida, pode emitir a sua imagem do módulo para o registo de contentor do Azure.
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Utilize o nome do utilizador, palavra-passe e servidor de início de sessão que copiou do registo de contentor do Azure na primeira secção. Também pode obter estes valores a partir da secção **Chaves de acesso** do registo no portal do Azure.

2. No explorador do VS Code, clique com o botão direito do rato no ficheiro deployment.template.json e selecione **Criar e Emitir solução do IoT Edge**. 

Quando indicar ao Visual Studio Code para criar a solução, este utiliza primeiro as informações no modelo de implementação e gera um ficheiro deployment.json numa nova pasta com o nome **config**. Em seguida, executa dois comandos no terminal integrado: `docker build` e `docker push`. Estes dois comandos criam o código, colocam o CSharpModule.dll em contentores e enviam-no para o registo de contentor que especificou quando inicializou a solução. 

Pode ver o endereço da imagem de contentor completo com a etiqueta no terminal integrado do VS Code. O endereço da imagem baseia-se nas informações no ficheiro module.json, com o formato \<repositório\>:\<versão\>-\<plataforma\>. Para este tutorial, deve ser semelhante a registryname.azurecr.io/csharpmodule:0.0.1-amd64.

>[!TIP]
>Se receber um erro ao tentar criar e enviar por push o seu módulo, tome as seguintes verificações:
>* Iniciou sessão ao Docker no Visual Studio Code, utilizando as credenciais do seu registo de contentor? Estas credenciais são diferentes dos que utilizar para iniciar sessão no portal do Azure.
>* O repositório do contentor está correto? Open **módulos** > **cmodule** > **Module** e encontre a **repositório** campo. O repositório de imagens deve ser semelhante à  **\<registryname\>.azurecr.io/csharpmodule**. 
>* Está a criar o mesmo tipo de contentores que está a executar o seu computador de desenvolvimento? Visual Studio Code é predefinida como contentores do Linux amd64. Se o computador de desenvolvimento está a executar contentores do Windows ou Linux arm32v7 contentores, atualize a plataforma na barra de status azul na parte inferior da janela do VS Code, de acordo com sua plataforma de contentores.

## <a name="deploy-and-run-the-solution"></a>Implementar e executar a solução

No artigo de início rápido que utilizou para configurar o seu dispositivo IoT Edge, implementou um módulo com o portal do Azure. Também pode implementar módulos com a extensão do Kit de ferramentas do Azure IoT Hub (anteriormente conhecido como extensão do Kit de ferramentas do Azure IoT) para Visual Studio Code. Já tem um manifesto de implementação preparado para o seu cenário, o ficheiro **deployment.json**. Agora tudo o que precisa de fazer é selecionar um dispositivo para receber a implementação.

1. Na paleta de comandos VS Code, execute **IoT Hub do Azure: Selecione o IoT Hub**. 

2. Escolha a subscrição e o hub IoT que contém o dispositivo do IoT Edge que pretende configurar. 

3. No explorador do VS Code, expanda a secção **Dispositivos do Hub IoT do Azure**. 

4. Carregue com o botão direito do rato no nome do seu dispositivo do IoT Edge e, em seguida, selecione **Criar Implementação para o Dispositivo Único**. 

   ![Criar implementação para dispositivo único](./media/tutorial-csharp-module/create-deployment.png)

5. Selecione o ficheiro **deployment.json** na pasta **config** e, em seguida, clique em **Selecionar Manifesto de Implementação do Edge**. Não utilize o ficheiro deployment.template.json. 

6. Clique no botão Atualizar. Deverá ver o novo **CSharpModule** em execução, juntamente com o módulo **TempSensor**, bem como **$edgeAgent** e **$edgeHub**.  

## <a name="view-generated-data"></a>Ver os dados gerados

Depois de aplicar o manifesto de implementação no seu dispositivo IoT Edge, o runtime do IoT Edge no dispositivo recolhe as novas informações de implementação e começa a ser executado no mesmo. Quaisquer módulos em execução no dispositivo que não estão incluídos no manifesto de implementação são parados. Quaisquer módulos em falta do dispositivo são iniciados. 

Pode ver o estado do seu dispositivo do IoT Edge com a secção **Dispositivos do Hub IoT do Azure** do explorador do Visual Studio Code. Expanda os detalhes do seu dispositivo para ver uma lista de módulos implementados e em execução. 

No próprio dispositivo IoT Edge, pode ver o estado dos seus módulos de implementação com o comando `iotedge list`. Deverá ver quatro módulos: os dois módulos de runtime do IoT Edge, o tempSensor e o módulo personalizado que criou neste tutorial. Pode demorar alguns minutos para que todos os módulos comecem, por isso, execute novamente o comando se não os vir todos inicialmente. 

Para ver as mensagens que são geradas por qualquer módulo, utilize o comando `iotedge logs <module name>`. 

Pode ver as mensagens conforme chegam ao seu hub IoT através do Visual Studio Code. 

1. Para monitorizar os dados que são recebidos pelo hub IoT, selecione as reticências (**...** ) e, em seguida, selecione **Iniciar Monitorização de Mensagens D2C**.
2. Para monitorizar a mensagem D2C para um dispositivo específico, clique com o botão direito do rato na lista e selecione **Iniciar Monitorização de Mensagens D2C**.
3. Para parar a monitorização de dados, execute o comando **IoT Hub do Azure: Parar a monitorização de mensagens D2C** na paleta de comandos. 
4. Para ver ou atualizar o módulo duplo, clique com o botão direito do rato no módulo na lista e selecione **Editar módulo duplo**. Para atualizar o módulo duplo, guarde o ficheiro JSON duplo, clique com o botão direito do rato na área de editor e selecione **Atualizar Módulo Duplo**.
5. Para ver os registos do Docker, instale o [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) para o VS Code. Pode encontrar os módulos em execução localmente no explorador do Docker. No menu de contexto, selecione **Mostrar Registos** para ver no terminal integrado.
 
## <a name="clean-up-resources"></a>Limpar recursos 

Se planeia avançar para o próximo artigo recomendado, pode manter os recursos e as configurações que criou e reutilizá-los. Também pode continuar a utilizar o mesmo dispositivo IoT Edge como um dispositivo de teste. 

Caso contrário, pode eliminar as configurações locais e os recursos do Azure que criou neste artigo para evitar custos. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, criou uma função do módulo do IoT Edge com código para filtrar os dados não processados que são gerados pelo seu dispositivo IoT Edge. Quando estiver pronto para criar os seus próprios módulos, pode saber mais sobre como [desenvolver um módulo de C# com o Azure IoT Edge para Visual Studio Code](how-to-develop-csharp-module.md). Pode continuar para os próximos tutoriais para aprender outras formas em que o Azure IoT Edge o pode ajudar a transformar os dados em informações empresariais na periferia.

> [!div class="nextstepaction"]
> [Armazenar dados na periferia com bases de dados do SQL Server](tutorial-store-data-sql-server.md)
