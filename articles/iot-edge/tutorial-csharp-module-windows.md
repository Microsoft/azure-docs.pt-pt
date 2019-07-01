---
title: Desenvolver o tutorial C# módulo para o Windows - Azure IoT Edge | Documentos da Microsoft
description: Este tutorial mostra-lhe como criar um módulo do IoT Edge com o C# de código e implementá-la a um dispositivo de Windows IoT Edge.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/23/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: d7ccce1f21b1caa2268317b7239617a80ddce10b
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485936"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-windows-devices"></a>Tutorial: Desenvolver um C# módulo do IoT Edge para dispositivos Windows

Utilizar o Visual Studio para desenvolver C# de código e implementá-la num dispositivo de Windows com o Azure IoT Edge. 

Pode utilizar os módulos do Azure IoT Edge para implementar código que aplica a sua lógica de negócio diretamente aos seus dispositivos IoT Edge. Este tutorial explica-lhe como criar e implementar um módulo do IoT Edge que filtra dados de sensores. Neste tutorial, ficará a saber como:    

> [!div class="checklist"]
> * Utilizar o Visual Studio para criar um módulo do IoT Edge que se baseia o C# SDK.
> * Utilize o Visual Studio e a Docker para criar uma imagem do Docker e publicá-lo para o seu registo.
> * Implemente o módulo no seu dispositivo IoT Edge.
> * Veja os dados gerados.

O módulo do IoT Edge que criou neste tutorial filtra os dados de temperatura que são gerados pelo seu dispositivo. Envia apenas mensagens de origem caso a temperatura seja superior a um limiar especificado. Este tipo de análise no Edge é útil para reduzir a quantidade de dados que são comunicados e armazenados na cloud. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Âmbito de solução

Este tutorial demonstra como desenvolver um módulo numa **C#** usando **2019 do Visual Studio**e como implementá-la para um **dispositivo de Windows**. Se estiver a desenvolver módulos para dispositivos de Linux, aceda a [desenvolver um C# módulo do IoT Edge para dispositivos de Linux](tutorial-csharp-module.md) em vez disso. 

Utilize a tabela seguinte para compreender as opções para desenvolver e implantar módulos de C para dispositivos Windows: 

| C# | Visual Studio Code | Visual Studio 2017/2019 | 
| -- | ------------------ | ------------------ |
| **Desenvolver AMD64 do Windows** | ![Desenvolva C# módulos de hardware para WinAMD64 no VS Code](./media/tutorial-c-module/green-check.png) | ![Desenvolva C# módulos de hardware para WinAMD64 no Visual Studio](./media/tutorial-c-module/green-check.png) |
| **Depuração do Windows AMD64** |   | ![Depurar C# módulos de hardware para WinAMD64 no Visual Studio](./media/tutorial-c-module/green-check.png) |

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, deve já leu o tutorial anterior para configurar o ambiente de desenvolvimento [desenvolver um módulo do IoT Edge para um dispositivo de Windows](tutorial-develop-for-windows.md). Depois de concluir esse tutorial, já deve ter os seguintes pré-requisitos: 

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) no escalão gratuito ou standard no Azure.
* R [dispositivo de Windows com o Azure IoT Edge](quickstart.md).
* Um registo de contentor, como [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/install-visual-studio) configurado com o [ferramentas do Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) extensão.
* [Docker CE](https://docs.docker.com/install/) configurado para executar contentores do Windows.

> [!TIP]
> Se estiver a utilizar o Visual Studio 2017 (versão 15.7 ou superior), plrease transfira e instale [ferramentas do Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) para VS 2017 a partir do Visual Studio marketplace

## <a name="create-a-module-project"></a>Criar um projeto de módulo

Os passos seguintes criam um projeto de módulo do IoT Edge com o Visual Studio e a extensão das ferramentas do Azure IoT Edge. Depois de ter um modelo de projeto criado, adicione o novo código para que o módulo filtra as mensagens com base nas respetivas propriedades comunicadas. 

### <a name="create-a-new-project"></a>Criar um novo projeto

As ferramentas do Azure IoT Edge fornece modelos de projeto para o IoT Edge de suportados todos os idiomas de módulo no Visual Studio. Esses modelos têm todos os arquivos e código que precisa implantar um módulo de trabalho para testar o IoT Edge ou dão-lhe um ponto de partida para personalizar o modelo com sua própria lógica de negócio. 

1. Inicie o Visual Studio 2019 e selecione **criar novo projeto**.

2. Na janela novo projeto, de pesquisa **IoT Edge** do projeto e escolha o **Azure IoT Edge (Windows amd64)** projeto. Clique em **Seguinte**. 

   ![Criar um novo projeto do Azure IoT Edge](./media/tutorial-csharp-module-windows/new-project.png)

3. Na configurar a nova janela de projeto, mudar o nome do projeto e a solução para algo descritivo semelhantes **CSharpTutorialApp**. Clique em **criar** para criar o projeto. 

   ![Configurar um novo projeto do Azure IoT Edge](./media/tutorial-csharp-module-windows/configure-project.png)

4. Na aplicação do IoT Edge e janela do módulo, configure o seu projeto com os seguintes valores: 

   | Campo | Value |
   | ----- | ----- |
   | Selecione um modelo | Selecione  **C# módulo**. | 
   | Nome do projeto de módulo | Atribua o nome **CSharpModule** ao módulo. | 
   | Repositório de imagens do docker | Os repositórios de imagens incluem o nome do seu registo de contentor e o nome da sua imagem de contentor. A imagem de contentor é pré-preenchida com aceder a partir do valor de nome de projeto de módulo. Substitua **localhost:5000** pelo valor do servidor de início de sessão do registo de contentor do Azure Container Registry. Pode obter o servidor de início de sessão na página Overview (Descrição Geral) do registo de contentor no portal do Azure. <br><br> O repositório de imagem final se parece com \<nome do registo\>.azurecr.io/csharpmodule. |

   ![Configurar o seu projeto para o dispositivo de destino, o tipo de módulo e o registo de contentor](./media/tutorial-csharp-module-windows/add-application-and-module.png)

5. Selecione **OK** para aplicar as alterações. 

### <a name="add-your-registry-credentials"></a>Adicionar as credenciais do registo

O manifesto de implantação compartilha as credenciais para o seu registo de contentor com o runtime do IoT Edge. O runtime precisa destas credenciais para solicitar as imagens privadas para o dispositivo IoT Edge. Utilize as credenciais a partir da **chaves de acesso** secção do seu registo de contentor do Azure. 

1. No Explorador de soluções do Visual Studio, abra a **deployment.template.json** ficheiro. 

2. Encontrar o **registryCredentials** propriedades pretendidas de propriedade no $edgeAgent. 

3. Atualize a propriedade com as suas credenciais, seguindo este formato: 

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "<username>",
       "password": "<password>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

4. Guarde o ficheiro de deployment.template.json. 

### <a name="update-the-module-with-custom-code"></a>Atualizar o módulo com o código personalizado

O código do módulo de predefinição recebe mensagens numa fila de entrada e transmite-los por meio de uma fila de saída. Vamos adicionar alguns códigos adicionais para que o módulo processa as mensagens na periferia antes do reencaminhamento-los para o IoT Hub. Atualize o módulo para que analisa os dados de temperatura em cada mensagem e só envia a mensagem para o IoT Hub, se a temperatura for superior a um determinado limiar. 

1. No Visual Studio, abra **CSharpModule** > **Program.cs**.

2. Na parte superior do espaço de nomes **CSharpModule**, adicione três declarações **em utilização** para os tipos que são utilizados mais tarde:

    ```csharp
    using System.Collections.Generic;     // For KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // For TwinCollection
    using Newtonsoft.Json;                // For JsonConvert
    ```

3. Adicionar a **temperatureThreshold** variável para o **programa** classe depois da variável de contador. A variável temperatureThreshold define o valor que a temperatura de medida tem de exceder para os dados sejam enviados para o hub IoT. 

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

4. Adicionar a **MessageBody**, **máquina**, e **Ambient** classes para o **programa** classe após as declarações de variável. Estas classes definem o esquema esperado para o corpo das mensagens a receber.

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

5. Encontrar o **Init** método. Este método cria e configura uma **ModuleClient** objeto, que permite que o módulo ligar ao tempo de execução local do Azure IoT Edge para enviar e receber mensagens. O código também registra um retorno de chamada para receber mensagens a partir de um hub do IoT Edge através da **input1** ponto final.

   Substitua o método Init todo o código a seguir:
   
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
   
   Este método Init atualizado ainda configura a ligação para o runtime do IoT Edge com o ModuleClient, mas também adiciona novas funcionalidades. Ele o módulo lê as propriedades pretendidas do duplo para obter o **temperatureThreshold** valor. Em seguida, cria um retorno de chamada que está à escuta de todas as futuras atualizações para as de propriedades pretendidas do duplo do módulo. Com esse retorno de chamada, pode atualizar remotamente o limiar de temperatura no duplo do módulo e as alterações serão incorporadas ao módulo. 

   O método Init atualizado também altera o existente **SetInputMessageHandlerAsync** método. No código de exemplo, mensagens de entrada no *input1* são processados com o *PipeMessage* função, mas podemos querer alterar isso para usar o *FilterMessages* funcionar que Vamos criar nos passos seguintes. 

6. Adicionar um novo **onDesiredPropertiesUpdate** método para o **programa** classe. Este método recebe atualizações nas propriedades pretendidas do módulo duplo e atualiza a variável **temperatureThreshold** para corresponder. Todos os módulos têm o seu próprio módulo duplo, que lhe permite configurar o código em execução no interior de um módulo diretamente a partir da cloud.

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

7. O exemplo de remoção **PipeMessage** método e substitua-o com uma nova **FilterMessages** método. Este método é chamado sempre que o módulo recebe uma mensagem do hub do IoT Edge. Ele filtra as mensagens que comunicam temperaturas inferiores ao limiar de temperatura definido através do módulo duplo. Também adiciona a propriedade **MessageType** à mensagem com o valor definido para **Alerta**. 

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

9. Abra o **deployment.template.json** ficheiro na sua solução de IoT Edge. Este ficheiro diz ao agente do IoT Edge para quais módulos para implementar, neste caso **tempSensor** e **CSharpModule**e informa ao hub do IoT Edge como rotear mensagens entre eles.

10. Adicione o módulo duplo **CSharpModule** ao manifesto de implementação. Insira o seguinte conteúdo JSON na parte inferior da secção **modulesContent**, após o módulo duplo **$edgeHub**: 

    ```json
       "CSharpModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
    ```

    ![Adicionar módulo duplo ao modelo de implementação](./media/tutorial-csharp-module-windows/module-twin.png)

11. Guarde o ficheiro de deployment.template.json.


## <a name="build-and-push-your-module"></a>Criar e enviar por push o seu módulo

Na secção anterior, criou uma solução do IoT Edge e adicionou código ao **CSharpModule** para filtrar mensagens onde a temperatura comunicada da máquina é inferior ao limiar aceitável. Agora, tem de criar a solução como uma imagem de contentor e enviá-la para o registo de contentor. 

1. Utilize o seguinte comando para iniciar sessão no Docker no seu computador de desenvolvimento. Utilize o nome de utilizador, a palavra-passe e o servidor de início de sessão do seu registo de contentor do Azure. Pode obter estes valores a partir da **chaves de acesso** secção do seu registo no portal do Azure.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Poderá receber um aviso de segurança recomenda a utilização do `--password-stdin`. Embora essa prática recomendada é recomendada para cenários de produção, é fora do âmbito deste tutorial. Para obter mais informações, consulte a [início de sessão do docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) referência.

2. No Explorador de soluções do Visual Studio, clique com botão direito no nome do projeto que deseja criar. O nome predefinido é **AzureIotEdgeApp1** e, uma vez que estiver criando um módulo do Windows, a extensão deve ser **Windows.Amd64**. 

3. Selecione **criar e enviar por Push módulos do IoT Edge**. 

   O comando de compilação e emissão inicia três operações. Primeiro, ele cria uma nova pasta na solução denominada **config** que contém os ficheiros de manifesto, criados fora das informações no modelo de implementação e outras soluções de implantação completa. Em segundo lugar, ele é executado `docker build` para criar a imagem de contentor com base no dockerfile apropriado para a sua arquitetura de destino. Em seguida, ele é executado `docker push` para enviar por push o repositório de imagens para o seu registo de contentor. 

## <a name="deploy-modules-to-device"></a>Implementar módulos no dispositivo

Utilize o cloud explorer do Visual Studio e a extensão de ferramentas do Azure IoT Edge para implementar o projeto de módulo no seu dispositivo IoT Edge. Já tem um manifesto de implantação preparado para o seu cenário, o **deployment.json** ficheiro na pasta config. Agora tudo o que precisa de fazer é selecionar um dispositivo para receber a implementação.

Certifique-se de que o seu dispositivo IoT Edge está em execução. 

1. No cloud explorer do Visual Studio, expanda os recursos para ver uma lista de dispositivos IoT. 

2. Com o botão direito no nome do dispositivo do IoT Edge que pretende receber a implementação. 

3. Selecione **criar implementação**.

4. No Explorador de ficheiros, selecione o **deployment.windows amd64** ficheiro na pasta de configuração da sua solução. 

5. Atualize o Explorador de cloud para ver os módulos implementados listados no seu dispositivo. 

## <a name="view-generated-data"></a>Ver os dados gerados

Depois de aplicar o manifesto de implementação no seu dispositivo IoT Edge, o runtime do IoT Edge no dispositivo recolhe as novas informações de implementação e começa a ser executado no mesmo. Quaisquer módulos em execução no dispositivo que não estão incluídos no manifesto de implementação são parados. Quaisquer módulos em falta do dispositivo são iniciados. 

Pode utilizar a extensão de IoT Edge ferramentas para ver mensagens quando chegam ao IoT Hub. 

1. No cloud explorer do Visual Studio, selecione o nome do seu dispositivo IoT Edge. 

2. Na **ações** lista, selecione **iniciar a monitorização de evento ponto final incorporado**. 

3. Ver as mensagens que chegam ao IoT Hub. Pode demorar algum tempo para as mensagens chegam, uma vez que as alterações feitas no código CSharpModule Aguarde até a temperatura de máquina atinge 25 graus antes de enviar mensagens. Ele também adiciona o tipo de mensagem **alerta** para todas as mensagens que atingir esse limite de temperatura. 

   ![Ver mensagens que chegam ao IoT Hub](./media/tutorial-csharp-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Editar o módulo duplo

Usamos o duplo do módulo de CSharpModule para definir o limiar de temperatura em graus 25. Pode utilizar o módulo duplo para alterar a funcionalidade sem ter de atualizar o código do módulo.

1. No Visual Studio, abra a **deployment.windows amd64.json** ficheiro. (Não o ficheiro de deployment.template. Se não vir a implementação de manifesto no ficheiro de configuração no solution explorer, selecione o **Mostrar todos os ficheiros** ícone na barra de ferramentas do explorer.)

2. Localizar o duplo CSharpModule e alterar o valor do **temperatureThreshold** parâmetro para uma nova temperatura graus de 5 a 10 graus de mais do que a temperatura comunicada mais recente. 

3. Guardar a **deployment.windows amd64.json** ficheiro.

4. Siga os passos de implementação novamente para aplicar o manifesto de implantação atualizadas para o seu dispositivo. 

5. Monitorize as mensagens do dispositivo para a nuvem de entrada. Deverá ver as mensagens parar até que seja atingido o limiar de temperatura de novo. 

## <a name="clean-up-resources"></a>Limpar recursos 

Se planeia avançar para o próximo artigo recomendado, pode manter os recursos e as configurações que criou e reutilizá-los. Também pode continuar a utilizar o mesmo dispositivo IoT Edge como um dispositivo de teste. 

Caso contrário, pode eliminar as configurações locais e os recursos do Azure que utilizou neste artigo para evitar encargos. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, criou uma função do módulo do IoT Edge com código para filtrar os dados não processados que são gerados pelo seu dispositivo IoT Edge. Quando estiver pronto para criar seus próprios módulos, pode saber mais sobre [desenvolver seus próprios módulos do IoT Edge](module-development.md) ou como [desenvolver módulos com o Visual Studio](how-to-visual-studio-develop-module.md). Pode continuar para os tutoriais seguintes para saber como Azure IoT Edge pode ajudá-lo a implementar serviços cloud do Azure para processar e analisar dados na periferia.

> [!div class="nextstepaction"]
> [As funções](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [serviço de visão personalizada](tutorial-deploy-custom-vision.md)
