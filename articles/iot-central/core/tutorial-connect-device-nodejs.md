---
title: Tutorial - Ligue uma app genérica Node.js ao Azure IoT Central Microsoft Docs
description: Este tutorial mostra-lhe como, como desenvolvedor de dispositivos, ligar um dispositivo que executa uma aplicação Node.js cliente à sua aplicação Azure IoT Central. Cria um modelo de dispositivo importando um modelo de capacidade do dispositivo e adiciona vistas que lhe permitem interagir com um dispositivo conectado
author: dominicbetts
ms.author: dobett
ms.date: 07/07/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom:
- mqtt
- device-developer
- devx-track-js
ms.openlocfilehash: 87284b88076cbd205a5d8ae388fe0b37c35cf6f4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91328552"
---
# <a name="tutorial-create-and-connect-a-client-application-to-your-azure-iot-central-application-nodejs"></a>Tutorial: Criar e ligar uma aplicação de cliente à sua aplicação Azure IoT Central (Node.js)

[!INCLUDE [iot-central-selector-tutorial-connect](../../../includes/iot-central-selector-tutorial-connect.md)]

*Este artigo aplica-se a construtores de soluções e desenvolvedores de dispositivos.*

Este tutorial mostra-lhe como, como desenvolvedor de dispositivos, ligar uma aplicação Node.js cliente à sua aplicação Azure IoT Central. A aplicação Node.js simula o comportamento de um dispositivo sensor ambiental. Utilize um _modelo de capacidade do dispositivo_ de amostra para criar um modelo de _dispositivo_ na IoT Central. Adiciona vistas ao modelo do dispositivo para permitir que um operador interaja com um dispositivo.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Importe um modelo de capacidade do dispositivo para criar um modelo de dispositivo.
> * Adicione vistas padrão e personalizadas a um modelo de dispositivo.
> * Publique um modelo de dispositivo e adicione um dispositivo real à sua aplicação IoT Central.
> * Crie e execute o código do dispositivo Node.js e veja-o ligar-se à sua aplicação IoT Central.
> * Veja a telemetria simulada enviada do dispositivo.
> * Utilize uma vista para gerir as propriedades do dispositivo.
> * Chame comandos sincronizados e assíncronos para controlar o dispositivo.

## <a name="prerequisites"></a>Pré-requisitos

Para executar os passos descritos neste artigo é necessário o seguinte:

* Uma aplicação Azure IoT Central criada usando o modelo **de aplicação personalizado.** Para obter mais informações, veja [criar um início rápido da aplicação](quick-deploy-iot-central.md). A aplicação deve ter sido criada em ou depois de 07/14/2020.
* Uma máquina de desenvolvimento com [Node.js](https://nodejs.org/) versão 10.0.0 ou posteriormente instalada. Pode correr `node --version` na linha de comando para verificar a sua versão. As instruções neste tutorial assumem que está a executar o comando do **nó** na pronta do comando do Windows. No entanto, pode utilizar Node.js em muitos outros sistemas operativos.

[!INCLUDE [iot-central-add-environmental-sensor](../../../includes/iot-central-add-environmental-sensor.md)]

### <a name="create-a-nodejs-application"></a>Criar uma aplicação Node.js

Os passos seguintes mostram-lhe como criar uma aplicação Node.js do cliente que se conecta ao dispositivo real que adicionou à aplicação. Esta aplicação Node.js simula o comportamento de um dispositivo real.

1. No seu ambiente de linha de comando, navegue para a `environmental-sensor` pasta que criou anteriormente.

1. Para rubricar o seu projeto Node.js e instalar as dependências necessárias, executar os seguintes comandos - aceite todas as opções padrão quando `npm init` executar:

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

    Atualize os espaços `{your Scope ID}` reservados, e com os `{your Device ID}` `{your Primary Key}` valores que fez anteriormente. Nesta amostra, inicializa-se `targetTemperature` a zero, pode utilizar a leitura atual do dispositivo ou um valor do twin do dispositivo.

1. Para enviar telemetria simulada para a sua aplicação Azure IoT Central, adicione a seguinte função ao ficheiro:

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

    Os nomes dos artigos de telemetria `temp` `humid` (e) devem corresponder aos nomes utilizados no modelo do dispositivo.

1. Para enviar propriedades gémeas do dispositivo para a sua aplicação Azure IoT Central, adicione a seguinte função ao seu ficheiro:

    ```javascript
    // Send device twin reported properties.
    function sendDeviceProperties(twin, properties) {
      twin.properties.reported.update(properties, (err) => console.log(`Sent device properties: ${JSON.stringify(properties)}; ` +
        (err ? `error: ${err.toString()}` : `status: success`)));
    }
    ```

    A IoT Central utiliza gémeos de dispositivos para sincronizar os valores de propriedade entre o dispositivo e a aplicação IoT Central. Os valores de propriedade do dispositivo usam propriedades reportadas pelo dispositivo twin. As propriedades escritas utilizam ambas as propriedades reportadas e desejadas pelo dispositivo.

1. Para definir e manusear as propriedades escritas a que o seu dispositivo responde, adicione o seguinte código. A mensagem que o dispositivo envia em resposta à [atualização de propriedade escrita](concepts-telemetry-properties-commands.md#writeable-property-types) deve incluir os `av` campos e `ac` campos. O `ad` campo é opcional:

    ```javascript
    // Add any writeable properties your device supports,
    // mapped to a function that's called when the writeable property
    // is updated in the IoT Central application.
    var writeableProperties = {
      'name': (newValue, callback) => {
          setTimeout(() => {
            callback(newValue, 'completed', 200);
          }, 1000);
      },
      'brightness': (newValue, callback) => {
        setTimeout(() => {
            callback(newValue, 'completed', 200);
        }, 5000);
      }
    };

    // Handle writeable property updates that come from IoT Central via the device twin.
    function handleWriteablePropertyUpdates(twin) {
      twin.on('properties.desired', function (desiredChange) {
        for (let setting in desiredChange) {
          if (writeableProperties[setting]) {
            console.log(`Received setting: ${setting}: ${desiredChange[setting]}`);
            writeableProperties[setting](desiredChange[setting], (newValue, status, code) => {
              var patch = {
                [setting]: {
                  value: newValue,
                  ad: status,
                  ac: code,
                  av: desiredChange.$version
                }
              }
              sendDeviceProperties(twin, patch);
            });
          }
        }
      });
    }
    ```

    Quando o operador define uma propriedade escrita na aplicação IoT Central, a aplicação utiliza um dispositivo que dois propriedades desejadas para enviar o valor para o dispositivo. Em seguida, o dispositivo responde utilizando uma propriedade reportada por gémeos do dispositivo. Quando a IoT Central recebe o valor da propriedade reportada, atualiza a vista da propriedade com um estado de **sincronização.**

    Os nomes das propriedades `name` `brightness` (e) devem corresponder aos nomes utilizados no modelo do dispositivo.

1. Adicione o seguinte código para lidar com os comandos enviados a partir da aplicação IoT Central:

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

    Os nomes dos comandos ( `blink` , , , e ) devem corresponder aos `turnon` `turnoff` `rundiagnostics` nomes utilizados no modelo do dispositivo.

    Atualmente, a IoT Central não utiliza o esquema de resposta definido no modelo de capacidade do dispositivo. Para um comando sincronizado, a carga útil de resposta pode ser qualquer JSON válido. Para um comando assíncrona, o dispositivo deve devolver imediatamente uma resposta 202, seguida de uma atualização de propriedade reportada quando o trabalho estiver concluído. O formato da atualização de propriedade reportada é:

    ```json
    {
      [command name] : {
        value: 'response message'
      }
    }
    ```

    Um operador pode ver a carga útil de resposta no histórico de comando.

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
              state: 'true',
              processorArchitecture: 'ARM',
              swVersion: '1.0.0'
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

Para iniciar a aplicação do cliente do dispositivo, execute o seguinte comando no seu ambiente de linha de comando:

```cmd/sh
node environmentalSensor.js
```

Pode ver que o dispositivo se conecta à sua aplicação Azure IoT Central e começa a enviar telemetria:

![Executar a aplicação do cliente](media/tutorial-connect-device-nodejs/run-application.png)

[!INCLUDE [iot-central-monitor-environmental-sensor](../../../includes/iot-central-monitor-environmental-sensor.md)]

Pode ver como o dispositivo responde a comandos e atualizações de propriedade:

![Observar a aplicação do cliente](media/tutorial-connect-device-nodejs/run-application-2.png)

## <a name="view-raw-data"></a>Ver dados brutos

[!INCLUDE [iot-central-monitor-environmental-sensor-raw-data](../../../includes/iot-central-monitor-environmental-sensor-raw-data.md)]

## <a name="next-steps"></a>Passos seguintes

Se preferir continuar através do conjunto de tutoriais IoT Central e aprender mais sobre a construção de uma solução IoT Central, consulte:

> [!div class="nextstepaction"]
> [Criar um modelo de dispositivo de gateway](./tutorial-define-gateway-device-type.md)

Como desenvolvedor de dispositivos, agora que aprendeu o básico de como criar um dispositivo usando Node.js, alguns dos próximos passos sugeridos são:

* Leia [Quais são os modelos do dispositivo para](./concepts-device-templates.md) saber mais sobre o papel dos modelos do dispositivo quando estiver a implementar o código do dispositivo.
* Leia [Se ligar à Azure IoT Central](./concepts-get-connected.md) para saber mais sobre como registar os dispositivos com a IoT Central e como a IoT Central assegura as ligações do dispositivo.
