---
title: O Azure IoT-device SDK para C - serializador | Documentos da Microsoft
description: Como utilizar a biblioteca de serializador no Azure IoT device SDK para C para criar aplicações de dispositivos que comunicam com um hub IoT.
author: yzhong94
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 09/06/2016
ms.author: yizhon
ms.openlocfilehash: 0a7e30be374ae5095e206ce0e519e51bb58f1f00
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60399274"
---
# <a name="azure-iot-device-sdk-for-c--more-about-serializer"></a>O Azure IoT device SDK para C – mais informações sobre o serializador

O primeiro artigo desta série introduzidas o [introdução ao Azure IoT device SDK para C](iot-hub-device-sdk-c-intro.md). O artigo seguinte proporciona uma descrição mais detalhada do [Azure IoT device SDK para C – o IoTHubClient](iot-hub-device-sdk-c-iothubclient.md). Este artigo termina a cobertura do SDK ao fornecer uma descrição mais detalhada do componente restante: o **serializador** biblioteca.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

O artigo introdutório descrito como utilizar o **serializador** biblioteca para enviar eventos para e receber mensagens do IoT Hub. Neste artigo, vamos expandir essa discussão, fornecendo uma explicação mais completa de como modelar os seus dados com o **serializador** idioma de macro. O artigo também inclui mais detalhes sobre como a biblioteca serializa mensagens (e, em alguns casos, como pode controlar o comportamento de serialização). Também descreveremos alguns parâmetros que pode modificar, determinam o tamanho dos modelos de que criar.

Por fim, o artigo revisits alguns tópicos abordados em artigos anteriores, como a mensagem e manipulação de propriedade. Como podemos irá Saiba de que esses trabalho de funcionalidades da mesma forma a utilizar o **serializador** biblioteca tal como com o **o IoTHubClient** biblioteca.

Tudo descrito neste artigo se baseia no **serializador** amostras do SDK. Se quiser acompanhar, consulte a **simplesample\_amqp** e **simplesample\_http** aplicações incluídas no Azure IoT device SDK para C.

Pode encontrar os [ **Azure IoT device SDK para C** ](https://github.com/Azure/azure-iot-sdk-c) GitHub repositório e exibir detalhes da API no [referência da API de C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/).

## <a name="the-modeling-language"></a>A linguagem de modelagem

O [Azure IoT device SDK para C](iot-hub-device-sdk-c-intro.md) artigo desta série introduzida a **Azure IoT device SDK para C** modelação de linguagem com o exemplo fornecido no **simplesample\_ amqp** aplicação:

```C
BEGIN_NAMESPACE(WeatherStation);

DECLARE_MODEL(ContosoAnemometer,
WITH_DATA(ascii_char_ptr, DeviceId),
WITH_DATA(double, WindSpeed),
WITH_ACTION(TurnFanOn),
WITH_ACTION(TurnFanOff),
WITH_ACTION(SetAirResistance, int, Position)
);

END_NAMESPACE(WeatherStation);
```

Como pode ver, a linguagem de modelagem é baseada em C macros. Começar a sua definição com sempre **BEGIN\_espaço de nomes** e sempre terminar com **final\_espaço de nomes**. É comum para mencionar o espaço de nomes para a sua empresa ou, tal como neste exemplo, o projeto que está trabalhando no.

O que acontece dentro do espaço de nomes são definições de modelo. Neste caso, há um único modelo para um anemometer. Mais uma vez, o modelo de nome pode ser qualquer coisa, mas, normalmente, o modelo com o nome para o dispositivo ou o tipo de dados que pretende para o exchange com o IoT Hub.  

Modelos contém uma definição dos eventos pode dar ao IoT Hub (a *dados*), bem como as mensagens que pode receber a partir do IoT Hub (a *ações*). Como pode ver no exemplo, os eventos têm um tipo e um nome; ações de ter um nome e parâmetros opcionais (cada um com um tipo).

O que não é demonstrado neste exemplo são os tipos de dados adicionais que são suportados pelo SDK. Iremos abranger isso a seguir.

> [!NOTE]
> IoT Hub refere-se aos dados de um dispositivo envia a ele como *eventos*, enquanto a linguagem de modelagem refere-se ao mesmo como *dados* (definido usando **WITH_DATA**). Da mesma forma, o IoT Hub refere-se aos dados que envia para os dispositivos como *mensagens*, enquanto a linguagem de modelagem refere-se ao mesmo como *ações* (definido usando **WITH_ACTION**). Lembre-se de que estes termos podem ser utilizados alternadamente neste artigo.
> 
> 

## <a name="supported-data-types"></a>Tipos de dados suportados

São suportados os seguintes tipos de dados na criação de modelos criadas com o **serializador** biblioteca:

| Type | Descrição |
| --- | --- |
| double |Double precisão número de vírgula flutuante |
| int |número inteiro de 32 bits |
| float |número de ponto flutuante de precisão única |
| long |número inteiro longo |
| int8\_t |número inteiro de 8 bits |
| int16\_t |número inteiro de 16 bits |
| int32\_t |número inteiro de 32 bits |
| int64\_t |número inteiro de 64 bits |
| bool |boolean |
| ascii\_char\_ptr |Cadeia de caracteres ASCII |
| EDM\_DATE\_TIME\_OFFSET |compensação de tempo de data |
| EDM\_GUID |GUID |
| EDM\_BINARY |binary |
| DECLARE\_STRUCT |Tipo de dados complexos |

Vamos começar com o último tipo de dados. O **DECLARE\_STRUCT** permite-lhe definir os tipos de dados complexos, que consistem em agrupamentos de outros tipos primitivos. Esses agrupamentos permitem-nos definir um modelo que tem esta aparência:

```C
DECLARE_STRUCT(TestType,
double, aDouble,
int, aInt,
float, aFloat,
long, aLong,
int8_t, aInt8,
uint8_t, auInt8,
int16_t, aInt16,
int32_t, aInt32,
int64_t, aInt64,
bool, aBool,
ascii_char_ptr, aAsciiCharPtr,
EDM_DATE_TIME_OFFSET, aDateTimeOffset,
EDM_GUID, aGuid,
EDM_BINARY, aBinary
);

DECLARE_MODEL(TestModel,
WITH_DATA(TestType, Test)
);
```

O nosso modelo contém um evento único de dados do tipo **TestType**. **TestType** é um tipo complexo que inclui vários membros, coletivamente demonstram os tipos primitivos suportados pela **serializador** linguagem de modelagem.

Com um modelo como este, podemos escrever código para enviar dados para o IoT Hub que é apresentado da seguinte forma:

```C
TestModel* testModel = CREATE_MODEL_INSTANCE(MyThermostat, TestModel);

testModel->Test.aDouble = 1.1;
testModel->Test.aInt = 2;
testModel->Test.aFloat = 3.0f;
testModel->Test.aLong = 4;
testModel->Test.aInt8 = 5;
testModel->Test.auInt8 = 6;
testModel->Test.aInt16 = 7;
testModel->Test.aInt32 = 8;
testModel->Test.aInt64 = 9;
testModel->Test.aBool = true;
testModel->Test.aAsciiCharPtr = "ascii string 1";

time_t now;
time(&now);
testModel->Test.aDateTimeOffset = GetDateTimeOffset(now);

EDM_GUID guid = { { 0x00, 0x01, 0x02, 0x03, 0x04, 0x05, 0x06, 0x07, 0x08, 0x09, 0x0A, 0x0B, 0x0C, 0x0D, 0x0E, 0x0F } };
testModel->Test.aGuid = guid;

unsigned char binaryArray[3] = { 0x01, 0x02, 0x03 };
EDM_BINARY binaryData = { sizeof(binaryArray), &binaryArray };
testModel->Test.aBinary = binaryData;

SendAsync(iotHubClientHandle, (const void*)&(testModel->Test));
```

Basicamente, estamos atribuindo um valor para cada membro do **teste** estrutura e, em seguida, chamar **SendAsync** para enviar o **teste** eventos de dados para a cloud. **SendAsync** é uma função auxiliar que envia um evento de dados individual para o IoT Hub:

```C
void SendAsync(IOTHUB_CLIENT_LL_HANDLE iotHubClientHandle, const void *dataEvent)
{
    unsigned char* destination;
    size_t destinationSize;
    if (SERIALIZE(&destination, &destinationSize, *(const unsigned char*)dataEvent) ==
    {
        // null terminate the string
        char* destinationAsString = (char*)malloc(destinationSize + 1);
        if (destinationAsString != NULL)
        {
            memcpy(destinationAsString, destination, destinationSize);
            destinationAsString[destinationSize] = '\0';
            IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromString(destinationAsString);
            if (messageHandle != NULL)
            {
                IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)0);

                IoTHubMessage_Destroy(messageHandle);
            }
            free(destinationAsString);
        }
        free(destination);
    }
}
```

Esta função serializa o evento de dados fornecido e envia-os para o IoT Hub com **o IoTHubClient\_SendEventAsync**. Este é o mesmo código discutido em artigos anteriores (**SendAsync** encapsula a lógica para uma função conveniente).

Uma função auxiliar utilizada no código anterior é **GetDateTimeOffset**. Esta função transforma o momento num valor do tipo **EDM\_data\_tempo\_deslocamento**:

```C
EDM_DATE_TIME_OFFSET GetDateTimeOffset(time_t time)
{
    struct tm newTime;
    gmtime_s(&newTime, &time);
    EDM_DATE_TIME_OFFSET dateTimeOffset;
    dateTimeOffset.dateTime = newTime;
    dateTimeOffset.fractionalSecond = 0;
    dateTimeOffset.hasFractionalSecond = 0;
    dateTimeOffset.hasTimeZone = 0;
    dateTimeOffset.timeZoneHour = 0;
    dateTimeOffset.timeZoneMinute = 0;
    return dateTimeOffset;
}
```

Se executar esse código, a seguinte mensagem é enviada para o IoT Hub:

```C
{"aDouble":1.100000000000000, "aInt":2, "aFloat":3.000000, "aLong":4, "aInt8":5, "auInt8":6, "aInt16":7, "aInt32":8, "aInt64":9, "aBool":true, "aAsciiCharPtr":"ascii string 1", "aDateTimeOffset":"2015-09-14T21:18:21Z", "aGuid":"00010203-0405-0607-0809-0A0B0C0D0E0F", "aBinary":"AQID"}
```

Tenha em atenção que a serialização é em JSON, que é o formato gerado pelos **serializador** biblioteca. Observe também que cada membro do objeto JSON serializado corresponda os membros do **TestType** que definimos em nosso modelo. Também exatamente os valores corresponderem aos utilizado no código. No entanto, tenha em atenção que os dados binários estão codificada em base64: "AQID" é o base64 codificação de {0x01, 0x02, 0x03}.

Este exemplo demonstra a vantagem de utilizar o **serializador** library-- permite-nos enviar JSON para a cloud, sem ter de lidar explicitamente com a serialização em nosso aplicativo. Tudo que temos de se preocupar é definir os valores dos eventos de dados em nosso modelo e, em seguida, chamar APIs simples para enviar esses eventos para a cloud.

Com essas informações, podemos definir modelos que incluem o intervalo de tipos de dados suportadas, incluindo tipos complexos (que pode até mesmo incluir tipos complexos dentro de outros tipos complexos). No entanto, o JSON serializado gerado pelo exemplo acima traz um ponto muito importante. *Como* enviamos dados com o **serializador** biblioteca determina exatamente como o JSON é formado. Desse ponto específico é o que vamos abordar a seguir.

## <a name="more-about-serialization"></a>Mais informações sobre a serialização

A secção anterior realça um exemplo da saída gerada pelos **serializador** biblioteca. Nesta secção, vamos explicar como a biblioteca serializa dados e como pode controlar esse comportamento usando a APIs de serialização.

Para avançar o debate sobre a serialização, trabalharemos com um novo modelo com base num termóstato. Em primeiro lugar, vamos fornecer algumas informações básicas sobre o cenário que estamos tentando endereço.

Queremos modelar um termóstato que mede a temperatura e humidade. Cada conjunto de dados vai ser enviadas ao IoT Hub forma diferente. Por predefinição, os ingresses termóstato um evento de temperatura a cada 2 minutos; um evento de humidade é ingressed uma vez a cada 15 minutos. Quando o evento for ingressed, tem de incluir um carimbo que mostra o tempo que a temperatura correspondente ou humidade foi medida.

Tendo em conta neste cenário, vamos demonstrar duas formas diferentes para modelar os dados, e vamos explicar o efeito que a Modelagem de tem o serializados de saída.

### <a name="model-1"></a>Modelo 1

Esta é a primeira versão de um modelo que suporta o cenário anterior:

```C
BEGIN_NAMESPACE(Contoso);

DECLARE_STRUCT(TemperatureEvent,
int, Temperature,
EDM_DATE_TIME_OFFSET, Time);

DECLARE_STRUCT(HumidityEvent,
int, Humidity,
EDM_DATE_TIME_OFFSET, Time);

DECLARE_MODEL(Thermostat,
WITH_DATA(TemperatureEvent, Temperature),
WITH_DATA(HumidityEvent, Humidity)
);

END_NAMESPACE(Contoso);
```

Tenha em atenção que o modelo inclui dois eventos de dados: **Temperatura** e **humidade**. Ao contrário dos exemplos anteriores, o tipo de cada evento é uma estrutura definida usando **DECLARE\_STRUCT**. **TemperatureEvent** inclui uma medida de temperatura e um timestamp **HumidityEvent** contém uma medida de humidade e um carimbo. Esse modelo oferece uma forma natural de modelar os dados para o cenário descrito acima. Quando podemos enviar um evento para a nuvem, iremos optar por enviar uma temperatura/timestamp ou um par de humidade/timestamp.

Podemos enviar um evento de temperatura à cloud através de código como o seguinte:

```C
time_t now;
time(&now);
thermostat->Temperature.Temperature = 75;
thermostat->Temperature.Time = GetDateTimeOffset(now);

unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Vamos usar valores embutidos de temperatura e humidade no código de exemplo, mas imagine que, na verdade, recuperando estes valores pelos sensores correspondentes no termóstato de amostragem.

O código acima utiliza a **GetDateTimeOffset** auxiliar introduzida anteriormente. Por motivos que se tornará claro posteriores, este código explicitamente separa a tarefa de serialização e enviar o evento. O código anterior serializa o evento de temperatura num buffer. Em seguida, **sendMessage** é uma função auxiliar (incluído no **simplesample\_amqp**) que envia o evento para o IoT Hub:

```C
static void sendMessage(IOTHUB_CLIENT_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
{
    static unsigned int messageTrackingId;
    IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
    if (messageHandle != NULL)
    {
        IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)(uintptr_t)messageTrackingId);

        IoTHubMessage_Destroy(messageHandle);
    }
    free((void*)buffer);
}
```

Esse código é um subconjunto do **SendAsync** auxiliar descrito na secção anterior, portanto, não percorreremos-lo novamente aqui.

Quando executamos o código anterior para enviar o evento de temperatura, este formato serializado do evento é enviado ao IoT Hub:

```C
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

A Microsoft está a enviar uma temperatura que é do tipo **TemperatureEvent** e essa estrutura contém um **temperatura** e **tempo** membro. Isso será diretamente refletido nos dados serializados.

Da mesma forma, podemos enviar um evento de humidade com este código:

```C
thermostat->Humidity.Humidity = 45;
thermostat->Humidity.Time = GetDateTimeOffset(now);
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

O formato serializado, que é enviado para o IoT Hub é apresentado da seguinte forma:

```C
{"Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

Novamente, trata-se conforme esperado.

Com esse modelo, pode imaginar como outros eventos podem ser facilmente adicionados. Define a mais estruturas usando **DECLARE\_STRUCT**e incluem o evento correspondente no modelo com **WITH\_dados**.

Agora, vamos modificar o modelo para que inclua os mesmos dados, mas com uma estrutura diferente.

### <a name="model-2"></a>Modelo 2

Considere este modelo alternativo com a descrita anteriormente:

```C
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Neste caso eliminámos a **DECLARE\_STRUCT** macros e simplesmente está a definir os itens de dados de nosso cenário usando tipos simples de linguagem de modelagem.

Apenas por enquanto, ignorar o **tempo** eventos. Com essa exceção, aqui está o código para entrada **temperatura**:

```C
time_t now;
time(&now);
thermostat->Temperature = 75;

unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Este código envia o seguinte evento serializado para o IoT Hub:

```C
{"Temperature":75}
```

E o código para enviar o evento de humidade apareça da seguinte forma:

```C
thermostat->Humidity = 45;
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Este código envia esse ao IoT Hub:

```C
{"Humidity":45}
```

Até agora ainda existem sem surpresas. Agora vamos alterar como utilizamos a macro SERIALIZE.

O **SERIALIZE** macro pode demorar vários eventos de dados como argumentos. Isto permite-nos serializar os **temperatura** e **humidade** eventos em conjunto e enviá-los para o IoT Hub numa chamada:

```C
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Pode imaginar que o resultado desse código é que os dados de dois eventos são enviados para o IoT Hub:

[ {"Temperature":75}, {"Humidity":45} ]

Em outras palavras, pode esperar que esse código é igual a envio **temperatura** e **humidade** separadamente. É apenas uma conveniência para passar os dois eventos para **SERIALIZE** na mesma chamada. No entanto, não for esse o caso. Em vez disso, o código acima envia este evento de dados individual para o IoT Hub:

{"Temperature":75, "Humidity":45}

Isso pode parecer estranho porque o nosso modelo define **temperatura** e **humidade** como dois *separado* eventos:

```C
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Mais para o ponto, usamos não modelos desses eventos em que **temperatura** e **humidade** estão na mesma estrutura:

```C
DECLARE_STRUCT(TemperatureAndHumidityEvent,
int, Temperature,
int, Humidity,
);

DECLARE_MODEL(Thermostat,
WITH_DATA(TemperatureAndHumidityEvent, TemperatureAndHumidity),
);
```

Se usamos esse modelo, seria mais fácil de entender como **temperatura** e **humidade** seriam enviados na mesma mensagem serializada. No entanto, talvez não esteja claro por que ele funciona dessa forma, quando passa os dois eventos de dados para **SERIALIZE** usando o modelo de 2.

Este comportamento é mais fácil de entender se sabe as suposições que o **serializador** biblioteca está fazendo. Dar sentido a isso, vamos voltar ao nosso modelo:

```C
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Considere esse modelo em termos orientados a objeto. Neste caso, está a modelar um dispositivo físico (um termóstato) e que o dispositivo inclui atributos como a **temperatura** e **humidade**.

Podemos enviar todo o estado do nosso modelo com um código como o seguinte:

```C
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity, thermostat->Time) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Supondo que os valores de temperatura, humidade e a hora estão definidas, verá um evento como esse enviadas ao IoT Hub:

```C
{"Temperature":75, "Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

Por vezes, só poderá enviar *alguns* propriedades do modelo para a nuvem (isso é especialmente verdadeiro se o seu modelo contém um grande número de eventos de dados). É útil enviar apenas um subconjunto dos eventos de dados, tal como no nosso exemplo anterior:

```C
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

Isso gera exatamente o mesmo evento serializado como se tivesse definimos uma **TemperatureEvent** com um **temperatura** e **tempo** membro, tal como usamos com modelos de 1. Neste caso, conseguimos gerar exatamente o mesmo evento serializado utilizando um modelo diferente (modelo de 2), uma vez que chamamos **SERIALIZE** de forma diferente.

O ponto importante é que, se passar vários eventos de dados para **SERIALIZE,** , em seguida, ele supõe que cada evento é uma propriedade num único objeto JSON.

A melhor abordagem depende de e como pensa em seu modelo. Se estiver enviando "eventos" para a nuvem e cada evento contém um conjunto definido de propriedades, em seguida, a primeira abordagem faz muito sentido. Nesse caso usaria **DECLARE\_STRUCT** para definir a estrutura de cada evento e, em seguida, incluí-los no seu modelo com o **WITH\_dados** macro. Em seguida, enviar cada evento como fizemos no primeiro exemplo acima. Nesta abordagem apenas transmite um evento de dados individual **SERIALIZADOR**.

Se pensar em seu modelo de forma orientada a objeto, em seguida, a segunda abordagem poderá atender às. Neste caso, os elementos definidos usando **WITH\_dados** são as "Propriedades" de seu objeto. Passa o subconjunto dos eventos para **SERIALIZE** que gosta, dependendo de sua estado do objeto "" pretende enviar para a cloud.

Abordagem nem é certa ou errada. Apenas Lembre-se de como o **serializador** trabalha de biblioteca e escolher a abordagem de modelagem que melhor se adequa às suas necessidades.

## <a name="message-handling"></a>Processamento de mensagens

Até agora este artigo apenas já discutiu o envio de eventos para o IoT Hub e ainda não resolvidos de recebimento de mensagens. O motivo para isso é que o que precisamos saber sobre o recebimento de mensagens em grande parte foram abordado no artigo [Azure IoT device SDK para C](iot-hub-device-sdk-c-intro.md). Lembre-se partir do respetivo artigo que processar mensagens ao se registrar uma função de retorno de chamada de mensagem:

```C
IoTHubClient_SetMessageCallback(iotHubClientHandle, IoTHubMessage, myWeather)
```

, Em seguida, escrever a função de retorno de chamada é invocada quando uma mensagem é recebida:

```C
static IOTHUBMESSAGE_DISPOSITION_RESULT IoTHubMessage(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    IOTHUBMESSAGE_DISPOSITION_RESULT result;
    const unsigned char* buffer;
    size_t size;
    if (IoTHubMessage_GetByteArray(message, &buffer, &size) != IOTHUB_MESSAGE_OK)
    {
        printf("unable to IoTHubMessage_GetByteArray\r\n");
        result = EXECUTE_COMMAND_ERROR;
    }
    else
    {
        /*buffer is not zero terminated*/
        char* temp = malloc(size + 1);
        if (temp == NULL)
        {
            printf("failed to malloc\r\n");
            result = EXECUTE_COMMAND_ERROR;
        }
        else
        {
            memcpy(temp, buffer, size);
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

Esta implementação do **IoTHubMessage** chama a função de específica para cada ação no seu modelo. Por exemplo, se seu modelo define esta ação:

```C
WITH_ACTION(SetAirResistance, int, Position)
```

Tem de definir uma função com essa assinatura:

```C
EXECUTE_COMMAND_RESULT SetAirResistance(ContosoAnemometer* device, int Position)
{
    (void)device;
    (void)printf("Setting Air Resistance Position to %d.\r\n", Position);
    return EXECUTE_COMMAND_SUCCESS;
}
```

**SetAirResistance** , em seguida, é chamado quando essa mensagem é enviada para o seu dispositivo.

O que ainda não Explicamos ainda são a versão serializada da mensagem semelhante ao seguinte. Em outras palavras, se pretender enviar uma **SetAirResistance** mensagem para o seu dispositivo, o qual essa aparência?

Se estiver enviando uma mensagem a um dispositivo, poderia fazê-lo através do SDK do serviço de IoT do Azure. Ainda precisa de saber qual cadeia de caracteres para enviar para invocar uma ação específica. O formato geral para enviar uma mensagem será exibida da seguinte forma:

```C
{"Name" : "", "Parameters" : "" }
```

Está a enviar um objeto JSON serializado com duas propriedades: **Nome** é o nome da ação (mensagens) e **parâmetros** contém os parâmetros da ação.

Por exemplo, para invocar **SetAirResistance** pode enviar esta mensagem para um dispositivo:

```C
{"Name" : "SetAirResistance", "Parameters" : { "Position" : 5 }}
```

O nome da ação tem de corresponder exatamente a uma ação definida no seu modelo. Os nomes dos parâmetros têm de corresponder também. Tenha também em atenção de maiúsculas e minúsculas. **Nome** e **parâmetros** estão sempre em maiúsculas. Certifique-se de acordo com o caso de seu nome de ação e parâmetros no seu modelo. Neste exemplo, o nome de ação é "SetAirResistance" e não "setairresistance".

As outras duas ações **TurnFanOn** e **TurnFanOff** podem ser invocados através do envio essas mensagens para um dispositivo:

```C
{"Name" : "TurnFanOn", "Parameters" : {}}
{"Name" : "TurnFanOff", "Parameters" : {}}
```

Esta secção descrito tudo o que precisa saber quando os eventos de enviar e receber mensagens com o **serializador** biblioteca. Antes de avançar, vamos abordar alguns parâmetros que pode configurar, controlam qual o tamanho máximo é de seu modelo.

## <a name="macro-configuration"></a>Configuração de macro

Se estiver a utilizar o **serializador** biblioteca uma parte importante do SDK estar atento é encontrada na biblioteca do azure-c-utilitário partilhado.

Se tem um clone do repositório Azure-iot-sdk-c do GitHub com a opção - recursivo, em seguida, irá encontrar esta biblioteca de utilitário compartilhado aqui:

```C
.\\c-utility
```

Se não tem um clone da biblioteca, pode encontrá-lo [aqui](https://github.com/Azure/azure-c-shared-utility).

Na biblioteca de utilitário compartilhado, encontrará a seguinte pasta:

```C
azure-c-shared-utility\\macro\_utils\_h\_generator.
```

Esta pasta contém uma solução do Visual Studio chamada **macro\_utils\_h\_generator.sln**:

  ![Captura de ecrã do maco_utils_h_generator de solução do Visual Studio](media/iot-hub-device-sdk-c-serializer/01-macro_utils_h_generator.png)

O programa nesta solução gera a **macro\_utils.h** ficheiro. Há uma macro de predefinição\_ficheiro utils.h incluído no SDK. Esta solução permite-lhe modificar alguns parâmetros e, em seguida, recriar o arquivo de cabeçalho com base nestes parâmetros.

Os dois parâmetros chave se preocupar com são **nArithmetic** e **nMacroParameters** que estão definido nestas duas linhas encontradas na macro\_utils.tt:

```C
<#int nArithmetic=1024;#>
<#int nMacroParameters=124;/*127 parameters in one macro deﬁnition in C99 in chapter 5.2.4.1 Translation limits*/#>
```

Estes valores são os parâmetros de padrão incluídos no SDK. Cada um dos parâmetros tem o significado seguinte:

* nMacroParameters – controla quantos parâmetros pode ter num um DECLARE\_definição de macro do modelo.
* nArithmetic – controla o número total de membros permitidos num modelo.

O motivo pelo qual que esses parâmetros são importantes é porque controlam o que tamanho pode ser seu modelo. Por exemplo, considere esta definição de modelo:

```C
DECLARE_MODEL(MyModel,
WITH_DATA(int, MyData)
);
```

Como mencionado anteriormente, **DECLARE\_modelo** é apenas uma macro de C. Os nomes do modelo e o **WITH\_dados** instrução (ainda outro macro) é parâmetros de **DECLARE\_modelo**. **nMacroParameters** define o número de parâmetros podem ser incluídos numa **DECLARE\_modelo**. Efetivamente, ele define quantos dados evento e ação declarações pode ter. Como tal, com o limite predefinido de 124 isso significa que pode definir um modelo com uma combinação de cerca de 60 ações e eventos de dados. Se tentar exceder este limite, receberá erros de compilador que ter um aspeto semelhantes a este:

  ![Captura de ecrã de erros do compilador de parâmetros de Macro](media/iot-hub-device-sdk-c-serializer/02-nMacroParametersCompilerErrors.png)

O **nArithmetic** parâmetro é mais sobre o funcionamento interno da linguagem macro que seu aplicativo.  Controla o número total de membros, pode ter no seu modelo, incluindo **DECLARE_STRUCT** macros. Se começa a ver erros de compilador como esse, então deve tentar aumentar **nArithmetic**:

   ![Captura de ecrã de erros do compilador aritmético](media/iot-hub-device-sdk-c-serializer/03-nArithmeticCompilerErrors.png)

Se pretender alterar esses parâmetros, modifique os valores na macro\_utils.tt de ficheiros, recompilar a macro\_utils\_h\_generator.sln solução e execute o programa compilado. Quando fizer isso, uma nova macro\_utils.h ficheiro é gerado e colocado na.\\ comuns\\inc directory.

Para poder utilizar a nova versão da macro\_utils.h, remova o **serializador** incluem o pacote NuGet da sua solução e no seu lugar a **serializador** projeto do Visual Studio. Isto permite que seu código seja compilado com o código-fonte da biblioteca do serializador. Isto inclui a macro atualizada\_utils.h. Se quiser fazê-lo **simplesample\_amqp**, comece por remover o pacote NuGet para a biblioteca de serializador da solução:

   ![Captura de ecrã de como remover o pacote NuGet para a biblioteca de serializador](media/iot-hub-device-sdk-c-serializer/04-serializer-github-package.png)

Em seguida, adicione este projeto à sua solução do Visual Studio:

> .\\c\\serializer\\build\\windows\\serializer.vcxproj
> 
> 

Quando tiver terminado, a sua solução deve ser assim:

   ![Captura de ecrã do simplesample_amqp solução do Visual Studio](media/iot-hub-device-sdk-c-serializer/05-serializer-project.png)

Agora quando compilar sua solução, a macro atualizada\_utils.h está incluído no seu binário.

Tenha em atenção que a aumentar estes valores suficientemente elevados pode exceder os limites de compilador. Nesse ponto, o **nMacroParameters** é o parâmetro principal com a qual se preocupar. A especificação de C99 Especifica que um mínimo de 127 parâmetros são permitidos numa definição de macro. O compilador do Microsoft segue a especificação exatamente (e tem um limite de 127), portanto, não vai conseguir aumentar **nMacroParameters** para lá da predefinição. Outros compiladores podem permitir que faça isso (por exemplo, o compilador GNU suporta um limite superior).

Até agora que abrangemos praticamente tudo o que precisa saber sobre como escrever código com o **serializador** biblioteca. Antes de concluir, vamos rever alguns tópicos de artigos anteriores que pode estar se perguntando sobre.

## <a name="the-lower-level-apis"></a>As APIs de nível inferior
É o aplicativo de exemplo em que este artigo concentra **simplesample\_amqp**. Este exemplo utiliza o nível mais alto (a não -**odas**) APIs para enviar eventos e receber mensagens. Se usar essas APIs, é executado um thread em segundo plano que assume o controlo de envio de eventos e receber mensagens. No entanto, pode utilizar as APIs de (todos) de nível inferior para eliminar este thread em segundo plano e assumir o controle explícito sobre o quando enviar eventos ou receber mensagens da cloud.

Conforme descrito numa [artigo anterior](iot-hub-device-sdk-c-iothubclient.md), existe um conjunto de funções que consiste nas APIs de nível superior:

* IoTHubClient\_CreateFromConnectionString
* IoTHubClient\_SendEventAsync
* IoTHubClient\_SetMessageCallback
* IoTHubClient\_Destroy

Essas APIs são demonstradas **simplesample\_amqp**.

Há também um análogo conjunto de APIs de nível inferior.

* IoTHubClient\_LL\_CreateFromConnectionString
* IoTHubClient\_LL\_SendEventAsync
* IoTHubClient\_LL\_SetMessageCallback
* IoTHubClient\_LL\_Destroy

Tenha em atenção que as APIs de nível inferior funcionam exatamente da mesma forma, conforme descrito nos artigos anteriores. Pode utilizar o primeiro conjunto de APIs, se pretender que um thread em segundo plano para tratar eventos de envio e recebimento de mensagens. Utilize o segundo conjunto de APIs se pretender que o controle explícito sobre o quando enviar e receber dados a partir do IoT Hub. De qualquer conjunto de trabalho APIs igualmente bem com o **serializador** biblioteca.

Para obter um exemplo de como as APIs de nível inferior são usadas com o **serializador** biblioteca, consulte a **simplesample\_http** aplicação.

## <a name="additional-topics"></a>Tópicos adicionais
Alguns outros tópicos que vale a pena mencionar são novamente a propriedade processar, usando as credenciais do dispositivo alternativo e opções de configuração. Estes são todos os tópicos abordados num [artigo anterior](iot-hub-device-sdk-c-iothubclient.md). O ponto principal é que todos esses recursos funcionam da mesma forma com o **serializador** biblioteca tal como com o **o IoTHubClient** biblioteca. Por exemplo, se quiser anexar propriedades a um evento a partir do seu modelo, utilize **IoTHubMessage\_propriedades** e **mapa**\_**AddorUpdate**, tal como descrito anteriormente:

```C
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

Se o evento foi gerado a partir da **serializador** biblioteca ou tiver sido criado manualmente com o **o IoTHubClient** biblioteca não é relevante.

Para as credenciais de dispositivo alternativo, usando **o IoTHubClient\_LL\_Create** funciona igualmente bem como **o IoTHubClient\_CreateFromConnectionString** para alocar um **IOTHUB\_cliente\_PROCESSAR**.

Por fim, se estiver a utilizar o **serializador** biblioteca, pode definir opções de configuração com **o IoTHubClient\_LL\_SetOption** tal como fez quando utilizando o **O IoTHubClient** biblioteca.

Uma funcionalidade que é exclusiva para o **serializador** biblioteca são a inicialização de APIs. Antes de começar a trabalhar com a biblioteca, tem de chamar **serializador\_init**:

```C
serializer_init(NULL);
```

Isso é feito antes de chamar **o IoTHubClient\_CreateFromConnectionString**.

Da mesma forma, quando terminar a última chamada, fará a trabalhar com a biblioteca, é **serializador\_deinit**:

```C
serializer_deinit();
```

Caso contrário, todos os outros recursos listados acima funcionam da mesma **serializador** biblioteca de forma que no **o IoTHubClient** biblioteca. Para obter mais informações sobre qualquer um destes tópicos, consulte a [artigo anterior](iot-hub-device-sdk-c-iothubclient.md) nesta série.

## <a name="next-steps"></a>Passos Seguintes

Este artigo descreve detalhadamente dos aspectos exclusivos dos **serializador** biblioteca contida no **Azure IoT device SDK para C**. Com as informações fornecidas que devem ter uma boa compreensão de como utilizar modelos para enviar eventos e receber mensagens do IoT Hub.

Isto também conclui a série de três partes sobre como desenvolver aplicativos com o **Azure IoT device SDK para C**. Deve ser informações suficientes para não apenas a começar mas dão-lhe um entendimento profundo de como funcionam as APIs. Para obter mais informações, existem alguns exemplos no SDK não abrangido aqui. Caso contrário, o [documentação do SDK do Azure IoT](https://github.com/Azure/azure-iot-sdk-c) é um bom recurso para obter informações adicionais.

Para saber mais sobre o desenvolvimento para o IoT Hub, veja a [do Azure IoT SDKs](iot-hub-devguide-sdks.md).

Para explorar ainda mais os recursos do IoT Hub, veja [implementação de ia para dispositivos de ponta com o Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md).