---
title: Compreender o suporte para MQTT de Hub do IoT do Azure | Documentos da Microsoft
description: Guia do programador - suporte para os dispositivos ligados a um ponto final voltado para o dispositivo do IoT Hub através do protocolo MQTT. Inclui informações sobre incorporada MQTT suporte no Azure IoT device SDKs.
author: rezasherafat
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/12/2018
ms.author: rezas
ms.openlocfilehash: 89c14f783cd4e46809cb4cae2dcc4c0853a10993
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57552049"
---
# <a name="communicate-with-your-iot-hub-using-the-mqtt-protocol"></a>Comunicar com o seu hub IoT com o protocolo MQTT

IoT Hub permite que os dispositivos a comunicar com os pontos de extremidade de dispositivo do IoT Hub com:

* [MQTT v3.1.1] [ lnk-mqtt-org] na porta 8883
* MQTT v3.1.1 sobre WebSocket na porta 443.

IoT Hub não é um mediador MQTT completo e não suporta todos os comportamentos especificados no padrão MQTT v3.1.1. Este artigo descreve como os dispositivos podem utilizar o suporte para MQTT comportamentos para comunicar com IoT Hub.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Todas as comunicações de dispositivos com o IoT Hub devem ser protegida com TLS/SSL. Por conseguinte, o IoT Hub não suporta ligações não seguras através da porta 1883.

## <a name="connecting-to-iot-hub"></a>Ligar ao IoT Hub

Um dispositivo pode utilizar o protocolo MQTT para ligar a um hub IoT com:

* Qualquer um das bibliotecas do [SDKs IoT do Azure][lnk-device-sdks].
* Ou o protocolo MQTT diretamente.

## <a name="using-the-device-sdks"></a>Utilizar os SDKs do dispositivo

[SDKs do dispositivo] [ lnk-device-sdks] que suportam o protocolo MQTT estão disponíveis para o Java, node. js, C, c# e Python. Os SDKs do dispositivo utilize a cadeia de ligação do IoT Hub padrão para estabelecer uma ligação para um hub IoT. Para utilizar o protocolo MQTT, o parâmetro de protocolo do cliente tem de ser definido **MQTT**. Por predefinição, o dispositivo SDKs ligar a um IoT Hub com o **CleanSession** sinalizador definido como **0** e utilizar **QoS 1** para troca de mensagens com o hub IoT.

Quando um dispositivo está ligado a um hub IoT, os SDKs do dispositivo fornecem métodos que permitem que o dispositivo trocar mensagens com um hub IoT.

A tabela seguinte contém ligações para exemplos de código para cada idioma suportado e especifica o parâmetro a utilizar para estabelecer uma ligação ao IoT Hub através do protocolo MQTT.

| Idioma | Parâmetro Protocol |
| --- | --- |
| [Node.js][lnk-sample-node] |azure-iot-device-mqtt |
| [Java][lnk-sample-java] |IotHubClientProtocol.MQTT |
| [C][lnk-sample-c] |MQTT_Protocol |
| [C#][lnk-sample-csharp] |TransportType.Mqtt |
| [Python][lnk-sample-python] |IoTHubTransportProvider.MQTT |

### <a name="migrating-a-device-app-from-amqp-to-mqtt"></a>Migrar uma aplicação de dispositivo do AMQP para MQTT

Se estiver a utilizar o [SDKs do dispositivo][lnk-device-sdks], mudança do através de AMQP para MQTT exige a alterar o parâmetro de protocolo na inicialização do cliente, conforme indicado anteriormente.

Ao fazê-lo, certifique-se verificar os seguintes itens:

* AMQP devolve erros para várias condições, enquanto MQTT encerra a conexão. Como resultado, a lógica de manipulação de exceções podem exigir algumas alterações.
* MQTT não suporta o *rejeitar* operações quando receber [mensagens da cloud para dispositivo][lnk-messaging]. Se a sua aplicação de back-end precisa receber uma resposta a partir da aplicação de dispositivo, considere utilizar [métodos diretos][lnk-methods].

## <a name="using-the-mqtt-protocol-directly-as-a-device"></a>Através do protocolo MQTT diretamente (como um dispositivo)

Se um dispositivo não é possível utilizar os SDKs do dispositivo, ainda pode ligar para os pontos de extremidade público do dispositivo através do protocolo MQTT na porta 8883. Na **CONNECT** pacotes o dispositivo devem utilizar os seguintes valores:

* Para o **ClientId** campo, utilize o **deviceId**.

* Para o **nome de utilizador** campo, utilize `{iothubhostname}/{device_id}/?api-version=2018-06-30`, onde `{iothubhostname}` é o CName completo do IoT hub.

    Por exemplo, se o nome do seu hub IoT é **contoso. Azure devices.net** e, se o nome do seu dispositivo é **MyDevice01**, o completo **nome de utilizador** campo deve conter:

    `contoso.azure-devices.net/MyDevice01/?api-version=2018-06-30`

* Para o **palavra-passe** campo, utilize um token SAS. O formato do SAS token é igual de protocolos de HTTPS e AMQP:

  `SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`

  > [!NOTE]
  > Se utilizar a autenticação de certificado X.509, palavras-passe de token do SAS não são necessárias. Para obter mais informações, consulte [configurar a segurança X.509 no seu IoT Hub do Azure][lnk-x509]

  Para obter mais informações sobre como gerar SAS tokens, consulte a secção de dispositivo de [tokens de segurança a utilizar o IoT Hub][lnk-sas-tokens].

  Ao testar, também pode utilizar para várias plataformas [ferramentas de IoT do Azure para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) ou o [Device Explorer] [ lnk-device-explorer] ferramenta para gerar um token SAS que pode copiar rapidamente e cole no seu próprio código:

Para ferramentas de IoT do Azure:

  1. Expanda a **dispositivos do AZURE IOT HUB** separador no canto inferior esquerdo do Visual Studio Code.
  2. O dispositivo com o botão direito e selecione **gerar Token SAS, para o dispositivo**.
  3. Definir **hora de expiração** e prima 'Enter'.
  4. O token SAS é criado e copiado para a área de transferência.

Para Device Explorer:

  1. Vá para o **gerenciamento** separador **Device Explorer**.
  2. Clique em **Token de SAS** (canto superior direito).
  3. No **SASTokenForm**, selecione o seu dispositivo na **DeviceID** menu pendente. Definir sua **TTL**.
  4. Clique em **gerar** para criar o token.

     O token SAS gerado tem a seguinte estrutura:

     `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

     A parte do token para utilizar como a **palavra-passe** campo para se conectar usando MQTT é:

     `SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

Para MQTT se ligar e desligar pacotes, o IoT Hub emite um evento no **operações de monitorização** canal. Neste evento tem informações adicionais que podem ajudar a resolver problemas de conectividade.

Pode especificar a aplicação de dispositivo uma **serão** mensagem no **CONNECT** pacotes. Deve utilizar a aplicação de dispositivo `devices/{device_id}/messages/events/` ou `devices/{device_id}/messages/events/{property_bag}` como o **será** para definir o nome do tópico **será** mensagens para ser encaminhadas como uma mensagem de telemetria. Neste caso, se a ligação de rede estiver fechada, mas uma **desligar** pacote não foi anteriormente recebido do dispositivo, em seguida, envia o IoT Hub a **será** mensagem fornecido no **CONNECT** pacotes para o canal de telemetria. O canal de telemetria pode ser qualquer um da predefinição **eventos** ponto final ou um ponto final personalizado definido pelo IoT Hub do encaminhamento. A mensagem tem o **iothub MessageType** propriedade com um valor de **será** atribuídos ao mesmo.

## <a name="using-the-mqtt-protocol-directly-as-a-module"></a>Através do protocolo MQTT diretamente (como um módulo)

Ligar ao IoT Hub sobre MQTT com uma identidade de módulo é semelhante ao dispositivo (descrito [acima](#using-the-mqtt-protocol-directly-as-a-device)), mas tem de utilizar o seguinte:
* Defina o id de cliente `{device_id}/{module_id}`.
* Se a autenticação com o nome de utilizador e palavra-passe, defina o nome de utilizador para `<hubname>.azure-devices.net/{device_id}/{module_id}/?api-version=2018-06-30` e utilizar o token SAS associado à identidade módulo como a palavra-passe.
* Utilize `devices/{device_id}/modules/{module_id}/messages/events/` como tópico para a publicação de telemetria.
* Utilize `devices/{device_id}/modules/{module_id}/messages/events/` como irá tópico.
* Os tópicos de PATCH e duplo GET são idênticos para os módulos e dispositivos.
* O tópico de estado de duplo é idêntico para os dispositivos e módulos.

### <a name="tlsssl-configuration"></a>Configuração de TLS/SSL

Para utilizar o MQTT protocolo diretamente, o cliente *tem* ligar através de TLS/SSL. Tentativas para ignorar este passo falharem com erros de ligação.

Para estabelecer uma conexão TLS, terá de baixar e referenciar o certificado de raiz Baltimore DigiCert. Este certificado é o que utiliza o Azure para proteger a ligação. Pode encontrar este certificado na [Azure-iot-sdk-c] [ lnk-sdk-c-certs] repositório. Obter mais informações sobre estes certificados podem ser encontradas no [site da Digicert][lnk-digicert-root-certs].

Um exemplo de como implementar essa funcionalidade usando a versão de Python do [biblioteca de Paho MQTT] [ lnk-paho] pela Eclipse Foundation poderá ser semelhante ao seguinte.

Primeiro, instale a biblioteca de Paho do seu ambiente de linha de comandos:

```cmd/sh
pip install paho-mqtt
```

Em seguida, implemente o cliente num script de Python. Substitua os marcadores de posição da seguinte forma:

* `<local path to digicert.cer>` é o caminho para um ficheiro local que contém o certificado de raiz de Baltimore DigiCert. Pode criar este ficheiro ao copiar as informações de certificado a partir [certs.c](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) no SDK do Azure IoT para C. inclua as linhas `-----BEGIN CERTIFICATE-----` e `-----END CERTIFICATE-----`, remova o `"` marcas no início e fim de cada linha, e remover o `\r\n` carateres no final de cada linha.
* `<device id from device registry>` é o ID de um dispositivo adicionado ao seu hub IoT.
* `<generated SAS token>` é um token SAS para o dispositivo criado como descrito anteriormente neste artigo.
* `<iot hub name>` o nome do IoT hub.

```python
from paho.mqtt import client as mqtt
import ssl

path_to_root_cert = "<local path to digicert.cer>"
device_id = "<device id from device registry>"
sas_token = "<generated SAS token>"
iot_hub_name = "<iot hub name>"

def on_connect(client, userdata, flags, rc):
  print ("Device connected with result code: " + str(rc))
def on_disconnect(client, userdata, rc):
  print ("Device disconnected with result code: " + str(rc))
def on_publish(client, userdata, mid):
  print ("Device sent message")

client = mqtt.Client(client_id=device_id, protocol=mqtt.MQTTv311)

client.on_connect = on_connect
client.on_disconnect = on_disconnect
client.on_publish = on_publish

client.username_pw_set(username=iot_hub_name+".azure-devices.net/" + device_id, password=sas_token)

client.tls_set(ca_certs=path_to_root_cert, certfile=None, keyfile=None, cert_reqs=ssl.CERT_REQUIRED, tls_version=ssl.PROTOCOL_TLSv1, ciphers=None)
client.tls_insecure_set(False)

client.connect(iot_hub_name+".azure-devices.net", port=8883)

client.publish("devices/" + device_id + "/messages/events/", "{id=123}", qos=1)
client.loop_forever()
```

### <a name="sending-device-to-cloud-messages"></a>Enviar mensagens dispositivo-para-cloud

Depois de fazer uma ligação com êxito, um dispositivo pode enviar mensagens para o IoT Hub usando `devices/{device_id}/messages/events/` ou `devices/{device_id}/messages/events/{property_bag}` como um **nome do tópico**. O `{property_bag}` elemento permite ao dispositivo enviar mensagens com propriedades adicionais num formato com codificação url. Por exemplo:

```text
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [!NOTE]
> Isso `{property_bag}` elemento utiliza a mesma codificação e para as cadeias de consulta o protocolo HTTPS.

Segue-se uma lista de comportamentos de implementação específicos do IoT Hub:

* IoT Hub não suporta mensagens de 2 de QoS. Se uma aplicação de dispositivo publica uma mensagem com **QoS 2**, IoT Hub fecha a ligação de rede.
* IoT Hub não persiste mensagens de manter. Se um dispositivo enviar uma mensagem com o **manter** sinalizador definido como 1, adiciona o IoT Hub a **x-optar ativamente por não-reter** propriedade da aplicação para a mensagem. Neste caso, em vez de manter a mensagem de manter, IoT Hub passa-o para a aplicação de back-end.
* IoT Hub suporta apenas uma ligação de MQTT de Active Directory por dispositivo. Qualquer nova ligação MQTT em nome do mesmo ID de dispositivo faz com que o IoT Hub para remover a ligação existente.

Para obter mais informações, consulte [Guia do Programador de mensagens][lnk-messaging].

### <a name="receiving-cloud-to-device-messages"></a>Receber mensagens da cloud para dispositivo

Para receber mensagens do IoT Hub, um dispositivo deve subscrever com `devices/{device_id}/messages/devicebound/#` como uma **tópico filtro**. O caráter universal de vários nível `#` no filtro de tópico é utilizado apenas para permitir que o dispositivo receber propriedades adicionais no nome do tópico. IoT Hub não permite a utilização do `#` ou `?` carateres universais para filtragem de subtópicos. Uma vez que o IoT Hub não é um mediador de mensagens de publicação para fins gerais, ela oferece suporte apenas os nomes de tópico documentado e filtros de tópico.

O dispositivo não receberá todas as mensagens do IoT Hub, até que subscreveu com êxito para o seu ponto de extremidade específicos do dispositivo, representado pelo `devices/{device_id}/messages/devicebound/#` filtro de tópico. Após o estabelecimento de uma subscrição, o dispositivo recebe mensagens de cloud para o dispositivo que foram enviadas para o mesmo após a hora da subscrição. Se o dispositivo estabelece ligação com **CleanSession** sinalizador definido como **0**, a subscrição está presentes em diferentes sessões. Neste caso, da próxima vez que o dispositivo estabelece ligação com **CleanSession 0** que recebe as mensagens pendentes, enviadas para o mesmo enquanto desligado. Se o dispositivo utiliza **CleanSession** sinalizador definido como **1** no entanto, não recebe todas as mensagens do IoT Hub até que ele assina seu ponto final do dispositivo.

IoT Hub entregar mensagens com o **nome do tópico** `devices/{device_id}/messages/devicebound/`, ou `devices/{device_id}/messages/devicebound/{property_bag}` quando existem propriedades da mensagem. `{property_bag}` contém pares de chave/valor com codificação url de propriedades da mensagem. Apenas as propriedades da aplicação e as propriedades do sistema definível de utilizador (por exemplo, **messageId** ou **correlationId**) estão incluídas no conjunto de propriedades. Nomes de propriedade de sistema têm o prefixo **$**, propriedades da aplicação utilizam o nome da propriedade original com nenhuma prefixo.

Quando uma aplicação de dispositivo se inscreve para um tópico com **QoS 2**, o IoT Hub concede o nível de QoS máximo 1 na **SUBACK** pacotes. Depois disso, o IoT Hub entregar mensagens ao dispositivo utilizar o QoS 1.

### <a name="retrieving-a-device-twins-properties"></a>A obter propriedades de um dispositivo duplo

Em primeiro lugar, um dispositivo subscreve `$iothub/twin/res/#`, para receber respostas da operação. Em seguida, ele envia uma mensagem de vazia para o tópico `$iothub/twin/GET/?$rid={request id}`, com um valor preenchido para **ID do pedido**. O serviço, em seguida, envia uma mensagem de resposta que contém os dados do dispositivo duplo no tópico `$iothub/twin/res/{status}/?$rid={request id}`, com o mesmo **ID do pedido** da solicitação.

ID do pedido pode ser qualquer valor válido para um valor de propriedade da mensagem, de acordo [mensagens guia para programadores do IoT Hub][lnk-messaging], e o estado é validado como um número inteiro.

O corpo da resposta contém a secção de propriedades do dispositivo duplo, conforme mostrado no exemplo seguinte resposta:

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

Os códigos de estado possíveis são:

|Estado | Descrição |
| ----- | ----------- |
| 204 | Êxito (nenhum conteúdo é devolvido) |
| 429 | Demasiados pedidos (limitados), como por [limitação o IoT Hub][lnk-quotas] |
| 5** | Erros de servidor |

Para obter mais informações, consulte [Guia do programador do dispositivo twins][lnk-devguide-twin].

### <a name="update-device-twins-reported-properties"></a>Atualizar propriedades comunicadas do dispositivo duplo

Para atualizar propriedades comunicadas, o dispositivo emite um pedido para o IoT Hub através de uma publicação através de um tópico MQTT designado. Depois de processar o pedido, o IoT Hub responde o estado de êxito ou falha da operação de atualização por meio de uma publicação mudar de tópico. Este tópico pode ser subscrita pelo dispositivo para notificá-lo sobre o resultado de sua solicitação de atualização de duplo. Para implementar este tipo de interação de solicitação/resposta no MQTT, podemos aproveitar a noção de id do pedido (`$rid`) fornecido inicialmente pelo dispositivo no seu pedido de atualização. Este id de pedido também está incluído na resposta do IoT Hub para permitir que o dispositivo correlacionar a resposta ao seu pedido anterior específico.

A sequência seguinte descreve como um dispositivo atualiza as propriedades reportadas no dispositivo duplo no IoT Hub:

1. Um dispositivo tem de subscrever primeiro o `$iothub/twin/res/#` tópico para receber respostas da operação a partir do IoT Hub.

1. Um dispositivo envia uma mensagem que contém a atualização de twin do dispositivo para o `$iothub/twin/PATCH/properties/reported/?$rid={request id}` tópico. Essa mensagem inclui uma **ID do pedido** valor.

1. O serviço, em seguida, envia uma mensagem de resposta que contém o novo valor de ETag para a coleção de propriedades comunicadas no tópico `$iothub/twin/res/{status}/?$rid={request id}`. Esta mensagem de resposta utiliza as mesmas **ID do pedido** da solicitação.

O corpo da mensagem de pedido contém um documento JSON, que contém novos valores para propriedades comunicadas. Cada membro no documento JSON de atualizações ou adicionar o membro correspondente no documento do dispositivo duplo. Um membro definido como `null`, elimina o membro do objeto contentor. Por exemplo:

```json
{
    "telemetrySendFrequency": "35m",
    "batteryLevel": 60
}
```

Os códigos de estado possíveis são:

|Estado | Descrição |
| ----- | ----------- |
| 200 | Êxito |
| 400 | Pedido incorreto. JSON com formato incorreto |
| 429 | Demasiados pedidos (limitados), como por [limitação o IoT Hub][lnk-quotas] |
| 5** | Erros de servidor |

O trecho de código do python, abaixo, demonstra o duplo reportou o processo de atualização de propriedades através de MQTT (usando o cliente de Paho MQTT):
```python
from paho.mqtt import client as mqtt

# authenticate the client with IoT Hub (not shown here)

client.subscribe("$iothub/twin/res/#")
rid = "1"
twin_reported_property_patch = "{\"firmware_version\": \"v1.1\"}"
client.publish("$iothub/twin/PATCH/properties/reported/?$rid=" + rid, twin_reported_property_patch, qos=0)
```

Após a conclusão bem-sucedida do duplo comunicado propriedades operação de atualização acima, a mensagem de publicação do IoT Hub terão o tópico seguinte: `$iothub/twin/res/204/?$rid=1&$version=6`, onde `204` é o código de estado que indica êxito, `$rid=1` corresponde ao ID de pedido fornecido pelo dispositivo no código, e `$version` corresponde à versão da secção de propriedades comunicadas dos dispositivos duplos após a atualização.

Para obter mais informações, consulte [Guia do programador do dispositivo twins][lnk-devguide-twin].

### <a name="receiving-desired-properties-update-notifications"></a>Receber notificações de atualização de propriedades pretendidas

Quando um dispositivo é ligado, o IoT Hub envia notificações para o tópico `$iothub/twin/PATCH/properties/desired/?$version={new version}`, que contêm o conteúdo da atualização realizada pelo back-end da solução. Por exemplo:

```json
{
    "telemetrySendFrequency": "5m",
    "route": null,
    "$version": 8
}
```

Como para atualizações de propriedade, `null` valores significa que o membro de objeto do JSON está a ser eliminado. Além disso, tenha em atenção que `$version` indica a nova versão da secção de propriedades pretendidas do duplo.

> [!IMPORTANT]
> IoT Hub gera notificações de alteração apenas quando os dispositivos ligados. Certifique-se implementar o [fluxo de restabelecimento de ligação do dispositivo] [ lnk-devguide-twin-reconnection] para manter as propriedades pretendidas sincronizadas entre o IoT Hub e a aplicação de dispositivo.

Para obter mais informações, consulte [Guia do programador do dispositivo twins][lnk-devguide-twin].

### <a name="respond-to-a-direct-method"></a>Responder a um método direto

Em primeiro lugar, um dispositivo tem de subscrever `$iothub/methods/POST/#`. IoT Hub envia pedidos de método para o tópico `$iothub/methods/POST/{method name}/?$rid={request id}`, com um JSON válido ou um corpo vazio.

Para responder, o dispositivo envia uma mensagem com um JSON válido ou um corpo vazio para o tópico `$iothub/methods/res/{status}/?$rid={request id}`. Nesta mensagem, o **ID do pedido** tem de coincidir com o mostrado na mensagem de solicitação, e **estado** tem de ser um número inteiro.

Para obter mais informações, consulte [direcionar o Guia do programador do método][lnk-methods].

### <a name="additional-considerations"></a>Considerações adicionais

Como uma consideração final, se precisar de personalizar o comportamento do protocolo MQTT no lado da nuvem, deve rever o [gateway de protocolo do IoT do Azure][lnk-azure-protocol-gateway]. Este software permite-lhe implementar um gateway de protocolo personalizado de alto desempenho desse interfaces diretamente com o IoT Hub. O gateway de protocolo do IoT do Azure permite-lhe personalizar o protocolo de dispositivo para acomodar as implementações de MQTT de recuperação – ou outros protocolos personalizados. Essa abordagem exige, no entanto, que executa e operar um gateway de protocolo personalizado.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre o protocolo MQTT, consulte a [documentação MQTT][lnk-mqtt-docs].

Para saber mais sobre o planeamento da implementação do IoT Hub, veja:

* [Certificado do Azure para o catálogo de dispositivos de IoT][lnk-devices]
* [Suporte para protocolos adicionais][lnk-protocols]
* [Comparar com os Hubs de eventos][lnk-compare]
* [Dimensionamento, HA e DR][lnk-scaling]

Para explorar ainda mais os recursos do IoT Hub, veja:

* [Guia do programador do IoT Hub][lnk-devguide]
* [Implementar o AI em dispositivos de ponta com o Azure IoT Edge][lnk-iotedge]

[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks
[lnk-mqtt-org]: https://mqtt.org/
[lnk-mqtt-docs]: https://mqtt.org/documentation
[lnk-sample-node]: https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device.js
[lnk-sample-java]: https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/SendReceive.java
[lnk-sample-c]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm
[lnk-sample-csharp]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device/samples
[lnk-sample-python]: https://github.com/Azure/azure-iot-sdk-python/tree/master/device/samples
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer
[lnk-sas-tokens]: iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md

[lnk-devices]: https://catalog.azureiotsolutions.com/
[lnk-protocols]: iot-hub-protocol-gateway.md
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-x509]: iot-hub-security-x509-get-started.md

[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-messaging]: iot-hub-devguide-messaging.md

[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-twin-reconnection]: iot-hub-devguide-device-twins.md#device-reconnection-flow
[lnk-devguide-twin]: iot-hub-devguide-device-twins.md
[lnk-sdk-c-certs]: https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c
[lnk-digicert-root-certs]: https://www.digicert.com/digicert-root-certificates.htm
[lnk-paho]: https://pypi.python.org/pypi/paho-mqtt
