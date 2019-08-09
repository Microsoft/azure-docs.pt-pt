---
title: SDK do dispositivo IoT do Azure para C-IoTHubClient | Microsoft Docs
description: Como usar a biblioteca IoTHubClient no SDK do dispositivo IoT do Azure para C para criar aplicativos de dispositivo que se comunicam com um hub IoT.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 08/29/2017
ms.author: robinsh
ms.openlocfilehash: fd3e02101f206ebdb183da87089eadcbc9619b33
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883182"
---
# <a name="azure-iot-device-sdk-for-c--more-about-iothubclient"></a>SDK do dispositivo IoT do Azure para C – mais sobre IoTHubClient

O [SDK do dispositivo IOT do Azure para c](iot-hub-device-sdk-c-intro.md) é o primeiro artigo desta série que apresenta o **SDK do dispositivo IOT do Azure para c**. Esse artigo explicou que há duas camadas arquitetônicas no SDK. Na base está a biblioteca **IoTHubClient** que gerencia diretamente a comunicação com o Hub IOT. Também há a biblioteca do serializador que se baseia para fornecer serviços de serialização. Neste artigo, forneceremos detalhes adicionais sobre a biblioteca **IoTHubClient** .

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

O artigo anterior descreveu como usar a biblioteca **IoTHubClient** para enviar eventos ao Hub IOT e receber mensagens. Este artigo estende essa discussão explicando como gerenciar mais precisamente *quando* você envia e recebe dados, apresentando as **APIs de nível inferior**. Também explicaremos como anexar Propriedades a eventos (e recuperá-los de mensagens) usando os recursos de manipulação de propriedades na biblioteca **IoTHubClient** . Por fim, forneceremos uma explicação adicional de diferentes maneiras de lidar com as mensagens recebidas do Hub IoT.

O artigo conclui abordando alguns tópicos diversos, incluindo mais sobre as credenciais do dispositivo e como alterar o comportamento do **IoTHubClient** por meio de opções de configuração.

Usaremos os exemplos do SDK do **IoTHubClient** para explicar esses tópicos. Se você quiser acompanhar, consulte os aplicativos de exemplo de cliente **\_iothub\_\_http** e **\_iothub\_cliente\_de exemplo AMQP** que estão incluídos no Azure IOT SDK do dispositivo para C. tudo o que é descrito nas seções a seguir é demonstrado nesses exemplos.

Você pode encontrar o [**SDK do dispositivo IOT do Azure para**](https://github.com/Azure/azure-iot-sdk-c) o repositório do GitHub c e exibir detalhes da API na [referência da API C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/).

## <a name="the-lower-level-apis"></a>As APIs de nível inferior

O artigo anterior descreveu a operação básica do **IotHubClient** dentro do contexto do aplicativo **iothub\_Client\_Sample\_AMQP** . Por exemplo, ele explicou como inicializar a biblioteca usando esse código.

```C
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Ele também descreveu como enviar eventos usando essa chamada de função.

```C
IoTHubClient_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message);
```

O artigo também descreveu como receber mensagens registrando uma função de retorno de chamada.

```C
int receiveContext = 0;
IoTHubClient_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext);
```

O artigo também mostrou como liberar recursos usando um código como o seguinte.

```C
IoTHubClient_Destroy(iotHubClientHandle);
```

Há funções complementares para cada uma dessas APIs:

* IoTHubClient\_LL\_CreateFromConnectionString
* IoTHubClient\_LL\_SendEventAsync
* IoTHubClient\_LL\_SetMessageCallback
* IoTHubClient\_LL\_Destroy

Todas essas funções incluem **ll** no nome da API. Outra parte do nome, os parâmetros de cada uma dessas funções são idênticos às suas contrapartes não ll. No entanto, o comportamento dessas funções é diferente de uma maneira importante.

Quando você chama **IoTHubClient\_CreateFromConnectionString**, as bibliotecas subjacentes criam um novo thread que é executado em segundo plano. Esse thread envia eventos para e recebe mensagens do Hub IoT. Esse thread não é criado ao trabalhar com as APIs **ll** . A criação do thread em segundo plano é uma conveniência para o desenvolvedor. Você não precisa se preocupar em enviar explicitamente eventos e receber mensagens do Hub IoT – ele ocorre automaticamente em segundo plano. Por outro lado, as APIs **ll** oferecem controle explícito sobre a comunicação com o Hub IOT, se necessário.

Para entender esse conceito melhor, vejamos um exemplo:

Quando você chama **IoTHubClient\_SendEventAsync**, o que realmente está fazendo é colocar o evento em um buffer. O thread em segundo plano criado quando você chama **IoTHubClient\_CreateFromConnectionString** monitora continuamente esse buffer e envia todos os dados que ele contém ao Hub IOT. Isso acontece em segundo plano ao mesmo tempo em que o thread principal está executando outro trabalho.

Da mesma forma, ao registrar uma função de retorno de chamada para mensagens usando **IoTHubClient\_SetMessageCallback**, você está instruindo o SDK para que o thread em segundo plano invoque a função de retorno de chamada quando uma mensagem é recebida, independentemente do thread principal.

As APIs **ll** não criam um thread em segundo plano. Em vez disso, uma nova API deve ser chamada para enviar e receber dados explicitamente do Hub IoT. Isso é demonstrado no exemplo a seguir.

O **aplicativo\_http\_deexemplo\_de cliente iothub** que está incluído no SDK demonstra as APIs de nível inferior. Nesse exemplo, enviamos eventos ao Hub IoT com um código como o seguinte:

```C
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Message_%d_From_IoTHubClient_LL_Over_HTTP", i);
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));

IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

As três primeiras linhas criam a mensagem e a última linha envia o evento. No entanto, como mencionado anteriormente, o envio do evento significa que os dados são simplesmente colocados em um buffer. Nada é transmitido na rede quando chamamos **\_IoTHubClient ll\_SendEventAsync**. Para realmente inserir os dados no Hub IOT, você deve chamar **\_IoTHubClient ll\_DoWork**, como neste exemplo:

```C
while (1)
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(100);
}
```

Esse código (do aplicativo **de\_exemplo\_\_de cliente iothub de http** ) chama repetidamente **\_IoTHubClient ll\_DoWork**. Cada vez **que\_IoTHubClient\_ll DoWork** é chamado, ele envia alguns eventos do buffer para o Hub IOT e recupera uma mensagem enfileirada que está sendo enviada para o dispositivo. O último caso significa que, se registramos uma função de retorno de chamada para mensagens, o retorno de chamada é invocado (supondo que todas as mensagens sejam enfileiradas). Teríamos registrado uma função de retorno de chamada com um código como o seguinte:

```C
IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext)
```

O motivo pelo **qual\_IoTHubClient\_ll DoWork** geralmente é chamado em um loop é que cada vez que ele é chamado, ele envia *alguns* eventos em buffer ao Hub IOT e recupera *a próxima* mensagem enfileirada para o dispositivo. Cada chamada não é garantida para enviar todos os eventos em buffer ou para recuperar todas as mensagens em fila. Se você quiser enviar todos os eventos no buffer e continuar com outro processamento, poderá substituir esse loop por um código como o seguinte:

```C
IOTHUB_CLIENT_STATUS status;

while ((IoTHubClient_LL_GetSendStatus(iotHubClientHandle, &status) == IOTHUB_CLIENT_OK) && (status == IOTHUB_CLIENT_SEND_STATUS_BUSY))
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(100);
}
```

Esse código chama **IoTHubClient\_ll\_DoWork** até que todos os eventos no buffer tenham sido enviados ao Hub IOT. Observe que isso também não significa que todas as mensagens em fila tenham sido recebidas. Parte do motivo disso é que a verificação de "todas" mensagens não é tão determinística como uma ação. O que acontece se você recuperar "tudo" das mensagens, mas outra será enviada para o dispositivo imediatamente após? Uma maneira melhor de lidar com isso é com um tempo limite programado. Por exemplo, a função de retorno de chamada de mensagem poderia redefinir um timer sempre que for invocada. Em seguida, você pode escrever lógica para continuar o processamento se, por exemplo, nenhuma mensagem tiver sido recebida nos últimos *X* segundos.

Quando terminar de entrar eventos e receber mensagens, certifique-se de chamar a função correspondente para limpar os recursos.

```C
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

Basicamente, há apenas um conjunto de APIs para enviar e receber dados com um thread em segundo plano e outro conjunto de APIs que faz a mesma coisa sem o thread em segundo plano. Muitos desenvolvedores podem preferir as APIs não LL, mas as APIs de nível inferior são úteis quando o desenvolvedor quer um controle explícito sobre as transmissões de rede. Por exemplo, alguns dispositivos coletam dados ao longo do tempo e apenas os eventos de entrada em intervalos especificados (por exemplo, uma vez por hora ou uma vez por dia). As APIs de nível inferior oferecem a capacidade de controlar explicitamente quando você envia e recebe dados do Hub IoT. Outras simplesmente preferem a simplicidade que as APIs de nível inferior fornecem. Tudo acontece no thread principal, em vez de algum trabalho acontecendo em segundo plano.

Seja qual for o modelo escolhido, certifique-se de estar consistente em quais APIs você usa. Se você começar chamando **\_IoTHubClient ll\_CreateFromConnectionString**, certifique-se de usar apenas as APIs de nível inferior correspondentes para qualquer trabalho de acompanhamento:

* IoTHubClient\_LL\_SendEventAsync
* IoTHubClient\_LL\_SetMessageCallback
* IoTHubClient\_LL\_Destroy
* IoTHubClient\_LL\_DoWork

O oposto também é verdadeiro. Se você começar com **IoTHubClient\_CreateFromConnectionString**, use as APIs não ll para qualquer processamento adicional.

No SDK do dispositivo IOT do Azure para C, consulte o aplicativo **http\_de exemplo de cliente\_iothub\_** para obter um exemplo completo das APIs de nível inferior. O **aplicativo\_iothub\_ClientSample\_AMQP** pode ser referenciado por um exemplo completo das APIs não ll.

## <a name="property-handling"></a>Manipulação de propriedades

Até agora, quando descrevemos o envio de dados, nos referimos ao corpo da mensagem. Por exemplo, considere este código:

```C
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Hello World");
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));
IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

Este exemplo envia uma mensagem ao Hub IoT com o texto "Olá, Mundo". No entanto, o Hub IoT também permite que as propriedades sejam anexadas a cada mensagem. As propriedades são pares de nome/valor que podem ser anexados à mensagem. Por exemplo, podemos modificar o código anterior para anexar uma propriedade à mensagem:

```C
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

Começamos chamando **as propriedades IoTHubMessage\_** e passando o identificador da nossa mensagem. O que obtemos é uma referência de **identificador de mapa\_** que nos permite começar a adicionar propriedades. O último é feito chamando **map\_AddOrUpdate**, que usa uma referência a um identificador de\_mapa, o nome da propriedade e o valor da propriedade. Com essa API, podemos adicionar quantas Propriedades desejar.

Quando o evento é lido nos **hubs de eventos**, o receptor pode enumerar as propriedades e recuperar seus valores correspondentes. Por exemplo, no .NET, isso seria conseguido acessando a [coleção Properties no objeto EVENTDATA](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.properties.aspx).

No exemplo anterior, estamos anexando Propriedades a um evento que enviamos ao Hub IoT. As propriedades também podem ser anexadas a mensagens recebidas do Hub IoT. Se quisermos recuperar as propriedades de uma mensagem, podemos usar um código como o seguinte em nossa função de retorno de chamada de mensagem:

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

A chamada para **Propriedades\_IoTHubMessage** retorna a referência de **identificador de\_mapa** . Em seguida, passamos essa referência para **\_mapear** getinternas para obter uma referência a uma matriz de pares de nome/valor (bem como uma contagem das propriedades). Nesse ponto, é uma simples questão de enumerar as propriedades para chegar aos valores que desejamos.

Você não precisa usar propriedades em seu aplicativo. No entanto, se você precisar defini-los em eventos ou recuperá-los de mensagens, a biblioteca **IoTHubClient** facilitará isso.

## <a name="message-handling"></a>Processamento de mensagens

Conforme mencionado anteriormente, quando as mensagens chegam do Hub IoT, a biblioteca **IoTHubClient** responde invocando uma função de retorno de chamada registrada. Há um parâmetro de retorno dessa função que merece uma explicação adicional. Aqui está um trecho da função de retorno de chamada no aplicativo de exemplo de **http de\_exemplo\_de cliente\_iothub** :

```C
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .
    return IOTHUBMESSAGE_ACCEPTED;
}
```

Observe que o tipo de retorno **é\_resultado\_de disposição IOTHUBMESSAGE** e, neste caso específico, retornamos **IOTHUBMESSAGE\_aceito**. Há outros valores que podemos retornar dessa função que alteram como a biblioteca **IoTHubClient** reage ao retorno de chamada da mensagem. Aqui estão as opções.

* **IOTHUBMESSAGE\_aceito** – a mensagem foi processada com êxito. A biblioteca **IoTHubClient** não invocará a função de retorno de chamada novamente com a mesma mensagem.

* **IOTHUBMESSAGE\_rejeitado** – a mensagem não foi processada e não há nenhum desejo de fazer isso no futuro. A biblioteca **IoTHubClient** não deve invocar a função de retorno de chamada novamente com a mesma mensagem.

* **IOTHUBMESSAGE\_abandonado** – a mensagem não foi processada com êxito, mas a biblioteca **IoTHubClient** deve invocar a função de retorno de chamada novamente com a mesma mensagem.

Para os dois primeiros códigos de retorno, a biblioteca **IoTHubClient** envia uma mensagem ao Hub IOT indicando que a mensagem deve ser excluída da fila do dispositivo e não entregue novamente. O efeito líquido é o mesmo (a mensagem é excluída da fila do dispositivo), mas se a mensagem foi aceita ou rejeitada ainda está registrada.  Registrar essa distinção é útil para remetentes da mensagem que podem ouvir comentários e descobrir se um dispositivo aceitou ou rejeitou uma mensagem específica.

No último caso, uma mensagem também é enviada ao Hub IoT, mas indica que a mensagem deve ser entregue novamente. Normalmente, você abandonará uma mensagem se encontrar algum erro, mas quiser tentar processar a mensagem novamente. Por outro lado, rejeitar uma mensagem é apropriado quando você encontra um erro irrecuperável (ou se você simplesmente decide que não deseja processar a mensagem).

Em qualquer caso, esteja ciente dos diferentes códigos de retorno para que você possa extrair o comportamento desejado da biblioteca **IoTHubClient** .

## <a name="alternate-device-credentials"></a>Credenciais de dispositivo alternativas

Conforme explicado anteriormente, a primeira coisa a fazer ao trabalhar com a biblioteca **IoTHubClient** é obter um **identificador\_de\_cliente IOTHUB** com uma chamada como a seguinte:

```C
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Os argumentos para **IoTHubClient\_CreateFromConnectionString** são a cadeia de conexão do dispositivo e um parâmetro que indica o protocolo que usamos para se comunicar com o Hub IOT. A cadeia de conexão do dispositivo tem um formato que aparece da seguinte maneira:

```C
HostName=IOTHUBNAME.IOTHUBSUFFIX;DeviceId=DEVICEID;SharedAccessKey=SHAREDACCESSKEY
```

Há quatro partes de informações nesta cadeia de caracteres: Nome do Hub IoT, sufixo do Hub IoT, ID do dispositivo e chave de acesso compartilhado. Você Obtém o FQDN (nome de domínio totalmente qualificado) de um hub IoT quando cria a instância do Hub IoT no portal do Azure — isso fornece o nome do Hub IoT (a primeira parte do FQDN) e o sufixo do Hub IoT (o restante do FQDN). Você Obtém a ID do dispositivo e a chave de acesso compartilhado ao registrar seu dispositivo no Hub IoT (conforme descrito no [artigo anterior](iot-hub-device-sdk-c-intro.md)).

**IoTHubClient\_CreateFromConnectionString** oferece uma maneira de inicializar a biblioteca. Se preferir, você pode criar um novo **identificador de\_cliente\_IOTHUB** usando esses parâmetros individuais em vez da cadeia de conexão do dispositivo. Isso é feito com o seguinte código:

```C
IOTHUB_CLIENT_CONFIG iotHubClientConfig;
iotHubClientConfig.iotHubName = "";
iotHubClientConfig.deviceId = "";
iotHubClientConfig.deviceKey = "";
iotHubClientConfig.iotHubSuffix = "";
iotHubClientConfig.protocol = HTTP_Protocol;
IOTHUB_CLIENT_HANDLE iotHubClientHandle = IoTHubClient_LL_Create(&iotHubClientConfig);
```

Isso realiza a mesma coisa que **IoTHubClient\_CreateFromConnectionString**.

Pode parecer óbvio que você desejaria usar **IoTHubClient\_CreateFromConnectionString** em vez desse método mais detalhado de inicialização. No entanto, tenha em mente que, ao registrar um dispositivo no Hub IoT, o que você obtém é uma ID do dispositivo e uma chave do dispositivo (não uma cadeia de conexão). A ferramenta SDK do *Gerenciador de dispositivos* introduzida no [artigo anterior](iot-hub-device-sdk-c-intro.md) usa bibliotecas no **SDK do serviço IOT do Azure** para criar a cadeia de conexão do dispositivo a partir da ID do dispositivo, da chave do dispositivo e do nome do host do Hub IOT. Portanto, **chamar\_IoTHubClient\_ll Create** pode ser preferível porque poupa a etapa de gerar uma cadeia de conexão. Use qualquer método que seja conveniente.

## <a name="configuration-options"></a>Opções de configuração

Até agora, tudo o que foi descrito sobre a maneira como a biblioteca **IoTHubClient** funciona reflete seu comportamento padrão. No entanto, há algumas opções que você pode definir para alterar a forma como a biblioteca funciona. Isso é feito aproveitando a **API\_SetOption IoTHubClient ll\_** . Considere este exemplo:

```C
unsigned int timeout = 30000;
IoTHubClient_LL_SetOption(iotHubClientHandle, "timeout", &timeout);
```

Há algumas opções que são normalmente usadas:

* **Em lotes** (bool) – se **for true**, os dados enviados ao Hub IOT serão enviados em lotes. Se **for false**, as mensagens serão enviadas individualmente. O padrão é **false**. O envio em lote de AMQP/AMQP-WS, bem como a adição de propriedades do sistema em mensagens D2C, tem suporte.

* **Tempo limite** (int não assinado) – esse valor é representado em milissegundos. Se o envio de uma solicitação HTTPS ou o recebimento de uma resposta demorar mais do que esse tempo, a conexão atingirá o tempo limite.

A opção de envio em lote é importante. Por padrão, a biblioteca insere eventos individualmente (um único evento é o que você passa **para\_IoTHubClient\_ll SendEventAsync**). Se a opção de envio em lote for **true**, a biblioteca coletará o maior número de eventos possível do buffer (até o tamanho máximo da mensagem que o Hub IOT aceitará).  O lote de eventos é enviado ao Hub IoT em uma única chamada HTTPS (os eventos individuais são agrupados em uma matriz JSON). A habilitação do envio em lote normalmente resulta em grandes ganhos de desempenho, pois você está reduzindo viagens de ida e volta da rede. Ele também reduz significativamente a largura de banda, já que você está enviando um conjunto de cabeçalhos HTTPS com um lote de eventos em vez de um conjunto de cabeçalhos para cada evento individual. A menos que você tenha um motivo específico para fazer o contrário, normalmente, você desejará habilitar o envio em lote.

## <a name="next-steps"></a>Passos Seguintes

Este artigo descreve em detalhes o comportamento da biblioteca **IoTHubClient** encontrada no SDK do **dispositivo IOT do Azure para C**. Com essas informações, você deve ter uma boa compreensão dos recursos da biblioteca **IoTHubClient** . O segundo artigo desta série é o [SDK do dispositivo IOT do Azure para C-serializador](iot-hub-device-sdk-c-serializer.md), que fornece detalhes semelhantes na biblioteca do serializador.

Para saber mais sobre como desenvolver para o Hub IoT, confira [SDKs do IOT do Azure](iot-hub-devguide-sdks.md).

Para explorar ainda mais os recursos do Hub IoT, consulte Implantando o [ia em dispositivos de borda com Azure IOT Edge](../iot-edge/tutorial-simulate-device-linux.md).
