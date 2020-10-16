---
title: Compreenda o suporte do Hub MQTT do Azure IoT Microsoft Docs
description: Suporte para dispositivos que se liguem a um ponto final virado para dispositivos IoT Hub utilizando o protocolo MQTT. Inclui informações sobre suporte MQTT incorporado no dispositivo Azure IoT SDKs.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/12/2018
ms.author: robinsh
ms.custom:
- amqp
- mqtt
- 'Role: IoT Device'
- 'Role: Cloud Development'
- contperfq1
ms.openlocfilehash: 720d8f3b1f3d13427cda56ee68596d190ac40dc7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91767312"
---
# <a name="communicate-with-your-iot-hub-using-the-mqtt-protocol"></a>Comunique com o seu hub IoT usando o protocolo MQTT

O IoT Hub permite que os dispositivos comuniquem com os pontos finais do dispositivo IoT Hub utilizando:

* [MQTT v3.1.1](https://mqtt.org/) na porta 8883
* MQTT v3.1.1 sobre WebSocket na porta 443.

O Hub IoT não é um mediador MQTT completo e não suporta todos os comportamentos especificados no MQTT v3.1.1 padrão. Este artigo descreve como os dispositivos podem usar comportamentos MQTT suportados para comunicar com o IoT Hub.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Toda a comunicação do dispositivo com o IoT Hub deve ser fixada utilizando OLS/SSL. Portanto, o IoT Hub não suporta ligações não seguras sobre a porta 1883.

## <a name="connecting-to-iot-hub"></a>Ligação ao Hub IoT

Um dispositivo pode utilizar o protocolo MQTT para ligar a um hub IoT utilizando qualquer uma das seguintes opções.

* Bibliotecas nos [Azure IoT SDKs](https://github.com/Azure/azure-iot-sdks).
* O protocolo MQTT diretamente.

A porta MQTT (8883) está bloqueada em muitos ambientes de networking corporativo e educativo. Se não conseguir abrir a porta 8883 na sua firewall, recomendamos a utilização de MQTT sobre tomadas web. MQTT sobre Web Sockets comunica sobre a porta 443, que está quase sempre aberta em ambientes de networking. Para saber como especificar os protocolos MQTT e MQTT sobre as Teias Quando utilizar os SDKs Azure IoT, consulte [utilizando os SDKs do dispositivo](#using-the-device-sdks).

## <a name="using-the-device-sdks"></a>Utilizando os SDKs do dispositivo

[Os SDKs](https://github.com/Azure/azure-iot-sdks) de dispositivo que suportam o protocolo MQTT estão disponíveis para Java, Node.js, C, C#e Python. Os SDKs do dispositivo utilizam a cadeia de ligação IoT Hub padrão para estabelecer uma ligação a um hub IoT. Para utilizar o protocolo MQTT, o parâmetro do protocolo do cliente deve ser definido como **MQTT**. Também pode especificar MQTT sobre tomadas web no parâmetro do protocolo do cliente. Por predefinição, os SDKs do dispositivo ligam-se a um Hub IoT com a bandeira **cleanSession** definida para **0** e utilizam **o QoS 1** para troca de mensagens com o hub IoT. Embora seja possível configurar **o QoS 0** para uma troca de mensagens mais rápida, deve notar que a entrega não está garantida nem reconhecida. Por esta razão, **o QoS 0** é muitas vezes referido como "fogo e esquecimento".

Quando um dispositivo está ligado a um hub IoT, os SDKs do dispositivo fornecem métodos que permitem ao dispositivo trocar mensagens com um hub IoT.

O quadro seguinte contém ligações a amostras de código para cada idioma suportado e especifica o parâmetro a utilizar para estabelecer uma ligação ao Hub IoT utilizando o MQTT ou o MQTT sobre o protocolo de Tomadas Web.

| Idioma | Parâmetro do protocolo MQTT | MQTT sobre o parâmetro do protocolo de tomadas web
| --- | --- | --- |
| [Node.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device.js) | azure-iot-device-mqtt. Mqtt | azure-iot-device-mqtt. MqttWs |
| [Java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/SendReceive.java) |[IotHubClientProtocol.](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.iothubclientprotocol?view=azure-java-stable) MQTT | IotHubClientProtocol.MQTT_WS |
| [C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm) | [MQTT_Protocol](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothubtransportmqtt-h/mqtt-protocol) | [MQTT_WebSocket_Protocol](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothubtransportmqtt-websockets-h/mqtt-websocket-protocol) |
| [C#](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device/samples) | [Tipo de Transporte](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.transporttype?view=azure-dotnet). Mqtt | TransportType.Mqtt recua para MQTT sobre tomadas web se MQTT falhar. Para especificar apenas O MQTT sobre as tomadas web, utilize TransportType.Mqtt_WebSocket_Only |
| [Python](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples) | Suporta MQTT por padrão | Adicione `websockets=True` a chamada para criar o cliente |

O seguinte fragmento mostra como especificar o protocolo MQTT sobre tomadas web ao utilizar o Azure IoT Node.js SDK:

```javascript
var Client = require('azure-iot-device').Client;
var Protocol = require('azure-iot-device-mqtt').MqttWs;
var client = Client.fromConnectionString(deviceConnectionString, Protocol);
```

O seguinte fragmento mostra como especificar o protocolo MQTT sobre tomadas web ao utilizar o Azure IoT Python SDK:

```python
from azure.iot.device.aio import IoTHubDeviceClient
device_client = IoTHubDeviceClient.create_from_connection_string(deviceConnectionString, websockets=True)
```

### <a name="default-keep-alive-timeout"></a>Tempo limite de tempo padrão

De forma a garantir que uma ligação cliente/IoT Hub permanece viva, tanto o serviço como o cliente enviam regularmente um sinal *de manutenção-vivo* um para o outro. O cliente que utiliza o IoT SDK envia uma conservação viva no intervalo definido nesta tabela abaixo:

|Idioma  |Intervalo padrão de manter vivo  |Configurável  |
|---------|---------|---------|
|Node.js     |   180 segundos      |     Não    |
|Java     |    230 segundos     |     Não    |
|C     | 240 segundos |  [Sim](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md#mqtt-transport)   |
|C#     | 300 segundos |  [Sim](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/src/Transport/Mqtt/MqttTransportSettings.cs#L89)   |
|Python   | 60 segundos |  Não   |

Seguindo a [especificação MQTT,](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718081)o intervalo de ping do IoT Hub é 1,5 vezes o valor de manter vivo o cliente. No entanto, o IoT Hub limita o tempo limite máximo do servidor para 29,45 minutos (1767 segundos) porque todos os serviços Azure estão ligados ao tempo limite de marcha lenta TCP, que é de 29,45 minutos. 

Por exemplo, um dispositivo que usa o SDK Java envia o ping de manter-se vivo e, em seguida, perde a conectividade da rede. 230 segundos depois, o dispositivo perde o ping vivo porque está desligado. No entanto, o IoT Hub não fecha a ligação imediatamente - aguarda mais `(230 * 1.5) - 230 = 115` alguns segundos antes de desligar o dispositivo com o erro [404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md). 

O valor máximo de manter o cliente vivo que pode definir é `1767 / 1.5 = 1177` de segundos. Qualquer tráfego irá reiniciar a manter-se viva. Por exemplo, uma atualização de ficha SAS bem sucedida repõe a vida.

### <a name="migrating-a-device-app-from-amqp-to-mqtt"></a>Migrar uma aplicação de dispositivo de AMQP para MQTT

Se estiver a utilizar o [dispositivo SDKs,](https://github.com/Azure/azure-iot-sdks)mudar de utilização de AMQP para MQTT requer alterar o parâmetro de protocolo na inicialização do cliente, como indicado anteriormente.

Ao fazê-lo, certifique-se de verificar os seguintes itens:

* AMQP retorna erros para muitas condições, enquanto MQTT termina a ligação. Como resultado, a sua lógica de tratamento de exceções pode exigir algumas alterações.

* A MQTT não suporta as operações *de rejeição* quando recebe [mensagens nuvem-dispositivo](iot-hub-devguide-messaging.md). Se a sua aplicação back-end precisar de receber uma resposta da aplicação do dispositivo, considere a utilização de [métodos diretos.](iot-hub-devguide-direct-methods.md)

* AmQP não é suportado no Python SDK.

## <a name="example-in-c-using-mqtt-without-an-azure-iot-sdk"></a>Exemplo em C usando MQTT sem um Azure IoT SDK

No [repositório IoT MQTT Sample,](https://github.com/Azure-Samples/IoTMQTTSample)você encontrará um par de projetos de demonstração C/C++ mostrando como enviar mensagens de telemetria, e receber eventos com um hub IoT sem usar o Azure IoT C SDK. 

Estas amostras utilizam a biblioteca Eclipse Mosquitto para enviar mensagens para o MQTT Broker implementado no hub IoT.

Este repositório contém:

**Para janelas:**

* TelemetriaMQTTWin32: contém código para enviar uma mensagem de telemetria para um hub Azure IoT, construído e executado numa máquina Windows.

* Inscreva-seMQTTWin32: contém código para subscrever eventos de um determinado hub IoT numa máquina Windows.

* DeviceTwinMQTTWin32: contém código para consultar e subscrever os eventos gémeos do dispositivo de um dispositivo no hub Azure IoT numa máquina Windows.

* PnPMQTTWin32: contém código para enviar uma mensagem de telemetria com capacidades de IoT Plug e Play para um hub IoT Azure, construído e executado numa máquina Windows. Pode ler mais no [IoT Plug and Play](https://docs.microsoft.com/azure/iot-pnp/overview-iot-plug-and-play)

**Para Linux:**

* MQTTLinux: contém código e script de construção para executar em Linux (WSL, Ubuntu e Raspbian foram testados até agora).

* LinuxConsoleVS2019: contém o mesmo código mas num projeto VS2019 direcionado para o WSL (subsistil Windows Linux). Este projeto permite-lhe depurar o código em execução no Linux passo a passo a partir do Visual Studio.

**Para mosquitto_pub:**

Esta pasta contém dois comandos de amostras utilizados com mosquitto_pub ferramenta de utilidade fornecida por Mosquitto.org.

* Mosquitto_sendmessage: enviar uma simples mensagem de texto para um hub Azure IoT agindo como um dispositivo.

* Mosquitto_subscribe: ver eventos que ocorrem num centro de IoT Azure.

## <a name="using-the-mqtt-protocol-directly-as-a-device"></a>Utilizando o protocolo MQTT diretamente (como um dispositivo)

Se um dispositivo não puder utilizar os SDKs do dispositivo, ainda pode ligar-se aos pontos finais do dispositivo público utilizando o protocolo MQTT na porta 8883. No pacote **CONNECT,** o dispositivo deve utilizar os seguintes valores:

* Para o campo **ClientId,** utilize o **dispositivoId**.

* Para o campo **Username,** `{iothubhostname}/{device_id}/?api-version=2018-06-30` utilize, onde `{iothubhostname}` está o CName completo do hub IoT.

    Por exemplo, se o nome do seu hub IoT for **contoso.azure-devices.net** e se o nome do seu dispositivo for **MyDevice01,** o campo completo do **nome de utilizador** deve conter:

    `contoso.azure-devices.net/MyDevice01/?api-version=2018-06-30`

* Para o campo **Palavra-Passe,** utilize um token SAS. O formato do token SAS é o mesmo que para os protocolos HTTPS e AMQP:

  `SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`

  > [!NOTE]
  > Se utilizar a autenticação do certificado X.509, não são necessárias palavras-passe para o símbolo SAS. Para obter mais informações, consulte [configurar a segurança X.509 no seu Hub Azure IoT](iot-hub-security-x509-get-started.md) e seguir as instruções de código na [secção de configuração TLS/SSL](#tlsssl-configuration).

  Para obter mais informações sobre como gerar fichas SAS, consulte a secção de dispositivos de Utilização de fichas de [segurança IoT Hub](iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app).

  Ao testar, também pode utilizar as [ferramentas Azure IoT de](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) plataforma cruzada para Código de Estúdio Visual ou o comando de extensão CLI [az iot hub generate-sas-token](/cli/azure/ext/azure-iot/iot/hub?view=azure-cli-latest#ext-azure-iot-az-iot-hub-generate-sas-token) para gerar rapidamente um token SAS que pode copiar e colar no seu próprio código.

### <a name="for-azure-iot-tools"></a>Para ferramentas Azure IoT

1. Expanda o separador **AZURE IOT HUB DEVICES** no canto inferior esquerdo do Código do Estúdio Visual.
  
2. Clique com o botão direito no seu dispositivo e **selecione Gerar Ficha SAS para dispositivo**.
  
3. Desa esta hora **de validade** e prima 'Enter'.
  
4. O token SAS é criado e copiado para a área de transferência.

   O símbolo SAS que é gerado tem a seguinte estrutura:

   `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

   A parte deste token a usar como campo **password** para ligar usando MQTT é:

   `SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

Para pacotes de ligação e desconexão MQTT, o IoT Hub emite um evento no canal **de Monitorização de Operações.** Este evento tem informações adicionais que podem ajudá-lo a resolver problemas de conectividade.

A aplicação do dispositivo pode especificar uma mensagem **Will** no pacote **CONNECT.** A aplicação do dispositivo deve usar `devices/{device_id}/messages/events/` ou como o nome tópico `devices/{device_id}/messages/events/{property_bag}` **will** para definir mensagens **Will** para ser reencaminhada como uma mensagem de telemetria. Neste caso, se a ligação de rede estiver fechada, mas um pacote **DISCONNECT** não foi previamente recebido do dispositivo, então o IoT Hub envia a mensagem **'Testamento'** fornecida no pacote **CONNECT** para o canal de telemetria. O canal de telemetria pode ser o ponto final padrão **de Eventos** ou um ponto final personalizado definido pelo encaminhamento IoT Hub. A mensagem tem a propriedade **iothub-MessageType** com um valor de **Will** atribuído a ela.

## <a name="using-the-mqtt-protocol-directly-as-a-module"></a>Utilização direta do protocolo MQTT (como módulo)

A ligação ao Hub IoT sobre o MQTT utilizando uma identidade de módulo é semelhante à do dispositivo (descrito [na secção sobre a utilização do protocolo MQTT diretamente como dispositivo),](#using-the-mqtt-protocol-directly-as-a-device)mas é necessário utilizar o seguinte:

* Desa estava a identificação do cliente. `{device_id}/{module_id}`

* Se autenticar com nome de utilizador e palavra-passe, desacorda o nome de utilizador `<hubname>.azure-devices.net/{device_id}/{module_id}/?api-version=2018-06-30` e utilize o token SAS associado à identidade do módulo como palavra-passe.

* Use `devices/{device_id}/modules/{module_id}/messages/events/` como tópico para a publicação de telemetria.

* Use `devices/{device_id}/modules/{module_id}/messages/events/` como tópico WILL.

* Os tópicos TWIN GET e PATCH são idênticos para módulos e dispositivos.

* O tópico de estado gémeo é idêntico para módulos e dispositivos.

## <a name="tlsssl-configuration"></a>Configuração TLS/SSL

Para utilizar o protocolo MQTT diretamente, o seu cliente *deve* ligar-se ao TLS/SSL. As tentativas de saltar este passo falham com erros de ligação.

Para estabelecer uma ligação TLS, poderá necessitar de descarregar e fazer referência ao Certificado de Raiz DigiCert Baltimore. Este certificado é o que a Azure utiliza para garantir a ligação. Você pode encontrar este certificado no repositório [Azure-iot-sdk-c.](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) Mais informações sobre estes certificados podem ser encontradas no [site da Digicert.](https://www.digicert.com/digicert-root-certificates.htm)

Um exemplo de como implementar isto usando a versão Python da [biblioteca Paho MQTT](https://pypi.python.org/pypi/paho-mqtt) pela Fundação Eclipse pode parecer o seguinte.

Primeiro, instale a biblioteca Paho a partir do seu ambiente de linha de comando:

```cmd/sh
pip install paho-mqtt
```

Então, implementar o cliente num roteiro python. Substitua os espaços reservados da seguinte forma:

* `<local path to digicert.cer>` é o caminho para um arquivo local que contém o certificado DigiCert Baltimore Root. Pode criar este ficheiro copiando as informações do certificado a partir de [certs.c](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) no Azure IoT SDK para C. Inclua as linhas `-----BEGIN CERTIFICATE-----` `-----END CERTIFICATE-----` e, remova as `"` marcas no início e no fim de cada linha e remova os `\r\n` caracteres no final de cada linha.

* `<device id from device registry>` é o ID de um dispositivo que adicionou ao seu hub IoT.

* `<generated SAS token>` é um símbolo SAS para o dispositivo criado como descrito anteriormente neste artigo.

* `<iot hub name>` o nome do seu hub IoT.

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
               cert_reqs=ssl.CERT_REQUIRED, tls_version=ssl.PROTOCOL_TLSv1_2, ciphers=None)
client.tls_insecure_set(False)

client.connect(iot_hub_name+".azure-devices.net", port=8883)

client.publish("devices/" + device_id + "/messages/events/", "{id=123}", qos=1)
client.loop_forever()
```

Para autenticar utilizando um certificado de dispositivo, atualize o corte de código acima com as seguintes alterações (ver [Como obter um certificado DE CA X.509](./iot-hub-x509ca-overview.md#how-to-get-an-x509-ca-certificate) sobre como preparar a autenticação baseada em certificados):

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
               cert_reqs=ssl.CERT_REQUIRED, tls_version=ssl.PROTOCOL_TLSv1_2, ciphers=None)

# Connect as before
client.connect(iot_hub_name+".azure-devices.net", port=8883)
```

## <a name="sending-device-to-cloud-messages"></a>Envio de mensagens dispositivo-nuvem

Depois de escoar uma ligação bem sucedida, um dispositivo pode enviar mensagens para o IoT Hub utilizando `devices/{device_id}/messages/events/` ou como nome `devices/{device_id}/messages/events/{property_bag}` **tópico**. O `{property_bag}` elemento permite que o dispositivo envie mensagens com propriedades adicionais num formato codificado por url. Por exemplo:

```text
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [!NOTE]
> Este `{property_bag}` elemento utiliza a mesma codificação que as cadeias de consulta no protocolo HTTPS.

Segue-se uma lista de comportamentos específicos de implementação do IoT Hub:

* O IoT Hub não suporta mensagens QoS 2. Se uma aplicação de dispositivo publicar uma mensagem com **o QoS 2,** o IoT Hub fecha a ligação à rede.

* IoT Hub não persiste retê-lo mensagens. Se um dispositivo enviar uma mensagem com a bandeira **RETEN** definida para 1, o IoT Hub adiciona a propriedade de aplicação **x-opt-retenido** à mensagem. Neste caso, em vez de persistir a mensagem de retenção, o IoT Hub passa-a para a aplicação backend.

* O IoT Hub suporta apenas uma ligação MQTT ativa por dispositivo. Qualquer nova ligação MQTT em nome do mesmo ID do dispositivo faz com que o IoT Hub deixe cair a ligação existente.

Para mais informações, consulte [o guia do desenvolvedor de mensagens.](iot-hub-devguide-messaging.md)

## <a name="receiving-cloud-to-device-messages"></a>Receber mensagens nuvem-a-dispositivo

Para receber mensagens do IoT Hub, um dispositivo deve subscrever `devices/{device_id}/messages/devicebound/#` usando como **filtro de tópico**. O wildcard de vários níveis `#` no Filtro Tópico é utilizado apenas para permitir que o dispositivo receba propriedades adicionais no nome do tópico. O IoT Hub não permite a utilização dos `#` `?` ou wildcards para filtragem de subtópicos. Uma vez que o IoT Hub não é um corretor de mensagens pub-sub de uso geral, apenas suporta os nomes de tópicos documentados e filtros tópicos.

O dispositivo não recebe nenhuma mensagem do IoT Hub até ter assinado com sucesso o seu ponto final específico do dispositivo, representado pelo `devices/{device_id}/messages/devicebound/#` filtro de tópicos. Depois de ter sido estabelecida uma subscrição, o dispositivo recebe mensagens nuvem-dispositivo que foram enviadas para o mesmo após o momento da subscrição. Se o dispositivo ligar-se com a bandeira **CleanSession** definida para **0,** a subscrição é persistiu em diferentes sessões. Neste caso, da próxima vez que o dispositivo ligar-se com **o CleanSession 0** recebe quaisquer mensagens pendentes que lhe tenham sido enviadas enquanto está desligado. No entanto, se o dispositivo utilizar a bandeira **CleanSession** definida para **1,** não recebe nenhuma mensagem do IoT Hub até subscrever o seu ponto final do dispositivo.

O IoT Hub entrega mensagens com o **Nome Tópico,** `devices/{device_id}/messages/devicebound/` ou quando `devices/{device_id}/messages/devicebound/{property_bag}` existem propriedades de mensagens. `{property_bag}` contém url-codificado chave/pars de valor de propriedades de mensagens. Apenas as propriedades da aplicação e as propriedades do sistema de definição de utilizador (como **messageId** ou **correlationId)** estão incluídas no saco de propriedade. Os nomes das propriedades do sistema têm o prefixo, **$** as propriedades da aplicação usam o nome original da propriedade sem prefixo. Para obter mais detalhes sobre o formato do saco de propriedade, consulte [enviar mensagens de dispositivo para nuvem](#sending-device-to-cloud-messages).

Nas mensagens nuvem-dispositivo, os valores no saco de propriedade são representados como na tabela seguinte:

| Valor patrimonial | Representação | Descrição |
|----|----|----|
| `null` | `key` | Apenas a chave aparece no saco da propriedade |
| corda vazia | `key=` | A chave seguida por um sinal igual sem valor |
| valor não nulo, não vazio | `key=value` | A chave seguida por um sinal igual e o valor |

O exemplo a seguir mostra um saco de propriedade que contém três propriedades de aplicação: **prop1** com um valor `null` de; **prop2**, uma corda vazia (""); e **prop3** com um valor de "uma corda".

```mqtt
/?prop1&prop2=&prop3=a%20string
```

Quando uma aplicação de dispositivo subscreve um tópico com **qoS 2,** o IoT Hub concede o nível QoS máximo 1 no pacote **SUBACK.** Depois disso, o IoT Hub entrega mensagens ao dispositivo utilizando o QoS 1.

## <a name="retrieving-a-device-twins-properties"></a>Recuperar as propriedades de um dispositivo twin

Em primeiro lugar, um dispositivo `$iothub/twin/res/#` subscreve, para receber as respostas da operação. Em seguida, envia uma mensagem vazia para o `$iothub/twin/GET/?$rid={request id}` tópico, com um valor povoado para **identificação de pedido**. Em seguida, o serviço envia uma mensagem de resposta contendo os dados gémeos do dispositivo sobre `$iothub/twin/res/{status}/?$rid={request id}` o tópico, utilizando o mesmo **ID de pedido** que o pedido.

O ID do pedido pode ser qualquer valor válido para um valor de propriedade de mensagem, de acordo com o [guia do desenvolvedor de mensagens IoT Hub](iot-hub-devguide-messaging.md), e o estado é validado como um número inteiro.

O corpo de resposta contém a secção de propriedades do twin dispositivo, como mostra o seguinte exemplo de resposta:

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
| 200 | Success |
| 429 | Demasiados pedidos (estrangulado), de acordo com [o estrangulamento do IoT Hub](iot-hub-devguide-quotas-throttling.md) |
| 5** | Erros de servidor |

Para mais informações, consulte o [guia do desenvolvedor de gémeos device](iot-hub-devguide-device-twins.md).

## <a name="update-device-twins-reported-properties"></a>Atualizar as propriedades reportadas do dispositivo Twin

Para atualizar as propriedades reportadas, o dispositivo emite um pedido ao IoT Hub através de uma publicação sobre um tópico MQTT designado. Após o processamento do pedido, o IoT Hub responde ao estado de sucesso ou falha da operação de atualização através de uma publicação para outro tópico. Este tópico pode ser subscrito pelo dispositivo de forma a notificá-lo sobre o resultado do seu pedido de atualização dupla. Para implementar este tipo de interação de pedido/resposta em MQTT, aproveitamos a noção de ID de pedido ( `$rid` ) fornecida inicialmente pelo dispositivo no seu pedido de atualização. Este ID de pedido também está incluído na resposta do IoT Hub para permitir que o dispositivo correlacione a resposta ao seu pedido anterior particular.

A seguinte sequência descreve como um dispositivo atualiza as propriedades reportadas no twin do dispositivo no IoT Hub:

1. Um dispositivo deve primeiro subscrever o `$iothub/twin/res/#` tópico para receber as respostas da operação do IoT Hub.

2. Um dispositivo envia uma mensagem que contém a atualização dupla do dispositivo para o `$iothub/twin/PATCH/properties/reported/?$rid={request id}` tópico. Esta mensagem inclui um valor **de ID de pedido.**

3. Em seguida, o serviço envia uma mensagem de resposta que contém o novo valor ETag para a recolha de propriedades reportadas sobre o `$iothub/twin/res/{status}/?$rid={request id}` tópico. Esta mensagem de resposta utiliza o mesmo **ID de pedido** que o pedido.

O corpo de mensagem de pedido contém um documento JSON, que contém novos valores para propriedades reportadas. Cada membro no documento JSON atualiza ou adiciona o membro correspondente no documento do twin do dispositivo. Um membro definido para `null` eliminar o membro do objeto contendo. Por exemplo:

```json
{
    "telemetrySendFrequency": "35m",
    "batteryLevel": 60
}
```

Os códigos de estado possíveis são:

|Estado | Descrição |
| ----- | ----------- |
| 204 | Sucesso (nenhum conteúdo é devolvido) |
| 400 | Mau pedido. JSON mal formado |
| 429 | Demasiados pedidos (estrangulado), de acordo com [o estrangulamento do IoT Hub](iot-hub-devguide-quotas-throttling.md) |
| 5** | Erros de servidor |

O snippet de código python abaixo, demonstra o processo de atualização de propriedades reportadas em gémeos sobre mQTT (usando o cliente Paho MQTT):

```python
from paho.mqtt import client as mqtt

# authenticate the client with IoT Hub (not shown here)

client.subscribe("$iothub/twin/res/#")
rid = "1"
twin_reported_property_patch = "{\"firmware_version\": \"v1.1\"}"
client.publish("$iothub/twin/PATCH/properties/reported/?$rid=" +
               rid, twin_reported_property_patch, qos=0)
```

Após o sucesso da operação de atualização de propriedades reportadas pela Twin acima, a mensagem de publicação do IoT Hub terá o seguinte tópico: `$iothub/twin/res/204/?$rid=1&$version=6` , onde está o código de estado que indica `204` sucesso, corresponde ao `$rid=1` ID de pedido fornecido pelo dispositivo no código, e `$version` corresponde à versão da secção de propriedades reportadas de gémeos do dispositivo após a atualização.

Para mais informações, consulte o [guia do desenvolvedor de gémeos device](iot-hub-devguide-device-twins.md).

## <a name="receiving-desired-properties-update-notifications"></a>Receber notificações de atualização de propriedades desejadas

Quando um dispositivo está ligado, o IoT Hub envia notificações para o tópico `$iothub/twin/PATCH/properties/desired/?$version={new version}` , que contêm o conteúdo da atualização realizada pela solução traseira. Por exemplo:

```json
{
    "telemetrySendFrequency": "5m",
    "route": null,
    "$version": 8
}
```

Quanto às atualizações de propriedade, `null` os valores significam que o membro do objeto JSON está a ser eliminado. Além disso, note que `$version` indica a nova versão da secção de propriedades desejadas do gémeo.

> [!IMPORTANT]
> O IoT Hub gera notificações de alteração apenas quando os dispositivos estão ligados. Certifique-se de implementar o [fluxo de reconexão](iot-hub-devguide-device-twins.md#device-reconnection-flow) do dispositivo para manter as propriedades desejadas sincronizadas entre o IoT Hub e a aplicação do dispositivo.

Para mais informações, consulte o [guia do desenvolvedor de gémeos device](iot-hub-devguide-device-twins.md).

## <a name="respond-to-a-direct-method"></a>Responder a um método direto

Primeiro, um dispositivo tem de `$iothub/methods/POST/#` subscrever. O IoT Hub envia pedidos de método para o `$iothub/methods/POST/{method name}/?$rid={request id}` tópico, com um JSON válido ou um corpo vazio.

Para responder, o dispositivo envia uma mensagem com um JSON válido ou corpo vazio para o tópico `$iothub/methods/res/{status}/?$rid={request id}` . Nesta mensagem, o **ID** do pedido deve corresponder ao da mensagem de pedido, e o **estado** deve ser um inteiro.

Para obter mais informações, consulte o [guia do programador de métodos Diretos.](iot-hub-devguide-direct-methods.md)

## <a name="additional-considerations"></a>Considerações adicionais

Como consideração final, se precisar de personalizar o comportamento do protocolo MQTT no lado da nuvem, deve rever o gateway de [protocolo Azure IoT](iot-hub-protocol-gateway.md). Este software permite-lhe implementar um portal de protocolo personalizado de alto desempenho que interage diretamente com o IoT Hub. O gateway de protocolo Azure IoT permite-lhe personalizar o protocolo do dispositivo para acomodar implementações MQTT brownfield ou outros protocolos personalizados. Esta abordagem requer, no entanto, que você corra e opere um portal de protocolo personalizado.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o protocolo MQTT, consulte a [documentação MQTT](https://mqtt.org/).

Para saber mais sobre o planeamento da sua implantação no IoT Hub, consulte:

* [Catálogo de dispositivos do Microsoft Azure Certified for IoT](https://catalog.azureiotsolutions.com/)
* [Apoiar protocolos adicionais](iot-hub-protocol-gateway.md)
* [Compare com centros de eventos](iot-hub-compare-event-hubs.md)
* [Escala, HA e DR](iot-hub-scaling.md)

Para explorar ainda mais as capacidades do IoT Hub, consulte:

* [Guia de desenvolvimento do IoT Hub](iot-hub-devguide.md)
* [Implementar o AI em dispositivos de ponta com o Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
