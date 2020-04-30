---
title: Dispositivo Azure IoT SDK para C - IoTHubClient [ Microsoft Docs
description: Como utilizar a biblioteca IoTHubClient no dispositivo Azure IoT SDK para C para criar aplicações de dispositivos que comunicam com um hub IoT.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 08/29/2017
ms.author: robinsh
ms.custom: amqp
ms.openlocfilehash: 91527b5f2159a336e8339c6a128e8d61965292a6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81732609"
---
# <a name="azure-iot-device-sdk-for-c--more-about-iothubclient"></a>Dispositivo Azure IoT SDK para C – mais sobre IoTHubClient

[Dispositivo Azure IoT SDK para C](iot-hub-device-sdk-c-intro.md) é o primeiro artigo desta série que introduz o **dispositivo Azure IoT SDK para C**. Este artigo explicou que há duas camadas arquitetónicas no SDK. Na base encontra-se a biblioteca **IoTHubClient** que gere diretamente a comunicação com o IoT Hub. Há também a biblioteca de **serializadores** que se baseia nisso para fornecer serviços de serialização. Neste artigo, forneceremos detalhes adicionais na biblioteca **IoTHubClient.**

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

O artigo anterior descrevia como usar a biblioteca **IoTHubClient** para enviar eventos para o IoT Hub e receber mensagens. Este artigo estende essa discussão explicando como gerir com mais precisão *quando* envia e recebe dados, introduzindo-o às **APIs de nível inferior**. Também explicaremos como anexar propriedades a eventos (e recuperá-los de mensagens) usando as funcionalidades de manuseamento de propriedades na biblioteca **IoTHubClient.** Finalmente, forneceremos explicações adicionais de diferentes formas de lidar com as mensagens recebidas do IoT Hub.

O artigo conclui cobrindo um par de tópicos diversos, incluindo mais sobre credenciais de dispositivo e como alterar o comportamento do **IoTHubClient** através de opções de configuração.

Usaremos as amostras **Do IoTHubClient** SDK para explicar estes tópicos. Se quiser acompanhar, consulte a **amostra\_\_de\_cliente iothub http** e as aplicações **\_\_amqp do cliente iothub\_** que estão incluídas no dispositivo Azure IoT SDK para C. Tudo descrito nas seguintes secções é demonstrado nestas amostras.

Pode encontrar o [**dispositivo Azure IoT SDK para**](https://github.com/Azure/azure-iot-sdk-c) o repositório C GitHub e visualizar detalhes da API na [referência C API](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/).

## <a name="the-lower-level-apis"></a>As APIs de nível inferior

O artigo anterior descrevia o funcionamento básico do **IotHubClient** no contexto da aplicação amqp da amostra **\_\_do cliente iothub.\_** Por exemplo, explicou como inicializar a biblioteca usando este código.

```C
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Também descreveu como enviar eventos usando esta chamada de função.

```C
IoTHubClient_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message);
```

O artigo também descreveu como receber mensagens registando uma função de callback.

```C
int receiveContext = 0;
IoTHubClient_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext);
```

O artigo também mostrou como libertar recursos usando códigos como o seguinte.

```C
IoTHubClient_Destroy(iotHubClientHandle);
```

Existem funções de acompanhante para cada uma destas APIs:

* IoTHubClient\_LL\_CreateFromConnectionString
* IoTHubClient\_LL\_SendEventAsync
* IoTHubClient\_LL\_SetMessageCallback
* IoTHubClient\_\_LL Destruir

Todas estas funções incluem **LL** no nome API. Outra **parte** LL do nome, os parâmetros de cada uma destas funções são idênticos aos seus homólogos não LL. No entanto, o comportamento destas funções é diferente de uma forma importante.

Quando chama **IoTHubClient\_CreateFromConnectionString,** as bibliotecas subjacentes criam um novo fio que corre em segundo plano. Este fio envia eventos para, e recebe mensagens do IoT Hub. Não é criado tal fio quando se trabalha com as APIs **LL.** A criação do fio de fundo é uma conveniência para o desenvolvedor. Não tens de te preocupar em enviar explicitamente eventos e receber mensagens do IoT Hub. Em contraste, as APIs **LL** dão-lhe controlo explícito sobre a comunicação com o IoT Hub, se precisar.

Para entender melhor este conceito, vamos olhar para um exemplo:

Quando chama **IoTHubClient\_SendEventAsync,** o que está realmente a fazer é colocar o evento num tampão. O fio de fundo criado quando chama **IoTHubClient\_CreateFromConnectionString** monitoriza continuamente este tampão e envia todos os dados que contém para o IoT Hub. Isto acontece em segundo plano ao mesmo tempo que o fio principal está a realizar outros trabalhos.

Da mesma forma, ao registar uma função de backback para mensagens que utilizem **o IoTHubClient\_SetMessageCallback,** está a instruir o SDK a ter o fio de fundo a invocar a função de chamada quando uma mensagem é recebida, independentemente do fio principal.

As **LL** APIs ll não criam um fio de fundo. Em vez disso, uma nova API deve ser chamada para enviar e receber explicitamente dados do IoT Hub. Isto é demonstrado no seguinte exemplo.

A aplicação de http da **\_amostra\_de cliente\_iothub** que está incluída no SDK demonstra as APIs de nível inferior. Nessa amostra, enviamos eventos para o IoT Hub com código sinuoso:

```C
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Message_%d_From_IoTHubClient_LL_Over_HTTP", i);
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));

IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

As três primeiras linhas criam a mensagem, e a última linha envia o evento. No entanto, como mencionado anteriormente, o envio do evento significa que os dados são simplesmente colocados num tampão. Nada é transmitido na rede quando chamamos **IoTHubClient\_LL\_SendEventAsync**. Para realmente inentrar os dados no IoT Hub, deve ligar para **ioTHubClient\_LL\_DoWork,** como neste exemplo:

```C
while (1)
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(100);
}
```

Este código (da aplicação http da **amostra\_\_de cliente\_iothub)** chama repetidamente **IoTHubClient\_LL\_DoWork**. Cada vez que **o\_\_IoTHubClient LL DoWork** é chamado, envia alguns eventos do tampão para o IoT Hub e recupera uma mensagem em fila a ser enviada para o dispositivo. Este último caso significa que se registámos uma função de chamada para mensagens, então o backback é invocado (assumindo que quaisquer mensagens estão na fila). Teríamos registado uma função de chamada com código como o seguinte:

```C
IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext)
```

A razão pela qual **o\_IoTHubClient LL\_DoWork** é frequentemente chamado em loop é que cada vez que é chamado, envia *alguns* eventos tamponados para o IoT Hub e recupera *a próxima* mensagem em fila para o dispositivo. Cada chamada não é garantida para enviar todos os eventos tamponados ou para recuperar todas as mensagens em fila. Se pretender enviar todos os eventos no tampão e, em seguida, continuar com outros processamentos, pode substituir este loop por código como o seguinte:

```C
IOTHUB_CLIENT_STATUS status;

while ((IoTHubClient_LL_GetSendStatus(iotHubClientHandle, &status) == IOTHUB_CLIENT_OK) && (status == IOTHUB_CLIENT_SEND_STATUS_BUSY))
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(100);
}
```

Este código chama **IoTHubClient\_\_LL DoWork** até que todos os eventos no tampão tenham sido enviados para o IoT Hub. Note que isto também não implica que todas as mensagens em fila tenham sido recebidas. Parte da razão para isto é que verificar mensagens "todas" não é uma ação tão determinística. O que acontece se recuperar "todas" das mensagens, mas depois outra é enviada para o dispositivo imediatamente a seguir? Uma melhor forma de lidar com isso é com um intervalo programado. Por exemplo, a função de chamada de mensagem pode repor um temporizador cada vez que é invocado. Pode então escrever lógica para continuar a processar se, por exemplo, não tiverem recebido mensagens nos *últimos* X segundos.

Quando terminar de ingressar eventos e receber mensagens, certifique-se de ligar para a função correspondente para limpar recursos.

```C
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

Basicamente, há apenas um conjunto de APIs para enviar e receber dados com um fio de fundo e outro conjunto de APIs que faz a mesma coisa sem o fio de fundo. Muitos desenvolvedores podem preferir as APIs não LL, mas as APIs de nível inferior são úteis quando o desenvolvedor quer um controlo explícito sobre as transmissões da rede. Por exemplo, alguns dispositivos recolhem dados ao longo do tempo e apenas ingressam eventos em intervalos especificados (por exemplo, uma vez por hora ou uma vez por dia). As APIs de nível inferior dão-lhe a capacidade de controlar explicitamente quando envia e recebe dados do IoT Hub. Outros simplesmente preferem a simplicidade que as APIs de nível inferior proporcionam. Tudo acontece no fio condutor em vez de algum trabalho acontecendo em segundo plano.

Seja qual for o modelo que escolher, certifique-se de ser consistente em que apis usa. Se começar por ligar para **ioTHubClient\_LL\_CreateFromConnectionString,** certifique-se de que utiliza apenas as APIs de nível inferior correspondentes para qualquer trabalho de seguimento:

* IoTHubClient\_LL\_SendEventAsync
* IoTHubClient\_LL\_SetMessageCallback
* IoTHubClient\_\_LL Destruir
* IoTHubClient\_LL\_DoWork

O oposto também é verdade. Se começar com **ioTHubClient\_CreateFromConnectionString,** use as APIs não LL para qualquer processamento adicional.

No dispositivo Azure IoT SDK para C, consulte a aplicação http da **\_amostra\_do cliente\_iothub** para um exemplo completo das APIs de nível inferior. A aplicação **amqp da\_amostra\_\_de cliente iothub** pode ser referenciada para um exemplo completo das APIs não LL.

## <a name="property-handling"></a>Manuseamento de propriedades

Até agora, quando descrevemos o envio de dados, temos nos referido ao corpo da mensagem. Por exemplo, considere este código:

```C
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Hello World");
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));
IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

Este exemplo envia uma mensagem ao IoT Hub com o texto "Hello World". No entanto, o IoT Hub também permite que as propriedades sejam anexadas a cada mensagem. As propriedades são pares de nome/valor que podem ser anexados à mensagem. Por exemplo, podemos modificar o código anterior para anexar uma propriedade à mensagem:

```C
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

Começamos por ligar para **\_ioTHubMessage Properties** e passar o cabo da nossa mensagem. O que recebemos é uma referência **MAP\_HANDLE** que nos permite começar a adicionar propriedades. Este último é realizado chamando **Map\_AddOrUpdate,** que\_leva uma referência a um MAP HANDLE, o nome da propriedade e o valor da propriedade. Com esta API podemos adicionar quantas propriedades quisermos.

Quando o evento for lido a partir de Centros de **Eventos,** o recetor pode enumerar as propriedades e recuperar os seus valores correspondentes. Por exemplo, em .NET isto seria realizado acedendo à [coleção De Propriedades no objeto EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.properties.aspx).

No exemplo anterior, estamos a anexar propriedades a um evento que enviamos para o IoT Hub. As propriedades também podem ser anexadas a mensagens recebidas do IoT Hub. Se quisermos recuperar propriedades de uma mensagem, podemos usar código supor, como o seguinte na nossa função de chamada de mensagem:

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

A chamada para **\_IoTHubMessage Properties** devolve a referência **\_MAP HANDLE.** Em seguida, passamos essa referência ao **Map\_GetInternals** para obter uma referência a uma matriz dos pares de nome/valor (bem como uma contagem das propriedades). Nessa altura é uma simples questão de enumerar as propriedades para chegar aos valores que queremos.

Não precisa usar propriedades na sua aplicação. No entanto, se precisar de os definir em eventos ou recuperá-los de mensagens, a biblioteca **IoTHubClient** facilita.

## <a name="message-handling"></a>Processamento de mensagens

Como referido anteriormente, quando as mensagens chegam do IoT Hub, a biblioteca **IoTHubClient** responde invocando uma função de callback registada. Há um parâmetro de retorno desta função que merece alguma explicação adicional. Aqui está um excerto da função de callback na aplicação de **\_amostra\_de amostra de cliente\_iothub:**

```C
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .
    return IOTHUBMESSAGE_ACCEPTED;
}
```

Note que o tipo de devolução é **Resultado de DISPOSIÇÃO\_\_IOTHUBMESSAGE** e, neste caso em particular, devolvemos **\_iOTHUBMESSAGE ACEITE**. Há outros valores que podemos devolver desta função que alteram a forma como a biblioteca **IoTHubClient** reage ao retorno da mensagem. Aqui estão as opções.

* **IOTHUBMESSAGE\_ACEITO** – A mensagem foi processada com sucesso. A biblioteca **IoTHubClient** não voltará a invocar a função de callback com a mesma mensagem.

* **IOTHUBMESSAGE\_REJEITADO** – A mensagem não foi processada e não há vontade de o fazer no futuro. A biblioteca **IoTHubClient** não deve voltar a invocar a função de callback com a mesma mensagem.

* **IOTHUBMESSAGE\_ABANDONADA** – A mensagem não foi processada com sucesso, mas a biblioteca **IoTHubClient** deve voltar a invocar a função de callback com a mesma mensagem.

Para os dois primeiros códigos de devolução, a biblioteca **IoTHubClient** envia uma mensagem ao IoT Hub indicando que a mensagem deve ser eliminada da fila do dispositivo e não novamente entregue. O efeito líquido é o mesmo (a mensagem é eliminada da fila do dispositivo), mas se a mensagem foi aceite ou rejeitada ainda é gravada.  Registar esta distinção é útil para os remetentes da mensagem que podem ouvir feedback e descobrir se um dispositivo aceitou ou rejeitou uma determinada mensagem.

No último caso, uma mensagem também é enviada para o IoT Hub, mas indica que a mensagem deve ser reentregue. Normalmente, abandonará uma mensagem se encontrar algum erro, mas quer tentar processar a mensagem novamente. Em contrapartida, rejeitar uma mensagem é apropriado quando se encontra um erro irrecuperável (ou se simplesmente decide que não quer processar a mensagem).

Em todo o caso, esteja ciente dos diferentes códigos de devolução para que possa provocar o comportamento que deseja da biblioteca **IoTHubClient.**

## <a name="alternate-device-credentials"></a>Credenciais alternativas de dispositivo

Como explicado anteriormente, a primeira coisa a fazer ao trabalhar com a biblioteca **IoTHubClient** é obter um HANDLE DE **CLIENTE\_\_IOTHUB** com uma chamada como:

```C
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Os argumentos para **IoTHubClient\_CreateFromConnectionString** são a cadeia de ligação do dispositivo e um parâmetro que indica o protocolo que usamos para comunicar com o IoT Hub. A cadeia de ligação do dispositivo tem um formato que aparece da seguinte forma:

```C
HostName=IOTHUBNAME.IOTHUBSUFFIX;DeviceId=DEVICEID;SharedAccessKey=SHAREDACCESSKEY
```

Existem quatro peças de informação nesta cadeia: nome IoT Hub, sufixo IoT Hub, ID do dispositivo e chave de acesso partilhado. Obtém o nome de domínio totalmente qualificado (FQDN) de um hub IoT quando cria a sua instância de hub IoT no portal Azure — isto dá-lhe o nome do hub IoT (a primeira parte do FQDN) e o sufixo do hub IoT (o resto do FQDN). Obtém o ID do dispositivo e a chave de acesso partilhada quando regista o seu dispositivo com o IoT Hub (conforme descrito no [artigo anterior).](iot-hub-device-sdk-c-intro.md)

**IoTHubClient\_CreateFromConnectionString** dá-lhe uma maneira de inicializar a biblioteca. Se preferir, pode criar um novo **\_\_PUNHO CLIENTE IOTHUB** utilizando estes parâmetros individuais em vez da cadeia de ligação do dispositivo. Isto é conseguido com o seguinte código:

```C
IOTHUB_CLIENT_CONFIG iotHubClientConfig;
iotHubClientConfig.iotHubName = "";
iotHubClientConfig.deviceId = "";
iotHubClientConfig.deviceKey = "";
iotHubClientConfig.iotHubSuffix = "";
iotHubClientConfig.protocol = HTTP_Protocol;
IOTHUB_CLIENT_HANDLE iotHubClientHandle = IoTHubClient_LL_Create(&iotHubClientConfig);
```

Isto realiza a mesma coisa que **ioTHubClient\_CreateFromConnectionString**.

Pode parecer óbvio que gostaria de usar **IoTHubClient\_CreateFromConnectionString** em vez deste método mais verboso de inicialização. Tenha em mente, no entanto, que quando regista um dispositivo no IoT Hub o que obtém é uma chave de identificação do dispositivo e dispositivo (não uma cadeia de ligação). A ferramenta SDK do explorador de *dispositivos* introduzida no [artigo anterior](iot-hub-device-sdk-c-intro.md) utiliza bibliotecas no **serviço Azure IoT SDK** para criar a cadeia de ligação do dispositivo a partir do ID do dispositivo, chave do dispositivo e nome de anfitrião Do IoT Hub. Assim, chamar **\_IoTHubClient\_LL Create** pode ser preferível porque poupa o passo de gerar uma cadeia de ligação. Use qualquer método que seja conveniente.

## <a name="configuration-options"></a>Opções de configuração

Até agora, tudo descrito sobre a forma como a biblioteca **IoTHubClient** funciona reflete o seu comportamento padrão. No entanto, existem algumas opções que pode definir para alterar o funcionamento da biblioteca. Isto é conseguido alavancando a **IoTHubClient\_\_LL SetOption** API. Considere este exemplo:

```C
unsigned int timeout = 30000;
IoTHubClient_LL_SetOption(iotHubClientHandle, "timeout", &timeout);
```

Existem algumas opções que são comumente usadas:

* **SetBatching** (bool) – Se **for verdade,** os dados enviados para o IoT Hub são enviados em lotes. Se **for falso,** as mensagens são enviadas individualmente. O padrão é **falso.** O lote sobre AMQP /AMQP-WS, bem como a adição de propriedades do sistema em mensagens D2C, é suportado.

* **Timeout** (int não assinado) – Este valor está representado em milissegundos. Se o envio de um pedido HTTPS ou receber uma resposta demorar mais tempo do que este tempo, então a ligação tem tempo de saída.

A opção de lote é importante. Por padrão, a biblioteca ingressa eventos individualmente (um único evento é o que você passa para **IoTHubClient\_LL\_SendEventAsync).** Se a opção de lote for **verdadeira,** a biblioteca recolhe o máximo de eventos que puder desde o tampão (até ao tamanho máximo da mensagem que o IoT Hub aceitará).  O lote do evento é enviado para ioT Hub numa única chamada HTTPS (os eventos individuais são agregados numa matriz JSON). Permitir o lote normalmente resulta em grandes ganhos de desempenho, uma vez que está a reduzir as viagens de ida e volta da rede. Também reduz significativamente a largura de banda uma vez que está a enviar um conjunto de cabeçalhos HTTPS com um lote de eventoem em vez de um conjunto de cabeçalhos para cada evento individual. A menos que tenha uma razão específica para fazer o contrário, normalmente vai querer ativar o loteamento.

## <a name="next-steps"></a>Passos seguintes

Este artigo descreve detalhadamente o comportamento da biblioteca **IoTHubClient** encontrada no **dispositivo Azure IoT SDK para C**. Com esta informação, deve ter uma boa compreensão das capacidades da biblioteca **IoTHubClient.** O segundo artigo desta série é [o dispositivo Azure IoT SDK para C - Serializer,](iot-hub-device-sdk-c-serializer.md)que fornece detalhes semelhantes na biblioteca **serializer.**

Para saber mais sobre o desenvolvimento para o IoT Hub, consulte os [SDKs Azure IoT](iot-hub-devguide-sdks.md).

Para explorar ainda mais as capacidades do IoT Hub, consulte [a implantação de II para dispositivos de borda com ponta Azure IoT](../iot-edge/tutorial-simulate-device-linux.md)Edge .
