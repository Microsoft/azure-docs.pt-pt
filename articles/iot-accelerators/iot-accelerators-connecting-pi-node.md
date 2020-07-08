---
title: Ligue raspberry Pi à solução de monitorização remota - Node.js - Azure / Microsoft Docs
description: Descreve como ligar um dispositivo Raspberry Pi ao acelerador de solução de monitorização remota utilizando uma aplicação escrita em Node.js.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 01/24/2018
ms.author: dobett
ms.custom: mqtt
ms.openlocfilehash: 9335c45688752ea41801e988157740f4170cfcb4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81683948"
---
# <a name="connect-your-raspberry-pi-device-to-the-remote-monitoring-solution-accelerator-nodejs"></a>Ligue o seu dispositivo Raspberry Pi ao acelerador de solução de monitorização remota (Node.js)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Este tutorial mostra-lhe como ligar um dispositivo real ao acelerador de solução de monitorização remota. Neste tutorial, você usa Node.js, que é uma boa opção para ambientes com restrições mínimas de recursos.

Se preferir simular um dispositivo, consulte [criar e testar um novo dispositivo simulado](iot-accelerators-remote-monitoring-create-simulated-device.md).

### <a name="required-hardware"></a>Hardware necessário

Um computador de secretária que lhe permite ligar remotamente à linha de comando do Raspberry Pi.

[Kit de arranque Microsoft IoT para raspberry Pi 3](https://azure.microsoft.com/develop/iot/starter-kits/) ou componentes equivalentes. Este tutorial utiliza os seguintes itens do kit:

- Framboesa Pi 3
- Cartão MicroSD (com NOOBS)
- Um mini cabo USB
- Um cabo Ethernet

### <a name="required-desktop-software"></a>Software de ambiente de trabalho necessário

Precisa de um cliente SSH na sua máquina de ambiente de trabalho para lhe permitir aceder remotamente à linha de comando do Raspberry Pi.

- O Windows não inclui um cliente SSH. Recomendamos a utilização [de PuTTY](https://www.putty.org/).
- A maioria das distribuições linux e Mac OS incluem o utilitário SSH de linha de comando. Para obter mais informações, consulte [SSH Usando Linux ou Mac OS](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md).

### <a name="required-raspberry-pi-software"></a>Software Raspberry Pi obrigatório

Se ainda não o fez, instale Node.js versão 4.0.0 ou posterior no seu Raspberry Pi. Os seguintes passos mostram-lhe como instalar Node.js v6 no seu Raspberry Pi:

1. Ligue-se ao seu Raspberry Pi utilizando `ssh` . Para mais informações, consulte [SSH (Secure Shell)](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md) no [site raspberry Pi](https://www.raspberrypi.org/).

1. Utilize o seguinte comando para atualizar o seu Raspberry Pi:

    ```sh
    sudo apt-get update
    ```

1. Utilize os seguintes comandos para remover qualquer instalação existente de Node.js do seu Raspberry Pi:

    ```sh
    sudo apt-get remove nodered -y
    sudo apt-get remove nodejs nodejs-legacy -y
    sudo apt-get remove npm  -y
    ```

1. Utilize o seguinte comando para descarregar e instalar Node.js v6 no seu Raspberry Pi:

    ```sh
    curl -sL https://deb.nodesource.com/setup_6.x | sudo bash -
    sudo apt-get install nodejs npm
    ```

1. Utilize o seguinte comando para verificar se instalou Node.js v6.11.4 com sucesso:

    ```sh
    node --version
    ```

## <a name="create-a-nodejs-solution"></a>Criar uma solução Node.js

Complete os seguintes passos utilizando `ssh` a ligação ao seu Raspberry Pi:

1. Crie uma pasta chamada `remotemonitoring` na sua pasta de casa na Raspberry Pi. Navegue para esta pasta na sua linha de comando:

    ```sh
    cd ~
    mkdir remotemonitoring
    cd remotemonitoring
    ```

1. Para descarregar e instalar os pacotes necessários para completar a aplicação da amostra, execute os seguintes comandos:

    ```sh
    npm install async azure-iot-device azure-iot-device-mqtt
    ```

1. Na `remotemonitoring` pasta, crie um ficheiro chamado **remote_monitoring.js**. Abra este ficheiro num editor de texto. No Raspberry Pi, você pode usar os `nano` editores ou `vi` editores de texto.

1. No ficheiro **remote_monitoring.js,** adicione as `require` seguintes declarações:

    ```javascript
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var async = require('async');
    ```

1. Adicione as seguintes declarações de variáveis a seguir às instruções `require`. Substitua o valor do espaço reservado `{device connection string}` pelo valor que notou para o dispositivo que aprovisionou na solução de Monitorização Remota:

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

1. Adicione a seguinte variável para definir as propriedades reportadas para enviar para a solução. Estas propriedades incluem metadados para exibir na UI Web:

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

1. Para imprimir os resultados da operação, adicione a seguinte função de ajudante:

    ```javascript
    function printErrorFor(op) {
        return function printError(err) {
            if (err) console.log(op + ' error: ' + err.toString());
        };
    }
    ```

1. Adicione a seguinte função de ajudante para utilizar para aleatoriamente os valores de telemetria:

     ```javascript
     function generateRandomIncrement() {
         return ((Math.random() * 2) - 1);
     }
     ```

1. Adicione a seguinte função genérica para lidar com chamadas de métodos diretos a partir da solução. A função apresenta informações sobre o método direto que foi invocado, mas nesta amostra não modifica o dispositivo de forma alguma. A solução utiliza métodos diretos para agir em dispositivos:

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

1. Adicione a seguinte função para manusear as chamadas diretas do método **FirmwareUpdate** a partir da solução. A função verifica os parâmetros passados na carga útil do método direto e, em seguida, assíncronea executa uma simulação de atualização de firmware:

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

1. Adicione a seguinte função para simular um fluxo de atualização de firmware de longa duração que reporte o progresso de volta à solução:

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

1. Adicione o seguinte código para enviar dados de telemetria à solução. A aplicação do cliente adiciona propriedades à mensagem para identificar o esquema de mensagem:

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

1. Adicione o seguinte código para criar uma instância do cliente:

     ```javascript
     var client = Client.fromConnectionString(connectionString, Protocol);
     ```

1. Adicione o seguinte código a:

    * Abra a ligação.
    * Crie um manipulador para as propriedades desejadas.
    * Envie propriedades reportadas.
    * Registar os manipuladores para os métodos diretos. A amostra utiliza um manipulador separado para o método direto de atualização de firmware.
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

1. Guarde as alterações no ficheiro **remote_monitoring.js.**

1. Para lançar a aplicação da amostra, executar o seguinte comando no seu comando na Raspberry Pi:

     ```sh
     node remote_monitoring.js
     ```

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
