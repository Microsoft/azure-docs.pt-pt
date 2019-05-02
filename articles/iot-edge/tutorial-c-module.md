---
title: Tutorial de desenvolver o módulo de C para Linux - Azure IoT Edge | Documentos da Microsoft
description: Este tutorial mostra-lhe como criar um módulo do IoT Edge com o código C e implementá-la num dispositivo de Linux com o IoT Edge
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 04/23/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: f5f6b6473a3cbe078ae2357e1f908788e54af25d
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64573420"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-linux-devices"></a>Tutorial: Desenvolver um módulo de C IoT Edge para dispositivos do Linux

Utilize o Visual Studio Code para desenvolver o código C e implementá-la num dispositivo de Linux com o Azure IoT Edge. 

Pode utilizar os módulos do IoT Edge para implementar código que aplica a sua lógica de negócio diretamente nos seus dispositivos IoT Edge. Este tutorial explica-lhe como criar e implementar um módulo do IoT Edge que filtra dados de sensores. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Utilizar o Visual Studio Code para criar um módulo do IoT Edge em C
> * Utilizar o Visual Studio Code e o Docker para criar uma imagem do Docker e publicá-la num registo de contentor
> * Implementar o módulo no seu dispositivo IoT Edge
> * Ver os dados gerados

O módulo do IoT Edge que criou neste tutorial filtra os dados de temperatura gerados pelo seu dispositivo. Envia apenas mensagens de origem caso a temperatura seja superior a um limiar especificado. Este tipo de análise no Edge é útil para reduzir a quantidade de dados comunicados e armazenados na cloud.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Âmbito de solução

Este tutorial demonstra como desenvolver um módulo numa **C** usando **Visual Studio Code**e como implementá-la para um **dispositivo Linux**. Se estiver a desenvolver módulos para dispositivos Windows, aceda a [desenvolver um módulo de C IoT Edge para dispositivos Windows](tutorial-c-module-windows.md) em vez disso.

Utilize a tabela seguinte para compreender as opções para desenvolver e implantar módulos de C para Linux: 

| C | Visual Studio Code | Visual Studio 2017 | 
| - | ------------------ | ------------------ |
| **Linux AMD64** | ![Utilizar o VS Code para módulos de C em Linux AMD64](./media/tutorial-c-module/green-check.png) | ![Utilizar o VS 2017 para os módulos de C em Linux AMD64](./media/tutorial-c-module/green-check.png) |
| **ARM32 do Linux** | ![Utilizar o VS Code para módulos de C em Linux ARM32](./media/tutorial-c-module/green-check.png) | ![Utilizar o VS 2017 para os módulos de C em Linux ARM32](./media/tutorial-c-module/green-check.png) |

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, deve já leu o tutorial anterior para configurar o ambiente de desenvolvimento para o desenvolvimento de contentores do Linux: [Desenvolver módulos do IoT Edge para dispositivos de Linux](tutorial-develop-for-linux.md). Ao concluir esse tutorial, deve ter os seguintes pré-requisitos no local: 

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) no escalão gratuito ou standard no Azure.
* A [dispositivo de Linux com o Azure IoT Edge](quickstart-linux.md)
* Um registo de contentor, como [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio Code](https://code.visualstudio.com/) configurado com o [ferramentas de IoT do Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) configurado para executar contentores do Linux.

Para desenvolver um módulo do IoT Edge em C, instale os seguintes pré-requisitos adicionais no computador de desenvolvimento: 

* [Extensão C/C++](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools) para o Visual Studio Code.

## <a name="create-a-module-project"></a>Criar um projeto de módulo

Os passos seguintes criam um projeto de módulo do IoT Edge para C com o Visual Studio Code e a extensão das ferramentas de IoT do Azure. Depois de ter um modelo de projeto criado, adicione o novo código para que o módulo filtra as mensagens com base nas respetivas propriedades comunicadas. 

### <a name="create-a-new-project"></a>Criar um novo projeto

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

### <a name="add-your-registry-credentials"></a>Adicionar as credenciais do registo

O ficheiro de ambiente armazena as credenciais do seu registo de contentor e partilha-as com o runtime do IoT Edge. O runtime precisa destas credenciais para solicitar as imagens privadas para o dispositivo IoT Edge.

1. No explorador do VS Code, abra o ficheiro .env.
2. Atualize os campos com os valores **nome de utilizador** e **palavra-passe** que copiou do seu Azure Container Registry.
3. Guarde este ficheiro.

### <a name="select-your-target-architecture"></a>Selecione a sua arquitetura de destino

Atualmente, o Visual Studio Code pode desenvolver módulos de C para dispositivos AMD64 de Linux e ARM32v7 de Linux. Tem de selecionar qual arquitetura visa com cada solução, uma vez que o contentor é criado e executado de forma diferente para cada tipo de arquitetura. A predefinição é AMD64 de Linux. 

1. Abra a paleta de comandos e procure **Azure IoT Edge: Definir a plataforma de destino predefinido para solução de ponta**, ou selecione o ícone de atalho na barra do lado na parte inferior da janela. 

2. Na paleta de comandos, selecione a arquitetura de destino na lista de opções. Para este tutorial, estamos a utilizar uma máquina virtual do Ubuntu como o dispositivo do IoT Edge, para que irá manter a predefinição **amd64**. 

### <a name="update-the-module-with-custom-code"></a>Atualizar o módulo com o código personalizado

O código do módulo de predefinição recebe mensagens numa fila de entrada e transmite-los por meio de uma fila de saída. Vamos adicionar alguns códigos adicionais para que o módulo processa as mensagens na periferia antes do reencaminhamento-los para o IoT Hub. Atualize o módulo para que analisa os dados de temperatura em cada mensagem e só envia a mensagem para o IoT Hub, se a temperatura for superior a um determinado limiar. 

1. Os dados do sensor neste cenário estão no formato JSON. Para filtrar mensagens no formato JSON, importe uma biblioteca JSON para C. Este tutorial utiliza o Parson.

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

1. No ficheiro **main.c**, adicione uma variável global chamada `temperatureThreshold` depois da secção de inclusão. Esta variável define o valor que a temperatura medida deve exceder, para que os dados sejam enviados para o Hub IoT.

    ```c
    static double temperatureThreshold = 25;
    ```

1. Encontrar o `CreateMessageInstance` função no main. Substitua a instrução if-else interna com o seguinte código que adiciona algumas linhas de funcionalidade: 

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

1. Substitua toda a função `InputQueue1Callback` pelo código abaixo. Esta função implementa o filtro de mensagens real. Quando uma mensagem é recebida, ele verifica se a temperatura comunicada excede o limiar. Se Sim, em seguida, encaminha a mensagem por meio da sua fila de saída. Caso contrário, em seguida, ele ignora a mensagem. 

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

1. Encontrar o `SetupCallbacksForModule` função. Substitua a função com o seguinte código que adiciona uma **ou se** instrução para verificar se o módulo duplo foi atualizado.

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

1. Guarde o ficheiro Main.

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

## <a name="build-and-push-your-module"></a>Criar e enviar por push o seu módulo

Na secção anterior, criou uma solução de IoT Edge e adicionar código para o CModule que filtrará as mensagens onde a temperatura comunicada máquina está dentro dos limites aceitáveis. Agora, tem de criar a solução como uma imagem de contentor e enviá-la para o registo de contentor.

1. Abra o terminal VS Code, selecionando **View** > **Terminal**.

1. Inicie sessão no Docker, introduzindo o seguinte comando no terminal. Inicie sessão com o nome de utilizador, a palavra-passe e o servidor de início de sessão do seu registo de contentor do Azure. Pode obter estes valores a partir da **chaves de acesso** secção do seu registo no portal do Azure.
     
   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Poderá receber um aviso de segurança recomenda a utilização do `--password-stdin`. Embora essa prática recomendada é recomendada para cenários de produção, é fora do âmbito deste tutorial. Para obter mais informações, consulte a [início de sessão do docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) referência.

2. No explorador do VS Code, clique com o botão direito do rato no ficheiro **deployment.template.json** e selecione **Criar e Emitir solução do IoT Edge**.

   O comando de compilação e emissão inicia três operações. Primeiro, ele cria uma nova pasta na solução denominada **config** que contém os ficheiros de manifesto, criados fora das informações no modelo de implementação e outras soluções de implantação completa. Em segundo lugar, ele é executado `docker build` para criar a imagem de contentor com base no dockerfile apropriado para a sua arquitetura de destino. Em seguida, ele é executado `docker push` para enviar por push o repositório de imagens para o seu registo de contentor.

## <a name="deploy-modules-to-device"></a>Implementar módulos no dispositivo

Utilize o Explorador do Visual Studio Code e a extensão das ferramentas de IoT do Azure para implementar o projeto de módulo no seu dispositivo IoT Edge. Já tem um manifesto de implantação preparado para o seu cenário, o **deployment.json** ficheiro na pasta config. Agora tudo o que precisa de fazer é selecionar um dispositivo para receber a implementação.

Certifique-se de que o seu dispositivo IoT Edge está em execução. 

1. No Explorador do Visual Studio Code, expanda o **dispositivos do Azure IoT Hub** secção para ver a lista de dispositivos de IoT.

2. Carregue com o botão direito do rato no nome do seu dispositivo do IoT Edge e, em seguida, selecione **Criar Implementação para o Dispositivo Único**.

3. Selecione o ficheiro **deployment.json** na pasta **config** e, em seguida, clique em **Selecionar Manifesto de Implementação do Edge**. Não utilize o ficheiro deployment.template.json.

4. Clique no botão Atualizar. Deverá ver o novo **CModule** em execução, juntamente com o módulo **TempSensor**, bem como **$edgeAgent** e **$edgeHub**.

## <a name="view-generated-data"></a>Ver os dados gerados

Depois de aplicar o manifesto de implementação no seu dispositivo IoT Edge, o runtime do IoT Edge no dispositivo recolhe as novas informações de implementação e começa a ser executado no mesmo. Quaisquer módulos em execução no dispositivo que não estão incluídos no manifesto de implementação são parados. Quaisquer módulos em falta do dispositivo são iniciados.

Pode ver o estado do seu dispositivo do IoT Edge com a secção **Dispositivos do Hub IoT do Azure** do explorador do Visual Studio Code. Expanda os detalhes do seu dispositivo para ver uma lista de módulos implementados e em execução.

1. No Explorador do Visual Studio Code, clique com o botão direito no nome do seu dispositivo IoT Edge e selecione **iniciar mensagens D2C monitorização**.

2. Ver as mensagens que chegam ao IoT Hub. Pode demorar algum tempo para as mensagens chegam, porque o dispositivo do IoT Edge tem de receber a sua nova implementação e comece a todos os módulos. Em seguida, as alterações feitas no código CModule Aguarde até a temperatura de máquina atinge 25 graus antes de enviar mensagens. Ele também adiciona o tipo de mensagem **alerta** para todas as mensagens que atingir esse limite de temperatura. 

   ![Ver mensagens que chegam ao IoT Hub](./media/tutorial-c-module/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Editar o módulo duplo

Usamos o duplo do módulo de CModule no manifesto de implantação para definir o limiar de temperatura em graus 25. Pode utilizar o módulo duplo para alterar a funcionalidade sem ter de atualizar o código do módulo.

1. No Visual Studio Code, expanda os detalhes em seu dispositivo IoT Edge para ver os módulos em execução. 

2. Com o botão direito **CModule** e selecione **editar módulo duplo**. 

3. Encontrar **TemperatureThreshold** nas propriedades pretendidas. Altere-o para uma nova temperatura graus de 5 a 10 graus de mais do que a temperatura comunicada mais recente. 

4. Guarde o ficheiro de duplo do módulo.

5. Com o botão direito em qualquer lugar na edição de painel e selecione o módulo duplo **duplo do módulo de atualização**. 

5. Monitorize as mensagens do dispositivo para a nuvem de entrada. Deverá ver as mensagens parar até que seja atingido o limiar de temperatura de novo. 

## <a name="clean-up-resources"></a>Limpar recursos

Se planeia avançar para o próximo artigo recomendado, pode manter os recursos e as configurações que criou e reutilizá-los. Também pode continuar a utilizar o mesmo dispositivo IoT Edge como um dispositivo de teste.

Caso contrário, pode eliminar as configurações locais e os recursos do Azure que utilizou neste artigo para evitar encargos.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]


## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, criou uma função do módulo do IoT Edge que contém código para filtrar dados não processados gerados pelo seu dispositivo IoT Edge. Quando estiver pronto para criar seus próprios módulos, pode saber mais sobre [desenvolver seus próprios módulos do IoT Edge](module-development.md) ou como [desenvolver módulos com o Visual Studio Code](how-to-vs-code-develop-module.md). Pode continuar para os tutoriais seguintes para saber como Azure IoT Edge pode ajudá-lo a implementar serviços cloud do Azure para processar e analisar dados na periferia.

> [!div class="nextstepaction"]
> [As funções](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [serviço de visão personalizada](tutorial-deploy-custom-vision.md)

