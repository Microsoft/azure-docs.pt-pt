---
title: Módulo C# IoT Edge para Azure Data Box Edge [ Microsoft Docs
description: Aprenda a desenvolver um módulo C# IoT Edge que pode ser implantado na sua Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/06/2019
ms.author: alkohli
ms.openlocfilehash: 3aa1190fb713c2fbdedcb1ce84a65d4263693827
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78942554"
---
# <a name="develop-a-c-iot-edge-module-to-move-files-on-data-box-edge"></a>Desenvolva um módulo C# IoT Edge para mover ficheiros no Data Box Edge

Este artigo intervém sobre como criar um módulo IoT Edge para implementação com o seu dispositivo Data Box Edge. O Azure Data Box Edge é uma solução de armazenamento que permite processar dados e enviá-los através de uma rede para o Azure.

Pode utilizar módulos Azure IoT Edge com o seu Data Box Edge para transformar os dados à medida que se desloca para O Azure. O módulo utilizado neste artigo implementa a lógica de copiar um ficheiro de uma parte local para uma partilha de nuvem no seu dispositivo Data Box Edge.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Crie um registo de contentores para armazenar e gerir os seus módulos (imagens Does).
> * Crie um módulo IoT Edge para implementar no seu dispositivo Data Box Edge.


## <a name="about-the-iot-edge-module"></a>Sobre o módulo IoT Edge

O seu dispositivo Data Box Edge pode implementar e executar módulos IoT Edge. Os módulos de borda são essencialmente recipientes Docker que executam uma tarefa específica, como ingerir uma mensagem a partir de um dispositivo, transformar uma mensagem ou enviar uma mensagem para um Hub IoT. Neste artigo, irá criar um módulo que copia ficheiros de uma parte local para uma partilha na nuvem no seu dispositivo Data Box Edge.

1. Os ficheiros estão escritos na parte local do seu dispositivo Data Box Edge.
2. O gerador de eventos de arquivo cria um evento de arquivo para cada ficheiro escrito à parte local. Os eventos de ficheiros também são gerados quando um ficheiro é modificado. Os eventos de ficheiro são então enviados para ioT Edge Hub (em tempo de execução IoT Edge).
3. O módulo personalizado IoT Edge processa o evento de arquivo para criar um objeto de evento de arquivo que também contém um caminho relativo para o ficheiro. O módulo gera um caminho absoluto usando o caminho relativo do ficheiro e copia o ficheiro da parte local para a partilha da nuvem. Em seguida, o módulo elimina o ficheiro da parte local.

![Como funciona o módulo Azure IoT Edge no Data Box Edge](./media/data-box-edge-create-iot-edge-module/how-module-works-1.png)

Uma vez que o ficheiro esteja na partilha da nuvem, é automaticamente enviado para a sua conta de Armazenamento Azure.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que tem:

- Um dispositivo Data Box Edge que está em execução.

    - O dispositivo também tem um recurso IoT Hub associado.
    - O dispositivo tem a função de computação Edge configurada.
    Para mais informações, vá à [Configuração compute](data-box-edge-deploy-configure-compute.md#configure-compute) para o seu Data Box Edge.

- Os seguintes recursos de desenvolvimento:

    - [Código de estúdio visual.](https://code.visualstudio.com/)
    - [Extensão C# para Visual Studio Code (com tecnologia da OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).
    - [Extensão Azure IoT Edge para Código de Estúdio Visual](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).
    - [SDK de .NET Core 2.1](https://www.microsoft.com/net/download).
    - [Docker CE.](https://store.docker.com/editions/community/docker-ce-desktop-windows) Poderá ter de criar uma conta para descarregar e instalar o software.

## <a name="create-a-container-registry"></a>Criar um registo de contentores

Um registo de contentor do Azure é um registo do Docker privado no Azure, onde pode armazenar e gerir as imagens privadas de contentor do Docker. Os dois populares serviços de registo docker disponíveis na nuvem são o Registo de Contentores Azure e Docker Hub. Este artigo utiliza o Registo de Contentores.

1. Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. Selecione **Criar um recurso > contentores > registo de contentores**. Clique em **Criar**.
3. Fornecer:

   1. Um **nome único de registo** dentro de Azure que contém 5 a 50 caracteres alfanuméricos.
   2. Escolha uma **Subscrição**.
   3. Crie novos ou escolha um grupo de **Recursos**existentes.
   4. Selecione um **Local**. Recomendamos que este local seja o mesmo que está associado ao recurso Data Box Edge.
   5. Mude **Utilizador administrador** para **Ativar**.
   6. Desloque o SKU para **Basic**.

      ![Criar registo de contentor](./media/data-box-edge-create-iot-edge-module/create-container-registry-1.png)
 
4. Selecione **Criar**.
5. Depois de o registo de contentores ser criado, navegue para o mesmo e selecione **Chaves de acesso**.

    ![Obter chaves de acesso](./media/data-box-edge-create-iot-edge-module/get-access-keys-1.png)
 
6. Copie os valores de **Servidor de início de sessão**, **Nome de utilizador** e **Palavra-passe**. Usa estes valores mais tarde para publicar a imagem do Docker no seu registo e para adicionar as credenciais de registo ao tempo de execução do Azure IoT Edge.


## <a name="create-an-iot-edge-module-project"></a>Criar um projeto do módulo do IoT Edge

Os passos seguintes criam um projeto de módulo IoT Edge baseado no .NET Core 2.1 SDK. O projeto utiliza o Visual Studio Code e a extensão Azure IoT Edge.

### <a name="create-a-new-solution"></a>Criar uma nova solução

Crie um modelo de solução C# que pode personalizar com o seu próprio código.

1. No Código do Estúdio Visual, selecione **Ver > Paleta de Comando** para abrir a paleta de comando vs Code.
2. Na paleta de comandos, introduza e execute o comando **Azure: Iniciar Sessão** e siga as instruções para iniciar sessão na sua conta do Azure. Se já iniciou sessão, pode ignorar este passo.
3. Na paleta de comandos, introduza e execute o comando **Azure IoT Edge: Nova solução do IoT Edge**. Na paleta de comandos, indique as seguintes informações para criar a sua solução:

    1. Selecione a pasta onde quer criar a solução.
    2. Indique um nome para a sua solução ou aceite a predefinição **EdgeSolution**.
    
        ![Criar nova solução 1](./media/data-box-edge-create-iot-edge-module/create-new-solution-1.png)

    3. Escolha o **Módulo C#** como modelo de módulo.
    4. Substitua o nome do módulo predefinido pelo nome que pretende atribuir, neste caso, é **FileCopyModule**.
    
        ![Criar nova solução 2](./media/data-box-edge-create-iot-edge-module/create-new-solution-2.png)

    5. Especifique o registo de contentores que criou na secção anterior como o repositório de imagem para o seu primeiro módulo. Substitua **localhost:5000** pelo valor do servidor de início de sessão que copiou.

        A corda final `<Login server name>/<Module name>`parece. Neste exemplo, a corda `mycontreg2.azurecr.io/filecopymodule`é: .

        ![Criar nova solução 3](./media/data-box-edge-create-iot-edge-module/create-new-solution-3.png)

4. Vá para **file> open folder**.

    ![Criar nova solução 4](./media/data-box-edge-create-iot-edge-module/create-new-solution-4.png)

5. Navegue e aponte para a pasta **EdgeSolution** que criou anteriormente. A janela VS Code carrega o espaço de trabalho da solução IoT Edge com os seus cinco componentes de nível superior. Não editará a pasta **.vscode,** **ficheiro .gitignore,** ficheiro **.env** e o **deployment.template.json** neste artigo.
    
    O único componente que modifica é a pasta dos módulos. Esta pasta tem o código C# para o seu módulo e ficheiros Docker para construir o seu módulo como imagem de recipiente.

    ![Criar nova solução 5](./media/data-box-edge-create-iot-edge-module/create-new-solution-5.png)

### <a name="update-the-module-with-custom-code"></a>Atualizar o módulo com o código personalizado

1. No explorador de código VS, abra **os módulos > FileCopyModule > Program.cs**.
2. Na parte superior do espaço de **nome FileCopyModule,** adicione as seguintes declarações utilizando os tipos que são utilizados posteriormente. **Microsoft.Azure.Devices.Client.Transport.Mqtt** é um protocolo para enviar mensagens para o IoT Edge Hub.

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

5. No **método Init,** o código cria e configura um objeto **ModuleClient.** Este objeto permite que o módulo se coneca ao tempo de funcionação do Azure IoT Edge local utilizando o protocolo MQTT para enviar e receber mensagens. A cadeia de ligação utilizada no método Init é dada ao módulo através do runtime do IoT Edge. O código regista uma chamada FileCopy para receber mensagens de um hub IoT Edge através do ponto final de **entrada1.** Substitua o **método Init** pelo seguinte código.

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

6. Remova o código para o **método PipeMessage** e, no seu lugar, insira o código para **FileCopy**.

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
8. Também pode [descarregar uma amostra](https://azure.microsoft.com/resources/samples/data-box-edge-csharp-modules/?cdn=disable) de código existente para este projeto. Pode então validar o ficheiro que guardou contra o ficheiro **program.cs** nesta amostra.

## <a name="build-your-iot-edge-solution"></a>Criar a sua solução do IoT Edge

Na secção anterior, criou uma solução IoT Edge e adicionou código ao FileCopyModule para copiar ficheiros de partilha local para a partilha na nuvem. Agora, tem de criar a solução como uma imagem de contentor e enviá-la para o registo de contentor.

1. No VSCode, vá ao Terminal > Novo Terminal para abrir um novo terminal integrado do Código do Estúdio Visual.
2. Inscreva-se no Docker introduzindo o seguinte comando no terminal integrado.

    `docker login <ACR login server> -u <ACR username>`

    Utilize o servidor de login e o nome de utilizador que copiou do registo do seu contentor.

    ![Construir e empurrar a solução IoT Edge](./media/data-box-edge-create-iot-edge-module/build-iot-edge-solution-1.png)

2. Quando solicitado para obter senha, forneça a senha. Também pode recuperar os valores para o servidor de login, nome de utilizador e palavra-passe das **Teclas** de Acesso no seu registo de contentores no portal Azure.
 
3. Uma vez fornecidas as credenciais, pode empurrar a imagem do módulo para o registo do seu contentor Azure. No Vs Code Explorer, clique no ficheiro **module.json** e selecione **build and Push IoT Edge solution**.

    ![Construir e empurrar a solução IoT Edge](./media/data-box-edge-create-iot-edge-module/build-iot-edge-solution-2.png)
 
    Quando diz ao Visual Studio Code para construir a sua solução, executa dois comandos no terminal integrado: construção de estivadores e pressão de estivador. Estes dois comandos criam o código, colocam o CSharpModule.dll em contentores e enviam-no para o registo de contentor que especificou quando inicializou a solução.

    Será solicitado a escolher a plataforma do módulo. Selecione *amd64* correspondente ao Linux.

    ![Selecione plataforma](./media/data-box-edge-create-iot-edge-module/select-platform.png)

    > [!IMPORTANT] 
    > Apenas os módulos Linux são suportados.

    Pode ver o seguinte aviso que pode ignorar:

    *Program.cs (77,44): aviso CS1998: Este método de asincronização carece de operadores 'aguarda' e funcionará sincronizadamente. Considere utilizar o operador 'aguarda' para aguardar chamadas API não bloqueadas, ou "aguardar Task.Run(...)" para fazer trabalhos ligados ao CPU numa linha de fundo.*

4. Pode ver o endereço da imagem de contentor completo com a etiqueta no terminal integrado do VS Code. O endereço de imagem é construído a partir de informações `<repository>:<version>-<platform>`que estão no ficheiro módulo.json com o formato . Para este artigo, deve `mycontreg2.azurecr.io/filecopymodule:0.0.1-amd64`parecer.

## <a name="next-steps"></a>Passos seguintes

Para implantar e executar este módulo no Data Box Edge, consulte os passos em [Adicionar um módulo](data-box-edge-deploy-configure-compute.md#add-a-module).
