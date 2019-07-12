---
title: O Azure IoT-device SDK para C - o IoTHubClient | Documentos da Microsoft
description: Como utilizar a biblioteca do IoTHubClient no Azure IoT device SDK para C para criar aplicações de dispositivos que comunicam com um hub IoT.
author: yzhong94
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 08/29/2017
ms.author: yizhon
ms.openlocfilehash: ff766375dd9ad7cb3bbdf1ef686abb77d1206099
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797871"
---
# <a name="azure-iot-device-sdk-for-c--more-about-iothubclient"></a>O Azure IoT-device SDK para C – mais informações sobre o IoTHubClient

[O Azure IoT-device SDK para C](iot-hub-device-sdk-c-intro.md) é o primeiro artigo desta série, apresentando o **Azure IoT device SDK para C**. Esse artigo explicou que existem duas camadas de arquiteturais no SDK. Na base é o **o IoTHubClient** biblioteca diretamente gerencia a comunicação com o IoT Hub. Também há a **serializador** biblioteca que baseia-se para fornecer serviços de serialização. Neste artigo, iremos fornecer detalhes adicionais sobre o **o IoTHubClient** biblioteca.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

O artigo anterior descrito como utilizar o **o IoTHubClient** biblioteca para enviar eventos para o IoT Hub e receber mensagens. Este artigo estende essa discussão, que explica como gerenciar mais precisamente *quando* enviar e receber dados, apresentando para o **APIs de nível inferior**. Também vamos explicar como anexar propriedades de eventos (e recuperá-las de mensagens) usando a propriedade de manipulação de recursos da **o IoTHubClient** biblioteca. Finalmente, forneceremos uma explicação adicional de diferentes maneiras de tratar as mensagens recebidas a partir do IoT Hub.

O artigo é concluído com que abrangem alguns dos diversos tópicos, incluindo mais sobre as credenciais do dispositivo e como alterar o comportamento do **o IoTHubClient** por meio de opções de configuração.

Vamos utilizar o **o IoTHubClient** amostras SDK para explicar esses tópicos. Se quiser acompanhar, consulte a **iothub\_cliente\_exemplo\_http** e **iothub\_cliente\_exemplo\_amqp**aplicativos que estão incluídos no Azure IoT device SDK para C. tudo descrito nas seções a seguir é demonstrado nesses exemplos.

Pode encontrar os [ **Azure IoT device SDK para C** ](https://github.com/Azure/azure-iot-sdk-c) GitHub repositório e exibir detalhes da API no [referência da API de C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/).

## <a name="the-lower-level-apis"></a>As APIs de nível inferior

O artigo anterior descrito a operação básica do **o IotHubClient** dentro do contexto da **iothub\_cliente\_exemplo\_amqp** aplicação. Por exemplo, ele explicou como inicializar a biblioteca usando este código.

```C
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Ele também descrita como enviar eventos com esta chamada de função.

```C
IoTHubClient_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message);
```

O artigo também descrita como receber mensagens ao se registrar uma função de retorno de chamada.

```C
int receiveContext = 0;
IoTHubClient_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext);
```

O artigo também mostrou como liberar os recursos usando um código como o seguinte.

```C
IoTHubClient_Destroy(iotHubClientHandle);
```

Existem funções de complementar para cada uma dessas APIs:

* IoTHubClient\_LL\_CreateFromConnectionString
* IoTHubClient\_LL\_SendEventAsync
* IoTHubClient\_LL\_SetMessageCallback
* IoTHubClient\_LL\_Destroy

Estas funções incluem **odas** no nome da API. Outros o **odas** parte do nome, os parâmetros de cada uma dessas funções são idênticos às suas contrapartes não LL. No entanto, o comportamento dessas funções é diferente de uma maneira importante.

Quando chama **o IoTHubClient\_CreateFromConnectionString**, as bibliotecas subjacentes criar um novo thread, que é executado em segundo plano. Esse thread para enviar eventos para e recebe mensagens do IoT Hub. Nenhum thread desse tipo é criado ao trabalhar com o **odas** APIs. A criação do thread em segundo plano é uma conveniência para o desenvolvedor. Não precisa se preocupar sobre explicitamente o envio de eventos e receber mensagens do IoT Hub, isso acontece automaticamente em segundo plano. Por outro lado, o **odas** APIs lhe dar controle explícito sobre comunicação com o IoT Hub, se necessário.

Para compreender melhor esse conceito, vamos examinar um exemplo:

Quando chama **o IoTHubClient\_SendEventAsync**, o que está realmente a fazer é colocar o evento num buffer. O thread de segundo plano criado ao chamar **o IoTHubClient\_CreateFromConnectionString** continuamente monitoriza esse buffer e enviar os dados nele contidos para o IoT Hub. Isso acontece em segundo plano ao mesmo tempo que o thread principal está executando outro trabalho.

Da mesma forma, quando registra uma função de retorno de chamada para mensagens usando **o IoTHubClient\_SetMessageCallback**, está instruindo o SDK para que o thread de segundo plano invocar a função de retorno de chamada quando uma mensagem é recebido, independente do thread principal.

O **odas** APIs não criam um thread em segundo plano. Em vez disso, uma nova API tem de ser chamada para explicitamente enviar e receber dados a partir do IoT Hub. Isso é demonstrado no exemplo a seguir.

O **iothub\_cliente\_exemplo\_http** aplicativo que está incluído no SDK demonstra as APIs de nível inferior. Esse exemplo, podemos enviar eventos para o IoT Hub com um código como o seguinte:

```C
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Message_%d_From_IoTHubClient_LL_Over_HTTP", i);
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));

IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

As primeiras três linhas criam a mensagem e a última linha envia o evento. No entanto, como mencionado anteriormente, enviar que o evento significa que os dados simplesmente são colocados num buffer. Nada é transmitido na rede quando chamamos **o IoTHubClient\_LL\_SendEventAsync**. Na ordem de entrada, na verdade, os dados para o IoT Hub, tem de chamar **o IoTHubClient\_LL\_DoWork**, como neste exemplo:

```C
while (1)
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(100);
}
```

Esse código (da **iothub\_cliente\_exemplo\_http** aplicação) chamasse repetidamente **o IoTHubClient\_LL\_DoWork**. Cada vez **o IoTHubClient\_LL\_DoWork** é chamado, ele envia alguns eventos do buffer para o IoT Hub e obtém uma mensagem em fila a ser enviada para o dispositivo. O último caso, significa que se registada uma função de retorno de chamada para mensagens, em seguida, o retorno de chamada é invocado (supondo que todas as mensagens são colocadas). Seria registramos uma função de retorno de chamada com um código como o seguinte:

```C
IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext)
```

O motivo que **o IoTHubClient\_LL\_DoWork** costuma ser chamada loop é que, sempre que é chamado, ele envia *alguns* colocados em memória intermédia eventos para o IoT Hub e obtém *próximo* mensagem colocada em fila para o dispositivo. Cada chamada não é garantida para enviar eventos tudo em buffer ou recuperar todos colocados em fila de mensagens. Se pretender enviar todos os eventos na memória intermédia e, em seguida, continuar com outro processamento pode substituir esse loop com um código como o seguinte:

```C
IOTHUB_CLIENT_STATUS status;

while ((IoTHubClient_LL_GetSendStatus(iotHubClientHandle, &status) == IOTHUB_CLIENT_OK) && (status == IOTHUB_CLIENT_SEND_STATUS_BUSY))
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(100);
}
```

Esse código chama **o IoTHubClient\_LL\_DoWork** até que todos os eventos na memória intermédia foram enviados para o IoT Hub. Tenha em atenção de que isso não também implica que todas as mensagens em fila foram recebidas. Parte do motivo para isso é que a verificação de mensagens "all" não é determinística como uma ação. O que acontece se recuperar "todas" as mensagens, mas, em seguida, o outro é enviado para o dispositivo imediatamente a seguir? Uma maneira melhor de lidar com isso é com um tempo limite programado. Por exemplo, a função de retorno de chamada de mensagem foi possível repor um temporizador, sempre que é invocado. Em seguida, pode escrever a lógica para continuar o processamento se, por exemplo, nenhuma mensagem recebida nos últimos *X* segundos.

Quando estiver terminado ingressing eventos e receber mensagens, não se esqueça de chamar a função correspondente para limpar os recursos.

```C
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

Basicamente, há apenas um conjunto de APIs para enviar e receber dados com um thread em segundo plano e outro conjunto de APIs que faz a mesma coisa sem o thread de segundo plano. Muitos desenvolvedores podem preferir as APIs não todos, mas as APIs de nível inferior são úteis quando o desenvolvedor deseja ter controle explícito sobre as transmissões de rede. Por exemplo, alguns dispositivos recolhem dados ao longo do tempo e eventos de entrada apenas em intervalos especificados (por exemplo, uma vez, uma hora ou uma vez por dia). As APIs de nível inferior dão-lhe a capacidade para controlar explicitamente ao enviar e receber dados a partir do IoT Hub. Outros simplesmente serão preferem a simplicidade que fornecem as APIs de nível inferior. Tudo o que acontece no thread principal em vez de acontecer algum trabalho em segundo plano.

Qualquer modelo que escolher, certifique-se de que seja consistente nos quais APIs usar. Se começa chamando **o IoTHubClient\_LL\_CreateFromConnectionString**, certifique-se de que utiliza apenas as APIs de nível inferior correspondentes para qualquer trabalho de acompanhamento:

* IoTHubClient\_LL\_SendEventAsync
* IoTHubClient\_LL\_SetMessageCallback
* IoTHubClient\_LL\_Destroy
* IoTHubClient\_LL\_DoWork

O oposto é verdadeiro também. Se começar com **o IoTHubClient\_CreateFromConnectionString**, em seguida, utilizar as APIs não LL para qualquer processamento adicional.

No Azure IoT device SDK para C, consulte a **iothub\_cliente\_exemplo\_http** aplicação para obter um exemplo completo das APIs de nível inferior. O **iothub\_cliente\_exemplo\_amqp** aplicativo pode ser referenciado para obter um exemplo completo de não - LL APIs.

## <a name="property-handling"></a>Manipulação de propriedade

Até agora quando descrevemos o envio de dados, podemos já foi me referindo ao corpo da mensagem. Por exemplo, considere este código:

```C
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Hello World");
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));
IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

Neste exemplo envia uma mensagem para o IoT Hub com o texto "Hello World". No entanto, o IoT Hub permite também as propriedades para ser anexados a cada mensagem. Propriedades são pares nome/valor que podem ser anexados à mensagem. Por exemplo, podemos modificar o código anterior para anexar uma propriedade para a mensagem:

```C
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

Começamos chamando **IoTHubMessage\_propriedades** e passando-o identificador da nossa mensagem. O que obtemos é um **mapa\_lidar com** referência permite-nos começar a adicionar propriedades. A última opção é realizado chamando **mapa\_AddOrUpdate**, que pega uma referência a um mapa\_identificador, o nome da propriedade e o valor da propriedade. Com esta API, pode adicionar tantas propriedades gostamos.

Quando o evento for lido a partir **os Hubs de eventos**, o recetor pode enumerar as propriedades e obter os valores correspondentes. Por exemplo, no .NET seria fazê-lo acedendo a [coleção de propriedades no objeto EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.properties.aspx).

No exemplo anterior, podemos está anexando propriedades para um evento que enviamos para o IoT Hub. Propriedades também podem ser anexadas a mensagens recebidas a partir do IoT Hub. Se quisermos obter as propriedades de uma mensagem, podemos usar código como o seguinte em nossa função de retorno de chamada de mensagem:

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

A chamada para **IoTHubMessage\_propriedades** retorna o **mapa\_PROCESSAR** referência. Em seguida, passamos essa referência para **mapa\_GetInternals** para obter uma referência a uma matriz de pares de nome/valor (bem como uma contagem das propriedades). Nesse momento é uma simples questão de enumerar as propriedades para obter valores que queremos.

Não tem de utilizar as propriedades em seu aplicativo. No entanto, se precisa defini-los em eventos ou recuperá-las de mensagens, o **o IoTHubClient** biblioteca torna mais fácil.

## <a name="message-handling"></a>Processamento de mensagens

Conforme indicado anteriormente, quando mensagens chegam a partir do IoT Hub a **o IoTHubClient** biblioteca responde ao invocar uma função de retorno de chamada registrado. Existe um parâmetro de retorno dessa função que merece uma explicação adicional. Aqui está um trecho da função de retorno de chamada no **iothub\_cliente\_exemplo\_http** aplicação de exemplo:

```C
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .
    return IOTHUBMESSAGE_ACCEPTED;
}
```

Tenha em atenção que é o tipo de retorno **IOTHUBMESSAGE\_disposição\_resultado** e, nesse caso específico, retornamos **IOTHUBMESSAGE\_ACEITES**. Há outros valores poderemos retornar por essa função que alterarem como a **o IoTHubClient** reage de biblioteca para o retorno de chamada de mensagem. Aqui estão as opções.

* **IOTHUBMESSAGE\_aceite** – a mensagem foi processada com êxito. O **o IoTHubClient** biblioteca não invoca a função de retorno de chamada novamente com a mesma mensagem.

* **IOTHUBMESSAGE\_rejeitado** – a mensagem não foi processada, e não existe nenhum desejo de fazê-lo no futuro. O **o IoTHubClient** biblioteca não deve invocar a função de retorno de chamada novamente com a mesma mensagem.

* **IOTHUBMESSAGE\_ABANDONED** – a mensagem não foi processada com êxito, mas o **o IoTHubClient** biblioteca deve invocar a função de retorno de chamada novamente com a mesma mensagem.

Para os dois primeiros retornam códigos, o **o IoTHubClient** biblioteca envia uma mensagem para o IoT Hub com a indicação de que a mensagem deve ser eliminada da fila de dispositivo e não entregar novamente. O efeito líquido é o mesmo (a mensagem é eliminada da fila de dispositivo), mas se a mensagem foi aceites ou rejeitada ainda é registrada.  Gravar essa distinção é útil para remetentes da mensagem que podem escutar os comentários e descobrir se um dispositivo tem aceites ou rejeitados uma mensagem específica.

Nesse último caso é também enviada uma mensagem para o IoT Hub, mas ele indica que a mensagem deve ser reenviada. Normalmente, vai abandonar uma mensagem se encontrar algum erro, mas quer experimentar processar a mensagem novamente. Por outro lado, uma mensagem a rejeitar é apropriada quando encontrar um erro irrecuperável (ou, se simplesmente decidir que não pretende processar a mensagem).

De qualquer forma, tenha em atenção os códigos de retorno diferentes para que pode obter o comportamento que pretender a partir da **o IoTHubClient** biblioteca.

## <a name="alternate-device-credentials"></a>Credenciais do dispositivo alternativo

Como explicado anteriormente, a primeira coisa a fazer ao trabalhar com o **o IoTHubClient** biblioteca é obter um **IOTHUB\_cliente\_PROCESSAR** com uma chamada, como o seguinte:

```C
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Os argumentos **o IoTHubClient\_CreateFromConnectionString** são a cadeia de ligação do dispositivo e um parâmetro que indica o protocolo que usamos para comunicar com IoT Hub. A cadeia de ligação do dispositivo tem um formato que é apresentado da seguinte forma:

```C
HostName=IOTHUBNAME.IOTHUBSUFFIX;DeviceId=DEVICEID;SharedAccessKey=SHAREDACCESSKEY
```

Existem quatro partes de informações nessa cadeia: Nome do IoT Hub, sufixo do IoT Hub, ID de dispositivo e a chave de acesso partilhado. Obter o nome de domínio completamente qualificado (FQDN) de um hub IoT ao criar a sua instância do hub IoT no portal do Azure — isso lhe dá o nome do hub IoT (a primeira parte do FQDN) e o sufixo do hub IoT (o resto do FQDN). Obter o ID de dispositivo e a chave de acesso partilhado quando registar o seu dispositivo com o IoT Hub (conforme descrito no [artigo anterior](iot-hub-device-sdk-c-intro.md)).

**O IoTHubClient\_CreateFromConnectionString** dá-lhe uma forma de inicializar a biblioteca. Se preferir, pode criar uma nova **IOTHUB\_cliente\_PROCESSAR** utilizando estes parâmetros individuais em vez da cadeia de ligação do dispositivo. Isto é conseguido com o código a seguir:

```C
IOTHUB_CLIENT_CONFIG iotHubClientConfig;
iotHubClientConfig.iotHubName = "";
iotHubClientConfig.deviceId = "";
iotHubClientConfig.deviceKey = "";
iotHubClientConfig.iotHubSuffix = "";
iotHubClientConfig.protocol = HTTP_Protocol;
IOTHUB_CLIENT_HANDLE iotHubClientHandle = IoTHubClient_LL_Create(&iotHubClientConfig);
```

A mesma coisa que chegará **o IoTHubClient\_CreateFromConnectionString**.

Pode parecer óbvio que iria querer usar **o IoTHubClient\_CreateFromConnectionString** em vez deste método mais detalhado de inicialização. Tenha em mente, no entanto, que ao registar um dispositivo no IoT Hub, o que obtém é um ID de dispositivo e a chave do dispositivo (não uma cadeia de ligação). O *Explorador de dispositivos* ferramenta SDK introduzida no [artigo anterior](iot-hub-device-sdk-c-intro.md) utiliza bibliotecas no **SDK do serviço do Azure IoT** para criar a cadeia de ligação do dispositivo da ID do dispositivo , chave do dispositivo e o nome de anfitrião do IoT Hub. Então, chamar **o IoTHubClient\_LL\_criar** pode ser preferível porque poupa a etapa de gerar uma cadeia de ligação. Utilize independentemente do método que é conveniente.

## <a name="configuration-options"></a>Opções de configuração

Até aqui tudo descrito sobre a forma como o **o IoTHubClient** biblioteca funciona reflete o seu comportamento padrão. No entanto, existem algumas opções que pode definir para alterar a como funciona a biblioteca. Isto é conseguido ao tirar partido da **o IoTHubClient\_LL\_SetOption** API. Considere este exemplo:

```C
unsigned int timeout = 30000;
IoTHubClient_LL_SetOption(iotHubClientHandle, "timeout", &timeout);
```

Existem algumas opções que são frequentemente utilizadas:

* **SetBatching** (bool) – se **true**, dados enviados para o IoT Hub é enviado em lotes. Se **false**, em seguida, as mensagens são enviadas individualmente. A predefinição é **false**. Processamento em lote através de AMQP / AMQP WS, bem como a adição de propriedades do sistema nas mensagens D2C, é suportado.

* **Tempo limite** (unsigned int) – este valor é representado em milissegundos. Se enviar um pedido HTTPS ou de receber uma resposta demora mais tempo do que este tempo, em seguida, a ligação exceder o tempo limite.

A opção de criação de batches é importante. Por predefinição, os eventos de ingresses biblioteca individualmente (um único evento, é tudo o que passar para **o IoTHubClient\_LL\_SendEventAsync**). Se a opção de criação de batches estiver **true**, a biblioteca recolhe os eventos de tantos possível do buffer (até o tamanho máximo de mensagens que aceitará do IoT Hub).  O lote de eventos é enviado para o IoT Hub numa única chamada HTTPS (os eventos individuais são agrupados numa matriz JSON). Ativar a criação de batches normalmente resulta em ganhos de desempenho grande, uma vez que estiver reduzindo ida e volta de rede. Ela reduz consideravelmente também os largura de banda, uma vez que estão a enviar um conjunto de cabeçalhos HTTPS com um lote de evento, em vez de um conjunto de cabeçalhos para cada evento individual. A menos que tenha um motivo específico para fazer de outra forma, normalmente, desejará permitir a criação de batches.

## <a name="next-steps"></a>Passos seguintes

Este artigo descreve detalhadamente o comportamento do **o IoTHubClient** biblioteca encontrada na **Azure IoT device SDK para C**. Com essas informações, deve ter uma boa compreensão dos recursos da **o IoTHubClient** biblioteca. É o segundo artigo dessa série [Azure IoT device SDK para C - serializador](iot-hub-device-sdk-c-serializer.md), que fornece detalhes semelhantes sobre o **serializador** biblioteca.

Para saber mais sobre o desenvolvimento para o IoT Hub, veja a [do Azure IoT SDKs](iot-hub-devguide-sdks.md).

Para explorar ainda mais os recursos do IoT Hub, veja [implementação de ia para dispositivos de ponta com o Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md).
