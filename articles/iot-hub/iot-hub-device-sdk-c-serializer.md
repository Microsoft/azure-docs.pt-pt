---
title: SDK do dispositivo IoT do Azure para C-serializador | Microsoft Docs
description: Como usar a biblioteca do serializador no SDK do dispositivo IoT do Azure para C para criar aplicativos de dispositivo que se comunicam com um hub IoT.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 09/06/2016
ms.author: robinsh
ms.openlocfilehash: a18f52f0d0979477ff8d6de6745694676f4b4d0e
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883155"
---
# <a name="azure-iot-device-sdk-for-c--more-about-serializer"></a>SDK do dispositivo IoT do Azure para C – mais sobre o serializador

O primeiro artigo desta série apresentou a [introdução ao SDK do dispositivo IOT do Azure para C](iot-hub-device-sdk-c-intro.md). O próximo artigo forneceu uma descrição mais detalhada do [SDK do dispositivo IOT do Azure para C--IoTHubClient](iot-hub-device-sdk-c-iothubclient.md). Este artigo conclui a cobertura do SDK fornecendo uma descrição mais detalhada do componente restante: a biblioteca do **serializador** .

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

O artigo introdutório descreveu como usar a biblioteca do serializador para enviar eventos e receber mensagens do Hub IOT. Neste artigo, estendemos essa discussão fornecendo uma explicação mais completa de como modelar seus dados com a linguagem de macro do serializador. O artigo também inclui mais detalhes sobre como a biblioteca serializa as mensagens (e, em alguns casos, como você pode controlar o comportamento de serialização). Também Descreveremos alguns parâmetros que podem ser modificados para determinar o tamanho dos modelos que você criar.

Por fim, o artigo revisita alguns tópicos abordados nos artigos anteriores, como manipulação de mensagens e propriedades. Como descobriremos, esses recursos funcionam da mesma maneira usando a biblioteca do **serializador** como fazem com a biblioteca **IoTHubClient** .

Todos os itens descritos neste artigo baseiam- se nos exemplos do SDK do serializador. Se você quiser acompanhar, consulte os aplicativos de **http\_SimpleSample AMQP** e **SimpleSample\_** incluídos no SDK do dispositivo IOT do Azure para C.

Você pode encontrar o [**SDK do dispositivo IOT do Azure para**](https://github.com/Azure/azure-iot-sdk-c) o repositório do GitHub c e exibir detalhes da API na [referência da API C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/).

## <a name="the-modeling-language"></a>A linguagem de modelagem

O artigo [SDK do dispositivo IOT do Azure para c](iot-hub-device-sdk-c-intro.md) nesta série introduziu a linguagem de modelagem do **SDK do dispositivo IOT do Azure para c** por meio do exemplo fornecido no aplicativo **\_SimpleSample AMQP** :

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

Como você pode ver, a linguagem de modelagem é baseada em macros C. Você sempre começa sua definição com **begin\_namespace** e sempre termina com **o\_namespace end**. É comum nomear o namespace para sua empresa ou, como neste exemplo, o projeto no qual você está trabalhando.

O que vai dentro do namespace são definições de modelo. Nesse caso, há um único modelo para um anemômetro. Mais uma vez, o modelo pode ser nomeado como qualquer coisa, mas normalmente o modelo é nomeado para o dispositivo ou tipo de dados que você deseja trocar com o Hub IoT.  

Os modelos contêm uma definição dos eventos que você pode ingressar no Hub IoT (os *dados*), bem como as mensagens que você pode receber do Hub IOT (as *ações*). Como você pode ver no exemplo, os eventos têm um tipo e um nome; as ações têm um nome e parâmetros opcionais (cada um com um tipo).

O que não é demonstrado neste exemplo são tipos de dados adicionais que são suportados pelo SDK. Abordaremos isso em seguida.

> [!NOTE]
> O Hub IoT refere-se aos dados que um dispositivo envia a ele como *eventos*, enquanto a linguagem de modelagem faz referência a ele como *dados* (definidos usando **WITH_DATA**). Da mesma forma, o Hub IoT refere-se aos dados que você envia para dispositivos como *mensagens*, enquanto a linguagem de modelagem faz referência a ele como *ações* (definidas usando **WITH_ACTION**). Lembre-se de que esses termos podem ser usados de maneira intercambiável neste artigo.
> 
> 

## <a name="supported-data-types"></a>Tipos de dados com suporte

Os seguintes tipos de dados têm suporte em modelos criados com a biblioteca do serializador:

| Type | Descrição |
| --- | --- |
| double |número de ponto flutuante de precisão dupla |
| int |inteiro de 32 bits |
| float |número de ponto flutuante de precisão única |
| long |inteiro longo |
| int8\_t |inteiro de 8 bits |
| int16\_t |inteiro de 16 bits |
| int32\_t |inteiro de 32 bits |
| int64\_t |inteiro de 64 bits |
| bool |boolean |
| ascii\_char\_ptr |Cadeia de caracteres ASCII |
| EDM\_DATE\_TIME\_OFFSET |deslocamento de data e hora |
| EDM\_GUID |GUID |
| EDM\_BINARY |binary |
| DECLARE\_STRUCT |tipo de dados complexo |

Vamos começar com o último tipo de dados. O **DECLARE\_STRUCT** permite que você defina tipos de dados complexos, que são agrupamentos dos outros tipos primitivos. Esses agrupamentos nos permitem definir um modelo parecido com este:

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

Nosso modelo contém um único evento de dados dotipo TestType. TestType é um tipo complexo que inclui vários membros, que demonstram coletivamente os tipos primitivos suportados pela linguagem de modelagem do **serializador** .

Com um modelo como esse, podemos escrever código para enviar dados ao Hub IoT que aparece da seguinte maneira:

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

Basicamente, estamos atribuindo um valor a todos os membros da estrutura de **teste** e, em seguida, chamando **SendAsync** para enviar o evento **Test** data para a nuvem. **SendAsync** é uma função auxiliar que envia um único evento de dados ao Hub IOT:

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

Essa função serializa o evento de dados fornecido e o envia para o Hub IOT **usando\_IoTHubClient SendEventAsync**. Este é o mesmo código discutido nos artigos anteriores (**SendAsync** encapsula a lógica em uma função conveniente).

Uma outra função auxiliar usada no código anterior é **getDateTimeOffset**. Essa função transforma o tempo determinado em um valor do tipo **EDM\_data\_hora\_offset**:

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

Se você executar esse código, a seguinte mensagem será enviada ao Hub IoT:

```C
{"aDouble":1.100000000000000, "aInt":2, "aFloat":3.000000, "aLong":4, "aInt8":5, "auInt8":6, "aInt16":7, "aInt32":8, "aInt64":9, "aBool":true, "aAsciiCharPtr":"ascii string 1", "aDateTimeOffset":"2015-09-14T21:18:21Z", "aGuid":"00010203-0405-0607-0809-0A0B0C0D0E0F", "aBinary":"AQID"}
```

Observe que a serialização está em JSON, que é o formato gerado pela biblioteca do serializador. Observe também que cada membro do objeto JSON serializado corresponde aos membros do Testfile que definimos em nosso modelo. Os valores também correspondem exatamente aos usados no código. No entanto, observe que os dados binários são codificados em base64: "AQID" é a codificação Base64 de {0x01, 0x02, 0x03}.

Este exemplo demonstra a vantagem de usar a biblioteca do serializador – ela nos permite enviar JSON para a nuvem, sem precisar lidar explicitamente com a serialização em nosso aplicativo. Tudo o que precisamos nos preocupar é definir os valores dos eventos de dados em nosso modelo e, em seguida, chamar APIs simples para enviar esses eventos para a nuvem.

Com essas informações, podemos definir modelos que incluem o intervalo de tipos de dados com suporte, incluindo tipos complexos (poderíamos até mesmo incluir tipos complexos dentro de outros tipos complexos). No entanto, o JSON serializado gerado pelo exemplo acima abre um ponto importante. A *maneira como* enviamos dados com a biblioteca do serializador determina exatamente como o JSON é formado. Esse ponto específico é o que abordaremos a seguir.

## <a name="more-about-serialization"></a>Mais sobre a serialização

A seção anterior realça um exemplo da saída gerada pela biblioteca do **serializador** . Nesta seção, explicaremos como a biblioteca serializa os dados e como você pode controlar esse comportamento usando as APIs de serialização.

Para avançar a discussão sobre serialização, trabalharemos com um novo modelo baseado em um termostato. Primeiro, vamos fornecer algum plano de fundo sobre o cenário que estamos tentando abordar.

Queremos modelar um termostato que mede a temperatura e a umidade. Cada parte dos dados será enviada ao Hub IoT de forma diferente. Por padrão, o termostato insere um evento de temperatura uma vez a cada 2 minutos; um evento de umidade é inserido uma vez a cada 15 minutos. Quando o evento é inserido, ele deve incluir um carimbo de data/hora que mostra a hora em que a temperatura ou umidade correspondente foi medida.

Considerando esse cenário, demonstraremos duas maneiras diferentes de modelar os dados e explicaremos o efeito que a modelagem tem na saída serializada.

### <a name="model-1"></a>Modelo 1

Esta é a primeira versão de um modelo que dá suporte ao cenário anterior:

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

Observe que o modelo inclui dois eventos de dados: **Temperatura** e **umidade**. Ao contrário dos exemplos anteriores, o tipo de cada evento é uma estrutura definida usando **Declare\_STRUCT**. **TemperatureEvent** inclui uma medição de temperatura e um carimbo de data/hora; **HumidityEvent** contém uma medida de umidade e um carimbo de data/hora. Esse modelo nos dá uma maneira natural de modelar os dados para o cenário descrito acima. Quando enviarmos um evento para a nuvem, enviaremos um par de temperatura/carimbo de data/hora ou de umidade/carimbo de data/hora.

Podemos enviar um evento de temperatura para a nuvem usando um código como o seguinte:

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

Usaremos valores embutidos em código para temperatura e umidade no código de exemplo, mas imagine que estamos realmente recuperando esses valores por meio da amostragem dos sensores correspondentes no termostato.

O código acima usa o auxiliar getDateTimeOffset que foi introduzido anteriormente. Por motivos que ficarão claros mais tarde, esse código separa explicitamente a tarefa de serialização e envio do evento. O código anterior serializa o evento de temperatura em um buffer. Em seguida, **SendMessage** é uma função auxiliar (incluída **em\_SimpleSample AMQP**) que envia o evento para o Hub IOT:

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

Esse código é um subconjunto do auxiliar **SendAsync** descrito na seção anterior, portanto, não vamos passar por ele novamente aqui.

Quando executamos o código anterior para enviar o evento de temperatura, essa forma serializada do evento é enviada ao Hub IoT:

```C
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

Estamos enviando uma temperatura que é do tipo **TemperatureEvent** e que struct contém uma **temperatura** e um membro de **tempo** . Isso é refletido diretamente nos dados serializados.

Da mesma forma, podemos enviar um evento de umidade com este código:

```C
thermostat->Humidity.Humidity = 45;
thermostat->Humidity.Time = GetDateTimeOffset(now);
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

O formulário serializado que é enviado ao Hub IoT aparece da seguinte maneira:

```C
{"Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

Novamente, isso é conforme o esperado.

Com esse modelo, você pode imaginar como os eventos adicionais podem ser adicionados facilmente. Você define mais estruturas usando **Declare\_struct**e inclui o evento correspondente no modelo usando **with\_data**.

Agora, vamos modificar o modelo para que ele inclua os mesmos dados, mas com uma estrutura diferente.

### <a name="model-2"></a>Modelo 2

Considere este modelo alternativo para aquele acima:

```C
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Nesse caso, eliminamos as macros **Declare\_struct** e estamos simplesmente definindo os itens de dados de nosso cenário usando tipos simples da linguagem de modelagem.

Apenas por enquanto, ignore o evento de **tempo** . Além disso, aqui está o código para a **temperatura**de entrada:

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

Esse código envia o seguinte evento serializado ao Hub IoT:

```C
{"Temperature":75}
```

E o código para enviar o evento de umidade aparece da seguinte maneira:

```C
thermostat->Humidity = 45;
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Esse código envia isso ao Hub IoT:

```C
{"Humidity":45}
```

Até agora, ainda não há surpresas. Agora, vamos alterar como usamos a macro SERIALIZE.

A macro **SERIALIZE** pode receber vários eventos de dados como argumentos. Isso nos permite serializar o evento de **temperatura** e **umidade** juntos e enviá-los ao Hub IOT em uma chamada:

```C
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Você pode imaginar que o resultado desse código é que dois eventos de dados são enviados ao Hub IoT:

[ {"Temperature":75}, {"Humidity":45} ]

Em outras palavras, você pode esperar que esse código seja o mesmo que o envio de **temperatura** e **umidade** separadamente. É apenas uma conveniência de passar ambos os eventos para **serializar** na mesma chamada. No entanto, esse não é o caso. Em vez disso, o código acima envia esse único evento de dados ao Hub IoT:

{"Temperature":75, "Humidity":45}

Isso pode parecer estranho porque nosso modelo define a **temperatura** e a **umidade** como dois eventos *separados* :

```C
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Mais até o ponto, não modelamos esses eventos em que a **temperatura** e a **umidade** estão na mesma estrutura:

```C
DECLARE_STRUCT(TemperatureAndHumidityEvent,
int, Temperature,
int, Humidity,
);

DECLARE_MODEL(Thermostat,
WITH_DATA(TemperatureAndHumidityEvent, TemperatureAndHumidity),
);
```

Se usarmos esse modelo, seria mais fácil entender como a **temperatura** e a **umidade** seriam enviadas na mesma mensagem serializada. No entanto, pode não estar claro por que funciona dessa maneira quando você passa os dois eventos de dados para serializar usando o modelo 2.

Esse comportamento é mais fácil de entender se você souber as suposições que a biblioteca do serializador está fazendo. Para dar sentido, vamos voltar ao nosso modelo:

```C
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Considere esse modelo em termos orientados a objeto. Nesse caso, estamos modelando um dispositivo físico (um termostato) e esse dispositivo inclui atributos como **temperatura** e **umidade**.

Podemos enviar todo o estado de nosso modelo com um código como o seguinte:

```C
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity, thermostat->Time) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Supondo que os valores de temperatura, umidade e tempo estejam definidos, vemos um evento como este enviado ao Hub IoT:

```C
{"Temperature":75, "Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

Às vezes, talvez você queira apenas enviar *algumas* Propriedades do modelo para a nuvem (isso é especialmente verdadeiro se o modelo contiver um grande número de eventos de dados). É útil enviar apenas um subconjunto de eventos de dados, como em nosso exemplo anterior:

```C
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

Isso gera exatamente o mesmo evento serializado como se tivéssemos definido um **TemperatureEvent** com uma **temperatura** e um membro de **tempo** , exatamente como fizemos com o modelo 1. Nesse caso, pudemos gerar exatamente o mesmo evento serializado usando um modelo diferente (modelo 2) porque chamamos a **serialização** de uma maneira diferente.

O ponto importante é que, se você passar vários eventos de dados para **serializar,** ele assumirá que cada evento é uma propriedade em um único objeto JSON.

A melhor abordagem depende de você e de como você imagina sobre seu modelo. Se você estiver enviando "eventos" para a nuvem e cada evento contiver um conjunto definido de propriedades, a primeira abordagem fará muito sentido. Nesse caso, você usaria **Declare\_struct** para definir a estrutura de cada evento e, em seguida, incluí-los em seu modelo com a macro **with\_data** . Em seguida, você envia cada evento como fizemos no primeiro exemplo acima. Nessa abordagem, você só passaria um único evento de dadospara o serializador.

Se você pensar em seu modelo de maneira orientada a objeto, a segunda abordagem pode ser adequada para você. Nesse caso, os elementos definidos usando **with\_data** são as "Propriedades" do seu objeto. Você passa qualquer subconjunto de eventos para serializar o que desejar, dependendo da quantidade de seu estado de "objeto" que você deseja enviar para a nuvem.

A abordagem nem está certa ou errada. Apenas esteja ciente de como a biblioteca do serializador funciona e escolha a abordagem de modelagem que melhor atenda às suas necessidades.

## <a name="message-handling"></a>Processamento de mensagens

Até o momento, este artigo abordou apenas o envio de eventos para o Hub IoT e não resolveu o recebimento de mensagens. O motivo disso é que o que precisamos saber sobre o recebimento de mensagens foi abordado em grande parte no artigo [SDK do dispositivo IOT do Azure para C](iot-hub-device-sdk-c-intro.md). Lembre-se de que você processa mensagens registrando uma função de retorno de chamada de mensagem no artigo:

```C
IoTHubClient_SetMessageCallback(iotHubClientHandle, IoTHubMessage, myWeather)
```

Em seguida, você escreve a função de retorno de chamada que é invocada quando uma mensagem é recebida:

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

Essa implementação de **IoTHubMessage** chama a função específica para cada ação em seu modelo. Por exemplo, se seu modelo definir esta ação:

```C
WITH_ACTION(SetAirResistance, int, Position)
```

Você deve definir uma função com esta assinatura:

```C
EXECUTE_COMMAND_RESULT SetAirResistance(ContosoAnemometer* device, int Position)
{
    (void)device;
    (void)printf("Setting Air Resistance Position to %d.\r\n", Position);
    return EXECUTE_COMMAND_SUCCESS;
}
```

Em seguida, **SetAirResistance** é chamado quando essa mensagem é enviada ao seu dispositivo.

O que ainda não explicamos é a aparência da versão serializada da mensagem. Em outras palavras, se você quiser enviar uma mensagem **SetAirResistance** para seu dispositivo, o que parece?

Se você estiver enviando uma mensagem para um dispositivo, faça isso por meio do SDK do serviço IoT do Azure. Você ainda precisa saber qual cadeia de caracteres enviar para invocar uma ação específica. O formato geral para enviar uma mensagem é exibido da seguinte maneira:

```C
{"Name" : "", "Parameters" : "" }
```

Você está enviando um objeto JSON serializado com duas propriedades: **Nome** é o nome da ação (mensagem) e os **parâmetros** contêm os parâmetros dessa ação.

Por exemplo, para invocar **SetAirResistance** , você pode enviar esta mensagem para um dispositivo:

```C
{"Name" : "SetAirResistance", "Parameters" : { "Position" : 5 }}
```

O nome da ação deve corresponder exatamente a uma ação definida em seu modelo. Os nomes de parâmetro também devem corresponder. Observe também a distinção entre maiúsculas e minúsculas. O **nome** e os **parâmetros** são sempre maiúsculos. Certifique-se de corresponder o caso do nome da ação e dos parâmetros em seu modelo. Neste exemplo, o nome da ação é "SetAirResistance" e não "SetAirResistance".

As duas outras ações **TurnFanOn** e **TurnFanOff** podem ser invocadas enviando essas mensagens para um dispositivo:

```C
{"Name" : "TurnFanOn", "Parameters" : {}}
{"Name" : "TurnFanOff", "Parameters" : {}}
```

Esta seção descreveu tudo o que você precisa saber ao enviar eventos e receber mensagens com a biblioteca do serializador. Antes de prosseguir, vamos abordar alguns parâmetros que você pode configurar que controlam o tamanho de seu modelo.

## <a name="macro-configuration"></a>Configuração de macro

Se você estiver usando a biblioteca do serializador, uma parte importante do SDK que deve estar ciente é encontrada na biblioteca Azure-c-Shared-Utility.

Se você tiver clonado o repositório Azure-IOT-SDK-c do GitHub usando a opção--recursiva, encontrará essa biblioteca de utilitários compartilhados aqui:

```C
.\\c-utility
```

Se você não tiver clonado a biblioteca, poderá encontrá-la [aqui](https://github.com/Azure/azure-c-shared-utility).

Na biblioteca de utilitários compartilhados, você encontrará a seguinte pasta:

```C
azure-c-shared-utility\\macro\_utils\_h\_generator.
```

Esta pasta contém uma solução do Visual Studio **chamada\_macro utils\_\_h Generator. sln**:

  ![Captura de tela da solução do Visual Studio maco_utils_h_generator](media/iot-hub-device-sdk-c-serializer/01-macro_utils_h_generator.png)

O programa nesta solução gera o arquivo **macro\_utils. h** . Há um arquivo padrão de\_macro utils. h incluído no SDK. Essa solução permite que você modifique alguns parâmetros e, em seguida, recrie o arquivo de cabeçalho com base nesses parâmetros.

Os dois parâmetros principais a serem preocupados são **nArithmetic** e **nMacroParameters** que são definidos nessas duas linhas encontradas em macro\_utils.tt:

```C
<#int nArithmetic=1024;#>
<#int nMacroParameters=124;/*127 parameters in one macro deﬁnition in C99 in chapter 5.2.4.1 Translation limits*/#>
```

Esses valores são os parâmetros padrão incluídos no SDK. Cada parâmetro tem o seguinte significado:

* nMacroParameters – controla quantos parâmetros você pode ter em uma definição de\_macro de modelo declare.
* nArithmetic – controla o número total de membros permitidos em um modelo.

O motivo pelo qual esses parâmetros são importantes é que eles controlam o tamanho do seu modelo. Por exemplo, considere esta definição de modelo:

```C
DECLARE_MODEL(MyModel,
WITH_DATA(int, MyData)
);
```

Como mencionado anteriormente, **Declare\_Model** é apenas uma macro C. Os nomes do modelo e a instrução **with\_data** (ainda outra macro) são parâmetros do **modelo Declare\_** . **nMacroParameters** define quantos parâmetros podem ser incluídos no **modelo de\_declaração**. Efetivamente, isso define a quantidade de declarações de evento e ação de dados que você pode ter. Dessa forma, com o limite padrão de 124, isso significa que você pode definir um modelo com uma combinação de aproximadamente 60 ações e eventos de dados. Se você tentar exceder esse limite, você receberá erros do compilador que se assemelham a este:

  ![Captura de tela de erros de compilador de parâmetros de macro](media/iot-hub-device-sdk-c-serializer/02-nMacroParametersCompilerErrors.png)

O parâmetro **nArithmetic** é mais sobre o funcionamento interno da linguagem de macro do que seu aplicativo.  Ele controla o número total de membros que você pode ter em seu modelo, incluindo macros **DECLARE_STRUCT** . Se você começar a ver erros do compilador como esse, você deve tentar aumentar **nArithmetic**:

   ![Captura de tela de erros de compilador aritméticos](media/iot-hub-device-sdk-c-serializer/03-nArithmeticCompilerErrors.png)

Se você quiser alterar esses parâmetros, modifique os valores no arquivo de macro\_utils.TT, recompile a solução de\_macro utils\_\_h Generator. sln e execute o programa compilado. Quando você fizer isso, um novo arquivo\_utils. h de macro será gerado e colocado no.\\ diretório\\comum da Inc.

Para usar a nova versão do macro\_utils. h, remova o pacote NuGet do **serializador** de sua solução e, em seu lugar, inclua o projeto do **serializador** do Visual Studio. Isso permite que seu código seja compilado em relação ao código-fonte da biblioteca do serializador. Isso inclui a macro\_utils. h atualizada. Se você quiser fazer isso para o **SimpleSample\_AMQP**, comece removendo o pacote NuGet da biblioteca do serializador da solução:

   ![Captura de tela da remoção do pacote NuGet para a biblioteca do serializador](media/iot-hub-device-sdk-c-serializer/04-serializer-github-package.png)

Em seguida, adicione este projeto à sua solução do Visual Studio:

> .\\c\\serializer\\build\\windows\\serializer.vcxproj
> 
> 

Quando terminar, sua solução deverá ter a seguinte aparência:

   ![Captura de tela da solução simplesample_amqp do Visual Studio](media/iot-hub-device-sdk-c-serializer/05-serializer-project.png)

Agora, quando você compilar sua solução, a macro\_utils. h atualizada será incluída no seu binário.

Observe que aumentar esses valores é alto o suficiente pode exceder os limites do compilador. Para esse ponto, o **nMacroParameters** é o principal parâmetro com o qual você deve se preocupar. A especificação C99 especifica que um mínimo de 127 parâmetros é permitido em uma definição de macro. O compilador da Microsoft segue exatamente a espec (e tem um limite de 127), portanto, você não poderá aumentar **nMacroParameters** além do padrão. Outros compiladores podem permitir que você faça isso (por exemplo, o compilador GNU dá suporte a um limite superior).

Até agora, abordamos praticamente tudo o que você precisa saber sobre como escrever código com a biblioteca do serializador. Antes de concluir, Vamos revisitar alguns tópicos de artigos anteriores sobre os quais você pode estar imaginando.

## <a name="the-lower-level-apis"></a>As APIs de nível inferior
O aplicativo de exemplo no qual este artigo se concentrou é **SimpleSample\_AMQP**. Este exemplo usa as APIs de nível superior (as não**ll**) para enviar eventos e receber mensagens. Se você usar essas APIs, um thread em segundo plano será executado, o que cuida do envio de eventos e de recebimento de mensagens. No entanto, você pode usar as APIs de nível inferior (LL) para eliminar esse thread em segundo plano e assumir o controle explícito ao enviar eventos ou receber mensagens da nuvem.

Conforme descrito em um [artigo anterior](iot-hub-device-sdk-c-iothubclient.md), há um conjunto de funções que consiste nas APIs de nível superior:

* IoTHubClient\_CreateFromConnectionString
* IoTHubClient\_SendEventAsync
* IoTHubClient\_SetMessageCallback
* IoTHubClient\_Destroy

Essas APIs são demonstradas **em\_SimpleSample AMQP**.

Também há um conjunto análogo de APIs de nível inferior.

* IoTHubClient\_LL\_CreateFromConnectionString
* IoTHubClient\_LL\_SendEventAsync
* IoTHubClient\_LL\_SetMessageCallback
* IoTHubClient\_LL\_Destroy

Observe que as APIs de nível inferior funcionam exatamente da mesma maneira descritas nos artigos anteriores. Você pode usar o primeiro conjunto de APIs se desejar que um thread em segundo plano manipule eventos de envio e recebimento de mensagens. Você usará o segundo conjunto de APIs se quiser ter controle explícito ao enviar e receber dados do Hub IoT. Qualquer conjunto de APIs funciona igualmente bem com a biblioteca do serializador.

Para obter um exemplo de como as APIs de nível inferior são usadas com a biblioteca do serializador, consulte o aplicativo **http SimpleSample\_** .

## <a name="additional-topics"></a>Tópicos adicionais
Alguns outros tópicos que vale A pena mencionar novamente são a manipulação de propriedades, o uso de credenciais de dispositivo alternativas e opções de configuração. Esses são todos os tópicos abordados em um [artigo anterior](iot-hub-device-sdk-c-iothubclient.md). O ponto principal é que todos esses recursos funcionam da mesma maneira com a biblioteca do **serializador** como fazem com a biblioteca **IoTHubClient** . Por exemplo, se você quiser anexar Propriedades a um evento de seu modelo, use **as propriedades IoTHubMessage\_** e **mapeie**\_**AddorUpdate**, da mesma maneira descrita anteriormente:

```C
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

Se o evento foi gerado da biblioteca do serializador ou criado manualmente usando a biblioteca **IoTHubClient** não importa.

Para as credenciais de dispositivo alternativas, **usar\_IoTHubClient\_ll Create** funciona tão bem quanto **IoTHubClient\_CreateFromConnectionString** para alocar **um\_clienteIOTHUB\_ IDENTIFICADOR**.

Por fim, se você estiver usando a biblioteca do serializador, poderá definir opções de configuração com **IoTHubClient\_ll\_SetOption** da mesma forma que fez ao usar a biblioteca **IoTHubClient** .

Um recurso que é exclusivo para a biblioteca do serializador são as APIs de inicialização. Antes de começar a trabalhar com a biblioteca, você deve chamar **o\_serializador init**:

```C
serializer_init(NULL);
```

Isso é feito logo antes de chamar **IoTHubClient\_CreateFromConnectionString**.

Da mesma forma, quando você terminar de trabalhar com a biblioteca, a última chamada que você fará **é\_serializar**a desinicialização:

```C
serializer_deinit();
```

Caso contrário, todos os outros recursos listados acima funcionam da mesma forma na biblioteca do serializador, como na biblioteca **IoTHubClient** . Para obter mais informações sobre qualquer um desses tópicos, consulte o [artigo anterior](iot-hub-device-sdk-c-iothubclient.md) nesta série.

## <a name="next-steps"></a>Passos Seguintes

Este artigo descreve detalhadamente os aspectos exclusivos da biblioteca do serializador contidos no **SDK do dispositivo IOT do Azure para C**. Com as informações fornecidas, você deve ter uma boa compreensão de como usar modelos para enviar eventos e receber mensagens do Hub IoT.

Isso também conclui a série de três partes sobre como desenvolver aplicativos com o SDK do **dispositivo IOT do Azure para C**. Essas informações devem ser suficientes para não apenas ajudá-lo a começar, mas lhe dão uma compreensão completa de como as APIs funcionam. Para obter informações adicionais, há alguns exemplos no SDK não abordados aqui. Caso contrário, a [documentação do SDK do Azure IOT](https://github.com/Azure/azure-iot-sdk-c) é um bom recurso para obter informações adicionais.

Para saber mais sobre como desenvolver para o Hub IoT, confira [SDKs do IOT do Azure](iot-hub-devguide-sdks.md).

Para explorar ainda mais os recursos do Hub IoT, consulte Implantando o [ia em dispositivos de borda com Azure IOT Edge](../iot-edge/tutorial-simulate-device-linux.md).