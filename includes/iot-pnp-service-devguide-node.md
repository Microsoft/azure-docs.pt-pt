---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 10/20/2020
ms.openlocfilehash: dfeeb451912dd32770a6ae92d73de83851d9d8f6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102244851"
---
Estão também disponíveis os seguintes recursos:

- [Node.js documentação de referência da SDK](/javascript/api/azure-iothub)
- [Amostras de clientes de serviço](https://github.com/Azure/azure-iot-sdk-node/blob/master/service/samples/javascript/twin.js)
- [Amostras de Gémeos Digitais](https://github.com/Azure/azure-iot-sdk-node/blob/master/service/samples/javascript/get_digital_twin.js)

## <a name="iot-hub-service-client-examples"></a>Exemplos de clientes de serviço IoT Hub

Esta secção mostra exemplos javaScript usando o cliente de serviço IoT Hub e as classes **de Registo** e **Cliente.** Utiliza a classe **Registry** para interagir com o estado do dispositivo utilizando gémeos do dispositivo. Também pode utilizar a classe **Registry** para [consultar registos de dispositivos](../articles/iot-hub/iot-hub-devguide-query-language.md) no seu Hub IoT. Utilize a classe **Cliente** para ligar para comandos no dispositivo. O modelo [DTDL](../articles/iot-pnp/concepts-digital-twin.md) para o dispositivo define as propriedades e comanda os instrumentos do dispositivo. Nos fragmentos de código, a `deviceId` variável detém o ID do dispositivo IoT Plug and Play registado no seu hub IoT.

### <a name="get-the-device-twin-and-model-id"></a>Obtenha o dispositivo twin e model ID

Para obter o iD duplo e modelo do dispositivo IoT Plug and Play que está ligado ao seu hub IoT:

```javascript
var Registry = require('azure-iothub').Registry;

// ...

var registry = Registry.fromConnectionString(connectionString);
registry.getTwin(deviceId, function(err, twin) {
  if (err) {
    console.error(err.message);
  } else {
    console.log('Model Id: ' + twin.modelId);
    console.log(JSON.stringify(twin, null, 2));
  }
}
```

### <a name="update-device-twin"></a>Atualização do dispositivo twin

O seguinte código snippet mostra como atualizar a `targetTemperature` propriedade em um dispositivo. A amostra mostra como precisa de obter o gémeo antes de atualizá-lo. A propriedade é definida no componente predefinido do dispositivo:

```javascript
var Registry = require('azure-iothub').Registry;
var registry = Registry.fromConnectionString(connectionString);

registry.getTwin(deviceId, function(err, twin) {
  if (err) {
    console.error(err.message);
  } else {
    var twinPatch = {
      properties: {
        desired: {
          targetTemperature: 42
        }
      }
    };
    twin.update(twinPatch, function(err, twin) {
      if (err) {
        console.error(err.message);
      } else {
        console.log(JSON.stringify(twin, null, 2));
      }
    }
  }
}
```

O seguinte corte mostra como atualizar a `targetTemperature` propriedade em um componente. A amostra mostra como precisa de obter o gémeo antes de atualizá-lo. A propriedade é definida no componente **termóstato1:**

```javascript
var Registry = require('azure-iothub').Registry;
var registry = Registry.fromConnectionString(connectionString);

registry.getTwin(deviceId, function(err, twin) {
  if (err) {
    console.error(err.message);
  } else {
    var twinPatch = {
      properties: {
        desired: {
          thermostat1:
          {
            __t: "c",
            targetTemperature: 45
          }
        }
      }
    };
    twin.update(twinPatch, function(err, twin) {
      if (err) {
        console.error(err.message);
      } else {
        console.log(JSON.stringify(twin, null, 2));
      }
    }
  }
}
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

```javascript
var Client = require('azure-iothub').Client;

// ...

var client = Client.fromConnectionString(connectionString);

var methodParams = {
  methodName: "getMaxMinReport",
  payload: new Date().getMinutes -2,
  responseTimeoutInSeconds: 15
};

client.invokeDeviceMethod(deviceId, methodParams, function (err, result) {
  if (err) {
    console.error('Failed to invoke method \'' + methodParams.methodName + '\': ' + err.message);
  } else {
    console.log(methodParams.methodName + ' on ' + deviceId + ':');
    console.log(JSON.stringify(result, null, 2));
  }
});
```

O seguinte corte mostra como chamar o `getMaxMinReport` comando de um componente. O comando é definido no componente **termostato1:**

```javascript
var Client = require('azure-iothub').Client;

// ...

var client = Client.fromConnectionString(connectionString);

var methodParams = {
  methodName: "thermostat1*getMaxMinReport",
  payload: new Date().getMinutes -2,
  responseTimeoutInSeconds: 15
};

client.invokeDeviceMethod(deviceId, methodParams, function (err, result) {
  if (err) {
    console.error('Failed to invoke method \'' + methodParams.methodName + '\': ' + err.message);
  } else {
    console.log(methodParams.methodName + ' on ' + deviceId + ':');
    console.log(JSON.stringify(result, null, 2));
  }
});
```

## <a name="iot-hub-digital-twin-examples"></a>Exemplos gémeos digitais IoT Hub

Utiliza-se a classe **DigitalTwinClient** para interagir com o estado do dispositivo utilizando gémeos digitais. O modelo [DTDL](../articles/iot-pnp/concepts-digital-twin.md) para o dispositivo define as propriedades e comanda os instrumentos do dispositivo.

Esta secção mostra exemplos javaScript usando a API dos Gémeos Digitais.

A `digitalTwinId` variável detém o ID do dispositivo IoT Plug and Play registado no seu hub IoT.

### <a name="get-the-digital-twin-and-model-id"></a>Obtenha o twin digital e o ID do modelo

Para obter o twin digital e o iD do ioT Plug and Play que se conecta ao seu hub IoT:

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;
const { inspect } = require('util');

// ...

const credentials = new IoTHubTokenCredentials(connectionString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const digitalTwin = await digitalTwinClient.getDigitalTwin(digitalTwinId);

console.log(inspect(digitalTwin));
console.log('Model Id: ' + inspect(digitalTwin.$metadata.$model));
```

### <a name="update-digital-twin"></a>Atualizar twin digital

O seguinte código snippet mostra como atualizar a `targetTemperature` propriedade em um dispositivo. A propriedade é definida no componente predefinido do dispositivo:

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;

// ...

const credentials = new IoTHubTokenCredentials(connString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const patch = [{
  op: 'add',
  path: '/targetTemperature',
  value: 42
}];
await digitalTwinClient.updateDigitalTwin(digitalTwinId, patch);
```

O seguinte corte mostra como atualizar a `targetTemperature` propriedade em um componente. A propriedade é definida no componente **termóstato1:**

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;

// ...

const credentials = new IoTHubTokenCredentials(connString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const patch = [{
  op: 'add',
  path: '/thermostat1/targetTemperature',
  value: 42
}];
await digitalTwinClient.updateDigitalTwin(digitalTwinId, patch);
```

### <a name="call-command"></a>Chamar comando

O seguinte corte mostra como invocar o `getMaxMinReport` comando definido num componente predefinido:

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;
const { inspect } = require('util');

// ...

const commandPayload = new Date().getMinutes -2;

const credentials = new IoTHubTokenCredentials(connectionString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const options = {
  connectTimeoutInSeconds: 30,
  responseTimeoutInSeconds: 40
};
const commandResponse = await digitalTwinClient.invokeCommand(digitalTwinId, "getMaxMinReport", commandPayload, options);

console.log(inspect(commandResponse));
```

O seguinte corte mostra como chamar o `getMaxMinReport` comando de um componente. O comando é definido no componente **termostato1:**

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;
const { inspect } = require('util');

// ...

const commandPayload = new Date().getMinutes -2;

const credentials = new IoTHubTokenCredentials(connectionString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const options = {
  connectTimeoutInSeconds: 30,
  responseTimeoutInSeconds: 40
};
const commandResponse = await digitalTwinClient.invokeComponentCommand(digitalTwinId, "thermostat1", "getMaxMinReport", commandPayload, options);

console.log(inspect(commandResponse));
```

## <a name="read-device-telemetry"></a>Ler telemetria do dispositivo

Os dispositivos IoT Plug e Play enviam a telemetria definida no modelo DTDL para o IoT Hub. Por padrão, o IoT Hub encaminha a telemetria para um ponto final do Event Hubs onde pode consumi-la. Para saber mais, consulte [o encaminhamento de mensagens IoT Hub para enviar mensagens dispositivo-a-nuvem para diferentes pontos finais](../articles/iot-hub/iot-hub-devguide-messages-d2c.md).

O seguinte corte de código mostra como ler a telemetria a partir do ponto final padrão do Event Hubs. O código deste corte é retirado do quickstart IoT Hub [Enviar telemetria de um dispositivo para um hub IoT e lê-lo com uma aplicação de back-end](../articles/iot-hub/quickstart-send-telemetry-node.md):

```javascript
const { EventHubConsumerClient } = require("@azure/event-hubs");

var printError = function (err) {
  console.log(err.message);
};

var printMessages = function (messages) {
  for (const message of messages) {
    console.log("Telemetry received: ");
    console.log(JSON.stringify(message.body));
    console.log("Properties (set by device): ");
    console.log(JSON.stringify(message.properties));
    console.log("System properties (set by IoT Hub): ");
    console.log(JSON.stringify(message.systemProperties));
    console.log("");
  }
};

// ...

const clientOptions = {};

const consumerClient = new EventHubConsumerClient("$Default", connectionString, clientOptions);

consumerClient.subscribe({
  processEvents: printMessages,
  processError: printError,
});
```

A seguinte saída do código anterior mostra a telemetria de temperatura enviada pelo dispositivo IoT Plug and Play **do Controlador de Temperatura** multi-componente. A `dt-subject` propriedade do sistema mostra o nome do componente que enviou a telemetria. Neste exemplo, os dois componentes são `thermostat1` e `thermostat2` tal como definidos no modelo DTDL. A `dt-dataschema` propriedade do sistema mostra o ID do modelo:

```cmd/sh
Telemetry received:
{"temperature":68.77370855171125}
Properties (set by device):
undefined
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-connection-auth-method":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","iothub-connection-auth-generation-id":"637388034455888246","iothub-enqueuedtime":1603206669320,"iothub-message-source":"Telemetry","dt-subject":"thermostat1","dt-dataschema":"dtmi:com:example:TemperatureController;1","contentType":"application/json","contentEncoding":"utf-8"}

Telemetry received:
{"temperature":30.833394506549226}
Properties (set by device):
undefined
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-connection-auth-method":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","iothub-connection-auth-generation-id":"637388034455888246","iothub-enqueuedtime":1603206665835,"iothub-message-source":"Telemetry","dt-subject":"thermostat2","dt-dataschema":"dtmi:com:example:TemperatureController;1","contentType":"application/json","contentEncoding":"utf-8"}
```

## <a name="read-device-twin-change-notifications"></a>Leia notificações de alteração dupla do dispositivo

Pode configurar o IoT Hub para gerar notificações de alterações gémeas do dispositivo para encaminhar para um ponto final suportado. Para saber mais, consulte [o encaminhamento de mensagens IoT Hub para enviar mensagens dispositivo-a-nuvem para diferentes pontos finais > eventos de não telemetria](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

O código mostrado no corte de código JavaScript anterior gera a seguinte saída quando o IoT Hub gera notificações de alteração dupla do dispositivo para um dispositivo termóstato sem componente. As propriedades da aplicação `iothub-message-schema` e `opType` dar-lhe informações sobre o tipo de notificação de alteração:

```cmd/sh
Telemetry received:
{"version":4,"properties":{"reported":{"maxTempSinceLastReboot":42.1415152639582,"$metadata":{"$lastUpdated":"2020-10-21T10:01:40.1281138Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-21T10:01:40.1281138Z"}},"$version":3}}}
Properties (set by device):
{"hubName":"my-pnp-hub","deviceId":"my-pnp-device","operationTimestamp":"2020-10-21T10:01:40.1281138+00:00","iothub-message-schema":"twinChangeNotification","opType":"updateTwin"}
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-enqueuedtime":1603274500282,"iothub-message-source":"twinChangeEvents","userId":{"type":"Buffer","data":[109,121,45,112,110,112,45,104,117,98]},"correlationId":"11ed82d13f50","contentType":"application/json","contentEncoding":"utf-8"}
```

O código mostrado no corte de código JavaScript anterior gera a seguinte saída quando o IoT Hub gera notificações de alteração dupla do dispositivo para um dispositivo com componentes. Este exemplo mostra a saída quando um dispositivo sensor de temperatura com um componente do termóstato gera notificações. As propriedades da aplicação `iothub-message-schema` e `opType` dar-lhe informações sobre o tipo de notificação de alteração:

```cmd/sh
Telemetry received:
{"version":4,"properties":{"reported":{"thermostat1":{"maxTempSinceLastReboot":3.5592971602417913,"__t":"c"},"$metadata":{"$lastUpdated":"2020-10-21T10:07:51.8284866Z","thermostat1":{"$lastUpdated":"2020-10-21T10:07:51.8284866Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-21T10:07:51.8284866Z"},"__t":{"$lastUpdated":"2020-10-21T10:07:51.8284866Z"}}},"$version":3}}}
Properties (set by device):
{"hubName":"my-pnp-hub","deviceId":"my-pnp-device","operationTimestamp":"2020-10-21T10:07:51.8284866+00:00","iothub-message-schema":"twinChangeNotification","opType":"updateTwin"}
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-enqueuedtime":1603274871951,"iothub-message-source":"twinChangeEvents","userId":{"type":"Buffer","data":[109,121,45,112,110,112,45,104,117,98]},"correlationId":"11ee605b195f","contentType":"application/json","contentEncoding":"utf-8"}
```

## <a name="read-digital-twin-change-notifications"></a>Leia notificações digitais de mudança de gémeos

Pode configurar o IoT Hub para gerar notificações digitais de alteração dupla para encaminhar para um ponto final suportado. Para saber mais, consulte [o encaminhamento de mensagens IoT Hub para enviar mensagens dispositivo-a-nuvem para diferentes pontos finais > eventos de não telemetria](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

O código mostrado no corte de código JavaScript anterior gera a seguinte saída quando o IoT Hub gera notificações de alteração gémea digital para um dispositivo termóstato sem componente. As propriedades da aplicação `iothub-message-schema` e `opType` dar-lhe informações sobre o tipo de notificação de alteração:

```cmd/sh
Telemetry received:
[{"op":"add","path":"/$metadata/maxTempSinceLastReboot","value":{"lastUpdateTime":"2020-10-21T10:01:40.1281138Z"}},{"op":"add","path":"/maxTempSinceLastReboot","value":42.1415152639582}]
Properties (set by device):
{"hubName":"my-pnp-hub","deviceId":"my-pnp-device","operationTimestamp":"2020-10-21T10:01:40.1281138+00:00","iothub-message-schema":"digitalTwinChangeNotification","opType":"updateTwin"}
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-enqueuedtime":1603274500282,"iothub-message-source":"digitalTwinChangeEvents","userId":{"type":"Buffer","data":[109,121,45,112,110,112,45,104,117,98]},"correlationId":"11ed82d13f50","contentType":"application/json-patch+json","contentEncoding":"utf-8"}
```

O código mostrado no corte de código JavaScript anterior gera a seguinte saída quando o IoT Hub gera notificações de mudança dupla digital para um dispositivo com componentes. Este exemplo mostra a saída quando um dispositivo sensor de temperatura com um componente do termóstato gera notificações. As propriedades da aplicação `iothub-message-schema` e `opType` dar-lhe informações sobre o tipo de notificação de alteração:

```cmd/sh
Telemetry received:
[{"op":"add","path":"/thermostat1","value":{"$metadata":{"maxTempSinceLastReboot":{"lastUpdateTime":"2020-10-21T10:07:51.8284866Z"}},"maxTempSinceLastReboot":3.5592971602417913}}]
Properties (set by device):
{"hubName":"my-pnp-hub","deviceId":"my-pnp-device","operationTimestamp":"2020-10-21T10:07:51.8284866+00:00","iothub-message-schema":"digitalTwinChangeNotification","opType":"updateTwin"}
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-enqueuedtime":1603274871951,"iothub-message-source":"digitalTwinChangeEvents","userId":{"type":"Buffer","data":[109,121,45,112,110,112,45,104,117,98]},"correlationId":"11ee605b195f","contentType":"application/json-patch+json","contentEncoding":"utf-8"}
```
