---
title: Tutorial - Desenvolver módulo C# para Windows utilizando Azure IoT Edge
description: Este tutorial mostra-lhe como criar um módulo IoT Edge com código C# e implantá-lo num dispositivo Windows IoT Edge.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/03/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, amqp, devx-track-csharp
ms.openlocfilehash: b9451bcf6f67eb6afc6d06f4aa0cbec83ce75d6e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2020
ms.locfileid: "91855264"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-windows-devices"></a>Tutorial: Desenvolver um módulo C# IoT Edge para dispositivos Windows

Utilize o Visual Studio para desenvolver o código C# e implemente-o num dispositivo Windows que executa o Azure IoT Edge.

Pode utilizar os módulos do Azure IoT Edge para implementar código que aplica a sua lógica de negócio diretamente aos seus dispositivos IoT Edge. Este tutorial explica-lhe como criar e implementar um módulo do IoT Edge que filtra dados de sensores. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Utilize o Visual Studio para criar um módulo IoT Edge baseado no C# SDK.
> * Use o Visual Studio e o Docker para criar uma imagem docker e publicá-la no seu registo.
> * Implemente o módulo no seu dispositivo IoT Edge.
> * Veja os dados gerados.

O módulo do IoT Edge que criou neste tutorial filtra os dados de temperatura que são gerados pelo seu dispositivo. Envia apenas mensagens de origem caso a temperatura seja superior a um limiar especificado. Este tipo de análise no Edge é útil para reduzir a quantidade de dados que são comunicados e armazenados na cloud.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Âmbito de solução

Este tutorial demonstra como desenvolver um módulo em **C#** utilizando **o Visual Studio 2019** e implantá-lo num **dispositivo Windows**. Se estiver a desenvolver módulos para dispositivos Linux, vá ao [Develop a C# IoT Edge module para dispositivos Linux.](tutorial-csharp-module.md)

Utilize a seguinte tabela para compreender as suas opções de desenvolvimento e implantação de módulos C# para dispositivos Windows:

| C# | Visual Studio Code | Estúdio Visual 2017/2019 |
| -- | ------------------ | ------------------ |
| **Windows AMD64 desenvolver** | ![Desenvolver módulos C# para WinAMD64 em Código VS](./media/tutorial-c-module/green-check.png) | ![Desenvolver módulos C# para WinAMD64 em Estúdio Visual](./media/tutorial-c-module/green-check.png) |
| **Depurar Windows AMD64** |   | ![Módulos Debug C# para WinAMD64 em Visual Studio](./media/tutorial-c-module/green-check.png) |

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, deveria ter passado pelo tutorial anterior para configurar o seu ambiente de desenvolvimento, [desenvolver um módulo IoT Edge para um dispositivo Windows](tutorial-develop-for-windows.md). Depois de completar este tutorial, já deve ter os seguintes pré-requisitos:

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) no escalão gratuito ou standard no Azure.
* Um [dispositivo Windows em execução Azure IoT Edge](quickstart.md).
* Um registo de contentores, como [o Registo de Contentores de Azure.](https://docs.microsoft.com/azure/container-registry/)
* [Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/install-visual-studio) configurado com a extensão [Azure IoT Edge Tools.](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools)
* [Docker Desktop](https://docs.docker.com/docker-for-windows/install/) configurado para executar recipientes Windows.

> [!TIP]
> Se estiver a utilizar o Visual Studio 2017 (versão 15.7 ou superior), descarregue plrease e instale [ferramentas de borda Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) para VS 2017 a partir do mercado do Visual Studio

## <a name="create-a-module-project"></a>Criar um projeto de módulo

Os passos seguintes criam um projeto de módulo IoT Edge utilizando o Visual Studio e a extensão Azure IoT Edge Tools. Assim que tiver um modelo de projeto criado, adicione um novo código para que o módulo filtra as mensagens com base nas suas propriedades relatadas.

### <a name="create-a-new-project"></a>Criar um novo projeto

As Ferramentas Azure IoT Edge fornecem modelos de projeto para todas as línguas de módulos IoT Edge suportados no Estúdio Visual. Estes modelos têm todos os ficheiros e códigos que você precisa para implementar um módulo de trabalho para testar IoT Edge, ou dar-lhe um ponto de partida para personalizar o modelo com a sua própria lógica de negócio.

1. Lançar Visual Studio 2019 e selecionar **Criar Novo Projeto.**

2. Procure **por IoT Edge** e escolha o projeto **Azure IoT Edge (Windows amd64).** Clique em **Seguinte**.

   ![Criar um novo projeto Azure IoT Edge](./media/tutorial-csharp-module-windows/new-project.png)

3. Mude o nome do projeto e da solução para algo descritivo como **o CSharpTutorialApp.** Clique em **Criar** para criar o projeto.

   ![Configure um novo projeto Azure IoT Edge](./media/tutorial-csharp-module-windows/configure-project.png)

4. Configure o seu projeto com os seguintes valores:

   | Campo | Valor |
   | ----- | ----- |
   | Selecione um modelo | Selecione **C# Módulo**. |
   | Nome do projeto do módulo | Atribua o nome **CSharpModule** ao módulo. |
   | Repositório de imagem de estivador | Os repositórios de imagens incluem o nome do seu registo de contentor e o nome da sua imagem de contentor. A sua imagem de recipiente é pré-apultada do valor do nome do projeto do módulo. Substitua **o local:5000** pelo valor do **servidor login** do registo do seu contentor Azure. Pode recuperar o servidor 'Iniciar sessão' a partir da página de visão geral do seu registo de contentores no portal Azure. <br><br> O repositório de imagem final parece \<registry name\> .azurecr.io/csharpmodule. |

   ![Configure o seu projeto para dispositivo alvo, tipo de módulo e registo de contentores](./media/tutorial-csharp-module-windows/add-application-and-module.png)

5. **Selecione Adicionar** para criar o projeto.

### <a name="add-your-registry-credentials"></a>Adicionar as credenciais do registo

O manifesto de implantação partilha as credenciais para o seu registo de contentores com o tempo de execução IoT Edge. O runtime precisa destas credenciais para solicitar as imagens privadas para o dispositivo IoT Edge. Utilize as credenciais da secção de **chaves de acesso** do seu registo de contentores Azure.

1. No explorador de soluções Visual Studio, abra a **deployment.template.jsem** arquivo.

2. Encontre a **propriedade registroscredentais** nas propriedades $edgeAgent desejadas. Deve ter o seu endereço de registo preenchido automaticamente a partir das informações fornecidas ao criar o projeto, e, em seguida, os campos de nome de utilizador e palavra-passe devem conter nomes variáveis. Por exemplo:

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "$CONTAINER_REGISTRY_USERNAME_<registry name>",
       "password": "$CONTAINER_REGISTRY_PASSWORD_<registry name>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

3. Abra o ficheiro **.env** na solução do módulo. (Está escondido por padrão no Solution Explorer, pelo que poderá ser necessário selecionar o botão **Mostrar Todos os Ficheiros** para o exibir.) O ficheiro .env deve conter o mesmo nome de utilizador e variáveis de palavra-passe que viu no deployment.template.jsficheiro.

4. Adicione o **nome de utilizador** e os valores de **palavra-passe** do seu registo de contentores Azure.

5. Guarde as alterações no ficheiro .env.

### <a name="update-the-module-with-custom-code"></a>Atualizar o módulo com o código personalizado

O código do módulo predefinido recebe mensagens numa fila de entrada e transmite-as através de uma fila de saída. Vamos adicionar algum código adicional para que o módulo processe as mensagens na borda antes de encaminhá-las para o IoT Hub. Atualize o módulo de modo a analisar os dados de temperatura em cada mensagem e só envie a mensagem para o IoT Hub se a temperatura exceder um determinado limiar.

1. In Visual Studio, open **CSharpModule**  >  **Program.cs**.

2. Na parte superior do espaço de nomes **CSharpModule**, adicione três declarações **em utilização** para os tipos que são utilizados mais tarde:

    ```csharp
    using System.Collections.Generic;     // For KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // For TwinCollection
    using Newtonsoft.Json;                // For JsonConvert
    ```

3. Adicione a **variável de regulação** de temperatura à classe **Programa** após a variável contadora. A variável de temperaturaStensa define o valor que a temperatura medida deve exceder para que os dados sejam enviados para o hub IoT.

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

4. Adicione as classes **MessageBody,** **Machine**e **Ambiente** à classe **Programa** após as declarações variáveis. Estas classes definem o esquema esperado para o corpo das mensagens a receber.

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

5. Encontre o método **Init.** Este método cria e configura um objeto **ModuleClient,** que permite ao módulo ligar-se ao tempo de execução local do Azure IoT Edge para enviar e receber mensagens. O código também regista uma chamada para receber mensagens de um hub IoT Edge através do ponto final **de entrada1.**

   Substitua todo o método Init pelo seguinte código:

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

   Este método init atualizado ainda configura a ligação ao tempo de execução IoT Edge com o MóduloClient, mas também adiciona uma nova funcionalidade. Lê as propriedades desejadas do módulo twin para recuperar o valor **de temperaturaThreshold.** Em seguida, cria uma chamada que ouve quaisquer futuras atualizações das propriedades desejadas pelo módulo twin. Com esta chamada, pode atualizar remotamente o limiar de temperatura no módulo twin e as alterações serão incorporadas no módulo.

   O método Init atualizado também altera o método **SetInputMessageHandlerAsync.** No código de amostra, as mensagens recebidas no *input1* são processadas com a função *PipeMessage,* mas queremos alterá-lo para utilizar a função *FilterMessages* que iremos criar nos seguintes passos.

6. Adicione um novo método **onDesiredPropertiesUpdate** à classe **Programa.** Este método recebe atualizações nas propriedades pretendidas do módulo duplo e atualiza a variável **temperatureThreshold** para corresponder. Todos os módulos têm o seu próprio módulo duplo, que lhe permite configurar o código em execução no interior de um módulo diretamente a partir da cloud.

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

7. Remova o método **pipemessage** da amostra e substitua-o por um novo método **FilterMessages.** Este método é chamado sempre que o módulo recebe uma mensagem do hub do IoT Edge. Ele filtra as mensagens que comunicam temperaturas inferiores ao limiar de temperatura definido através do módulo duplo. Também adiciona a propriedade **MessageType** à mensagem com o valor definido para **Alerta**.

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

9. Abra a **deployment.template.jsficheiro na** sua solução IoT Edge. Este ficheiro diz ao agente IoT Edge quais os módulos a implementar, neste caso **SimulaedTemperatureSensor** e **CSharpModule,** e diz ao hub IoT Edge como encaminhar mensagens entre eles.

10. Adicione o módulo duplo **CSharpModule** ao manifesto de implementação. Insira o seguinte conteúdo JSON na parte inferior da secção **modulesContent**, após o módulo duplo **$edgeHub**:

    ```json
       "CSharpModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
    ```

    ![Adicione módulo twin ao modelo de implementação](./media/tutorial-csharp-module-windows/module-twin.png)

11. Guarde o ficheiro deployment.template.json.

## <a name="build-and-push-your-module"></a>Construa e empurre o seu módulo

Na secção anterior, criou uma solução do IoT Edge e adicionou código ao **CSharpModule** para filtrar mensagens onde a temperatura comunicada da máquina é inferior ao limiar aceitável. Agora, tem de criar a solução como uma imagem de contentor e enviá-la para o registo de contentor.

1. Use o seguinte comando para iniciar sôm no Docker na sua máquina de desenvolvimento. Utilize o nome de utilizador, palavra-passe e servidor de login do seu registo de contentores Azure. Pode recuperar estes valores a partir da secção de **teclas de acesso** do seu registo no portal Azure.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Pode receber um aviso de segurança recomendando a utilização de `--password-stdin` . Embora essa melhor prática seja recomendada para cenários de produção, está fora do âmbito deste tutorial. Para mais informações, consulte a referência de login do [estivador.](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin)

2. No explorador de soluções Visual Studio, clique com o botão direito no nome do projeto que pretende construir. O nome padrão é **AzureIotEdgeApp1** e uma vez que está a construir um módulo Windows, a extensão deve ser **Windows.Amd64**.

3. Selecione **Módulos de Borda IoT**de construção e push .

   O comando de construção e pressão inicia três operações. Em primeiro lugar, cria uma nova pasta na solução chamada **config** que detém o manifesto de implantação completo, construído a partir de informações no modelo de implementação e outros ficheiros de solução. Em segundo lugar, funciona `docker build` para construir a imagem do contentor com base no arquivo apropriado para a arquitetura do seu alvo. Em seguida, corre `docker push` para empurrar o repositório de imagem para o seu registo de contentores.

   Este processo pode demorar vários minutos na primeira vez, mas é mais rápido da próxima vez que executar os comandos.

## <a name="deploy-modules-to-device"></a>Implementar módulos para dispositivo

Utilize o Visual Studio Cloud Explorer e a extensão Azure IoT Edge Tools para implementar o projeto do módulo no seu dispositivo IoT Edge. Já tem um manifesto de implantação preparado para o seu cenário, o **deployment.windows-amd64.jsficheiro na** pasta config. Agora tudo o que precisa de fazer é selecionar um dispositivo para receber a implementação.

Certifique-se de que o seu dispositivo IoT Edge está a funcionar.

1. No Visual Studio Cloud Explorer, expanda os recursos para ver a sua lista de dispositivos IoT.

2. Clique com o botão direito no nome do dispositivo IoT Edge que deseja receber a implementação.

3. Selecione **Criar Implementação**.

4. No explorador de ficheiros, selecione o **deployment.windows-amd64.jsficheiro** na pasta config da sua solução.

5. Refresh Cloud Explorer para ver os módulos implantados listados no seu dispositivo.

## <a name="view-generated-data"></a>Ver os dados gerados

Depois de aplicar o manifesto de implementação no seu dispositivo IoT Edge, o runtime do IoT Edge no dispositivo recolhe as novas informações de implementação e começa a ser executado no mesmo. Quaisquer módulos em execução no dispositivo que não estão incluídos no manifesto de implementação são parados. Quaisquer módulos em falta do dispositivo são iniciados.

Pode utilizar a extensão IoT Edge Tools para visualizar as mensagens à medida que chegam ao seu Hub IoT.

1. No Visual Studio Cloud Explorer, selecione o nome do seu dispositivo IoT Edge.

2. Na lista **de Ações,** selecione **Start Monitoring Built-in Event Endpoint**.

3. Veja as mensagens que chegam ao seu Hub IoT. Pode demorar algum tempo até que as mensagens cheguem, porque as alterações que fizemos ao código CSharpModule aguardam até que a temperatura da máquina atinja os 25 graus antes de enviar mensagens. Adiciona também o tipo de mensagem **Alerta** a todas as mensagens que atinjam esse limiar de temperatura.

   ![Ver mensagens que chegam ao IoT Hub](./media/tutorial-csharp-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Editar o módulo twin

Usamos o módulo CSharpModule twin para definir o limiar de temperatura em 25 graus. Pode utilizar o módulo twin para alterar a funcionalidade sem ter de atualizar o código do módulo.

1. No Visual Studio, abra a **deployment.windows-amd64.jsem** arquivo. (Não o ficheiro de implementação.modelo. Se não vir o manifesto de implantação no ficheiro config no explorador de solução, selecione o ícone **mostrar todos os ficheiros** na barra de ferramentas do explorador.)

2. Encontre o gémeo CSharpModule e altere o valor do parâmetro **de temperaturaStenuem** para uma nova temperatura de 5 graus a 10 graus mais alto do que a temperatura mais recente reportada.

3. Guarde o **deployment.windows-amd64.jsarquivado.**

4. Siga novamente os passos de implantação para aplicar o manifesto de implantação atualizado no seu dispositivo.

5. Monitorize as mensagens de entrada de dispositivo para nuvem. Deve ver as mensagens paradas até que o novo limiar de temperatura seja atingido.

## <a name="clean-up-resources"></a>Limpar os recursos

Se planeia avançar para o próximo artigo recomendado, pode manter os recursos e as configurações que criou e reutilizá-los. Também pode continuar a utilizar o mesmo dispositivo IoT Edge como um dispositivo de teste.

Caso contrário, pode eliminar as configurações locais e os recursos Azure que utilizou neste artigo para evitar encargos.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou uma função do módulo do IoT Edge com código para filtrar os dados não processados que são gerados pelo seu dispositivo IoT Edge.

Pode continuar nos próximos tutoriais para saber como o Azure IoT Edge pode ajudá-lo a implementar serviços em nuvem Azure para processar e analisar dados no limite.

> [!div class="nextstepaction"]
> [Funções](tutorial-deploy-function.md) 
>  [Stream Analytics](tutorial-deploy-stream-analytics.md) 
>  [Aprendizagem automática](tutorial-deploy-machine-learning.md) 
>  [Serviço de Visão Personalizada](tutorial-deploy-custom-vision.md)
