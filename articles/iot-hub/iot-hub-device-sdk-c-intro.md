---
title: O SDK do dispositivo IoT do Azure para C | Microsoft Docs
description: Introdução ao SDK do dispositivo IoT do Azure para C e saiba como criar aplicativos de dispositivo que se comunicam com um hub IoT.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: robinsh
ms.openlocfilehash: 1c1921391048fc59f03070d4753f422d9cfc5237
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883486"
---
# <a name="azure-iot-device-sdk-for-c"></a>SDK do dispositivo IoT do Azure para C

O **SDK do dispositivo IOT do Azure** é um conjunto de bibliotecas criadas para simplificar o processo de envio e recebimento de mensagens do serviço **Hub IOT do Azure** . Há diferentes variações do SDK, cada uma direcionada para uma plataforma específica, mas este artigo descreve o **SDK do dispositivo IOT do Azure para C**.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

O SDK do dispositivo IoT do Azure para C é escrito em ANSI C (C99) para maximizar a portabilidade. Esse recurso torna as bibliotecas adequadas para operar em várias plataformas e dispositivos, especialmente onde minimizar a superfície de disco e memória é uma prioridade.

Há uma ampla variedade de plataformas nas quais o SDK foi testado (consulte o catálogo de [dispositivos Azure Certified para IOT](https://catalog.azureiotsolutions.com/) para obter detalhes). Embora este artigo inclua orientações de código de exemplo em execução na plataforma Windows, o código descrito neste artigo é idêntico em toda a variedade de plataformas com suporte.

O vídeo a seguir apresenta uma visão geral do SDK do IoT do Azure para C:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-IoT-C-SDK-insights/Player]

Este artigo apresenta a arquitetura do SDK do dispositivo IoT do Azure para C. Ele demonstra como inicializar a biblioteca de dispositivos, enviar dados para o Hub IoT e receber mensagens dele. As informações neste artigo devem ser suficientes para começar a usar o SDK, mas também fornecem ponteiros para informações adicionais sobre as bibliotecas.

## <a name="sdk-architecture"></a>Arquitetura do SDK

Você pode encontrar o [**SDK do dispositivo IOT do Azure para**](https://github.com/Azure/azure-iot-sdk-c) o repositório do GitHub c e exibir detalhes da API na [referência da API C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/).

A versão mais recente das bibliotecas pode ser encontrada na ramificação **mestre** do repositório:

  ![Captura de tela da ramificação mestre do repositório](./media/iot-hub-device-sdk-c-intro/RepoMasterBranch.png)

* A implementação principal do SDK está na pasta **\_iothub Client** que contém a implementação da camada de API mais baixa no SDK: a biblioteca **IoTHubClient** . A biblioteca **IoTHubClient** contém APIs que implementam mensagens brutas para enviar mensagens ao Hub IOT e receber mensagens do Hub IOT. Ao usar essa biblioteca, você é responsável pela implementação da serialização de mensagens, mas outros detalhes da comunicação com o Hub IoT são tratados para você.

* A pasta serializador contém funções auxiliares e exemplos que mostram como serializar dados antes de enviar para o Hub IOT do Azure usando a biblioteca de cliente. O uso do serializador não é obrigatório e é fornecido como uma conveniência. Para usar a biblioteca do serializador, você define um modelo que especifica os dados a serem enviados ao Hub IOT e as mensagens que você espera receber dele. Depois que o modelo é definido, o SDK fornece uma superfície de API que permite que você trabalhe facilmente com mensagens do dispositivo para a nuvem e da nuvem para o dispositivo sem se preocupar com os detalhes de serialização. A biblioteca depende de outras bibliotecas de código aberto que implementam o transporte usando protocolos como MQTT e AMQP.

* A biblioteca **IoTHubClient** depende de outras bibliotecas de código aberto:

  * A biblioteca de [utilitários compartilhados do Azure C](https://github.com/Azure/azure-c-shared-utility) , que fornece funcionalidade comum para tarefas básicas (como cadeias de caracteres, manipulação de lista e e/s) necessárias em vários SDKs do C relacionados ao Azure.

  * A biblioteca [uAMQP do Azure](https://github.com/Azure/azure-uamqp-c) , que é uma implementação do lado do cliente do AMQP otimizado para dispositivos com restrição de recursos.

  * A biblioteca [UMQTT do Azure](https://github.com/Azure/azure-umqtt-c) , que é uma biblioteca de finalidade geral que implementa o protocolo MQTT e otimizada para dispositivos com restrição de recursos.

O uso dessas bibliotecas é mais fácil de entender olhando o código de exemplo. As seções a seguir orientam você por vários dos aplicativos de exemplo incluídos no SDK. Este passo a passos deve fornecer uma boa ideia para os vários recursos das camadas arquitetônicas do SDK e uma introdução à forma como as APIs funcionam.

## <a name="before-you-run-the-samples"></a>Antes de executar os exemplos

Antes de executar os exemplos no SDK do dispositivo IoT do Azure para C, você deve [criar uma instância do serviço Hub IOT](iot-hub-create-through-portal.md) em sua assinatura do Azure. Em seguida, conclua as seguintes tarefas:

* Preparar o ambiente de desenvolvimento
* Obtenha as credenciais do dispositivo.

### <a name="prepare-your-development-environment"></a>Preparar o ambiente de desenvolvimento

Os pacotes são fornecidos para plataformas comuns (como NuGet para Windows ou apt_get para Debian e Ubuntu) e os exemplos usam esses pacotes quando disponíveis. Em alguns casos, você precisa compilar o SDK para o ou no seu dispositivo. Se você precisar compilar o SDK, consulte [preparar seu ambiente de desenvolvimento](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) no repositório github.

Para obter o código do aplicativo de exemplo, Baixe uma cópia do SDK do GitHub. Obtenha sua cópia da origem do Branch **mestre** do [repositório do GitHub](https://github.com/Azure/azure-iot-sdk-c).


### <a name="obtain-the-device-credentials"></a>Obter as credenciais do dispositivo

Agora que você tem o código-fonte de exemplo, a próxima coisa a fazer é obter um conjunto de credenciais de dispositivo. Para que um dispositivo possa acessar um hub IoT, primeiro você deve adicionar o dispositivo ao registro de identidade do Hub IoT. Ao adicionar seu dispositivo, você obtém um conjunto de credenciais de dispositivo necessárias para que o dispositivo seja capaz de se conectar ao Hub IoT. Os aplicativos de exemplo abordados na próxima seção esperam essas credenciais na forma de uma **cadeia de conexão de dispositivo**.

Há várias ferramentas de código-fonte aberto para ajudá-lo a gerenciar seu hub IoT.

* Um aplicativo do Windows chamado [Gerenciador de dispositivos](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer).

* Uma extensão de Visual Studio Code de plataforma cruzada chamada [ferramentas de IOT do Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

* Uma CLI do Python de plataforma cruzada chamada [de extensão de IOT para CLI do Azure](https://github.com/Azure/azure-iot-cli-extension).

Este tutorial usa a ferramenta gráfica do *Gerenciador de dispositivos* . Você pode usar as *ferramentas de IOT do Azure para vs Code* se desenvolver no vs Code. Você também pode usar a *extensão de IOT para a ferramenta CLI do Azure 2,0* se preferir usar uma ferramenta CLI.

A ferramenta Gerenciador de dispositivos usa as bibliotecas de serviço IoT do Azure para executar várias funções no Hub IoT, incluindo a adição de dispositivos. Se você usar a ferramenta Gerenciador de dispositivos para adicionar um dispositivo, obterá uma cadeia de conexão para seu dispositivo. Você precisa dessa cadeia de conexão para executar os aplicativos de exemplo.

Se você não estiver familiarizado com a ferramenta Gerenciador de dispositivos, o procedimento a seguir descreve como usá-lo para adicionar um dispositivo e obter uma cadeia de conexão de dispositivo.

1. Para instalar a ferramenta Gerenciador de dispositivos, consulte [como usar o Device Explorer para dispositivos do Hub IOT](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer).

1. Ao executar o programa, você verá esta interface:

   ![Captura de tela do Device Explorer](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinConfigTab.png)

1. Insira a **cadeia de conexão do Hub IOT** no primeiro campo e clique em **Atualizar**. Esta etapa configura a ferramenta para que ela possa se comunicar com o Hub IoT. 

A **cadeia de conexão** pode ser encontrada em**configurações** > do **serviço** > de Hub IOT**política** > de acesso compartilhado**iothubowner**.

1. Quando a cadeia de conexão do Hub IoT estiver configurada, clique na guia **Gerenciamento** :

   ![Captura de tela de Device Explorer//gerenciamento](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinManagementTab.png)

Essa guia é onde você gerencia os dispositivos registrados em seu hub IoT.

1. Você cria um dispositivo clicando no botão **criar** . Uma caixa de diálogo é exibida com um conjunto de chaves preenchidas previamente (primária e secundária). Insira uma **ID do dispositivo** e clique em **criar**.

   ![Criar captura de tela do dispositivo](./media/iot-hub-device-sdk-c-intro/CreateDevice.png)

1. Quando o dispositivo é criado, a lista de dispositivos é atualizada com todos os dispositivos registrados, incluindo aquele que você acabou de criar. Se você clicar com o botão direito do mouse no novo dispositivo, verá este menu:

   ![Device Explorer, clique com o botão direito do mouse em resultado](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinManagementTab_RightClick.png)

1. Se você escolher **copiar cadeia de conexão para o dispositivo selecionado**, a cadeia de conexão do dispositivo será copiada para a área de transferência. Mantenha uma cópia da cadeia de conexão do dispositivo. Você precisará dela ao executar os aplicativos de exemplo descritos nas seções a seguir.

Depois de concluir as etapas acima, você estará pronto para começar a executar algum código. A maioria dos exemplos tem uma constante na parte superior do arquivo de origem principal que permite que você insira uma cadeia de conexão. Por exemplo, a linha correspondente do aplicativo **iothub_client\_Samples\_iothub_convenience_sample** aparece da seguinte maneira.

```c
static const char* connectionString = "[device connection string]";
```

## <a name="use-the-iothubclient-library"></a>Usar a biblioteca IoTHubClient

Na pasta **iothub\_Client** no repositório [Azure-IOT-SDK-c](https://github.com/azure/azure-iot-sdk-c) , há uma pasta Samples que contém um aplicativo chamado **iothub\_Client\_Sample\_MQTT** .

A versão do Windows do **aplicativo\_iothub_client\_Samples iothub_convenience_sample** inclui a seguinte solução do Visual Studio:

  ![Gerenciador de Soluções do Visual Studio](./media/iot-hub-device-sdk-c-intro/iothub-client-sample-mqtt.png)

> [!NOTE]
> Se o Visual Studio solicitar que você redirecione o projeto para a versão mais recente, aceite o prompt.

Essa solução contém um único projeto. Há quatro pacotes NuGet instalados nesta solução:

* Microsoft.Azure.C.SharedUtility
* Microsoft.Azure.IoTHub.MqttTransport
* Microsoft.Azure.IoTHub.IoTHubClient
* Microsoft.Azure.umqtt

Você sempre precisa do pacote **Microsoft. Azure. C. SharedUtility** quando estiver trabalhando com o SDK. Este exemplo usa o protocolo MQTT, portanto, você deve incluir os pacotes **Microsoft. Azure. umqtt** e **Microsoft. Azure. IoTHub. MqttTransport** (há pacotes equivalentes para AMQP e HTTPS). Como o exemplo usa a biblioteca **IoTHubClient** , você também deve incluir o pacote **Microsoft. Azure. IoTHub. IoTHubClient** em sua solução.

Você pode encontrar a implementação do aplicativo de exemplo no arquivo **de\_origem\_iothub_client Samples iothub_convenience_sample** .

As etapas a seguir usam esse aplicativo de exemplo para orientá-lo sobre o que é necessário para usar a biblioteca **IoTHubClient** .

### <a name="initialize-the-library"></a>Inicializar a biblioteca

> [!NOTE]
> Antes de começar a trabalhar com as bibliotecas, talvez seja necessário executar uma inicialização específica da plataforma. Por exemplo, se você planeja usar o AMQP no Linux, deverá inicializar a biblioteca OpenSSL. Os exemplos no [repositório do GitHub](https://github.com/Azure/azure-iot-sdk-c) chamam a função do utilitário **init da plataforma\_** quando o cliente inicia e chama a função de desinicialização da **plataforma\_** antes de sair. Essas funções são declaradas no arquivo de cabeçalho Platform. h. Examine as definições dessas funções para sua plataforma de destino no [repositório](https://github.com/Azure/azure-iot-sdk-c) para determinar se você precisa incluir qualquer código de inicialização específico da plataforma em seu cliente.

Para começar a trabalhar com as bibliotecas, primeiro aloque um identificador de cliente do Hub IoT:

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

Você passa uma cópia da cadeia de conexão do dispositivo obtida da ferramenta Gerenciador de dispositivos para essa função. Você também designa o protocolo de comunicação a ser usado. Este exemplo usa MQTT, mas AMQP e HTTPS também são opções.

Quando você tiver um **identificador de\_cliente\_IOTHUB**válido, poderá começar a chamar as APIs para enviar e receber mensagens de e para o Hub IOT.

### <a name="send-messages"></a>Enviar mensagens

O aplicativo de exemplo configura um loop para enviar mensagens para o Hub IoT. O trecho a seguir:

- Cria uma mensagem.
- Adiciona uma propriedade à mensagem.
- Envia uma mensagem.

Primeiro, crie uma mensagem:

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

Sempre que você envia uma mensagem, você especifica uma referência a uma função de retorno de chamada que é invocada quando os dados são enviados. Neste exemplo, a função de retorno de chamada é chamada **SendConfirmationCallback**. O trecho a seguir mostra essa função de retorno de chamada:

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

Observe a chamada para a **função\_IoTHubMessage Destroy** quando você terminar a mensagem. Essa função libera os recursos alocados quando você criou a mensagem.

### <a name="receive-messages"></a>Receber mensagens

O recebimento de uma mensagem é uma operação assíncrona. Primeiro, você registra o retorno de chamada para invocar quando o dispositivo recebe uma mensagem:

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

O último parâmetro é um ponteiro void para o que for desejado. No exemplo, é um ponteiro para um inteiro, mas pode ser um ponteiro para uma estrutura de dados mais complexa. Esse parâmetro permite que a função de retorno de chamada opere no estado compartilhado com o chamador dessa função.

Quando o dispositivo recebe uma mensagem, a função de retorno de chamada registrada é invocada. Essa função de retorno de chamada recupera:

* A ID da mensagem e a ID de correlação da mensagem.
* O conteúdo da mensagem.
* Todas as propriedades personalizadas da mensagem.

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

Use a **função\_IoTHubMessage GetByteArray** para recuperar a mensagem, que neste exemplo é uma cadeia de caracteres.

### <a name="uninitialize-the-library"></a>Não inicializar a biblioteca

Quando terminar de enviar eventos e receber mensagens, você poderá cancelar a inicialização da biblioteca IoT. Para fazer isso, emita a seguinte chamada de função:

```c
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

Essa chamada libera os recursos alocados anteriormente pela função **IoTHubClient\_CreateFromConnectionString** .

Como você pode ver, é fácil enviar e receber mensagens com a biblioteca **IoTHubClient** . A biblioteca manipula os detalhes de comunicação com o Hub IoT, incluindo o protocolo a ser usado (da perspectiva do desenvolvedor, essa é uma opção de configuração simples).

A biblioteca **IoTHubClient** também fornece controle preciso sobre como serializar os dados que o dispositivo envia ao Hub IOT. Em alguns casos, esse nível de controle é uma vantagem, mas, em outros, é um detalhe de implementação com o qual você não deseja se preocupar. Se esse for o caso, você pode considerar o uso da biblioteca do serializador, que é descrita na próxima seção.

## <a name="use-the-serializer-library"></a>Usar a biblioteca do serializador

Conceitualmente, a biblioteca do serializador se encontra na parte superior da biblioteca **IoTHubClient** no SDK. Ele usa a biblioteca **IoTHubClient** para a comunicação subjacente com o Hub IOT, mas adiciona recursos de modelagem que removem a carga de lidar com a serialização de mensagens do desenvolvedor. A maneira como essa biblioteca funciona é melhor demonstrada por um exemplo.

Dentro da pasta do serializador no [repositório Azure-IOT-SDK-c](https://github.com/Azure/azure-iot-sdk-c), é uma pasta de **exemplos** que contém um aplicativo chamado **SimpleSample\_MQTT**. A versão do Windows deste exemplo inclui a seguinte solução do Visual Studio:

  ![Exemplo de solução do Visual Studio para MQTT](./media/iot-hub-device-sdk-c-intro/simplesample_mqtt.png)

> [!NOTE]
> Se o Visual Studio solicitar que você redirecione o projeto para a versão mais recente, aceite o prompt.

Assim como no exemplo anterior, este inclui vários pacotes NuGet:

* Microsoft.Azure.C.SharedUtility
* Microsoft.Azure.IoTHub.MqttTransport
* Microsoft.Azure.IoTHub.IoTHubClient
* Microsoft.Azure.IoTHub.Serializer
* Microsoft.Azure.umqtt

Você viu a maioria desses pacotes no exemplo anterior, mas o **Microsoft. Azure. IoTHub. serializador** é novo. Esse pacote é necessário quando você usa a biblioteca do serializador.

Você pode encontrar a implementação do aplicativo de exemplo no arquivo **iothub_client\_Samples\_iothub_convenience_sample** .

As seções a seguir orientam você pelas principais partes deste exemplo.

### <a name="initialize-the-library"></a>Inicializar a biblioteca

Para começar a trabalhar com a biblioteca do serializador, chame as APIs de inicialização:

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

A chamada para a função de **inicialização\_** do serializador é uma chamada única e inicializa a biblioteca subjacente. Em seguida, você chama **a\_função\_IoTHubClient ll CreateFromConnectionString** , que é a mesma API do exemplo **IoTHubClient** . Essa chamada define a cadeia de conexão do dispositivo (essa chamada também é onde você escolhe o protocolo que deseja usar). Este exemplo usa MQTT como transporte, mas pode usar AMQP ou HTTPS.

Por fim, chame **a\_função\_criar instância de modelo** . **WeatherStation** é o namespace do modelo e **ContosoAnemometer** é o nome do modelo. Depois que a instância do modelo for criada, você poderá usá-la para começar a enviar e receber mensagens. No entanto, é importante entender o que é um modelo.

### <a name="define-the-model"></a>Definir o modelo

Um modelo na biblioteca do serializador define as mensagens que o dispositivo pode enviar ao Hub IOT e as mensagens, chamadas *ações* na linguagem de modelagem, que pode receber. Você define um modelo usando um conjunto de macros C como no aplicativo de exemplo **\_iothub_client Samples\_iothub_convenience_sample** :

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

As macros **begin\_namespace** e **end\_namespace** usam o namespace do modelo como um argumento. Espera-se que qualquer coisa entre essas macros seja a definição de seu modelo ou modelos e as estruturas de dados usadas pelos modelos.

Neste exemplo, há um único modelo chamado **ContosoAnemometer**. Esse modelo define dois tipos de dados que seu dispositivo pode enviar para o Hub IoT: DeviceID e **WindSpeed**. Ele também define três ações (mensagens) que seu dispositivo pode receber: **TurnFanOn**, **TurnFanOff**e **SetAirResistance**. Cada elemento de dados tem um tipo, e cada ação tem um nome (e, opcionalmente, um conjunto de parâmetros).

Os dados e as ações definidos no modelo definem uma superfície de API que você pode usar para enviar mensagens ao Hub IoT e responder às mensagens enviadas para o dispositivo. O uso desse modelo é mais bem compreendido por meio de um exemplo.

### <a name="send-messages"></a>Enviar mensagens

O modelo define os dados que você pode enviar ao Hub IoT. Neste exemplo, isso significa um dos dois itens de dados definidos usando a macro **WITH_DATA** . Há várias etapas necessárias para enviar os valores de DeviceID e **WindSpeed** para um hub IOT. A primeira é definir os dados que você deseja enviar:

```c
myWeather->DeviceId = "myFirstDevice";
myWeather->WindSpeed = avgWindSpeed + (rand() % 4 + 2);
```

O modelo que você definiu anteriormente permite que você defina os valores definindo os membros de um **struct**. Em seguida, Serialize a mensagem que você deseja enviar:

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

Esse código serializa o dispositivo para a nuvem para um buffer (referenciado pelo **destino**). Em seguida, o código invoca a função **SendMessage** para enviar a mensagem ao Hub IOT:

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

O segundo parâmetro to Last do **IoTHubClient\_ll\_SendEventAsync** é uma referência a uma função de retorno de chamada que é chamada quando os dados são enviados com êxito. Aqui está a função de retorno de chamada no exemplo:

```c
void sendCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    unsigned int messageTrackingId = (unsigned int)(uintptr_t)userContextCallback;

    (void)printf("Message Id: %u Received.\r\n", messageTrackingId);

    (void)printf("Result Call Back Called! Result is: %s \r\n", ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
}
```

O segundo parâmetro é um ponteiro para o contexto do usuário; o mesmo ponteiro passado para **IoTHubClient\_ll\_SendEventAsync**. Nesse caso, o contexto é um contador simples, mas pode ser qualquer coisa que você desejar.

Isso é tudo o que é para enviar mensagens do dispositivo para a nuvem. A única coisa que resta abordar é como receber mensagens.

### <a name="receive-messages"></a>Receber mensagens

O recebimento de uma mensagem funciona de forma semelhante à maneira como as mensagens funcionam na biblioteca **IoTHubClient** . Primeiro, você registra uma função de retorno de chamada de mensagem:

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

Em seguida, você escreve a função de retorno de chamada que é invocada quando uma mensagem é recebida:

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

Esse código é clichê. ele é o mesmo para qualquer solução. Essa função recebe a mensagem e cuida de roteá-la para a função apropriada por meio da chamada **para\_executar o comando**. A função chamada neste ponto depende da definição das ações em seu modelo.

Quando você define uma ação em seu modelo, é necessário implementar uma função que é chamada quando o dispositivo recebe a mensagem correspondente. Por exemplo, se seu modelo definir esta ação:

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

Observe como o nome da função corresponde ao nome da ação no modelo e se os parâmetros da função correspondem aos parâmetros especificados para a ação. O primeiro parâmetro é sempre necessário e contém um ponteiro para a instância do seu modelo.

Quando o dispositivo recebe uma mensagem que corresponde a essa assinatura, a função correspondente é chamada. Portanto, além de ter que incluir o código clichê de **IoTHubMessage**, o recebimento de mensagens é apenas uma questão de definir uma função simples para cada ação definida em seu modelo.

### <a name="uninitialize-the-library"></a>Não inicializar a biblioteca

Quando terminar de enviar dados e receber mensagens, você poderá cancelar a inicialização da biblioteca IoT:

```c
...
        DESTROY_MODEL_INSTANCE(myWeather);
    }
    IoTHubClient_LL_Destroy(iotHubClientHandle);
}
serializer_deinit();
```

Cada uma dessas três funções se alinha com as três funções de inicialização descritas anteriormente. Chamar essas APIs garante que você libere recursos alocados anteriormente.

## <a name="next-steps"></a>Próximos Passos

Este artigo abordou as noções básicas do uso das bibliotecas no **SDK do dispositivo IOT do Azure para C**. Ele forneceu informações suficientes para entender o que está incluído no SDK, sua arquitetura e como começar a trabalhar com os exemplos do Windows. O próximo artigo continua a descrição do SDK explicando [mais sobre a biblioteca IoTHubClient](iot-hub-device-sdk-c-iothubclient.md).

Para saber mais sobre como desenvolver para o Hub IoT, confira [SDKs do IOT do Azure](iot-hub-devguide-sdks.md).

Para explorar ainda mais os recursos do Hub IoT, consulte:

* [Implantando ia em dispositivos de borda com Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
