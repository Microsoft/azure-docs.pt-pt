---
title: Tutorial - Ligue uma aplicação genérica de clientes Node.js à Central Azure IoT [ Microsoft Docs
description: Este tutorial mostra-lhe como, como desenvolvedor de dispositivos, ligar um dispositivo que executa uma aplicação de cliente Node.js à sua aplicação Azure IoT Central. Cria um modelo de dispositivo importando um modelo de capacidade do dispositivo e adiciona ndo pontos de vista que lhe permitem interagir com um dispositivo conectado
author: dominicbetts
ms.author: dobett
ms.date: 02/26/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 1bcfc949eff0639dd1b4a063687e2c198f480ea3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "77624544"
---
# <a name="tutorial-create-and-connect-a-nodejs-client-application-to-your-azure-iot-central-application-nodejs"></a>Tutorial: Crie e ligue uma aplicação de cliente Node.js à sua aplicação Central Azure IoT (Node.js)

Este tutorial mostra-lhe como, como desenvolvedor de dispositivos, ligar uma aplicação de cliente Node.js à sua aplicação Azure IoT Central. A aplicação Node.js simula o comportamento de um dispositivo real. Utiliza um _modelo_ de capacidade de dispositivo de amostra para um dispositivo de sensor ambiental para criar um modelo de _dispositivo_ na IoT Central. Adiciona vistas ao modelo do dispositivo para que visualize a telemetria do dispositivo, gere as propriedades do dispositivo e utilize comandos para controlar os seus dispositivos.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Importar um modelo de capacidade de dispositivo para criar um modelo de dispositivo.
> * Adicione vistas padrão e personalizadas a um modelo de dispositivo.
> * Publique um modelo de dispositivo e adicione um dispositivo real à sua aplicação IoT Central.
> * Crie e execute o código do dispositivo Node.js e consulte-o ligado à sua aplicação IoT Central.
> * Veja a telemetria simulada que o dispositivo envia.
> * Utilize uma vista para gerir as propriedades do dispositivo.
> * Ligue para controlar o dispositivo.

## <a name="prerequisites"></a>Pré-requisitos

Para executar os passos descritos neste artigo é necessário o seguinte:

* Uma aplicação Azure IoT Central criada com o modelo **Custom application **. Para obter mais informações, veja [criar um início rápido da aplicação](quick-deploy-iot-central.md).
* Uma máquina de desenvolvimento com a versão [Nó.js](https://nodejs.org/) 10.0.0 ou posteriormente instalada. Pode correr `node --version` na linha de comando para verificar a sua versão. O Node.js está disponível para uma grande variedade de sistemas operativos. As instruções deste tutorial pressupõem que está a executar o comando do **nó** no pedido de comando do Windows. Você pode usar Node.js em uma variedade de sistemas operativos.

## <a name="create-a-device-template"></a>Criar um modelo de dispositivo

Crie uma `environmental-sensor` pasta chamada na sua máquina local.

Descarregue o ficheiro JSON da capacidade `environmental-sensor` do sensor [ambiental](https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensorInline.capabilitymodel.json) e guarde-o na pasta.

Utilize um editor de texto para `{YOUR_COMPANY_NAME_HERE}` substituir as duas `EnvironmentalSensorInline.capabilitymodel.json` instâncias do nome da sua empresa no ficheiro que descarregou.

Na sua aplicação Azure IoT Central, crie um `EnvironmentalSensorInline.capabilitymodel.json` modelo de dispositivo chamado sensor *ambiental* importando o ficheiro do modelo de capacidade do dispositivo:

![Modelo de dispositivo com modelo de capacidade de dispositivo importado](./media/tutorial-connect-device/device-template.png)

O modelo de capacidade do dispositivo inclui duas interfaces: a interface padrão de **informação** do dispositivo e a interface personalizada do **Sensor Ambiental.** A interface **Sensor Ambiental** define as seguintes capacidades:

| Tipo | Nome a Apresentar | Descrição |
| ---- | ------------ | ----------- |
| Propriedade | Estado do Dispositivo     | O estado do dispositivo. Dois estados online/offline estão disponíveis. |
| Propriedade | Nome do cliente    | O nome do cliente que está a operar o dispositivo. |
| Propriedade | Nível de brilho | O nível de luminosidade para a luz no dispositivo. Pode ser especificado como 1 (alto), 2 (médio), 3 (baixo). |
| Telemetria | Temperatura | Temperatura atual no dispositivo. |
| Telemetria | Humidade    | Humidade atual no dispositivo. |
| Comando | piscar          | Comece a piscar o LED para um determinado intervalo de tempo. |
| Comando | turnon         | Ligue a luz LED do dispositivo. |
| Comando | desvio        | Desligue a luz LED do dispositivo. |
| Comando | diagnósticos de execução | Este comando inicia uma execução de diagnóstico. |

Para personalizar a forma como a propriedade **do Estado** do Dispositivo exibe na sua aplicação IoT Central, selecione **Personalizar** no modelo do dispositivo. Expandir a entrada do Estado do **Dispositivo,** introduzir _online_ como nome **verdadeiro** e _offline_ como o **nome falso**. Em seguida, guarde as alterações:

![Personalize o modelo do dispositivo](media/tutorial-connect-device/customize-template.png)

## <a name="create-views"></a>Criar vistas

As vistas permitem-lhe interagir com dispositivos ligados à sua aplicação IoT Central. Por exemplo, você pode ter vistas que exibem telemetria, vistas que exibem propriedades e vistas que permitem editar propriedades reempreensíveis e cloud. As vistas fazem parte de um modelo de dispositivo.

Para adicionar algumas vistas predefinidas ao seu modelo de **dispositivo de sensor Ambiental,** navegue para o seu modelo de dispositivo, selecione **Views**, e selecione o azulejo de **vistas Depreto sê-lo.** Certifique-se de que a **visão geral** e **sobre** estão **ligados**, e, em seguida, selecione **Generate vistas padrão do dashboard**. Tem agora duas visões predefinidas definidas no seu modelo.

A interface **sensor ambiental** inclui duas propriedades reempreensíveis - Nome **do Cliente** e **Nível de Brilho**. Para criar uma vista, pode usar para editar estas propriedades:

1. Selecione **Views** e, em seguida, selecione o **dispositivo de edição e** o azulejo de dados em nuvem.

1. _Insira propriedades_ como nome de formulário.

1. Selecione as propriedades **do Nível de Luminosidade** e do Nome do **Cliente.** Em seguida, selecione **a secção Adicionar**.

1. Guarde as alterações.

![Adicione uma vista para permitir a edição de propriedades](media/tutorial-connect-device/properties-view.png)

## <a name="publish-the-template"></a>Publicar o modelo

Antes de adicionar um dispositivo à sua aplicação IoT Central que utiliza o modelo de **dispositivo de sensor Ambiental,** tem de publicar o modelo.

No modelo do dispositivo, **selecione Publicar**. No painel que mostra as alterações a publicar, **selecione Publicar**.

Para verificar se o modelo está pronto a ser utilizado, navegue para a página **dispositivos** na sua aplicação IoT Central. A secção **Dispositivos** mostra uma lista dos dispositivos publicados na aplicação:

![Modelos publicados na página de dispositivos](media/tutorial-connect-device/published-templates.png)

## <a name="add-a-real-device"></a>Adicionar um dispositivo real

Na sua aplicação Azure IoT Central, adicione um dispositivo real ao modelo de dispositivo que criou na secção anterior:

1. Na página **Dispositivos,** selecione o modelo do dispositivo **de sensor ambiental.**

1. Selecione **+ Novo**.

1. Certifique-se de que a **Simulação** está **desligada**. Em seguida, selecione **Criar**.

Clique no nome do dispositivo e, em seguida, selecione **Connect**. Tome nota das informações de ligação do dispositivo na página de **Ligação** do Dispositivo - **âmbito de identificação,** **ID do dispositivo**e chave **primária**. Precisa destes valores quando cria o código do dispositivo:

![Informações de ligação ao dispositivo](./media/tutorial-connect-device/device-connection.png)

### <a name="create-a-nodejs-application"></a>Criar uma aplicação Node.js

Os seguintes passos mostram-lhe como criar uma aplicação de cliente Node.js que implementa o dispositivo real que adicionou à aplicação. Esta aplicação Node.js simula o comportamento de um dispositivo real.

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

1. Para enviar telemetria para a sua aplicação Azure IoT Central, adicione a seguinte função ao ficheiro:

    ```javascript
    // Send device measurements.
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

1. Para enviar propriedades do dispositivo para a sua aplicação Azure IoT Central, adicione a seguinte função ao seu ficheiro:

    ```javascript
    // Send device reported properties.
    function sendDeviceProperties(twin, properties) {
      twin.properties.reported.update(properties, (err) => console.log(`Sent device properties: ${JSON.stringify(properties)}; ` +
        (err ? `error: ${err.toString()}` : `status: success`)));
    }
    ```

1. Para definir e manusear as propriedades reempreensíveis a que o seu dispositivo responde, adicione o seguinte código:

    ```javascript
    // Add any writeable properties your device supports,
    // mapped to a function that is called when the setting is changed.
    var settings = {
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

    // Handle writeable property changes that come from Azure IoT Central via the device twin.
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
              sendDeviceProperties(twin, patch);
            });
          }
        }
      });
    }
    ```

1. Adicione o seguinte código para manusear os comandos enviados da aplicação IoT Central:

    ```javascript
    // Handle blink command
    function onBlink(request, response) {
      console.log('Received asynchronous call to blink');
      response.send(200, (err) => {
        if (err) {
          console.error('Unable to send method response: ' + err.toString());
        } else {
          console.log('Blinking LED every ' + request.payload  + ' seconds');
        }
      });
    }

    // Handle LED turn on command
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

    // Handle LED turn off command
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

    // Handle sensor diagnostics command
    function diagnostics(request, response) {
      console.log('Received asynchronous call to run diagnostics');
      response.send(200, (err) => {
        if (err) {
          console.error('Unable to send method response: ' + err.toString());
        } else {
          console.log('Running diagnostics...');
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
        // Create handlers for commands
        hubClient.onDeviceMethod('blink', onBlink);
        hubClient.onDeviceMethod('turnon', turnOn);
        hubClient.onDeviceMethod('turnoff', turnOff);
        hubClient.onDeviceMethod('rundiagnostics', diagnostics);
        // Send telemetry measurements to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);
        // Get device twin from Azure IoT Central.
        hubClient.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send Environmental Sensor device properties once on device start up.
            var properties = {
              state: 'true'
            };
            sendDeviceProperties(twin, properties);
            handleSettings(twin);
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

![Executar a aplicação do cliente](media/tutorial-connect-device/run-application.png)

Como operador na sua aplicação Azure IoT Central, pode:

* Veja a telemetria enviada pelo dispositivo na página **'Visão Geral':**

    ![Ver telemetria](media/tutorial-connect-device/view-telemetry.png)

* Atualizar valores de propriedade writeable na página **Propriedades:**

    ![Atualizar propriedades](media/tutorial-connect-device/update-properties.png)

    ![Atualizar dispositivo de propriedades](media/tutorial-connect-device/update-properties-device.png)

* Ligue para os comandos da página **comandos:**

    ![Chamar o comando piscar](media/tutorial-connect-device/call-command.png)

    ![Dispositivo de comando de piscar de chamadas](media/tutorial-connect-device/call-command-device.png)

* Veja as propriedades do dispositivo na página **Sobre:**

    ![Ver propriedades](media/tutorial-connect-device/about-properties.png)

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre os modelos de capacidade do dispositivo e como criar os seus próprios modelos de dispositivo, continue a orientar:

> [!div class="nextstepaction"]
> [Defina um novo tipo de dispositivo IoT](./howto-set-up-template.md)
