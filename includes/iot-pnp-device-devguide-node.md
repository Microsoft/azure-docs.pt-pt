---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/19/2020
ms.openlocfilehash: dea63ba4939579f3f227d2eac05eb124ada1e4ec
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99616532"
---
## <a name="model-id-announcement"></a>Anúncio de ID modelo

Para anunciar o ID do modelo, o dispositivo deve incluí-lo nas informações de ligação:

```nodejs
const modelIdObject = { modelId: 'dtmi:com:example:Thermostat;1' };
const client = Client.fromConnectionString(deviceConnectionString, Protocol);
await client.setOptions(modelIdObject);
await client.open();
```

> [!TIP]
> Para módulos e IoT Edge, utilize `ModuleClient` no lugar de `Client` .

> [!TIP]
> Esta é a única vez que um dispositivo pode definir o ID do modelo, não pode ser atualizado depois de o dispositivo se ligar.

## <a name="dps-payload"></a>Payload do DPS

Os dispositivos que utilizam o [Serviço de Provisionamento de Dispositivos (DPS)](../articles/iot-dps/about-iot-dps.md) podem incluir o `modelId` a ser utilizado durante o processo de provisionamento utilizando a seguinte carga útil JSON.

```json
{
    "modelId" : "dtmi:com:example:Thermostat;1"
}
```

## <a name="implement-telemetry-properties-and-commands"></a>Implementar telemetria, propriedades e comandos

Conforme descrito nos [componentes Do Understand nos modelos IoT Plug e Play,](../articles/iot-pnp/concepts-components.md)os fabricantes de dispositivos devem decidir se pretendem utilizar componentes para descrever os seus dispositivos. Ao utilizar componentes, os dispositivos devem seguir as regras descritas nesta secção.

### <a name="telemetry"></a>Telemetria

Um componente predefinido não requer nenhuma propriedade especial.

Ao utilizar componentes aninhados, os dispositivos devem definir uma propriedade de mensagem com o nome do componente:

```nodejs
async function sendTelemetry(deviceClient, data, index, componentName) {
  const msg = new Message(data);
  if (!!(componentName)) {
    msg.properties.add(messageSubjectProperty, componentName);
  }
  msg.contentType = 'application/json';
  msg.contentEncoding = 'utf-8';
  await deviceClient.sendEvent(msg);
}
```

### <a name="read-only-properties"></a>Propriedades só de leitura

Reportar uma propriedade a partir do componente padrão não requer qualquer construção especial:

```nodejs
const createReportPropPatch = (propertiesToReport) => {
  let patch;
  patch = { };
  patch = propertiesToReport;
  return patch;
};

deviceTwin = await client.getTwin();
patchThermostat = createReportPropPatch({
  maxTempSinceLastReboot: 38.7
});

deviceTwin.properties.reported.update(patchThermostat, function (err) {
  if (err) throw err;
});
```

O dispositivo twin é atualizado com a próxima propriedade reportada:

```json
{
  "reported": {
      "maxTempSinceLastReboot" : 38.7
  }
}
```

Ao utilizar componentes aninhados, as propriedades devem ser criadas dentro do nome do componente.:

```nodejs
helperCreateReportedPropertiesPatch = (propertiesToReport, componentName) => {
  let patch;
  if (!!(componentName)) {
    patch = { };
    propertiesToReport.__t = 'c';
    patch[componentName] = propertiesToReport;
  } else {
    patch = { };
    patch = propertiesToReport;
  }
  return patch;
};

deviceTwin = await client.getTwin();
patchThermostat1Info = helperCreateReportedPropertiesPatch({
  maxTempSinceLastReboot: 38.7,
}, 'thermostat1');

deviceTwin.properties.reported.update(patchThermostat1Info, function (err) {
  if (err) throw err;
});
```

O dispositivo twin é atualizado com a próxima propriedade reportada:

```json
{
  "reported": {
    "thermostat1" : {  
      "__t" : "c",  
      "maxTempSinceLastReboot" : 38.7
     } 
  }
}
```

### <a name="writable-properties"></a>Propriedades writable

Estas propriedades podem ser definidas pelo dispositivo ou atualizadas pela solução. Se a solução atualizar um imóvel, o cliente recebe uma notificação como uma chamada no `Client` ou `ModuleClient` . Para seguir as convenções IoT Plug and Play, o dispositivo deve informar o serviço de que a propriedade foi recebida com sucesso.

#### <a name="report-a-writable-property"></a>Reportar uma propriedade writable

Quando um dispositivo reporta uma propriedade por si só, deve incluir os `ack` valores definidos nas convenções.

Para reportar uma propriedade por defeito do componente padrão:

```nodejs
patch = {
  targetTemperature:
    {
      'value': 23.2,
      'ac': 200,  // using HTTP status codes
      'ad': 'reported default value',
      'av': 0  // not read from a desired property
    }
};
deviceTwin.properties.reported.update(patch, function (err) {
  if (err) throw err;
});
```

O dispositivo twin é atualizado com a próxima propriedade reportada:

```json
{
  "reported": {
    "targetTemperature": {
      "value": 23.2,
      "ac": 200,
      "av": 0,
      "ad": "reported default value"
    }
  }
}
```

Para reportar uma propriedade writable de um componente aninhado, o gémeo deve incluir um marcador:

```nodejs
patch = {
  thermostat1: {
    '__t' : 'c',
    targetTemperature: {
      'value': 23.2,
      'ac': 200,  // using HTTP status codes
      'ad': 'reported default value',
      'av': 0  // not read from a desired property
    }
  }
};
deviceTwin.properties.reported.update(patch, function (err) {
  if (err) throw err;
});
```

O dispositivo twin é atualizado com a próxima propriedade reportada:

```json
{
  "reported": {
    "thermostat1": {
      "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 0,
          "ad": "complete"
      }
    }
  }
}
```

#### <a name="subscribe-to-desired-property-updates"></a>Subscreva as atualizações de propriedade desejadas

Os serviços podem atualizar as propriedades desejadas que desencadeiam uma notificação nos dispositivos conectados. Esta notificação inclui as propriedades desejadas atualizadas, incluindo o número de versão que identifica a atualização. Os dispositivos devem responder com a mesma `ack` mensagem que as propriedades reportadas.

Um componente predefinido vê a propriedade única e cria o reportado `ack` com a versão recebida:

```nodejs
const propertyUpdateHandler = (deviceTwin, propertyName, reportedValue, desiredValue, version) => {
  const patch = createReportPropPatch(
    { [propertyName]:
      {
        'value': desiredValue,
        'ac': 200,
        'ad': 'Successfully executed patch for ' + propertyName,
        'av': version
      }
    });
  updateComponentReportedProperties(deviceTwin, patch);
};

desiredPropertyPatchHandler = (deviceTwin) => {
  deviceTwin.on('properties.desired', (delta) => {
    const versionProperty = delta.$version;

    Object.entries(delta).forEach(([propertyName, propertyValue]) => {
      if (propertyName !== '$version') {
        propertyUpdateHandler(deviceTwin, propertyName, null, propertyValue, versionProperty);
      }
    });
  });
};
```

O dispositivo twin mostra a propriedade nas secções desejadas e reportadas:

```json
{
  "desired" : {
    "targetTemperature": 23.2,
    "$version" : 3
  },
  "reported": {
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
  }
}
```

Um componente aninhado recebe as propriedades desejadas embrulhadas com o nome do componente, e deve reportar de volta a `ack` propriedade relatada:

```nodejs
const desiredPropertyPatchListener = (deviceTwin, componentNames) => {
  deviceTwin.on('properties.desired', (delta) => {
    Object.entries(delta).forEach(([key, values]) => {
      const version = delta.$version;
      if (!!(componentNames) && componentNames.includes(key)) { // then it is a component we are expecting
        const componentName = key;
        const patchForComponents = { [componentName]: {} };
        Object.entries(values).forEach(([propertyName, propertyValue]) => {
          if (propertyName !== '__t' && propertyName !== '$version') {
            const propertyContent = { value: propertyValue };
            propertyContent.ac = 200;
            propertyContent.ad = 'Successfully executed patch';
            propertyContent.av = version;
            patchForComponents[componentName][propertyName] = propertyContent;
          }
        });
        updateComponentReportedProperties(deviceTwin, patchForComponents, componentName);
      }
      else if  (key !== '$version') { // individual property for root
        const patchForRoot = { };
        const propertyContent = { value: values };
        propertyContent.ac = 200;
        propertyContent.ad = 'Successfully executed patch';
        propertyContent.av = version;
        patchForRoot[key] = propertyContent;
        updateComponentReportedProperties(deviceTwin, patchForRoot, null);
      }
    });
  });
};
```

O dispositivo gémeo para componentes mostra as secções desejadas e comunicadas da seguinte forma:

```json
{
  "desired" : {
    "thermostat1" : {
        "__t" : "c",
        "targetTemperature": 23.2,
    }
    "$version" : 3
  },
  "reported": {
    "thermostat1" : {
        "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
    }
  }
}
```

### <a name="commands"></a>Comandos

Um componente predefinido recebe o nome de comando tal como foi invocado pelo serviço.

Um componente aninhado recebe o nome de comando pré-fixado com o nome do componente e o `*` separador.

```nodejs
const commandHandler = async (request, response) => {
  switch (request.methodName) {
  
  // ...

  case 'thermostat1*reboot': {
    await response.send(200, 'reboot response');
    break;
  }
  default:
    await response.send(404, 'unknown method');
    break;
  }
};

client.onDeviceMethod('thermostat1*reboot', commandHandler);
```

#### <a name="request-and-response-payloads"></a>Cargas de pedido e resposta

Os comandos utilizam tipos para definir as suas cargas de pedido e resposta. Um dispositivo deve deserizar o parâmetro de entrada de entrada e serializar a resposta. O exemplo a seguir mostra como implementar um comando com tipos complexos definidos nas cargas:

```json
{
  "@type": "Command",
  "name": "getMaxMinReport",
  "displayName": "Get Max-Min report.",
  "description": "This command returns the max, min and average temperature from the specified time to the current time.",
  "request": {
    "name": "since",
    "displayName": "Since",
    "description": "Period to return the max-min report.",
    "schema": "dateTime"
  },
  "response": {
    "name" : "tempReport",
    "displayName": "Temperature Report",
    "schema": {
      "@type": "Object",
      "fields": [
        {
          "name": "maxTemp",
          "displayName": "Max temperature",
          "schema": "double"
        },
        {
          "name": "minTemp",
          "displayName": "Min temperature",
          "schema": "double"
        },
        {
          "name" : "avgTemp",
          "displayName": "Average Temperature",
          "schema": "double"
        },
        {
          "name" : "startTime",
          "displayName": "Start Time",
          "schema": "dateTime"
        },
        {
          "name" : "endTime",
          "displayName": "End Time",
          "schema": "dateTime"
        }
      ]
    }
  }
}
```

Os seguintes fragmentos de código mostram como um dispositivo implementa esta definição de comando, incluindo os tipos utilizados para permitir a serialização e a deserialização:

```nodejs
class TemperatureSensor {

  // ...

  getMaxMinReportObject() {
    return {
      maxTemp: this.maxTemp,
      minTemp: this.minTemp,
      avgTemp: this.cumulativeTemperature / this.numberOfTemperatureReadings,
      endTime: (new Date(Date.now())).toISOString(),
      startTime: this.startTime
    };
  }
}

// ...

const deviceTemperatureSensor = new TemperatureSensor();

const commandHandler = async (request, response) => {
  switch (request.methodName) {
  case commandMaxMinReport: {
    console.log('MaxMinReport ' + request.payload);
    await response.send(200, deviceTemperatureSensor.getMaxMinReportObject());
    break;
  }
  default:
    await response.send(404, 'unknown method');
    break;
  }
};
```

> [!Tip]
> Os nomes de pedido e resposta não estão presentes nas cargas serializadas transmitidas pelo fio.
