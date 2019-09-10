---
title: Tutorial desenvolver módulo C para Linux-Azure IoT Edge | Microsoft Docs
description: Este tutorial mostra como criar um módulo de IoT Edge com código C e implantá-lo em um dispositivo Linux em execução IoT Edge
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 08/23/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 581d2e03474eb7e740f9d0468022269bdb20b663
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/09/2019
ms.locfileid: "70813812"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-linux-devices"></a>Tutorial: Desenvolver um módulo C IoT Edge para dispositivos Linux

Use Visual Studio Code para desenvolver código C e implantá-lo em um dispositivo Linux em execução Azure IoT Edge. 

Pode utilizar os módulos do IoT Edge para implementar código que aplica a sua lógica de negócio diretamente nos seus dispositivos IoT Edge. Este tutorial explica-lhe como criar e implementar um módulo do IoT Edge que filtra dados de sensores. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Utilizar o Visual Studio Code para criar um módulo do IoT Edge em C
> * Utilizar o Visual Studio Code e o Docker para criar uma imagem do Docker e publicá-la num registo de contentor
> * Implementar o módulo no seu dispositivo IoT Edge
> * Ver os dados gerados

O módulo do IoT Edge que criou neste tutorial filtra os dados de temperatura gerados pelo seu dispositivo. Envia apenas mensagens de origem caso a temperatura seja superior a um limiar especificado. Este tipo de análise no Edge é útil para reduzir a quantidade de dados comunicados e armazenados na cloud.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Escopo da solução

Este tutorial demonstra como desenvolver um módulo em **C** usando **Visual Studio Code**e como implantá-lo em um **dispositivo Linux**. Se você estiver desenvolvendo módulos para dispositivos Windows, vá para [desenvolver um módulo C IOT Edge para dispositivos Windows](tutorial-c-module-windows.md) .

Use a tabela a seguir para entender suas opções de desenvolvimento e implantação de módulos C para Linux: 

| C | Visual Studio Code | Visual Studio | 
| - | ------------------ | ------------- |
| **AMD64 do Linux** | ![Usar VS Code para módulos C no Linux AMD64](./media/tutorial-c-module/green-check.png) | ![Usar o VS para módulos C no Linux AMD64](./media/tutorial-c-module/green-check.png) |
| **ARM32 do Linux** | ![Usar VS Code para módulos C no Linux ARM32](./media/tutorial-c-module/green-check.png) | ![Usar o VS para módulos C no Linux ARM32](./media/tutorial-c-module/green-check.png) |

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, você deve ter passado pelo tutorial anterior para configurar seu ambiente de desenvolvimento para o desenvolvimento de contêiner do Linux: [Desenvolva módulos IOT Edge para dispositivos Linux](tutorial-develop-for-linux.md). Ao concluir esse tutorial, você deve ter os seguintes pré-requisitos em vigor: 

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) no escalão gratuito ou standard no Azure.
* Um [dispositivo Linux executando o Azure IOT Edge](quickstart-linux.md)
* Um registro de contêiner, como o [registro de contêiner do Azure](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio Code](https://code.visualstudio.com/) configurado com as [ferramentas de IOT do Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) configurado para executar contêineres do Linux.

Para desenvolver um módulo IoT Edge em C, instale os seguintes pré-requisitos adicionais em seu computador de desenvolvimento: 

* [Extensão C/C++](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools) para o Visual Studio Code.

## <a name="create-a-module-project"></a>Criar um projeto de módulo

As etapas a seguir criam um projeto de módulo IoT Edge para C usando Visual Studio Code e a extensão de ferramentas de IoT do Azure. Depois de criar um modelo de projeto, adicione um novo código para que o módulo filtre as mensagens com base em suas propriedades relatadas. 

### <a name="create-a-new-project"></a>Criar um novo projeto

Crie um modelo de solução C que pode personalizar com o seu próprio código.

1. Selecione **Ver** > **Paleta de Comandos** para abrir a paleta de comandos do VS Code.

2. Na paleta de comandos, digite e execute o comando **Azure:**  Entre e siga as instruções para entrar na sua conta do Azure. Se já iniciou sessão, pode ignorar este passo.

3. Na paleta de comandos, digite e execute o comando **Azure IOT Edge: Nova solução**de IOT Edge. Siga as instruções na paleta de comandos para criar a sua solução.

   | Campo | Valor |
   | ----- | ----- |
   | Selecionar pasta | Escolha a localização no computador de desenvolvimento na qual o VS Code vai criar os ficheiros da solução. |
   | Indicar um nome para a solução | Introduza um nome descritivo para a sua solução ou aceite a predefinição **EdgeSolution**. |
   | Selecionar modelo de módulo | Escolher **C módulo**. |
   | Indicar um nome para o módulo | Dê o nome **CModule** ao módulo. |
   | Indicar o repositório de imagens do Docker para o módulo | Os repositórios de imagens incluem o nome do seu registo de contentor e o nome da sua imagem de contentor. Sua imagem de contêiner é preenchida previamente com base no nome que você forneceu na última etapa. Substitua **localhost:5000** pelo valor do servidor de início de sessão do registo de contentor do Azure Container Registry. Pode obter o servidor de início de sessão na página Overview (Descrição Geral) do registo de contentor no portal do Azure. <br><br> O repositório de imagens final é \<semelhante ao\>nome do registro. azurecr.Io/cmodule. |
 
   ![Fornecer repositório de imagens do Docker](./media/tutorial-c-module/repository.png)

### <a name="add-your-registry-credentials"></a>Adicionar as credenciais do registo

O ficheiro de ambiente armazena as credenciais do seu registo de contentor e partilha-as com o runtime do IoT Edge. O runtime precisa destas credenciais para solicitar as imagens privadas para o dispositivo IoT Edge.

1. No explorador do VS Code, abra o ficheiro .env.
2. Atualize os campos com os valores **nome de utilizador** e **palavra-passe** que copiou do seu Azure Container Registry.
3. Guarde este ficheiro.

### <a name="select-your-target-architecture"></a>Selecione sua arquitetura de destino

Atualmente, Visual Studio Code pode desenvolver módulos C para dispositivos Linux AMD64 e Linux ARM32v7. Você precisa selecionar qual arquitetura está sendo direcionada a cada solução, porque o contêiner é compilado e executado de forma diferente para cada tipo de arquitetura. O padrão é o Linux AMD64. 

1. Abra a paleta de comandos e procure **Azure IOT Edge: Defina a plataforma de destino padrão para**a solução de borda ou selecione o ícone de atalho na barra lateral na parte inferior da janela. 

2. Na paleta de comandos, selecione a arquitetura de destino na lista de opções. Para este tutorial, estamos usando uma máquina virtual Ubuntu como o dispositivo IoT Edge, portanto, manterá o **AMD64**padrão. 

### <a name="update-the-module-with-custom-code"></a>Atualizar o módulo com o código personalizado

O código de módulo padrão recebe mensagens em uma fila de entrada e as passa por uma fila de saída. Vamos adicionar um código adicional para que o módulo processe as mensagens na borda antes de encaminhá-las ao Hub IoT. Atualize o módulo para que ele analise os dados de temperatura em cada mensagem e só envie a mensagem para o Hub IoT se a temperatura exceder um determinado limite. 

1. Os dados do sensor neste cenário estão no formato JSON. Para filtrar mensagens no formato JSON, importe uma biblioteca JSON para C. Este tutorial utiliza o Parson.

   1. Baixe o [repositório GitHub do Parson](https://github.com/kgabis/parson). Copie os ficheiros **parson.c** e **parson.h** para a pasta **CModule**.

   2. Abra **modules** > **CModule** > **CMakeLists.txt**. Na parte superior do ficheiro, importe os ficheiros do Parson como uma biblioteca chamada **my_parson**.

      ```
      add_library(my_parson
          parson.c
          parson.h
      )
      ```

   3. Adicione **my_parson** à lista de bibliotecas na função **target_link_libraries** de CMakeLists.txt.

   4. Guarde o ficheiro **CMakeLists.txt**.

   5. Abra **modules** > **CModule** > **main.c**. Na parte inferior da lista de instruções include, adicione uma nova para incluir `parson.h` para suporte a JSON:

      ```c
      #include "parson.h"
      ```

1. No ficheiro **main.c**, adicione uma variável global chamada `temperatureThreshold` depois da secção de inclusão. Esta variável define o valor que a temperatura medida deve exceder, para que os dados sejam enviados para o Hub IoT.

    ```c
    static double temperatureThreshold = 25;
    ```

1. Localize a `CreateMessageInstance` função em Main. c. Substitua a instrução if-else interna pelo código a seguir que adiciona algumas linhas de funcionalidade: 

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

1. Substitua toda a função `InputQueue1Callback` pelo código abaixo. Esta função implementa o filtro de mensagens real. Quando uma mensagem é recebida, ela verifica se a temperatura relatada excede o limite. Em caso afirmativo, ele encaminha a mensagem por meio de sua fila de saída. Caso contrário, ele ignora a mensagem. 

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

1. Adicione uma função `moduleTwinCallback`. Este método recebe atualizações nas propriedades pretendidas do módulo duplo e atualiza a variável **temperatureThreshold** para corresponder. Todos os módulos têm o seu próprio módulo duplo, que lhe permite configurar o código em execução no interior de um módulo diretamente a partir da cloud.

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

1. Localize a `SetupCallbacksForModule` função. Substitua a função pelo código a seguir que adiciona uma instrução **else if** para verificar se o módulo de atualização em cima foi atualizado.

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

1. Salve o arquivo Main. c.

1. No explorador do VS Code, abra o ficheiro **deployment.template.json** na área de trabalho da solução do IoT Edge. 

1. Adicione o módulo duplo CModule ao manifesto da implementação. Insira o seguinte conteúdo JSON na parte inferior da secção `moduleContent`, após o módulo duplo `$edgeHub`:

   ```json
   "CModule": {
       "properties.desired":{
           "TemperatureThreshold":25
       }
   }
   ```

   ![Adicionar o CModule duplo ao modelo de implementação](./media/tutorial-c-module/module-twin.png)

1. Guarde o ficheiro **deployment.template.json**.

## <a name="build-and-push-your-module"></a>Crie e envie por push seu módulo

Na seção anterior, você criou uma solução de IoT Edge e adicionou o código ao CModule que filtrará as mensagens em que a temperatura da máquina relatada está dentro dos limites aceitáveis. Agora, tem de criar a solução como uma imagem de contentor e enviá-la para o registo de contentor.

1. Abra o terminal vs Code selecionando **Exibir** > **terminal**.

1. Entre no Docker digitando o seguinte comando no terminal. Entre com o nome de usuário, a senha e o servidor de logon do seu registro de contêiner do Azure. Você pode recuperar esses valores da seção **chaves de acesso** do registro no portal do Azure.
     
   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Você pode receber um aviso de segurança recomendando o uso `--password-stdin`de. Embora essa prática recomendada seja recomendada para cenários de produção, ela está fora do escopo deste tutorial. Para obter mais informações, consulte a referência de [logon do Docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) .

2. No explorador do VS Code, clique com o botão direito do rato no ficheiro **deployment.template.json** e selecione **Criar e Emitir solução do IoT Edge**.

   O comando Build e Push inicia três operações. Primeiro, ele cria uma nova pasta na solução chamada **configuração** que contém o manifesto de implantação completa, criado sem informações no modelo de implantação e outros arquivos de solução. Em segundo lugar, `docker build` ele é executado para criar a imagem de contêiner com base no dockerfile apropriado para sua arquitetura de destino. Em seguida, ele `docker push` é executado para enviar por push o repositório de imagens para o registro de contêiner.

## <a name="deploy-modules-to-device"></a>Implantar módulos no dispositivo

Use o Visual Studio Code Explorer e a extensão de ferramentas de IoT do Azure para implantar o projeto de módulo em seu dispositivo de IoT Edge. Você já tem um manifesto de implantação preparado para seu cenário, o arquivo **Deployment. JSON** na pasta de configuração. Agora tudo o que precisa de fazer é selecionar um dispositivo para receber a implementação.

Verifique se o dispositivo IoT Edge está em execução. 

1. No Visual Studio Code Explorer, expanda a seção **dispositivos do Hub IOT do Azure** para ver sua lista de dispositivos IOT.

2. Carregue com o botão direito do rato no nome do seu dispositivo do IoT Edge e, em seguida, selecione **Criar Implementação para o Dispositivo Único**.

3. Selecione o ficheiro **deployment.json** na pasta **config** e, em seguida, clique em **Selecionar Manifesto de Implementação do Edge**. Não utilize o ficheiro deployment.template.json.

4. Clique no botão Atualizar. Você deve ver o novo **CModule** em execução junto com o módulo **SimulatedTemperatureSensor** e o **$edgeAgent** e **$edgeHub**.

## <a name="view-generated-data"></a>Ver os dados gerados

Depois de aplicar o manifesto de implementação no seu dispositivo IoT Edge, o runtime do IoT Edge no dispositivo recolhe as novas informações de implementação e começa a ser executado no mesmo. Quaisquer módulos em execução no dispositivo que não estão incluídos no manifesto de implementação são parados. Quaisquer módulos em falta do dispositivo são iniciados.

Pode ver o estado do seu dispositivo do IoT Edge com a secção **Dispositivos do Hub IoT do Azure** do explorador do Visual Studio Code. Expanda os detalhes do seu dispositivo para ver uma lista de módulos implementados e em execução.

1. No Visual Studio Code Explorer, clique com o botão direito do mouse no nome do dispositivo de IoT Edge e selecione **Iniciar Monitoramento de ponto de extremidade de evento interno**.

2. Exiba as mensagens que chegam ao seu hub IoT. Pode levar algum tempo para que as mensagens cheguem, pois o dispositivo IoT Edge precisa receber sua nova implantação e iniciar todos os módulos. Em seguida, as alterações feitas no código CModule esperam até que a temperatura do computador atinja 25 graus antes de enviar mensagens. Ele também adiciona o **alerta** de tipo de mensagem a todas as mensagens que atingem esse limite de temperatura. 

   ![Exibir mensagens que chegam ao Hub IoT](./media/tutorial-c-module/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Edite o módulo...

Usamos o módulo CModule no manifesto de implantação para definir o limite de temperatura em 25 graus. Você pode usar o módulo "atualizar" para alterar a funcionalidade sem precisar atualizar o código do módulo.

1. Em Visual Studio Code, expanda os detalhes em seu dispositivo de IoT Edge para ver os módulos em execução. 

2. Clique com o botão direito do mouse em **CModule** e selecione **Editar módulo**. 

3. Localize **TemperatureThreshold** nas propriedades desejadas. Altere seu valor para uma nova temperatura de 5 graus para 10 graus acima da temperatura mais recente relatada. 

4. Salve o arquivo de módulo.

5. Clique com o botão direito do mouse em qualquer lugar no painel de edição do módulo e selecione **Atualizar módulo**. 

5. Monitore as mensagens de entrada do dispositivo para a nuvem. Você deve ver as mensagens param até que o novo limite de temperatura seja atingido. 

## <a name="clean-up-resources"></a>Limpar recursos

Se planeia avançar para o próximo artigo recomendado, pode manter os recursos e as configurações que criou e reutilizá-los. Também pode continuar a utilizar o mesmo dispositivo IoT Edge como um dispositivo de teste.

Caso contrário, você pode excluir as configurações locais e os recursos do Azure que você usou neste artigo para evitar cobranças.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou uma função do módulo do IoT Edge que contém código para filtrar dados não processados gerados pelo seu dispositivo IoT Edge. Quando estiver pronto para criar seus próprios módulos, você pode aprender mais sobre como [desenvolver seus próprios módulos IOT Edge](module-development.md) ou como [desenvolver módulos com Visual Studio Code](how-to-vs-code-develop-module.md). Você pode continuar nos próximos tutoriais para saber como Azure IoT Edge pode ajudá-lo a implantar os serviços de nuvem do Azure para processar e analisar dados na borda.

> [!div class="nextstepaction"]
> [Functions](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Custom Vision Service](tutorial-deploy-custom-vision.md)

