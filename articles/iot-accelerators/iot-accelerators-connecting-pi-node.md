---
title: Conectar o Raspberry Pi à solução de monitoramento remoto-node. js – Azure | Microsoft Docs
description: Descreve como conectar um dispositivo Raspberry Pi ao acelerador de solução de monitoramento remoto usando um aplicativo escrito em node. js.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 01/24/2018
ms.author: dobett
ms.openlocfilehash: 98d947e8aabf20fbfdb192cb80c9bc881007d5da
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889272"
---
# <a name="connect-your-raspberry-pi-device-to-the-remote-monitoring-solution-accelerator-nodejs"></a>Conectar o dispositivo Raspberry Pi ao acelerador de solução de monitoramento remoto (Node. js)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Este tutorial mostra como conectar um dispositivo real ao acelerador de solução de monitoramento remoto. Neste tutorial, você usa o Node. js, que é uma boa opção para ambientes com restrições de recursos mínimas.

Se você preferir simular um dispositivo, consulte [criar e testar um novo dispositivo simulado](iot-accelerators-remote-monitoring-create-simulated-device.md).

### <a name="required-hardware"></a>Hardware necessário

Um computador desktop para permitir que você se conecte remotamente à linha de comando no Raspberry Pi.

[Kit de início de IOT da Microsoft para Raspberry Pi 3](https://azure.microsoft.com/develop/iot/starter-kits/) ou componentes equivalentes. Este tutorial usa os seguintes itens do kit:

- Raspberry Pi 3
- Placa MicroSD (com NOOBS)
- Um cabo USB mini
- Um cabo Ethernet

### <a name="required-desktop-software"></a>Software de desktop necessário

Você precisa do cliente SSH no computador desktop para permitir que você acesse remotamente a linha de comando no Raspberry Pi.

- O Windows não inclui um cliente SSH. É recomendável [usar a](https://www.putty.org/)reutilização.
- A maioria das distribuições Linux e Mac OS incluem o utilitário SSH de linha de comando. Para obter mais informações, consulte [SSH usando Linux ou Mac os](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md).

### <a name="required-raspberry-pi-software"></a>Software Raspberry Pi necessário

Se você ainda não fez isso, instale o Node. js versão 4.0.0 ou posterior em seu Raspberry Pi. As etapas a seguir mostram como instalar o Node. js V6 em seu Raspberry Pi:

1. Conecte-se ao seu Raspberry Pi usando `ssh`. Para obter mais informações, consulte [SSH (Secure Shell)](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md) no [site do Raspberry Pi](https://www.raspberrypi.org/).

1. Use o seguinte comando para atualizar seu Raspberry Pi:

    ```sh
    sudo apt-get update
    ```

1. Use os comandos a seguir para remover qualquer instalação existente do node. js do seu Raspberry Pi:

    ```sh
    sudo apt-get remove nodered -y
    sudo apt-get remove nodejs nodejs-legacy -y
    sudo apt-get remove npm  -y
    ```

1. Use o comando a seguir para baixar e instalar o Node. js V6 em seu Raspberry Pi:

    ```sh
    curl -sL https://deb.nodesource.com/setup_6.x | sudo bash -
    sudo apt-get install nodejs npm
    ```

1. Use o comando a seguir para verificar se você instalou o Node. js v 6.11.4 com êxito:

    ```sh
    node --version
    ```

## <a name="create-a-nodejs-solution"></a>Criar uma solução node. js

Conclua as etapas a seguir usando a conexão de `ssh` com o Raspberry Pi:

1. Crie uma pasta chamada `remotemonitoring` em sua pasta base no Raspberry Pi. Navegue até essa pasta na linha de comando:

    ```sh
    cd ~
    mkdir remotemonitoring
    cd remotemonitoring
    ```

1. Para baixar e instalar os pacotes necessários para concluir o aplicativo de exemplo, execute os seguintes comandos:

    ```sh
    npm install async azure-iot-device azure-iot-device-mqtt
    ```

1. Na pasta `remotemonitoring`, crie um arquivo chamado **remote_monitoring. js**. Abra este ficheiro num editor de texto. No Raspberry Pi, você pode usar os editores de texto `nano` ou `vi`.

1. No arquivo **remote_monitoring. js** , adicione as seguintes instruções `require`:

    ```javascript
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var async = require('async');
    ```

1. Adicione as seguintes declarações de variáveis a seguir às instruções `require`. Substitua o valor do espaço reservado `{device connection string}` com o valor que você anotou para o dispositivo provisionado na solução de monitoramento remoto:

    ```javascript
    var connectionString = '{device connection string}';
    ```

1. Para definir alguns dados de telemetria base, adicione as seguintes variáveis:

    ```javascript
    var temperature = 50;
    var temperatureUnit = 'F';
    var humidity = 50;
    var humidityUnit = '%';
    var pressure = 55;
    var pressureUnit = 'psig';
    ```

1. Para definir alguns valores de propriedade, adicione as seguintes variáveis:

    ```javascript
    var schema = "real-chiller;v1";
    var deviceType = "RealChiller";
    var deviceFirmware = "1.0.0";
    var deviceFirmwareUpdateStatus = "";
    var deviceLocation = "Building 44";
    var deviceLatitude = 47.638928;
    var deviceLongitude = -122.13476;
    var deviceOnline = true;
    ```

1. Adicione a variável a seguir para definir as propriedades relatadas a serem enviadas para a solução. Essas propriedades incluem metadados para exibir na interface do usuário da Web:

    ```javascript
    var reportedProperties = {
      "SupportedMethods": "Reboot,FirmwareUpdate,EmergencyValveRelease,IncreasePressure",
      "Telemetry": {
        [schema]: ""
      },
      "Type": deviceType,
      "Firmware": deviceFirmware,
      "FirmwareUpdateStatus": deviceFirmwareUpdateStatus,
      "Location": deviceLocation,
      "Latitude": deviceLatitude,
      "Longitude": deviceLongitude,
      "Online": deviceOnline
    }
    ```

1. Para imprimir os resultados da operação, adicione a seguinte função auxiliar:

    ```javascript
    function printErrorFor(op) {
        return function printError(err) {
            if (err) console.log(op + ' error: ' + err.toString());
        };
    }
    ```

1. Adicione a seguinte função auxiliar para usar para tornar aleatório os valores de telemetria:

     ```javascript
     function generateRandomIncrement() {
         return ((Math.random() * 2) - 1);
     }
     ```

1. Adicione a seguinte função genérica para lidar com chamadas de método diretas da solução. A função exibe informações sobre o método direto que foi invocado, mas neste exemplo não modifica o dispositivo de nenhuma forma. A solução usa métodos diretos para agir em dispositivos:

     ```javascript
     function onDirectMethod(request, response) {
       // Implement logic asynchronously here.
       console.log('Simulated ' + request.methodName);

       // Complete the response
       response.send(200, request.methodName + ' was called on the device', function (err) {
         if (err) console.error('Error sending method response :\n' + err.toString());
         else console.log('200 Response to method \'' + request.methodName + '\' sent successfully.');
       });
     }
     ```

1. Adicione a seguinte função para manipular as chamadas de método direto **FirmwareUpdate** da solução. A função verifica os parâmetros passados na carga do método direto e, em seguida, executa de forma assíncrona uma simulação de atualização de firmware:

     ```javascript
     function onFirmwareUpdate(request, response) {
       // Get the requested firmware version from the JSON request body
       var firmwareVersion = request.payload.Firmware;
       var firmwareUri = request.payload.FirmwareUri;
      
       // Ensure we got a firmware values
       if (!firmwareVersion || !firmwareUri) {
         response.send(400, 'Missing firmware value', function(err) {
           if (err) console.error('Error sending method response :\n' + err.toString());
           else console.log('400 Response to method \'' + request.methodName + '\' sent successfully.');
         });
       } else {
         // Respond the cloud app for the device method
         response.send(200, 'Firmware update started.', function(err) {
           if (err) console.error('Error sending method response :\n' + err.toString());
           else {
             console.log('200 Response to method \'' + request.methodName + '\' sent successfully.');

             // Run the simulated firmware update flow
             runFirmwareUpdateFlow(firmwareVersion, firmwareUri);
           }
         });
       }
     }
     ```

1. Adicione a seguinte função para simular um fluxo de atualização de firmware de execução longa que relata o progresso de volta para a solução:

     ```javascript
     // Simulated firmwareUpdate flow
     function runFirmwareUpdateFlow(firmwareVersion, firmwareUri) {
       console.log('Simulating firmware update flow...');
       console.log('> Firmware version passed: ' + firmwareVersion);
       console.log('> Firmware URI passed: ' + firmwareUri);
       async.waterfall([
         function (callback) {
           console.log("Image downloading from " + firmwareUri);
           var patch = {
             FirmwareUpdateStatus: 'Downloading image..'
           };
           reportUpdateThroughTwin(patch, callback);
           sleep(10000, callback);
         },
         function (callback) {
           console.log("Downloaded, applying firmware " + firmwareVersion);
           deviceOnline = false;
           var patch = {
             FirmwareUpdateStatus: 'Applying firmware..',
             Online: false
           };
           reportUpdateThroughTwin(patch, callback);
           sleep(8000, callback);
         },
         function (callback) {
           console.log("Rebooting");
           var patch = {
             FirmwareUpdateStatus: 'Rebooting..'
           };
           reportUpdateThroughTwin(patch, callback);
           sleep(10000, callback);
         },
         function (callback) {
           console.log("Firmware updated to " + firmwareVersion);
           deviceOnline = true;
           var patch = {
             FirmwareUpdateStatus: 'Firmware updated',
             Online: true,
             Firmware: firmwareVersion
           };
           reportUpdateThroughTwin(patch, callback);
           callback(null);
         }
       ], function(err) {
         if (err) {
           console.error('Error in simulated firmware update flow: ' + err.message);
         } else {
           console.log("Completed simulated firmware update flow");
         }
       });

       // Helper function to update the twin reported properties.
       function reportUpdateThroughTwin(patch, callback) {
         console.log("Sending...");
         console.log(JSON.stringify(patch, null, 2));
         client.getTwin(function(err, twin) {
           if (!err) {
             twin.properties.reported.update(patch, function(err) {
               if (err) callback(err);
             });      
           } else {
             if (err) callback(err);
           }
         });
       }

       function sleep(milliseconds, callback) {
         console.log("Simulate a delay (milleseconds): " + milliseconds);
         setTimeout(function () {
           callback(null);
         }, milliseconds);
       }
     }
     ```

1. Adicione o código a seguir para enviar dados de telemetria para a solução. O aplicativo cliente adiciona propriedades à mensagem para identificar o esquema da mensagem:

     ```javascript
     function sendTelemetry(data, schema) {
       if (deviceOnline) {
         var d = new Date();
         var payload = JSON.stringify(data);
         var message = new Message(payload);
         message.properties.add('iothub-creation-time-utc', d.toISOString());
         message.properties.add('iothub-message-schema', schema);

         console.log('Sending device message data:\n' + payload);
         client.sendEvent(message, printErrorFor('send event'));
       } else {
         console.log('Offline, not sending telemetry');
       }
     }
     ```

1. Adicione o seguinte código para criar uma instância de cliente:

     ```javascript
     var client = Client.fromConnectionString(connectionString, Protocol);
     ```

1. Adicione o seguinte código a:

    * Abra a conexão.
    * Configure um manipulador para as propriedades desejadas.
    * Enviar Propriedades relatadas.
    * Registre manipuladores para os métodos diretos. O exemplo usa um manipulador separado para o método direto de atualização de firmware.
    * Comece a enviar telemetria.

      ```javascript
      client.open(function (err) {
      if (err) {
        printErrorFor('open')(err);
      } else {
        // Create device Twin
        client.getTwin(function (err, twin) {
          if (err) {
            console.error('Could not get device twin');
          } else {
            console.log('Device twin created');

            twin.on('properties.desired', function (delta) {
              // Handle desired properties set by solution
              console.log('Received new desired properties:');
              console.log(JSON.stringify(delta));
            });

            // Send reported properties
            twin.properties.reported.update(reportedProperties, function (err) {
              if (err) throw err;
              console.log('Twin state reported');
            });

            // Register handlers for all the method names we are interested in.
            // Consider separate handlers for each method.
            client.onDeviceMethod('Reboot', onDirectMethod);
            client.onDeviceMethod('FirmwareUpdate', onFirmwareUpdate);
            client.onDeviceMethod('EmergencyValveRelease', onDirectMethod);
            client.onDeviceMethod('IncreasePressure', onDirectMethod);
          }
        });

        // Start sending telemetry
        var sendDeviceTelemetry = setInterval(function () {
          temperature += generateRandomIncrement();
          pressure += generateRandomIncrement();
          humidity += generateRandomIncrement();
          var data = {
            'temperature': temperature,
            'temperature_unit': temperatureUnit,
            'humidity': humidity,
            'humidity_unit': humidityUnit,
            'pressure': pressure,
            'pressure_unit': pressureUnit
          };
          sendTelemetry(data, schema)
        }, 5000);

        client.on('error', function (err) {
          printErrorFor('client')(err);
          if (sendTemperatureInterval) clearInterval(sendTemperatureInterval);
          if (sendHumidityInterval) clearInterval(sendHumidityInterval);
          if (sendPressureInterval) clearInterval(sendPressureInterval);
          client.close(printErrorFor('client.close'));
        });
      }
      });
      ```

1. Salve as alterações no arquivo **remote_monitoring. js** .

1. Para iniciar o aplicativo de exemplo, execute o seguinte comando no prompt de comando no Raspberry Pi:

     ```sh
     node remote_monitoring.js
     ```

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
