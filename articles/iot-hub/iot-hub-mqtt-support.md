---
title: Compreenda o suporte do Hub MQTT do Azure IoT Microsoft Docs
description: Guia de desenvolvimento - suporte para dispositivos que se ligam a um ponto final virado para o dispositivo IoT Hub utilizando o protocolo MQTT. Inclui informações sobre suporte mQTT incorporado nos SDKs do dispositivo Azure IoT.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/12/2018
ms.author: robinsh
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: c3fa56daee5d2dba98fa9fd420524a9b7e4c60ba
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83726116"
---
# <a name="communicate-with-your-iot-hub-using-the-mqtt-protocol"></a>Comunique com o seu hub IoT usando o protocolo MQTT

O IoT Hub permite que os dispositivos se comuniquem com os pontos finais do dispositivo IoT Hub utilizando:

* [MQTT v3.1.1](https://mqtt.org/) na porta 8883
* MQTT v3.1.1 sobre WebSocket na porta 443.

O Hub IoT não é um mediador MQTT completo e não suporta todos os comportamentos especificados no MQTT v3.1.1 padrão. Este artigo descreve como os dispositivos podem usar comportamentos MQTT suportados para comunicar com o IoT Hub.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Todas as comunicações do dispositivo com o Hub IoT devem ser protegidas utilizando TLS/SSL. Portanto, o IoT Hub não suporta ligações não seguras sobre a porta 1883.

## <a name="connecting-to-iot-hub"></a>Ligação ao IoT Hub

Um dispositivo pode utilizar o protocolo MQTT para se ligar a um hub IoT utilizando qualquer uma das seguintes opções.

* Bibliotecas nos [DSDs Azure IoT](https://github.com/Azure/azure-iot-sdks).
* O protocolo MQTT diretamente.

A porta MQTT (8883) está bloqueada em muitos ambientes de networking corporativos e educativos. Se não conseguir abrir a porta 8883 na sua firewall, recomendamos a utilização de MQTT através de Tomadas Web. MQTT sobre Web Sockets comunica sobre a porta 443, que está quase sempre aberta em ambientes de rede. Para saber especificar os protocolos MQTT e MQTT sobre as tomadas Web ao utilizar os SDKs Azure IoT, consulte [A utilização dos SDKs do dispositivo](#using-the-device-sdks).

## <a name="using-the-device-sdks"></a>Utilização dos SDKs do dispositivo

[Os SDKs](https://github.com/Azure/azure-iot-sdks) de dispositivo que suportam o protocolo MQTT estão disponíveis para Java, Node.js, C, C#e Python. Os SDKs do dispositivo utilizam a cadeia de ligação IoT Hub padrão para estabelecer uma ligação a um hub IoT. Para utilizar o protocolo MQTT, o parâmetro do protocolo do cliente deve ser definido para **MQTT**. Também pode especificar MQTT sobre Web Sockets no parâmetro do protocolo do cliente. Por predefinição, os SDKs do dispositivo ligam-se a um Hub IoT com a bandeira **CleanSession** definida para **0** e utilizam **o QoS 1** para troca de mensagens com o hub IoT.

Quando um dispositivo está ligado a um hub IoT, os SDKs do dispositivo fornecem métodos que permitem ao dispositivo trocar mensagens com um hub IoT.

A tabela seguinte contém links para amostras de código para cada idioma suportado e especifica o parâmetro a utilizar para estabelecer uma ligação ao IoT Hub utilizando o MQTT ou o MQTT sobre o protocolo Web Sockets.

| Linguagem | Parâmetro de protocolo MQTT | Parâmetro de protocolo MQTT sobre Web Sockets
| --- | --- | --- |
| [Node.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device.js) | azure-iot-dispositivo-mqtt. Mqtt | azure-iot-dispositivo-mqtt. MqttWs |
| [Java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/SendReceive.java) |[IotHubClientProtocol](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.iothubclientprotocol?view=azure-java-stable). MQTT | IotHubClientProtocol.MQTT_WS |
| [C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm) | [MQTT_Protocol](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothubtransportmqtt-h/mqtt-protocol) | [MQTT_WebSocket_Protocol](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothubtransportmqtt-websockets-h/mqtt-websocket-protocol) |
| [C #](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device/samples) | [Tipo de transporte](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.transporttype?view=azure-dotnet). Mqtt | TransportType.Mqtt volta a MQTT sobre as tomadas web se o MQTT falhar. Para especificar mQTT apenas sobre tomadas web, use TransportType.Mqtt_WebSocket_Only |
| [Python](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples) | Suporta MQTT por defeito | Adicione `websockets=True` a chamada para criar o cliente |

O seguinte fragmento mostra como especificar o protocolo MQTT sobre tomadas Web ao utilizar o Nó Azure IoT Node.js SDK:

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

### <a name="default-keep-alive-timeout"></a>Tempo de tempo de manutenção padrão

De forma a garantir que a ligação cliente/IoT Hub permaneça viva, tanto o serviço como o cliente enviam regularmente um ping *vivo* um ao outro. O cliente que utiliza o IoT SDK envia um keep-alive no intervalo definido nesta tabela abaixo:

|Linguagem  |Intervalo de manutenção-viva padrão  |Configurável  |
|---------|---------|---------|
|Node.js     |   180 segundos      |     Não    |
|Java     |    230 segundos     |     Não    |
|C     | 240 segundos |  [Sim](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md#mqtt-transport)   |
|C#     | 300 segundos |  [Sim](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/src/Transport/Mqtt/MqttTransportSettings.cs#L89)   |
|Python (V2)   | 60 segundos |  Não   |

Seguindo a [especificação MQTT,](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718081)o intervalo de ping do IoT Hub é 1,5 vezes o valor de manutenção vivo do cliente. No entanto, o IoT Hub limita o tempo máximo do servidor para 29,45 minutos (1767 segundos) porque todos os serviços Azure estão ligados ao tempo inativo do equilíbrio de carga Azure TCP, que é de 29,45 minutos. 

Por exemplo, um dispositivo que utiliza o Java SDK envia o ping de manutenção vivo e perde a conectividade da rede. 230 segundos depois, o dispositivo falha o ping de manutenção porque está offline. No entanto, o IoT Hub não fecha a ligação imediatamente - aguarda mais `(230 * 1.5) - 230 = 115` alguns segundos antes de desligar o dispositivo com o erro [404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md). 

O valor máximo de manutenção do cliente vivo que pode definir são `1767 / 1.5 = 1177` segundos. Qualquer tráfego redefinirá a manutenção viva. Por exemplo, um refresco de token sas bem sucedido repõe a manutenção viva.

### <a name="migrating-a-device-app-from-amqp-to-mqtt"></a>Migrar uma aplicação de dispositivo de AMQP para MQTT

Se estiver a utilizar os [SDKs](https://github.com/Azure/azure-iot-sdks)do dispositivo, mudar de utilização amqp para MQTT requer alterar o parâmetro de protocolo na inicialização do cliente, tal como indicado anteriormente.

Ao fazê-lo, certifique-se de verificar os seguintes itens:

* A AMQP devolve erros por muitas condições, enquanto o MQTT termina a ligação. Como resultado, a sua lógica de manipulação de exceções pode exigir algumas alterações.

* O MQTT não suporta as operações de *rejeição* ao receber [mensagens cloud-to-device](iot-hub-devguide-messaging.md). Se a sua aplicação back-end precisar de receber uma resposta da aplicação do dispositivo, considere utilizar [métodos diretos](iot-hub-devguide-direct-methods.md).

* AmQP não é suportado no Python SDK

## <a name="using-the-mqtt-protocol-directly-as-a-device"></a>Utilização direta do protocolo MQTT (como dispositivo)

Se um dispositivo não puder utilizar os SDKs do dispositivo, ainda pode ligar-se aos pontos finais do dispositivo público utilizando o protocolo MQTT na porta 8883. No pacote **CONNECT,** o dispositivo deve utilizar os seguintes valores:

* Para o campo **ClientId,** utilize o **dispositivoId**.

* Para o campo **Username,** `{iothubhostname}/{device_id}/?api-version=2018-06-30` utilize, onde `{iothubhostname}` está o CName completo do hub IoT.

    Por exemplo, se o nome do seu hub IoT for **contoso.azure-devices.net** e se o nome do seu dispositivo for **MyDevice01,** o campo de **username** completo deve conter:

    `contoso.azure-devices.net/MyDevice01/?api-version=2018-06-30`

* Para o campo **Password,** utilize um token SAS. O formato do token SAS é o mesmo que para os protocolos HTTPS e AMQP:

  `SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`

  > [!NOTE]
  > Se utilizar a autenticação do certificado X.509, não são necessárias senhas de token SAS. Para mais informações, consulte [A segurança X.509 no seu Hub Azure IoT](iot-hub-security-x509-get-started.md) e siga as instruções de código [abaixo](#tlsssl-configuration).

  Para obter mais informações sobre como gerar tokens SAS, consulte a secção do dispositivo de utilização de [tokens de segurança Do Hub IoT](iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app).

  Ao testar, também pode utilizar as [ferramentas azure ioT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) cross-platform para o Código do Estúdio Visual ou o comando de extensão CLI [az iot hub generate-sas-token](/cli/azure/ext/azure-iot/iot/hub?view=azure-cli-latest#ext-azure-iot-az-iot-hub-generate-sas-token) para gerar rapidamente um token SAS que você pode copiar e colar no seu próprio código:

### <a name="for-azure-iot-tools"></a>Para ferramentas Azure IoT

1. Expanda o separador **DISPOSITIVOS HUB AZURE IOT** no canto inferior esquerdo do Visual Studio Code.
  
2. Clique no seu dispositivo e selecione **Generate SAS Token for Device**.
  
3. Detete **o tempo** de validade e prima 'Enter'.
  
4. O token SAS é criado e copiado para a prancheta.

   O símbolo SAS gerado tem a seguinte estrutura:

   `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

   A parte deste símbolo para usar como campo **password** para ligar usando MQTT é:

   `SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

Para pacotes de ligação e desconexão MQTT, o IoT Hub emite um evento no canal de **Monitorização de Operações.** Este evento tem informações adicionais que podem ajudá-lo a resolver problemas de conectividade.

A aplicação do dispositivo pode especificar uma mensagem **Will** no pacote **CONNECT.** A aplicação do dispositivo deve ser utilizada ou como o nome tópico `devices/{device_id}/messages/events/` Will para definir as `devices/{device_id}/messages/events/{property_bag}` mensagens **Will** a serem reencaminhadas como uma mensagem de **Will** telemetria. Neste caso, se a ligação de rede estiver fechada, mas um pacote **DESEMPto** não foi previamente recebido do dispositivo, então o IoT Hub envia a mensagem **Will** fornecida no pacote **CONNECT** para o canal de telemetria. O canal de telemetria pode ser o ponto final padrão **eventos** ou um ponto final personalizado definido pelo encaminhamento IoT Hub. A mensagem tem a propriedade **iothub-MessageType** com um valor de **Will** atribuído a ela.

### <a name="an-example-of-c-code-using-mqtt-without-azure-iot-c-sdk"></a>Um exemplo de código C usando MQTT sem Azure IoT C SDK
Neste [repositório,](https://github.com/Azure-Samples/IoTMQTTSample)você encontrará um par de projetos de demonstração C/C++ mostrando como enviar mensagens de telemetria, receber eventos com um hub IoT sem usar o Azure IoT C SDK. 

Estas amostras utilizam a biblioteca Eclipse Mosquitto para enviar mensagem ao Corretor MQTT implementado no centro IoT.

Este repositório contém:

**Para janelas:**

* TelemettryMQTTWin32: contém código para enviar uma mensagem de telemetria para um hub Azure IoT, construído e executado numa máquina windows.

* SubscritoMQTTWin32: contém código para subscrever eventos de um determinado hub IoT numa máquina Windows.

* DispositivoTwinMQTTWin32: contém código para consultar e subscrever o dispositivo eventos gémeos de um dispositivo no hub Azure IoT numa máquina Windows.

* PnPMQTTWin32: contém código para enviar uma mensagem de telemetria com capacidades de pré-visualização IoT Plug & Play para um hub Azure IoT, construído e executado numa máquina windows. Mais sobre IoT Plug & Play [aqui](https://docs.microsoft.com/azure/iot-pnp/overview-iot-plug-and-play)

**Para Linux:**

* MQTTLinux: contém código e construção script para executar em Linux (WSL, Ubuntu e Raspbian foram testados até agora).

* LinuxConsoleVS2019: contém o mesmo código mas num projeto VS2019 direcionado para a WSL (subsistema Windows Linux). Este projeto permite-lhe depurar o código em execução no Linux passo a passo do Visual Studio.

**Para mosquitto_pub:**

Esta pasta contém duas amostras de comandos utilizados com mosquitto_pub ferramenta de utilidade fornecida por Mosquitto.org.

* Mosquitto_sendmessage: enviar uma simples mensagem de texto a um hub Azure IoT agindo como um dispositivo.

* Mosquitto_subscribe: ver eventos que ocorrem num centro azure ioT.

## <a name="using-the-mqtt-protocol-directly-as-a-module"></a>Utilização direta do protocolo MQTT (como módulo)

A ligação ao IoT Hub sobre o MQTT utilizando uma identidade de módulo é semelhante ao dispositivo [(descrito acima),](#using-the-mqtt-protocol-directly-as-a-device)mas é necessário utilizar o seguinte:

* Detete o ID do cliente para `{device_id}/{module_id}` .

* Se autenticar com nome de utilizador e senha, detete o nome de utilizador `<hubname>.azure-devices.net/{device_id}/{module_id}/?api-version=2018-06-30` e utilize o token SAS associado à identidade do módulo como palavra-passe.

* Use `devices/{device_id}/modules/{module_id}/messages/events/` como tópico para publicar telemetria.

* Use `devices/{device_id}/modules/{module_id}/messages/events/` como tópico WILL.

* Os tópicos twin GET e PATCH são idênticos para módulos e dispositivos.

* O tópico de estado gémeo é idêntico para módulos e dispositivos.

## <a name="tlsssl-configuration"></a>Configuração TLS/SSL

Para utilizar o protocolo MQTT diretamente, o seu cliente *deve* ligar-se ao TLS/SSL. As tentativas de saltar este passo falham com erros de ligação.

Para estabelecer uma ligação TLS, poderá ser necessário descarregar e fazer referência ao Certificado raiz digiCert Baltimore. Este certificado é o que o Azure usa para garantir a ligação. Pode encontrar este certificado no repositório [Azure-iot-sdk-c.](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) Mais informações sobre estes certificados podem ser encontradas no [site da Digicert.](https://www.digicert.com/digicert-root-certificates.htm)

Um exemplo de como implementá-lo usando a versão Python da [biblioteca Paho MQTT](https://pypi.python.org/pypi/paho-mqtt) pela Fundação Eclipse pode parecer o seguinte.

Primeiro, instale a biblioteca Paho a partir do seu ambiente de linha de comando:

```cmd/sh
pip install paho-mqtt
```

Então, implemente o cliente num guião python. Substitua os espaços reservados da seguinte forma:

* `<local path to digicert.cer>`é o caminho para um arquivo local que contém o certificado digiCert Baltimore Root. Pode criar este ficheiro copiando as informações do certificado de [certs.c](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) no Azure IoT SDK para C. Inclua as linhas `-----BEGIN CERTIFICATE-----` `-----END CERTIFICATE-----` e, remova as `"` marcas no início e no fim de cada linha, e remova os caracteres no final de cada `\r\n` linha.

* `<device id from device registry>`é a identificação de um dispositivo que adicionou ao seu hub IoT.

* `<generated SAS token>`é um símbolo SAS para o dispositivo criado como descrito anteriormente neste artigo.

* `<iot hub name>`o nome do seu centro ioT.

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

Para autenticar utilizando um certificado de dispositivo, atualize o código acima com as seguintes alterações (ver [como obter um certificado De Ca X.509](./iot-hub-x509ca-overview.md#how-to-get-an-x509-ca-certificate) sobre como se preparar para autenticação baseada em certificados):

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

## <a name="sending-device-to-cloud-messages"></a>Envio de mensagens de dispositivo-para-nuvem

Depois de fazer uma ligação bem sucedida, um dispositivo pode enviar mensagens para o IoT Hub usando `devices/{device_id}/messages/events/` ou como nome tópico `devices/{device_id}/messages/events/{property_bag}` . **Topic Name** O `{property_bag}` elemento permite que o dispositivo envie mensagens com propriedades adicionais num formato codificado por url. Por exemplo:

```text
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [!NOTE]
> Este elemento utiliza a mesma codificação que as cordas de `{property_bag}` consulta no protocolo HTTPS.

Segue-se uma lista de comportamentos específicos de implementação do IoT Hub:

* O IoT Hub não suporta mensagens QoS 2. Se uma aplicação de dispositivo publicar uma mensagem com **o QoS 2,** o IoT Hub fecha a ligação de rede.

* O IoT Hub não persiste Reter mensagens. Se um dispositivo enviar uma mensagem com a bandeira **REEM** definida para 1, o IoT Hub adiciona a propriedade de aplicação **x-opt-retenção** à mensagem. Neste caso, em vez de persistir na mensagem de retenção, o IoT Hub passa-a para a aplicação backend.

* O IoT Hub apenas suporta uma ligação MQTT ativa por dispositivo. Qualquer nova ligação MQTT em nome do mesmo dispositivo ID faz com que o IoT Hub deixe cair a ligação existente.

Para mais informações, consulte o [guia do desenvolvedor de Mensagens](iot-hub-devguide-messaging.md).

## <a name="receiving-cloud-to-device-messages"></a>Receber mensagens cloud-to-device

Para receber mensagens do IoT Hub, um dispositivo deve subscrever usando `devices/{device_id}/messages/devicebound/#` como **filtro**tópico . O wildcard multi-nível no Filtro tópico `#` é utilizado apenas para permitir que o dispositivo receba propriedades adicionais no nome tópico. O IoT Hub não permite a utilização dos `#` `?` ou wildcards para filtragem de subtópicos. Uma vez que o IoT Hub não é um corretor de mensagens sub-bar de uso geral, apenas suporta os nomes de tópicos documentados e filtros tópicos.

O dispositivo não recebe nenhuma mensagem do IoT Hub, até que tenha subscrito com sucesso o seu ponto final específico do dispositivo, representado pelo `devices/{device_id}/messages/devicebound/#` filtro tópico. Depois de ser estabelecida uma subscrição, o dispositivo recebe mensagens cloud-to-device que lhe foram enviadas após o momento da subscrição. Se o dispositivo se ligar à bandeira **CleanSession** definida para **0,** a subscrição é perdurada em diferentes sessões. Neste caso, da próxima vez que o dispositivo se ligar à **CleanSession 0,** recebe quaisquer mensagens pendentes enviadas para ele enquanto estiver desligado. Se o dispositivo utilizar a bandeira **CleanSession** definida para **1,** não recebe nenhuma mensagem do IoT Hub até que subscreva o seu ponto final do dispositivo.

O IoT Hub entrega mensagens com o **Nome tópico** , ou quando existem propriedades de `devices/{device_id}/messages/devicebound/` `devices/{device_id}/messages/devicebound/{property_bag}` mensagens. `{property_bag}`contém chave/par de valores codificados por url de propriedades de mensagem. Apenas as propriedades da aplicação e as propriedades do sistema definidos pelo utilizador (como **messageId** ou **correlationId)** estão incluídas no saco de propriedade. Os nomes de propriedade do sistema têm o **$** prefixo, as propriedades da aplicação usam o nome original da propriedade sem prefixo.

Quando uma aplicação de dispositivo subscreve um tópico com **QoS 2,** o IoT Hub concede o nível máximo de QoS 1 no pacote **SUBACK.** Depois disso, o IoT Hub entrega mensagens ao dispositivo utilizando o QoS 1.

## <a name="retrieving-a-device-twins-properties"></a>Recuperando as propriedades de um dispositivo twin

Em primeiro lugar, um dispositivo `$iothub/twin/res/#` subscreve, para receber as respostas da operação. Em seguida, envia uma mensagem vazia para o `$iothub/twin/GET/?$rid={request id}` tópico, com um valor povoado para **identificação**de pedido . O serviço envia então uma mensagem de resposta contendo os dados gémeos do dispositivo sobre o `$iothub/twin/res/{status}/?$rid={request id}` tópico, utilizando o mesmo ID de **pedido** que o pedido.

O ID de pedido pode ser qualquer valor válido para um valor de propriedade de mensagem, de acordo com o guia do programador de [mensagens IoT Hub,](iot-hub-devguide-messaging.md)e o estado é validado como um inteiro.

O corpo de resposta contém a secção de propriedades do dispositivo twin, como mostra o seguinte exemplo de resposta:

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
| 200 | Êxito |
| 429 | Demasiados pedidos (estrangulados), de acordo com o [iot hub estrangulando](iot-hub-devguide-quotas-throttling.md) |
| 5** | Erros de servidor |

Para mais informações, consulte o [guia do desenvolvedor de gémeos dispositivos.](iot-hub-devguide-device-twins.md)

## <a name="update-device-twins-reported-properties"></a>Atualizar as propriedades reportadas do dispositivo twin

Para atualizar as propriedades reportadas, o dispositivo emite um pedido ao IoT Hub através de uma publicação sobre um tópico designado de MQTT. Após o processamento do pedido, o IoT Hub responde ao estado de sucesso ou falha da operação de atualização através de uma publicação para outro tópico. Este tópico pode ser subscrito pelo dispositivo de forma a notificá-lo sobre o resultado do seu pedido de atualização dupla. Para implementar este tipo de interação de pedido/resposta no MQTT, aproveitamos a noção de ID de pedido ( ) fornecida inicialmente pelo dispositivo no seu pedido de `$rid` atualização. Este ID de pedido também está incluído na resposta do IoT Hub para permitir que o dispositivo correlacionado com a resposta ao seu pedido anterior específico.

A seguinte sequência descreve como um dispositivo atualiza as propriedades reportadas no dispositivo twin no IoT Hub:

1. Um dispositivo deve primeiro subscrever o `$iothub/twin/res/#` tópico para receber as respostas da operação a partir do IoT Hub.

2. Um dispositivo envia uma mensagem que contém a atualização gémea do dispositivo para o `$iothub/twin/PATCH/properties/reported/?$rid={request id}` tópico. Esta mensagem inclui um valor de ID de **pedido.**

3. O serviço envia então uma mensagem de resposta que contém o novo valor ETag para a recolha de imóveis reportados sobre tópico `$iothub/twin/res/{status}/?$rid={request id}` . Esta mensagem de resposta utiliza o mesmo PEDIDO de **identificação** que o pedido.

O organismo de mensagem de pedido contém um documento JSON, que contém novos valores para propriedades reportadas. Cada membro nas atualizações do documento JSON ou adicione o membro correspondente no documento do dispositivo twin. Um membro definido para `null` , elimina o membro do objeto que contém. Por exemplo:

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
| 429 | Demasiados pedidos (estrangulados), de acordo com o [iot hub estrangulando](iot-hub-devguide-quotas-throttling.md) |
| 5** | Erros de servidor |

O código python snippet abaixo, demonstra o processo de atualização de propriedades reportadas twin sobre MQTT (usando o cliente Paho MQTT):

```python
from paho.mqtt import client as mqtt

# authenticate the client with IoT Hub (not shown here)

client.subscribe("$iothub/twin/res/#")
rid = "1"
twin_reported_property_patch = "{\"firmware_version\": \"v1.1\"}"
client.publish("$iothub/twin/PATCH/properties/reported/?$rid=" +
               rid, twin_reported_property_patch, qos=0)
```

Após o sucesso da operação de atualização de propriedades duplas acima, a mensagem de publicação do IoT Hub terá o seguinte tópico: , onde está o código de `$iothub/twin/res/204/?$rid=1&$version=6` `204` estado indicando sucesso, corresponde ao ID de pedido fornecido pelo dispositivo `$rid=1` no código, e corresponde à versão `$version` da secção de propriedades reportadas de gémeos dispositivoapós a atualização.

Para mais informações, consulte o [guia do desenvolvedor de gémeos dispositivos.](iot-hub-devguide-device-twins.md)

## <a name="receiving-desired-properties-update-notifications"></a>Receber as notificações de atualização de propriedades desejadas

Quando um dispositivo está ligado, o IoT Hub envia notificações para o tópico `$iothub/twin/PATCH/properties/desired/?$version={new version}` , que contêm o conteúdo da atualização realizada pela extremidade traseira da solução. Por exemplo:

```json
{
    "telemetrySendFrequency": "5m",
    "route": null,
    "$version": 8
}
```

Quanto às atualizações de propriedades, `null` os valores significam que o membro do objeto JSON está a ser eliminado. Note ainda que indica a nova versão da secção de `$version` propriedades desejadas do gémeo.

> [!IMPORTANT]
> O IoT Hub só gera notificações de alteração quando os dispositivos estão ligados. Certifique-se de implementar o fluxo de [reconexão](iot-hub-devguide-device-twins.md#device-reconnection-flow) do dispositivo para manter as propriedades desejadas sincronizadas entre o IoT Hub e a aplicação do dispositivo.

Para mais informações, consulte o [guia do desenvolvedor de gémeos dispositivos.](iot-hub-devguide-device-twins.md)

## <a name="respond-to-a-direct-method"></a>Responder a um método direto

Primeiro, um dispositivo tem de subscrever `$iothub/methods/POST/#` . O IoT Hub envia pedidos de método para o `$iothub/methods/POST/{method name}/?$rid={request id}` tópico, com um JSON válido ou um corpo vazio.

Para responder, o dispositivo envia uma mensagem com um JSON válido ou um corpo vazio para o tópico `$iothub/methods/res/{status}/?$rid={request id}` . Nesta mensagem, o ID de **pedido** deve coincidir com o da mensagem de pedido, e o **estado** deve ser um inteiro.

Para mais informações, consulte [o guia do desenvolvedor de métodos diretos](iot-hub-devguide-direct-methods.md).

## <a name="additional-considerations"></a>Considerações adicionais

Como última consideração, se precisar de personalizar o comportamento do protocolo MQTT no lado da nuvem, deve rever o gateway do [protocolo Azure IoT](iot-hub-protocol-gateway.md). Este software permite-lhe implementar um gateway de protocolo personalizado de alto desempenho que interage diretamente com o IoT Hub. O portal de protocolo Azure IoT permite personalizar o protocolo do dispositivo para acomodar implementações de MQTT de Brownfield ou outros protocolos personalizados. Esta abordagem requer, no entanto, que você execute e opere um gateway de protocolo personalizado.

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre o protocolo MQTT, consulte a [documentação mQTT](https://mqtt.org/documentation).

Para saber mais sobre o planeamento da sua implantação do IoT Hub, consulte:

* [Catálogo de dispositivos Azure Certified for IoT](https://catalog.azureiotsolutions.com/)
* [Apoiar protocolos adicionais](iot-hub-protocol-gateway.md)
* [Compare com hubs de eventos](iot-hub-compare-event-hubs.md)
* [Escala, HA e DR](iot-hub-scaling.md)

Para explorar ainda mais as capacidades do IoT Hub, consulte:

* [Guia de desenvolvimento do IoT Hub](iot-hub-devguide.md)
* [Implementar o AI em dispositivos de ponta com o Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
