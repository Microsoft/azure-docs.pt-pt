---
title: Tutorial criar o módulo de C personalizado - Azure IoT Edge | Documentos da Microsoft
description: Este tutorial mostra como criar um módulo do IoT Edge com código C e implementá-lo num dispositivo Edge
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 01/04/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: b09282d3e897018aa6d5b2561f08d8eee0757d7a
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57455433"
---
# <a name="tutorial-develop-a-c-iot-edge-module-and-deploy-to-your-simulated-device"></a>Tutorial: Desenvolver um módulo do IoT Edge do C e implementar no seu dispositivo simulado

Pode utilizar os módulos do IoT Edge para implementar código que aplica a sua lógica de negócio diretamente nos seus dispositivos IoT Edge. Este tutorial explica-lhe como criar e implementar um módulo do IoT Edge que filtra dados de sensores. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Utilizar o Visual Studio Code para criar um módulo do IoT Edge em C
> * Utilizar o Visual Studio Code e o Docker para criar uma imagem do Docker e publicá-la num registo de contentor
> * Implementar o módulo no seu dispositivo IoT Edge
> * Ver os dados gerados


O módulo do IoT Edge que criou neste tutorial filtra os dados de temperatura gerados pelo seu dispositivo. Envia apenas mensagens de origem caso a temperatura seja superior a um limiar especificado. Este tipo de análise no Edge é útil para reduzir a quantidade de dados comunicados e armazenados na cloud.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Pré-requisitos

Um dispositivo Azure IoT Edge:

* Pode seguir os passos no início rápido para [dispositivos Linux](quickstart-linux.md) ou [Windows](quickstart.md) para utilizar o seu computador de desenvolvimento ou uma máquina virtual como um dispositivo Edge. 
* Os módulos de C para o Azure IoT Edge não suportam contentores do Windows. Se o seu dispositivo IoT Edge é uma máquina Windows, certifique-se de que está configurado para utilizar contentores do Linux. Para obter informações sobre as diferenças de instalação entre contentores do Windows e Linux, consulte [instalar o runtime do IoT Edge no Windows](how-to-install-iot-edge-windows.md).

Recursos da cloud:

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) no escalão gratuito ou standard no Azure.

Recursos de desenvolvimento:

* [Visual Studio Code](https://code.visualstudio.com/).
* [Extensão C/C++](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools) para o Visual Studio Code.
* [Ferramentas do Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) para Visual Studio Code.
* [Docker CE](https://docs.docker.com/install/).

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
Os passos seguintes mostram o como criar um projeto de módulo do IoT Edge com base no .NET core 2.0 usando Visual Studio Code e as ferramentas de IoT do Azure.

### <a name="create-a-new-solution"></a>Criar uma nova solução

Crie um modelo de solução C que pode personalizar com o seu próprio código.

1. Selecione **Ver** > **Paleta de Comandos** para abrir a paleta de comandos do VS Code.

2. Na paleta de comandos, escreva e execute o comando **Azure: Inicie sessão no** e siga as instruções para iniciar sessão na sua conta do Azure. Se já iniciou sessão, pode ignorar este passo.

3. Na paleta de comandos, escreva e execute o comando **Azure IoT Edge: Nova solução de IoT Edge**. Siga as instruções na paleta de comandos para criar a sua solução.

   | Campo | Valor |
   | ----- | ----- |
   | Selecionar pasta | Escolha a localização no computador de desenvolvimento na qual o VS Code vai criar os ficheiros da solução. |
   | Indicar um nome para a solução | Introduza um nome descritivo para a sua solução ou aceite a predefinição **EdgeSolution**. |
   | Selecionar modelo de módulo | Escolher **C módulo**. |
   | Indicar um nome para o módulo | Dê o nome **CModule** ao módulo. |
   | Indicar o repositório de imagens do Docker para o módulo | Os repositórios de imagens incluem o nome do seu registo de contentor e o nome da sua imagem de contentor. A imagem de contentor é pré-preenchida do nome que indicou no último passo. Substitua **localhost:5000** pelo valor do servidor de início de sessão do registo de contentor do Azure Container Registry. Pode obter o servidor de início de sessão na página Overview (Descrição Geral) do registo de contentor no portal do Azure. <br><br> O repositório de imagem final se parece com \<nome do registo\>.azurecr.io/cmodule. |
 
   ![Fornecer repositório de imagens do Docker](./media/tutorial-c-module/repository.png)

A janela do VS Code carrega a área de trabalho da solução do IoT Edge. A área de trabalho da solução contém cinco componentes de nível superior. A pasta **modules** contém o código C para o seu módulo, bem como Dockerfiles para criar o seu módulo como uma imagem de contentor. O ficheiro **\.env** armazena as credenciais do registo de contentor. O ficheiro **deployment.template.json** contém as informações que o runtime do IoT Edge utiliza para implementar módulos num dispositivo. E **deployment.debug.template.json** contentores de ficheiros a versão de depuração de módulos. Não irá editar a pasta **\.vscode** ou o ficheiro **\.gitignore** neste tutorial.

Se não tiver especificado um registo de contentor ao criar a sua solução, mas aceitou o valor do localhost:5000 predefinido, não terá um ficheiro \.env.

<!--
   ![C solution workspace](./media/tutorial-c-module/workspace.png)
-->

### <a name="add-your-registry-credentials"></a>Adicionar as credenciais do registo

O ficheiro de ambiente armazena as credenciais do seu registo de contentor e partilha-as com o runtime do IoT Edge. O runtime precisa destas credenciais para solicitar as imagens privadas para o dispositivo IoT Edge.

1. No explorador do VS Code, abra o ficheiro .env.
2. Atualize os campos com os valores **nome de utilizador** e **palavra-passe** que copiou do seu Azure Container Registry.
3. Guarde este ficheiro.

### <a name="update-the-module-with-custom-code"></a>Atualizar o módulo com o código personalizado

Adicione o código ao módulo C que permite a leitura de dados do sensor, verifique se a temperatura comunicada da máquina ultrapassou um limiar seguro e transmita essas informações ao Hub IoT.

5. Os dados do sensor neste cenário estão no formato JSON. Para filtrar mensagens no formato JSON, importe uma biblioteca JSON para C. Este tutorial utiliza o Parson.

   1. Transfira o [repositório do Parson GitHub](https://github.com/kgabis/parson). Copie os ficheiros **parson.c** e **parson.h** para a pasta **CModule**.

   2. Abra **modules** > **CModule** > **CMakeLists.txt**. Na parte superior do ficheiro, importe os ficheiros do Parson como uma biblioteca chamada **my_parson**.

      ```
      add_library(my_parson
          parson.c
          parson.h
      )
      ```

   3. Adicione **my_parson** à lista de bibliotecas na função **target_link_libraries** de CMakeLists.txt.

   4. Guarde o ficheiro **CMakeLists.txt**.

   5. Abra **modules** > **CModule** > **main.c**. Na parte inferior da lista de incluem instruções, adicionar um novo modelo para incluir `parson.h` para suporte JSON:

      ```c
      #include "parson.h"
      ```

6. No ficheiro **main.c**, adicione uma variável global chamada `temperatureThreshold` depois da secção de inclusão. Esta variável define o valor que a temperatura medida deve exceder, para que os dados sejam enviados para o Hub IoT.

    ```c
    static double temperatureThreshold = 25;
    ```

7. Substitua toda a função `CreateMessageInstance` pelo código abaixo. Esta função atribui um contexto à chamada de retorno.

    ```c
    static MESSAGE_INSTANCE* CreateMessageInstance(IOTHUB_MESSAGE_HANDLE message)
    {
        MESSAGE_INSTANCE* messageInstance = (MESSAGE_INSTANCE*)malloc(sizeof(MESSAGE_INSTANCE));
        if (NULL == messageInstance)
        {
            printf("Failed allocating 'MESSAGE_INSTANCE' for pipelined message\r\n");
        }
        else
        {
            memset(messageInstance, 0, sizeof(*messageInstance));

            if ((messageInstance->messageHandle = IoTHubMessage_Clone(message)) == NULL)
            {
                free(messageInstance);
                messageInstance = NULL;
            }
            else
            {
                messageInstance->messageTrackingId = messagesReceivedByInput1Queue;
                MAP_HANDLE propMap = IoTHubMessage_Properties(messageInstance->messageHandle);
                if (Map_AddOrUpdate(propMap, "MessageType", "Alert") != MAP_OK)
                {
                    printf("ERROR: Map_AddOrUpdate Failed!\r\n");
                }
            }
        }

        return messageInstance;
    }
    ```

8. Substitua toda a função `InputQueue1Callback` pelo código abaixo. Esta função implementa o filtro de mensagens real.

    ```c
    static IOTHUBMESSAGE_DISPOSITION_RESULT InputQueue1Callback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
    {
        IOTHUBMESSAGE_DISPOSITION_RESULT result;
        IOTHUB_CLIENT_RESULT clientResult;
        IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle = (IOTHUB_MODULE_CLIENT_LL_HANDLE)userContextCallback;

        unsigned const char* messageBody;
        size_t contentSize;

        if (IoTHubMessage_GetByteArray(message, &messageBody, &contentSize) != IOTHUB_MESSAGE_OK)
        {
            messageBody = "<null>";
        }

        printf("Received Message [%zu]\r\n Data: [%s]\r\n",
                messagesReceivedByInput1Queue, messageBody);

        JSON_Value *root_value = json_parse_string(messageBody);
        JSON_Object *root_object = json_value_get_object(root_value);
        double temperature;
        if (json_object_dotget_value(root_object, "machine.temperature") != NULL && (temperature = json_object_dotget_number(root_object, "machine.temperature")) > temperatureThreshold)
        {
            printf("Machine temperature %f exceeds threshold %f\r\n", temperature, temperatureThreshold);
            // This message should be sent to next stop in the pipeline, namely "output1".  What happens at "outpu1" is determined
            // by the configuration of the Edge routing table setup.
            MESSAGE_INSTANCE *messageInstance = CreateMessageInstance(message);
            if (NULL == messageInstance)
            {
                result = IOTHUBMESSAGE_ABANDONED;
            }
            else
            {
                printf("Sending message (%zu) to the next stage in pipeline\n", messagesReceivedByInput1Queue);

                clientResult = IoTHubModuleClient_LL_SendEventToOutputAsync(iotHubModuleClientHandle, messageInstance->messageHandle, "output1", SendConfirmationCallback, (void *)messageInstance);
                if (clientResult != IOTHUB_CLIENT_OK)
                {
                    IoTHubMessage_Destroy(messageInstance->messageHandle);
                    free(messageInstance);
                    printf("IoTHubModuleClient_LL_SendEventToOutputAsync failed on sending msg#=%zu, err=%d\n", messagesReceivedByInput1Queue, clientResult);
                    result = IOTHUBMESSAGE_ABANDONED;
                }
                else
                {
                    result = IOTHUBMESSAGE_ACCEPTED;
                }
            }
        }
        else
        {
            printf("Not sending message (%zu) to the next stage in pipeline.\r\n", messagesReceivedByInput1Queue);
            result = IOTHUBMESSAGE_ACCEPTED;
        }

        messagesReceivedByInput1Queue++;
        return result;
    }
    ```

9. Adicione uma função `moduleTwinCallback`. Este método recebe atualizações nas propriedades pretendidas do módulo duplo e atualiza a variável **temperatureThreshold** para corresponder. Todos os módulos têm o seu próprio módulo duplo, que lhe permite configurar o código em execução no interior de um módulo diretamente a partir da cloud.

    ```c
    static void moduleTwinCallback(DEVICE_TWIN_UPDATE_STATE update_state, const unsigned char* payLoad, size_t size, void* userContextCallback)
    {
        printf("\r\nTwin callback called with (state=%s, size=%zu):\r\n%s\r\n",
            ENUM_TO_STRING(DEVICE_TWIN_UPDATE_STATE, update_state), size, payLoad);
        JSON_Value *root_value = json_parse_string(payLoad);
        JSON_Object *root_object = json_value_get_object(root_value);
        if (json_object_dotget_value(root_object, "desired.TemperatureThreshold") != NULL) {
            temperatureThreshold = json_object_dotget_number(root_object, "desired.TemperatureThreshold");
        }
        if (json_object_get_value(root_object, "TemperatureThreshold") != NULL) {
            temperatureThreshold = json_object_get_number(root_object, "TemperatureThreshold");
        }
    }
    ```

10. Substitua a função `SetupCallbacksForModule` pelo código abaixo.

    ```c
    static int SetupCallbacksForModule(IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle)
    {
        int ret;

        if (IoTHubModuleClient_LL_SetInputMessageCallback(iotHubModuleClientHandle, "input1", InputQueue1Callback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
        {
            printf("ERROR: IoTHubModuleClient_LL_SetInputMessageCallback(\"input1\")..........FAILED!\r\n");
            ret = __FAILURE__;
        }
        else if (IoTHubModuleClient_LL_SetModuleTwinCallback(iotHubModuleClientHandle, moduleTwinCallback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
        {
            printf("ERROR: IoTHubModuleClient_LL_SetModuleTwinCallback(default)..........FAILED!\r\n");
            ret = __FAILURE__;
        }
        else
        {
            ret = 0;
        }

        return ret;
    }
    ```

11. Guarde o ficheiro Main.

12. No explorador do VS Code, abra o ficheiro **deployment.template.json** na área de trabalho da solução do IoT Edge. Este ficheiro diz ao agente do IoT Edge para quais módulos para implementar, neste caso **tempSensor** e **CModule**e informa ao hub do IoT Edge como rotear mensagens entre eles. A extensão do Visual Studio Code povoa automaticamente a maior parte das informações que precisa no modelo de implementação, mas certifique-se de que tudo está correta para a sua solução: 

   1. A plataforma padrão do seu IoT Edge está definida como **amd64** no seu estado do VS Code barra, que significa que seu **CModule** está definido para a versão do Linux amd64 da imagem. Alterar a plataforma de predefinição na barra de status da **amd64** ao **arm32v7** se de que é arquitetura de seu dispositivo IoT Edge. 

      ![Plataforma de imagem do módulo de atualização](./media/tutorial-c-module/image-platform.png)

   2. Verifique se o modelo tem o nome do módulo correto e não o nome predefinido **SampleModule** que alterou quando criou a solução IoT Edge.

   3. O **registryCredentials** secção armazena as suas credenciais do registo de Docker, para que o agente do IoT Edge pode extrair a sua imagem do módulo. Os pares de nome de utilizador e palavra-passe reais são armazenados no ficheiro .env, ignorado pelo git. Se ainda não o fez, adicione as suas credenciais para o ficheiro. env.  

   4. Se quiser obter mais informações sobre manifestos de implantação, consulte [Saiba como implementar módulos e estabelecer as rotas no IoT Edge](module-composition.md).

13. Adicione o módulo duplo CModule ao manifesto da implementação. Insira o seguinte conteúdo JSON na parte inferior da secção `moduleContent`, após o módulo duplo `$edgeHub`:

    ```json
        "CModule": {
            "properties.desired":{
                "TemperatureThreshold":25
            }
        }
    ```

   ![Adicionar o CModule duplo ao modelo de implementação](./media/tutorial-c-module/module-twin.png)

14. Guarde o ficheiro **deployment.template.json**.

## <a name="build-and-push-your-solution"></a>Criar e emitir sua solução

Na secção anterior, criou uma solução do IoT Edge e adicionou código ao CModule que filtrará mensagens onde a temperatura comunicada da máquina está dentro do limiar aceitável. Agora, tem de criar a solução como uma imagem de contentor e enviá-la para o registo de contentor.

1. Abra o terminal integrado do VS Code ao selecionar **Ver** > **Terminal integrado**.

1. Introduza o comando seguinte no terminal integrado do Visual Studio Code e inicie sessão no Docker. Tem de iniciar sessão com as suas credenciais do Azure Container Registry de modo a poder enviar a imagem do módulo para o registo.
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Utilize o nome do utilizador, palavra-passe e servidor de início de sessão que copiou do Azure Container Registry na primeira secção. Também pode obtê-los novamente a partir da secção **Chaves de acesso** do registo no portal do Azure.

2. No explorador do VS Code, clique com o botão direito do rato no ficheiro **deployment.template.json** e selecione **Criar e Emitir solução do IoT Edge**.

Quando indicar ao Visual Studio Code para criar a solução, este gera primeiro um ficheiro `deployment.json` numa nova pasta **config**. As informações para o ficheiro deployment.json são recolhidas a partir do ficheiro de modelo que atualizou, do ficheiro .env que utilizou para armazenar as credenciais do registo de contentor e do ficheiro module.json na pasta CModule.

Em seguida, o Visual Studio Code executa dois comandos no terminal integrado: `docker build` e `docker push`. Estes dois comandos criam o código, colocam `CModule.dll` em contentores e enviam-no para o registo de contentor que especificou quando inicializou a solução.

Pode ver o endereço da imagem de contentor completo com a etiqueta no terminal integrado do VS Code. O endereço da imagem baseia-se nas informações no ficheiro `module.json`, com o formato **\<repositório\>:\<versão\>-\<plataforma\>**. Neste tutorial, deve ser semelhante a **myregistry.azurecr.io/cmodule:0.0.1-amd64**.

>[!TIP]
>Se receber um erro ao tentar criar e enviar por push o seu módulo, tome as seguintes verificações:
>* Iniciou sessão ao Docker no Visual Studio Code, utilizando as credenciais do seu registo de contentor? Estas credenciais são diferentes dos que utilizar para iniciar sessão no portal do Azure.
>* O repositório do contentor está correto? Open **módulos** > **cmodule** > **Module** e encontre a **repositório** campo. O repositório de imagens deve ser semelhante à  **\<registryname\>.azurecr.io/cmodule**. 
>* Está a criar o mesmo tipo de contentores que está a executar o seu computador de desenvolvimento? Visual Studio Code é predefinida como contentores do Linux amd64. Se o computador de desenvolvimento estiver a executar contentores do Linux arm32v7, atualize a plataforma na barra de status azul na parte inferior da janela do VS Code, de acordo com sua plataforma de contentores. Módulos de C não podem ser criados como contentores do Windows. 

## <a name="deploy-and-run-the-solution"></a>Implementar e executar a solução

No artigo de início rápido que utilizou para configurar o seu dispositivo IoT Edge, implementou um módulo com o portal do Azure. Também pode implementar módulos com a extensão do Kit de ferramentas do Azure IoT Hub (anteriormente conhecido como extensão do Kit de ferramentas do Azure IoT) para Visual Studio Code. Já tem um manifesto de implementação preparado para o seu cenário, o ficheiro **deployment.json**. Agora tudo o que precisa de fazer é selecionar um dispositivo para receber a implementação.

1. Na paleta de comandos VS Code, execute **IoT Hub do Azure: Selecione o IoT Hub**.

2. Escolha a subscrição e o hub IoT que contém o dispositivo do IoT Edge que pretende configurar.

3. No explorador do VS Code, expanda a secção **Dispositivos do Hub IoT do Azure**.

4. Carregue com o botão direito do rato no nome do seu dispositivo do IoT Edge e, em seguida, selecione **Criar Implementação para o Dispositivo Único**.

   ![Criar implementação para dispositivo único](./media/tutorial-c-module/create-deployment.png)

5. Selecione o ficheiro **deployment.json** na pasta **config** e, em seguida, clique em **Selecionar Manifesto de Implementação do Edge**. Não utilize o ficheiro deployment.template.json.

6. Clique no botão Atualizar. Deverá ver o novo **CModule** em execução, juntamente com o módulo **TempSensor**, bem como **$edgeAgent** e **$edgeHub**.

## <a name="view-generated-data"></a>Ver os dados gerados

Depois de aplicar o manifesto de implementação no seu dispositivo IoT Edge, o runtime do IoT Edge no dispositivo recolhe as novas informações de implementação e começa a ser executado no mesmo. Quaisquer módulos em execução no dispositivo que não estão incluídos no manifesto de implementação são parados. Quaisquer módulos em falta do dispositivo são iniciados.

Pode ver o estado do seu dispositivo do IoT Edge com a secção **Dispositivos do Hub IoT do Azure** do explorador do Visual Studio Code. Expanda os detalhes do seu dispositivo para ver uma lista de módulos implementados e em execução.

No próprio dispositivo IoT Edge, pode ver o estado dos seus módulos de implementação com o comando `iotedge list`. Deverá ver quatro módulos: os dois módulos de runtime do IoT Edge, o tempSensor e o módulo personalizado que criou neste tutorial. Pode demorar alguns minutos para que todos os módulos comecem, por isso, execute novamente o comando se não os vir todos inicialmente.

Para ver as mensagens que são geradas por qualquer módulo, utilize o comando `iotedge logs <module name>`.

Pode ver as mensagens conforme chegam ao seu hub IoT através do Visual Studio Code.

1. Para monitorizar os dados que chegam ao hub IoT, clique em **...** e selecione **Iniciar Monitorização de Mensagens D2C**.
2. Para monitorizar a mensagem D2C para um dispositivo específico, clique com o botão direito do rato na lista e selecione **Iniciar Monitorização de Mensagens D2C**.
3. Para parar a monitorização de dados, execute o comando **IoT Hub do Azure: Parar a monitorização de mensagens D2C** na paleta de comandos.
4. Para ver ou atualizar o módulo duplo, clique com o botão direito do rato no módulo na lista e selecione **Editar módulo duplo**. Para atualizar o módulo duplo, guarde o ficheiro JSON duplo e clique com o botão direito do rato na área de editor e selecione **Atualizar Módulo Duplo**.
5. Para ver os registos do Docker, pode instalar o [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) para o VS Code e localizar os módulos em execução localmente no explorador do Docker. No menu de contexto, clique em **Mostrar Registos** para ver no terminal integrado.

## <a name="clean-up-resources"></a>Limpar recursos

Se planeia avançar para o próximo artigo recomendado, pode manter os recursos e as configurações que criou e reutilizá-los. Também pode continuar a utilizar o mesmo dispositivo IoT Edge como um dispositivo de teste.

Caso contrário, pode eliminar as configurações locais e os recursos do Azure que criou neste artigo para evitar custos.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, criou uma função do módulo do IoT Edge que contém código para filtrar dados não processados gerados pelo seu dispositivo IoT Edge. Quando estiver pronto para criar os seus próprios módulos, pode saber mais sobre como [desenvolver um módulo em C com o Azure IoT Edge para o Visual Studio Code](how-to-develop-c-module.md). Pode continuar para os próximos tutoriais para saber mais sobre outras formas em que o Azure IoT Edge o pode ajudar a transformar os dados em informações empresariais na periferia.

> [!div class="nextstepaction"]
> [Armazenar dados na periferia com bases de dados do SQL Server](tutorial-store-data-sql-server.md)

