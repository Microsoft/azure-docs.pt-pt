---
title: Compreenda o suporte amQP do Hub Azure IoT / Microsoft Docs
description: Guia do desenvolvedor - suporte para dispositivos que se liguem aos pontos finais virados para dispositivos IoT Hub e virados para o serviço utilizando o Protocolo AMQP. Inclui informações sobre suporte AMQP incorporado no dispositivo Azure IoT SDKs.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/30/2019
ms.author: robinsh
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 7b3dcfc51df7f0fe4291e9c5babccc1444ad32e9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "81730760"
---
# <a name="communicate-with-your-iot-hub-by-using-the-amqp-protocol"></a>Comunique com o seu hub IoT utilizando o Protocolo AMQP

O Azure IoT Hub suporta [a versão 1.0 do OASIS Advanced Message Queuing Protocol (AMQP)](https://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf) para fornecer uma variedade de funcionalidades através de pontos finais voltados para dispositivos e virados para o serviço. Este documento descreve o uso de clientes AMQP para se conectarem a um hub IoT para usar a funcionalidade IoT Hub.

## <a name="service-client"></a>Cliente de serviço

### <a name="connect-and-authenticate-to-an-iot-hub-service-client"></a>Conecte-se e autença-se com um hub IoT (cliente de serviço)

Para se conectar a um hub IoT utilizando amQP, um cliente pode utilizar a autenticação [de segurança baseada em sinistros (CBS)](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc) ou simples [autenticação e camada de segurança (SASL).](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer)

São necessárias as seguintes informações para o cliente de serviço:

| Informações | Valor |
|-------------|--------------|
| Nome anfitrião do hub IoT | `<iot-hub-name>.azure-devices.net` |
| Nome da chave | `service` |
| Chave de acesso | Uma chave primária ou secundária que está associada ao serviço |
| Assinatura de acesso partilhado | Uma assinatura de acesso partilhado de curta duração no seguinte formato: `SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}` . Para obter o código para gerar esta assinatura, consulte [o Control access to IoT Hub](./iot-hub-devguide-security.md#security-token-structure).

O seguinte código snippet utiliza a [biblioteca uAMQP em Python](https://github.com/Azure/azure-uamqp-python) para ligar a um hub IoT através de uma ligação de remetente.

```python
import uamqp
import urllib
import time

# Use generate_sas_token implementation available here:
# https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
policy_name = 'service'
access_key = '<primary-or-secondary-key>'
operation = '<operation-link-name>'  # example: '/messages/devicebound'

username = '{policy_name}@sas.root.{iot_hub_name}'.format(
    iot_hub_name=iot_hub_name, policy_name=policy_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

# Create a send or receive client
send_client = uamqp.SendClient(uri, debug=True)
receive_client = uamqp.ReceiveClient(uri, debug=True)
```

### <a name="invoke-cloud-to-device-messages-service-client"></a>Invocar mensagens nuvem-dispositivo (cliente de serviço)

Para saber mais sobre a troca de mensagens nuvem-para-dispositivo entre o serviço e o hub IoT e entre o dispositivo e o hub IoT, consulte [Enviar mensagens nuvem-a-dispositivo do seu hub IoT](iot-hub-devguide-messages-c2d.md). O cliente de serviço utiliza dois links para enviar mensagens e receber feedback para mensagens previamente enviadas a partir de dispositivos, conforme descrito na tabela seguinte:

| Criado por | Tipo de ligação | Caminho de ligação | Descrição |
|------------|-----------|-----------|-------------|
| Serviço | Ligação remetente | `/messages/devicebound` | As mensagens cloud-to-device que se destinam a dispositivos são enviadas para este link pelo serviço. As mensagens enviadas através deste link têm a sua `To` propriedade definida para o caminho de ligação do recetor do dispositivo alvo, `/devices/<deviceID>/messages/devicebound` . |
| Serviço | Ligação do recetor | `/messages/serviceBound/feedback` | Conclusão, rejeição e abandono de mensagens de feedback que vêm de dispositivos recebidos neste link por serviço. Para obter mais informações sobre mensagens de feedback, consulte [Enviar mensagens cloud-to-device a partir de um hub IoT](./iot-hub-devguide-messages-c2d.md#message-feedback). |

O seguinte corte de código demonstra como criar uma mensagem nuvem-a-dispositivo e enviá-la para um dispositivo utilizando a [biblioteca uAMQP em Python](https://github.com/Azure/azure-uamqp-python).

```python
import uuid
# Create a message and set message property 'To' to the device-bound link on device
msg_id = str(uuid.uuid4())
msg_content = b"Message content goes here!"
device_id = '<device-id>'
to = '/devices/{device_id}/messages/devicebound'.format(device_id=device_id)
ack = 'full'  # Alternative values are 'positive', 'negative', and 'none'
app_props = {'iothub-ack': ack}
msg_props = uamqp.message.MessageProperties(message_id=msg_id, to=to)
msg = uamqp.Message(msg_content, properties=msg_props,
                    application_properties=app_props)

# Send the message by using the send client that you created and connected to the IoT hub earlier
send_client.queue_message(msg)
results = send_client.send_all_messages()

# Close the client if it's not needed
send_client.close()
```

Para receber feedback, o cliente de serviço cria um link recetor. O seguinte corte de código demonstra como criar um link utilizando a [biblioteca uAMQP em Python:](https://github.com/Azure/azure-uamqp-python)

```python
import json

operation = '/messages/serviceBound/feedback'

# ...
# Re-create the URI by using the preceding feedback path and authenticate it
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
batch = receive_client.receive_message_batch(max_batch_size=10)
for msg in batch:
    print('received a message')
    # Check content_type in message property to identify feedback messages coming from device
    if msg.properties.content_type == 'application/vnd.microsoft.iothub.feedback.json':
        msg_body_raw = msg.get_data()
        msg_body_str = ''.join(msg_body_raw)
        msg_body = json.loads(msg_body_str)
        print(json.dumps(msg_body, indent=2))
        print('******************')
        for feedback in msg_body:
            print('feedback received')
            print('\tstatusCode: ' + str(feedback['statusCode']))
            print('\toriginalMessageId: ' + str(feedback['originalMessageId']))
            print('\tdeviceId: ' + str(feedback['deviceId']))
            print
    else:
        print('unknown message:', msg.properties.content_type)
```

Como mostrado no código anterior, uma mensagem de feedback nuvem-a-dispositivo tem um tipo de *aplicação/vnd.microsoft.iothub.feedback.jsde*conteúdo ligado . Pode utilizar as propriedades no corpo JSON da mensagem para inferir o estado de entrega da mensagem original:

* A chave `statusCode` no corpo de feedback tem um dos seguintes valores: *Sucesso,* *Expirado,* *DeliveryCountExceeded,* *Rejeitado,* ou *Purgado*.

* A chave `deviceId` no corpo de feedback tem a identificação do dispositivo alvo.

* A chave `originalMessageId` no corpo de feedback tem o ID da mensagem original nuvem-para-dispositivo que foi enviada pelo serviço. Pode utilizar este estado de entrega para correlacionar o feedback com mensagens nuvem-dispositivo.

### <a name="receive-telemetry-messages-service-client"></a>Receber mensagens de telemetria (cliente de serviço)

Por padrão, o seu hub IoT armazena mensagens de telemetria de dispositivos ingeridos num centro de eventos incorporado. O seu cliente de serviço pode utilizar o Protocolo AMQP para receber os eventos armazenados.

Para o efeito, o cliente de serviço precisa primeiro de se ligar ao ponto final do hub IoT e receber um endereço de redirecionamento para os centros de eventos incorporados. O cliente de serviço utiliza então o endereço fornecido para ligar ao centro de eventos incorporado.

Em cada passo, o cliente necessita de apresentar as seguintes informações:

* Credenciais de serviço válidas (ficha de assinatura de acesso partilhado de serviço).

* Um caminho bem formatado para a partição do grupo de consumidores de que pretende recuperar mensagens. Para um determinado grupo de consumidores e ID de partição, o caminho tem o seguinte formato: `/messages/events/ConsumerGroups/<consumer_group>/Partitions/<partition_id>` (o grupo de consumidores predefinidos é `$Default` ).

* Um predicado de filtragem opcional para designar um ponto de partida na partição. Este predicado pode ser na forma de um número de sequência, offset ou tempotamp enfecado.

O seguinte código snippet utiliza a [biblioteca uAMQP em Python](https://github.com/Azure/azure-uamqp-python) para demonstrar os passos anteriores:

```python
import json
import uamqp
import urllib
import time

# Use the generate_sas_token implementation that's available here: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
policy_name = 'service'
access_key = '<primary-or-secondary-key>'
operation = '/messages/events/ConsumerGroups/{consumer_group}/Partitions/{p_id}'.format(
    consumer_group='$Default', p_id=0)

username = '{policy_name}@sas.root.{iot_hub_name}'.format(
    policy_name=policy_name, iot_hub_name=iot_hub_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

# Optional filtering predicates can be specified by using endpoint_filter
# Valid predicates include:
# - amqp.annotation.x-opt-sequence-number
# - amqp.annotation.x-opt-offset
# - amqp.annotation.x-opt-enqueued-time
# Set endpoint_filter variable to None if no filter is needed
endpoint_filter = b'amqp.annotation.x-opt-sequence-number > 2995'

# Helper function to set the filtering predicate on the source URI


def set_endpoint_filter(uri, endpoint_filter=''):
    source_uri = uamqp.address.Source(uri)
    source_uri.set_filter(endpoint_filter)
    return source_uri


receive_client = uamqp.ReceiveClient(
    set_endpoint_filter(uri, endpoint_filter), debug=True)
try:
    batch = receive_client.receive_message_batch(max_batch_size=5)
except uamqp.errors.LinkRedirect as redirect:
    # Once a redirect error is received, close the original client and recreate a new one to the re-directed address
    receive_client.close()

    sas_auth = uamqp.authentication.SASTokenAuth.from_shared_access_key(
        redirect.address, policy_name, access_key)
    receive_client = uamqp.ReceiveClient(set_endpoint_filter(
        redirect.address, endpoint_filter), auth=sas_auth, debug=True)

# Start receiving messages in batches
batch = receive_client.receive_message_batch(max_batch_size=5)
for msg in batch:
    print('*** received a message ***')
    print(''.join(msg.get_data()))
    print('\t: ' + str(msg.annotations['x-opt-sequence-number']))
    print('\t: ' + str(msg.annotations['x-opt-offset']))
    print('\t: ' + str(msg.annotations['x-opt-enqueued-time']))
```

Para um determinado dispositivo ID, o hub IoT utiliza um haxixe do ID do dispositivo para determinar em que partição armazenar as suas mensagens. O corte de código anterior demonstra como os eventos são recebidos de uma única divisão. No entanto, note que uma aplicação típica muitas vezes precisa de recuperar eventos que são armazenados em todas as divisórias do centro de eventos.

## <a name="device-client"></a>Cliente do dispositivo

### <a name="connect-and-authenticate-to-an-iot-hub-device-client"></a>Conecte-se e autença-se com um hub IoT (cliente do dispositivo)

Para se ligar a um hub IoT utilizando amQP, um dispositivo pode utilizar a [autenticação baseada em sinistros (CBS)](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc) ou simples [autenticação e camada de segurança (SASL).](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer)

São necessárias as seguintes informações para o cliente do dispositivo:

| Informações | Valor |
|-------------|--------------|
| Nome anfitrião do hub IoT | `<iot-hub-name>.azure-devices.net` |
| Chave de acesso | Uma chave primária ou secundária que está associada ao dispositivo |
| Assinatura de acesso partilhado | Uma assinatura de acesso partilhado de curta duração no seguinte formato: `SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}` . Para obter o código para gerar esta assinatura, consulte [o Control access to IoT Hub](./iot-hub-devguide-security.md#security-token-structure).

O seguinte código snippet utiliza a [biblioteca uAMQP em Python](https://github.com/Azure/azure-uamqp-python) para ligar a um hub IoT através de uma ligação de remetente.

```python
import uamqp
import urllib
import uuid

# Use generate_sas_token implementation available here:
# https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
device_id = '<device-id>'
access_key = '<primary-or-secondary-key>'
username = '{device_id}@sas.{iot_hub_name}'.format(
    device_id=device_id, iot_hub_name=iot_hub_name)
sas_token = generate_sas_token('{hostname}/devices/{device_id}'.format(
    hostname=hostname, device_id=device_id), access_key, None)

# e.g., '/devices/{device_id}/messages/devicebound'
operation = '<operation-link-name>'
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
send_client = uamqp.SendClient(uri, debug=True)
```

Os seguintes caminhos de ligação são suportados como operações do dispositivo:

| Criado por | Tipo de ligação | Caminho de ligação | Descrição |
|------------|-----------|-----------|-------------|
| Dispositivos | Ligação do recetor | `/devices/<deviceID>/messages/devicebound` | As mensagens cloud-to-device que se destinam a dispositivos são recebidas nesta ligação por cada dispositivo de destino. |
| Dispositivos | Ligação remetente | `/devices/<deviceID>/messages/events` | As mensagens dispositivo-nuvem que são enviadas a partir de um dispositivo são enviadas por este link. |
| Dispositivos | Ligação remetente | `/messages/serviceBound/feedback` | Feedback de mensagem nuvem-para-dispositivo enviado ao serviço através deste link por dispositivos. |

### <a name="receive-cloud-to-device-commands-device-client"></a>Receber comandos nuvem-a-dispositivo (cliente do dispositivo)

Os comandos nuvem-dispositivo que são enviados para os dispositivos chegam a um `/devices/<deviceID>/messages/devicebound` link. Os dispositivos podem receber estas mensagens em lotes e utilizar a carga útil de dados de mensagens, propriedades de mensagens, anotações ou propriedades de aplicação na mensagem, conforme necessário.

O seguinte código snippet utiliza a [biblioteca uAMQP em Python](https://github.com/Azure/azure-uamqp-python)) para receber mensagens nuvem-a-dispositivo por um dispositivo.

```python
# ...
# Create a receive client for the cloud-to-device receive link on the device
operation = '/devices/{device_id}/messages/devicebound'.format(
    device_id=device_id)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
while True:
    batch = receive_client.receive_message_batch(max_batch_size=5)
    for msg in batch:
        print('*** received a message ***')
        print(''.join(msg.get_data()))

        # Property 'to' is set to: '/devices/device1/messages/devicebound',
        print('\tto:                     ' + str(msg.properties.to))

        # Property 'message_id' is set to value provided by the service
        print('\tmessage_id:             ' + str(msg.properties.message_id))

        # Other properties are present if they were provided by the service
        print('\tcreation_time:          ' + str(msg.properties.creation_time))
        print('\tcorrelation_id:         ' +
              str(msg.properties.correlation_id))
        print('\tcontent_type:           ' + str(msg.properties.content_type))
        print('\treply_to_group_id:      ' +
              str(msg.properties.reply_to_group_id))
        print('\tsubject:                ' + str(msg.properties.subject))
        print('\tuser_id:                ' + str(msg.properties.user_id))
        print('\tgroup_sequence:         ' +
              str(msg.properties.group_sequence))
        print('\tcontent_encoding:       ' +
              str(msg.properties.content_encoding))
        print('\treply_to:               ' + str(msg.properties.reply_to))
        print('\tabsolute_expiry_time:   ' +
              str(msg.properties.absolute_expiry_time))
        print('\tgroup_id:               ' + str(msg.properties.group_id))

        # Message sequence number in the built-in event hub
        print('\tx-opt-sequence-number:  ' +
              str(msg.annotations['x-opt-sequence-number']))
```

### <a name="send-telemetry-messages-device-client"></a>Enviar mensagens de telemetria (cliente do dispositivo)

Também pode enviar mensagens de telemetria a partir de um dispositivo utilizando AMQP. O dispositivo pode opcionalmente fornecer um dicionário de propriedades de aplicações, ou várias propriedades de mensagens, como iD de mensagem.

O seguinte código snippet utiliza a [biblioteca uAMQP em Python](https://github.com/Azure/azure-uamqp-python) para enviar mensagens dispositivo-a-nuvem a partir de um dispositivo.

```python
# ...
# Create a send client for the device-to-cloud send link on the device
operation = '/devices/{device_id}/messages/events'.format(device_id=device_id)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

send_client = uamqp.SendClient(uri, debug=True)

# Set any of the applicable message properties
msg_props = uamqp.message.MessageProperties()
msg_props.message_id = str(uuid.uuid4())
msg_props.creation_time = None
msg_props.correlation_id = None
msg_props.content_type = None
msg_props.reply_to_group_id = None
msg_props.subject = None
msg_props.user_id = None
msg_props.group_sequence = None
msg_props.to = None
msg_props.content_encoding = None
msg_props.reply_to = None
msg_props.absolute_expiry_time = None
msg_props.group_id = None

# Application properties in the message (if any)
application_properties = { "app_property_key": "app_property_value" }

# Create message
msg_data = b"Your message payload goes here"
message = uamqp.Message(msg_data, properties=msg_props, application_properties=application_properties)

send_client.queue_message(message)
results = send_client.send_all_messages()

for result in results:
    if result == uamqp.constants.MessageState.SendFailed:
        print result
```

## <a name="additional-notes"></a>Notas adicionais

* As ligações AMQP podem ser interrompidas devido a uma falha de rede ou à expiração do token de autenticação (gerado no código). O cliente de serviço deve lidar com estas circunstâncias e restabelecer a ligação e links, se necessário. Se um símbolo de autenticação expirar, o cliente pode evitar uma queda de ligação renovando proativamente o token antes da sua expiração.

* O seu cliente deve ocasionalmente ser capaz de lidar corretamente com as reorientações de ligação. Para entender tal operação, consulte a documentação do seu cliente AMQP.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o Protocolo AMQP, consulte a [especificação AMQP v1.0](https://www.amqp.org/sites/amqp.org/files/amqp.pdf).

Para saber mais sobre mensagens IoT Hub, consulte:

* [Mensagens nuvem-para-dispositivo](./iot-hub-devguide-messages-c2d.md)
* [Apoio a protocolos adicionais](iot-hub-protocol-gateway.md)
* [Apoio ao Protocolo de Transporte de Telemetria de Fila de Mensagens (MQTT)](./iot-hub-mqtt-support.md)
