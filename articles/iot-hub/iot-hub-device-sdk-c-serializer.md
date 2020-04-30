---
title: Dispositivo Azure IoT SDK para C - Serializer Microsoft Docs
description: Como utilizar a biblioteca Serializer no dispositivo Azure IoT SDK para C para criar aplicações de dispositivos que comunicam com um hub IoT.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 09/06/2016
ms.author: robinsh
ms.custom: amqp
ms.openlocfilehash: d4916d651638f0d1dbb4f10e0e0732f5c330d300
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81767026"
---
# <a name="azure-iot-device-sdk-for-c--more-about-serializer"></a>Dispositivo Azure IoT SDK para C – mais sobre serializador

O primeiro artigo desta série introduziu a [introdução ao dispositivo Azure IoT SDK para C](iot-hub-device-sdk-c-intro.md). O próximo artigo forneceu uma descrição mais detalhada do [dispositivo Azure IoT SDK para C -- IoTHubClient](iot-hub-device-sdk-c-iothubclient.md). Este artigo completa a cobertura do SDK fornecendo uma descrição mais detalhada do componente restante: a biblioteca **serializer.**

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

O artigo introdutório descrevia como usar a biblioteca **serializer** para enviar eventos e receber mensagens do IoT Hub. Neste artigo, estendemos essa discussão fornecendo uma explicação mais completa sobre como modelar os seus dados com a linguagem macro **serializador.** O artigo também inclui mais detalhes sobre como a biblioteca serializa mensagens (e em alguns casos como pode controlar o comportamento de serialização). Também descreveremos alguns parâmetros que pode modificar que determinam o tamanho dos modelos que cria.

Por fim, o artigo revisita alguns tópicos abordados em artigos anteriores, como mensagens e tratamento de propriedades. Como vamos descobrir, essas funcionalidades funcionam da mesma forma usando a biblioteca **serializer** como fazem com a biblioteca **IoTHubClient.**

Tudo descrito neste artigo é baseado nas amostras de SDK **serializer.** Se quiser acompanhar, consulte as aplicações **simplesample\_amqp** e **\_simplesample http** incluídas no dispositivo Azure IoT SDK para C.

Pode encontrar o [**dispositivo Azure IoT SDK para**](https://github.com/Azure/azure-iot-sdk-c) o repositório C GitHub e visualizar detalhes da API na [referência C API](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/).

## <a name="the-modeling-language"></a>A linguagem de modelação

O [dispositivo Azure IoT SDK para c](iot-hub-device-sdk-c-intro.md) artigo nesta série introduziu o dispositivo **Azure IoT SDK para** a linguagem de modelação C através do exemplo fornecido na aplicação **amqp\_simplesample:**

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

Como pode ver, a linguagem de modelação baseia-se em macros C. Comece sempre a sua definição com ESPAÇO DE **NOMES\_BEGIN** e termine sempre com ESPAÇO DE **NOMEFinal\_**. É comum nomear o espaço de nome para a sua empresa ou, como neste exemplo, o projeto em que está a trabalhar.

O que entra no espaço de nome são definições de modelo. Neste caso, há um único modelo para um anemómetro. Mais uma vez, o modelo pode ser nomeado qualquer coisa, mas tipicamente o modelo é nomeado para o dispositivo ou tipo de dados que pretende trocar com o IoT Hub.  

Os modelos contêm uma definição dos eventos que pode ingressar no IoT Hub (os *dados),* bem como as mensagens que pode receber do IoT Hub (as *ações).* Como pode ver pelo exemplo, os eventos têm um tipo e um nome; as ações têm um nome e parâmetros opcionais (cada um com um tipo).

O que não está demonstrado nesta amostra são tipos de dados adicionais que são suportados pelo SDK. Vamos cobrir isto a seguir.

> [!NOTE]
> O IoT Hub refere-se aos dados que um dispositivo envia para ele como *eventos,* enquanto a linguagem de modelação refere-se a ele como *dados* (definidos usando **WITH_DATA).** Da mesma forma, o IoT Hub refere-se aos dados que envia para os dispositivos como *mensagens,* enquanto a linguagem de modelação refere-se a ele como *ações* (definidas usando **WITH_ACTION**). Esteja ciente de que estes termos podem ser utilizados intercambiavelmente neste artigo.
> 
> 

## <a name="supported-data-types"></a>Tipos de dados suportados

Os seguintes tipos de dados são suportados em modelos criados com a biblioteca **serializador:**

| Tipo | Descrição |
| --- | --- |
| double |número de ponto flutuante de precisão dupla |
| int |Inteiro de 32 bits |
| float |único número de ponto flutuante de precisão |
| long |inteiro longo |
| int8\_t |8 bits inteiro |
| int16\_t |16 bits inteiro |
| int32\_t |Inteiro de 32 bits |
| int64\_t |64 bits inteiro |
| bool |boolean |
| ascii\_\_char ptr |Cadeia ASCII |
| OFFSET\_DATA\_\_EDM |data compensada |
| EDM\_GUID |GUID |
| EDM\_BINARY |binary |
| DECLARAR\_STRUCT |tipo de dados complexo |

Vamos começar com o último tipo de dados. O **\_DECLARE STRUCT** permite-lhe definir tipos de dados complexos, que são agrupamentos de outros tipos primitivos. Estes agrupamentos permitem-nos definir um modelo que se parece com este:

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

O nosso modelo contém um único evento de dados do tipo **TestType**. **TestType** é um tipo complexo que inclui vários membros, que demonstram coletivamente os tipos primitivos suportados pela linguagem de modelação **serializador.**

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

Basicamente, estamos a atribuir um valor a todos os membros da estrutura de **teste** e depois a ligar para o **SendAsync** para enviar o evento de dados do **Teste** para a nuvem. **SendAsync** é uma função de ajudante que envia um único evento de dados para o IoT Hub:

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

Esta função serializa o evento de dados dado e envia-o para o IoT Hub utilizando **o IoTHubClient\_SendEventAsync**. Este é o mesmo código discutido em artigos anteriores **(SendAsync** encapsula a lógica numa função conveniente).

Uma outra função de ajudante utilizada no código anterior é **GetDateTimeOffset**. Esta função transforma o tempo dado num valor de TEMPO **\_DE DATA\_\_DE DATA EDM tipo:**

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

Se executar este código, a seguinte mensagem é enviada para ioT Hub:

```C
{"aDouble":1.100000000000000, "aInt":2, "aFloat":3.000000, "aLong":4, "aInt8":5, "auInt8":6, "aInt16":7, "aInt32":8, "aInt64":9, "aBool":true, "aAsciiCharPtr":"ascii string 1", "aDateTimeOffset":"2015-09-14T21:18:21Z", "aGuid":"00010203-0405-0607-0809-0A0B0C0D0E0F", "aBinary":"AQID"}
```

Note que a serialização está em JSON, que é o formato gerado pela biblioteca **serializer.** Note também que cada membro do objeto JSON serializado corresponde aos membros do **TestType** que definimos no nosso modelo. Os valores também correspondem exatamente aos utilizados no código. No entanto, note que os dados binários são codificados pela base64: "AQID" é a codificação base64 de {0x01, 0x02, 0x03}.

Este exemplo demonstra a vantagem de usar a biblioteca **serializer** -- permite-nos enviar jSON para a nuvem, sem ter que lidar explicitamente com a serialização na nossa aplicação. Tudo o que temos que nos preocupar é definir os valores dos eventos de dados no nosso modelo e, em seguida, chamar apis simples para enviar esses eventos para a nuvem.

Com esta informação, podemos definir modelos que incluem a gama de tipos de dados suportados, incluindo tipos complexos (poderíamos até incluir tipos complexos dentro de outros tipos complexos). No entanto, o JSON serializado gerado pelo exemplo acima traz um ponto importante. *Como* enviamos dados com a biblioteca **serializador** determina exatamente como o JSON é formado. Este ponto em particular é o que vamos cobrir a seguir.

## <a name="more-about-serialization"></a>Mais sobre serialização

A secção anterior destaca um exemplo da saída gerada pela biblioteca **serializador.** Nesta secção, vamos explicar como a biblioteca serializa dados e como pode controlar esse comportamento usando as APIs de serialização.

Para promover a discussão sobre a serialização, trabalharemos com um novo modelo baseado num termóstato. Primeiro, vamos fornecer alguns antecedentes sobre o cenário que estamos a tentar resolver.

Queremos modelar um termóstato que mede a temperatura e a humidade. Cada pedaço de dados será enviado para o IoT Hub de forma diferente. Por predefinição, o termóstato insere um evento de temperatura uma vez a cada 2 minutos; um evento de humidade é ingressado uma vez a cada 15 minutos. Quando qualquer um dos eventos é ineferido, deve incluir um carimbo temporal que mostre o tempo em que a temperatura ou humidade correspondente foi medida.

Perante este cenário, vamos demonstrar duas formas diferentes de modelar os dados, e vamos explicar o efeito que a modelação tem na saída serializada.

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

Note que o modelo inclui dois eventos de dados: **Temperatura** e **Humidade.** Ao contrário dos exemplos anteriores, o tipo de cada evento é uma estrutura definida com recurso ao **DECLARE\_STRUCT**. **TemperaturaEvento** inclui uma medição de temperatura e um carimbo de tempo; **HumidityEvent** contém uma medição de humidade e um carimbo de tempo. Este modelo dá-nos uma forma natural de modelar os dados para o cenário acima descrito. Quando enviarmos um evento para a nuvem, ou enviaremos uma temperatura/carimbo de tempo ou um par de humidade/carimbo de tempo.

Podemos enviar um evento de temperatura para a nuvem usando código como o seguinte:

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

Usaremos valores codificados para temperatura e humidade no código da amostra, mas imaginem que estamos a recuperar estes valores, amostrando os sensores correspondentes no termóstato.

O código acima utiliza o ajudante **GetDateTimeOffset** que foi introduzido anteriormente. Por razões que ficarão claras mais tarde, este código separa explicitamente a tarefa de serializar e enviar o evento. O código anterior serializa o evento de temperatura num tampão. Em seguida, **o sendMessage** é uma função auxiliar (incluída em **\_amqp simplesample)** que envia o evento para ioT Hub:

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

Este código é um subconjunto do ajudante **SendAsync** descrito na secção anterior, por isso não vamos revê-lo aqui.

Quando executamos o código anterior para enviar o evento Temperatura, esta forma serializada do evento é enviada para o IoT Hub:

```C
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

Estamos enviando uma temperatura, que é de tipo **TemperatureEvent,** e que a estrutura contém um membro **da Temperatura** e **do Tempo.** Isto reflete-se diretamente nos dados serializados.

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

Com este modelo, pode imaginar como eventos adicionais podem ser facilmente adicionados. Define mais estruturas utilizando **o DECLARE\_STRUCT,** e inclui o evento correspondente no modelo utilizando COM **\_DADOS**.

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

Neste caso, eliminámos as macros **DECLARE\_STRUCT** e estamos simplesmente a definir os itens de dados do nosso cenário usando tipos simples da linguagem de modelação.

Só por enquanto, ignore o evento **da Hora.** Com isso à parte, aqui está o código para ingresso **Temperatura:**

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

Este código envia o seguinte evento serializado para ioT Hub:

```C
{"Temperature":75}
```

E o código para o envio do evento Humidade aparece da seguinte forma:

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

Até agora ainda não há surpresas. Agora vamos mudar a forma como usamos a macro SERIALIZE.

A macro **SERIALIZE** pode levar vários eventos de dados como argumentos. Isto permite-nos serializar o evento **temperatura** e **humidade** juntos e enviá-los para o IoT Hub numa única chamada:

```C
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Pode supor que o resultado deste código é que dois eventos de dados são enviados para o IoT Hub:

{"Temperatura":75}, {"Humidade":45} ]

Por outras palavras, pode esperar que este código seja o mesmo que enviar **temperatura** e **humidade** separadamente. É apenas uma conveniência passar os dois eventos para o **SERIALIZE** na mesma chamada. No entanto, não é esse o caso. Em vez disso, o código acima envia este único evento de dados para o IoT Hub:

{"Temperatura":75, "Humidade":45}

Isto pode parecer estranho porque o nosso modelo define **temperatura** e **humidade** como dois eventos *separados:*

```C
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Mais do que isso, não modelámos estes eventos onde a **Temperatura** e a **Humidade** estão na mesma estrutura:

```C
DECLARE_STRUCT(TemperatureAndHumidityEvent,
int, Temperature,
int, Humidity,
);

DECLARE_MODEL(Thermostat,
WITH_DATA(TemperatureAndHumidityEvent, TemperatureAndHumidity),
);
```

Se usássemos este modelo, seria mais fácil entender como a **Temperatura** e a **Humidade** seriam enviadas na mesma mensagem serializada. No entanto, pode não ser claro por que funciona assim quando passa ambos os eventos de dados para **SERIALIZE** usando o model 2.

Este comportamento é mais fácil de entender se souberes as suposições que a biblioteca de **serializadors** está a fazer. Para dar sentido a isto, voltemos ao nosso modelo:

```C
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Pense neste modelo em termos orientados para o objeto. Neste caso, estamos a modelar um dispositivo físico (um termóstato) e esse dispositivo inclui atributos como **Temperatura** e **Humidade**.

Podemos enviar todo o estado do nosso modelo com código como o seguinte:

```C
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity, thermostat->Time) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Assumindo que os valores de Temperatura, Humidade e Tempo estão definidos, assistiríamos a um evento como este enviado ao IoT Hub:

```C
{"Temperature":75, "Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

Por vezes, só pode querer enviar *algumas* propriedades do modelo para a nuvem (isto é especialmente verdade se o seu modelo contiver um grande número de eventos de dados). É útil enviar apenas um subconjunto de eventos de dados, como no nosso exemplo anterior:

```C
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

Isto gera exatamente o mesmo evento serializado como se tivéssemos definido um **TemperatureEvent** com um membro **temperatura** e **tempo,** tal como fizemos com o modelo 1. Neste caso, conseguimos gerar exatamente o mesmo evento serializado usando um modelo diferente (modelo 2) porque chamávamos **SERIALIZE** de uma forma diferente.

O ponto importante é que se passar vários eventos de dados para **serialIZE,** então assume que cada evento é uma propriedade num único objeto JSON.

A melhor abordagem depende de si e da forma como pensa sobre o seu modelo. Se você está enviando "eventos" para a nuvem e cada evento contém um conjunto definido de propriedades, então a primeira abordagem faz muito sentido. Nesse caso, utilizaria o **DECLARE\_STRUCT** para definir a estrutura de cada evento e, em seguida, incluí-los no seu modelo com a macro **\_COM DADOS.** Depois envias cada evento como fizemos no primeiro exemplo acima. Nesta abordagem, só passaria um único evento de dados para o **SERIALIZER**.

Se pensar no seu modelo de forma orientada para o objeto, então a segunda abordagem pode servir-lhe. Neste caso, os elementos definidos com **DADOS\_** são as "propriedades" do seu objeto. Passas qualquer subconjunto de eventos para o **SERIALIZE** que quiseres, dependendo da quantidade do teu estado de "objeto" que queres enviar para a nuvem.

A abordagem de Nether é certa ou errada. Basta estar ciente de como a biblioteca de **serializer** funciona, e escolha a abordagem de modelação que melhor se adequa às suas necessidades.

## <a name="message-handling"></a>Processamento de mensagens

Até agora este artigo só discutiu o envio de eventos para o IoT Hub, e não abordou mensagens de receção. A razão para isso é que o que precisamos de saber sobre receber mensagens foi amplamente abordado no artigo [Dispositivo Azure IoT SDK para C](iot-hub-device-sdk-c-intro.md). Lembre-se desse artigo que processa mensagens registando uma função de chamada de mensagem:

```C
IoTHubClient_SetMessageCallback(iotHubClientHandle, IoTHubMessage, myWeather)
```

Em seguida, escreve a função de chamada que é invocada quando uma mensagem é recebida:

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

**SetAirResistance** é então chamado quando essa mensagem é enviada para o seu dispositivo.

O que ainda não explicámos é como é a versão serializada da mensagem. Por outras palavras, se quiser enviar uma mensagem **SetAirResistance** para o seu dispositivo, como é que isso se parece?

Se estiver a enviar uma mensagem para um dispositivo, fá-lo-ia através do serviço Azure IoT SDK. Ainda precisas de saber que corda enviar para invocar uma ação em particular. O formato geral para o envio de uma mensagem aparece da seguinte forma:

```C
{"Name" : "", "Parameters" : "" }
```

Está a enviar um objeto JSON serializado com duas propriedades: **O nome** é o nome da ação (mensagem) e os **parâmetros** contêm os parâmetros dessa ação.

Por exemplo, para invocar **a SetAirResistance** pode enviar esta mensagem para um dispositivo:

```C
{"Name" : "SetAirResistance", "Parameters" : { "Position" : 5 }}
```

O nome de ação deve corresponder exatamente a uma ação definida no seu modelo. Os nomes dos parâmetros também devem coincidir. Note também a sensibilidade do caso. **O nome** e **os parâmetros** são sempre maiúsculos. Certifique-se de que corresponde ao caso do seu nome de ação e parâmetros no seu modelo. Neste exemplo, o nome de ação é "SetAirResistance" e não "setairresistance".

As outras duas ações **TurnFanOn** e **TurnFanOff** podem ser invocadas enviando estas mensagens para um dispositivo:

```C
{"Name" : "TurnFanOn", "Parameters" : {}}
{"Name" : "TurnFanOff", "Parameters" : {}}
```

Esta secção descreveu tudo o que precisa de saber ao enviar eventos e receber mensagens com a biblioteca de **serializadores.** Antes de seguir em frente, vamos cobrir alguns parâmetros que pode configurar que controlam o tamanho do seu modelo.

## <a name="macro-configuration"></a>Configuração macro

Se estiver a usar a biblioteca **Serializer,** uma parte importante do SDK para estar ciente encontra-se na biblioteca azure-c-shared-utility.

Se clonou o repositório Azure-iot-sdk-c do GitHub `git submodule update --init` e emitiu o comando, então encontrará esta biblioteca de utilidade partilhada aqui:

```C
.\\c-utility
```

Se não clonou a biblioteca, pode encontrá-la [aqui.](https://github.com/Azure/azure-c-shared-utility)

Dentro da biblioteca de utilidade partilhada, encontrará a seguinte pasta:

```C
azure-c-shared-utility\\macro\_utils\_h\_generator.
```

Esta pasta contém uma solução Visual Studio chamada **macro\_utils\_h\_generator.sln:**

  ![Screenshot da solução do Estúdio Visual maco_utils_h_generator](media/iot-hub-device-sdk-c-serializer/01-macro_utils_h_generator.png)

O programa nesta solução gera o ficheiro **macro\_utils.h.** Há um ficheiro\_macro utils.h padrão incluído com o SDK. Esta solução permite modificar alguns parâmetros e, em seguida, recriar o ficheiro cabeçalho com base nestes parâmetros.

Os dois parâmetros-chave a preocupar são **os nArithmtic** e **os nMacroParameters,** que são definidos nestas duas linhas encontradas em macro\_utils.tt:

```C
<#int nArithmetic=1024;#>
<#int nMacroParameters=124;/*127 parameters in one macro deﬁnition in C99 in chapter 5.2.4.1 Translation limits*/#>
```

Estes valores são os parâmetros padrão incluídos com o SDK. Cada parâmetro tem o seguinte significado:

* nMacroParâmetros – Controla quantos parâmetros\_pode ter numa definição macro DECLARE MODEL.
* nAraritthmetic – Controla o número total de membros permitidos num modelo.

A razão pela qual estes parâmetros são importantes é porque controlam o tamanho do seu modelo. Por exemplo, considere esta definição de modelo:

```C
DECLARE_MODEL(MyModel,
WITH_DATA(int, MyData)
);
```

Como mencionado anteriormente, **o MODELO DECLARE\_** é apenas um C macro. Os nomes do modelo e a declaração **COM\_DADOS** (mais uma macro) são parâmetros do MODELO **DECLARE\_**. **nMacroParameters** define quantos parâmetros podem ser incluídos no **MODELO DECLARE\_**. Efetivamente, isto define quantos eventos de dados e declarações de ação pode ter. Como tal, com o limite padrão de 124 isto significa que você pode definir um modelo com uma combinação de cerca de 60 ações e eventos de dados. Se tentar ultrapassar este limite, receberá erros compiladores semelhantes a este:

  ![Screenshot de erros compiladores de parâmetros macro](media/iot-hub-device-sdk-c-serializer/02-nMacroParametersCompilerErrors.png)

O parâmetro **nArithmetic** é mais sobre o funcionamento interno da linguagem macro do que a sua aplicação.  Controla o número total de membros que pode ter no seu modelo, incluindo **macros DECLARE_STRUCT.** Se começar a ver erros de compilador como este, então deve tentar aumentar **o nArithmetic:**

   ![Screenshot de erros do compilador aritmético](media/iot-hub-device-sdk-c-serializer/03-nArithmeticCompilerErrors.png)

Se quiser alterar estes parâmetros, modifique\_os valores do ficheiro\_macro\_\_utils.tt, recompile a solução macro utils h generator.sln e execute o programa compilado. Quando o fizer, um\_novo ficheiro macro utils.h é gerado e colocado no . \\diretório inc comum.\\

Para utilizar a nova versão\_do macro utils.h, remova o pacote NuGet **serializador** da sua solução e no seu lugar inclua o projeto **serializer** Visual Studio. Isto permite que o seu código compile contra o código fonte da biblioteca serializador. Isto inclui o\_macro utils.h atualizado. Se quiser fazer isto por **uma\_amostra simples,** comece por remover o pacote NuGet para a biblioteca serializador da solução:

   ![Screenshot de remover o pacote NuGet para a biblioteca serializer](media/iot-hub-device-sdk-c-serializer/04-serializer-github-package.png)

Em seguida, adicione este projeto à sua solução Visual Studio:

> . \\c\\\\serializer\\\\construir janelas serializer.vcxproj
> 
> 

Quando terminar, a sua solução deve ser assim:

   ![Screenshot da solução simplesample_amqp Visual Studio](media/iot-hub-device-sdk-c-serializer/05-serializer-project.png)

Agora, quando compila a sua\_solução, o macro utils.h atualizado está incluído no seu binário.

Note que o aumento destes valores suficientemente alto pode exceder os limites do compilador. Até agora, o **nMacroParâmetros** é o parâmetro principal com o qual se deve estar em causa. A especificação C99 especifica que um mínimo de 127 parâmetros são permitidos numa definição macro. O compilador da Microsoft segue exatamente a especificação (e tem um limite de 127), pelo que não poderá aumentar os **nMacroParâmetros** para além do padrão. Outros compiladores podem permitir-lhe fazê-lo (por exemplo, o compilador GNU suporta um limite mais elevado).

Até agora cobrimos tudo o que precisas de saber sobre como escrever código com a biblioteca de **serializadores.** Antes de concluir, vamos revisitar alguns tópicos de artigos anteriores sobre os quais poderá estar a pensar.

## <a name="the-lower-level-apis"></a>As APIs de nível inferior
A aplicação da amostra em que este artigo se focou é **simples amostra\_amqp**. Esta amostra utiliza as APIs de nível superior (as APIs**não-LL**) para enviar eventos e receber mensagens. Se utilizar estas APIs, corre uma linha de fundo que cuida tanto do envio de eventos como da receção de mensagens. No entanto, pode utilizar as APIs de nível inferior (LL) para eliminar este fio de fundo e assumir o controlo explícito quando envia eventos ou recebe mensagens da nuvem.

Tal como descrito num [artigo anterior,](iot-hub-device-sdk-c-iothubclient.md)existe um conjunto de funções que consiste nas APIs de nível superior:

* IoTHubClient\_CriaFromConnectionString
* IoTHubClient\_SendEventAsync
* IoTHubClient\_SetMessageCallback
* Destruição do IoTHubClient\_

Estas APIs são demonstradas em **amqp de\_amostra simples.**

Há também um conjunto análogo de APIs de nível inferior.

* IoTHubClient\_LL\_CreateFromConnectionString
* IoTHubClient\_LL\_SendEventAsync
* IoTHubClient\_LL\_SetMessageCallback
* IoTHubClient\_\_LL Destruir

Note que as APIs de nível inferior funcionam exatamente da mesma forma que descritas nos artigos anteriores. Pode utilizar o primeiro conjunto de APIs se quiser um fio de fundo para lidar com o envio de eventos e receber mensagens. Utiliza o segundo conjunto de APIs se quiser um controlo explícito quando envia e recebe dados do IoT Hub. Qualquer um dos conjuntos de APIs funciona igualmente bem com a biblioteca de **serializer.**

Para um exemplo de como as APIs de nível inferior são usadas com a biblioteca **serializer,** consulte a aplicação **http\_simplesample.**

## <a name="additional-topics"></a>Tópicos adicionais
Alguns outros tópicos que merecem ser mencionados novamente são o manuseamento de propriedades, usando credenciais alternativas de dispositivos e opções de configuração. Estes são todos os tópicos abordados num [artigo anterior.](iot-hub-device-sdk-c-iothubclient.md) O ponto principal é que todas estas funcionalidades funcionam da mesma forma com a biblioteca **serializer** como fazem com a biblioteca **IoTHubClient.** Por exemplo, se pretender anexar propriedades a um evento do seu modelo, utiliza propriedades **IoTHubMessage\_** e **Map**\_**AddorUpdate,** da mesma forma que descrito anteriormente:

```C
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

Não importa se o evento foi gerado a partir da biblioteca de **serializadors** ou se foi criado manualmente utilizando a biblioteca **IoTHubClient.**

Para as credenciais alternativas do dispositivo, a utilização do **IoTHubClient\_LL\_Create** funciona tão bem como o **IoTHubClient\_CreateFromConnectionString** para a locar um **MANI OCLIENTE\_\_IOTHUB**.

Finalmente, se estiver a usar a biblioteca **serializer,** pode definir opções de configuração com **\_ioTHubClient LL\_SetOption** tal como fez ao utilizar a biblioteca **IoTHubClient.**

Uma característica que é única na biblioteca de **serializadores** são as APIs de inicialização. Antes de começar a trabalhar com a biblioteca, deve ligar para o **serializer\_init:**

```C
serializer_init(NULL);
```

Isto é feito pouco antes de ligar para **ioTHubClient\_CreateFromConnectionString**.

Da mesma forma, quando terminar de trabalhar com a biblioteca, a última chamada que fará é para **deinitem serializador:\_**

```C
serializer_deinit();
```

Caso contrário, todas as outras funcionalidades listadas acima funcionam da mesma forma na biblioteca **serializer** como na biblioteca **IoTHubClient.** Para mais informações sobre qualquer um destes tópicos, consulte o [artigo anterior](iot-hub-device-sdk-c-iothubclient.md) nesta série.

## <a name="next-steps"></a>Passos seguintes

Este artigo descreve detalhadamente os aspetos únicos da biblioteca **serializador** contida no **dispositivo Azure IoT SDK para C**. Com as informações fornecidas deve ter uma boa compreensão de como usar modelos para enviar eventos e receber mensagens do IoT Hub.

Isto também conclui a série em três partes sobre como desenvolver aplicações com o **dispositivo Azure IoT SDK para C**. Isto deve ser informação suficiente para não só começar, mas dar-lhe uma compreensão completa de como as APIs funcionam. Para obter informações adicionais, há algumas amostras no SDK que não estão abrangidas aqui. Caso contrário, a [documentação Azure IoT SDK](https://github.com/Azure/azure-iot-sdk-c) é um bom recurso para informações adicionais.

Para saber mais sobre o desenvolvimento para o IoT Hub, consulte os [SDKs Azure IoT](iot-hub-devguide-sdks.md).

Para explorar ainda mais as capacidades do IoT Hub, consulte [a implantação de II para dispositivos de borda com ponta Azure IoT](../iot-edge/tutorial-simulate-device-linux.md)Edge .