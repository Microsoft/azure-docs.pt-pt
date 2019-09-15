---
title: Entender o suporte do MQTT do Hub IoT do Azure | Microsoft Docs
description: Guia do desenvolvedor-suporte para dispositivos que se conectam a um ponto de extremidade voltado para o dispositivo do Hub IoT usando o protocolo MQTT. Inclui informações sobre o suporte interno do MQTT nos SDKs do dispositivo IoT do Azure.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/12/2018
ms.author: robinsh
ms.openlocfilehash: 6a43b721b70858d82083538638853c5bbdf1531d
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/15/2019
ms.locfileid: "71004133"
---
# <a name="communicate-with-your-iot-hub-using-the-mqtt-protocol"></a>Comunicar-se com o Hub IoT usando o protocolo MQTT

O Hub IoT permite que os dispositivos se comuniquem com os pontos de extremidade de dispositivo do Hub IoT usando:

* [MQTT v 3.1.1](https://mqtt.org/) na porta 8883
* MQTT v 3.1.1 sobre WebSocket na porta 443.

O Hub IoT não é um agente MQTT completo e não dá suporte a todos os comportamentos especificados no padrão MQTT v 3.1.1. Este artigo descreve como os dispositivos podem usar os comportamentos de MQTT com suporte para se comunicar com o Hub IoT.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Todas as comunicações de dispositivo com o Hub IoT devem ser protegidas usando TLS/SSL. Portanto, o Hub IoT não dá suporte a conexões não seguras pela porta 1883.

## <a name="connecting-to-iot-hub"></a>Conectando-se ao Hub IoT

Um dispositivo pode usar o protocolo MQTT para se conectar a um hub IoT usando qualquer uma das opções a seguir.

* Bibliotecas nos [SDKs do IOT do Azure](https://github.com/Azure/azure-iot-sdks).
* O protocolo MQTT diretamente.

## <a name="using-the-device-sdks"></a>Usando os SDKs do dispositivo

Os [SDKs do dispositivo](https://github.com/Azure/azure-iot-sdks) que dão suporte ao protocolo MQTT estão disponíveis para Java, Node. js C#, C, e Python. Os SDKs do dispositivo usam a cadeia de conexão do Hub IoT padrão para estabelecer uma conexão com um hub IoT. Para usar o protocolo MQTT, o parâmetro de protocolo do cliente deve ser definido como **MQTT**. Por padrão, os SDKs do dispositivo se conectam a um hub IoT com o sinalizador **CleanSession** definido como **0** e usam a **QoS 1** para troca de mensagens com o Hub IOT.

Quando um dispositivo está conectado a um hub IoT, os SDKs do dispositivo fornecem métodos que permitem ao dispositivo trocar mensagens com um hub IoT.

A tabela a seguir contém links para exemplos de código para cada idioma com suporte e especifica o parâmetro a ser usado para estabelecer uma conexão com o Hub IoT usando o protocolo MQTT.

| Idioma | Parâmetro de protocolo |
| --- | --- |
| [Node.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device.js) |azure-iot-device-mqtt |
| [Java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/SendReceive.java) |IotHubClientProtocol.MQTT |
| [C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm) |MQTT_Protocol |
| [C#](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device/samples) |TransportType.Mqtt |
| [Python](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples) |Sempre dá suporte a MQTT por padrão |

### <a name="migrating-a-device-app-from-amqp-to-mqtt"></a>Migrando um aplicativo de dispositivo do AMQP para o MQTT

Se você estiver usando os [SDKs do dispositivo](https://github.com/Azure/azure-iot-sdks), alternar de usando AMQP para MQTT requer a alteração do parâmetro de protocolo na inicialização do cliente, conforme mencionado anteriormente.

Ao fazer isso, certifique-se de verificar os seguintes itens:

* AMQP retorna erros para muitas condições, enquanto MQTT encerra a conexão. Como resultado, sua lógica de tratamento de exceções pode exigir algumas alterações.

* O MQTT não oferece suporte às operações de *rejeição* ao receber [mensagens da nuvem para o dispositivo](iot-hub-devguide-messaging.md). Se o aplicativo de back-end precisar receber uma resposta do aplicativo do dispositivo, considere o uso de [métodos diretos](iot-hub-devguide-direct-methods.md).

* AMQP não tem suporte no SDK do Python

## <a name="using-the-mqtt-protocol-directly-as-a-device"></a>Usando o protocolo MQTT diretamente (como um dispositivo)

Se um dispositivo não puder usar os SDKs do dispositivo, ele ainda poderá se conectar aos pontos de extremidade do dispositivo público usando o protocolo MQTT na porta 8883. No pacote do **Connect** , o dispositivo deve usar os seguintes valores:

* Para o campo **ClientID** , use **DeviceID**.

* Para o campo **nome** de usuário `{iothubhostname}/{device_id}/?api-version=2018-06-30`, use `{iothubhostname}` , em que é o CNAME completo do Hub IOT.

    Por exemplo, se o nome do seu hub IoT for **contoso.Azure-Devices.net** e se o nome do seu dispositivo for **MyDevice01**, o campo **nome de usuário** completo deverá conter:

    `contoso.azure-devices.net/MyDevice01/?api-version=2018-06-30`

* Para o campo **senha** , use um token SAS. O formato do token SAS é o mesmo para os protocolos HTTPS e AMQP:

  `SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`

  > [!NOTE]
  > Se você usar a autenticação de certificado X. 509, as senhas de token SAS não serão necessárias. Para obter mais informações, consulte [Configurar a segurança X. 509 no Hub IOT do Azure](iot-hub-security-x509-get-started.md) e seguir as instruções de código [abaixo](#tlsssl-configuration).

  Para obter mais informações sobre como gerar tokens SAS, consulte a seção dispositivo de [usando tokens de segurança do Hub IOT](iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app).

  Ao testar, você também pode usar as [Ferramentas do Azure IOT de](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) plataforma cruzada para Visual Studio Code ou a ferramenta de [Device Explorer](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer) para gerar rapidamente um token SAS que você pode copiar e colar em seu próprio código:

### <a name="for-azure-iot-tools"></a>Para ferramentas de IoT do Azure

1. Expanda a guia **dispositivos do Hub IOT do Azure** no canto inferior esquerdo da Visual Studio Code.
  
2. Clique com o botão direito do mouse no dispositivo e selecione **gerar token SAS para o dispositivo**.
  
3. Defina o **tempo de expiração** e pressione ' Enter '.
  
4. O token SAS é criado e copiado para a área de transferência.

### <a name="for-device-explorer"></a>Para Device Explorer

1. Vá para a guia **Gerenciamento** em **Device Explorer**.

2. Clique em **token SAS** (canto superior direito).

3. Em **SASTokenForm**, selecione seu dispositivo na lista suspensa **DeviceID** . Defina seu **TTL**.

4. Clique em **gerar** para criar seu token.

   O token SAS gerado tem a seguinte estrutura:

   `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

   A parte deste token a ser usada como o campo de **senha** para se conectar usando MQTT é:

   `SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

Para pacotes MQTT Connect e Disconnect, o Hub IoT emite um evento no canal de **monitoramento de operações** . Esse evento tem informações adicionais que podem ajudá-lo a solucionar problemas de conectividade.

O aplicativo do dispositivo pode especificar uma mensagem **será** exibida no pacote do **Connect** . O aplicativo do dispositivo deve `devices/{device_id}/messages/events/` usar `devices/{device_id}/messages/events/{property_bag}` ou como o nome do tópico **será** para **definir as mensagens** a serem encaminhadas como uma mensagem de telemetria. Nesse caso, se a conexão de rede for fechada, mas um pacote de **desconexão** não tiver sido recebido anteriormente do dispositivo **, o Hub** IOT enviará a mensagem fornecida no pacote **Connect** para o canal de telemetria. O canal de telemetria pode ser o ponto de extremidade de **eventos** padrão ou um ponto de extremidade personalizado definido pelo roteamento do Hub IOT. A mensagem tem a propriedade **iothub-MessageType** com um valor de **será** atribuída a ela.

## <a name="using-the-mqtt-protocol-directly-as-a-module"></a>Usando o protocolo MQTT diretamente (como um módulo)

Conectar-se ao Hub IoT em MQTT usando uma identidade de módulo é semelhante ao dispositivo (descrito [acima](#using-the-mqtt-protocol-directly-as-a-device)), mas você precisa usar o seguinte:

* Defina a ID do cliente `{device_id}/{module_id}`como.

* Se estiver autenticando com nome de usuário e senha, `<hubname>.azure-devices.net/{device_id}/{module_id}/?api-version=2018-06-30` defina o nome de usuário como e use o token SAS associado à identidade do módulo como sua senha.

* Use `devices/{device_id}/modules/{module_id}/messages/events/` o tópico as para publicar a telemetria.

* Usar `devices/{device_id}/modules/{module_id}/messages/events/` como será o tópico.

* Os tópicos GET e PATCH do "/" são idênticos para módulos e dispositivos.

* O tópico de status de entrelaçamento é idêntico para módulos e dispositivos.

## <a name="tlsssl-configuration"></a>Configuração de TLS/SSL

Para usar o protocolo MQTT diretamente, o cliente *deve* se conectar via TLS/SSL. As tentativas de ignorar essa etapa falham com erros de conexão.

Para estabelecer uma conexão TLS, talvez seja necessário baixar e fazer referência ao certificado raiz DigiCert Baltimore. Esse certificado é aquele que o Azure usa para proteger a conexão. Você pode encontrar esse certificado no repositório [Azure-IOT-SDK-c](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) . Mais informações sobre esses certificados podem ser encontradas no [site do DigiCert](https://www.digicert.com/digicert-root-certificates.htm).

Um exemplo de como implementar isso usando a versão Python da [biblioteca PAHO MQTT](https://pypi.python.org/pypi/paho-mqtt) pelo Eclipse Foundation pode ser semelhante ao seguinte.

Primeiro, instale a biblioteca PAHO do seu ambiente de linha de comando:

```cmd/sh
pip install paho-mqtt
```

Em seguida, implemente o cliente em um script Python. Substitua os espaços reservados da seguinte maneira:

* `<local path to digicert.cer>`é o caminho para um arquivo local que contém o certificado raiz DigiCert Baltimore. Você pode criar esse arquivo copiando as informações do certificado de [certs. c](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) no SDK do IOT do Azure para c. inclua `-----BEGIN CERTIFICATE-----` as `-----END CERTIFICATE-----`linhas e remova `"` as marcas no início e no final de cada linha e remova o `\r\n`caracteres no final de cada linha.

* `<device id from device registry>`é a ID de um dispositivo que você adicionou ao seu hub IoT.

* `<generated SAS token>`é um token SAS para o dispositivo criado conforme descrito anteriormente neste artigo.

* `<iot hub name>`o nome do Hub IoT.

```python
from paho.mqtt import client as mqtt
import ssl

path_to_root_cert = "<local path to digicert.cer file>"
device_id = "<device id from device registry>"
sas_token = "<generated SAS token>"
iot_hub_name = "<iot hub name>"


def on_connect(client, userdata, flags, rc):
    print("Device connected with result code: " + str(rc))


def on_disconnect(client, userdata, rc):
    print("Device disconnected with result code: " + str(rc))


def on_publish(client, userdata, mid):
    print("Device sent message")


client = mqtt.Client(client_id=device_id, protocol=mqtt.MQTTv311)

client.on_connect = on_connect
client.on_disconnect = on_disconnect
client.on_publish = on_publish

client.username_pw_set(username=iot_hub_name+".azure-devices.net/" +
                       device_id + "/?api-version=2018-06-30", password=sas_token)

client.tls_set(ca_certs=path_to_root_cert, certfile=None, keyfile=None,
               cert_reqs=ssl.CERT_REQUIRED, tls_version=ssl.PROTOCOL_TLSv1, ciphers=None)
client.tls_insecure_set(False)

client.connect(iot_hub_name+".azure-devices.net", port=8883)

client.publish("devices/" + device_id + "/messages/events/", "{id=123}", qos=1)
client.loop_forever()
```

Veja a seguir as instruções de instalação para os pré-requisitos.

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

Para autenticar usando um certificado de dispositivo, atualize o trecho de código acima com as seguintes alterações (consulte [como obter um certificado de autoridade de certificação X. 509](./iot-hub-x509ca-overview.md#how-to-get-an-x509-ca-certificate) sobre como preparar a autenticação baseada em certificado):

```python
# Create the client as before
# ...

# Set the username but not the password on your client
client.username_pw_set(username=iot_hub_name+".azure-devices.net/" +
                       device_id + "/?api-version=2018-06-30", password=None)

# Set the certificate and key paths on your client
cert_file = "<local path to your certificate file>"
key_file = "<local path to your device key file>"
client.tls_set(ca_certs=path_to_root_cert, certfile=cert_file, keyfile=key_file,
               cert_reqs=ssl.CERT_REQUIRED, tls_version=ssl.PROTOCOL_TLSv1, ciphers=None)

# Connect as before
client.connect(iot_hub_name+".azure-devices.net", port=8883)
```

## <a name="sending-device-to-cloud-messages"></a>Enviando mensagens do dispositivo para a nuvem

Depois de fazer uma conexão bem-sucedida, um dispositivo pode enviar mensagens ao Hub IOT `devices/{device_id}/messages/events/` usando `devices/{device_id}/messages/events/{property_bag}` ou como um **nome de tópico**. O `{property_bag}` elemento permite que o dispositivo envie mensagens com propriedades adicionais em um formato codificado por URL. Por exemplo:

```text
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [!NOTE]
> Esse `{property_bag}` elemento usa a mesma codificação que as cadeias de caracteres de consulta no protocolo HTTPS.

Veja a seguir uma lista de comportamentos específicos de implementação do Hub IoT:

* O Hub IoT não oferece suporte a mensagens de QoS 2. Se um aplicativo de dispositivo publicar uma mensagem com o **QoS 2**, o Hub IOT fechará a conexão de rede.

* O Hub IoT não mantém as mensagens retidas. Se um dispositivo enviar uma mensagem com o sinalizador de **retenção** definido como 1, o Hub IOT adicionará a propriedade de aplicativo **x-opt-reter** à mensagem. Nesse caso, em vez de persistir a mensagem de retenção, o Hub IoT a transmite para o aplicativo de back-end.

* O Hub IoT dá suporte apenas a uma conexão MQTT ativa por dispositivo. Qualquer conexão New MQTT em nome da mesma ID de dispositivo faz com que o Hub IoT descarte a conexão existente.

Para obter mais informações, consulte [Guia do desenvolvedor de mensagens](iot-hub-devguide-messaging.md).

## <a name="receiving-cloud-to-device-messages"></a>Recebendo mensagens da nuvem para o dispositivo

Para receber mensagens do Hub IOT, um dispositivo deve assinar usando `devices/{device_id}/messages/devicebound/#` como um **filtro de tópico**. O curinga `#` de vários níveis no filtro de tópico é usado somente para permitir que o dispositivo receba propriedades adicionais no nome do tópico. O Hub IOT não permite o uso dos `#` caracteres curinga ou `?` para a filtragem de subtópicos. Como o Hub IoT não é um agente de mensagens pub-sub de uso geral, ele dá suporte apenas aos nomes de tópico e filtros de tópico documentados.

O dispositivo não recebe nenhuma mensagem do Hub IOT até que tenha se inscrito com êxito em seu ponto de extremidade específico do dispositivo, representado pelo filtro `devices/{device_id}/messages/devicebound/#` de tópico. Depois que uma assinatura tiver sido estabelecida, o dispositivo receberá mensagens da nuvem para o dispositivo que foram enviadas a ela após o horário da assinatura. Se o dispositivo se conectar com o sinalizador **CleanSession** definido como **0**, a assinatura será persistida em diferentes sessões. Nesse caso, na próxima vez que o dispositivo se conectar com o **CleanSession 0** , ele receberá todas as mensagens pendentes enviadas a ele enquanto estiver desconectado. Se o dispositivo usar o sinalizador **CleanSession** definido como **1** no entanto, ele não receberá nenhuma mensagem do Hub IOT até assinar seu ponto de extremidade do dispositivo.

O Hub IOT entrega mensagens com o **nome** `devices/{device_id}/messages/devicebound/`do tópico `devices/{device_id}/messages/devicebound/{property_bag}` ou quando há propriedades de mensagem. `{property_bag}`contém pares de chave/valor codificados de URL de propriedades de mensagem. Somente as propriedades do aplicativo e as propriedades do sistema de usuário configurável (como **MessageId** ou **CorrelationId**) são incluídas no recipiente de propriedades. Os nomes de Propriedade do sistema **$** têm o prefixo, as propriedades do aplicativo usam o nome da propriedade original sem prefixo.

Quando um aplicativo de dispositivo assina um tópico com o **QoS 2**, o Hub IOT concede o nível 1 de QoS máximo no pacote **SUBACK** . Depois disso, o Hub IoT entrega mensagens para o dispositivo usando a QoS 1.

## <a name="retrieving-a-device-twins-properties"></a>Recuperando as propriedades de um dispositivo

Primeiro, um dispositivo assina `$iothub/twin/res/#`o para receber as respostas da operação. Em seguida, ele envia uma mensagem vazia para `$iothub/twin/GET/?$rid={request id}`o tópico, com um valor preenchido para a **ID da solicitação**. O serviço, em seguida, envia uma mensagem de resposta que contém os `$iothub/twin/res/{status}/?$rid={request id}`dados do dispositivo "entrelaçar" no tópico, usando a mesma **ID de solicitação** da solicitação.

A ID de solicitação pode ser qualquer valor válido para um valor de propriedade de mensagem, de acordo com o [Guia do desenvolvedor de mensagens do Hub IOT](iot-hub-devguide-messaging.md)e o status é validado como um inteiro.

O corpo da resposta contém a seção de propriedades do dispositivo de texto, conforme mostrado no exemplo de resposta a seguir:

```json
{
    "desired": {
        "telemetrySendFrequency": "5m",
        "$version": 12
    },
    "reported": {
        "telemetrySendFrequency": "5m",
        "batteryLevel": 55,
        "$version": 123
    }
}
```

Os códigos de status possíveis são:

|State | Descrição |
| ----- | ----------- |
| 204 | Êxito (nenhum conteúdo é retornado) |
| 429 | Muitas solicitações (limitadas), de acordo com a [limitação do Hub IOT](iot-hub-devguide-quotas-throttling.md) |
| 5** | Erros do servidor |

Para obter mais informações, consulte [Guia do desenvolvedor do dispositivo gêmeos](iot-hub-devguide-device-twins.md).

## <a name="update-device-twins-reported-properties"></a>Atualizar Propriedades relatadas do dispositivo

Para atualizar as propriedades relatadas, o dispositivo emite uma solicitação ao Hub IoT por meio de uma publicação em um tópico de MQTT designado. Após o processamento da solicitação, o Hub IoT responde o status de êxito ou falha da operação de atualização por meio de uma publicação para outro tópico. Este tópico pode ser assinado pelo dispositivo para notificá-lo sobre o resultado de sua solicitação de atualização de entrelaçamento. Para implementar esse tipo de interação de solicitação/resposta no MQTT, aproveitamos a noção da ID da`$rid`solicitação () fornecida inicialmente pelo dispositivo em sua solicitação de atualização. Essa ID de solicitação também é incluída na resposta do Hub IoT para permitir que o dispositivo correlacione a resposta a sua solicitação anterior específica.

A sequência a seguir descreve como um dispositivo atualiza as propriedades relatadas no dispositivo "r" no Hub IoT:

1. Um dispositivo deve primeiro assinar o `$iothub/twin/res/#` tópico para receber as respostas da operação do Hub IOT.

2. Um dispositivo envia uma mensagem que contém a atualização do dispositivo para o `$iothub/twin/PATCH/properties/reported/?$rid={request id}` tópico. Essa mensagem inclui um valor de **ID de solicitação** .

3. Em seguida, o serviço envia uma mensagem de resposta que contém o novo valor de ETag para a coleção `$iothub/twin/res/{status}/?$rid={request id}`Propriedades relatadas no tópico. Essa mensagem de resposta usa a mesma **ID de solicitação** que a solicitação.

O corpo da mensagem de solicitação contém um documento JSON que contém novos valores para propriedades relatadas. Cada membro no documento JSON atualiza ou adiciona o membro correspondente no documento do dispositivo. Um membro definido como `null`, exclui o membro do objeto recipiente. Por exemplo:

```json
{
    "telemetrySendFrequency": "35m",
    "batteryLevel": 60
}
```

Os códigos de status possíveis são:

|State | Descrição |
| ----- | ----------- |
| 200 | Êxito |
| 400 | Solicitação inadequada. JSON malformado |
| 429 | Muitas solicitações (limitadas), de acordo com a [limitação do Hub IOT](iot-hub-devguide-quotas-throttling.md) |
| 5** | Erros do servidor |

O trecho de código Python abaixo demonstra o processo de atualização de propriedades relatadas de entrelaçar em MQTT (usando o cliente PAHO MQTT):

```python
from paho.mqtt import client as mqtt

# authenticate the client with IoT Hub (not shown here)

client.subscribe("$iothub/twin/res/#")
rid = "1"
twin_reported_property_patch = "{\"firmware_version\": \"v1.1\"}"
client.publish("$iothub/twin/PATCH/properties/reported/?$rid=" +
               rid, twin_reported_property_patch, qos=0)
```

Após o êxito da operação de atualização das propriedades relatadas de entrelaçamento acima, a mensagem de publicação do `$iothub/twin/res/204/?$rid=1&$version=6`Hub IOT `204` terá o seguinte tópico:, em `$rid=1` que é o código de status indicando êxito, corresponde à ID da solicitação fornecido pelo dispositivo no código e `$version` corresponde à versão da seção de propriedades relatadas do dispositivo gêmeos após a atualização.

Para obter mais informações, consulte [Guia do desenvolvedor do dispositivo gêmeos](iot-hub-devguide-device-twins.md).

## <a name="receiving-desired-properties-update-notifications"></a>Recebendo notificações de atualização de propriedades desejadas

Quando um dispositivo está conectado, o Hub IOT envia notificações para o `$iothub/twin/PATCH/properties/desired/?$version={new version}`tópico, que contêm o conteúdo da atualização executada pelo back-end da solução. Por exemplo:

```json
{
    "telemetrySendFrequency": "5m",
    "route": null,
    "$version": 8
}
```

Para atualizações de propriedade, `null` os valores significam que o membro do objeto JSON está sendo excluído. Além disso, observe `$version` que indica a nova versão da seção de propriedades desejadas de a.

> [!IMPORTANT]
> O Hub IoT gera notificações de alteração somente quando os dispositivos estão conectados. Certifique-se de implementar o [fluxo de reconexão do dispositivo](iot-hub-devguide-device-twins.md#device-reconnection-flow) para manter as propriedades desejadas sincronizadas entre o Hub IOT e o aplicativo do dispositivo.

Para obter mais informações, consulte [Guia do desenvolvedor do dispositivo gêmeos](iot-hub-devguide-device-twins.md).

## <a name="respond-to-a-direct-method"></a>Responder a um método direto

Primeiro, um dispositivo precisa assinar `$iothub/methods/POST/#`. O Hub IOT envia solicitações de método para `$iothub/methods/POST/{method name}/?$rid={request id}`o tópico, com um JSON válido ou um corpo vazio.

Para responder, o dispositivo envia uma mensagem com um corpo válido de JSON ou vazio para o `$iothub/methods/res/{status}/?$rid={request id}`tópico. Nesta mensagem, a **ID da solicitação** deve corresponder à que está na mensagem de solicitação e o **status** deve ser um inteiro.

Para obter mais informações, consulte [Guia do desenvolvedor do método direto](iot-hub-devguide-direct-methods.md).

## <a name="additional-considerations"></a>Considerações adicionais

Como uma consideração final, se você precisar personalizar o comportamento do protocolo MQTT no lado da nuvem, examine o gateway do [protocolo IOT do Azure](iot-hub-protocol-gateway.md). Este software permite que você implante um gateway de protocolo personalizado de alto desempenho que se interfaces diretamente com o Hub IoT. O gateway de protocolo IoT do Azure permite que você personalize o protocolo de dispositivo para acomodar implantações MQTTs de Brownfield ou outros protocolos personalizados. No entanto, essa abordagem requer que você execute e opere um gateway de protocolo personalizado.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre o protocolo MQTT, consulte a [documentação do MQTT](https://mqtt.org/documentation).

Para saber mais sobre como planejar a implantação do Hub IoT, confira:

* [Catálogo de dispositivos Azure Certified for IoT](https://catalog.azureiotsolutions.com/)
* [Suporte a protocolos adicionais](iot-hub-protocol-gateway.md)
* [Comparar com hubs de eventos](iot-hub-compare-event-hubs.md)
* [Dimensionamento, alta disponibilidade e DR](iot-hub-scaling.md)

Para explorar ainda mais os recursos do Hub IoT, consulte:

* [guia para programadores do IoT Hub](iot-hub-devguide.md)
* [Implantando ia em dispositivos de borda com Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
