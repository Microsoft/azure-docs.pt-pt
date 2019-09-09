---
title: Tutorial desenvolver módulo C para Windows-Azure IoT Edge | Microsoft Docs
description: Este tutorial mostra como criar um módulo de IoT Edge com código C e implantá-lo em um dispositivo Windows em execução IoT Edge
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 05/28/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: b69000e251bb71bc08ea97b24a7422daf2cd89e4
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/09/2019
ms.locfileid: "70813822"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-windows-devices"></a>Tutorial: Desenvolver um módulo C IoT Edge para dispositivos Windows

Use o Visual Studio para desenvolver código C e implantá-lo em um dispositivo Windows executando o Azure IoT Edge. 

Pode utilizar os módulos do Azure IoT Edge para implementar código que aplica a sua lógica de negócio diretamente aos seus dispositivos IoT Edge. Este tutorial explica-lhe como criar e implementar um módulo do IoT Edge que filtra dados de sensores. Neste tutorial, ficará a saber como:    

> [!div class="checklist"]
> * Use o Visual Studio para criar um módulo IoT Edge baseado no SDK do C.
> * Use o Visual Studio e o Docker para criar uma imagem do Docker e publicá-la no registro.
> * Implemente o módulo no seu dispositivo IoT Edge.
> * Veja os dados gerados.

O módulo do IoT Edge que criou neste tutorial filtra os dados de temperatura que são gerados pelo seu dispositivo. Envia apenas mensagens de origem caso a temperatura seja superior a um limiar especificado. Este tipo de análise no Edge é útil para reduzir a quantidade de dados que são comunicados e armazenados na cloud. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Escopo da solução

Este tutorial demonstra como desenvolver um módulo em **C** usando o **Visual Studio 2019**e como implantá-lo em um **dispositivo Windows**. Se você estiver desenvolvendo módulos para dispositivos Linux, vá para [desenvolver um módulo C IOT Edge para dispositivos Linux](tutorial-c-module.md) . 

Use a tabela a seguir para entender suas opções de desenvolvimento e implantação de módulos C em dispositivos Windows: 

| C | Visual Studio Code | Visual Studio 2017/2019 | 
| -- | ------------------ | ------------------ |
| **Windows AMD64** |  | ![Desenvolver módulos C para WinAMD64 no Visual Studio](./media/tutorial-c-module/green-check.png) |

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, você deve ter passado pelo tutorial anterior para configurar seu ambiente de desenvolvimento para o desenvolvimento de contêiner do Windows: [Desenvolva módulos IOT Edge para dispositivos Windows](tutorial-develop-for-windows.md). Depois de concluir esse tutorial, você deve ter os seguintes pré-requisitos em vigor: 

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) no escalão gratuito ou standard no Azure.
* Um [dispositivo Windows executando o Azure IOT Edge](quickstart.md).
* Um registro de contêiner, como o [registro de contêiner do Azure](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/install-visual-studio) configurado com a extensão de [ferramentas de Azure IOT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) .
* [Área de trabalho do Docker](https://docs.docker.com/docker-for-windows/install/) configurada para executar contêineres do Windows.
* Instale o SDK do Azure IoT C para Windows x64 por meio do vcpkg:

   ```powershell
   git clone https://github.com/Microsoft/vcpkg
   cd vcpkg
   .\bootstrap-vcpkg.bat
   .\vcpkg install azure-iot-sdk-c:x64-windows
   .\vcpkg --triplet x64-windows integrate install
   ```
   
> [!TIP]
> Se você estiver usando o Visual Studio 2017 (versão 15,7 ou superior), baixe e instale o [Azure IOT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) para vs 2017 no Visual Studio Marketplace

## <a name="create-a-module-project"></a>Criar um projeto de módulo

As etapas a seguir criam um projeto de módulo IoT Edge baseado no SDK do C usando o Visual Studio e a extensão de ferramentas de Azure IoT Edge. Depois de criar um modelo de projeto, adicione um novo código para que o módulo filtre as mensagens com base em suas propriedades relatadas. 

### <a name="create-a-new-project"></a>Criar um novo projeto

Crie um modelo de solução C que pode personalizar com o seu próprio código.

1. Inicie o Visual Studio 2019 e selecione **criar novo projeto**.

2. Na janela novo projeto, pesquise **IOT Edge** projeto e escolha o projeto **Azure IOT Edge (Windows AMD64)** . Clique em **Seguinte**. 

   ![Criar um novo projeto de Azure IoT Edge](./media/tutorial-c-module-windows/new-project.png)

3. Na janela configurar seu novo projeto, renomeie o projeto e a solução como algo descritivo, como **CTutorialApp**. Clique em **criar** para criar o projeto. 

   ![Configurar um novo projeto de Azure IoT Edge](./media/tutorial-c-module-windows/configure-project.png)

4. Na janela IoT Edge aplicativo e módulo, configure o projeto com os seguintes valores: 

   | Campo | Value |
   | ----- | ----- |
   | Seleccionar um modelo | Selecione **módulo C**. | 
   | Nome do projeto de módulo | Dê o nome **CModule** ao módulo. | 
   | Repositório de imagens do Docker | Os repositórios de imagens incluem o nome do seu registo de contentor e o nome da sua imagem de contentor. Sua imagem de contêiner é preenchida previamente a partir do valor do nome do projeto de módulo. Substitua **localhost:5000** pelo valor do servidor de início de sessão do registo de contentor do Azure Container Registry. Pode obter o servidor de início de sessão na página Overview (Descrição Geral) do registo de contentor no portal do Azure. <br><br> O repositório de imagens final é \<semelhante ao\>nome do registro. azurecr.Io/cmodule. |

   ![Configurar seu projeto para o dispositivo de destino, o tipo de módulo e o registro de contêiner](./media/tutorial-c-module-windows/add-application-and-module.png)

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


1. Os dados do sensor neste cenário estão no formato JSON. Para filtrar mensagens no formato JSON, importe uma biblioteca JSON para C. Este tutorial utiliza o Parson.

   1. Baixe o [repositório GitHub do Parson](https://github.com/kgabis/parson). Copie os arquivos **Parson. c** e **Parson. h** para o projeto **CModule** .

   2. No Visual Studio, abra o arquivo **CMakeLists. txt** na pasta do projeto CModule. Na parte superior do ficheiro, importe os ficheiros do Parson como uma biblioteca chamada **my_parson**.

      ```
      add_library(my_parson
          parson.c
          parson.h
      )
      ```

   3. Adicione **my_parson** à lista de bibliotecas na seção **Target_link_libraries** do arquivo CMakeLists. txt.

   4. Guarde o ficheiro **CMakeLists.txt**.

   5. Abra **CModule** > **Main. c**. Na parte inferior da lista de instruções include, adicione uma nova para incluir `parson.h` para suporte a JSON:

      ```c
      #include "parson.h"
      ```

2.  No arquivo **Main. c** , adicione uma variável global chamada `temperatureThreshold` ao lado da variável messagesReceivedByInput1Queue. Esta variável define o valor que a temperatura medida deve exceder, para que os dados sejam enviados para o Hub IoT.

    ```c
    static double temperatureThreshold = 25;
    ```

3. Localize a `CreateMessageInstance` função em Main. c. Substitua a instrução if-else interna pelo código a seguir que adiciona algumas linhas de funcionalidade: 

   ```c
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
   ```

   As novas linhas de código na instrução else adicionam uma nova propriedade à mensagem, que rotula a mensagem como um alerta. Esse código rotula todas as mensagens como alertas, pois adicionaremos funcionalidade que só envia mensagens ao Hub IoT se elas relatarem temperaturas altas. 

4. Localize a `InputQueue1Callback` função e substitua a função inteira pelo código a seguir. Esta função implementa o filtro de mensagens real. Quando uma mensagem é recebida, ela verifica se a temperatura relatada excede o limite. Em caso afirmativo, ele encaminha a mensagem por meio de sua fila de saída. Caso contrário, ele ignora a mensagem. 

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

        // Check if the message reports temperatures higher than the threshold
        JSON_Value *root_value = json_parse_string(messageBody);
        JSON_Object *root_object = json_value_get_object(root_value);
        double temperature;

        // If temperature exceeds threshold, send to output1
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
        // If message does not exceed threshold, do not forward
        else
        {
            printf("Not sending message (%zu) to the next stage in pipeline.\r\n", messagesReceivedByInput1Queue);
            result = IOTHUBMESSAGE_ACCEPTED;
        }

        messagesReceivedByInput1Queue++;
        return result;
    }
    ```

5. Adicione uma função `moduleTwinCallback`. Este método recebe atualizações nas propriedades pretendidas do módulo duplo e atualiza a variável **temperatureThreshold** para corresponder. Todos os módulos têm o seu próprio módulo duplo, que lhe permite configurar o código em execução no interior de um módulo diretamente a partir da cloud.

    ```c
    static void moduleTwinCallback(DEVICE_TWIN_UPDATE_STATE update_state, const unsigned char* payLoad, size_t size, void* userContextCallback)
    {
        printf("\r\nTwin callback called with (state=%s, size=%zu):\r\n%s\r\n",
            MU_ENUM_TO_STRING(DEVICE_TWIN_UPDATE_STATE, update_state), size, payLoad);
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

6. Localize a `SetupCallbacksForModule` função. Substitua a função pelo código a seguir que adiciona uma instrução **else if** para verificar se o módulo de atualização em cima foi atualizado. 

   ```c
   static int SetupCallbacksForModule(IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle)
   {
       int ret;

       if (IoTHubModuleClient_LL_SetInputMessageCallback(iotHubModuleClientHandle, "input1", InputQueue1Callback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
       {
           printf("ERROR: IoTHubModuleClient_LL_SetInputMessageCallback(\"input1\")..........FAILED!\r\n");
           ret = MU_FAILURE;
       }
       else if (IoTHubModuleClient_LL_SetModuleTwinCallback(iotHubModuleClientHandle, moduleTwinCallback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
       {
           printf("ERROR: IoTHubModuleClient_LL_SetModuleTwinCallback(default)..........FAILED!\r\n");
           ret = MU_FAILURE;
       }
       else
       {
           ret = 0;
       }

       return ret;
   }
   ```

7. Salve o arquivo Main. c.

8. Abra o arquivo **Deployment. Template. JSON** . 

9. Adicione o módulo duplo CModule ao manifesto da implementação. Insira o seguinte conteúdo JSON na parte inferior da secção `moduleContent`, após o módulo duplo `$edgeHub`:

   ```json
   "CModule": {
       "properties.desired":{
           "TemperatureThreshold":25
       }
   }
   ```

   ![Adicionar o CModule duplo ao modelo de implementação](./media/tutorial-c-module-windows/module-twin.png)

1. Guarde o ficheiro **deployment.template.json**.

## <a name="build-and-push-your-module"></a>Crie e envie por push seu módulo

Na seção anterior, você criou uma solução de IoT Edge e adicionou o código ao **CModule** para filtrar mensagens em que a temperatura da máquina relatada está abaixo do limite aceitável. Agora, tem de criar a solução como uma imagem de contentor e enviá-la para o registo de contentor. 

1. Use o comando a seguir para entrar no Docker em seu computador de desenvolvimento. Entre com o nome de usuário, a senha e o servidor de logon do seu registro de contêiner do Azure. Você pode recuperar esses valores da seção **chaves de acesso** do registro no portal do Azure.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Você pode receber um aviso de segurança recomendando o uso `--password-stdin`de. Embora essa prática recomendada seja recomendada para cenários de produção, ela está fora do escopo deste tutorial. Para obter mais informações, consulte a referência de [logon do Docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) .

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

3. Exiba as mensagens que chegam ao seu hub IoT. Pode levar algum tempo para que as mensagens cheguem, pois o dispositivo IoT Edge precisa receber sua nova implantação e iniciar todos os módulos. Em seguida, as alterações feitas no código CModule esperam até que a temperatura do computador atinja 25 graus antes de enviar mensagens. Ele também adiciona o **alerta** de tipo de mensagem a todas as mensagens que atingem esse limite de temperatura. 

   ![Exibir mensagens que chegam ao Hub IoT](./media/tutorial-c-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Edite o módulo...

Usamos o módulo CModule de 10º para definir o limite de temperatura em 25 graus. Você pode usar o módulo "atualizar" para alterar a funcionalidade sem precisar atualizar o código do módulo.

1. No Visual Studio, abra o arquivo **Deployment. Windows-AMD64. JSON** . (Não o arquivo Deployment. Template. Se você não vir o manifesto de implantação no arquivo de configuração no Gerenciador de soluções, selecione o ícone **Mostrar todos os arquivos** na barra de ferramentas do Explorer.)

2. Localize o CModule 10 e altere o valor do parâmetro **temperatureThreshold** para uma nova temperatura de 5 graus para 10 graus acima da temperatura relatada mais recente. 

3. Salve o arquivo **Deployment. Windows-AMD64. JSON** .

4. Siga as etapas de implantação novamente para aplicar o manifesto de implantação atualizado ao seu dispositivo. 

5. Monitore as mensagens de entrada do dispositivo para a nuvem. Você deve ver as mensagens param até que o novo limite de temperatura seja atingido. 

## <a name="clean-up-resources"></a>Limpar recursos 

Se planeia avançar para o próximo artigo recomendado, pode manter os recursos e as configurações que criou e reutilizá-los. Também pode continuar a utilizar o mesmo dispositivo IoT Edge como um dispositivo de teste. 

Caso contrário, você pode excluir as configurações locais e os recursos do Azure que você usou neste artigo para evitar cobranças. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, criou uma função do módulo do IoT Edge com código para filtrar os dados não processados que são gerados pelo seu dispositivo IoT Edge. Quando estiver pronto para criar seus próprios módulos, você pode aprender mais sobre como desenvolver [seus próprios módulos IOT Edge](module-development.md) ou como [desenvolver módulos com o Visual Studio](how-to-visual-studio-develop-module.md). Você pode continuar nos próximos tutoriais para saber como Azure IoT Edge pode ajudá-lo a implantar os serviços de nuvem do Azure para processar e analisar dados na borda.

> [!div class="nextstepaction"]
> [Funções](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)[](tutorial-deploy-custom-vision.md) [Machine Learning](tutorial-deploy-machine-learning.md)serviço de visão personalizada
> 
> 
