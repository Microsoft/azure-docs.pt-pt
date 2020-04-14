---
title: O dispositivo Azure IoT SDK para C / Microsoft Docs
description: Inicie-se com o dispositivo Azure IoT SDK para C e aprenda a criar aplicações de dispositivos que se comuniquem com um hub IoT.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: robinsh
ms.openlocfilehash: 71193523a83987de2440d8c70c133c29dde4fe91
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81257883"
---
# <a name="azure-iot-device-sdk-for-c"></a>Dispositivo Azure IoT SDK para C

O **dispositivo Azure IoT SDK** é um conjunto de bibliotecas projetadas para simplificar o processo de envio de mensagens e receber mensagens do serviço **Azure IoT Hub.** Existem diferentes variações do SDK, cada uma visando uma plataforma específica, mas este artigo descreve o **dispositivo Azure IoT SDK para C**.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

O dispositivo Azure IoT SDK para C está escrito em ANSI C (C99) para maximizar a portabilidade. Esta funcionalidade torna as bibliotecas bem adaptadas para operar em várias plataformas e dispositivos, especialmente onde minimizar o disco e a pegada de memória é uma prioridade.

Existem uma ampla gama de plataformas nas quais o SDK foi testado (ver o catálogo de [dispositivos Azure Certified para ioT](https://catalog.azureiotsolutions.com/) para mais detalhes). Embora este artigo inclua walkthroughs de código de amostra em execução na plataforma Windows, o código descrito neste artigo é idêntico em toda a gama de plataformas suportadas.

O seguinte vídeo apresenta uma visão geral do Azure IoT SDK para C:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-IoT-C-SDK-insights/Player]

Este artigo apresenta-lhe a arquitetura do dispositivo Azure IoT SDK para C. Demonstra como inicializar a biblioteca do dispositivo, enviar dados para o IoT Hub e receber mensagens da mesmo. As informações neste artigo devem ser suficientes para começar a usar o SDK, mas também fornece indicações para informações adicionais sobre as bibliotecas.

## <a name="sdk-architecture"></a>Arquitetura SDK

Pode encontrar o [**dispositivo Azure IoT SDK para**](https://github.com/Azure/azure-iot-sdk-c) o repositório C GitHub e visualizar detalhes da API na [referência C API](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/).

A versão mais recente das bibliotecas pode ser encontrada no ramo **principal** do repositório:

  ![Screenshot do ramo principal do repositório](./media/iot-hub-device-sdk-c-intro/RepoMasterBranch.png)

* A implementação principal do SDK está na pasta do **cliente iothub\_** que contém a implementação da camada API mais baixa no SDK: a biblioteca **IoTHubClient.** A biblioteca **IoTHubClient** contém APIs implementando mensagens cruas para enviar mensagens para o IoT Hub e receber mensagens do IoT Hub. Ao utilizar esta biblioteca, é responsável pela implementação da serialização da mensagem, mas outros detalhes de comunicação com o IoT Hub são tratados para si.

* A pasta **serializador** contém funções de ajudante e amostras que mostram como serializar dados antes de enviar para o Azure IoT Hub usando a biblioteca do cliente. O uso do serializador não é obrigatório e é fornecido como uma conveniência. Para utilizar a biblioteca **serializer,** define um modelo que especifica os dados para enviar para o IoT Hub e as mensagens que espera receber do mesmo. Uma vez definido o modelo, o SDK fornece-lhe uma superfície API que lhe permite trabalhar facilmente com mensagens de dispositivo-a-nuvem e cloud-to-device sem se preocupar com os detalhes da serialização. A biblioteca depende de outras bibliotecas de código aberto que implementem o transporte utilizando protocolos como MQTT e AMQP.

* A biblioteca **IoTHubClient** depende de outras bibliotecas de código aberto:

  * A biblioteca [de utilidade partilhada Azure C,](https://github.com/Azure/azure-c-shared-utility) que fornece uma funcionalidade comum para tarefas básicas (tais como cordas, manipulação de listas e IO) necessária em vários C SDKs relacionados com OT.

  * A biblioteca [Azure uAMQP,](https://github.com/Azure/azure-uamqp-c) que é uma implementação do lado do cliente da AMQP otimizada para dispositivos limitados de recursos.

  * A biblioteca [Azure uMQTT,](https://github.com/Azure/azure-umqtt-c) que é uma biblioteca de uso geral implementando o protocolo MQTT e otimizada para dispositivos restritos de recursos.

A utilização destas bibliotecas é mais fácil de entender olhando para o código de exemplo. As seguintes secções passam por várias das aplicações de amostra que estão incluídas no SDK. Este passeio deve dar-lhe uma boa sensação para as várias capacidades das camadas arquitetónicas do SDK e uma introdução ao funcionar das APIs.

## <a name="before-you-run-the-samples"></a>Antes de executar as amostras

Antes de poder executar as amostras no dispositivo Azure IoT SDK para C, deve [criar uma instância do serviço IoT Hub](iot-hub-create-through-portal.md) na sua subscrição Azure. Em seguida, complete as seguintes tarefas:

* Preparar o ambiente de desenvolvimento
* Obtenha credenciais de dispositivo.

### <a name="prepare-your-development-environment"></a>Preparar o ambiente de desenvolvimento

Os pacotes são fornecidos para plataformas comuns (como NuGet para Windows ou apt_get para Debian e Ubuntu) e as amostras utilizam estes pacotes quando disponíveis. Em alguns casos, é necessário compilar o SDK para ou no seu dispositivo. Se precisar de compilar o SDK, consulte [Prepare o seu ambiente](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) de desenvolvimento no repositório GitHub.

Para obter o código de aplicação da amostra, descarregue uma cópia do SDK do GitHub. Obtenha a sua cópia da fonte do ramo **principal** do [repositório GitHub.](https://github.com/Azure/azure-iot-sdk-c)


### <a name="obtain-the-device-credentials"></a>Obtenha as credenciais do dispositivo

Agora que tens o código fonte da amostra, a próxima coisa a fazer é obter um conjunto de credenciais de dispositivo. Para que um dispositivo possa aceder a um hub IoT, primeiro deve adicionar o dispositivo ao registo de identidade IoT Hub. Quando adiciona o seu dispositivo, obtém um conjunto de credenciais de dispositivo que precisa para que o dispositivo possa ligar-se ao hub IoT. As aplicações de amostra discutidas na secção seguinte esperam estas credenciais sob a forma de uma cadeia de **ligação**ao dispositivo .

Existem várias ferramentas de código aberto para ajudá-lo a gerir o seu hub IoT.

* Uma aplicação Windows chamada [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer).

* Uma extensão de Código de Estúdio Visual de plataforma cruzada chamada [Ferramentas Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

* Um Python CLI transversal chamado [a extensão IoT para Azure CLI](https://github.com/Azure/azure-iot-cli-extension).

Este tutorial utiliza a ferramenta de explorador de *dispositivos gráficos.* Pode utilizar as *ferramentas Azure IoT para o Código VS* se desenvolver no Código VS. Também pode utilizar a extensão IoT para a ferramenta *Azure CLI 2.0* se preferir utilizar uma ferramenta CLI.

A ferramenta exploradora de dispositivos utiliza as bibliotecas de serviço SIOT para executar várias funções no IoT Hub, incluindo a adição de dispositivos. Se utilizar a ferramenta de explorador de dispositivos para adicionar um dispositivo, obtém uma corda de ligação para o seu dispositivo. Precisa desta cadeia de ligação para executar as aplicações da amostra.

Se não estiver familiarizado com a ferramenta exploradora do dispositivo, o procedimento seguinte descreve como usá-lo para adicionar um dispositivo e obter uma cadeia de ligação ao dispositivo.

1. Para instalar a ferramenta de explorador de dispositivos, consulte [como utilizar o Device Explorer para dispositivos IoT Hub](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer).

1. Quando executa o programa, vê esta interface:

   ![Screenshot twin do explorador do dispositivo](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinConfigTab.png)

1. Introduza a sua cadeia de **ligação IoT Hub** no primeiro campo e clique em **Atualizar**. Este passo configura a ferramenta para que possa comunicar com o IoT Hub. 

A cadeia de **ligação** pode ser encontrada em**definições** > de serviço >  **IoT Hub**Definições de**iothubowner**de**política** > de acesso partilhado .

1. Quando a cadeia de ligação IoT Hub estiver configurada, clique no separador **Gestão:**

   ![Screenshot de explorador de dispositivo twin / gestão](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinManagementTab.png)

Este separador é onde gere os dispositivos registados no seu hub IoT.

1. Cria um dispositivo clicando no botão **Criar.** Um diálogo exibe com um conjunto de teclas pré-povoadas (primária e secundária). Introduza um **ID do dispositivo** e, em seguida, clique em **Criar**.

   ![Criar screenshot do dispositivo](./media/iot-hub-device-sdk-c-intro/CreateDevice.png)

1. Quando o dispositivo é criado, a lista de Dispositivos atualiza com todos os dispositivos registados, incluindo o que acabou de criar. Se clicar no seu novo dispositivo, verá este menu:

   ![Resultado do clique direito do Explorador do Dispositivo Twin](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinManagementTab_RightClick.png)

1. Se escolher a cadeia de **ligação Copy para o dispositivo selecionado,** a cadeia de ligação do dispositivo é copiada para a área de reparação. Guarde uma cópia da corda de ligação do dispositivo. Precisa dele ao executar as aplicações de amostra descritas nas seguintes secções.

Quando completar os passos acima, está pronto para começar a executar um código. A maioria das amostras tem uma constante no topo do ficheiro principal de origem que lhe permite introduzir uma cadeia de ligação. Por exemplo, a linha correspondente das **amostras\_\_iothub_client iothub_convenience_sample** aplicação aparece da seguinte forma.

```c
static const char* connectionString = "[device connection string]";
```

## <a name="use-the-iothubclient-library"></a>Use a biblioteca IoTHubClient

Dentro da pasta do **cliente\_iothub** no repositório [azure-iot-sdk-c,](https://github.com/azure/azure-iot-sdk-c) há uma pasta **de amostras** que contém uma aplicação chamada **iothub\_client\_sample\_mqtt**.

A versão do Windows da **iothub_client\_amostras\_iothub_convenience_sample** aplicação inclui a seguinte solução Visual Studio:

  ![Explorador de soluções de estúdio visual](./media/iot-hub-device-sdk-c-intro/iothub-client-sample-mqtt.png)

> [!NOTE]
> Se o Visual Studio lhe pedir para redirecionar o projeto para a versão mais recente, aceite o pedido.

Esta solução contém um único projeto. Existem quatro pacotes NuGet instalados nesta solução:

* Microsoft.Azure.C.SharedUtility
* Microsoft.Azure.IoTHub.MqttTransport
* Microsoft.Azure.IoTHub.ioTHubClient
* Microsoft.Azure.umqtt

Precisa sempre do pacote **Microsoft.Azure.C.SharedUtility** quando estiver a trabalhar com o SDK. Esta amostra utiliza o protocolo MQTT, pelo que deve incluir os pacotes **Microsoft.Azure.umqtt** e **Microsoft.Azure.IoTHub.MqttTransport** (existem pacotes equivalentes para AMQP e HTTPS). Como a amostra utiliza a biblioteca **IoTHubClient,** também deve incluir o pacote **Microsoft.Azure.IoTHub.IoTHubClient** na sua solução.

Pode encontrar a implementação da aplicação da amostra nas **amostras\_\_iothub_client iothub_convenience_sample** ficheiro fonte.

Os seguintes passos utilizam esta aplicação de amostra para o acompanhar através do que é necessário para usar a biblioteca **IoTHubClient.**

### <a name="initialize-the-library"></a>Inicializar a biblioteca

> [!NOTE]
> Antes de começar a trabalhar com as bibliotecas, poderá ter de realizar uma inicialização específica da plataforma. Por exemplo, se planeia utilizar amqp no Linux, tem de inicializar a biblioteca OpenSSL. As amostras do [repositório GitHub](https://github.com/Azure/azure-iot-sdk-c) chamam a **plataforma\_** de função de utilidade quando o cliente começa e chama a **função de deinit da plataforma\_** antes de sair. Estas funções são declaradas no ficheiro de cabeçalho da plataforma.h. Examine as definições destas funções para a sua plataforma-alvo no [repositório](https://github.com/Azure/azure-iot-sdk-c) para determinar se precisa de incluir qualquer código de inicialização específico da plataforma no seu cliente.

Para começar a trabalhar com as bibliotecas, aloque primeiro um cabo de cliente IoT Hub:

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

Passa uma cópia da cadeia de ligação do dispositivo obtida da ferramenta exploradora do dispositivo para esta função. Também designa o protocolo de comunicações a utilizar. Este exemplo utiliza MQTT, mas AMQP e HTTPS também são opções.

Quando tiver um **MANIDOR\_\_DE CLIENTE IOTHUB**válido, pode começar a ligar para as APIs para enviar e receber mensagens de e para o IoT Hub.

### <a name="send-messages"></a>Enviar mensagens

A aplicação da amostra configura um loop para enviar mensagens para o seu centro ioT. O seguinte corte:

- Cria uma mensagem.
- Adiciona uma propriedade à mensagem.
- Envia uma mensagem.

Primeiro, criar uma mensagem:

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

Sempre que envia uma mensagem, especifica uma referência a uma função de chamada que é invocada quando os dados são enviados. Neste exemplo, a função de backback chama-se **SendConfirmationCallback**. O seguinte corte mostra esta função de callback:

```c
static void SendConfirmationCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    EVENT_INSTANCE* eventInstance = (EVENT_INSTANCE*)userContextCallback;
    (void)printf("Confirmation[%d] received for message tracking id = %zu with result = %s\r\n", callbackCounter, eventInstance->messageTrackingId, MU_ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
    /* Some device specific action code goes here... */
    callbackCounter++;
    IoTHubMessage_Destroy(eventInstance->messageHandle);
}
```

Note a chamada para a função **\_IoTHubMessage Destroy** quando terminar a mensagem. Esta função liberta os recursos atribuídos quando criou a mensagem.

### <a name="receive-messages"></a>Receber mensagens

Receber uma mensagem é uma operação assíncrona. Primeiro, regista a chamada para invocar quando o dispositivo recebe uma mensagem:

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

O último parâmetro é um ponteiro vazio para o que quiser. Na amostra, é um ponteiro para um inteiro, mas pode ser um ponteiro para uma estrutura de dados mais complexa. Este parâmetro permite que a função de chamada funcione em estado partilhado com o chamador desta função.

Quando o dispositivo recebe uma mensagem, a função de chamada registada é invocada. Esta função de callback recupera:

* O ID da mensagem e a identificação da correlação da mensagem.
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

Utilize a função **IoTHubMessage\_GetByteArray** para recuperar a mensagem, que neste exemplo é uma corda.

### <a name="uninitialize-the-library"></a>Não rubricar a biblioteca

Quando terminar de enviar eventos e receber mensagens, pode não ininicializar a biblioteca IoT. Para isso, emita a seguinte chamada de função:

```c
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

Esta chamada liberta os recursos anteriormente atribuídos pela função **IoTHubClient\_CreateFromConnectionString.**

Como pode ver, é fácil enviar e receber mensagens com a biblioteca **IoTHubClient.** A biblioteca lida com os detalhes da comunicação com o IoT Hub, incluindo qual o protocolo a utilizar (na perspetiva do desenvolvedor, esta é uma opção de configuração simples).

A biblioteca **IoTHubClient** também fornece um controlo preciso sobre como serializar os dados que o seu dispositivo envia para o IoT Hub. Em alguns casos, este nível de controlo é uma vantagem, mas noutros é um detalhe de implementação com o qual não se quer preocupar. Se for esse o caso, pode considerar usar a biblioteca de **serializadores,** que é descrita na próxima secção.

## <a name="use-the-serializer-library"></a>Use a biblioteca serializer

Conceptualmente, a biblioteca **serializer** fica em cima da biblioteca **IoTHubClient** no SDK. Utiliza a biblioteca **IoTHubClient** para a comunicação subjacente com o IoT Hub, mas adiciona capacidades de modelação que removem o fardo de lidar com a serialização de mensagens do desenvolvedor. Como funciona esta biblioteca é melhor demonstrado por um exemplo.

Dentro da pasta **serializador** no [repositório azure-iot-sdk-c](https://github.com/Azure/azure-iot-sdk-c), está uma pasta **de amostras** que contém uma aplicação chamada **simplesample\_mqtt**. A versão Windows desta amostra inclui a seguinte solução Visual Studio:

  ![Solução de estúdio visual para amostra de mqtt](./media/iot-hub-device-sdk-c-intro/simplesample_mqtt.png)

> [!NOTE]
> Se o Visual Studio lhe pedir para redirecionar o projeto para a versão mais recente, aceite o pedido.

Tal como acontece com a amostra anterior, esta inclui vários pacotes NuGet:

* Microsoft.Azure.C.SharedUtility
* Microsoft.Azure.IoTHub.MqttTransport
* Microsoft.Azure.IoTHub.ioTHubClient
* Microsoft.Azure.IoTHub.Serializer
* Microsoft.Azure.umqtt

Já viu a maioria destes pacotes na amostra anterior, mas **microsoft.Azure.IoTHub.Serializer** é novo. Este pacote é necessário quando utilizar a biblioteca **serializador.**

Pode encontrar a implementação da aplicação da amostra na **iothub_client\_amostras\_iothub_convenience_sample** ficheiro.

As seguintes secções passam por você através das partes-chave desta amostra.

### <a name="initialize-the-library"></a>Inicializar a biblioteca

Para começar a trabalhar com a biblioteca **serializer,** ligue para as APIs de inicialização:

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

A chamada para a função de entrada do **serializador\_** é uma chamada única e inicializa a biblioteca subjacente. Em seguida, ligue para a função **IoTHubClient\_LL\_CreateFromConnectionString,** que é a mesma API que na amostra **IoTHubClient.** Esta chamada define a corda de ligação do dispositivo (esta chamada também é onde escolhe o protocolo que pretende utilizar). Esta amostra utiliza o MQTT como transporte, mas pode utilizar AMQP ou HTTPS.

Por fim, ligue para a função **CREATE\_MODEL\_INSTANCE.** **WeatherStation** é o espaço-nome do modelo e **ContosoAnemometer** é o nome do modelo. Uma vez criada a instância do modelo, pode usá-la para começar a enviar e receber mensagens. No entanto, é importante entender o que é um modelo.

### <a name="define-the-model"></a>Definir o modelo

Um modelo na biblioteca de **serializadores** define as mensagens que o seu dispositivo pode enviar para o IoT Hub e as mensagens, chamadas *ações* na linguagem de modelação, que pode receber. Define um modelo utilizando um conjunto de macros C como nas **amostras\_\_iothub_client iothub_convenience_sample** aplicação da amostra:

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

As macros START **\_NAMESPACE** e **\_END NAMESPACE** tomam ambos o espaço de nome do modelo como argumento. Espera-se que qualquer coisa entre estas macros seja a definição do seu modelo ou modelo, e as estruturas de dados que os modelos usam.

Neste exemplo, há um único modelo chamado **ContosoAnemometro.** Este modelo define duas peças de dados que o seu dispositivo pode enviar para ioT Hub: **DeviceId** e **WindSpeed**. Também define três ações (mensagens) que o seu dispositivo pode receber: **TurnFanOn,** **TurnFanOff**e **SetAirResistance**. Cada elemento de dados tem um tipo, e cada ação tem um nome (e opcionalmente um conjunto de parâmetros).

Os dados e ações definidos no modelo definem uma superfície API que pode usar para enviar mensagens para o IoT Hub e responder às mensagens enviadas para o dispositivo. A utilização deste modelo é melhor compreendida através de um exemplo.

### <a name="send-messages"></a>Enviar mensagens

O modelo define os dados que pode enviar para o IoT Hub. Neste exemplo, isto significa um dos dois itens de dados definidos com a **macro WITH_DATA.** Existem vários passos necessários para enviar os valores **deviceId** e **WindSpeed** para um hub IoT. A primeira é definir os dados que pretende enviar:

```c
myWeather->DeviceId = "myFirstDevice";
myWeather->WindSpeed = avgWindSpeed + (rand() % 4 + 2);
```

O modelo que definiu anteriormente permite definir os valores definindo os membros de uma **estrutura**. Em seguida, serialize a mensagem que pretende enviar:

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

Este código serializa o dispositivo-para-nuvem para um tampão (referenciado por **destino).** O código invoca então a função **enviar Mensagens** para enviar a mensagem para o IoT Hub:

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

O segundo e último parâmetro do **IoTHubClient\_LL\_SendEventAsync** é uma referência a uma função de callback que é chamada quando os dados são enviados com sucesso. Aqui está a função de chamada na amostra:

```c
void sendCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    unsigned int messageTrackingId = (unsigned int)(uintptr_t)userContextCallback;

    (void)printf("Message Id: %u Received.\r\n", messageTrackingId);

    (void)printf("Result Call Back Called! Result is: %s \r\n", MU_ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
}
```

O segundo parâmetro é um indicador para o contexto do utilizador; o mesmo ponteiro passou para **IoTHubClient\_LL\_SendEventAsync**. Neste caso, o contexto é um simples contador, mas pode ser o que quiser.

É tudo o que há para enviar mensagens de dispositivo-para-nuvem. A única coisa que resta para cobrir é como receber mensagens.

### <a name="receive-messages"></a>Receber mensagens

Receber uma mensagem funciona da mesma forma que as mensagens funcionam na biblioteca **IoTHubClient.** Primeiro, regista uma função de chamada de mensagem:

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

Depois, escreve-se a função de chamada que é invocada quando uma mensagem é recebida:

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

Este código é uma placa de caldeira. Esta função recebe a mensagem e cuida de encaminhamento para a função adequada através da chamada para **EXECUTAR\_COMANDO**. A função chamada neste momento depende da definição das ações no seu modelo.

Quando define uma ação no seu modelo, é-lhe exigido implementar uma função que é chamada quando o seu dispositivo recebe a mensagem correspondente. Por exemplo, se o seu modelo definir esta ação:

```c
WITH_ACTION(SetAirResistance, int, Position)
```

Defina uma função com esta assinatura:

```c
EXECUTE_COMMAND_RESULT SetAirResistance(ContosoAnemometer* device, int Position)
{
    (void)device;
    (void)printf("Setting Air Resistance Position to %d.\r\n", Position);
    return EXECUTE_COMMAND_SUCCESS;
}
```

Note como o nome da função corresponde ao nome da ação no modelo e que os parâmetros da função correspondem aos parâmetros especificados para a ação. O primeiro parâmetro é sempre necessário e contém um ponteiro para a instância do seu modelo.

Quando o dispositivo recebe uma mensagem que corresponda a esta assinatura, a função correspondente é chamada. Portanto, para além de ter de incluir o código da placa de caldeira do **IoTHubMessage,** receber mensagens é apenas uma questão de definir uma função simples para cada ação definida no seu modelo.

### <a name="uninitialize-the-library"></a>Não rubricar a biblioteca

Quando terminar de enviar dados e receber mensagens, pode não ininicializar a biblioteca IoT:

```c
...
        DESTROY_MODEL_INSTANCE(myWeather);
    }
    IoTHubClient_LL_Destroy(iotHubClientHandle);
}
serializer_deinit();
```

Cada uma destas três funções alinha-se com as três funções de inicialização descritas anteriormente. Chamar estas APIs garante que liberte recursos previamente atribuídos.

## <a name="next-steps"></a>Passos Seguintes

Este artigo cobria os fundamentos da utilização das bibliotecas do **dispositivo Azure IoT SDK para C**. Forneceu-lhe informações suficientes para entender o que está incluído no SDK, na sua arquitetura e como começar a trabalhar com as amostras do Windows. O próximo artigo continua a descrição do SDK explicando [mais sobre a biblioteca IoTHubClient](iot-hub-device-sdk-c-iothubclient.md).

Para saber mais sobre o desenvolvimento para o IoT Hub, consulte os [SDKs Azure IoT](iot-hub-devguide-sdks.md).

Para explorar ainda mais as capacidades do IoT Hub, consulte:

* [Implementar o AI em dispositivos de ponta com o Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
