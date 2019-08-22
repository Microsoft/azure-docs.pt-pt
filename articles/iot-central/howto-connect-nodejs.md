---
title: Conectar um aplicativo cliente Node. js genérico ao Azure IoT Central | Microsoft Docs
description: Como um desenvolvedor de dispositivos, como conectar um dispositivo node. js genérico ao aplicativo IoT Central do Azure.
author: dominicbetts
ms.author: dobett
ms.date: 06/14/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 3b73344a233182fe8366795cfa111b706c6d06ac
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876238"
---
# <a name="connect-a-generic-client-application-to-your-azure-iot-central-application-nodejs"></a>Conectar um aplicativo cliente genérico ao seu aplicativo de IoT Central do Azure (Node. js)

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

Este artigo descreve como, como um desenvolvedor de dispositivos, conectar um aplicativo node. js genérico que representa um dispositivo real ao seu aplicativo Microsoft Azure IoT Central.

## <a name="before-you-begin"></a>Antes de começar

Para executar os passos descritos neste artigo é necessário o seguinte:

1. Um aplicativo IoT Central do Azure. Para obter mais informações, veja [criar um início rápido da aplicação](quick-deploy-iot-central.md).
1. Um computador de desenvolvimento com o [node. js](https://nodejs.org/) versão 4.0.0 ou posterior instalado. Você pode executar `node --version` na linha de comando para verificar sua versão. O Node.js está disponível para uma grande variedade de sistemas operativos.

## <a name="create-a-device-template"></a>Criar um modelo de dispositivo

No aplicativo IoT Central do Azure, você precisa de um modelo de dispositivo com as seguintes medidas, propriedades do dispositivo, configurações e comandos:

### <a name="telemetry-measurements"></a>Medições de telemetria

Adicione a seguinte telemetria na página **medidas** :

| Nome a Apresentar | Nome do Campo  | Unidades | Mín. | Máx. | Casas Decimais |
| ------------ | ----------- | ----- | --- | --- | -------------- |
| Temperatura  | temperatura | S     | 60  | 110 | 0              |
| Humidade     | humidade    | %     | 0   | 100 | 0              |
| Pressão     | pressure    | kPa   | 80  | 110 | 0              |

> [!NOTE]
> O tipo de dados da medição de telemetria é um número de ponto flutuante.

Insira nomes de campo exatamente como mostrado na tabela no modelo de dispositivo. Se os nomes de campo não corresponderem aos nomes de propriedade no código de dispositivo correspondente, a telemetria não poderá ser exibida no aplicativo.

### <a name="state-measurements"></a>Medidas de estado

Adicione o seguinte estado na página **medidas** :

| Nome a Apresentar | Nome do Campo  | Valor 1 | Nome a Apresentar | Valor 2 | Nome a Apresentar |
| ------------ | ----------- | --------| ------------ | ------- | ------------ | 
| Modo da Ventoinha     | fanmode     | 1       | A executar      | 0       | Parada      |

> [!NOTE]
> O tipo de dados da medida de estado é cadeia de caracteres.

Insira nomes de campo exatamente como mostrado na tabela no modelo de dispositivo. Se os nomes de campo não corresponderem aos nomes de propriedade no código de dispositivo correspondente, o estado não poderá ser exibido no aplicativo.

### <a name="event-measurements"></a>Medições de evento

Adicione o seguinte evento na página **medidas** :

| Nome a Apresentar | Nome do Campo  | Severity |
| ------------ | ----------- | -------- |
| Superaquecimento  | aquecido    | Erro    |

> [!NOTE]
> O tipo de dados da medição do evento é String.

### <a name="location-measurements"></a>Medidas de localização

Adicione a seguinte medida de localização na página **medidas** :

| Nome a Apresentar | Nome do Campo  |
| ------------ | ----------- |
| Location     | location    |

O tipo de dados de medição local é composto de dois números de ponto flutuante para longitude e latitude e um número de ponto flutuante opcional para altitude.

Insira nomes de campo exatamente como mostrado na tabela no modelo de dispositivo. Se os nomes de campo não corresponderem aos nomes de propriedade no código de dispositivo correspondente, o local não poderá ser exibido no aplicativo.

### <a name="device-properties"></a>Propriedades do dispositivo

Adicione as seguintes propriedades de dispositivo na página **Propriedades** :

| Nome a Apresentar        | Nome do Campo        | Tipo de dados |
| ------------------- | ----------------- | --------- |
| Número de Série       | serialNumber      | text      |
| Fabricante do dispositivo | fabricante      | text      |

Insira os nomes de campo exatamente como mostrado na tabela no modelo de dispositivo. Se os nomes de campo não corresponderem aos nomes de propriedade no código de dispositivo correspondente, as propriedades não poderão ser exibidas no aplicativo.

### <a name="settings"></a>Definições

Adicione as seguintes configurações de **número** na página **configurações** :

| Nome a Apresentar    | Nome do Campo     | Unidades | Decimais | Mín. | Máx.  | Inicial |
| --------------- | -------------- | ----- | -------- | --- | ---- | ------- |
| Velocidade do ventilador       | fanSpeed       | rpm   | 0        | 0   | 3000 | 0       |
| Definir Temperatura | setTemperature | S     | 0        | 20  | 200  | 80      |

Insira o nome do campo exatamente como mostrado na tabela no modelo de dispositivo. Se os nomes de campo não corresponderem aos nomes de propriedade no código do dispositivo correspondente, o dispositivo não poderá receber o valor da configuração.

### <a name="commands"></a>Comandos

Adicione o seguinte comando na página **comandos** :

| Nome a Apresentar    | Nome do Campo     | Tempo Limite Predefinido | Tipo de Dados |
| --------------- | -------------- | --------------- | --------- |
| Contagem regressiva       | contagem regressiva      | 30              | número    |

Adicione o seguinte campo de entrada ao comando de contagem regressiva:

| Nome a Apresentar    | Nome do Campo     | Tipo de Dados | Value |
| --------------- | -------------- | --------- | ----- |
| Contar de      | countFrom      | número    | 10    |

Insira nomes de campo exatamente como mostrado nas tabelas no modelo de dispositivo. Se os nomes de campo não corresponderem aos nomes de propriedade no código de dispositivo correspondente, o dispositivo não poderá processar o comando.

## <a name="add-a-real-device"></a>Adicionar um dispositivo real

No aplicativo IoT Central do Azure, adicione um dispositivo real ao modelo de dispositivo que você criou na seção anterior.

Em seguida, siga as instruções no tutorial "adicionar um dispositivo" para [gerar uma cadeia de conexão para o dispositivo real](tutorial-add-device.md#generate-connection-string). Você usa essa cadeia de conexão na seguinte seção:

### <a name="create-a-nodejs-application"></a>Criar uma aplicação Node.js

As etapas a seguir mostram como criar um aplicativo cliente que implementa o dispositivo real que você adicionou ao aplicativo. Aqui, o aplicativo node. js representa o dispositivo real. 

1. Crie uma pasta denominada `connected-air-conditioner-adv` no computador. Navegue até essa pasta em seu ambiente de linha de comando.

1. Para inicializar o projeto Node. js, execute os seguintes comandos:

    ```cmd/sh
    npm init
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. Crie um arquivo chamado **connectedAirConditionerAdv. js** na `connected-air-conditioner-adv` pasta.

1. Adicione as seguintes `require` instruções no início do arquivo **connectedAirConditionerAdv. js** :

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
    var locLong = -122.1215;
    var locLat = 47.6740;
    var client = clientFromConnectionString(connectionString);
    ```

    Atualize o espaço `{your device connection string}` reservado com a [cadeia de conexão do dispositivo](tutorial-add-device.md#generate-connection-string). Neste exemplo, você inicializa `targetTemperature` como zero, pode usar a leitura atual do dispositivo ou um valor do dispositivo.

1. Para enviar medidas de telemetria, estado, evento e local para o aplicativo de IoT Central do Azure, adicione a seguinte função ao arquivo:

    ```javascript
    // Send device measurements.
    function sendTelemetry() {
      var temperature = targetTemperature + (Math.random() * 15);
      var humidity = 70 + (Math.random() * 10);
      var pressure = 90 + (Math.random() * 5);
      var fanmode = 0;
      var locationLong = locLong - (Math.random() / 100);
      var locationLat = locLat - (Math.random() / 100);
      var data = JSON.stringify({
        temperature: temperature,
        humidity: humidity,
        pressure: pressure,
        fanmode: (temperature > 25) ? "1" : "0",
        overheat: (temperature > 35) ? "ER123" : undefined,
        location: {
            lon: locationLong,
            lat: locationLat }
        });
      var message = new Message(data);
      client.sendEvent(message, (err, res) => console.log(`Sent message: ${message.getData()}` +
        (err ? `; error: ${err.toString()}` : '') +
        (res ? `; status: ${res.constructor.name}` : '')));
    }
    ```

1. Para enviar Propriedades de dispositivo para o aplicativo de IoT Central do Azure, adicione a seguinte função ao seu arquivo:

    ```javascript
    // Send device reported properties.
    function sendDeviceProperties(twin, properties) {
      twin.properties.reported.update(properties, (err) => console.log(`Sent device properties: ${JSON.stringify(properties)}; ` +
        (err ? `error: ${err.toString()}` : `status: success`)));
    }
    ```

1. Para definir as configurações às quais seu dispositivo responde, adicione a seguinte definição:

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

1. Para tratar as configurações atualizadas do aplicativo IoT Central do Azure, adicione o seguinte ao arquivo:

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

1. Adicione o seguinte código para manipular um comando de contagem regressiva enviado do aplicativo IoT Central:

    ```javascript
    // Handle countdown command
    function onCountdown(request, response) {
      console.log('Received call to countdown');

      var countFrom = (typeof(request.payload.countFrom) === 'number' && request.payload.countFrom < 100) ? request.payload.countFrom : 10;

      response.send(200, (err) => {
        if (err) {
          console.error('Unable to send method response: ' + err.toString());
        } else {
          client.getTwin((err, twin) => {
            function doCountdown(){
              if ( countFrom >= 0 ) {
                var patch = {
                  countdown:{
                    value: countFrom
                  }
                };
                sendDeviceProperties(twin, patch);
                countFrom--;
                setTimeout(doCountdown, 2000 );
              }
            }

            doCountdown();
          });
        }
      });
    }
    ```

1. Adicione o seguinte código para concluir a ligação ao Azure IoT Central e ligar as funções no código de cliente:

    ```javascript
    // Handle device connection to Azure IoT Central.
    var connectCallback = (err) => {
      if (err) {
        console.log(`Device could not connect to Azure IoT Central: ${err.toString()}`);
      } else {
        console.log('Device successfully connected to Azure IoT Central');

        // Create handler for countdown command
        client.onDeviceMethod('countdown', onCountdown);

        // Send telemetry measurements to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);

        // Get device twin from Azure IoT Central.
        client.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send device properties once on device start up.
            var properties = {
              serialNumber: '123-ABC',
              manufacturer: 'Contoso'
            };
            sendDeviceProperties(twin, properties);

            // Apply device settings and handle changes to device settings.
            handleSettings(twin);
          }
        });
      }
    };

    // Start the device (connect it to Azure IoT Central).
    client.open(connectCallback);
    ```

## <a name="run-your-nodejs-application"></a>Executar seu aplicativo node. js

Execute o seguinte comando em seu ambiente de linha de comando:

```cmd/sh
node connectedAirConditionerAdv.js
```

Como um operador em seu aplicativo de IoT Central do Azure, para seu dispositivo real, você pode:

* Exiba a telemetria na página **medidas** :

    ![Ver telemetria](media/howto-connect-nodejs/viewtelemetry.png)

* Exiba o local na página **medidas** :

    ![Exibir medidas de local](media/howto-connect-nodejs/viewlocation.png)

* Exiba os valores de Propriedade do dispositivo enviados do seu dispositivo na página **Propriedades** . Os blocos de propriedades do dispositivo são atualizados quando o dispositivo se conecta:

    ![Exibir Propriedades do dispositivo](media/howto-connect-nodejs/viewproperties.png)

* Defina a velocidade do ventilador e a temperatura de destino na página **configurações** :

    ![Definir a velocidade do ventilador](media/howto-connect-nodejs/setfanspeed.png)

* Chame o comando de contagem regressiva na página **comandos** :

    ![Comando chamar contagem regressiva](media/howto-connect-nodejs/callcountdown.png)

## <a name="next-steps"></a>Passos seguintes

Agora que você aprendeu como conectar um cliente Node. js genérico ao aplicativo IoT Central do Azure, a próxima etapa sugerida é aprender a [configurar um modelo de dispositivo personalizado](howto-set-up-template.md) para seu próprio dispositivo IOT.
