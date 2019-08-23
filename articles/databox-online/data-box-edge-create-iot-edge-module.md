---
title: C#IoT Edge módulo para Azure Data Box Edge | Microsoft Docs
description: Saiba como desenvolver um C# módulo IOT Edge que pode ser implantado em seu data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/06/2019
ms.author: alkohli
ms.openlocfilehash: daf7b01725a931b8fa76be14e06e2b32cffe5da6
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69900638"
---
# <a name="develop-a-c-iot-edge-module-to-move-files-on-data-box-edge"></a>Desenvolver um C# módulo IOT Edge para mover arquivos em data Box Edge

Este artigo explica como criar um módulo IoT Edge para implantação com o dispositivo Data Box Edge. O Azure Data Box Edge é uma solução de armazenamento que permite processar dados e enviá-los através de uma rede para o Azure.

Você pode usar Azure IoT Edge módulos com seus Data Box Edge para transformar os dados conforme movidos para o Azure. O módulo usado neste artigo implementa a lógica para copiar um arquivo de um compartilhamento local para um compartilhamento de nuvem em seu dispositivo de Data Box Edge.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Crie um registro de contêiner para armazenar e gerenciar seus módulos (imagens do Docker).
> * Crie um módulo IoT Edge para implantar em seu dispositivo Data Box Edge.


## <a name="about-the-iot-edge-module"></a>Sobre o módulo IoT Edge

O dispositivo Data Box Edge pode implantar e executar módulos IoT Edge. Os módulos de borda são essencialmente contêineres do Docker que executam uma tarefa específica, como ingerir uma mensagem de um dispositivo, transformar uma mensagem ou enviar uma mensagem para um hub IoT. Neste artigo, você criará um módulo que copia arquivos de um compartilhamento local para um compartilhamento de nuvem em seu dispositivo Data Box Edge.

1. Os arquivos são gravados no compartilhamento local em seu dispositivo Data Box Edge.
2. O gerador de eventos de arquivo cria um evento de arquivo para cada arquivo gravado no compartilhamento local. Os eventos de arquivo também são gerados quando um arquivo é modificado. Em seguida, os eventos de arquivo são enviados para IoT Edge Hub (em tempo de execução IoT Edge).
3. O módulo personalizado IoT Edge processa o evento file para criar um objeto de evento File que também contém um caminho relativo para o arquivo. O módulo gera um caminho absoluto usando o caminho de arquivo relativo e copia o arquivo do compartilhamento local para o compartilhamento de nuvem. O módulo, em seguida, exclui o arquivo do compartilhamento local.

![Como Azure IoT Edge módulo funciona em Data Box Edge](./media/data-box-edge-create-iot-edge-module/how-module-works-1.png)

Depois que o arquivo estiver no compartilhamento de nuvem, ele será automaticamente carregado em sua conta de armazenamento do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que tem:

- Um dispositivo Data Box Edge que está executando o.

    - O dispositivo também tem um recurso de Hub IoT associado.
    - O dispositivo tem a função de computação de borda configurada.
    Para obter mais informações, vá para [Configurar a computação](data-box-edge-deploy-configure-compute.md#configure-compute) para seu data Box Edge.

- Os seguintes recursos de desenvolvimento:

    - [Visual Studio Code](https://code.visualstudio.com/).
    - [Extensão C# para Visual Studio Code (com tecnologia da OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).
    - [Extensão Azure IoT Edge para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).
    - [SDK de .NET Core 2.1](https://www.microsoft.com/net/download).
    - [Docker CE](https://store.docker.com/editions/community/docker-ce-desktop-windows). Talvez seja necessário criar uma conta para baixar e instalar o software.

## <a name="create-a-container-registry"></a>Criar um registo de contentores

Um registo de contentor do Azure é um registo do Docker privado no Azure, onde pode armazenar e gerir as imagens privadas de contentor do Docker. Os dois serviços de registro do Docker populares disponíveis na nuvem são o registro de contêiner do Azure e o Hub do Docker. Este artigo usa o registro de contêiner.

1. Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. Selecione **criar um recurso > contêineres > registro de contêiner**. Clique em **Criar**.
3. Lhe

   1. Um **nome de registro** exclusivo no Azure que contém de 5 a 50 caracteres alfanuméricos.
   2. Escolha uma **assinatura**.
   3. Crie um novo **grupo de recursos**ou escolha um existente.
   4. Selecione uma **Localização**. É recomendável que esse local seja o mesmo que o que está associado ao recurso de Data Box Edge.
   5. Mude **Utilizador administrador** para **Ativar**.
   6. Defina a SKU como **básica**.

      ![Criar registo de contentor](./media/data-box-edge-create-iot-edge-module/create-container-registry-1.png)
 
4. Selecione **Criar**.
5. Depois de o registo de contentores ser criado, navegue para o mesmo e selecione **Chaves de acesso**.

    ![Obter chaves de acesso](./media/data-box-edge-create-iot-edge-module/get-access-keys-1.png)
 
6. Copie os valores de **Servidor de início de sessão**, **Nome de utilizador** e **Palavra-passe**. Você usará esses valores posteriormente para publicar a imagem do Docker no registro e adicionar as credenciais do registro ao tempo de execução de Azure IoT Edge.


## <a name="create-an-iot-edge-module-project"></a>Criar um projeto do módulo do IoT Edge

As etapas a seguir criam um projeto de módulo IoT Edge com base no SDK do .NET Core 2,1. O projeto usa Visual Studio Code e a extensão Azure IoT Edge.

### <a name="create-a-new-solution"></a>Criar uma nova solução

Crie um modelo de solução C# que pode personalizar com o seu próprio código.

1. Em Visual Studio Code, selecione **exibir > paleta de comandos** para abrir a paleta de comandos vs Code.
2. Na paleta de comandos, insira e execute o comando **Azure:**  Entre e siga as instruções para entrar na sua conta do Azure. Se já iniciou sessão, pode ignorar este passo.
3. Na paleta de comandos, insira e execute o comando **Azure IOT Edge: Nova solução**de IOT Edge. Na paleta de comandos, indique as seguintes informações para criar a sua solução:

    1. Selecione a pasta onde quer criar a solução.
    2. Indique um nome para a sua solução ou aceite a predefinição **EdgeSolution**.
    
        ![Criar nova solução 1](./media/data-box-edge-create-iot-edge-module/create-new-solution-1.png)

    3. Selecione **Módulo C#** como modelo de módulo.
    4. Substitua o nome do módulo padrão pelo nome que você deseja atribuir, nesse caso, é **FileCopyModule**.
    
        ![Criar nova solução 2](./media/data-box-edge-create-iot-edge-module/create-new-solution-2.png)

    5. Especifique o registro de contêiner que você criou na seção anterior como o repositório de imagens para seu primeiro módulo. Substitua **localhost:5000** pelo valor do servidor de início de sessão que copiou.

        A cadeia de caracteres final `<Login server name>/<Module name>`é semelhante a. Neste exemplo, a cadeia de caracteres é `mycontreg2.azurecr.io/filecopymodule`:.

        ![Criar nova solução 3](./media/data-box-edge-create-iot-edge-module/create-new-solution-3.png)

4. Vá para **arquivo > abrir pasta**.

    ![Criar nova solução 4](./media/data-box-edge-create-iot-edge-module/create-new-solution-4.png)

5. Procure e aponte para a pasta **EdgeSolution** que você criou anteriormente. A janela de VS Code carrega seu espaço de trabalho de solução IoT Edge com seus cinco componentes de nível superior. Você não Editará a pasta **. vscode** , o arquivo **. gitignore** , o arquivo **. env** e o **Deployment. Template. JSON** neste artigo.
    
    O único componente que você modifica é a pasta modules. Essa pasta tem o C# código para o módulo e os arquivos do Docker para criar seu módulo como uma imagem de contêiner.

    ![Criar nova solução 5](./media/data-box-edge-create-iot-edge-module/create-new-solution-5.png)

### <a name="update-the-module-with-custom-code"></a>Atualizar o módulo com o código personalizado

1. No VS Code Explorer, abra **modules > FileCopyModule > Program.cs**.
2. Na parte superior do **namespace FileCopyModule**, adicione as seguintes instruções using para os tipos que são usados posteriormente. **Microsoft. Azure. Devices. Client. Transport. MQTT** é um protocolo para enviar mensagens para IOT Edge Hub.

    ```
    namespace FileCopyModule
    {
        using Microsoft.Azure.Devices.Client.Transport.Mqtt;
        using Newtonsoft.Json;
    ```
3. Adicione a variável **InputFolderPath** e **OutputFolderPath** à classe Program.

    ```
    class Program
        {
            static int counter;
            private const string InputFolderPath = "/home/input";
            private const string OutputFolderPath = "/home/output";
    ```

4. Imediatamente após a etapa anterior, adicione a classe fileevent para definir o corpo da mensagem.

    ```
    /// <summary>
    /// The FileEvent class defines the body of incoming messages. 
    /// </summary>
    private class FileEvent
    {
        public string ChangeType { get; set; }

        public string ShareRelativeFilePath { get; set; }

        public string ShareName { get; set; }
    }
    ```

5. No **método Init**, o código cria e configura um objeto **ModuleClient** . Esse objeto permite que o módulo se conecte ao tempo de execução de Azure IoT Edge local usando o protocolo MQTT para enviar e receber mensagens. A cadeia de conexão usada no método Init é fornecida ao módulo pelo tempo de execução IoT Edge. O código registra um retorno de chamada FileCopy para receber mensagens de um hub IoT Edge por meio do ponto de extremidade **entrada1** . Substitua o **método Init** pelo código a seguir.

    ```
    /// <summary>
    /// Initializes the ModuleClient and sets up the callback to receive
    /// messages containing file event information
    /// </summary>
    static async Task Init()
    {
        MqttTransportSettings mqttSetting = new MqttTransportSettings(TransportType.Mqtt_Tcp_Only);
        ITransportSettings[] settings = { mqttSetting };

        // Open a connection to the IoT Edge runtime
        ModuleClient ioTHubModuleClient = await ModuleClient.CreateFromEnvironmentAsync(settings);
        await ioTHubModuleClient.OpenAsync();
        Console.WriteLine("IoT Hub module client initialized.");

        // Register callback to be called when a message is received by the module
        await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FileCopy, ioTHubModuleClient);
    }
    ```

6. Remova o código do **método de mensagem de pipe** e, em seu lugar, insirao código para FileCopy.

    ```
        /// <summary>
        /// This method is called whenever the module is sent a message from the IoT Edge Hub.
        /// This method deserializes the file event, extracts the corresponding relative file path, and creates the absolute input file path using the relative file path and the InputFolderPath.
        /// This method also forms the absolute output file path using the relative file path and the OutputFolderPath. It then copies the input file to output file and deletes the input file after the copy is complete.
        /// </summary>
        static async Task<MessageResponse> FileCopy(Message message, object userContext)
        {
            int counterValue = Interlocked.Increment(ref counter);

            try
            {
                byte[] messageBytes = message.GetBytes();
                string messageString = Encoding.UTF8.GetString(messageBytes);
                Console.WriteLine($"Received message: {counterValue}, Body: [{messageString}]");

                if (!string.IsNullOrEmpty(messageString))
                {
                    var fileEvent = JsonConvert.DeserializeObject<FileEvent>(messageString);

                    string relativeFileName = fileEvent.ShareRelativeFilePath.Replace("\\", "/");
                    string inputFilePath = InputFolderPath + relativeFileName;
                    string outputFilePath = OutputFolderPath + relativeFileName;

                    if (File.Exists(inputFilePath))                
                    {
                        Console.WriteLine($"Moving input file: {inputFilePath} to output file: {outputFilePath}");
                        var outputDir = Path.GetDirectoryName(outputFilePath);
                        if (!Directory.Exists(outputDir))
                        {
                            Directory.CreateDirectory(outputDir);
                        }

                        File.Copy(inputFilePath, outputFilePath, true);
                        Console.WriteLine($"Copied input file: {inputFilePath} to output file: {outputFilePath}");
                        File.Delete(inputFilePath);
                        Console.WriteLine($"Deleted input file: {inputFilePath}");
                    } 
                    else
                    {
                        Console.WriteLine($"Skipping this event as input file doesn't exist: {inputFilePath}");   
                    }
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine("Caught exception: {0}", ex.Message);
                Console.WriteLine(ex.StackTrace);
            }

            Console.WriteLine($"Processed event.");
            return MessageResponse.Completed;
        }
    ```

7. Guarde este ficheiro.
8. Você também pode [baixar um exemplo de código existente](https://azure.microsoft.com/resources/samples/data-box-edge-csharp-modules/?cdn=disable) para este projeto. Em seguida, você pode validar o arquivo que você salvou no arquivo **Program.cs** neste exemplo.

## <a name="build-your-iot-edge-solution"></a>Criar a sua solução do IoT Edge

Na seção anterior, você criou uma solução de IoT Edge e adicionou o código ao FileCopyModule para copiar arquivos do compartilhamento local para o compartilhamento de nuvem. Agora, tem de criar a solução como uma imagem de contentor e enviá-la para o registo de contentor.

1. Em VSCode, vá para Terminal > novo terminal para abrir um novo terminal Visual Studio Code integrado.
2. Entre no Docker digitando o seguinte comando no terminal integrado.

    `docker login <ACR login server> -u <ACR username>`

    Use o servidor de logon e o nome de usuário que você copiou do registro de contêiner.

    ![Criar e enviar por push IoT Edge solução](./media/data-box-edge-create-iot-edge-module/build-iot-edge-solution-1.png)

2. Quando a senha for solicitada, forneça a senha. Você também pode recuperar os valores para servidor de logon, nome de usuário e senha das **chaves de acesso** no registro de contêiner no portal do Azure.
 
3. Depois que as credenciais forem fornecidas, você poderá enviar por push a imagem do módulo para o registro de contêiner do Azure. No VS Code Explorer, clique com o botão direito do mouse no arquivo **Module. JSON** e selecione **criar e enviar por push IOT Edge solução**.

    ![Criar e enviar por push IoT Edge solução](./media/data-box-edge-create-iot-edge-module/build-iot-edge-solution-2.png)
 
    Quando você informa Visual Studio Code para criar sua solução, ela executa dois comandos no terminal integrado: Docker Build e Docker Push. Estes dois comandos criam o código, colocam o CSharpModule.dll em contentores e enviam-no para o registo de contentor que especificou quando inicializou a solução.

    Você será solicitado a escolher a plataforma do módulo. Selecione *AMD64* correspondente ao Linux.

    ![Selecionar plataforma](./media/data-box-edge-create-iot-edge-module/select-platform.png)

    > [!IMPORTANT] 
    > Há suporte apenas para os módulos do Linux.

    Você pode ver o seguinte aviso que pode ignorar:

    *Program. cs (77, 44): aviso CS1998: Esse método assíncrono não tem operadores ' Await ' e será executado de forma síncrona. Considere o uso do operador ' Await ' para aguardar chamadas de API que não sejam de bloqueio ou ' aguardar tarefa. executar (...) ' para fazer o trabalho associado à CPU em um thread em segundo plano.*

4. Pode ver o endereço da imagem de contentor completo com a etiqueta no terminal integrado do VS Code. O endereço da imagem é criado com base nas informações contidas no arquivo module. JSON com `<repository>:<version>-<platform>`o formato. Para este artigo, deve ser semelhante `mycontreg2.azurecr.io/filecopymodule:0.0.1-amd64`a.

## <a name="next-steps"></a>Passos seguintes

Para implantar e executar esse módulo no Data Box Edge, consulte as etapas em [Adicionar um módulo](data-box-edge-deploy-configure-compute.md#add-a-module).
