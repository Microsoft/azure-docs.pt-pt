---
title: Tutorial desenvolver módulo C para Windows - Azure IoT Edge / Microsoft Docs
description: Este tutorial mostra-lhe como criar um módulo IoT Edge com código C e implantá-lo num dispositivo Windows que executa o IoT Edge
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 05/28/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: d9cffcadcb95b6c8c61205d458610f402fa7286d
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94964597"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-windows-devices"></a>Tutorial: Desenvolver um módulo C IoT Edge para dispositivos Windows

Utilize o Visual Studio para desenvolver o código C e implemente-o num dispositivo Windows que executa o Azure IoT Edge.

Pode utilizar os módulos do Azure IoT Edge para implementar código que aplica a sua lógica de negócio diretamente aos seus dispositivos IoT Edge. Este tutorial explica-lhe como criar e implementar um módulo do IoT Edge que filtra dados de sensores. Neste tutorial, vai aprender a:

> [!div class="checklist"]
>
> * Use o Visual Studio para criar um módulo IoT Edge baseado no C SDK.
> * Use o Visual Studio e o Docker para criar uma imagem docker e publicá-la no seu registo.
> * Implemente o módulo no seu dispositivo IoT Edge.
> * Veja os dados gerados.

O módulo do IoT Edge que criou neste tutorial filtra os dados de temperatura que são gerados pelo seu dispositivo. Envia apenas mensagens de origem caso a temperatura seja superior a um limiar especificado. Este tipo de análise no Edge é útil para reduzir a quantidade de dados que são comunicados e armazenados na cloud.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial demonstra como desenvolver um módulo em **C** utilizando **o Visual Studio 2019** e implantá-lo num **dispositivo Windows**. Se estiver a desenvolver módulos para dispositivos Linux, vá ao [Develop a C IoT Edge module para dispositivos Linux.](tutorial-c-module.md)

Utilize a seguinte tabela para compreender as suas opções de desenvolvimento e implantação de módulos C para dispositivos Windows:

| C | Visual Studio Code | Estúdio Visual 2017/2019 |
| -- | ------------------ | ------------------ |
| **Windows AMD64** |  | ![Desenvolver módulos C para WinAMD64 em Estúdio Visual](./media/tutorial-c-module/green-check.png) |

Antes de iniciar este tutorial, deveria ter passado pelo tutorial anterior para configurar o seu ambiente de desenvolvimento para o desenvolvimento de contentores Windows: [Desenvolver módulos IoT Edge para dispositivos Windows](tutorial-develop-for-windows.md). Depois de completar este tutorial, deverá ter os seguintes pré-requisitos no lugar:

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) no escalão gratuito ou standard no Azure.
* Um [dispositivo Windows em execução Azure IoT Edge](quickstart.md).
* Um registo de contentores, como [o Registo de Contentores de Azure.](../container-registry/index.yml)
* [Visual Studio 2019](/visualstudio/install/install-visual-studio) configurado com a extensão [Azure IoT Edge Tools.](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools)
* [Docker Desktop](https://docs.docker.com/docker-for-windows/install/) configurado para executar recipientes Windows.
* Instale o Azure IoT C SDK para windows x64 através de vcpkg:

   ```powershell
   git clone https://github.com/Microsoft/vcpkg
   cd vcpkg
   .\bootstrap-vcpkg.bat
   .\vcpkg install azure-iot-sdk-c:x64-windows
   .\vcpkg --triplet x64-windows integrate install
   ```

> [!TIP]
> Se estiver a utilizar o Visual Studio 2017 (versão 15.7 ou superior), por favor descarregue e instale [ferramentas de borda Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) para VS 2017 a partir do mercado do Estúdio Visual

## <a name="create-a-module-project"></a>Criar um projeto de módulo

Os passos seguintes criam um projeto de módulo IoT Edge baseado no C SDK utilizando o Visual Studio e a extensão Azure IoT Edge Tools. Assim que tiver um modelo de projeto criado, adicione um novo código para que o módulo filtra as mensagens com base nas suas propriedades relatadas.

### <a name="create-a-new-project"></a>Criar um novo projeto

Crie um modelo de solução C que pode personalizar com o seu próprio código.

1. Lançar Visual Studio 2019 e selecionar **Criar Novo Projeto.**

2. Procure **por IoT Edge** e escolha o projeto **Azure IoT Edge (Windows amd64).** Clique em **Seguinte**.

   ![Criar um novo projeto Azure IoT Edge](./media/tutorial-c-module-windows/new-project.png)

3. Mude o nome do projeto e da solução para algo descritivo como **o CTutorialApp.** Clique em **Criar** para criar o projeto.

   ![Configure um novo projeto Azure IoT Edge](./media/tutorial-c-module-windows/configure-project.png)

4. Configure o seu projeto com os seguintes valores:

   | Campo | Valor |
   | ----- | ----- |
   | Selecione um modelo | Selecione **O Módulo C**. |
   | Nome do projeto do módulo | Dê o nome **CModule** ao módulo. |
   | Repositório de imagem de estivador | Os repositórios de imagens incluem o nome do seu registo de contentor e o nome da sua imagem de contentor. A sua imagem de recipiente é pré-apultada do valor do nome do projeto do módulo. Substitua **o local:5000** pelo valor do **servidor login** do registo do seu contentor Azure. Pode recuperar o servidor 'Iniciar sessão' a partir da página de visão geral do seu registo de contentores no portal Azure. <br><br> O repositório de imagens final parece \<registry name\> .azurecr.io/cmodule. |

   ![Configure o seu projeto para dispositivo alvo, tipo de módulo e registo de contentores](./media/tutorial-c-module-windows/add-application-and-module.png)

5. **Selecione Adicionar** para criar o projeto.

### <a name="add-your-registry-credentials"></a>Adicionar as credenciais do registo

O manifesto de implantação partilha as credenciais para o seu registo de contentores com o tempo de execução IoT Edge. O runtime precisa destas credenciais para solicitar as imagens privadas para o dispositivo IoT Edge. Utilize as credenciais da secção de **chaves de acesso** do seu registo de contentores Azure.

1. No explorador de soluções Visual Studio, abra a **deployment.template.jsem** arquivo.

2. Encontre a **propriedade registroscredentais** nas propriedades $edgeAgent desejadas. Deverá ter o seu endereço de registo preenchido automaticamente a partir das informações que forneceu ao criar o projeto. Os campos de nome de utilizador e palavra-passe devem conter nomes variáveis. Por exemplo:

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "$CONTAINER_REGISTRY_USERNAME_<registry name>",
       "password": "$CONTAINER_REGISTRY_PASSWORD_<registry name>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

3. Abra o ficheiro **.env** na solução do módulo. (Está escondido por padrão no Solution Explorer, pelo que poderá ser necessário selecionar o botão **Mostrar Todos os Ficheiros** para o exibir.) O ficheiro .env deve conter o mesmo nome de utilizador e variáveis de palavra-passe que viu no deployment.template.jsficheiro.

4. Adicione o **nome de utilizador** e os valores de **palavra-passe** do seu registo de contentores Azure.

5. Guarde as alterações no ficheiro .env.

### <a name="update-the-module-with-custom-code"></a>Atualizar o módulo com o código personalizado

O código do módulo predefinido recebe mensagens numa fila de entrada e transmite-as através de uma fila de saída. Vamos adicionar algum código adicional para que o módulo processe as mensagens na borda antes de encaminhá-las para o IoT Hub. Atualize o módulo de modo a analisar os dados de temperatura em cada mensagem e só envie a mensagem para o IoT Hub se a temperatura exceder um determinado limiar.

1. Os dados do sensor neste cenário estão no formato JSON. Para filtrar mensagens no formato JSON, importe uma biblioteca JSON para C. Este tutorial utiliza o Parson.

   1. Descarregue o [repositório Do Pároco GitHub.](https://github.com/kgabis/parson) Copie os ficheiros **parson.c** e **pároco.h** no projeto **CModule.**

   2. No Visual Studio, abra o ficheiro **CMakeLists.txt** da pasta do projeto CModule. Na parte superior do ficheiro, importe os ficheiros do Parson como uma biblioteca chamada **my_parson**.

      ```txt
      add_library(my_parson
          parson.c
          parson.h
      )
      ```

   3. Adicione `my_parson` à lista de bibliotecas na secção **target_link_libraries** do ficheiro CMakeLists.txt.

   4. Guarde o ficheiro **CMakeLists.txt**.

   5. Abra **o CModule**  >  **principal.c**. Na parte inferior da lista de declarações incluem-se, adicione uma nova para incluir `parson.h` para o suporte JSON:

      ```c
      #include "parson.h"
      ```

2. No ficheiro **.c principal,** adicione uma variável global chamada `temperatureThreshold` ao lado da variável MessagesReceivedByInput1Queue. Esta variável define o valor que a temperatura medida deve exceder, para que os dados sejam enviados para o Hub IoT.

    ```c
    static double temperatureThreshold = 25;
    ```

3. Encontre a `CreateMessageInstance` função na .c principal. Substitua a declaração interna por outro código que adiciona algumas linhas de funcionalidade:

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

   As novas linhas de código na outra declaração adicionam uma nova propriedade à mensagem, que rotula a mensagem como um alerta. Este código rotula todas as mensagens como alertas, porque adicionamos funcionalidade que só envia mensagens para o IoT Hub se reportarem temperaturas elevadas.

4. Encontre a `InputQueue1Callback` função e substitua toda a função pelo seguinte código. Esta função implementa o filtro de mensagens real. Quando uma mensagem é recebida, verifica se a temperatura reportada excede o limiar. Se sim, então encaminha a mensagem através da sua fila de saída. Se não, então ignora a mensagem.

    ```c
    static unsigned char *bytearray_to_str(const unsigned char *buffer, size_t len)
    {
        unsigned char *ret = (unsigned char *)malloc(len + 1);
        memcpy(ret, buffer, len);
        ret[len] = '\0';
        return ret;
    }

    static IOTHUBMESSAGE_DISPOSITION_RESULT InputQueue1Callback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
    {
        IOTHUBMESSAGE_DISPOSITION_RESULT result;
        IOTHUB_CLIENT_RESULT clientResult;
        IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle = (IOTHUB_MODULE_CLIENT_LL_HANDLE)userContextCallback;

        unsigned const char* messageBody;
        size_t contentSize;

        if (IoTHubMessage_GetByteArray(message, &messageBody, &contentSize) == IOTHUB_MESSAGE_OK)
        {
            messageBody = bytearray_to_str(messageBody, contentSize);
        } else
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

6. Encontre a `SetupCallbacksForModule` função. Substitua a função pelo seguinte código que adiciona **mais** uma se a declaração verificar se o módulo twin foi atualizado.

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

7. Guarde o ficheiro main.c.

8. Abra a **deployment.template.jsarquivada.**

9. Adicione o módulo duplo CModule ao manifesto da implementação. Insira o seguinte conteúdo JSON na parte inferior da secção `moduleContent`, após o módulo duplo `$edgeHub`:

   ```json
   "CModule": {
       "properties.desired":{
           "TemperatureThreshold":25
       }
   }
   ```

   ![Adicionar o CModule duplo ao modelo de implementação](./media/tutorial-c-module-windows/module-twin.png)

10. Guarde o ficheiro **deployment.template.json**.

## <a name="build-and-push-your-module"></a>Construa e empurre o seu módulo

Na secção anterior, criou uma solução IoT Edge e adicionou código ao **CModule** para filtrar mensagens onde a temperatura da máquina reportada está abaixo do limiar aceitável. Agora, tem de criar a solução como uma imagem de contentor e enviá-la para o registo de contentor.

### <a name="sign-in-to-docker"></a>Inscreva-se no Docker

Forneça as suas credenciais de registo de contentores ao Docker na sua máquina de desenvolvimento para que possa empurrar a imagem do seu recipiente para ser armazenado no registo.

1. Abrir PowerShell ou um pedido de comando.

2. Inscreva-se no Docker com as credenciais de registo do contentor Azure que guardou depois de criar o registo.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Pode receber um aviso de segurança recomendando a utilização de `--password-stdin` . Embora essa melhor prática seja recomendada para cenários de produção, está fora do âmbito deste tutorial. Para mais informações, consulte a referência de login do [estivador.](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin)

### <a name="build-and-push"></a>Construir e empurrar

A sua máquina de desenvolvimento tem agora acesso ao seu registo de contentores, e os seus dispositivos IoT Edge também. É hora de transformar o código do projeto numa imagem de contentor.

1. No explorador de soluções Visual Studio, clique com o botão direito no nome do projeto que pretende construir. O nome padrão é **AzureIotEdgeApp1**. Para este tutorial, foi escolhido o nome **CTutorialApp.** Uma vez que está a construir um módulo Windows, a extensão deve ser **Windows.Amd64**.

2. Selecione **Módulos de Borda IoT** de construção e push .

   O comando de construção e pressão inicia três operações. Em primeiro lugar, cria uma nova pasta na solução chamada **config** que detém o manifesto de implantação completo, construído a partir de informações no modelo de implementação e outros ficheiros de solução. Em segundo lugar, funciona `docker build` para construir a imagem do contentor com base no arquivo apropriado para a arquitetura do seu alvo. Em seguida, corre `docker push` para empurrar o repositório de imagem para o seu registo de contentores.

   Este processo pode demorar vários minutos na primeira vez, mas é mais rápido da próxima vez que executar os comandos.

## <a name="deploy-modules-to-device"></a>Implementar módulos para dispositivo

Utilize o Visual Studio Cloud Explorer e a extensão Azure IoT Edge Tools para implementar o projeto do módulo no seu dispositivo IoT Edge. Já tem um manifesto de implantação preparado para o seu cenário, o **deployment.windows-amd64.jsficheiro na** pasta config. Agora tudo o que precisa de fazer é selecionar um dispositivo para receber a implementação.


Certifique-se de que o seu dispositivo IoT Edge está a funcionar.

1. No Visual Studio Cloud Explorer, expanda os recursos para ver a sua lista de dispositivos IoT.

2. Clique com o botão direito no nome do dispositivo IoT Edge que deseja receber a implementação.

3. Selecione **Criar Implementação**.

4. No explorador de ficheiros, selecione o ficheiro **deployment.windows-amd64** na pasta config da sua solução.

5. Refresh Cloud Explorer para ver os módulos implantados listados no seu dispositivo.

## <a name="view-generated-data"></a>Ver os dados gerados

Depois de aplicar o manifesto de implementação no seu dispositivo IoT Edge, o runtime do IoT Edge no dispositivo recolhe as novas informações de implementação e começa a ser executado no mesmo. Quaisquer módulos em execução no dispositivo que não estão incluídos no manifesto de implementação são parados. Quaisquer módulos em falta do dispositivo são iniciados.

Pode utilizar a extensão IoT Edge Tools para visualizar as mensagens à medida que chegam ao seu Hub IoT.

1. No Visual Studio Cloud Explorer, selecione o nome do seu dispositivo IoT Edge.

2. Na lista **de Ações,** selecione **Start Monitoring Built-in Event Endpoint**.

3. Veja as mensagens que chegam ao seu Hub IoT. Pode demorar algum tempo até que as mensagens cheguem, porque o dispositivo IoT Edge tem de receber a sua nova implementação e iniciar todos os módulos. Em seguida, as alterações que fizemos ao código CModule aguardem até que a temperatura da máquina atinja os 25 graus antes de enviar mensagens. Adiciona também o tipo de mensagem **Alerta** a todas as mensagens que atinjam esse limiar de temperatura.

   ![Ver mensagens que chegam ao IoT Hub](./media/tutorial-c-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Editar o módulo twin

Usamos o módulo CModule twin para definir o limiar de temperatura em 25 graus. Pode utilizar o módulo twin para alterar a funcionalidade sem ter de atualizar o código do módulo.

1. No Visual Studio, abra a **deployment.windows-amd64.jsem** arquivo. (Não o ficheiro de implementação.modelo. Se não vir o manifesto de implantação no ficheiro config no explorador de solução, selecione o ícone **mostrar todos os ficheiros** na barra de ferramentas do explorador.)

2. Encontre o gémeo CModule e altere o valor do parâmetro **de temperaturaStenha** um novo ponto de temperatura de 5 a 10 graus mais alto do que a última temperatura reportada.

3. Guarde o **deployment.windows-amd64.jsarquivado.**

4. Siga novamente os passos de implantação para aplicar o manifesto de implantação atualizado no seu dispositivo.

5. Monitorize as mensagens de entrada de dispositivo para nuvem. Deve ver as mensagens paradas até que o novo limiar de temperatura seja atingido.

## <a name="clean-up-resources"></a>Limpar os recursos

Se planeia avançar para o próximo artigo recomendado, pode manter os recursos e as configurações que criou e reutilizá-los. Também pode continuar a utilizar o mesmo dispositivo IoT Edge como um dispositivo de teste.

Caso contrário, pode eliminar as configurações locais e os recursos Azure que utilizou neste artigo para evitar encargos.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou uma função do módulo do IoT Edge com código para filtrar os dados não processados que são gerados pelo seu dispositivo IoT Edge.

Pode continuar nos próximos tutoriais para saber como o Azure IoT Edge pode ajudá-lo a implementar serviços em nuvem Azure para processar e analisar dados no limite.

> [!div class="nextstepaction"]
> [Funções](tutorial-deploy-function.md) 
>  [Stream Analytics](tutorial-deploy-stream-analytics.md) 
>  [Aprendizagem automática](tutorial-deploy-machine-learning.md) 
>  [Serviço de Visão Personalizada](tutorial-deploy-custom-vision.md)