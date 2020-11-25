---
title: Tutorial - Ligue uma app genérica Node.js ao Azure IoT Central Microsoft Docs
description: Este tutorial mostra-lhe como, como desenvolvedor de dispositivos, ligar um dispositivo que executa uma aplicação Node.js cliente à sua aplicação Azure IoT Central. Modifica o modelo do dispositivo gerado automaticamente adicionando vistas que permitem que um operador interaja com um dispositivo conectado.
author: dominicbetts
ms.author: dobett
ms.date: 11/03/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom:
- mqtt
- device-developer
- devx-track-js
ms.openlocfilehash: 6175be702f0824ad2cd2b146e96641037407ca0b
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "96018805"
---
# <a name="tutorial-create-and-connect-a-client-application-to-your-azure-iot-central-application-nodejs"></a>Tutorial: Criar e ligar uma aplicação cliente à aplicação Azure IoT Central (Node.js)

[!INCLUDE [iot-central-selector-tutorial-connect](../../../includes/iot-central-selector-tutorial-connect.md)]

*Este artigo aplica-se a construtores de soluções e desenvolvedores de dispositivos.*

Este tutorial mostra-lhe como, como desenvolvedor de dispositivos, ligar uma aplicação Node.js cliente à sua aplicação Azure IoT Central. A aplicação Node.js simula o comportamento de um dispositivo termóstato. Quando a aplicação se liga à IoT Central, envia o ID do modelo do dispositivo termóstato. A IoT Central utiliza o ID do modelo para recuperar o modelo do dispositivo e criar um modelo de dispositivo para si. Adiciona personalizações e vistas ao modelo do dispositivo para permitir que um operador interaja com um dispositivo.

Neste tutorial, vai aprender a:

> [!div class="checklist"]
> * Crie e execute o código do dispositivo Node.js e veja-o ligar-se à sua aplicação IoT Central.
> * Veja a telemetria simulada enviada do dispositivo.
> * Adicione vistas personalizadas a um modelo de dispositivo.
> * Publique o modelo do dispositivo.
> * Utilize uma vista para gerir as propriedades do dispositivo.
> * Chame um comando para controlar o dispositivo.

## <a name="prerequisites"></a>Pré-requisitos

Para executar os passos descritos neste artigo é necessário o seguinte:

* Uma aplicação Azure IoT Central criada usando o modelo **de aplicação personalizado.** Para obter mais informações, veja [criar um início rápido da aplicação](quick-deploy-iot-central.md). O pedido deve ter sido criado em ou depois de 14 de julho de 2020.
* Uma máquina de desenvolvimento com [Node.js](https://nodejs.org/) versão 6 ou posteriormente instalada. Pode correr `node --version` na linha de comando para verificar a sua versão. As instruções neste tutorial assumem que está a executar o comando do **nó** na pronta do comando do Windows. No entanto, pode utilizar Node.js em muitos outros sistemas operativos.
* Uma cópia local do [Microsoft Azure IoT SDK para Node.js](https://github.com/Azure/azure-iot-sdk-node) repositório GitHub que contém o código de amostra. Utilize este link para descarregar uma cópia do repositório: [Descarregue ZIP](https://github.com/Azure/azure-iot-sdk-node/archive/master.zip). Em seguida, desaperte o ficheiro para um local adequado na sua máquina local.

## <a name="review-the-code"></a>Rever o código

Na cópia do Microsoft Azure IoT SDK para Node.js que descarregou anteriormente, abra o ficheiro *azure-iot-sdk-node/dispositivo/samples/pnp/simple_thermostat.js* num editor de texto.

Quando executar a amostra para ligar à IoT Central, utiliza o Serviço de Provisionamento de Dispositivos (DPS) para registar o dispositivo e gerar uma cadeia de ligação. A amostra recolhe a informação de ligação DPS de que necessita do ambiente da linha de comando.

O `main` método:

* Cria um `client` objeto e define o ID do modelo antes de abrir a `dtmi:com:example:Thermostat;1` ligação.
* Cria um manipulador de comando.
* Inicia um loop para enviar telemetria de temperatura a cada 10 segundos.
* Envia a `maxTempSinceLastReboot` propriedade para a IoT Central. A IoT Central ignora a `serialNumber` propriedade porque não faz parte do modelo do dispositivo.
* Cria um manipulador de propriedades writable.

```javascript
async function main() {

  // ...

  // fromConnectionString must specify a transport, coming from any transport package.
  const client = Client.fromConnectionString(deviceConnectionString, Protocol);

  let resultTwin;
  try {
    // Add the modelId here
    await client.setOptions(modelIdObject);
    await client.open();

    client.onDeviceMethod(commandMaxMinReport, commandHandler);

    // Send Telemetry every 10 secs
    let index = 0;
    intervalToken = setInterval(() => {
      sendTelemetry(client, index).catch((err) => console.log('error', err.toString()));
      index += 1;
    }, telemetrySendInterval);

    // attach a standard input exit listener
    attachExitHandler(client);

    // Deal with twin
    try {
      resultTwin = await client.getTwin();
      const patchRoot = createReportPropPatch({ serialNumber: deviceSerialNum });
      const patchThermostat = createReportPropPatch({
        maxTempSinceLastReboot: deviceTemperatureSensor.getMaxTemperatureValue()
      });

      // the below things can only happen once the twin is there
      updateComponentReportedProperties(resultTwin, patchRoot);
      updateComponentReportedProperties(resultTwin, patchThermostat);

      // Setup the handler for desired properties
      desiredPropertyPatchHandler(resultTwin);

    } catch (err) {
      console.error('could not retrieve twin or report twin properties\n' + err.toString());
    }
  } catch (err) {
    console.error('could not connect Plug and Play client or could not attach interval function for telemetry\n' + err.toString());
  }
}
```

A `provisionDevice` função mostra como o dispositivo utiliza DPS para registar e ligar à IoT Central. A carga útil inclui o ID do modelo:

```javascript
async function provisionDevice(payload) {
  var provSecurityClient = new SymmetricKeySecurityClient(registrationId, symmetricKey);
  var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvProtocol(), provSecurityClient);

  if (!!(payload)) {
    provisioningClient.setProvisioningPayload(payload);
  }

  try {
    let result = await provisioningClient.register();
    deviceConnectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';SharedAccessKey=' + symmetricKey;
  } catch (err) {
    console.error("error registering device: " + err.toString());
  }
}
```

A `sendTelemetry` função mostra como o dispositivo envia a telemetria de temperatura para a IoT Central. O `getCurrentTemperatureObject` método devolve um objeto que se parece `{ temperature: 45.6 }` com:

```javascript
async function sendTelemetry(deviceClient, index) {
  console.log('Sending telemetry message %d...', index);
  const msg = new Message(
    JSON.stringify(
      deviceTemperatureSensor.updateSensor().getCurrentTemperatureObject()
    )
  );
  msg.contentType = 'application/json';
  msg.contentEncoding = 'utf-8';
  await deviceClient.sendEvent(msg);
}
```

O `main` método utiliza os dois métodos seguintes para enviar a propriedade para a `maxTempSinceLastReboot` IoT Central. O `main` método chama com um objeto que se parece `createReportPropPatch` `{maxTempSinceLastReboot: 80.9}` com:

```javascript
const createReportPropPatch = (propertiesToReport) => {
  let patch;
  patch = { };
  patch = propertiesToReport;
  return patch;
};

const updateComponentReportedProperties = (deviceTwin, patch) => {
  deviceTwin.properties.reported.update(patch, function (err) {
    if (err) throw err;
    console.log('Properties have been reported for component');
  });
};
```

O `main` método utiliza os dois métodos seguintes para lidar com atualizações à propriedade de _temperatura-alvo_ da IoT Central. Note como `propertyUpdateHandle` constrói a resposta com a versão e código de estado:

```javascript
const desiredPropertyPatchHandler = (deviceTwin) => {
  deviceTwin.on('properties.desired', (delta) => {
    const versionProperty = delta.$version;

    Object.entries(delta).forEach(([propertyName, propertyValue]) => {
      if (propertyName !== '$version') {
        propertyUpdateHandler(deviceTwin, propertyName, null, propertyValue, versionProperty);
      }
    });
  });
};

const propertyUpdateHandler = (deviceTwin, propertyName, reportedValue, desiredValue, version) => {
  console.log('Received an update for property: ' + propertyName + ' with value: ' + JSON.stringify(desiredValue));
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
  console.log('updated the property');
};
```

O `main` método utiliza os dois métodos seguintes para lidar com chamadas para o `getMaxMinReport` comando. O `getMaxMinReportObject` método gera o relatório como um objeto JSON:

```javascript
const commandHandler = async (request, response) => {
  switch (request.methodName) {
  case commandMaxMinReport: {
    console.log('MaxMinReport ' + request.payload);
    await sendCommandResponse(request, response, 200, deviceTemperatureSensor.getMaxMinReportObject());
    break;
  }
  default:
    await sendCommandResponse(request, response, 404, 'unknown method');
    break;
  }
};

const sendCommandResponse = async (request, response, status, payload) => {
  try {
    await response.send(status, payload);
    console.log('Response to method \'' + request.methodName +
              '\' sent successfully.' );
  } catch (err) {
    console.error('An error ocurred when sending a method response:\n' +
              err.toString());
  }
};
```

## <a name="get-connection-information"></a>Obter informações da ligação

[!INCLUDE [iot-central-connection-configuration](../../../includes/iot-central-connection-configuration.md)]

## <a name="run-the-code"></a>Executar o código

Para executar a aplicação da amostra, abra um ambiente de linha de comando e navegue para a pasta *azure-iot-sdk-node/dispositivo/samples/pnp* pasta que contém o ficheiro de amostra *simple_thermostat.js.*

[!INCLUDE [iot-central-connection-environment](../../../includes/iot-central-connection-environment.md)]

Instalar as embalagens necessárias:

```cmd/sh
npm install
```

Execute o exemplo:

```cmd/sh
node simple_thermostat.js
```

A seguinte saída mostra o dispositivo a registar-se e a ligar-se à IoT Central. A amostra envia então a `maxTempSinceLastReboot` propriedade antes de começar a enviar telemetria:

```cmd/sh
registration succeeded
assigned hub=iotc-.......azure-devices.net
deviceId=sample-device-01
payload=undefined
Connecting using connection string HostName=iotc-........azure-devices.net;DeviceId=sample-device-01;SharedAccessKey=Ci....=
Enabling the commands on the client
Please enter q or Q to exit sample.
The following properties will be updated for root interface:
{ maxTempSinceLastReboot: 55.20309427428496 }
Properties have been reported for component
Sending telemetry message 0...
Sending telemetry message 1...
Sending telemetry message 2...
Sending telemetry message 3...
```

[!INCLUDE [iot-central-monitor-thermostat](../../../includes/iot-central-monitor-thermostat.md)]

Pode ver como o dispositivo responde a comandos e atualizações de propriedade:

```cmd/sh
MaxMinReport 2020-10-15T12:00:00.000Z
Response to method 'getMaxMinReport' sent successfully.

...

Received an update for property: targetTemperature with value: {"value":86.3}
The following properties will be updated for root interface:
{
  targetTemperature: {
    value: { value: 86.3 },
    ac: 200,
    ad: 'Successfully executed patch for targetTemperature',
    av: 2
  }
}
```

## <a name="view-raw-data"></a>Ver dados brutos

[!INCLUDE [iot-central-monitor-thermostat-raw-data](../../../includes/iot-central-monitor-thermostat-raw-data.md)]

## <a name="next-steps"></a>Passos seguintes

Se preferir continuar através do conjunto de tutoriais IoT Central e aprender mais sobre a construção de uma solução IoT Central, consulte:

> [!div class="nextstepaction"]
> [Criar um modelo de dispositivo de gateway](./tutorial-define-gateway-device-type.md)

Como desenvolvedor de dispositivos, agora que aprendeu o básico de como criar um dispositivo usando Node.js, alguns dos próximos passos sugeridos são:

* Leia [Quais são os modelos do dispositivo para](./concepts-device-templates.md) saber mais sobre o papel dos modelos do dispositivo quando estiver a implementar o código do dispositivo.
* Leia [Se ligar à Azure IoT Central](./concepts-get-connected.md) para saber mais sobre como registar os dispositivos com a IoT Central e como a IoT Central assegura as ligações do dispositivo.
* Leia [Telemetria, propriedade e cargas de comando](concepts-telemetry-properties-commands.md) para saber mais sobre os dados que o dispositivo troca com a IoT Central.
