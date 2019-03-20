---
title: Módulo de c# IoT Edge para o Edge de caixa de dados do Azure | Documentos da Microsoft
description: Saiba como desenvolver um módulo de c# IoT Edge que pode ser implementado no seu limite de caixa de dados.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 01/31/2019
ms.author: alkohli
ms.openlocfilehash: 81407a298ccfe1b9884fc5d5b815ac8c18ffee6a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58094682"
---
# <a name="develop-a-c-iot-edge-module-to-move-files-on-data-box-edge-preview"></a>Desenvolver um módulo de c# IoT Edge para mover ficheiros do Edge de caixa de dados (pré-visualização)

Este artigo descreve como criar um módulo do IoT Edge para a implementação com o seu dispositivo Edge de caixa de dados. O Azure Data Box Edge é uma solução de armazenamento que permite processar dados e enviá-los através de uma rede para o Azure.

Pode utilizar módulos do Azure IoT Edge com o seu limite de caixa de dados para transformar os dados conforme fosse movida para o Azure. O módulo usado neste artigo, implementa a lógica para copiar um ficheiro a partir de uma partilha local para uma partilha de cloud no seu dispositivo do Edge de caixa de dados.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Crie um registo de contentores para armazenar e gerir os seus módulos de hardware (imagens do Docker).
> * Crie um módulo do IoT Edge para implementar no seu dispositivo Edge de caixa de dados.

> [!IMPORTANT]
> O Data Box Edge está em pré-visualização. Reveja os [Termos de serviço do Azure para pré-visualização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) antes de encomendar e implementar esta solução. 

## <a name="about-the-iot-edge-module"></a>Sobre o módulo do IoT Edge

O dispositivo do Edge de caixa de dados pode implementar e executar os módulos do IoT Edge. Módulos do Edge são, essencialmente, os contentores de Docker que executam uma tarefa específica, tal como uma mensagem a partir de um dispositivo de ingestão, transformar uma mensagem ou enviam uma mensagem para um IoT Hub. Neste artigo, irá criar um módulo que copia ficheiros a partir de uma partilha local para uma partilha de cloud no seu dispositivo Edge de caixa de dados.

1. Ficheiros são escritos para a partilha local no seu dispositivo do Edge de caixa de dados.
2. O gerador de eventos do ficheiro cria um evento de ficheiro para cada ficheiro escrito para a partilha local. Os eventos de ficheiro, em seguida, são enviados para o Hub do IoT Edge (no runtime do IoT Edge).

   > [!IMPORTANT]
   > Os eventos de ficheiro são gerados apenas para os ficheiros recentemente criados. Modificação de arquivos existentes não gera quaisquer eventos de ficheiro.

3. O módulo personalizado do IoT Edge processa o evento de arquivo para criar um objeto de evento de ficheiro que também contém um caminho relativo para o ficheiro. O módulo gera um caminho absoluto através do caminho de ficheiro relativo e copia o ficheiro da partilha de local para a partilha de cloud. O módulo, em seguida, elimina o ficheiro da partilha local.

![Como funciona o módulo do Azure IoT Edge no limite da caixa de dados](./media/data-box-edge-create-iot-edge-module/how-module-works.png)

Depois do ficheiro está no compartilhamento de cloud, ele automaticamente é carregado para a sua conta de armazenamento do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que tem:

- Um dispositivo de limite de caixa de dados que está a executar.

    - O dispositivo também tem um recurso do IoT Hub associado. Para obter mais informações, aceda a [criar um recurso do IoT Hub](data-box-edge-deploy-configure-compute.md#create-an-iot-hub-resource) para dados de caixa de extremidade.
    - O dispositivo tem o limite configurada de função de computação. Para obter mais informações, aceda a [configurar a função de computação](data-box-edge-deploy-configure-compute.md#set-up-compute-role) na extremidade da caixa de dados.

- Os seguintes recursos de desenvolvimento:

    - [Visual Studio Code](https://code.visualstudio.com/).
    - [Extensão C# para Visual Studio Code (com tecnologia da OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).
    - [Extensão Azure IoT Edge para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).
    - [SDK de .NET Core 2.1](https://www.microsoft.com/net/download).
    - [Docker CE](https://store.docker.com/editions/community/docker-ce-desktop-windows). Poderá ter de criar uma conta para transferir e instalar o software.

## <a name="create-a-container-registry"></a>Criar um registo de contentores

Um registo de contentor do Azure é um registo do Docker privado no Azure, onde pode armazenar e gerir as imagens privadas de contentor do Docker. Os dois Docker registry serviços populares disponíveis na cloud são o Azure Container Registry e o Docker Hub. Este artigo utiliza o registo de contentor.

1. Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. Selecione **criar um recurso > contentores > registo de contentor**. Clique em **Criar**.
3. Fornece o seguinte:

   1. Um exclusivo **nome do registo** no Azure que contém 5 e 50 carateres de alfanuméricos.
   2. Escolher uma **subscrição**.
   3. Criar um novo ou selecione um existente **grupo de recursos**.
   4. Selecione uma **Localização**. Recomendamos que esta localização ser a mesma, que é associado ao recurso de borda de caixa de dados.
   5. Mude **Utilizador administrador** para **Ativar**.
   6. Defina o SKU como **básica**.

      ![Criar registo de contentor](./media/data-box-edge-create-iot-edge-module/create-container-registry-1.png)
 
4. Selecione **Criar**.
5. Depois de o registo de contentores ser criado, navegue para o mesmo e selecione **Chaves de acesso**.

    ![Obter chaves de acesso](./media/data-box-edge-create-iot-edge-module/get-access-keys-1.png)
 
6. Copie os valores de **Servidor de início de sessão**, **Nome de utilizador** e **Palavra-passe**. Utilize estes valores mais tarde para publicar a imagem do Docker para o seu registo e para adicionar as credenciais de registo para o runtime do Azure IoT Edge.


## <a name="create-an-iot-edge-module-project"></a>Criar um projeto do módulo do IoT Edge

Os passos seguintes criam um projeto de módulo do IoT Edge baseado o SDK 2.1 do .NET Core. O projeto utiliza o Visual Studio Code e a extensão Azure IoT Edge.

### <a name="create-a-new-solution"></a>Criar uma nova solução

Crie um modelo de solução C# que pode personalizar com o seu próprio código.

1. No Visual Studio Code, selecione **vista > paleta de comandos** para abrir a paleta de comandos do VS Code.
2. Na paleta de comandos, introduza e execute o comando **Azure: Inicie sessão no** e siga as instruções para iniciar sessão na sua conta do Azure. Se já iniciou sessão, pode ignorar este passo.
3. Na paleta de comandos, introduza e execute o comando **Azure IoT Edge: Nova solução de IoT Edge**. Na paleta de comandos, indique as seguintes informações para criar a sua solução:

    1. Selecione a pasta onde quer criar a solução.
    2. Indique um nome para a sua solução ou aceite a predefinição **EdgeSolution**.
    
        ![Criar nova solução 1](./media/data-box-edge-create-iot-edge-module/create-new-solution-1.png)

    3. Selecione **Módulo C#** como modelo de módulo.
    4. Substitua o nome do módulo de padrão com o nome que pretende atribuir, neste caso, é **FileCopyModule**.
    
        ![Criar nova solução 2](./media/data-box-edge-create-iot-edge-module/create-new-solution-2.png)

    5. Especifique o registo de contentor que criou na secção anterior como repositório de imagens para o seu primeiro módulo. Substitua **localhost:5000** pelo valor do servidor de início de sessão que copiou.

        A cadeia final se parece com `<Login server name>/<Module name>`. Neste exemplo, a cadeia de caracteres é: `mycontreg2.azurecr.io/filecopymodule`.

        ![Criar nova solução 3](./media/data-box-edge-create-iot-edge-module/create-new-solution-3.png)

4. Aceda a **ficheiro > Abrir pasta**.

    ![Criar nova solução 4](./media/data-box-edge-create-iot-edge-module/create-new-solution-4.png)

5. Procurar e aponte para o **EdgeSolution** pasta que criou anteriormente. Janela do VS Code carrega sua área de trabalho de solução de IoT Edge com seus cinco componentes de nível superior. Não irá editar a **.vscode** pasta, **. gitignore** ficheiro, **. env** arquivo e a **deployment.template.json** neste artigo.
    
    O único componente que modificar é a pasta de módulos. Esta pasta tem o código c# para o seu módulo e ficheiros de Docker para criar o seu módulo como uma imagem de contentor.

    ![Criar nova solução 5](./media/data-box-edge-create-iot-edge-module/create-new-solution-5.png)

### <a name="update-the-module-with-custom-code"></a>Atualizar o módulo com o código personalizado

1. No Explorador do VS Code, abra **módulos > CSharpModule > Program.cs**.
2. Na parte superior a **FileCopyModule espaço de nomes**, adicione as seguintes instruções "using" para tipos que são utilizados mais tarde. **Microsoft.Azure.Devices.Client.Transport.Mqtt** é um protocolo para enviar mensagens para o Hub do IoT Edge.

    ```
    using Microsoft.Azure.Devices.Client.Transport.Mqtt;
    using Newtonsoft.Json;
    ```
3. Adicionar a **InputFolderPath** e **OutputFolderPath** variável à classe Program.

    ```
    class Program
        {
            static int counter;
            private const string InputFolderPath = "/home/LocalShare";
            private const string OutputFolderPath = "/home/CloudShare";
    ```

    > [!IMPORTANT]
    > Anote o `InputFolderPath` e o `OutputFolderPath`. Terá de fornecer estes caminhos, ao implementar este módulo.

4. Adicionar a **MessageBody** classe à classe Program. Estas classes definem o esquema esperado para o corpo das mensagens a receber.

    ```
    /// <summary>
    /// The MessageBody class defines the expected schema for the body of incoming messages. 
    /// </summary>
    private class FileEvent
    {
        public string ChangeType { get; set; }

        public string ShareRelativeFilePath { get; set; }

        public string ShareName { get; set; }
    }
    ```

5. No método **Init**, o código cria e configura um objeto **ModuleClient**. Este objeto permite que o módulo ligar para o tempo de execução do Azure IoT Edge local usando o protocolo MQTT para enviar e receber mensagens. A cadeia de ligação que é utilizada no método Init é fornecida para o módulo pelo runtime do IoT Edge. O código registra um retorno de chamada de filecopy sem êxito para receber mensagens a partir de um hub do IoT Edge através da **input1** ponto final.

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

6. Insira o código para **filecopy sem êxito**.

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

    }
    ```

7. Guarde este ficheiro.

## <a name="build-your-iot-edge-solution"></a>Criar a sua solução do IoT Edge

Na secção anterior, criou uma solução de IoT Edge e adicionado o código para o FileCopyModule para copiar ficheiros de partilha local para a partilha de cloud. Agora, tem de criar a solução como uma imagem de contentor e enviá-la para o registo de contentor.

1. Introduza o comando seguinte no terminal integrado do Visual Studio Code e inicie sessão no Docker.

    `docker login <ACR login server> -u <ACR username>`

    Utilize o servidor de início de sessão e o nome de utilizador que copiou do seu registo de contentor. 

    ![Criar e enviar por push a solução de IoT Edge](./media/data-box-edge-create-iot-edge-module/build-iot-edge-solution-1.png)

2. Quando lhe for pedido para a palavra-passe, forneça a palavra-passe. Também pode obter os valores para o servidor de início de sessão, nome de utilizador e palavra-passe a partir da **chaves de acesso** no seu registo de contentor no portal do Azure.
 
3. Depois das credenciais são fornecidas, pode enviar a sua imagem do módulo para o seu registo de contentor do Azure. No VS Code Explorer, clique com botão direito a **Module** do ficheiro e selecione **solução de compilação e de Push IoT Edge**.

    ![Criar e enviar por push a solução de IoT Edge](./media/data-box-edge-create-iot-edge-module/build-iot-edge-solution-2.png)
 
    Quando diz para Visual Studio Code para criar a sua solução, ele executa dois comandos no terminal integrado: compilação do docker e emissão de docker. Estes dois comandos criam o código, colocam o CSharpModule.dll em contentores e enviam-no para o registo de contentor que especificou quando inicializou a solução.

    Será solicitado a escolher a plataforma de módulo. Selecione *amd64* correspondente ao Linux.

    ![Selecionar plataforma](./media/data-box-edge-create-iot-edge-module/select-platform.png)

    > [!IMPORTANT] 
    > Só os módulos do Linux são suportados.

    Pode ver o seguinte aviso que pode ignorar:

    *Program.cs(77,44): warning CS1998: Este método assíncrono não tem operadores 'await' e será executado de forma síncrona. Considere utilizar o operador 'await' para o await sem bloqueio de chamadas de API ou "await Task.Run(...)" para fazer o trabalho vinculadas à CPU num thread em segundo plano.*

4. Pode ver o endereço da imagem de contentor completo com a etiqueta no terminal integrado do VS Code. O endereço de imagem é criado a partir de informações que estão no arquivo Module com o formato `<repository>:<version>-<platform>`. Neste artigo, deve ser semelhante `mycontreg2.azurecr.io/filecopymodule:0.0.1-amd64`.

## <a name="next-steps"></a>Passos Seguintes

Para implementar e executar este módulo do Edge de caixa de dados, veja os passos em [adicionar um módulo personalizado](data-box-edge-deploy-configure-compute.md#add-a-custom-module).
