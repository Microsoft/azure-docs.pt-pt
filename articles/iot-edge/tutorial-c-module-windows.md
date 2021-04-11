---
title: Tutorial - Desenvolver módulos C para Windows utilizando a borda IoT do Azure
description: Este tutorial mostra-lhe como criar módulos IoT Edge com código C e implantá-los em dispositivos Windows que estão a executar IoT Edge.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 05/28/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
monikerRange: =iotedge-2018-06
ms.openlocfilehash: a67bf92dbee9bf3d444add3be32544e4c3eba4f3
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2021
ms.locfileid: "106221607"
---
# <a name="tutorial-develop-c-iot-edge-modules-using-windows-containers"></a>Tutorial: Desenvolver módulos C IoT Edge utilizando recipientes Windows

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Este artigo mostra-lhe como usar o Visual Studio para desenvolver código C e implantá-lo num dispositivo Windows que está a executar Azure IoT Edge.

>[!NOTE]
>IoT Edge 1.1 LTS é o último canal de lançamento que suporta recipientes Windows. A partir da versão 1.2, os recipientes windows não são suportados. Considere usar ou mover-se para [IoT Edge para o Linux no Windows](iot-edge-for-linux-on-windows.md) para executar IoT Edge em dispositivos Windows.

Pode utilizar módulos Azure IoT Edge para implementar código que implementa a sua lógica de negócio diretamente nos seus dispositivos IoT Edge. Este tutorial explica-lhe como criar e implementar um módulo do IoT Edge que filtra dados de sensores.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Use o Visual Studio para criar um módulo IoT Edge baseado no C SDK.
> * Use o Visual Studio e o Docker para criar uma imagem docker e publicá-la no seu registo.
> * Implemente o módulo no seu dispositivo IoT Edge.
> * Veja os dados gerados.

O módulo do IoT Edge que criou neste tutorial filtra os dados de temperatura que são gerados pelo seu dispositivo. O módulo envia mensagens a montante apenas se a temperatura exceder um limiar especificado. Este tipo de análise no Edge é útil para reduzir a quantidade de dados que são comunicados e armazenados na cloud.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial demonstra como desenvolver um módulo em C utilizando o Visual Studio 2019 e, em seguida, implantá-lo num dispositivo Windows. Se estiver a desenvolver módulos utilizando recipientes Linux, vá ao [Develop C IoT Edge módulos utilizando recipientes Linux.](tutorial-csharp-module.md)

Para compreender as suas opções de desenvolvimento e implantação de módulos C utilizando recipientes Windows, consulte a seguinte tabela:

| C | Código do Estúdio Visual &nbsp; &nbsp; | Estúdio Visual 2017 &nbsp; e &nbsp; 2019 |
| -- | ------------------ | :------------------: |
| Windows AMD64 |  | ![Desenvolver módulos C para WinAMD64 em Estúdio Visual](./media/tutorial-c-module/green-check.png) |

Antes de iniciar este tutorial, crie o seu ambiente de desenvolvimento seguindo as instruções nos [módulos Develop IoT Edge utilizando](tutorial-develop-for-windows.md) o tutorial de recipientes Windows. Depois de o completar, o seu ambiente conterá os seguintes pré-requisitos:

* Um [hub IoT](../iot-hub/iot-hub-create-through-portal.md) gratuito ou de nível padrão em Azure.
* Um [dispositivo Windows que está a executar a Azure IoT Edge](how-to-install-iot-edge-windows-on-windows.md).
* Um registo de contentores, como [o Registo do Contentor de Azure.](../container-registry/index.yml)
* [Visual Studio 2019,](/visualstudio/install/install-visual-studio)configurado com a extensão [Azure IoT Edge Tools.](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools)
* [Docker Desktop](https://docs.docker.com/docker-for-windows/install/), configurado para executar recipientes Windows.

Instale o Azure IoT C SDK para windows x64 através de vcpkg executando os seguintes comandos:

   ```powershell
   git clone https://github.com/Microsoft/vcpkg
   cd vcpkg
   .\bootstrap-vcpkg.bat
   .\vcpkg install azure-iot-sdk-c:x64-windows
   .\vcpkg --triplet x64-windows integrate install
   ```

> [!TIP]
> Se estiver a utilizar o Visual Studio 2017 (versão 15.7 ou posterior), descarregue e instale Azure IoT Edge Tools para Visual Studio 2017 do [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools).

## <a name="create-a-module-project"></a>Criar um projeto de módulo

Nesta secção, você cria um projeto de módulo IoT Edge baseado no C SDK usando o Visual Studio e a extensão Azure IoT Edge Tools. Depois de criar um modelo de projeto, irá adicionar um novo código para que o módulo filtra as mensagens com base nas suas propriedades relatadas.

### <a name="create-a-new-project"></a>Criar um novo projeto

Crie um modelo de solução C que pode personalizar com o seu próprio código.

1. Open Visual Studio 2019 e, em seguida, **selecione Create New Project**.

1. No painel **de projetos Criar um novo painel** de projetos, procure **o IoT Edge** e, em seguida, na lista de resultados, selecione o projeto **Azure IoT Edge (Windows amd64).**

   ![Screenshot do painel IoT Edge "Criar um novo projeto".](./media/tutorial-c-module-windows/new-project.png)

1. Selecione **Seguinte**.

    O **Configure o seu novo painel** de projeto abre.

   ![Screenshot do painel "Configurar o seu novo projeto".](./media/tutorial-c-module-windows/configure-project.png)

1. No **Configure** o seu novo painel de projeto, mude o nome do projeto e solução para algo mais descritivo, como **o CTutorialApp.** 

1. Selecione **Create** (Criar) para criar o projeto.

   O painel do Módulo de **Adição** abre-se.

   ![Screenshot do painel "Adicionar Módulo" para configurar o seu projeto.](./media/tutorial-c-module-windows/add-application-and-module.png)

1. Na **página Configure a sua nova** página de projeto, faça o seguinte:

   a. No painel esquerdo, selecione o modelo **do módulo C.**  
   b. Na caixa nome do **módulo,** insira **CModule**.  
   c. Na caixa **de url do Repositório,** substitua o **local:5000** pelo valor do **servidor de Login** do seu registo de contentores Azure, no seguinte formato: `<registry name>.azurecr.io/cmodule`

    > [!NOTE]
    > Os repositórios de imagens incluem o nome do seu registo de contentor e o nome da sua imagem de contentor. A sua imagem de recipiente é pré-apultada do valor do projeto do módulo.  Pode recuperar o servidor de login a partir da página geral do seu registo de contentores no portal Azure.

1. **Selecione Adicionar** para criar o projeto.

### <a name="add-your-registry-credentials"></a>Adicionar as credenciais do registo

O manifesto de implantação partilha as credenciais para o seu registo de contentores com o tempo de execução IoT Edge. O runtime precisa destas credenciais para solicitar as imagens privadas para o dispositivo IoT Edge. Utilize as credenciais da secção de **chaves de acesso** do seu registo de contentores Azure.

1. No Visual Studio Solution Explorer, abra a *deployment.template.jsem* arquivo.

1. Procure a propriedade **registrária** nas propriedades $edgeAgent desejadas. O endereço de registo da propriedade deve ser preenchido automaticamente com a informação que forneceu quando criou o projeto. Os campos de nome de utilizador e palavra-passe devem conter nomes variáveis. Por exemplo:

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "$CONTAINER_REGISTRY_USERNAME_<registry name>",
       "password": "$CONTAINER_REGISTRY_PASSWORD_<registry name>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

1. Abra o arquivo ambiente (ENV) na solução do módulo. Por predefinição, o ficheiro está escondido no Solution Explorer, pelo que poderá ser necessário selecionar o botão **Mostrar Todos os Ficheiros** para o exibir. O ficheiro ENV deve conter o mesmo nome de utilizador e variáveis de palavra-passe que viu no *deployment.template.jsno* ficheiro.

1. Adicione o **nome de utilizador** e os valores de **palavra-passe** do seu registo de contentores Azure.

1. Guarde as suas alterações no ficheiro ENV.

### <a name="update-the-module-with-custom-code"></a>Atualizar o módulo com o código personalizado

O código do módulo predefinido recebe mensagens numa fila de entrada e passa-as através de uma fila de saída. Vamos adicionar mais um código para que o módulo processe as mensagens na borda antes de encaminhá-las para o seu hub IoT. Atualize o módulo de modo a analisar os dados de temperatura em cada mensagem e envie a mensagem para o hub IoT apenas se a temperatura exceder um determinado limiar.

1. Os dados do sensor neste cenário estão no formato JSON. Para filtrar mensagens no formato JSON, importe uma biblioteca JSON para C. Este tutorial utiliza o Parson.

   a. Descarregue o [repositório Do Pároco GitHub.](https://github.com/kgabis/parson)  
   b. Copie os ficheiros *parson.c* e *pároco.h* para o projeto CModule.  
   c. No Visual Studio, abra o ficheiro *CMakeLists.txt* da pasta do projeto CModule.  
   d. Na parte superior do ficheiro, importe os ficheiros do Parson como uma biblioteca chamada **my_parson**.

      ```txt
      add_library(my_parson
          parson.c
          parson.h
      )
      ```  
   e. Adicione `my_parson` à lista de bibliotecas na secção "target_link_libraries" do ficheiro *CMakeLists.txt.*  
   f. Guarde o ficheiro *CMakeLists.txt*.  
   exemplo, Selecione **CModule**  >  **principal.c**. Na parte inferior da lista de declarações incluem-se uma nova declaração para incluir `parson.h` para o suporte da JSON:

      ```c
      #include "parson.h"
      ```

1. No ficheiro *principal.c,* adicione uma variável global chamada `temperatureThreshold` ao lado da `messagesReceivedByInput1Queue` variável. Esta variável define o valor que a temperatura medida deve exceder para que os dados sejam enviados para o seu hub IoT.

    ```c
    static double temperatureThreshold = 25;
    ```

1. Encontre a `CreateMessageInstance` função na *.c* principal . Substitua a declaração interna por *outro* código, que adiciona algumas linhas de funcionalidade:

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

   As novas linhas de código na *outra* declaração adicionam uma nova propriedade à mensagem, que rotula a mensagem como um alerta. Este código rotula todas as mensagens como alertas, porque vamos adicionar funcionalidade que envia mensagens para o hub IoT apenas se reportarem altas temperaturas.

1. Encontre a `InputQueue1Callback` função e substitua toda a função pelo seguinte código. Esta função implementa o filtro de mensagens real. Quando uma mensagem é recebida, verifica se a temperatura reportada excede o limiar. Se a temperatura exceder o limiar, a função encaminha a mensagem através da sua fila de saída. Se não exceder o limiar, a função ignora a mensagem.

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

        // Check whether the message reports temperatures that exceed the threshold
        JSON_Value *root_value = json_parse_string(messageBody);
        JSON_Object *root_object = json_value_get_object(root_value);
        double temperature;

        // If temperature exceeds the threshold, send to output1
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
        // If message does not exceed the threshold, do not forward
        else
        {
            printf("Not sending message (%zu) to the next stage in pipeline.\r\n", messagesReceivedByInput1Queue);
            result = IOTHUBMESSAGE_ACCEPTED;
        }

        messagesReceivedByInput1Queue++;
        return result;
    }
    ```

1. Adicione uma função `moduleTwinCallback`. Este método recebe atualizações nas propriedades pretendidas do módulo duplo e atualiza a variável **temperatureThreshold** para corresponder. Todos os módulos têm o seu próprio módulo twin, o que permite configurar o código que está a funcionar dentro do módulo diretamente da nuvem.

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

1. Procure a `SetupCallbacksForModule` função. Substitua a função pelo seguinte código, que adiciona uma declaração *mais se se* a indicação para verificar se o módulo twin foi atualizado.

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

1. Guarde o ficheiro *main.c*.

1. Abra a *deployment.template.jsarquivada.*

1. Adicione o módulo **CModule** twin ao manifesto de implantação. Insira o seguinte conteúdo JSON na parte inferior da secção `moduleContent`, após o módulo duplo `$edgeHub`:

   ```json
   "CModule": {
       "properties.desired":{
           "TemperatureThreshold":25
       }
   }
   ```

   ![Screenshot mostrando o módulo twin sendo adicionado ao modelo de implementação.](./media/tutorial-c-module-windows/module-twin.png)

1. Guarde o ficheiro *deployment.template.json*.

## <a name="build-and-push-your-module"></a>Construa e empurre o seu módulo

Na secção anterior, criou uma solução IoT Edge e adicionou código à **CModule** para filtrar mensagens onde a temperatura da máquina reportada está abaixo do limiar aceitável. Agora, tem de criar a solução como uma imagem de contentor e enviá-la para o registo de contentor.

### <a name="sign-in-to-docker"></a>Inscreva-se no Docker

Forneça as suas credenciais de registo de contentores ao Docker na sua máquina de desenvolvimento para que possa empurrar a imagem do seu recipiente para ser armazenado no registo.

1. Abrir PowerShell ou uma janela de solicitação de comando.

1. Inscreva-se no Docker com as credenciais de registo do contentor Azure que guardou depois de ter criado o registo.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Poderá receber um aviso de segurança que recomende a utilização de `--password-stdin` . Embora recomendemos isto como uma melhor prática para cenários de produção, está fora do âmbito deste tutorial. Para mais informações, consulte a referência de login do [estivador.](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin)

### <a name="build-and-push"></a>Construir e empurrar

A sua máquina de desenvolvimento tem agora acesso ao seu registo de contentores, e os seus dispositivos IoT Edge também. É hora de transformar o código do projeto numa imagem de contentor.

1. No Visual Studio Solution Explorer, clique com o botão direito no nome do projeto que pretende construir. O nome padrão é **AzureIotEdgeApp1**. Para este tutorial, escolhemos o nome **CTutorialApp** e, como está a construir um módulo Windows, a extensão deve ser **Windows.Amd64**.

1. Selecione **Módulos de Borda IoT** de construção e push .

   O comando de construção e pressão inicia três operações:
   * Em primeiro lugar, cria uma nova pasta na solução denominada *config*, que detém o manifesto de implantação completo. É construído a partir de informações no modelo de implementação e outros ficheiros de solução. 
   * Em segundo lugar, funciona `docker build` para construir a imagem do contentor, com base no Dockerfile apropriado para a sua arquitetura alvo. 
   * Finalmente, corre `docker push` para empurrar o repositório de imagem para o seu registo de contentores.

   Este processo pode demorar vários minutos na primeira vez, mas será mais rápido da próxima vez que executar os comandos.

## <a name="deploy-modules-to-the-device"></a>Implementar módulos para o dispositivo

Utilize o Visual Studio Cloud Explorer e a extensão Azure IoT Edge Tools para implementar o projeto do módulo no seu dispositivo IoT Edge. Já preparou um manifesto de implantação para o seu cenário, o *deployment.windows-amd64.jsarquivado* na pasta *config.* Agora tudo o que precisa de fazer é selecionar um dispositivo para receber a implementação.

Certifique-se de que o seu dispositivo IoT Edge está a funcionar.

1. No Visual Studio Cloud Explorer, expanda os recursos para ver a sua lista de dispositivos IoT.

1. Clique com o botão direito no nome do dispositivo IoT Edge que deseja receber a implementação.

1. Selecione **Criar Implementação**.

1. No Visual Studio File Explorer, selecione a *deployment.windows-amd64.jsno* ficheiro na pasta *config* da sua solução.

1. Refresh Cloud Explorer para ver os módulos implantados que estão listados sob o seu dispositivo.

## <a name="view-generated-data"></a>Ver os dados gerados

Depois de aplicar o manifesto de implantação no seu dispositivo IoT Edge, o tempo de funcionamento do IoT Edge no dispositivo recolhe as novas informações de implantação e começa a executá-lo. Todos os módulos que estejam a funcionar no dispositivo mas não incluídos no manifesto de implantação estão parados. Todos os módulos que faltam no dispositivo são iniciados.

Pode utilizar a extensão IoT Edge Tools para visualizar as mensagens à medida que chegam ao seu hub IoT.

1. No Visual Studio Cloud Explorer, selecione o nome do seu dispositivo IoT Edge.

1. Na lista **de Ações,** selecione **Start Monitoring Built-in Event Endpoint**.

1. Veja as mensagens que estão a chegar ao seu hub IoT. Pode demorar algum tempo até que as mensagens cheguem, porque o dispositivo IoT Edge tem de receber a sua nova implementação e iniciar todos os módulos. As alterações ao código CModule devem aguardar até que a temperatura da máquina atinja os 25 graus antes de as mensagens poderem ser enviadas. O código também adiciona o tipo de mensagem **Alerta** a quaisquer mensagens que atinjam esse limiar de temperatura.

   ![Screenshot da janela de saída que exibe mensagens que estão a chegar ao hub IoT.](./media/tutorial-c-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Editar o módulo twin

Usaste o módulo CModule twin para fixar o limiar de temperatura a 25 graus. Pode utilizar o módulo twin para alterar a funcionalidade sem ter de atualizar o código do módulo.

1. No Visual Studio, abra a *deployment.windows-amd64.jsem* arquivo. 

   *Não* abra o ficheiro *de implementação.modelo.* Se não vir o manifesto de implantação no ficheiro *config* no Solution Explorer, selecione o ícone **'Mostrar todos os ficheiros'** na barra de ferramentas 'Solução Explorer'.

1. Procure o gémeo CModule e mude o valor do parâmetro **de temperaturaStatés** para uma nova temperatura que seja 5 a 10 graus mais alta que a temperatura mais recente reportada.

1. Guarde o *deployment.windows-amd64.jsarquivado.*

1. Siga novamente as instruções de utilização para aplicar o manifesto de implantação atualizado no seu dispositivo.

1. Monitorize as mensagens de entrada de dispositivo para nuvem. As mensagens devem parar até que o novo limiar de temperatura seja atingido.

## <a name="clean-up-resources"></a>Limpar os recursos

Se pretender continuar para o próximo artigo recomendado, pode conservar e reutilizar os recursos e configurações que criou neste tutorial. Também pode continuar a utilizar o mesmo dispositivo IoT Edge como um dispositivo de teste.

Caso contrário, para evitar incorrer em encargos, pode eliminar as configurações locais e os recursos Azure que utilizou aqui.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou uma função do módulo do IoT Edge com código para filtrar os dados não processados que são gerados pelo seu dispositivo IoT Edge.

Para saber como o Azure IoT Edge pode ajudá-lo a implementar serviços em nuvem Azure para processar e analisar dados no limite, continue para os próximos tutoriais.

> [!div class="nextstepaction"]
> Funções Azure [](tutorial-deploy-function.md) 
>  [Azure Stream Analytics](tutorial-deploy-stream-analytics.md) 
>  [Aprendizagem de Máquinas](tutorial-deploy-machine-learning.md) 
>  Azure [Serviço de visão personalizada](tutorial-deploy-custom-vision.md)
