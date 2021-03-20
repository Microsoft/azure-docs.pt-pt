---
title: Dispositivo Azure IoT SDK para C - Serializer | Microsoft Docs
description: Como utilizar a biblioteca Serializer no dispositivo Azure IoT SDK para C para criar aplicações de dispositivos que comunicam com um hub IoT.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 09/06/2016
ms.author: robinsh
ms.custom: amqp
ms.openlocfilehash: f52d1d1c5f264550076688d5e25e110de230eff4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92152241"
---
# <a name="azure-iot-device-sdk-for-c--more-about-serializer"></a>Azure IoT dispositivo SDK para C – mais sobre serializer

O primeiro artigo desta série introduziu o [dispositivo SDK de Introdução ao Azure IoT para C](iot-hub-device-sdk-c-intro.md). O artigo seguinte forneceu uma descrição mais detalhada do [dispositivo Azure IoT SDK para C -- IoTHubClient](iot-hub-device-sdk-c-iothubclient.md). Este artigo completa a cobertura do SDK fornecendo uma descrição mais detalhada do componente restante: a biblioteca **serializadora.**

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

O artigo introdutório descrevia como usar a biblioteca **serializer** para enviar eventos e receber mensagens do IoT Hub. Neste artigo, alargamos essa discussão, fornecendo uma explicação mais completa de como modelar os seus dados com a linguagem macro **serializer.** O artigo também inclui mais detalhes sobre como a biblioteca serializa mensagens (e em alguns casos como pode controlar o comportamento de serialização). Também descreveremos alguns parâmetros que pode modificar que determinam o tamanho dos modelos que cria.

Finalmente, o artigo revisita alguns tópicos abordados em artigos anteriores, como mensagem e manuseamento de propriedades. Como vamos descobrir, essas características funcionam da mesma forma que usam a biblioteca **de serializers** como fazem com a biblioteca **IoTHubClient.**

Tudo descrito neste artigo baseia-se nas amostras SDK **do serializer.** Se quiser acompanhar, consulte as aplicações mais simples de amqp e simples **\_ amqp** e **\_ simplesmosmas http** incluídas no dispositivo Azure IoT SDK para C.

Pode encontrar o [**dispositivo Azure IoT SDK para**](https://github.com/Azure/azure-iot-sdk-c) o repositório C GitHub e ver detalhes da API na [referência C API](/azure/iot-hub/iot-c-sdk-ref/).

## <a name="the-modeling-language"></a>A linguagem de modelação

O [dispositivo Azure IoT SDK para](iot-hub-device-sdk-c-intro.md) artigo C nesta série introduziu o dispositivo **Azure IoT SDK para** linguagem de modelação C através do exemplo fornecido na aplicação **\_ amqp simples:**

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

Como pode ver, a linguagem de modelação baseia-se em macros C. Começa sempre a definição com **START \_ NAMESPACE** e termina sempre com **END \_ NAMESPACE.** É comum nomear o espaço de nome para a sua empresa ou, como neste exemplo, o projeto em que está a trabalhar.

O que entra no espaço de nomes são definições de modelo. Neste caso, existe um único modelo para um anemómetro. Mais uma vez, o modelo pode ser nomeado qualquer coisa, mas normalmente o modelo está nomeado para o dispositivo ou tipo de dados que pretende trocar com o IoT Hub.  

Os modelos contêm uma definição dos eventos que pode ingressar no IoT Hub (os *dados),* bem como as mensagens que pode receber do IoT Hub (as *ações).* Como pode ver pelo exemplo, os eventos têm um tipo e um nome; as ações têm um nome e parâmetros opcionais (cada um com um tipo).

O que não está demonstrado nesta amostra são tipos de dados adicionais que são suportados pelo SDK. Vamos cobrir a próxima.

> [!NOTE]
> IoT Hub refere-se aos dados que um dispositivo lhe envia como *eventos,* enquanto a linguagem de modelação refere-se a ele como *dados* (definidos usando **WITH_DATA).** Da mesma forma, o IoT Hub refere-se aos dados que envia para os dispositivos como *mensagens,* enquanto o idioma de modelação refere-se a ele como *ações* (definidas utilizando **WITH_ACTION).** Esteja ciente de que estes termos podem ser usados intercambiavelmente neste artigo.
> 
> 

## <a name="supported-data-types"></a>Tipos de dados suportados

Os seguintes tipos de dados são suportados em modelos criados com a biblioteca **serializador:**

| Tipo | Description |
| --- | --- |
| double |número de ponto flutuante de dupla precisão |
| int |Inteiro de 32 bits |
| float |número de ponto flutuante de precisão única |
| long |inteiro inteiro longo |
| int8 \_ t |Inteiro de 8 bits |
| int16 \_ t |Inteiro de 16 bits |
| int32 \_ t |Inteiro de 32 bits |
| int64 \_ t |Número inteiro de 64 bits |
| bool |boolean |
| ascii \_ char \_ ptr |Cadeia ASCII |
| OFFSET DE \_ HORA DE DATA EDM \_ \_ |compensação de tempo de data |
| EDM \_ GUID |GUID |
| BINÁRIO \_ EDM |binary |
| DECLARAR \_ STRUCT |tipo de dados complexo |

Vamos começar com o último tipo de dados. O **DECLARE \_ STRUCT** permite-lhe definir tipos de dados complexos, que são agrupamentos de outros tipos primitivos. Estes agrupamentos permitem-nos definir um modelo que se parece com este:

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

O nosso modelo contém um único evento de dados do tipo **TestType.** **TestType** é um tipo complexo que inclui vários membros, que demonstram coletivamente os tipos primitivos suportados pela linguagem de modelação **serializadora.**

Com um modelo como este, podemos escrever código para enviar dados para o IoT Hub que aparece da seguinte forma:

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

Basicamente, estamos a atribuir um valor a cada membro da estrutura de **teste** e depois ligamos para o **SendAsync** para enviar o evento de dados de **teste** para a nuvem. **SendAsync** é uma função de ajudante que envia um único evento de dados para o IoT Hub:

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

Esta função serializa o evento de dados dado e envia-o para o IoT Hub utilizando **o IoTHubClient \_ SendEventAsync**. Este é o mesmo código discutido em artigos anteriores **(SendAsync** encapsula a lógica numa função conveniente).

Uma outra função de ajudante utilizada no código anterior é **GetDateTimeOffset**. Esta função transforma o tempo dado num valor do tipo **EDM \_ DATE \_ TIME \_ OFFSET**:

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

Se executar este código, a seguinte mensagem é enviada para o IoT Hub:

```C
{"aDouble":1.100000000000000, "aInt":2, "aFloat":3.000000, "aLong":4, "aInt8":5, "auInt8":6, "aInt16":7, "aInt32":8, "aInt64":9, "aBool":true, "aAsciiCharPtr":"ascii string 1", "aDateTimeOffset":"2015-09-14T21:18:21Z", "aGuid":"00010203-0405-0607-0809-0A0B0C0D0E0F", "aBinary":"AQID"}
```

Note que a serialização está no JSON, que é o formato gerado pela biblioteca **serializadora.** Note também que cada membro do objeto JSON serializado corresponde aos membros do **TestType** que definimos no nosso modelo. Os valores também correspondem exatamente aos utilizados no código. No entanto, note que os dados binários estão codificados com base64: "AQID" é a codificação base de {0x01, 0x02, 0x03}.

Este exemplo demonstra a vantagem de usar a biblioteca **serializadora** -- permite-nos enviar JSON para a nuvem, sem ter que lidar explicitamente com a serialização na nossa aplicação. Só temos de nos preocupar em definir os valores dos eventos de dados no nosso modelo e, em seguida, chamar apis simples para enviar esses eventos para a nuvem.

Com esta informação, podemos definir modelos que incluam a gama de tipos de dados suportados, incluindo tipos complexos (poderíamos até incluir tipos complexos dentro de outros tipos complexos). No entanto, o JSON serializado gerado pelo exemplo acima traz um ponto importante. *A forma como* enviamos dados com a biblioteca **serializadora** determina exatamente como o JSON é formado. Este ponto em particular é o que vamos cobrir a seguir.

## <a name="more-about-serialization"></a>Mais sobre serialização

A secção anterior destaca um exemplo da saída gerada pela biblioteca **serializadora.** Nesta secção, vamos explicar como a biblioteca serializa dados e como você pode controlar esse comportamento usando as APIs de serialização.

Para promover a discussão sobre a serialização, trabalharemos com um novo modelo baseado num termóstato. Primeiro, vamos fornecer alguns antecedentes sobre o cenário que estamos a tentar abordar.

Queremos modelar um termóstato que mede a temperatura e a humidade. Cada pedaço de dados será enviado para o IoT Hub de forma diferente. Por predefinição, o termóstato ingressa um evento de temperatura uma vez a cada 2 minutos; um evento de humidade é ingressado uma vez a cada 15 minutos. Quando qualquer um dos eventos é ingressado, deve incluir um relógio de tempo que mostre o tempo que a temperatura ou humidade correspondentes foi medida.

Dado este cenário, vamos demonstrar duas maneiras diferentes de modelar os dados, e vamos explicar o efeito que a modelação tem na saída serializada.

### <a name="model-1"></a>Modelo 1

Aqui está a primeira versão de um modelo que suporta o cenário anterior:

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

Note que o modelo inclui dois eventos de dados: **Temperatura** e **Humidade**. Ao contrário de exemplos anteriores, o tipo de cada evento é uma estrutura definida usando **a DECLARE \_ STRUCT**. **TemperaturaEvente** inclui uma medição da temperatura e um tempotando; **HumidadeEvent** contém uma medição de humidade e um tempotando. Este modelo dá-nos uma forma natural de modelar os dados para o cenário acima descrito. Quando enviarmos um evento para a nuvem, enviaremos um medidor de temperatura/hora ou um par de humidade/hora.

Podemos enviar um evento de temperatura para a nuvem usando código, como:

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

Usaremos valores codificados para temperatura e humidade no código da amostra, mas imagine que estamos a recuperar estes valores através da amostragem dos sensores correspondentes no termóstato.

O código acima utiliza o ajudante **GetDateTimeOffset** que foi introduzido anteriormente. Por razões que ficarão claras mais tarde, este código separa explicitamente a tarefa de serializar e enviar o evento. O código anterior serializa o evento de temperatura num tampão. Em seguida, **sendMessage** é uma função de ajudante (incluída em **amqp simples de \_ amqp)** que envia o evento para ioT Hub:

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

Este código é um subconjunto do ajudante **SendAsync** descrito na secção anterior, por isso não vamos revi-lo aqui.

Quando corremos o código anterior para enviar o evento Temperatura, esta forma serializada do evento é enviada para o IoT Hub:

```C
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

Estamos enviando uma temperatura, que é do tipo **TemperaturaEvent,** e que a estrutura contém um membro **da Temperatura** e **do Tempo.** Isto reflete-se diretamente nos dados serializados.

Da mesma forma, podemos enviar um evento de humidade com este código:

```C
thermostat->Humidity.Humidity = 45;
thermostat->Humidity.Time = GetDateTimeOffset(now);
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

O formulário serializado que é enviado para o IoT Hub aparece da seguinte forma:

```C
{"Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

Mais uma vez, isto é como esperado.

Com este modelo, pode imaginar como eventos adicionais podem ser facilmente adicionados. Define mais estruturas utilizando **a DECLARE \_ STRUCT,** e inclui o evento correspondente no modelo utilizando **com \_ DADOS**.

Agora, vamos modificar o modelo para que inclua os mesmos dados, mas com uma estrutura diferente.

### <a name="model-2"></a>Modelo 2

Considere este modelo alternativo ao acima:

```C
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Neste caso, eliminámos as macros **DECLARA \_ STRUCT** e estamos simplesmente a definir os dados do nosso cenário usando tipos simples da linguagem de modelação.

Só por enquanto, ignore o evento **do tempo.** Com isso à parte, aqui está o código para entrar **temperatura:**

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

E o código para o envio do evento de humidade aparece da seguinte forma:

```C
thermostat->Humidity = 45;
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Este código envia isto para o IoT Hub:

```C
{"Humidity":45}
```

Até agora ainda não há surpresas. Agora vamos mudar a forma como usamos o macro SERIALIZE.

A macro **SERIALIZE** pode tomar vários eventos de dados como argumentos. Isto permite-nos serializar o evento **temperatura** e **humidade** juntos e enviá-los para o IoT Hub numa chamada:

```C
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Pode adivinhar que o resultado deste código é que dois eventos de dados são enviados para o IoT Hub:

[ {"Temperatura":75}, {"Humidade":45}

Por outras palavras, pode esperar que este código seja o mesmo que enviar **temperatura** e **humidade** separadamente. É apenas uma conveniência passar ambos os eventos para **o SERIALIZE** na mesma chamada. No entanto, não é esse o caso. Em vez disso, o código acima envia este único evento de dados para o IoT Hub:

{"Temperatura":75, "Humidade":45}

Isto pode parecer estranho porque o nosso modelo define **temperatura** e **humidade** como dois eventos *separados:*

```C
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Mais do que isso, não modelamos estes eventos onde **a temperatura** e **a humidade** estão na mesma estrutura:

```C
DECLARE_STRUCT(TemperatureAndHumidityEvent,
int, Temperature,
int, Humidity,
);

DECLARE_MODEL(Thermostat,
WITH_DATA(TemperatureAndHumidityEvent, TemperatureAndHumidity),
);
```

Se usássemos este modelo, seria mais fácil entender como **a temperatura** e **a humidade** seriam enviadas na mesma mensagem serializada. No entanto, pode não ser claro por que razão funciona assim quando transmite ambos os eventos de dados para **o SERIALIZE** usando o Model 2.

Este comportamento é mais fácil de entender se você sabe os pressupostos que a biblioteca **serializer** está fazendo. Para dar sentido a isto, vamos voltar ao nosso modelo:

```C
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Pense neste modelo em termos orientados para os objetos. Neste caso, estamos a modelar um dispositivo físico (um termóstato) e esse dispositivo inclui atributos como **Temperatura** e **Humidade.**

Podemos enviar todo o estado do nosso modelo com código como:

```C
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity, thermostat->Time) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Assumindo que os valores da Temperatura, Humidade e Tempo estão definidos, veríamos um evento como este enviado para o IoT Hub:

```C
{"Temperature":75, "Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

Por vezes, pode apenas querer enviar *algumas* propriedades do modelo para a nuvem (isto é especialmente verdade se o seu modelo contiver um grande número de eventos de dados). É útil enviar apenas um subconjunto de eventos de dados, como no nosso exemplo anterior:

```C
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

Isto gera exatamente o mesmo evento serializado como se tivéssemos definido um **TemperatureEvent** com um membro **de Temperatura** e **Tempo,** tal como fizemos com o Model 1. Neste caso, conseguimos gerar exatamente o mesmo evento serializado usando um modelo diferente (modelo 2) porque chamámos **SERIALIZE de** uma forma diferente.

O ponto importante é que se você passar vários eventos de dados para **SERIALIZE,** então assume que cada evento é uma propriedade em um único objeto JSON.

A melhor abordagem depende de si e de como pensa sobre o seu modelo. Se você está enviando "eventos" para a nuvem e cada evento contém um conjunto definido de propriedades, então a primeira abordagem faz muito sentido. Nesse caso, utilizaria **o DECLARE \_ STRUCT** para definir a estrutura de cada evento e, em seguida, incluí-los no seu modelo com o macro **COM \_ DATA.** Em seguida, envie cada evento como fizemos no primeiro exemplo acima. Nesta abordagem, passaria apenas um único evento de dados para **o SERIALIZER.**

Se pensar no seu modelo de uma forma orientada para os objetos, então a segunda abordagem pode servir-lhe. Neste caso, os elementos definidos usando **COM \_ DADOs** são as "propriedades" do seu objeto. Você passa o subconjunto de eventos para **o SERIALIZE** que você gosta, dependendo do estado do seu "objeto" que você quer enviar para a nuvem.

A abordagem nether é certa ou errada. Basta estar atento ao funcionamento da biblioteca **serializer** e escolher a abordagem de modelação que melhor se adequa às suas necessidades.

## <a name="message-handling"></a>Processamento de mensagens

Até agora este artigo apenas discutiu o envio de eventos para o IoT Hub, e não abordou receber mensagens. A razão para isso é que o que precisamos de saber sobre receber mensagens foi amplamente coberto no artigo [Azure IoT dispositivo SDK para C](iot-hub-device-sdk-c-intro.md). Lembre-se desse artigo que processa mensagens registando uma função de chamada de mensagem:

```C
IoTHubClient_SetMessageCallback(iotHubClientHandle, IoTHubMessage, myWeather)
```

Em seguida, escreva a função de retorno que é invocada quando uma mensagem é recebida:

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

Esta implementação do **IoTHubMessage** chama a função específica para cada ação no seu modelo. Por exemplo, se o seu modelo definir esta ação:

```C
WITH_ACTION(SetAirResistance, int, Position)
```

Deve definir uma função com esta assinatura:

```C
EXECUTE_COMMAND_RESULT SetAirResistance(ContosoAnemometer* device, int Position)
{
    (void)device;
    (void)printf("Setting Air Resistance Position to %d.\r\n", Position);
    return EXECUTE_COMMAND_SUCCESS;
}
```

**O SetAirResistance** é então chamado quando essa mensagem é enviada para o seu dispositivo.

O que ainda não explicamos é como é a versão serializada da mensagem. Por outras palavras, se pretender enviar uma mensagem **SetAirResistance** para o seu dispositivo, como é que isso se parece?

Se estiver a enviar uma mensagem para um dispositivo, fá-lo-ia através do serviço Azure IoT SDK. Ainda precisas de saber que corda enviar para invocar uma ação em particular. O formato geral para o envio de uma mensagem aparece da seguinte forma:

```C
{"Name" : "", "Parameters" : "" }
```

Está a enviar um objeto JSON serializado com duas propriedades: **Nome** é o nome da ação (mensagem) e **os parâmetros** contêm os parâmetros dessa ação.

Por exemplo, para invocar **o SetAirResistance** pode enviar esta mensagem para um dispositivo:

```C
{"Name" : "SetAirResistance", "Parameters" : { "Position" : 5 }}
```

O nome de ação deve corresponder exatamente a uma ação definida no seu modelo. Os nomes dos parâmetros também devem coincidir. Note também a sensibilidade do caso. **Nome** e **parâmetros** são sempre maiúsculas. Certifique-se de que corresponde ao caso do seu nome de ação e parâmetros no seu modelo. Neste exemplo, o nome de ação é "SetAirResistance" e não "setairresistance".

As outras duas ações **TurnFanOn** e **TurnFanOff** podem ser invocadas enviando estas mensagens para um dispositivo:

```C
{"Name" : "TurnFanOn", "Parameters" : {}}
{"Name" : "TurnFanOff", "Parameters" : {}}
```

Esta secção descreveu tudo o que precisa de saber ao enviar eventos e receber mensagens com a biblioteca **serializadora.** Antes de seguir em frente, vamos cobrir alguns parâmetros que pode configurar que controlam o tamanho do seu modelo.

## <a name="macro-configuration"></a>Configuração macro

Se estiver a utilizar a biblioteca **Serializer,** uma parte importante do SDK a ter conhecimento encontra-se na biblioteca de utilidade partilhada azure-c.

Se clonou o repositório Azure-iot-sdk-c do GitHub e emitiu o `git submodule update --init` comando, então encontrará esta biblioteca de utilidade partilhada aqui:

```C
.\\c-utility
```

Se não clonou a biblioteca, pode encontrá-la [aqui.](https://github.com/Azure/azure-c-shared-utility)

Dentro da biblioteca de utilidade partilhada, encontrará a seguinte pasta:

```C
azure-c-shared-utility\\macro\_utils\_h\_generator.
```

Esta pasta contém uma solução Visual Studio chamada **\_ macro utils \_ h \_ gerador.sln**:

  ![Screenshot da solução Visual Studio maco_utils_h_generator](media/iot-hub-device-sdk-c-serializer/01-macro_utils_h_generator.png)

O programa nesta solução gera o ficheiro **macro \_ utils.h.** Há um ficheiro macro \_ utils.h padrão incluído com o SDK. Esta solução permite modificar alguns parâmetros e, em seguida, recriar o ficheiro do cabeçalho com base nestes parâmetros.

Os dois parâmetros-chave a que se deve preocupar são **os nArithmetic** e **nMacroParameters,** que são definidos nestas duas linhas encontradas em \_ macro utils.tt:

```C
<#int nArithmetic=1024;#>
<#int nMacroParameters=124;/*127 parameters in one macro deﬁnition in C99 in chapter 5.2.4.1 Translation limits*/#>
```

Estes valores são os parâmetros padrão incluídos com o SDK. Cada parâmetro tem o seguinte significado:

* nMacroParameters – Controla quantos parâmetros pode ter numa \_ definição macro DO MODELO DECLARA.
* nArithmetic – Controla o número total de membros permitidos num modelo.

A razão pela qual estes parâmetros são importantes é porque eles controlam o tamanho do seu modelo. Por exemplo, considere esta definição de modelo:

```C
DECLARE_MODEL(MyModel,
WITH_DATA(int, MyData)
);
```

Como mencionado anteriormente, **o \_ MODELO DECLARA** É apenas uma macro C. Os nomes do modelo e a declaração **COM \_ DATA** (mais uma macro) são parâmetros do **\_ MODELO DECLARA.** **nMacroParameters** define quantos parâmetros podem ser incluídos no **\_ MODELO DECLARE**. Efetivamente, isto define quantos eventos de dados e declarações de ação você pode ter. Como tal, com o limite padrão de 124 isto significa que pode definir um modelo com uma combinação de cerca de 60 ações e eventos de dados. Se tentar ultrapassar este limite, receberá erros de compilação semelhantes a este:

  ![Screenshot de erros do compilador de parâmetros macro](media/iot-hub-device-sdk-c-serializer/02-nMacroParametersCompilerErrors.png)

O parâmetro **nArithmetic** é mais sobre o funcionamento interno da linguagem macro do que a sua aplicação.  Controla o número total de membros que pode ter no seu modelo, incluindo **DECLARE_STRUCT** macros. Se começar a ver erros do compilador como este, então deve tentar aumentar **o nArithmetic**:

   ![Screenshot de erros do compilador aritmético](media/iot-hub-device-sdk-c-serializer/03-nArithmeticCompilerErrors.png)

Se pretender alterar estes parâmetros, modifique os valores no ficheiro macro \_ utils.tt, recomponha a solução de gerador de .sln macro \_ \_ utils e \_ execute o programa compilado. Quando o fizer, um novo ficheiro macro \_ utils.h é gerado e colocado no . \\ \\diretório inc comum.

Para utilizar a nova versão do macro \_ utils.h, remova o pacote **NuGet serializador** da sua solução e no seu lugar inclua o projeto **serializer** Visual Studio. Isto permite que o seu código compile contra o código fonte da biblioteca serializadora. Isto inclui os \_ utils macro atualizados.h. Se quiser fazê-lo para **\_ amqp simples,** comece por remover o pacote NuGet para a biblioteca serializadora da solução:

   ![Screenshot de remover o pacote NuGet para a biblioteca serializer](media/iot-hub-device-sdk-c-serializer/04-serializer-github-package.png)

Em seguida, adicione este projeto à sua solução Visual Studio:

> .\\ c \\ serializer \\ construir \\ janelas \\ serializer.vcxproj
> 
> 

Quando terminar, a sua solução deve ser assim:

   ![Screenshot da solução simplesample_amqp Visual Studio](media/iot-hub-device-sdk-c-serializer/05-serializer-project.png)

Agora, quando compila a sua solução, o macro \_ utils.h atualizado está incluído no seu binário.

Note que o aumento destes valores alto o suficiente pode exceder os limites do compilador. Neste ponto, o **nMacroParameters** é o parâmetro principal com o qual se deve estar em causa. A especificação C99 especifica que um mínimo de 127 parâmetros são permitidos numa definição macro. O compilador da Microsoft segue exatamente a especificação (e tem um limite de 127), pelo que não poderá aumentar **os nMacroParameters** para além do padrão. Outros compiladores podem permitir-lhe fazê-lo (por exemplo, o compilador GNU suporta um limite mais elevado).

Até agora, cobrimos tudo o que precisa de saber sobre como escrever código com a biblioteca **de serializers.** Antes de concluir, vamos revisitar alguns tópicos de artigos anteriores que você pode estar a pensar.

## <a name="the-lower-level-apis"></a>As APIs de nível inferior
A aplicação de amostra em que este artigo se focou é **simples \_ amqp.** Esta amostra utiliza as APIs de nível superior (as APIs **não-LL)** para enviar eventos e receber mensagens. Se utilizar estes APIs, corre um fio de fundo que cuida tanto do envio de eventos como da receção de mensagens. No entanto, pode utilizar as APIs de nível inferior (LL) para eliminar este fio de fundo e assumir controlo explícito quando envia eventos ou recebe mensagens da nuvem.

Como descrito num [artigo anterior,](iot-hub-device-sdk-c-iothubclient.md)existe um conjunto de funções que consistem nas APIs de nível superior:

* IoTHubClient \_ CriaFromConnectionString
* IoTHubClient \_ SendEventAsync
* IoTHubClient \_ SetMessageCallback
* Destruição IoTHubClient \_

Estas APIs são demonstradas em **\_ amqp simples.**

Há também um conjunto análogo de APIs de nível inferior.

* IoTHubClient \_ LL \_ CreateFromConnectionString
* IoTHubClient \_ LL \_ SendEventAsync
* IoTHubClient \_ LL \_ SetMessageCallback
* IoTHubClient \_ LL \_ Destroy

Note que as APIs de nível inferior funcionam exatamente da mesma forma que as descritas nos artigos anteriores. Pode utilizar o primeiro conjunto de APIs se quiser um fio de fundo para lidar com eventos de envio e receber mensagens. Utilize o segundo conjunto de APIs se quiser um controlo explícito sobre quando envia e recebe dados do IoT Hub. Qualquer um dos conjuntos de APIs funciona igualmente bem com a biblioteca **serializadora.**

Para um exemplo de como as APIs de nível inferior são usadas com a biblioteca **serializer,** consulte a aplicação **\_ http mais simples.**

## <a name="additional-topics"></a>Tópicos adicionais
Alguns outros tópicos que merecem ser mencionados novamente são o manuseamento de propriedades, usando credenciais de dispositivo alternativos e opções de configuração. Estes são todos os tópicos abordados num [artigo anterior.](iot-hub-device-sdk-c-iothubclient.md) O ponto principal é que todas estas características funcionam da mesma forma com a biblioteca **serializadora** como fazem com a biblioteca **IoTHubClient.** Por exemplo, se pretender anexar propriedades a um evento do seu modelo, utilize **\_ propriedades IoTHubMessage** e **Map** \_ **AddorUpdate**, da mesma forma que descrito anteriormente:

```C
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

Se o evento foi gerado a partir da biblioteca **serializadora** ou criado manualmente usando a biblioteca **IoTHubClient** não importa.

Para as credenciais alternativas do dispositivo, a utilização **do IoTHubClient \_ LL \_ Create** funciona tão bem como **o IoTHubClient \_ CreateFromConnectionString** para alocar um **\_ \_ manípulo cliente IOTHUB**.

Finalmente, se estiver a utilizar a biblioteca **serializer,** pode definir opções de configuração com **IoTHubClient \_ LL \_ SetOption** tal como fez ao utilizar a biblioteca **IoTHubClient.**

Uma característica única na biblioteca **serializer** são as APIs de inicialização. Antes de começar a trabalhar com a biblioteca, deve ligar para **o \_ serializer:**

```C
serializer_init(NULL);
```

Isto é feito pouco antes de ligar para **ioTHubClient \_ CreateFromConnectionString**.

Da mesma forma, quando terminar de trabalhar com a biblioteca, a última chamada que fará é para **serializer \_ deinit:**

```C
serializer_deinit();
```

Caso contrário, todas as outras funcionalidades listadas acima funcionam da mesma forma que na **biblioteca** **ioTHubClient.** Para mais informações sobre qualquer um destes tópicos, consulte o [artigo anterior](iot-hub-device-sdk-c-iothubclient.md) nesta série.

## <a name="next-steps"></a>Passos seguintes

Este artigo descreve em pormenor os aspetos únicos da biblioteca **serializadora** contidos no **dispositivo Azure IoT SDK para C**. Com as informações fornecidas deve ter uma boa compreensão de como usar modelos para enviar eventos e receber mensagens do IoT Hub.

Isto também conclui a série em três partes sobre como desenvolver aplicações com o **dispositivo Azure IoT SDK para C**. Isto deve ser informação suficiente para não só começar, mas também dar-lhe uma compreensão completa de como as APIs funcionam. Para mais informações, há algumas amostras no SDK não abrangidas aqui. Caso contrário, a [documentação Azure IoT SDK](https://github.com/Azure/azure-iot-sdk-c) é um bom recurso para informações adicionais.

Para saber mais sobre o desenvolvimento para o IoT Hub, consulte os [Azure IoT SDKs.](iot-hub-devguide-sdks.md)

Para explorar ainda mais as capacidades do IoT Hub, consulte [implementar ia para dispositivos de borda com Azure IoT Edge](../iot-edge/quickstart-linux.md).