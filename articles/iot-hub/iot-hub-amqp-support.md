---
title: Compreender o suporte do Azure IoT Hub AMQP | Documentos da Microsoft
description: Guia do desenvolvedor – suporte para os dispositivos ligados ao IoT Hub os pontos de extremidade voltado para o dispositivo e voltado para o serviço utilizando o protocolo AMQP. Inclui informações sobre incorporada suporte de AMQP no Azure IoT device SDKs.
author: rezasherafat
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/30/2019
ms.author: rezas
ms.openlocfilehash: 703e2c842fb42bad8aa112d84c516a29c2327378
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65473510"
---
# <a name="communicate-with-your-iot-hub-using-the-amqp-protocol"></a>Comunicar com o seu hub IoT com o protocolo AMQP

IoT Hub suporta [AMQP versão 1.0](http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf) fornecer uma variedade de funcionalidades através de pontos finais com acesso de dispositivo e voltado para o serviço. Este documento descreve a utilização de clientes AMQP estabeleça ligação ao IoT Hub para utilizar a funcionalidade do IoT Hub.

## <a name="service-client"></a>Cliente do serviço

### <a name="connection-and-authenticating-to-iot-hub-service-client"></a>Ligação e autenticar para o IoT Hub (cliente de serviço)
Para ligar ao IoT Hub através de AMQP, um cliente pode utilizar o [afirmações com base em segurança (CBS)](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc) ou [autenticação autenticação simples e a camada de segurança (SASL)](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer).

As seguintes informações são necessárias para o cliente de serviço:

| Informações | Value | 
|-------------|--------------|
| Nome do Anfitrião do Hub IoT | `<iot-hub-name>.azure-devices.net` |
| Nome da chave | `service` |
| Chave de acesso | Chave primária ou secundária associado ao serviço |
| Assinatura de acesso partilhado | SAS de curta duração no seguinte formato: `SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}` (pode encontrar o código para gerar esta assinatura [aqui](./iot-hub-devguide-security.md#security-token-structure)).


O trecho de código abaixo utiliza [uAMQP biblioteca em Python](https://github.com/Azure/azure-uamqp-python) para ligar ao hub IoT através de uma ligação de remetente.

```python
import uamqp
import urllib
import time

# Use generate_sas_token implementation available here: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
policy_name = 'service'
access_key = '<primary-or-secondary-key>'
operation = '<operation-link-name>' # e.g., '/messages/devicebound'

username = '{policy_name}@sas.root.{iot_hub_name}'.format(iot_hub_name=iot_hub_name, policy_name=policy_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

# Create a send or receive client
send_client = uamqp.SendClient(uri, debug=True)
receive_client = uamqp.ReceiveClient(uri, debug=True)
```

### <a name="invoking-cloud-to-device-messages-service-client"></a>Invocar mensagens cloud para o dispositivo (cliente de serviço)
A troca de mensagens de cloud-para-dispositivo entre o serviço e o IoT Hub, bem como entre dispositivos e IoT Hub é descrita [aqui](iot-hub-devguide-messages-c2d.md). O cliente do serviço usa duas ligações descritas abaixo para enviar mensagens e receber comentários para mensagens enviadas anteriormente a partir de dispositivos.

| Criado por | Tipo de ligação | Caminho de ligação | Descrição |
|------------|-----------|-----------|-------------|
| Serviço | Ligação de remetente | `/messages/devicebound` | As mensagens de C2D destinadas aos dispositivos são enviadas para esta ligação pelo serviço. As mensagens enviadas por esta ligação tem seus `To` propriedade definida para o caminho de ligação do recetor do dispositivo de destino: ou seja, `/devices/<deviceID>/messages/devicebound`. |
| Serviço | Ligação de recetor | `/messages/serviceBound/feedback` | Conclusão, rejeição e abandonment mensagens de comentários provenientes de dispositivos recebidos nesta ligação pelo serviço. Ver [aqui](./iot-hub-devguide-messages-c2d.md#message-feedback) para obter mais informações sobre mensagens de comentários. |

O trecho de código abaixo demonstra como criar uma mensagem de C2D e enviá-lo para um dispositivo utilizando [uAMQP biblioteca em Python](https://github.com/Azure/azure-uamqp-python).

```python
import uuid
# Create a message and set message property 'To' to the devicebound link on device
msg_id = str(uuid.uuid4())
msg_content = b"Message content goes here!"
device_id = '<device-id>'
to = '/devices/{device_id}/messages/devicebound'.format(device_id=device_id)
ack = 'full' # Alternative values are 'positive', 'negative', and 'none'
app_props = { 'iothub-ack': ack }
msg_props = uamqp.message.MessageProperties(message_id=msg_id, to=to)
msg = uamqp.Message(msg_content, properties=msg_props, application_properties=app_props)

# Send the message using the send client created and connected IoT Hub earlier
send_client.queue_message(msg)
results = send_client.send_all_messages()

# Close the client if not needed
send_client.close()
```

Para receber comentários, o cliente do serviço cria uma ligação de recetor. O trecho de código abaixo demonstra como fazer isso usando [uAMQP biblioteca em Python](https://github.com/Azure/azure-uamqp-python).

```python
import json

operation = '/messages/serviceBound/feedback'

# ...
# Recreate the URI using the feedback path above and authenticate
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

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

Conforme mostrado acima, uma mensagem de comentários C2D tem o tipo de conteúdo de `application/vnd.microsoft.iothub.feedback.json` e propriedades em seu corpo JSON podem ser utilizadas para inferir o status de entrega da mensagem original:
* Chave `statusCode` comentários corpo apresenta qualquer um dos seguintes valores: `['Success', 'Expired', 'DeliveryCountExceeded', 'Rejected', 'Purged']`.
* Chave `deviceId` comentários corpo tem o ID de dispositivo de destino.
* Chave `originalMessageId` comentários corpo tem o ID da mensagem original de C2D enviado pelo serviço. Isso pode ser utilizado para correlacionar os comentários para C2D mensagens.

### <a name="receive-telemetry-messages-service-client"></a>Receber mensagens de telemetria (cliente de serviço)


### <a name="additional-notes"></a>Notas adicionais
* As ligações AMQP podem ser interrompidas devido à falha ou expiração do token de autenticação (que é gerado no código) de rede. O cliente do serviço tem de lidar com essas circunstâncias e voltar a estabelecer a ligação e ligações se for necessário. Para o caso de expiração do token de autenticação, o cliente também proativamente pode renovar o token antes de expirar para evitar uma queda de conexão.
* Em alguns casos, o seu cliente tem de ser capaz de lidar corretamente com os redirecionamentos de ligação. Consulte a documentação do cliente AMQP sobre como fazer isso.

### <a name="receive-cloud-to-device-messages-device-and-module-client"></a>Receber mensagens da cloud para o dispositivo (cliente de dispositivo e o módulo)
Links AMQP usados no lado do dispositivo são os seguintes:

| Criado por | Tipo de ligação | Caminho de ligação | Descrição |
|------------|-----------|-----------|-------------|
| Dispositivos | Ligação de recetor | `/devices/<deviceID>/messages/devicebound` | As mensagens de C2D destinadas aos dispositivos são recebidas nesta ligação por cada dispositivo de destino. |
| Dispositivos | Ligação de remetente | `/messages/serviceBound/feedback` | Comentários de mensagem de C2D enviados ao serviço através desta ligação pelos dispositivos. |
| Módulos | Ligação de recetor | `/devices/<deviceID>/modules/<moduleID>/messages/devicebound` | As mensagens de C2D destinadas aos módulos são recebidas nesta ligação por cada módulo de destino. |
| Módulos | Ligação de remetente | `/messages/serviceBound/feedback` | Comentários de mensagem de C2D enviados ao serviço através desta ligação por módulos. |


## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre o protocolo AMQP, consulte a [especificação do AMQP v1.0](http://www.amqp.org/sites/amqp.org/files/amqp.pdf).

Para saber mais sobre o IoT Hub de mensagens, consulte:

* [Mensagens da cloud para dispositivo](./iot-hub-devguide-messages-c2d.md)
* [Suporte para protocolos adicionais](iot-hub-protocol-gateway.md)
* [Suporte para o protocolo MQTT](./iot-hub-mqtt-support.md)
