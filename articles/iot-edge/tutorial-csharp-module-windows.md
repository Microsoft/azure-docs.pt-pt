---
title: Tutorial de C# desenvolvimento de módulo para Windows-Azure IOT Edge | Microsoft Docs
description: Este tutorial mostra como criar um módulo IoT Edge com C# código e implantá-lo em um dispositivo Windows IOT Edge.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/23/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: c872b10d7819fb95d614664ed32831f410349760
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71122896"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-windows-devices"></a>Tutorial: Desenvolver um C# módulo IOT Edge para dispositivos Windows

Use o Visual Studio para C# desenvolver código e implantá-lo em um dispositivo Windows que executa o Azure IOT Edge. 

Pode utilizar os módulos do Azure IoT Edge para implementar código que aplica a sua lógica de negócio diretamente aos seus dispositivos IoT Edge. Este tutorial explica-lhe como criar e implementar um módulo do IoT Edge que filtra dados de sensores. Neste tutorial, ficará a saber como:    

> [!div class="checklist"]
> * Use o C# Visual Studio para criar um módulo IOT Edge baseado no SDK.
> * Use o Visual Studio e o Docker para criar uma imagem do Docker e publicá-la no registro.
> * Implemente o módulo no seu dispositivo IoT Edge.
> * Veja os dados gerados.

O módulo do IoT Edge que criou neste tutorial filtra os dados de temperatura que são gerados pelo seu dispositivo. Envia apenas mensagens de origem caso a temperatura seja superior a um limiar especificado. Este tipo de análise no Edge é útil para reduzir a quantidade de dados que são comunicados e armazenados na cloud. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Escopo da solução

Este tutorial demonstra como desenvolver um módulo no **C#** usando o **Visual Studio 2019**e como implantá-lo em um **dispositivo Windows**. Se você estiver desenvolvendo módulos para dispositivos Linux, vá para [desenvolver um C# módulo de IOT Edge para dispositivos Linux](tutorial-csharp-module.md) em vez disso. 

Use a tabela a seguir para entender suas opções de desenvolvimento e implantação de módulos C em dispositivos Windows: 

| C# | Visual Studio Code | Visual Studio 2017/2019 | 
| -- | ------------------ | ------------------ |
| **Desenvolvimento do Windows AMD64** | ![Desenvolver C# módulos para WinAMD64 no vs Code](./media/tutorial-c-module/green-check.png) | ![Desenvolver C# módulos para WinAMD64 no Visual Studio](./media/tutorial-c-module/green-check.png) |
| **Depuração do Windows AMD64** |   | ![Módulos C# de depuração para WinAMD64 no Visual Studio](./media/tutorial-c-module/green-check.png) |

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, você deve ter passado pelo tutorial anterior para configurar seu ambiente de desenvolvimento, [desenvolver um módulo IOT Edge para um dispositivo Windows](tutorial-develop-for-windows.md). Depois de concluir esse tutorial, você já deve ter os seguintes pré-requisitos: 

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) no escalão gratuito ou standard no Azure.
* Um [dispositivo Windows executando o Azure IOT Edge](quickstart.md).
* Um registro de contêiner, como o [registro de contêiner do Azure](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/install-visual-studio) configurado com a extensão de [ferramentas de Azure IOT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) .
* [Área de trabalho do Docker](https://docs.docker.com/docker-for-windows/install/) configurada para executar contêineres do Windows.

> [!TIP]
> Se você estiver usando o Visual Studio 2017 (versão 15,7 ou superior), plrease baixar e instalar o [Azure IOT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) para vs 2017 no Visual Studio Marketplace

## <a name="create-a-module-project"></a>Criar um projeto de módulo

As etapas a seguir criam um projeto de módulo IoT Edge usando o Visual Studio e a extensão de ferramentas de Azure IoT Edge. Depois de criar um modelo de projeto, adicione um novo código para que o módulo filtre as mensagens com base em suas propriedades relatadas. 

### <a name="create-a-new-project"></a>Criar um novo projeto

As ferramentas de Azure IoT Edge fornecem modelos de projeto para todas as linguagens de módulo IoT Edge com suporte no Visual Studio. Esses modelos têm todos os arquivos e códigos de que você precisa para implantar um módulo de trabalho para testar IoT Edge ou fornecer um ponto de partida para personalizar o modelo com sua própria lógica de negócios. 

1. Inicie o Visual Studio 2019 e selecione **criar novo projeto**.

2. Na janela novo projeto, pesquise **IOT Edge** projeto e escolha o projeto **Azure IOT Edge (Windows AMD64)** . Clique em **Seguinte**. 

   ![Criar um novo projeto de Azure IoT Edge](./media/tutorial-csharp-module-windows/new-project.png)

3. Na janela configurar seu novo projeto, renomeie o projeto e a solução como algo descritivo, como **CSharpTutorialApp**. Clique em **criar** para criar o projeto. 

   ![Configurar um novo projeto de Azure IoT Edge](./media/tutorial-csharp-module-windows/configure-project.png)

4. Na janela IoT Edge aplicativo e módulo, configure o projeto com os seguintes valores: 

   | Campo | Value |
   | ----- | ----- |
   | Seleccionar um modelo | Selecione  **C# módulo**. | 
   | Nome do projeto de módulo | Atribua o nome **CSharpModule** ao módulo. | 
   | Repositório de imagens do Docker | Os repositórios de imagens incluem o nome do seu registo de contentor e o nome da sua imagem de contentor. Sua imagem de contêiner é preenchida previamente a partir do valor do nome do projeto de módulo. Substitua **localhost:5000** pelo valor do servidor de início de sessão do registo de contentor do Azure Container Registry. Pode obter o servidor de início de sessão na página Overview (Descrição Geral) do registo de contentor no portal do Azure. <br><br> O repositório de imagens final é \<semelhante ao\>nome do registro. azurecr.Io/csharpmodule. |

   ![Configurar seu projeto para o dispositivo de destino, o tipo de módulo e o registro de contêiner](./media/tutorial-csharp-module-windows/add-application-and-module.png)

5. Selecione **OK** para aplicar suas alterações. 

### <a name="add-your-registry-credentials"></a>Adicionar as credenciais do registo

O manifesto de implantação compartilha as credenciais para o registro de contêiner com o tempo de execução IoT Edge. O runtime precisa destas credenciais para solicitar as imagens privadas para o dispositivo IoT Edge. Use as credenciais da seção **chaves de acesso** do registro de contêiner do Azure. 

1. No Gerenciador de soluções do Visual Studio, abra o arquivo **Deployment. Template. JSON** . 

2. Localize a propriedade **registryCredentials** no $edgeAgent propriedades desejadas. 

3. Atualize a propriedade com suas credenciais, seguindo este formato: 

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "<username>",
       "password": "<password>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

4. Salve o arquivo Deployment. Template. JSON. 

### <a name="update-the-module-with-custom-code"></a>Atualizar o módulo com o código personalizado

O código de módulo padrão recebe mensagens em uma fila de entrada e as passa por uma fila de saída. Vamos adicionar um código adicional para que o módulo processe as mensagens na borda antes de encaminhá-las ao Hub IoT. Atualize o módulo para que ele analise os dados de temperatura em cada mensagem e só envie a mensagem para o Hub IoT se a temperatura exceder um determinado limite. 

1. No Visual Studio, abra **CSharpModule** > **Program.cs**.

2. Na parte superior do espaço de nomes **CSharpModule**, adicione três declarações **em utilização** para os tipos que são utilizados mais tarde:

    ```csharp
    using System.Collections.Generic;     // For KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // For TwinCollection
    using Newtonsoft.Json;                // For JsonConvert
    ```

3. Adicione a variável **temperatureThreshold** à classe **Program** após a variável Counter. A variável temperatureThreshold define o valor que a temperatura medida deve exceder para que os dados sejam enviados ao Hub IoT. 

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

4. Adicione as classes **MessageBody**, **Machine**e **ambiente** à classe **Program** após as declarações de variável. Estas classes definem o esquema esperado para o corpo das mensagens a receber.

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

5. Localize o método **init** . Esse método cria e configura um objeto **ModuleClient** , que permite que o módulo se conecte ao tempo de execução de Azure IOT Edge local para enviar e receber mensagens. O código também registra um retorno de chamada para receber mensagens de um hub de IoT Edge por meio do ponto de extremidade **entrada1** .

   Substitua todo o método Init pelo código a seguir:
   
   ```csharp
   static async Task Init()
   {
       AmqpTransportSettings amqpSetting = new AmqpTransportSettings(TransportType.Amqp_Tcp_Only);
       ITransportSettings[] settings = { amqpSetting };

       // Open a connection to the Edge runtime
       ModuleClient ioTHubModuleClient = await ModuleClient.CreateFromEnvironmentAsync(settings);
       await ioTHubModuleClient.OpenAsync();
       Console.WriteLine("IoT Hub module client initialized.");

       // Read the TemperatureThreshold value from the module twin's desired properties
       var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
       await OnDesiredPropertiesUpdate(moduleTwin.Properties.Desired, ioTHubModuleClient);

       // Attach a callback for updates to the module twin's desired properties.
       await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertiesUpdate, null);

       // Register a callback for messages that are received by the module.
       await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
   }
   ```
   
   Esse método Init atualizado ainda configura a conexão com o IoT Edge tempo de execução com o ModuleClient, mas também adiciona Nova funcionalidade. Ele lê as propriedades desejadas do módulo "s" para recuperar o valor de **temperatureThreshold** . Em seguida, ele cria um retorno de chamada que escuta quaisquer atualizações futuras nas propriedades desejadas do módulo. Com esse retorno de chamada, você pode atualizar o limite de temperatura no módulo para a mesclagem remota e as alterações serão incorporadas ao módulo. 

   O método Init atualizado também altera o método **SetInputMessageHandlerAsync** existente. No código de exemplo, as mensagens de entrada em *entrada1* são processadas com a função *PipeMessage* , mas queremos alterar isso para usar a função *FilterMessages* que criaremos nas etapas a seguir. 

6. Adicione um novo método **onDesiredPropertiesUpdate** à classe **Program** . Este método recebe atualizações nas propriedades pretendidas do módulo duplo e atualiza a variável **temperatureThreshold** para corresponder. Todos os módulos têm o seu próprio módulo duplo, que lhe permite configurar o código em execução no interior de um módulo diretamente a partir da cloud.

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

7. Remova o método **PipeMessage** de exemplo e substitua-o por um novo método **FilterMessages** . Este método é chamado sempre que o módulo recebe uma mensagem do hub do IoT Edge. Ele filtra as mensagens que comunicam temperaturas inferiores ao limiar de temperatura definido através do módulo duplo. Também adiciona a propriedade **MessageType** à mensagem com o valor definido para **Alerta**. 

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
                using(var filteredMessage = new Message(messageBytes))
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

9. Abra o arquivo **Deployment. Template. JSON** em sua solução de IOT Edge. Esse arquivo informa ao agente de IoT Edge quais módulos implantar, neste caso, **SimulatedTemperatureSensor** e **CSharpModule**, e informa ao Hub de IOT Edge como rotear mensagens entre eles.

10. Adicione o módulo duplo **CSharpModule** ao manifesto de implementação. Insira o seguinte conteúdo JSON na parte inferior da secção **modulesContent**, após o módulo duplo **$edgeHub**: 

    ```json
       "CSharpModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
    ```

    ![Adicionar módulo duplo ao modelo de implementação](./media/tutorial-csharp-module-windows/module-twin.png)

11. Salve o arquivo Deployment. Template. JSON.


## <a name="build-and-push-your-module"></a>Crie e envie por push seu módulo

Na secção anterior, criou uma solução do IoT Edge e adicionou código ao **CSharpModule** para filtrar mensagens onde a temperatura comunicada da máquina é inferior ao limiar aceitável. Agora, tem de criar a solução como uma imagem de contentor e enviá-la para o registo de contentor. 

1. Use o comando a seguir para entrar no Docker em seu computador de desenvolvimento. Use o nome de usuário, a senha e o servidor de logon do seu registro de contêiner do Azure. Você pode recuperar esses valores da seção **chaves de acesso** do registro no portal do Azure.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Você pode receber um aviso de segurança recomendando o uso `--password-stdin`de. Embora essa prática recomendada seja recomendada para cenários de produção, ela está fora do escopo deste tutorial. Para obter mais informações, consulte a referência de [logon](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) do Docker.

2. No Gerenciador de soluções do Visual Studio, clique com o botão direito do mouse no nome do projeto que você deseja compilar. O nome padrão é **AzureIotEdgeApp1** e, como você está criando um módulo do Windows, a extensão deve ser **Windows. AMD64**. 

3. Selecione **Compilar e enviar IOT Edge módulos**. 

   O comando Build e Push inicia três operações. Primeiro, ele cria uma nova pasta na solução chamada **configuração** que contém o manifesto de implantação completa, criado sem informações no modelo de implantação e outros arquivos de solução. Em segundo lugar, `docker build` ele é executado para criar a imagem de contêiner com base no dockerfile apropriado para sua arquitetura de destino. Em seguida, ele `docker push` é executado para enviar por push o repositório de imagens para o registro de contêiner. 

## <a name="deploy-modules-to-device"></a>Implantar módulos no dispositivo

Use o Gerenciador de nuvem do Visual Studio e a extensão de ferramentas de Azure IoT Edge para implantar o projeto de módulo em seu dispositivo de IoT Edge. Você já tem um manifesto de implantação preparado para seu cenário, o arquivo **Deployment. JSON** na pasta de configuração. Agora tudo o que precisa de fazer é selecionar um dispositivo para receber a implementação.

Verifique se o dispositivo IoT Edge está em execução. 

1. No Visual Studio Cloud Explorer, expanda os recursos para ver sua lista de dispositivos IoT. 

2. Clique com o botão direito do mouse no nome do dispositivo IoT Edge que você deseja receber a implantação. 

3. Selecione **criar implantação**.

4. No explorador de arquivos, selecione o arquivo **Deployment. Windows-AMD64** na pasta config da sua solução. 

5. Atualize o Cloud Explorer para ver os módulos implantados listados em seu dispositivo. 

## <a name="view-generated-data"></a>Ver os dados gerados

Depois de aplicar o manifesto de implementação no seu dispositivo IoT Edge, o runtime do IoT Edge no dispositivo recolhe as novas informações de implementação e começa a ser executado no mesmo. Quaisquer módulos em execução no dispositivo que não estão incluídos no manifesto de implementação são parados. Quaisquer módulos em falta do dispositivo são iniciados. 

Você pode usar a extensão de ferramentas de IoT Edge para exibir mensagens à medida que elas chegam ao seu hub IoT. 

1. No Visual Studio Cloud Explorer, selecione o nome do seu dispositivo IoT Edge. 

2. Na lista **ações** , selecione **Iniciar Monitoramento de ponto de extremidade de evento interno**. 

3. Exiba as mensagens que chegam ao seu hub IoT. Pode levar algum tempo para que as mensagens cheguem, pois as alterações feitas no código CSharpModule esperam até que a temperatura do computador atinja 25 graus antes de enviar mensagens. Ele também adiciona o **alerta** de tipo de mensagem a todas as mensagens que atingem esse limite de temperatura. 

   ![Exibir mensagens que chegam ao Hub IoT](./media/tutorial-csharp-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Edite o módulo...

Usamos o módulo CSharpModule de 10º para definir o limite de temperatura em 25 graus. Você pode usar o módulo "atualizar" para alterar a funcionalidade sem precisar atualizar o código do módulo.

1. No Visual Studio, abra o arquivo **Deployment. Windows-AMD64. JSON** . (Não o arquivo Deployment. Template. Se você não vir o manifesto de implantação no arquivo de configuração no Gerenciador de soluções, selecione o ícone **Mostrar todos os arquivos** na barra de ferramentas do Explorer.)

2. Localize o CSharpModule 10 e altere o valor do parâmetro **temperatureThreshold** para uma nova temperatura de 5 graus para 10 graus acima da temperatura relatada mais recente. 

3. Salve o arquivo **Deployment. Windows-AMD64. JSON** .

4. Siga as etapas de implantação novamente para aplicar o manifesto de implantação atualizado ao seu dispositivo. 

5. Monitore as mensagens de entrada do dispositivo para a nuvem. Você deve ver as mensagens param até que o novo limite de temperatura seja atingido. 

## <a name="clean-up-resources"></a>Limpar recursos 

Se planeia avançar para o próximo artigo recomendado, pode manter os recursos e as configurações que criou e reutilizá-los. Também pode continuar a utilizar o mesmo dispositivo IoT Edge como um dispositivo de teste. 

Caso contrário, você pode excluir as configurações locais e os recursos do Azure que você usou neste artigo para evitar cobranças. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou uma função do módulo do IoT Edge com código para filtrar os dados não processados que são gerados pelo seu dispositivo IoT Edge. Quando estiver pronto para criar seus próprios módulos, você pode aprender mais sobre como desenvolver [seus próprios módulos IOT Edge](module-development.md) ou como [desenvolver módulos com o Visual Studio](how-to-visual-studio-develop-module.md). Você pode continuar nos próximos tutoriais para saber como Azure IoT Edge pode ajudá-lo a implantar os serviços de nuvem do Azure para processar e analisar dados na borda.

> [!div class="nextstepaction"]
> [Functions](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Custom Vision Service](tutorial-deploy-custom-vision.md)
