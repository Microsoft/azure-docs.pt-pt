---
title: Tutorial de desenvolver o módulo de C para Windows - Azure IoT Edge | Documentos da Microsoft
description: Este tutorial mostra-lhe como criar um módulo do IoT Edge com o código C e implementá-la a um dispositivo de Windows que executam o IoT Edge
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 05/28/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 79f3b125a4cb88b3555cf13aa4d4bc5c430df166
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/29/2019
ms.locfileid: "66303899"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-windows-devices"></a>Tutorial: Desenvolver um módulo de C IoT Edge para dispositivos Windows

Utilize o Visual Studio para desenvolver o código C e implementá-la no dispositivo Windows com o Azure IoT Edge. 

Pode utilizar os módulos do Azure IoT Edge para implementar código que aplica a sua lógica de negócio diretamente aos seus dispositivos IoT Edge. Este tutorial explica-lhe como criar e implementar um módulo do IoT Edge que filtra dados de sensores. Neste tutorial, ficará a saber como:    

> [!div class="checklist"]
> * Utilize o Visual Studio para criar um módulo do IoT Edge com base no SDK de C.
> * Utilize o Visual Studio e a Docker para criar uma imagem do Docker e publicá-lo para o seu registo.
> * Implemente o módulo no seu dispositivo IoT Edge.
> * Veja os dados gerados.

O módulo do IoT Edge que criou neste tutorial filtra os dados de temperatura que são gerados pelo seu dispositivo. Envia apenas mensagens de origem caso a temperatura seja superior a um limiar especificado. Este tipo de análise no Edge é útil para reduzir a quantidade de dados que são comunicados e armazenados na cloud. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Âmbito de solução

Este tutorial demonstra como desenvolver um módulo numa **C** usando **2019 do Visual Studio**e como implementá-la para um **dispositivo de Windows**. Se estiver a desenvolver módulos para dispositivos de Linux, aceda a [desenvolver um módulo de C IoT Edge para dispositivos de Linux](tutorial-c-module.md) em vez disso. 

Utilize a tabela seguinte para compreender as opções para desenvolver e implantar módulos de C para dispositivos Windows: 

| C | Visual Studio Code | Visual Studio 2017/2019 | 
| -- | ------------------ | ------------------ |
| **Windows AMD64** |  | ![Desenvolver módulos de C para WinAMD64 no Visual Studio](./media/tutorial-c-module/green-check.png) |

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, deve já leu o tutorial anterior para configurar o ambiente de desenvolvimento para o desenvolvimento de contentores do Windows: [Desenvolver módulos do IoT Edge para dispositivos Windows](tutorial-develop-for-windows.md). Depois de concluir esse tutorial, deve ter os seguintes pré-requisitos no local: 

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) no escalão gratuito ou standard no Azure.
* R [dispositivo de Windows com o Azure IoT Edge](quickstart.md).
* Um registo de contentor, como [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/install-visual-studio) configurado com o [ferramentas do Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) extensão.
* [Docker CE](https://docs.docker.com/install/) configurado para executar contentores do Windows.
* O SDK do Azure IoT para C. 

> [!TIP]
> Se estiver a utilizar o Visual Studio 2017 (versão 15.7 ou superior), transfira e instale [ferramentas do Azure IoT Edge (pré-visualização)](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) para VS 2017 a partir do Visual Studio marketplace

## <a name="create-a-module-project"></a>Criar um projeto de módulo

Os passos seguintes criam um projeto de módulo do IoT Edge com base no SDK de C com o Visual Studio e a extensão das ferramentas do Azure IoT Edge. Depois de ter um modelo de projeto criado, adicione o novo código para que o módulo filtra as mensagens com base nas respetivas propriedades comunicadas. 

### <a name="create-a-new-project"></a>Criar um novo projeto

Crie um modelo de solução C que pode personalizar com o seu próprio código.

1. Inicie o Visual Studio 2019 e selecione **criar novo projeto**.

2. Na janela novo projeto, de pesquisa **IoT Edge** do projeto e escolha o **Azure IoT Edge (Windows amd64)** projeto. Clique em **Seguinte**. 

   ![Criar um novo projeto do Azure IoT Edge](./media/tutorial-c-module-windows/new-project.png)

3. Na configurar a nova janela de projeto, mudar o nome do projeto e a solução para algo descritivo semelhantes **CTutorialApp**. Clique em **criar** para criar o projeto. 

   ![Configurar um novo projeto do Azure IoT Edge](./media/tutorial-c-module-windows/configure-project.png)

4. Na aplicação do IoT Edge e janela do módulo, configure o seu projeto com os seguintes valores: 

   | Campo | Value |
   | ----- | ----- |
   | Seleccionar um modelo | Selecione **C módulo**. | 
   | Nome do projeto de módulo | Dê o nome **CModule** ao módulo. | 
   | Repositório de imagens do docker | Os repositórios de imagens incluem o nome do seu registo de contentor e o nome da sua imagem de contentor. A imagem de contentor é pré-preenchida com aceder a partir do valor de nome de projeto de módulo. Substitua **localhost:5000** pelo valor do servidor de início de sessão do registo de contentor do Azure Container Registry. Pode obter o servidor de início de sessão na página Overview (Descrição Geral) do registo de contentor no portal do Azure. <br><br> O repositório de imagem final se parece com \<nome do registo\>.azurecr.io/cmodule. |

   ![Configurar o seu projeto para o dispositivo de destino, o tipo de módulo e o registo de contentor](./media/tutorial-c-module-windows/add-application-and-module.png)

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

4. Save the deployment.template.json file. 

### Update the module with custom code

The default module code receives messages on an input queue and passes them along through an output queue. Let's add some additional code so that the module processes the messages at the edge before forwarding them to IoT Hub. Update the module so that it analyzes the temperature data in each message, and only sends the message to IoT Hub if the temperature exceeds a certain threshold. 


1. The data from the sensor in this scenario comes in JSON format. To filter messages in JSON format, import a JSON library for C. This tutorial uses Parson.

   1. Download the [Parson GitHub repository](https://github.com/kgabis/parson). Copy the **parson.c** and **parson.h** files into the **CModule** project.

   2. In Visual Studio, open the **CMakeLists.txt** file from the CModule project folder. At the top of the file, import the Parson files as a library called **my_parson**.

      ```
      add_library(my_parson        parson.c        parson.h    )
      ```

   3. Add **my_parson** to the list of libraries in the **target_link_libraries** section of the CMakeLists.txt file.

   4. Save the **CMakeLists.txt** file.

   5. Open **CModule** > **main.c**. At the bottom of the list of include statements, add a new one to include `parson.h` for JSON support:

      ```c
      #include "parson.h"
      ```

2.  Na **Main** do ficheiro, adicione uma variável global chamada `temperatureThreshold` junto a variável messagesReceivedByInput1Queue. Esta variável define o valor que a temperatura medida deve exceder, para que os dados sejam enviados para o Hub IoT.

    ```c
    static double temperatureThreshold = 25;
    ```

3. Encontrar o `CreateMessageInstance` função no main. Substitua a instrução if-else interna com o seguinte código que adiciona algumas linhas de funcionalidade: 

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

   As novas linhas de código na instrução else adicionar uma nova propriedade para a mensagem, que a mensagem como um alerta de etiquetas. Esse código das etiquetas todas as mensagens como alertas, uma vez que vamos adicionar funcionalidade que só envia mensagens para o IoT Hub se que reportam temperaturas elevadas. 

4. Encontrar o `InputQueue1Callback` funcionar e substitua a função de toda o código a seguir. Esta função implementa o filtro de mensagens real. Quando uma mensagem é recebida, ele verifica se a temperatura comunicada excede o limiar. Se Sim, em seguida, encaminha a mensagem por meio da sua fila de saída. Caso contrário, em seguida, ele ignora a mensagem. 

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

6. Encontrar o `SetupCallbacksForModule` função. Substitua a função com o seguinte código que adiciona uma **ou se** instrução para verificar se o módulo duplo foi atualizado. 

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

7. Guarde o ficheiro Main.

8. Abra o **deployment.template.json** ficheiro. 

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

## <a name="build-and-push-your-module"></a>Criar e enviar por push o seu módulo

Na secção anterior, criou uma solução de IoT Edge e adicionar código para o **CModule** para filtrar as mensagens em que a temperatura comunicada máquina é inferior ao limiar aceitável. Agora, tem de criar a solução como uma imagem de contentor e enviá-la para o registo de contentor. 

1. Utilize o seguinte comando para iniciar sessão no Docker no seu computador de desenvolvimento. Inicie sessão com o nome de utilizador, a palavra-passe e o servidor de início de sessão do seu registo de contentor do Azure. Pode obter estes valores a partir da **chaves de acesso** secção do seu registo no portal do Azure.

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

3. Ver as mensagens que chegam ao IoT Hub. Pode demorar algum tempo para as mensagens chegam, porque o dispositivo do IoT Edge tem de receber a sua nova implementação e comece a todos os módulos. Em seguida, as alterações feitas no código CModule Aguarde até a temperatura de máquina atinge 25 graus antes de enviar mensagens. Ele também adiciona o tipo de mensagem **alerta** para todas as mensagens que atingir esse limite de temperatura. 

   ![Ver mensagens que chegam ao IoT Hub](./media/tutorial-c-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Editar o módulo duplo

Usamos o duplo do módulo de CModule para definir o limiar de temperatura em graus 25. Pode utilizar o módulo duplo para alterar a funcionalidade sem ter de atualizar o código do módulo.

1. No Visual Studio, abra a **deployment.windows amd64.json** ficheiro. (Não o ficheiro de deployment.template. Se não vir a implementação de manifesto no ficheiro de configuração no solution explorer, selecione o **Mostrar todos os ficheiros** ícone na barra de ferramentas do explorer.)

2. Localizar o duplo CModule e alterar o valor do **temperatureThreshold** parâmetro para uma nova temperatura graus de 5 a 10 graus de mais do que a temperatura comunicada mais recente. 

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
