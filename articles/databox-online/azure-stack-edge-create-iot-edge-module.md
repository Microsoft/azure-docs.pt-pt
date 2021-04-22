---
title: Módulo IoT Edge C# para Azure Stack Edge Pro | Microsoft Docs
description: Saiba como desenvolver um módulo C# IoT Edge que pode ser implantado no seu Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/06/2019
ms.author: alkohli
ms.custom: devx-track-csharp
ms.openlocfilehash: 4519bc187c4ec53294e5eef15c4ad1954b691224
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107870846"
---
# <a name="develop-a-c-iot-edge-module-to-move-files-with-azure-stack-edge-pro"></a>Desenvolver um módulo C# IoT Edge para mover ficheiros com Azure Stack Edge Pro

Este artigo percorre a forma de criar um módulo IoT Edge para implantação com o seu dispositivo Azure Stack Edge Pro. Azure Stack Edge Pro é uma solução de armazenamento que lhe permite processar dados e enviá-lo para o Azure.

Pode utilizar módulos Azure IoT Edge com o seu Azure Stack Edge Pro para transformar os dados à medida que se deslocam para Azure. O módulo utilizado neste artigo implementa a lógica de copiar um ficheiro de uma partilha local para uma partilha de nuvem no seu dispositivo Azure Stack Edge Pro.

Neste artigo, vai aprender a:

> [!div class="checklist"]
>
> * Crie um registo de contentores para armazenar e gerir os seus módulos (imagens Docker).
> * Crie um módulo IoT Edge para implementar no seu dispositivo Azure Stack Edge Pro. 


## <a name="about-the-iot-edge-module"></a>Sobre o módulo IoT Edge

O seu dispositivo Azure Stack Edge Pro pode implantar e executar módulos IoT Edge. Os módulos edge são essencialmente recipientes Docker que executam uma tarefa específica, como ingerir uma mensagem a partir de um dispositivo, transformar uma mensagem ou enviar uma mensagem para um Hub IoT. Neste artigo, irá criar um módulo que copia ficheiros de uma partilha local para uma partilha na nuvem no seu dispositivo Azure Stack Edge Pro.

1. Os ficheiros são escritos para a parte local do seu dispositivo Azure Stack Edge Pro.
2. O gerador de eventos de ficheiros cria um evento de ficheiro para cada ficheiro escrito para a parte local. Os eventos de ficheiro também são gerados quando um ficheiro é modificado. Os eventos de ficheiro são então enviados para ioT Edge Hub (em tempo de execução IoT Edge).
3. O módulo personalizado IoT Edge processa o evento de ficheiro para criar um objeto de evento de ficheiro que também contém um caminho relativo para o ficheiro. O módulo gera um caminho absoluto usando o caminho relativo do ficheiro e copia o ficheiro da partilha local para a partilha de nuvem. Em seguida, o módulo elimina o ficheiro da parte local.

![Como o módulo Azure IoT Edge funciona no Azure Stack Edge Pro](./media/azure-stack-edge-create-iot-edge-module/how-module-works-1.png)

Uma vez que o ficheiro esteja na partilha de nuvem, é automaticamente enviado para a sua conta de Armazenamento Azure.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que tem:

- Um dispositivo Azure Stack Edge Pro que está em execução.

    - O dispositivo também tem um recurso IoT Hub associado.
    - O dispositivo tem o papel de computação Edge configurado.
    Para mais informações, aceda ao [computo Configure](azure-stack-edge-deploy-configure-compute.md#configure-compute) para o seu Azure Stack Edge Pro.

- Os seguintes recursos de desenvolvimento:

    - [Visual Studio Code](https://code.visualstudio.com/).
    - [Extensão C# para Visual Studio Code (com tecnologia da OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).
    - [Extensão Azure IoT Edge para Código de Estúdio Visual](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).
    - [SDK de .NET Core 2.1](https://dotnet.microsoft.com/download/dotnet/2.1).
    - [Docker CE.](https://store.docker.com/editions/community/docker-ce-desktop-windows) Poderá ter de criar uma conta para descarregar e instalar o software.

## <a name="create-a-container-registry"></a>Criar um registo de contentor

Um registo de contentor do Azure é um registo do Docker privado no Azure, onde pode armazenar e gerir as imagens privadas de contentor do Docker. Os dois populares serviços de registo docker disponíveis na nuvem são O Registo de Contentores Azure e Docker Hub. Este artigo utiliza o Registo de Contentores.

1. Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. **Selecione Criar um recurso > contentores > registo de contentores**. Clique em **Criar**.
3. Fornecer:

   1. Um **nome único de registo** dentro de Azure que contém 5 a 50 caracteres alfanuméricos.
   2. Escolha uma **subscrição.**
   3. Criar novo ou escolher um grupo de **Recursos** existente.
   4. Selecione uma **localização**. Recomendamos que esta localização seja a mesma que está associada ao recurso Azure Stack Edge.
   5. Mude **Utilizador administrador** para **Ativar**.
   6. Desa estação o SKU para **Basic**.

      ![Criar registo de contentor](./media/azure-stack-edge-create-iot-edge-module/create-container-registry-1.png)
 
4. Selecione **Criar**.
5. Depois de o registo de contentores ser criado, navegue para o mesmo e selecione **Chaves de acesso**.

    ![Obter chaves de acesso](./media/azure-stack-edge-create-iot-edge-module/get-access-keys-1.png)
 
6. Copie os valores de **Servidor de início de sessão**, **Nome de utilizador** e **Palavra-passe**. Utilize estes valores mais tarde para publicar a imagem do Docker no seu registo e para adicionar as credenciais de registo ao tempo de execução do Azure IoT Edge.


## <a name="create-an-iot-edge-module-project"></a>Criar um projeto do módulo do IoT Edge

Os passos seguintes criam um projeto de módulo IoT Edge baseado no .NET Core 2.1 SDK. O projeto utiliza o Código do Estúdio Visual e a extensão Azure IoT Edge.

### <a name="create-a-new-solution"></a>Criar uma nova solução

Crie um modelo de solução C# que pode personalizar com o seu próprio código.

1. No Código do Estúdio Visual, selecione **Ver > Paleta de Comando** para abrir a paleta de comando vs Código.
2. Na paleta de comandos, introduza e execute o comando **Azure: Iniciar Sessão** e siga as instruções para iniciar sessão na sua conta do Azure. Se já iniciou sessão, pode ignorar este passo.
3. Na paleta de comandos, introduza e execute o comando **Azure IoT Edge: Nova solução do IoT Edge**. Na paleta de comandos, indique as seguintes informações para criar a sua solução:

    1. Selecione a pasta onde quer criar a solução.
    2. Indique um nome para a sua solução ou aceite a predefinição **EdgeSolution**.
    
        ![Criar nova solução 1](./media/azure-stack-edge-create-iot-edge-module/create-new-solution-1.png)

    3. Escolha **o módulo C#** como modelo do módulo.
    4. Substitua o nome do módulo predefinido pelo nome que pretende atribuir, neste caso, é **FileCopyModule**.
    
        ![Criar nova solução 2](./media/azure-stack-edge-create-iot-edge-module/create-new-solution-2.png)

    5. Especifique o registo do contentor que criou na secção anterior como o repositório de imagem para o seu primeiro módulo. Substitua **localhost:5000** pelo valor do servidor de início de sessão que copiou.

        A corda final `<Login server name>/<Module name>` parece. Neste exemplo, a corda é: `mycontreg2.azurecr.io/filecopymodule` .

        ![Criar nova solução 3](./media/azure-stack-edge-create-iot-edge-module/create-new-solution-3.png)

4. Vá para **arquivar > pasta aberta**.

    ![Criar nova solução 4](./media/azure-stack-edge-create-iot-edge-module/create-new-solution-4.png)

5. Navegue e aponte para a pasta **EdgeSolution** que criou anteriormente. A janela vs Code carrega o seu espaço de trabalho de solução IoT Edge com os seus cinco componentes de nível superior. Não editará a pasta **.vscode,** **ficheiro .gitignore,** **ficheiro .env** e a **deployment.template.js** neste artigo.
    
    O único componente que modifica é a pasta dos módulos. Esta pasta tem o código C# para o seu módulo e ficheiros Docker para construir o seu módulo como imagem de recipiente.

    ![Criar nova solução 5](./media/azure-stack-edge-create-iot-edge-module/create-new-solution-5.png)

### <a name="update-the-module-with-custom-code"></a>Atualizar o módulo com o código personalizado

1. No explorador de código VS, **abram os módulos > Programa de > FileCopyModule.cs**.
2. No topo do espaço de **nome FileCopyModule**, adicione o seguinte usando declarações para tipos que são usados mais tarde. **Microsoft.Azure.Devices.Client.Transport.Mqtt** é um protocolo para enviar mensagens para ioT Edge Hub.

    ```
    namespace FileCopyModule
    {
        using Microsoft.Azure.Devices.Client.Transport.Mqtt;
        using Newtonsoft.Json;
    ```
3. Adicione a variável **InputFolderPath** e **OutputFolderPath** à classe Programa.

    ```
    class Program
        {
            static int counter;
            private const string InputFolderPath = "/home/input";
            private const string OutputFolderPath = "/home/output";
    ```

4. Imediatamente após o passo anterior, adicione a classe **FileEvent** para definir o corpo da mensagem.

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

5. No **método Init,** o código cria e configura um **objeto MóduloClient.** Este objeto permite que o módulo se conecte ao tempo de funcionamento do Azure IoT Edge local utilizando o protocolo MQTT para enviar e receber mensagens. A cadeia de ligação utilizada no método Init é dada ao módulo através do runtime do IoT Edge. O código regista uma chamada FileCopy para receber mensagens de um hub IoT Edge através do ponto final **de entrada1.** Substitua o **método Init** pelo seguinte código.

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

6. Remova o código para **o método PipeMessage** e, no seu lugar, insira o código para **FileCopy**.

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
8. Também pode [descarregar uma amostra de código existente](https://azure.microsoft.com/resources/samples/data-box-edge-csharp-modules/?cdn=disable) para este projeto. Pode então validar o ficheiro que guardou contra o ficheiro **.cs programa** nesta amostra.

## <a name="build-your-iot-edge-solution"></a>Criar a sua solução do IoT Edge

Na secção anterior, criou uma solução IoT Edge e adicionou código ao FileCopyModule para copiar ficheiros da partilha local para a partilha na nuvem. Agora, tem de criar a solução como uma imagem de contentor e enviá-la para o registo de contentor.

1. No VSCode, vá ao Terminal > Novo Terminal para abrir um novo terminal integrado visual Studio Code.
2. Inscreva-se no Docker introduzindo o seguinte comando no terminal integrado.

    `docker login <ACR login server> -u <ACR username>`

    Utilize o servidor de login e o nome de utilizador que copiou do registo do seu contentor.

    ![Construa e empurre a solução IoT Edge](./media/azure-stack-edge-create-iot-edge-module/build-iot-edge-solution-1.png)

2. Quando solicitado para obter a senha, forneça a senha. Também pode recuperar os valores do servidor de login, nome de utilizador e palavra-passe a partir das Chaves de **Acesso** no registo do seu contentor no portal Azure.
 
3. Uma vez fornecidas as credenciais, pode empurrar a imagem do módulo para o registo do seu contentor Azure. No VS Code Explorer, clique commodule.jsà direita **no** ficheiro e selecione **a solução Build and Push IoT Edge**.

    ![Construir e empurrar a solução IoT Edge 2](./media/azure-stack-edge-create-iot-edge-module/build-iot-edge-solution-2.png)
 
    Quando diz ao Visual Studio Code para construir a sua solução, executa dois comandos no terminal integrado: estivador e estivador. Estes dois comandos criam o código, colocam o CSharpModule.dll em contentores e enviam-no para o registo de contentor que especificou quando inicializou a solução.

    Ser-lhe-á solicitado que escolha a plataforma do módulo. Selecione *amd64* correspondente ao Linux.

    ![Selecione plataforma](./media/azure-stack-edge-create-iot-edge-module/select-platform.png)

    > [!IMPORTANT] 
    > Apenas os módulos Linux são suportados.

    Pode ver o seguinte aviso de que pode ignorar:

    *Programa.cs (77,44): aviso CS1998: Este método assíclo carece de operadores de "espera" e funcionará de forma sincronizada. Considere usar o operador "aguardar" para aguardar chamadas de API não bloqueando, ou "aguardar Tarefa.Run(...)" para fazer trabalhos ligados ao CPU numa linha de fundo.*

4. Pode ver o endereço da imagem de contentor completo com a etiqueta no terminal integrado do VS Code. O endereço de imagem é construído a partir de informações que estão no module.jsem arquivo com o formato `<repository>:<version>-<platform>` . Para este artigo, deve `mycontreg2.azurecr.io/filecopymodule:0.0.1-amd64` parecer.

## <a name="next-steps"></a>Passos seguintes

Para implementar e executar este módulo no Azure Stack Edge Pro, consulte os passos em [Adicionar um módulo](azure-stack-edge-deploy-configure-compute.md#add-a-module).
