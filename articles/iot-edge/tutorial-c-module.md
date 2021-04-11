---
title: Tutorial desenvolver módulo C para Linux - Azure IoT Edge | Microsoft Docs
description: Este tutorial mostra-lhe como criar um módulo IoT Edge com código C e implantá-lo num dispositivo Linux que executa ioT Edge
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/30/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 3778361d066543325672f37cf163e58329f04abe
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2021
ms.locfileid: "106221573"
---
# <a name="tutorial-develop-a-c-iot-edge-module-using-linux-containers"></a>Tutorial: Desenvolver um módulo C IoT Edge utilizando recipientes Linux

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Utilize o Código do Estúdio Visual para desenvolver o código C e implemente-o num dispositivo em execução Azure IoT Edge.

Pode utilizar os módulos do IoT Edge para implementar código que aplica a sua lógica de negócio diretamente nos seus dispositivos IoT Edge. Este tutorial explica-lhe como criar e implementar um módulo do IoT Edge que filtra dados de sensores. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Utilizar o Visual Studio Code para criar um módulo do IoT Edge em C
> * Utilizar o Visual Studio Code e o Docker para criar uma imagem do Docker e publicá-la num registo de contentor
> * Implementar o módulo no seu dispositivo IoT Edge
> * Ver os dados gerados

O módulo do IoT Edge que criou neste tutorial filtra os dados de temperatura gerados pelo seu dispositivo. Envia apenas mensagens de origem caso a temperatura seja superior a um limiar especificado. Este tipo de análise no Edge é útil para reduzir a quantidade de dados comunicados e armazenados na cloud.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial demonstra como desenvolver um módulo em **C** usando **o Código do Estúdio Visual,** e como implantá-lo num dispositivo IoT Edge. Se estiver a desenvolver módulos utilizando recipientes Windows, vá para [desenvolver um módulo C IoT Edge utilizando recipientes Windows.](tutorial-c-module-windows.md)

Utilize a seguinte tabela para compreender as suas opções de desenvolvimento e implantação de módulos C utilizando recipientes Linux:

| C | Visual Studio Code | Visual Studio |
| - | ------------------ | ------------- |
| **Linux AMD64** | ![Utilize o Código VS para módulos C no Linux AMD64](./media/tutorial-c-module/green-check.png) | ![Utilizar VS para módulos C no Linux AMD64](./media/tutorial-c-module/green-check.png) |
| **Linux ARM32** | ![Utilize o Código VS para módulos C no Linux ARM32](./media/tutorial-c-module/green-check.png) | ![Utilizar VS para módulos C no Linux ARM32](./media/tutorial-c-module/green-check.png) |

Antes de iniciar este tutorial, deveria ter passado pelo tutorial anterior para configurar o seu ambiente de desenvolvimento para o desenvolvimento de recipientes Linux: [Desenvolver módulos IoT Edge utilizando recipientes Linux](tutorial-develop-for-linux.md). Ao completar este tutorial, deverá ter os seguintes pré-requisitos no lugar:

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) no escalão gratuito ou standard no Azure.
* Um dispositivo que funciona Azure IoT Edge com recipientes Linux. Pode utilizar os arranques rápidos para configurar um [dispositivo Linux](quickstart-linux.md) ou [dispositivo Windows](quickstart.md).
* Um registo de contentores, como [o Registo de Contentores de Azure.](../container-registry/index.yml)
* [Código de Estúdio Visual](https://code.visualstudio.com/) configurado com as [Ferramentas Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) configurado para executar contentores Linux.

Para desenvolver um módulo IoT Edge em C, instale os seguintes pré-requisitos adicionais na sua máquina de desenvolvimento:

* [Extensão C/C++](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools) para o Visual Studio Code.

A instalação do Azure IoT C SDK não é necessária para este tutorial, mas pode fornecer funcionalidades úteis como definições de intellisense e programa de leitura. Para obter informações de instalação, consulte [Azure IoT C SDKs e Bibliotecas.](https://github.com/Azure/azure-iot-sdk-c)

## <a name="create-a-module-project"></a>Criar um projeto de módulo

Os passos seguintes criam um projeto de módulo IoT Edge para C utilizando o Código de Estúdio Visual e a extensão Azure IoT Tools. Assim que tiver um modelo de projeto criado, adicione um novo código para que o módulo filtra as mensagens com base nas suas propriedades relatadas.

### <a name="create-a-new-project"></a>Criar um novo projeto

Crie um modelo de solução C que pode personalizar com o seu próprio código.

1. Selecione **Ver**  >  **Paleta de Comando** para abrir a paleta de comandos VS Code.

2. Na paleta de comandos, escreva e execute o comando **Azure: Iniciar sessão** e siga as instruções para iniciar sessão na sua conta do Azure. Se já iniciou sessão, pode ignorar este passo.

3. Na paleta de comandos, escreva e execute o comando **Azure IoT Edge: Nova solução do IoT Edge**. Siga as instruções na paleta de comandos para criar a sua solução.

   | Campo | Valor |
   | ----- | ----- |
   | Selecionar pasta | Escolha a localização no computador de desenvolvimento na qual o VS Code vai criar os ficheiros da solução. |
   | Indicar um nome para a solução | Introduza um nome descritivo para a sua solução ou aceite a **solução edges padrão**. |
   | Selecionar modelo de módulo | Escolha **o módulo C**. |
   | Indicar um nome para o módulo | Dê o nome **CModule** ao módulo. |
   | Indicar o repositório de imagens do Docker para o módulo | Os repositórios de imagens incluem o nome do seu registo de contentor e o nome da sua imagem de contentor. A sua imagem de contentor está pré-apovoada do nome que forneceu no último passo. Substitua **o local:5000** pelo valor do **servidor login** do registo do seu contentor Azure. Pode recuperar o servidor 'Iniciar sessão' a partir da página de visão geral do seu registo de contentores no portal Azure. <br><br> O repositório de imagens final parece \<registry name\> .azurecr.io/cmodule. |

   ![Fornecer repositório de imagens do Docker](./media/tutorial-c-module/repository.png)

### <a name="add-your-registry-credentials"></a>Adicionar as credenciais do registo

O ficheiro de ambiente armazena as credenciais do seu registo de contentor e partilha-as com o runtime do IoT Edge. O runtime precisa destas credenciais para solicitar as imagens privadas para o dispositivo IoT Edge.

A extensão IoT Edge tenta retirar as credenciais de registo do seu contentor do Azure e povoá-las no ficheiro ambiente. Verifique se as suas credenciais já estão incluídas. Caso contrário, adicione-os agora:

1. No explorador do VS Code, abra o ficheiro .env.
2. Atualize os campos com os valores **nome de utilizador** e **palavra-passe** que copiou do seu Azure Container Registry.
3. Guarde este ficheiro.

### <a name="select-your-target-architecture"></a>Selecione a sua arquitetura-alvo

Atualmente, o Visual Studio Code pode desenvolver módulos C para dispositivos Linux AMD64 e Linux ARM32v7. Você precisa selecionar que arquitetura você está dirigindo com cada solução, porque o recipiente é construído e executado de forma diferente para cada tipo de arquitetura. O padrão é Linux AMD64.

1. Abra a paleta de comando e procure **por Azure IoT Edge: Definir Plataforma-alvo padrão para solução de borda**, ou selecione o ícone de atalho na barra lateral na parte inferior da janela.

2. Na paleta de comando, selecione a arquitetura-alvo da lista de opções. Para este tutorial, estamos a usar uma máquina virtual Ubuntu como dispositivo IoT Edge, por isso manteremos o **amd64** padrão.

### <a name="update-the-module-with-custom-code"></a>Atualizar o módulo com o código personalizado

O código do módulo predefinido recebe mensagens numa fila de entrada e transmite-as através de uma fila de saída. Vamos adicionar algum código adicional para que o módulo processe as mensagens na borda antes de encaminhá-las para o IoT Hub. Atualize o módulo de modo a analisar os dados de temperatura em cada mensagem e só envie a mensagem para o IoT Hub se a temperatura exceder um determinado limiar.

1. Os dados do sensor neste cenário estão no formato JSON. Para filtrar mensagens no formato JSON, importe uma biblioteca JSON para C. Este tutorial utiliza o Parson.

   1. Descarregue o [repositório Do Pároco GitHub.](https://github.com/kgabis/parson) Copie os ficheiros **parson.c** e **parson.h** para a pasta **CModule**.

   2. Abra **os módulos**  >  **CModule**  >  **CMakeLists.txt**. Na parte superior do ficheiro, importe os ficheiros do Parson como uma biblioteca chamada **my_parson**.

      ```txt
      add_library(my_parson
          parson.c
          parson.h
      )
      ```

   3. Adicione `my_parson` à lista de bibliotecas na **função target_link_libraries** de CMakeLists.txt.

   4. Guarde o ficheiro **CMakeLists.txt**.

   5. Abra **os módulos**  >  **CModule**  >  **principal.c**. Na parte inferior da lista de declarações incluem-se, adicione uma nova para incluir `parson.h` para o suporte JSON:

      ```c
      #include "parson.h"
      ```

1. No ficheiro **main.c**, adicione uma variável global chamada `temperatureThreshold` depois da secção de inclusão. Esta variável define o valor que a temperatura medida deve exceder, para que os dados sejam enviados para o Hub IoT.

    ```c
    static double temperatureThreshold = 25;
    ```

1. Encontre a `CreateMessageInstance` função na .c principal. Substitua a declaração interna por outro código que adiciona algumas linhas de funcionalidade:

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

1. Substitua toda a função `InputQueue1Callback` pelo código abaixo. Esta função implementa o filtro de mensagens real. Quando uma mensagem é recebida, verifica se a temperatura reportada excede o limiar. Se sim, então encaminha a mensagem através da sua fila de saída. Se não, então ignora a mensagem.

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

1. Encontre a `SetupCallbacksForModule` função. Substitua a função pelo seguinte código que adiciona **mais** uma se a declaração verificar se o módulo twin foi atualizado.

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

1. Guarde o ficheiro main.c.

1. No explorador de código VS, abra o **deployment.template.jsno** ficheiro no seu espaço de trabalho de solução IoT Edge.

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

## <a name="build-and-push-your-module"></a>Construa e empurre o seu módulo

Na secção anterior, criou uma solução IoT Edge e adicionou código ao CModule que filtrará as mensagens onde a temperatura da máquina reportada está dentro dos limites aceitáveis. Agora, tem de criar a solução como uma imagem de contentor e enviá-la para o registo de contentor.

1. Abra o terminal de código VS selecionando o Terminal **de Visualização**  >  .

2. Inscreva-se no Docker introduzindo o seguinte comando no terminal. Inicie sessão com o nome de utilizador, palavra-passe e servidor de login do seu registo de contentores Azure. Pode recuperar estes valores a partir da secção de **teclas de acesso** do seu registo no portal Azure.

   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Pode receber um aviso de segurança recomendando a utilização de `--password-stdin` . Embora essa melhor prática seja recomendada para cenários de produção, está fora do âmbito deste tutorial. Para mais informações, consulte a referência de login do [estivador.](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin)

3. No explorador de código VS, clique com o botão direito **no ficheirodeployment.template.js** e selecione a **Solução de Borda De Construção e Pressão IoT**.

   O comando de construção e pressão inicia três operações. Em primeiro lugar, cria uma nova pasta na solução chamada **config** que detém o manifesto de implantação completo, construído a partir de informações no modelo de implementação e outros ficheiros de solução. Em segundo lugar, funciona `docker build` para construir a imagem do contentor com base no arquivo apropriado para a arquitetura do seu alvo. Em seguida, corre `docker push` para empurrar o repositório de imagem para o seu registo de contentores.

   Este processo pode demorar vários minutos na primeira vez, mas é mais rápido da próxima vez que executar os comandos.

## <a name="deploy-modules-to-device"></a>Implementar módulos para dispositivo

Utilize o explorador visual Studio Code e a extensão Azure IoT Tools para implantar o projeto do módulo no seu dispositivo IoT Edge. Já tem um manifesto de implantação preparado para o seu cenário, o **deployment.amd64.jsficheiro na** pasta config. Agora tudo o que precisa de fazer é selecionar um dispositivo para receber a implementação.

Certifique-se de que o seu dispositivo IoT Edge está a funcionar.

1. No explorador visual Studio Code, na secção **Azure IoT Hub,** expanda **os Dispositivos** para ver a sua lista de dispositivos IoT.

2. Carregue com o botão direito do rato no nome do seu dispositivo do IoT Edge e, em seguida, selecione **Criar Implementação para o Dispositivo Único**.

3. Selecione a **deployment.amd64.jsno** ficheiro na pasta **config** e, em seguida, clique em **Select Edge Deployment Manifest**. Não utilize o ficheiro deployment.template.json.

4. Sob o seu dispositivo, expanda **os Módulos** para ver uma lista de módulos implantados e em funcionamento. Clique no botão Atualizar. Deverá ver o novo **CModule** a funcionar juntamente com o módulo **SimuladoTemperatureSensor** e o **$edgeAgent** e **$edgeHub**.

    Pode levar alguns minutos para os módulos começarem. O tempo de execução IoT Edge precisa de receber o seu novo manifesto de implantação, retirar as imagens do módulo do tempo de funcionamento do contentor e, em seguida, iniciar cada novo módulo.

## <a name="view-generated-data"></a>Ver os dados gerados

Depois de aplicar o manifesto de implementação no seu dispositivo IoT Edge, o runtime do IoT Edge no dispositivo recolhe as novas informações de implementação e começa a ser executado no mesmo. Quaisquer módulos em execução no dispositivo que não estão incluídos no manifesto de implementação são parados. Quaisquer módulos em falta do dispositivo são iniciados.

1. No explorador visual Studio Code, clique com o botão direito no nome do seu dispositivo IoT Edge e selecione **Start Monitoring Built-in Event Endpoint**.

2. Veja as mensagens que chegam ao seu Hub IoT. Pode demorar algum tempo até que as mensagens cheguem, porque o dispositivo IoT Edge tem de receber a sua nova implementação e iniciar todos os módulos. Em seguida, as alterações que fizemos ao código CModule aguardem até que a temperatura da máquina atinja os 25 graus antes de enviar mensagens. Adiciona também o tipo de mensagem **Alerta** a todas as mensagens que atinjam esse limiar de temperatura.

   ![Ver mensagens que chegam ao IoT Hub](./media/tutorial-c-module/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Editar o módulo twin

Usamos o módulo CModule twin no manifesto de implantação para definir o limiar de temperatura em 25 graus. Pode utilizar o módulo twin para alterar a funcionalidade sem ter de atualizar o código do módulo.

1. No Código do Estúdio Visual, expanda os detalhes no seu dispositivo IoT Edge para ver os módulos de execução.

2. Clique com o botão direito **CModule** e **selecione Editar módulo twin**.

3. Encontre **temperaturaSRestém nas** propriedades desejadas. Mude o seu valor para uma nova temperatura de 5 graus a 10 graus mais alto do que a última temperatura reportada.

4. Guarde o ficheiro twin do módulo.

5. Clique com o botão direito em qualquer lugar do painel de edição twin do módulo e selecione **Update module twin**.

6. Monitorize as mensagens de entrada de dispositivo para nuvem. Deve ver as mensagens paradas até que o novo limiar de temperatura seja atingido.

## <a name="clean-up-resources"></a>Limpar os recursos

Se planeia avançar para o próximo artigo recomendado, pode manter os recursos e as configurações que criou e reutilizá-los. Também pode continuar a utilizar o mesmo dispositivo IoT Edge como um dispositivo de teste.

Caso contrário, pode eliminar as configurações locais e os recursos Azure que utilizou neste artigo para evitar encargos.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou uma função do módulo do IoT Edge que contém código para filtrar dados não processados gerados pelo seu dispositivo IoT Edge.

Pode continuar nos próximos tutoriais para saber como o Azure IoT Edge pode ajudá-lo a implementar serviços em nuvem Azure para processar e analisar dados no limite.

> [!div class="nextstepaction"]
> [Funções](tutorial-deploy-function.md) 
>  [Stream Analytics](tutorial-deploy-stream-analytics.md) 
>  [Aprendizagem automática](tutorial-deploy-machine-learning.md) 
>  [Serviço de Visão Personalizada](tutorial-deploy-custom-vision.md)