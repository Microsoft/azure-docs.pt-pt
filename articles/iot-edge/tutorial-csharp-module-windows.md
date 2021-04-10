---
title: Tutorial - Desenvolver módulos C# para Windows utilizando a Azure IoT Edge
description: Este tutorial mostra-lhe como criar módulos IoT Edge com código C# e implantá-los em dispositivos Windows que estão a executar IoT Edge.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/03/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, amqp, devx-track-csharp
ms.openlocfilehash: 4e01b1ca9a3858ff31ad9b5da1d1159209c44330
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103464071"
---
# <a name="tutorial-develop-c-iot-edge-modules-using-windows-containers"></a>Tutorial: Desenvolver módulos IoT Edge C# utilizando recipientes Windows

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Este artigo mostra-lhe como usar o Visual Studio para desenvolver o código C# e implantá-lo num dispositivo Windows que está a executar Azure IoT Edge.

>[!NOTE]
>IoT Edge 1.1 LTS é o último canal de lançamento que irá suportar recipientes Windows. A partir da versão 1.2, os recipientes windows não são suportados. Considere usar ou mover-se para [IoT Edge para o Linux no Windows](iot-edge-for-linux-on-windows.md) para executar IoT Edge em dispositivos Windows.

Pode utilizar módulos Azure IoT Edge para implementar código que implementa a sua lógica de negócio diretamente nos seus dispositivos IoT Edge. Este tutorial explica-lhe como criar e implementar um módulo do IoT Edge que filtra dados de sensores.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Utilize o Visual Studio para criar um módulo IoT Edge baseado no C# SDK.
> * Use o Visual Studio e o Docker para criar uma imagem docker e publicá-la no seu registo.
> * Implemente o módulo no seu dispositivo IoT Edge.
> * Veja os dados gerados.

O módulo do IoT Edge que criou neste tutorial filtra os dados de temperatura que são gerados pelo seu dispositivo. O módulo envia mensagens a montante apenas se a temperatura exceder um limiar especificado. Este tipo de análise no Edge é útil para reduzir a quantidade de dados que são comunicados e armazenados na cloud.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial demonstra como desenvolver um módulo em C# utilizando o Visual Studio 2019 e, em seguida, implantá-lo num dispositivo Windows. Se estiver a desenvolver módulos utilizando recipientes Linux, vá para [desenvolver módulos IoT Edge utilizando recipientes Linux.](tutorial-csharp-module.md)

Para compreender as suas opções de desenvolvimento e implantação de módulos C# utilizando recipientes Windows, consulte a seguinte tabela:

| C# | Código do Estúdio Visual &nbsp; &nbsp; | Estúdio Visual 2017 &nbsp; e &nbsp; 2019 |
| -- | :------------------: | :------------------: |
| Windows AMD64 desenvolver | ![Desenvolver módulos C# para WinAMD64 em Código de Estúdio Visual](./media/tutorial-c-module/green-check.png) | ![Desenvolver módulos C# para WinAMD64 em Estúdio Visual](./media/tutorial-c-module/green-check.png) |
| Depurar Windows AMD64 |   | ![Módulos Debug C# para WinAMD64 em Visual Studio](./media/tutorial-c-module/green-check.png) |

Antes de iniciar este tutorial, crie o seu ambiente de desenvolvimento seguindo as instruções nos [módulos Develop IoT Edge utilizando](tutorial-develop-for-windows.md) o tutorial de recipientes Windows. Depois de o completar, o seu ambiente conterá os seguintes pré-requisitos:

* Um [hub IoT](../iot-hub/iot-hub-create-through-portal.md) gratuito ou de nível padrão em Azure.
* Um [dispositivo Windows que está a executar a Azure IoT Edge](how-to-install-iot-edge-windows-on-windows.md).
* Um registo de contentores, como [o Registo do Contentor de Azure.](../container-registry/index.yml)
* [Visual Studio 2019,](/visualstudio/install/install-visual-studio)configurado com a extensão [Azure IoT Edge Tools.](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools)
* [Docker Desktop](https://docs.docker.com/docker-for-windows/install/), configurado para executar recipientes Windows.

> [!TIP]
> Se estiver a utilizar o Visual Studio 2017 (versão 15.7 ou posterior), descarregue e instale Azure IoT Edge Tools para Visual Studio 2017 do [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools).

## <a name="create-a-module-project"></a>Criar um projeto de módulo

Nesta secção, você cria um projeto de módulo IoT Edge usando o Visual Studio e a extensão Azure IoT Edge Tools. Depois de criar um modelo de projeto, irá adicionar um novo código para que o módulo filtra as mensagens com base nas suas propriedades relatadas.

### <a name="create-a-new-project"></a>Criar um novo projeto

Azure IoT Edge Tools fornece modelos de projeto para todas as línguas de módulos IoT Edge suportados no Estúdio Visual. Estes modelos têm todos os ficheiros e códigos necessários para implementar um módulo de trabalho para testar o IoT Edge. Eles também podem dar-lhe um ponto de partida para personalizá-los com a sua própria lógica de negócio.

1. Open Visual Studio 2019 e, em seguida, **selecione Create New Project**.

1. No painel **de projetos Criar um novo painel** de projetos, procure **o IoT Edge** e, em seguida, na lista de resultados, selecione o projeto **Azure IoT Edge (Windows amd64).**

   ![Screenshot do painel IoT Edge "Criar um novo projeto".](./media/tutorial-csharp-module-windows/new-project.png)

1. Selecione **Seguinte**.

    O **Configure o seu novo painel** de projeto abre.

   ![Screenshot do painel "Configurar o seu novo projeto".](./media/tutorial-csharp-module-windows/configure-project.png)

1. No **Configure** o seu novo painel de projeto, mude o nome do projeto e solução para algo mais descritivo, como **o CSharpTutorialApp.** 

1. Selecione **Create** (Criar) para criar o projeto.

   O painel do Módulo de **Adição** abre-se.

   ![Screenshot do painel "Adicionar Módulo" para configurar o seu projeto.](./media/tutorial-csharp-module-windows/add-application-and-module.png)

1. Na **página Configure a sua nova** página de projeto, faça o seguinte:

   a. No painel esquerdo, selecione o modelo **do módulo C.**  
   b. Na caixa nome do **módulo,** insira **cSharpModule**.  
   c. Na caixa **de url do Repositório,** substitua o **local:5000** pelo valor do **servidor de Login** do seu registo de contentores Azure, no seguinte formato: `<registry name>.azurecr.io/csharpmodule`

    > [!NOTE]
    > Os repositórios de imagens incluem o nome do seu registo de contentor e o nome da sua imagem de contentor. A sua imagem de recipiente é pré-apultada do valor do projeto do módulo.  Pode recuperar o servidor de login a partir da página geral do seu registo de contentores no portal Azure.

1. **Selecione Adicionar** para criar o projeto.

### <a name="add-your-registry-credentials"></a>Adicionar as credenciais do registo

O manifesto de implantação partilha as credenciais para o seu registo de contentores com o tempo de execução IoT Edge. O runtime precisa destas credenciais para solicitar as imagens privadas para o dispositivo IoT Edge. Utilize as credenciais da secção de **chaves de acesso** do seu registo de contentores Azure.

1. No Visual Studio Solution Explorer, abra a *deployment.template.jsem* arquivo.

1. Procure a propriedade **registrária** nas propriedades $edgeAgent desejadas. O endereço de registo da propriedade deve ser preenchido automaticamente com a informação que forneceu quando criou o projeto. Os campos de nome de utilizador e palavra-passe devem conter nomes variáveis. Por exemplo:

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "$CONTAINER_REGISTRY_USERNAME_<registry name>",
       "password": "$CONTAINER_REGISTRY_PASSWORD_<registry name>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

1. Abra o arquivo ambiente (ENV) na solução do módulo. Por predefinição, o ficheiro está escondido no Solution Explorer, pelo que poderá ser necessário selecionar o botão **Mostrar Todos os Ficheiros** para o exibir. O ficheiro ENV deve conter o mesmo nome de utilizador e variáveis de palavra-passe que viu no *deployment.template.jsno* ficheiro.

1. Adicione o **nome de utilizador** e os valores de **palavra-passe** do seu registo de contentores Azure.

1. Guarde as suas alterações no ficheiro ENV.

### <a name="update-the-module-with-custom-code"></a>Atualizar o módulo com o código personalizado

O código do módulo predefinido recebe mensagens numa fila de entrada e passa-as através de uma fila de saída. Vamos adicionar algum código adicional para que o módulo processe as mensagens na borda antes de encaminhá-las para o seu hub IoT. Atualize o módulo de modo a analisar os dados de temperatura em cada mensagem e envie a mensagem para o hub IoT apenas se a temperatura exceder um determinado limiar.

1. No Visual Studio, selecione O Programa **CSharpModule.cs**  >  .

1. Na parte superior do espaço de nomes **CSharpModule**, adicione três declarações **em utilização** para os tipos que são utilizados mais tarde:

    ```csharp
    using System.Collections.Generic;     // For KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // For TwinCollection
    using Newtonsoft.Json;                // For JsonConvert
    ```

1. Adicione a **variável de regulação** de temperatura à classe **Programa** após a variável contadora. A variável de temperaturaStensa define o valor que a temperatura medida deve exceder para que os dados sejam enviados para o hub IoT.

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

1. Adicione as classes **MessageBody,** **Machine** e **Ambiente** à classe **Programa** após as declarações variáveis. Estas classes definem o esquema esperado para o corpo das mensagens a receber.

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

1. Procure o método **Init.** Este método cria e configura um objeto **ModuleClient,** que permite ao módulo ligar-se ao tempo de execução local do Azure IoT Edge para enviar e receber mensagens. O código também regista uma chamada para receber mensagens de um hub IoT Edge através do ponto final **de entrada1.**

   Substitua todo o método Init pelo seguinte código:

   ```csharp
   static async Task Init()
   {
       AmqpTransportSettings amqpSetting = new AmqpTransportSettings(TransportType.Amqp_Tcp_Only);
       ITransportSettings[] settings = { amqpSetting };

       // Open a connection to the Edge runtime.
       ModuleClient ioTHubModuleClient = await ModuleClient.CreateFromEnvironmentAsync(settings);
       await ioTHubModuleClient.OpenAsync();
       Console.WriteLine("IoT Hub module client initialized.");

       // Read the TemperatureThreshold value from the module twin's desired properties.
       var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
       await OnDesiredPropertiesUpdate(moduleTwin.Properties.Desired, ioTHubModuleClient);

       // Attach a callback for updates to the module twin's desired properties.
       await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertiesUpdate, null);

       // Register a callback for messages that are received by the module.
       await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
   }
   ```

   Este método init atualizado ainda configura a ligação ao tempo de execução IoT Edge com o MóduloClient, mas também adiciona uma nova funcionalidade. Lê as propriedades desejadas do módulo twin para recuperar o valor **de temperaturaThreshold.** Em seguida, cria uma chamada que ouve quaisquer futuras atualizações às propriedades desejadas do módulo twin. Com esta chamada, pode atualizar remotamente o limiar de temperatura no módulo twin e as alterações serão incorporadas no módulo.

   O método Init atualizado também altera o método **SetInputMessageHandlerAsync.** No código de amostra, as mensagens recebidas no *input1* são processadas com a função *PipeMessage,* mas queremos alterá-lo para utilizar a função *FilterMessages* que iremos criar nos seguintes passos.

1. Adicione um novo método **onDesiredPropertiesUpdate** à classe **Programa.** Este método recebe atualizações sobre as propriedades desejadas do módulo twin, e atualiza a **variável de velocidadeThreshold** para combinar. Todos os módulos têm o seu próprio módulo duplo, que lhe permite configurar o código em execução no interior de um módulo diretamente a partir da cloud.

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

1. Remova o método **pipemessage** da amostra e substitua-o por um novo método **FilterMessages.** Este método é chamado sempre que o módulo recebe uma mensagem do hub do IoT Edge. Filtra mensagens que reportam temperaturas abaixo do limiar de temperatura definido através do módulo gémeo. O método também adiciona a propriedade **MessageType** à mensagem com o valor definido para **Alerta**.

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

1. Guarde o ficheiro *Program.cs*.

1. Abra a *deployment.template.jsficheiro na* sua solução IoT Edge. Este ficheiro diz ao agente IoT Edge quais os módulos a implementar e diz ao hub IoT Edge como encaminhar mensagens entre eles. Aqui, os módulos a implementar são **SimulaçãoSteperatureSensor** e **CSharpModule**.

1. Adicione o módulo duplo **CSharpModule** ao manifesto de implementação. Insira o seguinte conteúdo JSON na parte inferior da `modulesContent` secção, depois do **$edgeHub** módulo twin:

    ```json
       "CSharpModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
    ```

    ![Screenshot mostrando o módulo twin sendo adicionado ao modelo de implementação.](./media/tutorial-csharp-module-windows/module-twin.png)

1. Guarde o ficheiro *deployment.template.json*.

## <a name="build-and-push-your-module"></a>Construa e empurre o seu módulo

Na secção anterior, criou uma solução IoT Edge e adicionou código ao **CSharpModule** para filtrar mensagens onde a temperatura da máquina reportada está abaixo do limiar aceitável. Agora, tem de criar a solução como uma imagem de contentor e enviá-la para o registo de contentor.

### <a name="sign-in-to-docker"></a>Inscreva-se no Docker

1. Use o seguinte comando para iniciar sôm no Docker na sua máquina de desenvolvimento. Utilize o nome de utilizador, palavra-passe e servidor de login do seu registo de contentores Azure. Pode recuperar estes valores a partir da secção de **teclas de acesso** do seu registo no portal Azure.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Poderá receber um aviso de segurança que recomende a utilização de `--password-stdin` . Embora recomendemos isto como uma melhor prática para cenários de produção, está fora do âmbito deste tutorial. Para mais informações, consulte a referência de login do [estivador.](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin)

### <a name="build-and-push"></a>Construir e empurrar

1. No Visual Studio Solution Explorer, clique com o botão direito no nome do projeto que pretende construir. O nome padrão é **AzureIotEdgeApp1** e, como está a construir um módulo Windows, a extensão deve ser **Windows.Amd64**.

1. Selecione **Módulos de Borda IoT** de construção e push .

   O comando de construção e pressão inicia três operações:
   * Em primeiro lugar, cria uma nova pasta na solução denominada *config*, que detém o manifesto de implantação completo. É construído a partir de informações no modelo de implementação e outros ficheiros de solução. 
   * Em segundo lugar, funciona `docker build` para construir a imagem do contentor, com base no Dockerfile apropriado para a sua arquitetura alvo. 
   * Finalmente, corre `docker push` para empurrar o repositório de imagem para o seu registo de contentores.

   Este processo pode demorar vários minutos na primeira vez, mas será mais rápido da próxima vez que executar os comandos.

## <a name="deploy-modules-to-the-device"></a>Implementar módulos para o dispositivo

Utilize o Visual Studio Cloud Explorer e a extensão Azure IoT Edge Tools para implementar o projeto do módulo no seu dispositivo IoT Edge. Já preparou um manifesto de implantação para o seu cenário, o *deployment.windows-amd64.jsarquivado* na pasta *config.* Agora tudo o que precisa de fazer é selecionar um dispositivo para receber a implementação.

Certifique-se de que o seu dispositivo IoT Edge está a funcionar.

1. No Visual Studio Cloud Explorer, expanda os recursos para ver a sua lista de dispositivos IoT.

1. Clique com o botão direito no nome do dispositivo IoT Edge que deseja receber a implementação.

1. Selecione **Criar Implementação**.

1. No Visual Studio File Explorer, selecione a *deployment.windows-amd64.jsno* ficheiro na pasta *config* da sua solução.

1. Refresh Cloud Explorer para ver os módulos implantados que estão listados sob o seu dispositivo.

## <a name="view-generated-data"></a>Ver os dados gerados

Depois de aplicar o manifesto de implantação no seu dispositivo IoT Edge, o tempo de funcionamento do IoT Edge no dispositivo recolhe as novas informações de implantação e começa a executá-lo. Todos os módulos que estejam a funcionar no dispositivo mas não incluídos no manifesto de implantação estão parados. Todos os módulos que faltam no dispositivo são iniciados.

Pode utilizar a extensão IoT Edge Tools para visualizar as mensagens à medida que chegam ao seu hub IoT.

1. No Visual Studio Cloud Explorer, selecione o nome do seu dispositivo IoT Edge.

1. Na lista **de Ações,** selecione **Start Monitoring Built-in Event Endpoint**.

1. Veja as mensagens que estão a chegar ao seu hub IoT. Pode demorar algum tempo até que as mensagens cheguem, porque o dispositivo IoT Edge tem de receber a sua nova implementação e iniciar todos os módulos. As alterações ao código CSharpModule devem aguardar até que a temperatura da máquina atinja os 25 graus antes de as mensagens poderem ser enviadas. O código também adiciona o tipo de mensagem **Alerta** a quaisquer mensagens que atinjam esse limiar de temperatura.

   ![Screenshot da janela de saída que exibe mensagens que estão a chegar ao hub IoT.](./media/tutorial-csharp-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Editar o módulo twin

Usou o módulo CSharpModule twin para fixar o limiar de temperatura em 25 graus. Pode utilizar o módulo twin para alterar a funcionalidade sem ter de atualizar o código do módulo.

1. No Visual Studio, abra a *deployment.windows-amd64.jsem* arquivo. 

   *Não* abra o ficheiro *de implementação.modelo.* Se não vir o manifesto de implantação no ficheiro *config* no Solution Explorer, selecione o ícone **'Mostrar todos os ficheiros'** na barra de ferramentas 'Solução Explorer'.

1. Procure o gémeo CSharpModule e mude o valor do parâmetro **de temperaturaThreshold** para uma nova temperatura que seja 5 a 10 graus mais alta que a temperatura mais recente reportada.

1. Guarde o *deployment.windows-amd64.jsarquivado.*

1. Siga novamente as instruções de utilização para aplicar o manifesto de implantação atualizado no seu dispositivo.

1. Monitorize as mensagens de entrada de dispositivo para nuvem. As mensagens devem parar até que o novo limiar de temperatura seja atingido.

## <a name="clean-up-resources"></a>Limpar os recursos

Se pretender continuar para o próximo artigo recomendado, pode conservar e reutilizar os recursos e configurações que criou neste tutorial. Também pode continuar a utilizar o mesmo dispositivo IoT Edge como um dispositivo de teste.

Caso contrário, para evitar incorrer em encargos, pode eliminar as configurações locais e os recursos Azure que utilizou aqui.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou uma função do módulo do IoT Edge com código para filtrar os dados não processados que são gerados pelo seu dispositivo IoT Edge.

Para saber como o Azure IoT Edge pode ajudá-lo a implementar serviços em nuvem Azure para processar e analisar dados no limite, continue para os próximos tutoriais.

> [!div class="nextstepaction"]
> Funções Azure [](tutorial-deploy-function.md) 
>  [Azure Stream Analytics](tutorial-deploy-stream-analytics.md) 
>  [Aprendizagem de Máquinas](tutorial-deploy-machine-learning.md) 
>  Azure [Serviço de visão personalizada](tutorial-deploy-custom-vision.md)
