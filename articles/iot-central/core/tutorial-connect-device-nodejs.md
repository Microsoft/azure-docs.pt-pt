---
title: Tutorial - Ligue uma aplicação genérica de clientes Node.js à Central Azure IoT [ Microsoft Docs
description: Este tutorial mostra-lhe como, como desenvolvedor de dispositivos, ligar um dispositivo que executa uma aplicação de cliente Node.js à sua aplicação Azure IoT Central. Cria um modelo de dispositivo importando um modelo de capacidade do dispositivo e adiciona ndo pontos de vista que lhe permitem interagir com um dispositivo conectado
author: dominicbetts
ms.author: dobett
ms.date: 03/24/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: b7aebb5b9c1aa6566cedda869f97f2d1aa20df83
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673958"
---
# <a name="tutorial-create-and-connect-a-nodejs-client-application-to-your-azure-iot-central-application-nodejs"></a>Tutorial: Crie e ligue uma aplicação de cliente Node.js à sua aplicação Central Azure IoT (Node.js)

[!INCLUDE [iot-central-selector-tutorial-connect](../../../includes/iot-central-selector-tutorial-connect.md)]

Este tutorial mostra-lhe como, como desenvolvedor de dispositivos, ligar uma aplicação de cliente Node.js à sua aplicação Azure IoT Central. A aplicação Node.js simula o comportamento de um dispositivo de sensor ambiental. Você usa um modelo de capacidade de _dispositivo_ de amostra para criar um modelo de _dispositivo_ na IoT Central. Adiciona vistas ao modelo do dispositivo para permitir que um operador interaja com um dispositivo.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Importar um modelo de capacidade de dispositivo para criar um modelo de dispositivo.
> * Adicione vistas padrão e personalizadas a um modelo de dispositivo.
> * Publique um modelo de dispositivo e adicione um dispositivo real à sua aplicação IoT Central.
> * Crie e execute o código do dispositivo Node.js e consulte-o ligado à sua aplicação IoT Central.
> * Veja a telemetria simulada enviada do dispositivo.
> * Utilize uma vista para gerir as propriedades do dispositivo.
> * Ligue para comandos sincronizados e assíncronos para controlar o dispositivo.

## <a name="prerequisites"></a>Pré-requisitos

Para executar os passos descritos neste artigo é necessário o seguinte:

* Uma aplicação Azure IoT Central criada com o modelo de **aplicação Personalizado.** Para obter mais informações, veja [criar um início rápido da aplicação](quick-deploy-iot-central.md).
* Uma máquina de desenvolvimento com a versão [Nó.js](https://nodejs.org/) 10.0.0 ou posteriormente instalada. Pode correr `node --version` na linha de comando para verificar a sua versão. As instruções deste tutorial pressupõem que está a executar o comando do **nó** no pedido de comando do Windows. No entanto, pode utilizar o Node.js em muitos outros sistemas operativos.

[!INCLUDE [iot-central-add-environmental-sensor](../../../includes/iot-central-add-environmental-sensor.md)]

### <a name="create-a-nodejs-application"></a>Criar uma aplicação Node.js

Os seguintes passos mostram-lhe como criar uma aplicação de cliente Node.js que se conecta ao dispositivo real que adicionou à aplicação. Esta aplicação Node.js simula o comportamento de um dispositivo real.

1. No ambiente da linha de `environmental-sensor` comando, navegue para a pasta que criou anteriormente.

1. Para inicializar o seu projeto Node.js e instalar as dependências necessárias, execute `npm init`os seguintes comandos - aceite todas as opções predefinidas quando executar:

    ```cmd/sh
    npm init
    npm install azure-iot-device azure-iot-device-mqtt azure-iot-provisioning-device-mqtt azure-iot-security-symmetric-key --save
    ```

1. Crie um ficheiro chamado **environmentalSensor.js** na `environmental-sensor` pasta.

1. Adicione as `require` seguintes declarações no início do ficheiro **environmentalSensor.js:**

    ```javascript
    "use strict";

    // Use the Azure IoT device SDK for devices that connect to Azure IoT Central.
    var iotHubTransport = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var ProvisioningTransport = require('azure-iot-provisioning-device-mqtt').Mqtt;
    var SymmetricKeySecurityClient = require('azure-iot-security-symmetric-key').SymmetricKeySecurityClient;
    var ProvisioningDeviceClient = require('azure-iot-provisioning-device').ProvisioningDeviceClient;
    ```

1. Adicione as seguintes declarações de variáveis ao ficheiro:

    ```javascript
    var provisioningHost = 'global.azure-devices-provisioning.net';
    var idScope = '{your Scope ID}';
    var registrationId = '{your Device ID}';
    var symmetricKey = '{your Primary Key}';
    var provisioningSecurityClient = new SymmetricKeySecurityClient(registrationId, symmetricKey);
    var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvisioningTransport(), provisioningSecurityClient);
    var hubClient;

    var targetTemperature = 0;
    var ledOn = true;
    ```

    Atualize os `{your Scope ID}`espaços `{your Device ID}`reservados, e `{your Primary Key}` com os valores que fez anteriormente. Nesta amostra, inicie-se `targetTemperature` a zero, pode utilizar a leitura atual do dispositivo ou um valor do dispositivo twin.

1. Para enviar telemetria simulada para a sua aplicação Central Azure IoT, adicione a seguinte função ao ficheiro:

    ```javascript
    // Send simulated device telemetry.
    function sendTelemetry() {
      var temp = targetTemperature + (Math.random() * 15);
      var humid = 70 + (Math.random() * 10);
      var data = JSON.stringify({
        temp: temp,
        humid: humid,
        });
      var message = new Message(data);
      hubClient.sendEvent(message, (err, res) => console.log(`Sent message: ${message.getData()}` +
        (err ? `; error: ${err.toString()}` : '') +
        (res ? `; status: ${res.constructor.name}` : '')));
    }
    ```

    Os nomes dos artigos de`temp` `humid`telemetria (e) devem coincidir com os nomes utilizados no modelo do dispositivo.

1. Para enviar propriedades gémeas do dispositivo para a sua aplicação Azure IoT Central, adicione a seguinte função ao seu ficheiro:

    ```javascript
    // Send device twin reported properties.
    function sendDeviceProperties(twin, properties) {
      twin.properties.reported.update(properties, (err) => console.log(`Sent device properties: ${JSON.stringify(properties)}; ` +
        (err ? `error: ${err.toString()}` : `status: success`)));
    }
    ```

    A IoT Central utiliza gémeos de dispositivos para sincronizar valores de propriedade entre o dispositivo e a aplicação IoT Central. Os valores de propriedade do dispositivo usam propriedades reportadas do dispositivo twin. As propriedades reempreensíveis utilizam ambas as propriedades reportadas e desejadas pelo dispositivo Twin.

1. Para definir e manusear as propriedades reempreensíveis a que o seu dispositivo responde, adicione o seguinte código:

    ```javascript
    // Add any writeable properties your device supports,
    // mapped to a function that's called when the writeable property
    // is updated in the IoT Central application.
    var writeableProperties = {
      'name': (newValue, callback) => {
          setTimeout(() => {
            callback(newValue, 'completed');
          }, 1000);
      },
      'brightness': (newValue, callback) => {
        setTimeout(() => {
            callback(newValue, 'completed');
        }, 5000);
      }
    };

    // Handle writeable property updates that come from IoT Central via the device twin.
    function handleWriteablePropertyUpdates(twin) {
      twin.on('properties.desired', function (desiredChange) {
        for (let setting in desiredChange) {
          if (writeableProperties[setting]) {
            console.log(`Received setting: ${setting}: ${desiredChange[setting].value}`);
            writeableProperties[setting](desiredChange[setting].value, (newValue, status) => {
              var patch = {
                [setting]: {
                  value: newValue,
                  status: status,
                  desiredVersion: desiredChange.$version
                }
              }
              sendDeviceProperties(twin, patch);
            });
          }
        }
      });
    }
    ```

    Quando o operador define uma propriedade reempregável na aplicação IoT Central, a aplicação utiliza uma propriedade desejada por dispositivo para enviar o valor para o dispositivo. O dispositivo responde então usando uma propriedade reportada de gémeos dispositivos. Quando a IoT Central recebe o valor de propriedade reportado, atualiza a vista do imóvel com um estado de **sincronização.**

    Os nomes das`name` `brightness`propriedades (e) devem coincidir com os nomes utilizados no modelo do dispositivo.

1. Adicione o seguinte código para manusear os comandos enviados da aplicação IoT Central:

    ```javascript
    // Setup command handlers
    function setupCommandHandlers(twin) {

      // Handle synchronous LED blink command with request and response payload.
      function onBlink(request, response) {
        console.log('Received synchronous call to blink');
        var responsePayload = {
          status: 'Blinking LED every ' + request.payload  + ' seconds'
        }
        response.send(200, responsePayload, (err) => {
          if (err) {
            console.error('Unable to send method response: ' + err.toString());
          } else {
            console.log('Blinking LED every ' + request.payload  + ' seconds');
          }
        });
      }

      // Handle synchronous LED turn on command
      function turnOn(request, response) {
        console.log('Received synchronous call to turn on LED');
        if(!ledOn){
          console.log('Turning on the LED');
          ledOn = true;
        }
        response.send(200, (err) => {
          if (err) {
            console.error('Unable to send method response: ' + err.toString());
          }
        });
      }

      // Handle synchronous LED turn off command
      function turnOff(request, response) {
        console.log('Received synchronous call to turn off LED');
        if(ledOn){
          console.log('Turning off the LED');
          ledOn = false;
        }
        response.send(200, (err) => {
          if (err) {
            console.error('Unable to send method response: ' + err.toString());
          }
        });
      }

      // Handle asynchronous sensor diagnostics command with response payload.
      function diagnostics(request, response) {
        console.log('Starting asynchronous diagnostics run...');
        response.send(202, (err) => {
          if (err) {
            console.error('Unable to send method response: ' + err.toString());
          } else {
            var repetitions = 3;
            var intervalID = setInterval(() => {
              console.log('Generating diagnostics...');
              if (--repetitions === 0) {
                clearInterval(intervalID);
                var properties = {
                  rundiagnostics: {
                    value: 'Diagnostics run complete at ' + new Date().toLocaleString()
                  }
                };
                sendDeviceProperties(twin, properties);
              }
            }, 2000);
          }
        });
      }

      hubClient.onDeviceMethod('blink', onBlink);
      hubClient.onDeviceMethod('turnon', turnOn);
      hubClient.onDeviceMethod('turnoff', turnOff);
      hubClient.onDeviceMethod('rundiagnostics', diagnostics);
    }
    ```

    Os nomes dos`blink`comandos `turnoff`( `rundiagnostics`, `turnon`, e ) devem coincidir com os nomes utilizados no modelo do dispositivo.

    Atualmente, a IoT Central não utiliza o esquema de resposta definido no modelo de capacidade do dispositivo. Para um comando sincronizado, a carga útil de resposta pode ser qualquer JSON válido. Para um comando assíncrono, o dispositivo deve devolver imediatamente uma resposta de 202, seguida de atualização de propriedade reportada quando a obra estiver concluída. O formato da atualização de propriedade reportada é:

    ```json
    {
      [command name] : {
        value: 'response message'
      }
    }
    ```

    Um operador pode ver a carga de resposta no histórico do comando.

1. Adicione o seguinte código para concluir a ligação ao Azure IoT Central e ligar as funções no código de cliente:

    ```javascript
    // Handle device connection to Azure IoT Central.
    var connectCallback = (err) => {
      if (err) {
        console.log(`Device could not connect to Azure IoT Central: ${err.toString()}`);
      } else {
        console.log('Device successfully connected to Azure IoT Central');

        // Send telemetry to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);

        // Get device twin from Azure IoT Central.
        hubClient.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send device properties once on device start up.
            var properties = {
              state: 'true'
            };
            sendDeviceProperties(twin, properties);

            handleWriteablePropertyUpdates(twin);

            setupCommandHandlers(twin);
          }
        });
      }
    };

    // Start the device (register and connect to Azure IoT Central).
    provisioningClient.register((err, result) => {
      if (err) {
        console.log('Error registering device: ' + err);
      } else {
        console.log('Registration succeeded');
        console.log('Assigned hub=' + result.assignedHub);
        console.log('DeviceId=' + result.deviceId);
        var connectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';SharedAccessKey=' + symmetricKey;
        hubClient = Client.fromConnectionString(connectionString, iotHubTransport);

        hubClient.open(connectCallback);
      }
    });
    ```

## <a name="run-your-nodejs-application"></a>Executar a aplicação Node.js

Para iniciar a aplicação do cliente do dispositivo, execute o seguinte comando no ambiente da linha de comando:

```cmd/sh
node environmentalSensor.js
```

Pode ver que o dispositivo se liga à sua aplicação Central Azure IoT e começa a enviar telemetria:

![Executar a aplicação do cliente](media/tutorial-connect-device-nodejs/run-application.png)

[!INCLUDE [iot-central-monitor-environmental-sensor](../../../includes/iot-central-monitor-environmental-sensor.md)]

Pode ver como o dispositivo reage a comandos e atualizações de propriedades:

![Observe a aplicação do cliente](media/tutorial-connect-device-nodejs/run-application-2.png)

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre os modelos de capacidade do dispositivo e como criar os seus próprios modelos de dispositivo, continue a orientar:

> [!div class="nextstepaction"]
> [Defina um novo tipo de dispositivo IoT](./howto-set-up-template.md)
