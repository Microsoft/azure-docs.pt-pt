---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 10/20/2020
ms.openlocfilehash: a925c3a17988ef6f4b95a1e3cf4dd5fb8baa4829
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "102510824"
---
Estão também disponíveis os seguintes recursos:

- [Documentação de referência do SDK de Python](/python/api/azure-iot-hub/azure.iot.hub)
- [Amostras de clientes de serviço](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-hub/samples/iothub_registry_manager_method_sample.py)
- [Amostras de Gémeos Digitais](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-hub/samples/get_digital_twin_sample.py)

## <a name="iot-hub-service-client-examples"></a>Exemplos de clientes de serviço IoT Hub

Esta secção mostra exemplos python usando o cliente de serviço IoT Hub e as classes **IoTHubRegistryManager** e **CloudToDeviceMethod.** Utiliza-se a classe **IoTHubRegistryManager** para interagir com o estado do dispositivo utilizando gémeos do dispositivo. Também pode utilizar a classe **IoTHubRegistryManager** para [consultar registos de dispositivos](../articles/iot-hub/iot-hub-devguide-query-language.md) no seu IoT Hub. Utilize a classe **CloudToDeviceMethod** para chamar comandos no dispositivo. O modelo [DTDL](../articles/iot-pnp/concepts-digital-twin.md) para o dispositivo define as propriedades e comanda os instrumentos do dispositivo. Nos fragmentos de código, a `device_id` variável detém o ID do dispositivo IoT Plug and Play registado no seu hub IoT.

### <a name="get-the-device-twin-and-model-id"></a>Obtenha o dispositivo twin e model ID

Para obter o iD duplo e modelo do dispositivo IoT Plug and Play que está ligado ao seu hub IoT:

```python
from azure.iot.hub import IoTHubRegistryManager
from azure.iot.hub.models import Twin, TwinProperties

iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

# ...

twin = iothub_registry_manager.get_twin(device_id)
print("The device twin is: ")
print("")
print(twin)
print("")

additional_props = twin.additional_properties
if "modelId" in additional_props:
    print("The Model ID for this device is:")
    print(additional_props["modelId"])
    print("")
```

### <a name="update-device-twin"></a>Atualização do dispositivo twin

O seguinte código snippet mostra como atualizar a `targetTemperature` propriedade em um dispositivo. A amostra mostra como precisa de obter o gémeo antes de `etag` atualizá-lo. A propriedade é definida no componente predefinido do dispositivo:

```python
iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

twin = iothub_registry_manager.get_twin(device_id)

twin_patch = Twin()

twin_patch.properties = TwinProperties(
    desired={"targetTemperature": 42}
)
updated_twin = iothub_registry_manager.update_twin(device_id, twin_patch, twin.etag)
```

O seguinte corte mostra como atualizar a `targetTemperature` propriedade em um componente. A amostra mostra como precisa de obter o gémeo antes de `ETag` atualizá-lo. A propriedade é definida no componente **termóstato1:**

```python
iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

twin = iothub_registry_manager.get_twin(device_id)

twin_patch = Twin()

twin_patch.properties = TwinProperties(
    desired={ "thermostat1": {
        "__t": "c",
        "targetTemperature": 42}
    }
)
updated_twin = iothub_registry_manager.update_twin(device_id, twin_patch, twin.etag)
```

Para uma propriedade num componente, o patch de propriedade parece o seguinte exemplo:

```json
{
"thermostat1":
  {
    "__t": "c",
    "targetTemperature": 20
  }
}
```

### <a name="call-command"></a>Chamar comando

O seguinte corte mostra como invocar o `getMaxMinReport` comando definido num componente predefinido:

```python
from azure.iot.hub import IoTHubRegistryManager
from azure.iot.hub.models import CloudToDeviceMethod

# ...

iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

method_payload = datetime.datetime.now() - datetime.timedelta(minutes=2)
device_method = CloudToDeviceMethod(method_name="getMaxMinReport", payload=method_payload)
result = iothub_registry_manager.invoke_device_method(device_id, device_method)
print(result.payload)
```

O seguinte corte mostra como chamar o `getMaxMinReport` comando de um componente. O comando é definido no componente **termostato1:**

```python
from azure.iot.hub import IoTHubRegistryManager
from azure.iot.hub.models import CloudToDeviceMethod

# ...

iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

method_payload = datetime.datetime.now() - datetime.timedelta(minutes=2)
device_method = CloudToDeviceMethod(method_name="thermostat1*getMaxMinReport", payload=method_payload)
result = iothub_registry_manager.invoke_device_method(device_id, device_method)
print(result.payload)
```

## <a name="iot-hub-digital-twin-examples"></a>Exemplos gémeos digitais IoT Hub

Utiliza-se a classe **DigitalTwinClient** para interagir com o estado do dispositivo utilizando gémeos digitais. O modelo [DTDL](../articles/iot-pnp/concepts-digital-twin.md) para o dispositivo define as propriedades e comanda os instrumentos do dispositivo.

A `device_id` variável detém o ID do dispositivo IoT Plug and Play registado no seu hub IoT.

### <a name="get-the-digital-twin-and-model-id"></a>Obtenha o twin digital e o ID do modelo

Para obter o twin digital e o iD do ioT Plug and Play que se conecta ao seu hub IoT:

```python
from azure.iot.hub import DigitalTwinClient

digital_twin_client = DigitalTwinClient(iothub_connection_str)

digital_twin = digital_twin_client.get_digital_twin(device_id)
if digital_twin:
    print(digital_twin)
    print("Model Id: " + digital_twin["$metadata"]["$model"])
else:
    print("No digital_twin found")
```

### <a name="update-digital-twin"></a>Atualizar twin digital

O seguinte código snippet mostra como atualizar a `targetTemperature` propriedade em um dispositivo. A propriedade é definida no componente predefinido do dispositivo:

```python
from azure.iot.hub import DigitalTwinClient

digital_twin_client = DigitalTwinClient(iothub_connection_str)

patch = [{"op": "add", "path": "/targetTemperature", "value": 42}]
digital_twin_client.update_digital_twin(device_id, patch)
```

O seguinte corte mostra como atualizar a `targetTemperature` propriedade em um componente. A propriedade é definida no componente **termóstato1:**

```python
from azure.iot.hub import DigitalTwinClient

digital_twin_client = DigitalTwinClient(iothub_connection_str)

patch = [{"op": "add", "path": "/targetTemperature", "value": 42}]
digital_twin_client.update_digital_twin(device_id, patch)
```

### <a name="call-command"></a>Chamar comando

O seguinte corte mostra como invocar o `getMaxMinReport` comando definido num componente predefinido:

```python
from azure.iot.hub import DigitalTwinClient

payload = datetime.datetime.now() - datetime.timedelta(minutes=2)

connect_timeout_in_seconds = 3
response_timeout_in_seconds = 7


digital_twin_client = DigitalTwinClient(iothub_connection_str)

invoke_command_result = digital_twin_client.invoke_command(
    device_id, "getMaxMinReport", payload, connect_timeout_in_seconds, response_timeout_in_seconds
)
if invoke_command_result:
    print(invoke_command_result)
else:
    print("No invoke_command_result found")
```

O seguinte corte mostra como chamar o `getMaxMinReport` comando de um componente. O comando é definido no componente **termostato1:**

```python
from azure.iot.hub import DigitalTwinClient

payload = datetime.datetime.now() - datetime.timedelta(minutes=2)

connect_timeout_in_seconds = 3
response_timeout_in_seconds = 7


digital_twin_client = DigitalTwinClient(iothub_connection_str)

invoke_command_result = digital_twin_client.invoke_component_command(
    device_id, "thermostat1", "getMaxMinReport", payload, connect_timeout_in_seconds, response_timeout_in_seconds
)
if invoke_command_result:
    print(invoke_command_result)
else:
    print("No invoke_command_result found")
```

## <a name="read-device-telemetry"></a>Ler telemetria do dispositivo

Os dispositivos IoT Plug e Play enviam a telemetria definida no modelo DTDL para o IoT Hub. Por padrão, o IoT Hub encaminha a telemetria para um ponto final do Event Hubs onde pode consumi-la. Para saber mais, consulte [o encaminhamento de mensagens IoT Hub para enviar mensagens dispositivo-a-nuvem para diferentes pontos finais](../articles/iot-hub/iot-hub-devguide-messages-d2c.md).

O seguinte corte de código mostra como ler a telemetria a partir do ponto final padrão do Event Hubs. O código deste corte é retirado do quickstart IoT Hub [Enviar telemetria de um dispositivo para um hub IoT e lê-lo com uma aplicação de back-end](../articles/iot-hub/quickstart-send-telemetry-python.md):

```python
import asyncio
from azure.eventhub import TransportType
from azure.eventhub.aio import EventHubConsumerClient

# Define callbacks to process events
async def on_event_batch(partition_context, events):
    for event in events:
        print("Received event from partition: {}.".format(partition_context.partition_id))
        print("Telemetry received: ", event.body_as_str())
        print("Properties (set by device): ", event.properties)
        print("System properties (set by IoT Hub): ", event.system_properties)
        print()
    await partition_context.update_checkpoint()

async def on_error(partition_context, error):
    # ...

loop = asyncio.get_event_loop()
client = EventHubConsumerClient.from_connection_string(
    conn_str=CONNECTION_STR,
    consumer_group="$default",
)

try:
    loop.run_until_complete(client.receive_batch(on_event_batch=on_event_batch, on_error=on_error))
except KeyboardInterrupt:
    print("Receiving has stopped.")
finally:
    loop.run_until_complete(client.close())
    loop.stop()
```

A saída a seguir do código anterior mostra a telemetria de temperatura enviada pelo dispositivo **Thermostat** IoT Plug and Play que só tem o componente predefinido. A `dt-dataschema` propriedade do sistema mostra o ID do modelo:

```cmd/sh
Received event from partition: 1.
Telemetry received:  {"temperature": 12}
Properties (set by device):  None
System properties (set by IoT Hub):  {b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-connection-auth-method': b'{"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}', b'iothub-connection-auth-generation-id': b'637388855582764406', b'iothub-enqueuedtime': 1603288810715, b'iothub-message-source': b'Telemetry', b'dt-dataschema': b'dtmi:com:example:Thermostat;1', b'x-opt-sequence-number': 13280, b'x-opt-offset': b'12890070640', b'x-opt-enqueued-time': 1603288810824}
```

A seguinte saída do código anterior mostra a telemetria de temperatura enviada pelo dispositivo IoT Plug and Play **do Controlador de Temperatura** multi-componente. A `dt-subject` propriedade do sistema mostra o nome do componente que enviou a telemetria. Neste exemplo, os dois componentes são `thermostat1` e `thermostat2` tal como definidos no modelo DTDL. A `dt-dataschema` propriedade do sistema mostra o ID do modelo:

```cmd/sh
Received event from partition: 1.
Telemetry received:  {"temperature": 45}
Properties (set by device):  None
System properties (set by IoT Hub):  {b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-connection-auth-method': b'{"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}', b'iothub-connection-auth-generation-id': b'637388858939631652', b'iothub-enqueuedtime': 1603289127844, b'iothub-message-source': b'Telemetry', b'dt-subject': b'thermostat1', b'dt-dataschema': b'dtmi:com:example:TemperatureController;1', b'x-opt-sequence-number': 13328, b'x-opt-offset': b'12890095440', b'x-opt-enqueued-time': 1603289128001}

Received event from partition: 1.
Telemetry received:  {"temperature": 49}
Properties (set by device):  None
System properties (set by IoT Hub):  {b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-connection-auth-method': b'{"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}', b'iothub-connection-auth-generation-id': b'637388858939631652', b'iothub-enqueuedtime': 1603289133017, b'iothub-message-source': b'Telemetry', b'dt-subject': b'thermostat2', b'dt-dataschema': b'dtmi:com:example:TemperatureController;1', b'x-opt-sequence-number': 13329, b'x-opt-offset': b'12890095928', b'x-opt-enqueued-time': 1603289133173}
```

## <a name="read-device-twin-change-notifications"></a>Leia notificações de alteração dupla do dispositivo

Pode configurar o IoT Hub para gerar notificações de alterações gémeas do dispositivo para encaminhar para um ponto final suportado. Para saber mais, consulte [o encaminhamento de mensagens IoT Hub para enviar mensagens dispositivo-a-nuvem para diferentes pontos finais > eventos de não telemetria](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

O código mostrado no corte de código Python anterior gera a seguinte saída quando o IoT Hub gera notificações de alteração dupla do dispositivo para um dispositivo termóstato sem componente. As propriedades da aplicação `iothub-message-schema` e `opType` dar-lhe informações sobre o tipo de notificação de alteração:

```cmd/sh
Received event from partition: 1.
Telemetry received:  {"version":3,"properties":{"reported":{"maxTempSinceLastReboot":10.96,"$metadata":{"$lastUpdated":"2020-10-21T14:10:42.4171263Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-21T14:10:42.4171263Z"}},"$version":2}}}
Properties (set by device):  {b'hubName': b'my-pnp-hub', b'deviceId': b'my-pnp-device', b'operationTimestamp': b'2020-10-21T14:10:42.4171263+00:00', b'iothub-message-schema': b'twinChangeNotification', b'opType': b'updateTwin'}
System properties (set by IoT Hub):  {b'user-id': b'my-pnp-hub\x81\x0e\xa4\x7f', b'correlation-id': b'12104ced5402', b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-enqueuedtime': 1603289442519, b'iothub-message-source': b'twinChangeEvents', b'x-opt-sequence-number': 13332, b'x-opt-offset': b'12890097392', b'x-opt-enqueued-time': 1603289442738}
```

O código mostrado no corte de código Python anterior gera a seguinte saída quando o IoT Hub gera notificações de alteração dupla do dispositivo para um dispositivo com componentes. Este exemplo mostra a saída quando um dispositivo sensor de temperatura com um componente do termóstato gera notificações. As propriedades da aplicação `iothub-message-schema` e `opType` dar-lhe informações sobre o tipo de notificação de alteração:

```cmd/sh
Received event from partition: 1.
Telemetry received:  {"version":4,"properties":{"reported":{"thermostat1":{"maxTempSinceLastReboot":98.34,"__t":"c"},"$metadata":{"$lastUpdated":"2020-10-21T14:13:39.36491Z","thermostat1":{"$lastUpdated":"2020-10-21T14:13:39.36491Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-21T14:13:39.36491Z"},"__t":{"$lastUpdated":"2020-10-21T14:13:39.36491Z"}}},"$version":3}}}
Properties (set by device):  {b'hubName': b'my-pnp-hub', b'deviceId': b'my-pnp-device', b'operationTimestamp': b'2020-10-21T14:13:39.3649100+00:00', b'iothub-message-schema': b'twinChangeNotification', b'opType': b'updateTwin'}
System properties (set by IoT Hub):  {b'user-id': b'my-pnp-hub', b'correlation-id': b'1210b664ab83', b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-enqueuedtime': 1603289619481, b'iothub-message-source': b'twinChangeEvents', b'x-opt-sequence-number': 13341, b'x-opt-offset': b'12890102216', b'x-opt-enqueued-time': 1603289619668}
```

## <a name="read-digital-twin-change-notifications"></a>Leia notificações digitais de mudança de gémeos

Pode configurar o IoT Hub para gerar notificações digitais de alteração dupla para encaminhar para um ponto final suportado. Para saber mais, consulte [o encaminhamento de mensagens IoT Hub para enviar mensagens dispositivo-a-nuvem para diferentes pontos finais > eventos de não telemetria](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

O código mostrado no corte de código Python anterior gera a seguinte saída quando o IoT Hub gera notificações digitais de alteração dupla para um dispositivo termóstato sem componente. As propriedades da aplicação `iothub-message-schema` e `opType` dar-lhe informações sobre o tipo de notificação de alteração:

```cmd/sh
Received event from partition: 1.
Telemetry received:  [{"op":"add","path":"/$metadata/maxTempSinceLastReboot","value":{"lastUpdateTime":"2020-10-21T14:10:42.4171263Z"}},{"op":"add","path":"/maxTempSinceLastReboot","value":10.96}]
Properties (set by device):  {b'hubName': b'my-pnp-hub', b'deviceId': b'my-pnp-device', b'operationTimestamp': b'2020-10-21T14:10:42.4171263+00:00', b'iothub-message-schema': b'digitalTwinChangeNotification', b'opType': b'updateTwin'}
System properties (set by IoT Hub):  {b'user-id': b'my-pnp-hub\x81\x0e\xa4\x7f', b'correlation-id': b'12104ced5402', b'content-type': b'application/json-patch+json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-enqueuedtime': 1603289442519, b'iothub-message-source': b'digitalTwinChangeEvents', b'x-opt-sequence-number': 13333, b'x-opt-offset': b'12890098024', b'x-opt-enqueued-time': 1603289442738}
```

O código mostrado no corte de código Python anterior gera a seguinte saída quando o IoT Hub gera notificações digitais de mudança de gémeos para um dispositivo com componentes. Este exemplo mostra a saída quando um dispositivo sensor de temperatura com um componente do termóstato gera notificações. As propriedades da aplicação `iothub-message-schema` e `opType` dar-lhe informações sobre o tipo de notificação de alteração:

```cmd/sh
Received event from partition: 1.
Telemetry received:  [{"op":"add","path":"/thermostat1","value":{"$metadata":{"maxTempSinceLastReboot":{"lastUpdateTime":"2020-10-21T14:13:39.36491Z"}},"maxTempSinceLastReboot":98.34}}]
Properties (set by device):  {b'hubName': b'my-pnp-hub', b'deviceId': b'my-pnp-device', b'operationTimestamp': b'2020-10-21T14:13:39.3649100+00:00', b'iothub-message-schema': b'digitalTwinChangeNotification', b'opType': b'updateTwin'}
System properties (set by IoT Hub):  {b'user-id': b'my-pnp-hub', b'correlation-id': b'1210b664ab83', b'content-type': b'application/json-patch+json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-enqueuedtime': 1603289619481, b'iothub-message-source': b'digitalTwinChangeEvents', b'x-opt-sequence-number': 13342, b'x-opt-offset': b'12890102984', b'x-opt-enqueued-time': 1603289619668}
```
