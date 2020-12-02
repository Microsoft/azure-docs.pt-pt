---
title: O dispositivo Azure IoT SDK para C / Microsoft Docs
description: Começa com o dispositivo Azure IoT SDK para C e aprende a criar aplicações de dispositivos que comunicam com um hub IoT.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: robinsh
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: 9b870e21ffd5c6a8261b6731b939b5dff558256d
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96501197"
---
# <a name="azure-iot-device-sdk-for-c"></a>Dispositivo Azure IoT SDK para C

O **dispositivo Azure IoT SDK** é um conjunto de bibliotecas projetadas para simplificar o processo de envio de mensagens e receber mensagens do serviço **Azure IoT Hub.** Existem diferentes variações do SDK, cada um direcionando uma plataforma específica, mas este artigo descreve o **dispositivo Azure IoT SDK para C**.

> [!NOTE]
> O Incorporado C SDK é uma alternativa para dispositivos constrangidos que suportam a abordagem trazer a sua própria rede (BYON). Os desenvolvedores de IoT têm a liberdade de trazer o cliente MQTT, TLS, e tomada da sua escolha para criar uma solução de dispositivo. [Saiba mais sobre o C SDK incorporado.](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

O dispositivo Azure IoT SDK para C está escrito em ANSI C (C99) para maximizar a portabilidade. Esta funcionalidade torna as bibliotecas bem adaptadas para operar em várias plataformas e dispositivos, especialmente quando a minimização da pegada de disco e memória é uma prioridade.

Existe uma ampla gama de plataformas nas quais o SDK foi testado (consulte o catálogo de [dispositivos Azure Certified for IoT](https://catalog.azureiotsolutions.com/) para mais detalhes). Embora este artigo inclua a passagem de código de amostra em execução na plataforma Windows, o código descrito neste artigo é idêntico em todas as gamas de plataformas suportadas.

O vídeo a seguir apresenta uma visão geral do Azure IoT SDK para C:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-IoT-C-SDK-insights/Player]

Este artigo apresenta-lhe a arquitetura do dispositivo Azure IoT SDK para C. Demonstra como inicializar a biblioteca do dispositivo, enviar dados para o IoT Hub e receber mensagens do mesmo. As informações neste artigo devem ser suficientes para começar a usar o SDK, mas também fornece indicações para informações adicionais sobre as bibliotecas.

## <a name="sdk-architecture"></a>Arquitetura SDK

Pode encontrar o [**dispositivo Azure IoT SDK para**](https://github.com/Azure/azure-iot-sdk-c) o repositório C GitHub e ver detalhes da API na [referência C API](/azure/iot-hub/iot-c-sdk-ref/).

A versão mais recente das bibliotecas pode ser encontrada no ramo **principal** do repositório:

  ![Screenshot do ramo principal do repositório](./media/iot-hub-device-sdk-c-intro/RepoMasterBranch.png)

* A implementação central do SDK está na pasta do **\_ cliente iothub** que contém a implementação da camada API mais baixa na SDK: a biblioteca **IoTHubClient.** A biblioteca **IoTHubClient** contém APIs implementando mensagens cruas para enviar mensagens para o IoT Hub e receber mensagens do IoT Hub. Ao utilizar esta biblioteca, é responsável pela implementação da serialização de mensagens, mas outros detalhes de comunicação com o IoT Hub são tratados por si.

* A pasta **serializadora** contém funções de ajudante e amostras que mostram como serializar dados antes de enviar para o Azure IoT Hub usando a biblioteca do cliente. A utilização do serializador não é obrigatória e é fornecida como uma conveniência. Para utilizar a biblioteca **serializadora,** define um modelo que especifica os dados a enviar para o IoT Hub e as mensagens que espera receber do mesmo. Uma vez definido o modelo, o SDK fornece-lhe uma superfície API que lhe permite trabalhar facilmente com mensagens de dispositivo-nuvem e nuvem-para-dispositivo sem se preocupar com os detalhes da serialização. A biblioteca depende de outras bibliotecas de código aberto que implementem o transporte usando protocolos como MQTT e AMQP.

* A biblioteca **IoTHubClient** depende de outras bibliotecas de código aberto:

  * A biblioteca [de utilidade partilhada Azure C,](https://github.com/Azure/azure-c-shared-utility) que fornece uma funcionalidade comum para tarefas básicas (tais como cordas, manipulação de listas e IO) necessárias em vários SDKs relacionados com a Azure.

  * A biblioteca [Azure uAMQP,](https://github.com/Azure/azure-uamqp-c) que é uma implementação do lado do cliente de AMQP otimizada para dispositivos restritos de recursos.

  * A biblioteca [Azure uMQTT,](https://github.com/Azure/azure-umqtt-c) que é uma biblioteca de uso geral implementando o protocolo MQTT e otimizada para dispositivos restritos de recursos.

A utilização destas bibliotecas é mais fácil de entender olhando para o código de exemplo. As secções seguintes acompanham-no através de várias das aplicações de amostra que estão incluídas no SDK. Este walkthrough deve dar-lhe uma boa sensação para as várias capacidades das camadas arquitetónicas do SDK e uma introdução ao funcionação das APIs.

## <a name="before-you-run-the-samples"></a>Antes de fazer as amostras.

Antes de poder executar as amostras no dispositivo Azure IoT SDK para C, deve [criar uma instância do serviço IoT Hub](iot-hub-create-through-portal.md) na sua subscrição Azure. Em seguida, complete as seguintes tarefas:

* Preparar o ambiente de desenvolvimento
* Obtenha credenciais de dispositivo.

### <a name="prepare-your-development-environment"></a>Preparar o ambiente de desenvolvimento

Os pacotes são fornecidos para plataformas comuns (como NuGet para Windows ou apt_get para Debian e Ubuntu) e as amostras utilizam estes pacotes quando disponíveis. Em alguns casos, é necessário compilar o SDK para ou para o seu dispositivo. Se precisar de compilar o SDK, consulte Prepare o [seu ambiente de desenvolvimento](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) no repositório GitHub.

Para obter o código de aplicação da amostra, faça o download de uma cópia do SDK do GitHub. Obtenha a sua cópia da fonte do ramo **principal** do [repositório GitHub.](https://github.com/Azure/azure-iot-sdk-c)


### <a name="obtain-the-device-credentials"></a>Obtenha as credenciais do dispositivo

Agora que tens o código-fonte da amostra, a próxima coisa a fazer é obter um conjunto de credenciais do dispositivo. Para que um dispositivo possa aceder a um hub IoT, tem primeiro de adicionar o dispositivo ao registo de identidade IoT Hub. Ao adicionar o seu dispositivo, obtém um conjunto de credenciais do dispositivo de que necessita para que o dispositivo possa ligar-se ao hub IoT. As aplicações de amostra discutidas na secção seguinte esperam estas credenciais sob a forma de uma cadeia de **ligação** do dispositivo .

Existem várias ferramentas de código aberto para ajudá-lo a gerir o seu hub IoT.

* Uma aplicação do Windows chamada [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer).

* Uma extensão de Código estúdio visual transversal chamada [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

* Um Python CLI de plataforma cruzada chamou [a extensão IoT para Azure CLI](https://github.com/Azure/azure-iot-cli-extension).

Este tutorial utiliza a ferramenta de *explorador de dispositivos* gráficos. Pode utilizar as *Ferramentas Azure IoT para código VS* se desenvolver no Código VS. Também pode utilizar a extensão IoT para a ferramenta *Azure CLI 2.0* se preferir utilizar uma ferramenta CLI.

A ferramenta exploradora do dispositivo utiliza as bibliotecas de serviçoS Azure IoT para executar várias funções no IoT Hub, incluindo a adição de dispositivos. Se utilizar a ferramenta de explorador do dispositivo para adicionar um dispositivo, obtém uma cadeia de ligação para o seu dispositivo. Precisa desta cadeia de ligação para executar as aplicações da amostra.

Se não estiver familiarizado com a ferramenta de explorador do dispositivo, o seguinte procedimento descreve como usá-lo para adicionar um dispositivo e obter uma cadeia de ligação do dispositivo.

1. Para instalar a ferramenta de explorador do dispositivo, consulte [como utilizar o Explorador de Dispositivos para dispositivos IoT Hub](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/).

1. Quando executam o programa, vêem esta interface:

   ![Imagem de twin explorador de dispositivo](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinConfigTab.png)

1. Introduza a sua **cadeia de conexão IoT Hub** no primeiro campo e clique em **Update**. Este passo configura a ferramenta para que possa comunicar com o IoT Hub. 

A **cadeia de ligação** pode ser encontrada no **iothubowner IoT Hub Service**  >  **Settings** Shared Access  >  **Policy**  >  **iothubowner**.

1. Quando a cadeia de ligação IoT Hub estiver configurada, clique no **separador Gestão:**

   ![Dispositivo Explorer Twin / Imagem de gestão](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinManagementTab.png)

Este separador é o local onde gere os dispositivos registados no seu hub IoT.

1. Cria-se um dispositivo clicando no botão **Criar.** Um diálogo apresenta um conjunto de teclas pré-povoadas (primária e secundária). Introduza um **ID do dispositivo** e clique em **Criar**.

   ![Criar screenshot do dispositivo](./media/iot-hub-device-sdk-c-intro/CreateDevice.png)

1. Quando o dispositivo é criado, a lista de Dispositivos atualiza com todos os dispositivos registados, incluindo o que acabaste de criar. Se clicar no seu novo dispositivo, consulte este menu:

   ![Resultado do clique direito do Explorador do Dispositivo](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinManagementTab_RightClick.png)

1. Se escolher **a cadeia de ligação Copy para dispositivo selecionado,** a cadeia de ligação do dispositivo é copiada para a área de transferência. Guarde uma cópia da cadeia de ligação do dispositivo. Necessita-o ao executar as aplicações de amostra descritas nas seguintes secções.

Quando completar os passos acima, está pronto para começar a executar algum código. A maioria das amostras tem uma constante na parte superior do ficheiro de origem principal que lhe permite introduzir uma cadeia de ligação. Por exemplo, a linha correspondente a partir da **iothub_client \_ amostras \_ iothub_convenience_sample** aplicação aparece da seguinte forma.

```c
static const char* connectionString = "[device connection string]";
```

## <a name="use-the-iothubclient-library"></a>Use a biblioteca IoTHubClient

Dentro da pasta do **\_ cliente iothub** no repositório [azure-iot-sdk-c,](https://github.com/azure/azure-iot-sdk-c) existe uma pasta **de amostras** que contém uma aplicação chamada **\_ iothub amostra de cliente \_ \_ mqtt**.

A versão Windows da **iothub_client \_ amostras \_ iothub_convenience_sample** aplicação inclui a seguinte solução Visual Studio:

  ![Explorador de soluções de estúdio visual](./media/iot-hub-device-sdk-c-intro/iothub-client-sample-mqtt.png)

> [!NOTE]
> Se o Visual Studio lhe pedir para retar o projeto para a versão mais recente, aceite o pedido.

Esta solução contém um único projeto. Existem quatro pacotes NuGet instalados nesta solução:

* Microsoft.Azure.C.SharedUtility
* Microsoft.Azure.IoTHub.MqttTransport
* Microsoft.Azure.ioTHub.ioTHubClient
* Microsoft.Azure.umqtt

Precisa sempre do pacote **Microsoft.Azure.C.SharedUtility** quando está a trabalhar com o SDK. Esta amostra utiliza o protocolo MQTT, pelo que deve incluir os pacotes **Microsoft.Azure.umqtt** e **Microsoft.Azure.IoTHub.MqttTransport** (existem pacotes equivalentes para AMQP e HTTPS). Uma vez que a amostra utiliza a biblioteca **IoTHubClient,** também deve incluir o pacote **Microsoft.Azure.IoTHub.IoTHubClient** na sua solução.

Pode encontrar a implementação do pedido de amostra no **iothub_client \_ amostras \_ iothub_convenience_sample** ficheiro de origem.

Os passos seguintes usam esta aplicação de amostra para o acompanhar através do que é necessário para usar a biblioteca **IoTHubClient.**

### <a name="initialize-the-library"></a>Inicializar a biblioteca

> [!NOTE]
> Antes de começar a trabalhar com as bibliotecas, poderá ter de realizar alguma inicialização específica da plataforma. Por exemplo, se planeia utilizar AMQP no Linux, tem de rubricar a biblioteca OpenSSL. As amostras do [repositório GitHub](https://github.com/Azure/azure-iot-sdk-c) ligam para a plataforma de função de utilidade quando o cliente começa e ligam para a função **de \_ deinit da plataforma** antes de sair. **\_** Estas funções são declaradas no ficheiro cabeçalho da plataforma.h. Examine as definições destas funções para a sua plataforma-alvo no [repositório](https://github.com/Azure/azure-iot-sdk-c) para determinar se precisa de incluir qualquer código de inicialização específico da plataforma no seu cliente.

Para começar a trabalhar com as bibliotecas, primeiro aloque um manípulo de cliente IoT Hub:

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

Passa uma cópia da cadeia de ligação do dispositivo que obteve da ferramenta exploradora do dispositivo para esta função. Também designa o protocolo de comunicações a utilizar. Este exemplo utiliza MQTT, mas AMQP e HTTPS também são opções.

Quando tiver um **\_ \_ MANÍpulo cliente IOTHUB** válido, pode começar a ligar para as APIs para enviar e receber mensagens de e para o IoT Hub.

### <a name="send-messages"></a>Enviar mensagens

A aplicação da amostra configura um loop para enviar mensagens para o seu hub IoT. O seguinte corte:

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

Sempre que envia uma mensagem, especifica uma referência a uma função de retorno que é invocada quando os dados são enviados. Neste exemplo, a função de retorno chama-se **SendConfirmationCallback**. O seguinte corte mostra esta função de retorno:

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

Note a chamada para a função **IoTHubMessage \_ Destroy** quando terminar a mensagem. Esta função liberta os recursos atribuídos quando criou a mensagem.

### <a name="receive-messages"></a>Receber mensagens

Receber uma mensagem é uma operação assíncronea. Primeiro, regista a chamada para invocar quando o dispositivo recebe uma mensagem:

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

O último parâmetro é um ponteiro vazio para o que quiser. Na amostra, é um ponteiro para um inteiro, mas pode ser um ponteiro para uma estrutura de dados mais complexa. Este parâmetro permite que a função de retorno funcione em estado partilhado com o chamador desta função.

Quando o dispositivo recebe uma mensagem, a função de retorno registado é invocada. Esta função de retorno recupera:

* A identificação da mensagem e a identificação da correlação da mensagem.
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

Utilize a função **IoTHubMessage \_ GetByteArray** para recuperar a mensagem, que neste exemplo é uma corda.

### <a name="uninitialize-the-library"></a>Uninitialize a biblioteca

Quando terminar de enviar eventos e receber mensagens, pode não ter uma biblioteca IoT. Para tal, emite a seguinte chamada de função:

```c
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

Esta chamada liberta os recursos anteriormente atribuídos pela função **IoTHubClient \_ CreateFromConnectionString.**

Como pode ver, é fácil enviar e receber mensagens com a biblioteca **IoTHubClient.** A biblioteca trata dos detalhes da comunicação com o IoT Hub, incluindo qual o protocolo a utilizar (na perspetiva do desenvolvedor, esta é uma opção de configuração simples).

A biblioteca **IoTHubClient** também fornece um controlo preciso sobre como serializar os dados que o seu dispositivo envia para o IoT Hub. Em alguns casos, este nível de controlo é uma vantagem, mas em outros é um detalhe de implementação com o qual não quer se preocupar. Se for esse o caso, pode considerar a utilização da biblioteca **serializadora,** que é descrita na secção seguinte.

## <a name="use-the-serializer-library"></a>Use a biblioteca serializadora

Conceptualmente, a biblioteca **serializer** fica no topo da biblioteca **IoTHubClient** no SDK. Utiliza a biblioteca **IoTHubClient** para a comunicação subjacente com o IoT Hub, mas adiciona capacidades de modelação que removem o fardo de lidar com a serialização da mensagem do desenvolvedor. Como funciona esta biblioteca é melhor demonstrado por um exemplo.

Dentro da pasta **serializadora** no [repositório azure-iot-sdk-c , encontra-se](https://github.com/Azure/azure-iot-sdk-c)uma pasta de **amostras** que contém uma aplicação chamada **simples \_ mqtt**. A versão Windows desta amostra inclui a seguinte solução Visual Studio:

  ![Solução de estúdio visual para amostra de mqtt](./media/iot-hub-device-sdk-c-intro/simplesample_mqtt.png)

> [!NOTE]
> Se o Visual Studio lhe pedir para retar o projeto para a versão mais recente, aceite o pedido.

Tal como na amostra anterior, este inclui vários pacotes NuGet:

* Microsoft.Azure.C.SharedUtility
* Microsoft.Azure.IoTHub.MqttTransport
* Microsoft.Azure.ioTHub.ioTHubClient
* Microsoft.Azure.IoTHub.Serializer
* Microsoft.Azure.umqtt

Já viu a maioria destes pacotes na amostra anterior, mas **microsoft.Azure.ioTHub.Serializer** é novo. Esta embalagem é necessária quando utilizar a biblioteca **serializadora.**

Pode encontrar a implementação do pedido de amostra no **ficheiro iothub_client \_ amostras \_ iothub_convenience_sample.**

As seguintes secções acompanham-no através das partes-chave desta amostra.

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

A chamada para a função **\_ init serializer** é uma chamada única e inicializa a biblioteca subjacente. Em seguida, você chama a função **IoTHubClient \_ LL \_ CreateFromConnectionString,** que é a mesma API que na amostra **IoTHubClient.** Esta chamada define a cadeia de ligação do dispositivo (esta chamada é também onde escolhe o protocolo que pretende utilizar). Esta amostra utiliza o MQTT como transporte, mas pode utilizar AMQP ou HTTPS.

Por fim, ligue para a função **EXEMPLO \_ DE MODELO \_ CREATE.** **WeatherStation** é o espaço de nome do modelo e **ContosoAnemometer** é o nome do modelo. Uma vez criada a instância do modelo, pode usá-la para começar a enviar e receber mensagens. No entanto, é importante entender o que é um modelo.

### <a name="define-the-model"></a>Definir o modelo

Um modelo na biblioteca **serializer** define as mensagens que o seu dispositivo pode enviar para o IoT Hub e as mensagens, chamadas *ações* no idioma de modelação, que pode receber. Define-se um modelo utilizando um conjunto de macros C como nas **amostras iothub_client \_ \_ iothub_convenience_sample** aplicação da amostra:

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

As macros **START \_ NAMESPACE** e **END \_ NAMESPACE** tomam o espaço de nome do modelo como argumento. Espera-se que qualquer coisa entre estas macros seja a definição do seu modelo ou modelo, e as estruturas de dados que os modelos usam.

Neste exemplo, há um único modelo chamado **ContosoAnemometer**. Este modelo define duas peças de dados que o seu dispositivo pode enviar para o IoT Hub: **DeviceId** e **WindSpeed**. Também define três ações (mensagens) que o seu dispositivo pode receber: **TurnFanOn,** **TurnFanOff** e **SetAirResistance**. Cada elemento de dados tem um tipo, e cada ação tem um nome (e opcionalmente um conjunto de parâmetros).

Os dados e ações definidos no modelo definem uma superfície API que pode utilizar para enviar mensagens para o IoT Hub e responder às mensagens enviadas para o dispositivo. A utilização deste modelo é melhor entendida através de um exemplo.

### <a name="send-messages"></a>Enviar mensagens

O modelo define os dados que pode enviar para o IoT Hub. Neste exemplo, significa um dos dois itens de dados definidos usando o **WITH_DATA** macro. Existem vários passos necessários para enviar valores **DeviceId** e **WindSpeed** para um hub IoT. A primeira é definir os dados que pretende enviar:

```c
myWeather->DeviceId = "myFirstDevice";
myWeather->WindSpeed = avgWindSpeed + (rand() % 4 + 2);
```

O modelo que definiu anteriormente permite-lhe definir os valores definindo os membros de uma **estrutura**. Em seguida, serialize a mensagem que deseja enviar:

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

Este código serializa o dispositivo-a-nuvem para um tampão (referenciado por **destino).** Em seguida, o código invoca a função **de mensagens enviadas** para enviar a mensagem para o IoT Hub:

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

O segundo e último parâmetro do **IoTHubClient \_ LL \_ SendEventAsync** é uma referência a uma função de retorno que é chamada quando os dados são enviados com sucesso. Aqui está a função de retorno na amostra:

```c
void sendCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    unsigned int messageTrackingId = (unsigned int)(uintptr_t)userContextCallback;

    (void)printf("Message Id: %u Received.\r\n", messageTrackingId);

    (void)printf("Result Call Back Called! Result is: %s \r\n", MU_ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
}
```

O segundo parâmetro é um ponteiro para o contexto do utilizador; o mesmo ponteiro passou para **IoTHubClient \_ LL \_ SendEventAsync**. Neste caso, o contexto é um contador simples, mas pode ser o que quiser.

É tudo o que há para enviar mensagens de dispositivo para nuvem. A única coisa que resta a cobrir é como receber mensagens.

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

Em seguida, escreve-se a função de retorno que é invocada quando uma mensagem é recebida:

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

Este código é a placa de caldeira. Esta função recebe a mensagem e cuida de encaminhá-la para a função adequada através da chamada para **EXECUTAR \_ COMANDO**. A função chamada neste momento depende da definição das ações no seu modelo.

Quando define uma ação no seu modelo, é-lhe exigido que implemente uma função chamada quando o seu dispositivo recebe a mensagem correspondente. Por exemplo, se o seu modelo definir esta ação:

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

### <a name="uninitialize-the-library"></a>Uninitialize a biblioteca

Quando terminar de enviar dados e receber mensagens, pode não ter uma biblioteca IoT:

```c
...
        DESTROY_MODEL_INSTANCE(myWeather);
    }
    IoTHubClient_LL_Destroy(iotHubClientHandle);
}
serializer_deinit();
```

Cada uma destas três funções alinha-se com as três funções de inicialização descritas anteriormente. Chamar estas APIs garante que você livre recursos previamente alocados.

## <a name="next-steps"></a>Passos Seguintes

Este artigo abrangeu os fundamentos da utilização das bibliotecas no **dispositivo Azure IoT SDK para C**. Forneceu-lhe informações suficientes para entender o que está incluído no SDK, a sua arquitetura e como começar a trabalhar com as amostras do Windows. O próximo artigo continua a descrição do SDK explicando [mais sobre a biblioteca IoTHubClient](iot-hub-device-sdk-c-iothubclient.md).

Para saber mais sobre o desenvolvimento para o IoT Hub, consulte os [Azure IoT SDKs.](iot-hub-devguide-sdks.md)

Para explorar ainda mais as capacidades do IoT Hub, consulte:

* [Implementar o AI em dispositivos de ponta com o Azure IoT Edge](../iot-edge/quickstart-linux.md)
