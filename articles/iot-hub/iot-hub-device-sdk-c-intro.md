---
title: Azure IoT device SDK para C | Documentos da Microsoft
description: Introdução ao Azure IoT device SDK para C e saiba como criar aplicações de dispositivos que comunicam com um hub IoT.
author: yzhong94
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 08/25/2017
ms.author: yizhon
ms.openlocfilehash: fe34668577efb1552731af7760896bccbb99cf67
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "57009193"
---
# <a name="azure-iot-device-sdk-for-c"></a>O Azure IoT-device SDK para C

O **Azure IoT device SDK** é um conjunto de bibliotecas projetadas para simplificar o processo de envio de mensagens para e receber mensagens a partir do **IoT Hub do Azure** serviço. Existem diferentes variações do SDK, cada um direcionado para uma plataforma específica, mas este artigo descreve os **Azure IoT device SDK para C**.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Azure IoT device SDK para C é escrito em ANSI C (C99) para maximizar a portabilidade. Esta funcionalidade torna as bibliotecas adequadas para operar em várias plataformas e dispositivos, especialmente onde minimizando o disco e requisitos de espaço de memória é uma prioridade.

Há uma ampla variedade de plataformas em que foi testado o SDK (consulte a [certificação do Azure para o catálogo de dispositivos de IoT](https://catalog.azureiotsolutions.com/) para obter detalhes). Embora este artigo inclui instruções passo a passo do código de exemplo em execução na plataforma Windows, o código descrito neste artigo é idêntico a variedade de plataformas suportadas.

O vídeo seguinte apresenta uma visão geral do SDK do Azure IoT para c:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-IoT-C-SDK-insights/Player]

Este artigo apresenta a arquitetura do Azure IoT device SDK para C. Ele demonstra como inicializar a biblioteca de dispositivo, enviar dados para o IoT Hub e receber mensagens a partir do mesmo. As informações neste artigo devem ser suficiente para começar a utilizar o SDK, mas também fornece indicadores para informações adicionais sobre as bibliotecas.

## <a name="sdk-architecture"></a>Arquitetura do SDK

Pode encontrar os [ **Azure IoT device SDK para C** ](https://github.com/Azure/azure-iot-sdk-c) GitHub repositório e exibir detalhes da API no [referência da API de C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/).

A versão mais recente das bibliotecas que pode ser encontrada no **mestre** ramo do repositório:

  ![Captura de ecrã do ramo principal do repositório](./media/iot-hub-device-sdk-c-intro/RepoMasterBranch.png)

* A implementação principal do SDK está a **iothub\_cliente** pasta que contém a implementação da camada de API mais baixa no SDK: a **o IoTHubClient** biblioteca. O **o IoTHubClient** biblioteca contém APIs que implementar em bruto de mensagens para enviar mensagens ao IoT Hub e receber mensagens do IoT Hub. Quando utilizar esta biblioteca, é responsável pela implementação de serialização de mensagem, mas outros detalhes de comunicar com o IoT Hub são tratados por si.

* O **serializador** pasta contém funções auxiliares e exemplos que mostram como serializar os dados antes de enviar para o IoT Hub do Azure com a biblioteca de cliente. O uso do serializador não é obrigatório e é fornecido como uma conveniência. Para utilizar o **serializador** biblioteca, define um modelo que especifica os dados para enviar para o IoT Hub e as mensagens que espera de receber a partir do mesmo. Assim que o modelo é definido, o SDK fornece uma superfície de API que lhe permite trabalhar facilmente com mensagens dispositivo-para-cloud e cloud-para-dispositivo sem se preocupar sobre os detalhes de serialização. A biblioteca depende de outras bibliotecas de código-fonte aberto que implementam o transporte através de protocolos como MQTT e AMQP.

* O **o IoTHubClient** biblioteca depende de outras bibliotecas de código-fonte aberto:

  * O [utilitário partilhado do Azure C](https://github.com/Azure/azure-c-shared-utility) biblioteca, que fornece funcionalidade comum para as tarefas básicas (por exemplo, cadeias de caracteres, manipulação de lista e e/s) necessária em vários SDKs C relacionados com o Azure.

  * O [Azure uAMQP](https://github.com/Azure/azure-uamqp-c) biblioteca, o que é uma implementação do lado do cliente do AMQP otimizado para dispositivos de restrição de recursos.

  * O [Azure uMQTT](https://github.com/Azure/azure-umqtt-c) biblioteca, o que é uma biblioteca para fins gerais de implementação do protocolo MQTT e otimizado para dispositivos de restrição de recursos.

Uso dessas bibliotecas é mais fácil de entender examinando o código de exemplo. As secções seguintes descrevem várias das aplicações de exemplo que estão incluídas no SDK. Este passo a passo deve dar a uma boa percepção as várias capacidades de camadas de arquiteturais do SDK e uma introdução ao como funcionam as APIs.

## <a name="before-you-run-the-samples"></a>Antes de executar os exemplos

Antes de poder executar os exemplos no Azure IoT device SDK para C, deve [criar uma instância do serviço IoT Hub](iot-hub-create-through-portal.md) na sua subscrição do Azure. Em seguida, conclua as seguintes tarefas:

* Preparar o ambiente de desenvolvimento
* Obter as credenciais do dispositivo.

### <a name="prepare-your-development-environment"></a>Preparar o ambiente de desenvolvimento

Pacotes são fornecidos para plataformas comuns (por exemplo, o NuGet para Windows ou apt_get para Debian e Ubuntu) e os exemplos utilizam estes pacotes quando disponível. Em alguns casos, precisa compilar o SDK para ou no seu dispositivo. Se precisa compilar o SDK, veja [preparar o ambiente de desenvolvimento](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) no repositório do GitHub.

Para obter o código de aplicativo de exemplo, transfira uma cópia do SDK do GitHub. Obter a sua cópia da origem do **mestre** ramo do [repositório do GitHub](https://github.com/Azure/azure-iot-sdk-c).


### <a name="obtain-the-device-credentials"></a>Obter as credenciais de dispositivo

Agora que tem o código de origem de exemplo, a próxima coisa a fazer é obter um conjunto de credenciais do dispositivo. Para um dispositivo poder aceder a um hub IoT, primeiro tem de adicionar o dispositivo no registo de identidade do IoT Hub. Quando adicionar o seu dispositivo, obtém um conjunto de credenciais de dispositivo que necessita para o dispositivo poder estabelecer ligação ao IoT hub. Os aplicativos de exemplo discutidos na próxima seção esperam estas credenciais na forma de um **cadeia de ligação do dispositivo**.

Existem várias ferramentas de código-fonte aberto para ajudar a gerir o seu hub IoT.

* Um aplicativo do Windows chamado [Explorador de dispositivos](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer).

* Uma extensão do Visual Studio Code para várias plataformas chamada [ferramentas do Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

* Uma CLI de Python para várias plataformas chamada [a extensão de IoT para a CLI do Azure](https://github.com/Azure/azure-iot-cli-extension).

Este tutorial utiliza o gráfica *Explorador de dispositivos* ferramenta. Pode utilizar o *ferramentas de IoT do Azure para VS Code* se desenvolver no VS Code. Também pode utilizar o *a extensão de IoT do Azure CLI 2.0* ferramenta se preferir usar uma ferramenta CLI.

A ferramenta Explorador do dispositivo usa as bibliotecas de serviço de IoT do Azure para executar várias funções no IoT Hub, incluindo a adição de dispositivos. Se utilizar a ferramenta Explorador do dispositivo para adicionar um dispositivo, obtém uma cadeia de ligação para o seu dispositivo. Precisa esta cadeia de ligação para executar as aplicações de exemplo.

Se não estiver familiarizado com a ferramenta Explorador do dispositivo, o procedimento seguinte descreve como usá-lo para adicionar um dispositivo e obter uma cadeia de ligação do dispositivo.

1. Para instalar a ferramenta Explorador do dispositivo, veja [como utilizar o Device Explorer para dispositivos do IoT Hub](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer).

1. Quando executar o programa, verá essa interface:

  ![Captura de ecrã do Device Explorer duplo](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinConfigTab.png)

1. Introduza o seu **cadeia de ligação do Hub IoT** no primeiro campo e clique **atualização**. Este passo configura a ferramenta, para que este possa comunicar com IoT Hub. 

O **cadeia de ligação** pode ser encontrado na **serviço Hub IoT** > **definições** > **política de acesso partilhado**  >  **iothubowner**.

1. Quando a cadeia de ligação do IoT Hub é configurada, clique nas **gestão** separador:

  ![Device Explorer duplo / captura de ecrã de gestão](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinManagementTab.png)

Este separador é onde gerir os dispositivos registados no IoT hub.

1. Criar um dispositivo ao clicar o **criar** botão. Uma caixa de diálogo é apresentada com um conjunto de chaves preenchidas previamente (primários e secundários). Introduza um **ID do dispositivo** e, em seguida, clique em **criar**.

  ![Criar a captura de ecrã do dispositivo](./media/iot-hub-device-sdk-c-intro/CreateDevice.png)

1. Quando o dispositivo é criado, os dispositivos listam atualizações com todos os dispositivos registados, incluindo o que acabou de criar. Se clique no seu dispositivo novo, vê esse menu:

  ![Resultado do Device Explorer duplo de contexto](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinManagementTab_RightClick.png)

1. Se escolher **copiar a cadeia de ligação para o dispositivo selecionado**, a cadeia de ligação do dispositivo é copiada para a área de transferência. Mantenha uma cópia da cadeia de ligação do dispositivo. Irá precisar dele quando executar os aplicativos de exemplo descritos nas seções a seguir.

Quando tiver concluído os passos acima, está pronto para iniciar a execução de código. A maioria dos exemplos possuem uma constante na parte superior do ficheiro de origem principal que permite-lhe introduzir uma cadeia de ligação. Por exemplo, a linha correspondente a partir do **iothub_client\_exemplos\_iothub_convenience_sample** aplicação é apresentado da seguinte forma.

```c
static const char* connectionString = "[device connection string]";
```

## <a name="use-the-iothubclient-library"></a>Utilize a biblioteca o IoTHubClient

Dentro os **iothub\_cliente** pasta no [azure-iot-sdk-c](https://github.com/azure/azure-iot-sdk-c) repositório, há um **exemplos** pasta que contém um aplicativo chamado **iothub\_cliente\_exemplo\_mqtt**.

A versão do Windows dos **iothub_client\_amostras\_iothub_convenience_sample** aplicativo inclui a seguinte solução do Visual Studio:

  ![Explorador de soluções do Visual Studio](./media/iot-hub-device-sdk-c-intro/iothub-client-sample-mqtt.png)

> [!NOTE]
> Se abrir esse projeto no Visual Studio 2017, aceite as sugestões para redirecionar o projeto para a versão mais recente.

Essa solução contém um único projeto. Existem quatro pacotes de NuGet instaladas nesta solução:

* Microsoft.Azure.C.SharedUtility
* Microsoft.Azure.IoTHub.MqttTransport
* Microsoft.Azure.IoTHub.IoTHubClient
* Microsoft.Azure.umqtt

Precisa sempre a **Microsoft.Azure.C.SharedUtility** quando estiver a trabalhar com o SDK do pacote. Este exemplo utiliza o protocolo MQTT, por isso deve incluir o **Microsoft.Azure.umqtt** e **Microsoft.Azure.IoTHub.MqttTransport** pacotes (existem pacotes equivalentes para AMQP e HTTPS). Uma vez que o exemplo utiliza a **o IoTHubClient** biblioteca, tem também de incluir o **Microsoft.Azure.IoTHub.IoTHubClient** pacote na sua solução.

Pode encontrar a implementação da aplicação de exemplo no **iothub_client\_amostras\_iothub_convenience_sample** ficheiro de origem.

Os passos seguintes utilizam este aplicativo de exemplo para percorrer o que é necessário para utilizar o **o IoTHubClient** biblioteca.

### <a name="initialize-the-library"></a>Inicializar a biblioteca

> [!NOTE]
> Antes de começar a trabalhar com as bibliotecas, terá de executar alguma inicialização específica da plataforma. Por exemplo, se planeja usar o AMQP no Linux, é preciso inicializar a biblioteca OpenSSL. Os exemplos a [repositório do GitHub](https://github.com/Azure/azure-iot-sdk-c) chamar a função de utilitário **plataforma\_init** quando o cliente é iniciado e chamar o **plataforma\_deinit**função antes de sair. Estas funções são declaradas no arquivo de cabeçalho platform.h. Examinar as definições destas funções para a sua plataforma de destino no [repositório](https://github.com/Azure/azure-iot-sdk-c) para determinar se é preciso incluir qualquer código de inicialização específica da plataforma no seu cliente.

Para começar a trabalhar com as bibliotecas, primeiro alocar um identificador de cliente do IoT Hub:

```c
if ((iotHubClientHandle = 
  IoTHubClient_LL_CreateFromConnectionString(connectionString, MQTT_Protocol)) == NULL)
{
    (void)printf("ERROR: iotHubClientHandle is NULL!\r\n");
}
else
{
    ...
```

Passa uma cópia da cadeia de ligação de dispositivo que obteve a partir da ferramenta do Explorador de dispositivos para esta função. Também designar o protocolo de comunicações a utilizar. Este exemplo utiliza MQTT mas, AMQP e HTTPS também são opções.

Quando tiver válido **IOTHUB\_cliente\_PROCESSAR**, pode começar a chamar as APIs para enviar e receber mensagens de e para o IoT Hub.

### <a name="send-messages"></a>Enviar mensagens

O aplicativo de exemplo configura um loop para enviar mensagens ao seu hub IoT. O trecho a seguir:

- Cria uma mensagem.
- Adiciona uma propriedade para a mensagem.
- Envia uma mensagem.

Em primeiro lugar, crie uma mensagem:

```c
size_t iterator = 0;
do
{
    if (iterator < MESSAGE_COUNT)
    {
        sprintf_s(msgText, sizeof(msgText), "{\"deviceId\":\"myFirstDevice\",\"windSpeed\":%.2f}", avgWindSpeed + (rand() % 4 + 2));
        if ((messages[iterator].messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText))) == NULL)
        {
            (void)printf("ERROR: iotHubMessageHandle is NULL!\r\n");
        }
        else
        {
            messages[iterator].messageTrackingId = iterator;
            MAP_HANDLE propMap = IoTHubMessage_Properties(messages[iterator].messageHandle);
            (void)sprintf_s(propText, sizeof(propText), "PropMsg_%zu", iterator);
            if (Map_AddOrUpdate(propMap, "PropName", propText) != MAP_OK)
            {
                (void)printf("ERROR: Map_AddOrUpdate Failed!\r\n");
            }

            if (IoTHubClient_LL_SendEventAsync(iotHubClientHandle, messages[iterator].messageHandle, SendConfirmationCallback, &messages[iterator]) != IOTHUB_CLIENT_OK)
            {
                (void)printf("ERROR: IoTHubClient_LL_SendEventAsync..........FAILED!\r\n");
            }
            else
            {
                (void)printf("IoTHubClient_LL_SendEventAsync accepted message [%d] for transmission to IoT Hub.\r\n", (int)iterator);
            }
        }
    }
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(1);

    iterator++;
} while (g_continueRunning);
```

Sempre que envia uma mensagem, especifique uma referência a uma função de retorno de chamada é invocada quando os dados são enviados. Neste exemplo, a função de retorno de chamada é chamada **SendConfirmationCallback**. O fragmento seguinte mostra esta função de retorno de chamada:

```c
static void SendConfirmationCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    EVENT_INSTANCE* eventInstance = (EVENT_INSTANCE*)userContextCallback;
    (void)printf("Confirmation[%d] received for message tracking id = %zu with result = %s\r\n", callbackCounter, eventInstance->messageTrackingId, ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
    /* Some device specific action code goes here... */
    callbackCounter++;
    IoTHubMessage_Destroy(eventInstance->messageHandle);
}
```

Observe a chamada para o **IoTHubMessage\_destruir** funcionar quando tiver terminado com a mensagem. Essa função libera os recursos alocados quando criou a mensagem.

### <a name="receive-messages"></a>Receber mensagens

Receber uma mensagem é uma operação assíncrona. Em primeiro lugar, registra o retorno de chamada a ser invocado quando o dispositivo recebe uma mensagem:

```c
if (IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext) != IOTHUB_CLIENT_OK)
{
    (void)printf("ERROR: IoTHubClient_LL_SetMessageCallback..........FAILED!\r\n");
}
else
{
    (void)printf("IoTHubClient_LL_SetMessageCallback...successful.\r\n");
    ...
```

O último parâmetro é um ponteiro nulo como desejar. No exemplo, é um ponteiro para um número inteiro, mas poderia ser um ponteiro para uma estrutura de dados mais complexo. Este parâmetro permite que a função de retorno de chamada para operar no Estado compartilhado com o chamador dessa função.

Quando o dispositivo recebe uma mensagem, a função de retorno de chamada registrado é invocada. Esta função de retorno de chamada obtém:

* O id da mensagem e o id de correlação da mensagem.
* O conteúdo da mensagem.
* Quaisquer propriedades personalizadas da mensagem.

```c
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    int* counter = (int*)userContextCallback;
    const char* buffer;
    size_t size;
    MAP_HANDLE mapProperties;
    const char* messageId;
    const char* correlationId;

    // Message properties
    if ((messageId = IoTHubMessage_GetMessageId(message)) == NULL)
    {
        messageId = "<null>";
    }

    if ((correlationId = IoTHubMessage_GetCorrelationId(message)) == NULL)
    {
        correlationId = "<null>";
    }

    // Message content
    if (IoTHubMessage_GetByteArray(message, (const unsigned char**)&buffer, &size) != IOTHUB_MESSAGE_OK)
    {
        (void)printf("unable to retrieve the message data\r\n");
    }
    else
    {
        (void)printf("Received Message [%d]\r\n Message ID: %s\r\n Correlation ID: %s\r\n Data: <<<%.*s>>> & Size=%d\r\n", *counter, messageId, correlationId, (int)size, buffer, (int)size);
        // If we receive the work 'quit' then we stop running
        if (size == (strlen("quit") * sizeof(char)) && memcmp(buffer, "quit", size) == 0)
        {
            g_continueRunning = false;
        }
    }

    // Retrieve properties from the message
    mapProperties = IoTHubMessage_Properties(message);
    if (mapProperties != NULL)
    {
        const char*const* keys;
        const char*const* values;
        size_t propertyCount = 0;
        if (Map_GetInternals(mapProperties, &keys, &values, &propertyCount) == MAP_OK)
        {
            if (propertyCount > 0)
            {
                size_t index;

                printf(" Message Properties:\r\n");
                for (index = 0; index < propertyCount; index++)
                {
                    (void)printf("\tKey: %s Value: %s\r\n", keys[index], values[index]);
                }
                (void)printf("\r\n");
            }
        }
    }

    /* Some device specific action code goes here... */
    (*counter)++;
    return IOTHUBMESSAGE_ACCEPTED;
}
```

Utilize o **IoTHubMessage\_GetByteArray** função para obter a mensagem, que, neste exemplo, é uma cadeia de caracteres.

### <a name="uninitialize-the-library"></a>Uninitialize a biblioteca

Quando tiver terminado eventos de envio e recebimento de mensagens, pode uninitialize a biblioteca de IoT. Para fazê-lo, emita a chamada de função seguinte:

```c
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

Esta chamada liberta os recursos alocados anteriormente pelos **o IoTHubClient\_CreateFromConnectionString** função.

Como pode ver, é fácil enviar e receber mensagens com o **o IoTHubClient** biblioteca. A biblioteca processa os detalhes de comunicação com o IoT Hub, incluindo qual protocolo usar (da perspectiva do desenvolvedor, esta é uma opção de configuração simples).

O **o IoTHubClient** biblioteca também fornece um controlo preciso sobre como serializar os dados do seu dispositivo envia ao IoT Hub. Em alguns casos, este nível de controlo é uma vantagem, mas em outros, é um detalhe de implementação que pretende que não se preocupar com. Se for esse o caso, poderá considerar a utilização a **serializador** biblioteca, o que é descrita na secção seguinte.

## <a name="use-the-serializer-library"></a>Utilize a biblioteca de serializador

Conceitualmente a **serializador** biblioteca encontra-se na parte superior do **o IoTHubClient** biblioteca no SDK. Ele usa o **o IoTHubClient** biblioteca para a comunicação subjacente com o IoT Hub, mas adiciona capacidades de modelação o sacrifício de lidar com a serialização de mensagem do desenvolvedor. Como funciona esta biblioteca é melhor demonstrado pelo exemplo.

Dentro de **serializador** pasta no [repositório azure-iot-sdk-c](https://github.com/Azure/azure-iot-sdk-c), é um **exemplos** pasta que contém um aplicativo chamado **simplesample\_mqtt**. A versão do Windows deste exemplo inclui a seguinte solução do Visual Studio:

  ![Solução do Visual Studio para o exemplo de mqtt](./media/iot-hub-device-sdk-c-intro/simplesample_mqtt.png)

> [!NOTE]
> Se abrir esse projeto no Visual Studio 2017, aceite as sugestões para redirecionar o projeto para a versão mais recente.

Tal como acontece com o exemplo anterior, este inclui vários pacotes de NuGet:

* Microsoft.Azure.C.SharedUtility
* Microsoft.Azure.IoTHub.MqttTransport
* Microsoft.Azure.IoTHub.IoTHubClient
* Microsoft.Azure.IoTHub.Serializer
* Microsoft.Azure.umqtt

Viu que a maioria desses pacotes no exemplo anterior, mas **Microsoft.Azure.IoTHub.Serializer** há de novo. Este pacote é necessário quando utiliza a **serializador** biblioteca.

Pode encontrar a implementação do exemplo de aplicativo no ***iothub_client\_amostras\_iothub_convenience_sample** ficheiro.

As secções seguintes guiá-lo pelas principais partes deste exemplo.

### <a name="initialize-the-library"></a>Inicializar a biblioteca

Para começar a trabalhar com o **serializador** biblioteca, chamar a APIs de inicialização:

```c
if (serializer_init(NULL) != SERIALIZER_OK)
{
    (void)printf("Failed on serializer_init\r\n");
}
else
{
    IOTHUB_CLIENT_LL_HANDLE iotHubClientHandle = IoTHubClient_LL_CreateFromConnectionString(connectionString, MQTT_Protocol);
    srand((unsigned int)time(NULL));
    int avgWindSpeed = 10;

    if (iotHubClientHandle == NULL)
    {
        (void)printf("Failed on IoTHubClient_LL_Create\r\n");
    }
    else
    {
        ContosoAnemometer* myWeather = CREATE_MODEL_INSTANCE(WeatherStation, ContosoAnemometer);
        if (myWeather == NULL)
        {
            (void)printf("Failed on CREATE_MODEL_INSTANCE\r\n");
        }
        else
        {
...
```

A chamada para o **serializador\_init** é uma única chamada de função e inicializa a biblioteca subjacente. Em seguida, chamar o **o IoTHubClient\_LL\_CreateFromConnectionString** função, que é a mesma API como no **o IoTHubClient** exemplo. Esta chamada define a cadeia de ligação do dispositivo (esta chamada é também onde escolher o protocolo que pretende utilizar). Este exemplo utiliza MQTT como transporte, mas poderia utilizar AMQP ou HTTPS.

Por fim, chame o **CREATE\_modelo\_instância** função. **WeatherStation** é o espaço de nomes do modelo e **ContosoAnemometer** é o nome do modelo. Assim que a instância do modelo for criada, pode usá-lo para iniciar o envio e recebimento de mensagens. No entanto, é importante compreender que um modelo é.

### <a name="define-the-model"></a>Definir o modelo

Um modelo no **serializador** biblioteca define as mensagens que o dispositivo pode enviar para o IoT Hub e as mensagens, chamadas *ações* na linguagem de modelagem, que pode receber. Define um modelo utilizando um conjunto de macros de C, como mostra a **iothub_client\_amostras\_iothub_convenience_sample** aplicação de exemplo:

```c
BEGIN_NAMESPACE(WeatherStation);

DECLARE_MODEL(ContosoAnemometer,
WITH_DATA(ascii_char_ptr, DeviceId),
WITH_DATA(int, WindSpeed),
WITH_ACTION(TurnFanOn),
WITH_ACTION(TurnFanOff),
WITH_ACTION(SetAirResistance, int, Position)
);

END_NAMESPACE(WeatherStation);
```

O **começar\_espaço de nomes** e **END\_espaço de nomes** macros, ambos os usar o espaço de nomes do modelo como um argumento. Espera-se que nada entre essas macros é a definição de seu modelo ou modelos e as estruturas de dados que utilizam os modelos.

Neste exemplo, existe um modelo único chamado **ContosoAnemometer**. Esse modelo define duas partes de dados que o dispositivo pode enviar para o IoT Hub: **DeviceId** e **WindSpeed**. Também define três ações (mensagens), que pode receber o seu dispositivo: **TurnFanOn**, **TurnFanOff**, e **SetAirResistance**. Cada elemento de dados tem um tipo e cada ação tem um nome (e, opcionalmente, um conjunto de parâmetros).

Os dados e as ações definidas no modelo de definem uma superfície de API que pode utilizar para enviar mensagens para o IoT Hub e responder às mensagens enviadas para o dispositivo. O uso desse modelo é mais bem compreendido por meio de um exemplo.

### <a name="send-messages"></a>Enviar mensagens

O modelo define os dados que pode enviar para o IoT Hub. Neste exemplo, isso significa que um dos itens de dados de dois definidos com o **WITH_DATA** macro. Há várias etapas necessárias para enviar **DeviceId** e **WindSpeed** valores para um hub IoT. A primeira é definir os dados que pretende enviar:

```c
myWeather->DeviceId = "myFirstDevice";
myWeather->WindSpeed = avgWindSpeed + (rand() % 4 + 2);
```

O modelo que definiu anteriormente permite-lhe definir os valores ao definir membros de um **struct**. Em seguida, serializa a mensagem que pretende enviar:

```c
unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, myWeather->DeviceId, myWeather->WindSpeed) != CODEFIRST_OK)
{
    (void)printf("Failed to serialize\r\n");
}
else
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
    free(destination);
}
```

Esse código serializa a dispositivo para a cloud para um buffer (referenciado pela **destino**). O código, em seguida, invoca o **sendMessage** função para enviar a mensagem para o IoT Hub:

```c
static void sendMessage(IOTHUB_CLIENT_LL_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
{
    static unsigned int messageTrackingId;
    IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
    if (messageHandle == NULL)
    {
        printf("unable to create a new IoTHubMessage\r\n");
    }
    else
    {
        if (IoTHubClient_LL_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)(uintptr_t)messageTrackingId) != IOTHUB_CLIENT_OK)
        {
            printf("failed to hand over the message to IoTHubClient");
        }
        else
        {
            printf("IoTHubClient accepted the message for delivery\r\n");
        }
        IoTHubMessage_Destroy(messageHandle);
    }
    messageTrackingId++;
}
```

O segundo para o último parâmetro de **o IoTHubClient\_LL\_SendEventAsync** é uma referência a uma função de retorno de chamada que é chamada quando os dados são enviados com êxito. Esta é a função de retorno de chamada no exemplo:

```c
void sendCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    unsigned int messageTrackingId = (unsigned int)(uintptr_t)userContextCallback;

    (void)printf("Message Id: %u Received.\r\n", messageTrackingId);

    (void)printf("Result Call Back Called! Result is: %s \r\n", ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
}
```

O segundo parâmetro é um ponteiro para o contexto de usuário; o ponteiro do mesmo transmitidos para **o IoTHubClient\_LL\_SendEventAsync**. Neste caso, o contexto é um contador simple, mas pode ser que quiser.

É tudo o que há para enviar mensagens dispositivo-para-cloud. O única coisa que falta para abordar é como receber mensagens.

### <a name="receive-messages"></a>Receber mensagens

Receber uma mensagem funciona de forma semelhante às mensagens de forma trabalham no **o IoTHubClient** biblioteca. Em primeiro lugar, registra uma função de retorno de chamada de mensagem:

```c
if (IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, 
  IoTHubMessage, myWeather) != IOTHUB_CLIENT_OK)
{
    printf("unable to IoTHubClient_SetMessageCallback\r\n");
}
else
{
...
```

Em seguida, escrever a função de retorno de chamada é invocada quando uma mensagem é recebida:

```c
static IOTHUBMESSAGE_DISPOSITION_RESULT IoTHubMessage(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    IOTHUBMESSAGE_DISPOSITION_RESULT result;
    const unsigned char* buffer;
    size_t size;
    if (IoTHubMessage_GetByteArray(message, &buffer, &size) != IOTHUB_MESSAGE_OK)
    {
        printf("unable to IoTHubMessage_GetByteArray\r\n");
        result = IOTHUBMESSAGE_ABANDONED;
    }
    else
    {
        /*buffer is not zero terminated*/
        char* temp = malloc(size + 1);
        if (temp == NULL)
        {
            printf("failed to malloc\r\n");
            result = IOTHUBMESSAGE_ABANDONED;
        }
        else
        {
            (void)memcpy(temp, buffer, size);
            temp[size] = '\0';
            EXECUTE_COMMAND_RESULT executeCommandResult = EXECUTE_COMMAND(userContextCallback, temp);
            result =
                (executeCommandResult == EXECUTE_COMMAND_ERROR) ? IOTHUBMESSAGE_ABANDONED :
                (executeCommandResult == EXECUTE_COMMAND_SUCCESS) ? IOTHUBMESSAGE_ACCEPTED :
                IOTHUBMESSAGE_REJECTED;
            free(temp);
        }
    }
    return result;
}
```

Esse código é clichê, mas sim os mesmos para qualquer solução. Esta função recebe a mensagem e se encarrega de encaminhamento para a função adequada por meio da chamada para **EXECUTE\_comando**. A função chamada depende neste momento a definição de ações no seu modelo.

Quando define uma ação no seu modelo, tem de implementar uma função que é chamada quando o dispositivo recebe a mensagem correspondente. Por exemplo, se seu modelo define esta ação:

```c
WITH_ACTION(SetAirResistance, int, Position)
```

Defina uma função com essa assinatura:

```c
EXECUTE_COMMAND_RESULT SetAirResistance(ContosoAnemometer* device, int Position)
{
    (void)device;
    (void)printf("Setting Air Resistance Position to %d.\r\n", Position);
    return EXECUTE_COMMAND_SUCCESS;
}
```

Observe como o nome da função corresponde ao nome da ação no modelo e que os parâmetros da função correspondem aos parâmetros especificados para a ação. O primeiro parâmetro é sempre necessário e contém um ponteiro para a instância do seu modelo.

Quando o dispositivo recebe uma mensagem que corresponde a essa assinatura, denomina-se a função correspondente. Por conseguinte, além de ter de incluir o código clichê partir **IoTHubMessage**, recebimento de mensagens é apenas uma questão de definir uma função simple para cada ação definida no seu modelo.

### <a name="uninitialize-the-library"></a>Uninitialize a biblioteca

Quando tiver terminado o envio de dados e de recebimento de mensagens, pode uninitialize a biblioteca de IoT:

```c
...
        DESTROY_MODEL_INSTANCE(myWeather);
    }
    IoTHubClient_LL_Destroy(iotHubClientHandle);
}
serializer_deinit();
```

Cada uma destas três funções se alinha com três funções de inicialização descritas anteriormente. Chamar essas APIs garante que liberar recursos alocados anteriormente.

## <a name="next-steps"></a>Próximos Passos

Este artigo abordou as noções básicas da utilização de bibliotecas no **Azure IoT device SDK para C**. Ele lhe forneceu informações suficientes para compreender o que está incluído no SDK, sua arquitetura e como começar a trabalhar com os exemplos do Windows. O artigo seguinte continua a descrição do SDK ao explicar [mais sobre a biblioteca o IoTHubClient](iot-hub-device-sdk-c-iothubclient.md).

Para saber mais sobre o desenvolvimento para o IoT Hub, veja a [do Azure IoT SDKs](iot-hub-devguide-sdks.md).

Para explorar ainda mais os recursos do IoT Hub, veja:

* [Implementar o AI em dispositivos de ponta com o Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
