---
title: Dispositivo Azure IoT SDK para C - IoTHubClient | Microsoft Docs
description: Como utilizar a biblioteca IoTHubClient no dispositivo Azure IoT SDK para C para criar aplicações de dispositivos que comunicam com um hub IoT.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 08/29/2017
ms.author: robinsh
ms.custom: amqp
ms.openlocfilehash: f33521dd9110d7ba6ee84650345b38c8c6a4950b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92149134"
---
# <a name="azure-iot-device-sdk-for-c--more-about-iothubclient"></a>Dispositivo Azure IoT SDK para C – mais sobre ioTHubClient

[Azure IoT device SDK for C](iot-hub-device-sdk-c-intro.md) é o primeiro artigo desta série introduzindo o **dispositivo Azure IoT SDK para C**. O artigo explicou que há duas camadas arquitetónicas em SDK. Na base encontra-se a biblioteca **IoTHubClient** que gere diretamente a comunicação com o IoT Hub. Há também a biblioteca **serializadora** que se baseia em tudo para fornecer serviços de serialização. Neste artigo, forneceremos detalhes adicionais sobre a biblioteca **IoTHubClient.**

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

O artigo anterior descrevia como usar a biblioteca **IoTHubClient** para enviar eventos para o IoT Hub e receber mensagens. Este artigo alarga essa discussão explicando como gerir com mais precisão *quando* envia e recebe dados, introduzindo-o nas **APIs de nível inferior.** Também explicaremos como anexar propriedades a eventos (e recuperá-los de mensagens) usando as funcionalidades de manuseamento de propriedades na biblioteca **IoTHubClient.** Finalmente, forneceremos uma explicação adicional de diferentes formas de lidar com mensagens recebidas do IoT Hub.

O artigo conclui cobrindo um par de tópicos diversos, incluindo mais sobre credenciais de dispositivo e como mudar o comportamento do **IoTHubClient** através de opções de configuração.

Usaremos as amostras **de SDK IoTHubClient** para explicar estes tópicos. Se quiser acompanhar, consulte a amostra do **cliente iothub \_ \_ \_ http** e aplicações **\_ \_ \_ amqp de amostra de cliente iothub** incluídas no dispositivo Azure IoT SDK para C. Tudo descrito nas seguintes secções é demonstrado nestas amostras.

Pode encontrar o [**dispositivo Azure IoT SDK para**](https://github.com/Azure/azure-iot-sdk-c) o repositório C GitHub e ver detalhes da API na [referência C API](/azure/iot-hub/iot-c-sdk-ref/).

## <a name="the-lower-level-apis"></a>As APIs de nível inferior

O artigo anterior descrevia o funcionamento básico do **IotHubClient** no contexto da aplicação **\_ \_ \_ amqp** da amostra de cliente iothub. Por exemplo, explicou como inicializar a biblioteca usando este código.

```C
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Também descreveu como enviar eventos usando esta chamada de função.

```C
IoTHubClient_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message);
```

O artigo também descreveu como receber mensagens registando uma função de retorno.

```C
int receiveContext = 0;
IoTHubClient_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext);
```

O artigo também mostrou como libertar recursos usando código, como o seguinte.

```C
IoTHubClient_Destroy(iotHubClientHandle);
```

Existem funções de acompanhante para cada uma destas APIs:

* IoTHubClient \_ LL \_ CreateFromConnectionString
* IoTHubClient \_ LL \_ SendEventAsync
* IoTHubClient \_ LL \_ SetMessageCallback
* IoTHubClient \_ LL \_ Destroy

Todas estas funções incluem **LL** no nome API. Outra parte do **LL** do nome, os parâmetros de cada uma destas funções são idênticos aos seus homólogos não-LL. No entanto, o comportamento destas funções é diferente de uma forma importante.

Quando chama **ioTHubClient \_ CreateFromConnectionString,** as bibliotecas subjacentes criam um novo fio que funciona em segundo plano. Este fio envia eventos para, e recebe mensagens de IoT Hub. Não se cria tal fio quando se trabalha com as APIs **da LL.** A criação do fio de fundo é uma conveniência para o desenvolvedor. Não tens de te preocupar em enviar eventos explícitos e receber mensagens do IoT Hub. Em contraste, as APIs **d.LL** dão-lhe um controlo explícito sobre a comunicação com o IoT Hub, se precisar.

Para compreender melhor este conceito, vejamos um exemplo:

Quando ligas para **o IoTHubClient \_ SendEventAsync,** o que estás realmente a fazer é colocar o evento num amortecedor. O fio de fundo criado quando chama **IoTHubClient \_ CreateFromConnectionString** monitoriza continuamente este tampão e envia todos os dados que contém para o IoT Hub. Isto acontece em segundo plano ao mesmo tempo que o fio principal está a realizar outros trabalhos.

Da mesma forma, quando regista uma função de retorno para mensagens utilizando **o IoTHubClient \_ SetMessageCallback,** está a instruir o SDK para que o fio de fundo invoque a função de retorno quando uma mensagem é recebida, independentemente do fio principal.

As **APIs ll** não criam um fio de fundo. Em vez disso, uma nova API deve ser chamada para enviar e receber explicitamente dados do IoT Hub. Isto é demonstrado no exemplo seguinte.

A **\_ \_ aplicação \_ de amostra de cliente iothub** que está incluída no SDK demonstra as APIs de nível inferior. Nessa amostra, enviamos eventos para o IoT Hub com código como:

```C
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Message_%d_From_IoTHubClient_LL_Over_HTTP", i);
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));

IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

As três primeiras linhas criam a mensagem, e a última linha envia o evento. No entanto, como mencionado anteriormente, o envio do evento significa que os dados são simplesmente colocados num tampão. Nada é transmitido na rede quando chamamos **IoTHubClient \_ LL \_ SendEventAsync**. Para realmente entrar nos dados para o IoT Hub, deve ligar para **ioTHubClient \_ LL \_ DoWork,** como neste exemplo:

```C
while (1)
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(100);
}
```

Este código (a partir da **\_ \_ aplicação \_ http amostra de cliente iothub)** chama repetidamente **IoTHubClient \_ LL \_ DoWork**. Cada vez **que o IoTHubClient \_ LL \_ DoWork** é chamado, envia alguns eventos do tampão para o IoT Hub e recupera uma mensagem em fila sendo enviada para o dispositivo. Este último caso significa que se registarmos uma função de retorno para mensagens, então a chamada é invocada (assumindo que quaisquer mensagens são em fila). Teríamos registado tal função de retorno com código como:

```C
IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext)
```

A razão pela qual **o IoTHubClient \_ LL \_ DoWork** é muitas vezes chamado em loop é que cada vez que é chamado, envia *alguns* eventos tampão para o IoT Hub e recupera *a próxima* mensagem em fila para o dispositivo. Cada chamada não é garantida para enviar todos os eventos tamponados ou para recuperar todas as mensagens em fila. Se quiser enviar todos os eventos no tampão e continuar com outros tratamentos, pode substituir este ciclo por códigos como:

```C
IOTHUB_CLIENT_STATUS status;

while ((IoTHubClient_LL_GetSendStatus(iotHubClientHandle, &status) == IOTHUB_CLIENT_OK) && (status == IOTHUB_CLIENT_SEND_STATUS_BUSY))
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(100);
}
```

Este código chama **IoTHubClient \_ LL \_ DoWork** até que todos os eventos no tampão tenham sido enviados para o IoT Hub. Note que isto também não implica que todas as mensagens em fila tenham sido recebidas. Parte da razão para isso é que verificar mensagens de "todos" não é uma ação tão determinística. O que acontece se recuperar "todas" as mensagens, mas depois outra é enviada para o dispositivo imediatamente a seguir? Uma forma melhor de lidar com isso é com um tempo limite programado. Por exemplo, a função de retorno de mensagens pode reiniciar um temporizador sempre que é invocada. Em seguida, pode escrever lógica para continuar a processar se, por exemplo, não tiverem sido recebidas mensagens nos *últimos* X segundos.

Quando terminar de entrar em eventos e receber mensagens, não se esqueça de ligar para a função correspondente para limpar recursos.

```C
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

Basicamente, há apenas um conjunto de APIs para enviar e receber dados com um fio de fundo e outro conjunto de APIs que faz a mesma coisa sem o fio de fundo. Muitos desenvolvedores podem preferir as APIs não-LL, mas as APIs de nível inferior são úteis quando o desenvolvedor quer controlo explícito sobre as transmissões de rede. Por exemplo, alguns dispositivos recolhem dados ao longo do tempo e apenas ingressam eventos em intervalos especificados (por exemplo, uma hora ou uma vez por dia). As APIs de nível inferior dão-lhe a capacidade de controlar explicitamente quando envia e recebe dados do IoT Hub. Outros simplesmente preferirão a simplicidade que as APIs de nível inferior proporcionam. Tudo acontece no fio principal em vez de algum trabalho acontecendo em segundo plano.

Qualquer que seja o modelo que escolher, certifique-se de ser consistente em que APIs você usa. Se começar por ligar para **ioTHubClient \_ LL \_ CreateFromConnectionString,** certifique-se de que utiliza apenas as APIs de nível inferior correspondentes para qualquer trabalho de seguimento:

* IoTHubClient \_ LL \_ SendEventAsync
* IoTHubClient \_ LL \_ SetMessageCallback
* IoTHubClient \_ LL \_ Destroy
* IoTHubClient \_ LL \_ DoWork

O oposto também é verdade. Se começar com **ioTHubClient \_ CreateFromConnectionString**, use as APIs não-LL para qualquer processamento adicional.

No dispositivo Azure IoT SDK para C, consulte a **\_ \_ aplicação \_ http** da amostra do cliente iothub para um exemplo completo das APIs de nível inferior. A **\_ \_ aplicação \_ amqp de amostra de cliente iothub** pode ser referenciada para um exemplo completo das APIs não-LL.

## <a name="property-handling"></a>Movimentação de imóveis

Até agora, quando descrevemos o envio de dados, temos-nos vindo a referir ao corpo da mensagem. Por exemplo, considere este código:

```C
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Hello World");
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));
IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

Este exemplo envia uma mensagem para o IoT Hub com o texto "Hello World". No entanto, o IoT Hub também permite que as propriedades sejam anexadas a cada mensagem. As propriedades são pares de nome/valor que podem ser anexados à mensagem. Por exemplo, podemos modificar o código anterior para anexar uma propriedade à mensagem:

```C
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

Começamos por ligar para **a IoTHubMessage \_ Properties** e passar-lhe o cabo da nossa mensagem. O que recebemos de volta é uma referência **MAP \_ HANDLE** que nos permite começar a adicionar propriedades. Este último é realizado chamando **Map \_ AddOrUpdate,** que leva uma referência a um \_ MANÍSTA MAP, o nome da propriedade e o valor da propriedade. Com esta API podemos adicionar quantas propriedades quisermos.

Quando o evento é lido a partir de **Event Hubs,** o recetor pode enumerar as propriedades e recuperar os valores correspondentes. Por exemplo, em .NET isso seria conseguido acedendo à [coleção Propriedades no objeto EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata).

No exemplo anterior, estamos anexando propriedades a um evento que enviamos para ioT Hub. As propriedades também podem ser anexadas a mensagens recebidas do IoT Hub. Se quisermos recuperar propriedades a partir de uma mensagem, podemos usar código como o seguinte na nossa função de chamada de mensagem:

```C
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .

    // Retrieve properties from the message
    MAP_HANDLE mapProperties = IoTHubMessage_Properties(message);
    if (mapProperties != NULL)
    {
        const char*const* keys;
        const char*const* values;
        size_t propertyCount = 0;
        if (Map_GetInternals(mapProperties, &keys, &values, &propertyCount) == MAP_OK)
        {
            if (propertyCount > 0)
            {
                printf("Message Properties:\r\n");
                for (size_t index = 0; index < propertyCount; index++)
                {
                    printf("\tKey: %s Value: %s\r\n", keys[index], values[index]);
                }
                printf("\r\n");
            }
        }
    }

    . . .
}
```

A chamada para **IoTHubMessage \_ Properties** devolve a referência **MAP \_ HANDLE.** Em seguida, passamos essa referência a **Map \_ GetInternals** para obter uma referência a uma matriz dos pares de nome/valor (bem como uma contagem das propriedades). Nessa altura, é uma simples questão de enumerar as propriedades para chegar aos valores que queremos.

Não tem de usar propriedades na sua aplicação. No entanto, se precisar de os definir em eventos ou recuperá-los de mensagens, a biblioteca **IoTHubClient** facilita.

## <a name="message-handling"></a>Processamento de mensagens

Como indicado anteriormente, quando as mensagens chegam do IoT Hub, a biblioteca **IoTHubClient** responde invocando uma função de retorno registado. Há um parâmetro de retorno desta função que merece alguma explicação adicional. Aqui está um excerto da função de retorno na amostra de **cliente do iothub \_ \_ \_ http** pedido de amostra:

```C
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .
    return IOTHUBMESSAGE_ACCEPTED;
}
```

Note que o tipo de devolução é **IOTHUBMESSAGE \_ \_ DISPOSITION RESULT** e neste caso particular devolvemos **IOTHUBMESSAGE \_ ACCEPTED**. Existem outros valores que podemos voltar desta função que alteram a forma como a biblioteca **IoTHubClient** reage à chamada de mensagem. Aqui estão as opções.

* **IOTHUBMESSAGE \_ ACEITA –** A mensagem foi processada com sucesso. A biblioteca **IoTHubClient** não voltará a invocar a função de retorno com a mesma mensagem.

* **IOTHUBMESSAGE \_ REJEITADO** – A mensagem não foi processada e não há vontade de o fazer no futuro. A biblioteca **IoTHubClient** não deve invocar novamente a função de retorno com a mesma mensagem.

* **IOTHUBMESSAGE \_ ABANDONADA** – A mensagem não foi processada com sucesso, mas a biblioteca **IoTHubClient** deve invocar novamente a função de retorno com a mesma mensagem.

Para os dois primeiros códigos de devolução, a biblioteca **IoTHubClient** envia uma mensagem para o IoT Hub indicando que a mensagem deve ser eliminada da fila do dispositivo e não entregue novamente. O efeito net é o mesmo (a mensagem é eliminada da fila do dispositivo), mas se a mensagem foi aceite ou rejeitada ainda é gravada.  Registar esta distinção é útil para os remetentes da mensagem que podem ouvir feedback e descobrir se um dispositivo aceitou ou rejeitou uma mensagem em particular.

No último caso, uma mensagem também é enviada para o IoT Hub, mas indica que a mensagem deve ser reentrequiva. Normalmente, abandonará uma mensagem se encontrar algum erro, mas quer tentar processar a mensagem novamente. Em contrapartida, rejeitar uma mensagem é apropriado quando se encontra um erro irrecuperável (ou se simplesmente decide que não quer processar a mensagem).

Em todo o caso, esteja atento aos diferentes códigos de devolução para que possa provocar o comportamento que pretende da biblioteca **IoTHubClient.**

## <a name="alternate-device-credentials"></a>Credenciais alternativas do dispositivo

Como explicado anteriormente, a primeira coisa a fazer quando se trabalha com a biblioteca **IoTHubClient** é obter um **\_ \_ IOTHUB CLIENT HANDLE** com uma chamada como:

```C
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Os argumentos para **ioTHubClient \_ CreateFromConnectionString** são a cadeia de ligação do dispositivo e um parâmetro que indica o protocolo que usamos para comunicar com o IoT Hub. A cadeia de ligação do dispositivo tem um formato que aparece da seguinte forma:

```C
HostName=IOTHUBNAME.IOTHUBSUFFIX;DeviceId=DEVICEID;SharedAccessKey=SHAREDACCESSKEY
```

Existem quatro peças de informação nesta cadeia: nome IoT Hub, sufixo IoT Hub, ID do dispositivo e chave de acesso partilhado. Obtém o nome de domínio totalmente qualificado (FQDN) de um hub IoT quando cria a sua instância de hub IoT no portal Azure — isto dá-lhe o nome do hub IoT (a primeira parte do FQDN) e o sufixo do hub IoT (o resto do FQDN). Obtém o ID do dispositivo e a chave de acesso partilhada quando regista o seu dispositivo com o IoT Hub (conforme descrito no [artigo anterior).](iot-hub-device-sdk-c-intro.md)

**IoTHubClient \_ CreateFromConnectionString** dá-lhe uma maneira de inicializar a biblioteca. Se preferir, pode criar um novo **\_ \_ MANÍpulo cliente IOTHUB** utilizando estes parâmetros individuais em vez da cadeia de ligação do dispositivo. Isto é conseguido com o seguinte código:

```C
IOTHUB_CLIENT_CONFIG iotHubClientConfig;
iotHubClientConfig.iotHubName = "";
iotHubClientConfig.deviceId = "";
iotHubClientConfig.deviceKey = "";
iotHubClientConfig.iotHubSuffix = "";
iotHubClientConfig.protocol = HTTP_Protocol;
IOTHUB_CLIENT_HANDLE iotHubClientHandle = IoTHubClient_LL_Create(&iotHubClientConfig);
```

Isto realiza a mesma coisa que **ioTHubClient \_ CreateFromConnectionString**.

Pode parecer óbvio que gostaria de usar **ioTHubClient \_ CreateFromConnectionString** em vez deste método mais verboso de inicialização. Tenha em mente, no entanto, que quando regista um dispositivo no IoT Hub o que obtém é uma chave de ID e dispositivo (não uma cadeia de ligação). A ferramenta SDK *do explorador do dispositivo* introduzida no artigo [anterior](iot-hub-device-sdk-c-intro.md) utiliza bibliotecas no **serviço Azure IoT SDK** para criar a cadeia de ligação do dispositivo a partir do ID do dispositivo, chave do dispositivo e nome de anfitrião IoT Hub. Assim, chamar **ioTHubClient \_ LL \_ Create** pode ser preferível porque poupa-lhe o passo de gerar uma cadeia de ligação. Use qualquer método conveniente.

## <a name="configuration-options"></a>Opções de configuração

Até agora tudo descrito sobre a forma como a biblioteca **IoTHubClient** funciona reflete o seu comportamento padrão. No entanto, existem algumas opções que pode definir para alterar o funcionamento da biblioteca. Isto é conseguido aproveitando a **IoTHubClient \_ LL \_ SetOption** API. Considere este exemplo:

```C
unsigned int timeout = 30000;
IoTHubClient_LL_SetOption(iotHubClientHandle, "timeout", &timeout);
```

Existem algumas opções que são comumente usadas:

* **SetBatching** (bool) – Se **for verdade,** os dados enviados para o IoT Hub são enviados em lotes. Se **for falso,** as mensagens são enviadas individualmente. O padrão é **falso.** O loteamento sobre AMQP / AMQP-WS, bem como a adição de propriedades do sistema em mensagens D2C, é suportado.

* **Timeout** (int não assinado) – Este valor está representado em milissegundos. Se enviar um pedido HTTPS ou receber uma resposta demorar mais do que este tempo, então a ligação termina.

A opção de loteamento é importante. Por predefinição, a biblioteca ingressa eventos individualmente (um único evento é o que você passa para **IoTHubClient \_ LL \_ SendEventAsync).** Se a opção de lotear for **verdadeira,** a biblioteca recolhe o máximo de eventos que puder a partir do tampão (até ao tamanho máximo de mensagem que o IoT Hub aceitará).  O lote do evento é enviado para o IoT Hub numa única chamada HTTPS (os eventos individuais são agregados numa matriz JSON). Ativar o lote resulta tipicamente em grandes ganhos de desempenho, uma vez que está a reduzir as viagens de ida e volta da rede. Também reduz significativamente a largura de banda uma vez que está a enviar um conjunto de cabeçalhos HTTPS com um lote de eventos em vez de um conjunto de cabeçalhos para cada evento. A menos que tenha uma razão específica para fazer o contrário, normalmente vai querer ativar o lote.

## <a name="next-steps"></a>Passos seguintes

Este artigo descreve em detalhe o comportamento da biblioteca **IoTHubClient** encontrada no **dispositivo Azure IoT SDK para C**. Com esta informação, deverá ter uma boa compreensão das capacidades da biblioteca **IoTHubClient.** O segundo artigo desta série é [O dispositivo Azure IoT SDK para C - Serializer,](iot-hub-device-sdk-c-serializer.md)que fornece detalhes semelhantes na biblioteca **de serializer.**

Para saber mais sobre o desenvolvimento para o IoT Hub, consulte os [Azure IoT SDKs.](iot-hub-devguide-sdks.md)

Para explorar ainda mais as capacidades do IoT Hub, consulte [implementar ia para dispositivos de borda com Azure IoT Edge](../iot-edge/quickstart-linux.md).