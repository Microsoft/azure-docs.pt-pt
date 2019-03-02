---
title: Ligar uma aplicação de cliente genérica do node. js para o Azure IoT Central | Documentos da Microsoft
description: Como um desenvolvedor de dispositivo, como ligar um dispositivo genérico do node. js à sua aplicação do Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 02/04/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: ba5a8cc09a098e58c39d42475d18e92e45222937
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57216786"
---
# <a name="connect-a-generic-client-application-to-your-azure-iot-central-application-nodejs"></a>Ligar uma aplicação de cliente genérico à sua aplicação do Azure IoT Central (node. js)

Este artigo descreve como, como um desenvolvedor de dispositivo, para ligar uma aplicação node. js genérica que representa um dispositivo real à sua aplicação do Microsoft Azure IoT Central.

## <a name="before-you-begin"></a>Antes de começar

Para executar os passos descritos neste artigo é necessário o seguinte:

1. Uma aplicação do Azure IoT Central. Para obter mais informações, veja [criar um início rápido da aplicação](quick-deploy-iot-central-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).
1. Uma máquina de desenvolvimento com [node. js](https://nodejs.org/) versão 4.0.0 ou posterior instalado. Pode executar `node --version` na linha de comandos para verificar a sua versão. O Node.js está disponível para uma grande variedade de sistemas operativos.

## <a name="create-a-device-template"></a>Criar um modelo de dispositivo

Na aplicação do Azure IoT Central, terá de um modelo de dispositivo com as seguintes medidas e propriedades de dispositivo definidas:

### <a name="telemetry-measurements"></a>Medições de telemetria

Adicione a seguinte telemetria no **medidas** página:

| Nome a Apresentar | Nome do Campo  | Unidades | Mín. | Máx. | Casas Decimais |
| ------------ | ----------- | ----- | --- | --- | -------------- |
| Temperatura  | temperatura | F     | 60  | 110 | 0              |
| Humidade     | humidade    | %     | 0   | 100 | 0              |
| Pressão     | pressure    | kPa   | 80  | 110 | 0              |

> [!NOTE]
  O tipo de dados da medição de telemetria é uma vírgula flutuante ponto número.

Introduza os nomes de campos exatamente como mostrados na tabela no modelo de dispositivo. Se os nomes de campos não coincidem com os nomes de propriedade no código de dispositivo correspondente, a telemetria não é possível apresentar na aplicação.

### <a name="state-measurements"></a>Medidas de estado

Adicione o seguinte estado no **medidas** página:

| Nome a Apresentar | Nome do Campo  | Valor 1 | Nome a Apresentar | Valor 2 | Nome a Apresentar |
| ------------ | ----------- | --------| ------------ | ------- | ------------ | 
| Modo da Ventoinha     | fanmode     | 1       | A executar      | 0       | Parada      |

> [!NOTE]
  O tipo de dados da medição de estado é a cadeia de caracteres.

Introduza os nomes de campos exatamente como mostrados na tabela no modelo de dispositivo. Se os nomes de campos não coincidem com os nomes de propriedade no código de dispositivo correspondente, o estado não é possível apresentar na aplicação.

### <a name="event-measurements"></a>Medições de eventos

Adicione o seguinte evento no **medidas** página:

| Nome a Apresentar | Nome do Campo  | Gravidade |
| ------------ | ----------- | -------- |
| Sobreaquecimento  | superaquecimento    | Erro    |

> [!NOTE]
  O tipo de dados da medição de eventos é a cadeia de caracteres.

### <a name="device-properties"></a>Propriedades do dispositivo

Adicione as seguintes propriedades do dispositivo na **página de propriedades**:

| Nome a Apresentar        | Nome do Campo        | Tipo de dados |
| ------------------- | ----------------- | --------- |
| Número de série       | serialNumber      | texto      |
| Fabricante do dispositivo | fabricante      | texto      |

Introduza os nomes de campo exatamente como mostrados na tabela no modelo de dispositivo. Se os nomes de campos não coincidem com os nomes de propriedade no código de dispositivo correspondente, o aplicativo não é possível mostrar o valor de propriedade do dispositivo.

### <a name="settings"></a>Definições

Adicione as seguintes **número** definições no **página de definições**:

| Nome a Apresentar    | Nome do Campo     | Unidades | Casas decimais | Mín. | Máx.  | Inicial |
| --------------- | -------------- | ----- | -------- | --- | ---- | ------- |
| Ventoinha velocidade       | fanSpeed       | rpm   | 0        | 0   | 3000 | 0       |
| Definir Temperatura | setTemperature | F     | 0        | 20  | 200  | 80      |

Introduza o nome do campo exatamente como mostrados na tabela no modelo de dispositivo. Se os nomes de campos não coincidem com os nomes de propriedade no código de dispositivo correspondente, o dispositivo não pode receber o valor de definição.

## <a name="add-a-real-device"></a>Adicionar um dispositivo real

Na aplicação do Azure IoT Central, adicione um dispositivo real a partir do modelo de dispositivo, criar e tome nota da cadeia de ligação do dispositivo. Para obter instruções passo a passo sobre como ligar uma aplicação node. js ao IoT Central, consulte [gerar cadeia de ligação para o dispositivo real do aplicativo](tutorial-add-device-experimental.md#generate-connection-string) e [preparar o código de cliente](tutorial-add-device-experimental.md#prepare-the-client-code) nos tutoriais > Adicione um dispositivo.

### <a name="create-a-nodejs-application"></a>Criar uma aplicação Node.js

Os passos seguintes mostram como criar uma aplicação de cliente que implementa o dispositivo real adicionados à aplicação. Aqui, a aplicação node. js representa o dispositivo real. 

1. Crie uma pasta denominada `connected-air-conditioner-adv` no computador. Navegue para essa pasta no seu ambiente de linha de comandos.

1. Para inicializar o seu projeto de node. js, execute os seguintes comandos:

    ```cmd/sh
    npm init
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. Crie um ficheiro chamado **connectedAirConditionerAdv.js** no `connected-air-conditioner-adv` pasta.

1. Adicione as seguintes `require` instruções no início do **connectedAirConditionerAdv.js** ficheiro:

    ```javascript
    "use strict";

    // Use the Azure IoT device SDK for devices that connect to Azure IoT Central.
    var clientFromConnectionString = require('azure-iot-device-mqtt').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    var ConnectionString = require('azure-iot-device').ConnectionString;
    ```

1. Adicione as seguintes declarações de variáveis ao ficheiro:

    ```javascript
    var connectionString = '{your device connection string}';
    var targetTemperature = 0;
    var client = clientFromConnectionString(connectionString);
    ```

    > [!NOTE]
    > O Azure IoT Central transitou para utilizar o serviço de aprovisionamento de dispositivos do Azure IoT Hub (DPS) para todas as ligações de dispositivo, siga estas instruções para [obter a cadeia de ligação do dispositivo](concepts-connectivity-experimental.md#get-a-connection-string) e continuar com o resto do tutorial. Para obter mais ajuda, também pode encontrar um conjunto detalhado de instruções em [preparar o código de cliente](tutorial-add-device-experimental.md#prepare-the-client-code) nos tutoriais > Adicionar um dispositivo.

    Atualizar o marcador de posição `{your device connection string}` com a cadeia de ligação do dispositivo. Neste exemplo, vamos inicializar `targetTemperature` para zero, pode, opcionalmente, efetuar a leitura atual do dispositivo ou o valor do dispositivo duplo. 

1. Para enviar medições de telemetria, o estado e o evento para a aplicação Azure IoT Central, adicione a seguinte função para o ficheiro:

    ```javascript
    // Send device measurements.
    function sendTelemetry() {
      var temperature = targetTemperature + (Math.random() * 15);
      var humidity = 70 + (Math.random() * 10);
      var pressure = 90 + (Math.random() * 5);
      var fanmode = 0;
      var data = JSON.stringify({ 
        temperature: temperature, 
        humidity: humidity, 
        pressure: pressure,
        fanmode: (temperature > 25) ? "1" : "0",
        overheat: (temperature > 35) ? "ER123" : undefined });
      var message = new Message(data);
      client.sendEvent(message, (err, res) => console.log(`Sent message: ${message.getData()}` +
        (err ? `; error: ${err.toString()}` : '') +
        (res ? `; status: ${res.constructor.name}` : '')));
    }
    ```

1. Para enviar as propriedades dos dispositivos à sua aplicação do Azure IoT Central, adicione a seguinte função para o ficheiro:

    ```javascript
    // Send device properties.
    function sendDeviceProperties(twin) {
      var properties = {
        serialNumber: '123-ABC',
        manufacturer: 'Contoso'
      };
      twin.properties.reported.update(properties, (err) => console.log(`Sent device properties; ` +
        (err ? `error: ${err.toString()}` : `status: success`)));
    }
    ```

1. Para definir as definições do que seu dispositivo responde a, adicione a seguinte definição:

    ```javascript
    // Add any settings your device supports,
    // mapped to a function that is called when the setting is changed.
    var settings = {
      'fanSpeed': (newValue, callback) => {
          // Simulate it taking 1 second to set the fan speed.
          setTimeout(() => {
            callback(newValue, 'completed');
          }, 1000);
      },
      'setTemperature': (newValue, callback) => {
        // Simulate the temperature setting taking two steps.
        setTimeout(() => {
          targetTemperature = targetTemperature + (newValue - targetTemperature) / 2;
          callback(targetTemperature, 'pending');
          setTimeout(() => {
            targetTemperature = newValue;
            callback(targetTemperature, 'completed');
          }, 5000);
        }, 5000);
      }
    };
    ```

1. Para lidar com as definições atualizadas a partir da sua aplicação do Azure IoT Central, adicione o seguinte ao ficheiro:

    ```javascript
    // Handle settings changes that come from Azure IoT Central via the device twin.
    function handleSettings(twin) {
      twin.on('properties.desired', function (desiredChange) {
        for (let setting in desiredChange) {
          if (settings[setting]) {
            console.log(`Received setting: ${setting}: ${desiredChange[setting].value}`);
            settings[setting](desiredChange[setting].value, (newValue, status, message) => {
              var patch = {
                [setting]: {
                  value: newValue,
                  status: status,
                  desiredVersion: desiredChange.$version,
                  message: message
                }
              }
              twin.properties.reported.update(patch, (err) => console.log(`Sent setting update for ${setting}; ` +
                (err ? `error: ${err.toString()}` : `status: success`)));
            });
          }
        }
      });
    }
    ```

1. Adicione o seguinte para concluir a ligação ao Azure IoT Central e conectar as funções no código do cliente:

    ```javascript
    // Handle device connection to Azure IoT Central.
    var connectCallback = (err) => {
      if (err) {
        console.log(`Device could not connect to Azure IoT Central: ${err.toString()}`);
      } else {
        console.log('Device successfully connected to Azure IoT Central');

        // Send telemetry measurements to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);

        // Get device twin from Azure IoT Central.
        client.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send device properties once on device start up.
            sendDeviceProperties(twin);
            // Apply device settings and handle changes to device settings.
            handleSettings(twin);
          }
        });
      }
    };

    // Start the device (connect it to Azure IoT Central).
    client.open(connectCallback);
    ```

## <a name="run-your-nodejs-application"></a>Execute a sua aplicação node. js

Execute o seguinte comando no seu ambiente de linha de comandos:

```cmd/sh
node connectedAirConditionerAdv.js
```

Como um operador na sua aplicação do Azure IoT Central, para o seu dispositivo real, pode:

* Ver a telemetria sobre o **medidas** página:

    ![Ver telemetria](media/howto-connect-nodejs-experimental/viewtelemetry.png)

* Ver os valores de propriedade do dispositivo enviados a partir do seu dispositivo na **propriedades** página. A dispositivo propriedades mosaicos atualização se a ligação for bem-sucedida.

    ![Ver as propriedades do dispositivo](media/howto-connect-nodejs-experimental/viewproperties.png)

* Definir a temperatura de velocidade e o destino de fã do **definições** página. Os valores de definições de sincronização se a ligação for bem-sucedida.

    ![Velocidade de fã de conjunto](media/howto-connect-nodejs-experimental/setfanspeed.png)

## <a name="next-steps"></a>Passos Seguintes

Agora que sabe como ligar um cliente genérico do node. js à sua aplicação do Azure IoT Central, aqui estão os passos sugeridos seguintes:
* [Preparar e ligue-se um Raspberry Pi](howto-connect-raspberry-pi-python.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
<!-- Next how-tos in the sequence -->
