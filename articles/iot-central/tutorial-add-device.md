---
title: Adicionar um dispositivo real a uma aplicação do Azure IoT Central | Microsoft Docs
description: Como operador, adicione um dispositivo real à aplicação do Azure IoT Central.
author: sandeeppujar
ms.author: sandeepu
ms.date: 08/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 96b8090bd0e178be53cb49f42438951645def5d9
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/04/2019
ms.locfileid: "71960499"
---
# <a name="tutorial-add-a-real-device-to-your-azure-iot-central-application"></a>Tutorial: Adicionar um dispositivo real à aplicação do Azure IoT Central

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

Este tutorial mostra como adicionar e configurar um dispositivo real à aplicação do Microsoft Azure IoT Central. 
_* você não precisa de seu próprio dispositivo externo para concluir este tutorial. O "dispositivo real" será criado como um aplicativo codificado em execução no ambiente de linha de comando._ 

Este tutorial é constituído por duas partes:

* Primeiro, como operador, vai aprender a adicionar e configurar um dispositivo real na aplicação do Azure IoT Central. No final desta parte, pode obter uma cadeia de ligação para utilizar na segunda parte.
* Em seguida, como programador de dispositivos, vai obter informações sobre o código no dispositivo real. Vai adicionar a cadeia de ligação da primeira parte ao código de exemplo.



Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Adicionar um novo dispositivo real
> * Configurar o dispositivo real
> * Obter a cadeia de ligação para o dispositivo real a partir da aplicação
> * Compreender a forma como o código de cliente mapeia para a aplicação
> * Configurar o código de cliente para o dispositivo real

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, o construtor deve concluir, pelo menos, o primeiro tutorial do construtor para criar a aplicação do Azure IoT Central: [Definir um novo tipo de dispositivo](tutorial-define-device-type.md) (obrigatório)

Instale o [node. js](https://nodejs.org/) versão 8.0.0 ou posterior em seu computador de desenvolvimento. Você pode executar `node --version` na linha de comando para verificar sua versão. O Node.js está disponível para uma grande variedade de sistemas operativos.

## <a name="add-a-real-device"></a>Adicionar um dispositivo real

Para adicionar um dispositivo real à sua aplicação, utilize o modelo de dispositivo **Ar Condicionado Ligado** que criou no tutorial [Definir um novo tipo de dispositivo](tutorial-define-device-type.md).

1. Para adicionar um novo dispositivo como um operador, escolha **dispositivos** no menu de navegação à esquerda:

   ![Página do Device Explorer que mostra o ar condicionado ligado](media/tutorial-add-device/explorer.png)

   O **Device Explorer** mostra o modelo de dispositivo do **ar-condicionado conectado** e um dispositivo simulado. Quando você cria um modelo de dispositivo, IoT Central cria automaticamente um dispositivo simulado.

2. Observe que o modelo de dispositivo **conectado do ar-condicionado** é aquele selecionado na **Device Explorer**. Para começar a conectar um dispositivo de ar-condicionado real que usa esse modelo, selecione **+** , em seguida, **real**:

   ![Começar a adicionar um novo dispositivo de ar condicionado ligado real](media/tutorial-add-device/newreal.png)

3. Insira sua própria **ID de dispositivo** (deve estar em minúsculas) ou use o valor sugerido. Você também pode inserir um **nome de dispositivo** para o novo dispositivo e escolher **criar**.

   ![Mudar o nome do dispositivo](media/tutorial-add-device/rename.png)

## <a name="configure-a-real-device"></a>Configurar um dispositivo real

O dispositivo real é criado a partir do modelo de dispositivo **Ar Condicionado Ligado**. Pode utilizar as **Definições** para configurar o dispositivo e definir valores de propriedade para registar informações sobre o dispositivo.

1. Na página **Definições**, tenha em atenção que o estado da definição **Definir Temperatura** é **sem atualização**. Permanece neste estado até o dispositivo real ligar à aplicação e reconhecer que agiu na definição.

    ![As definições mostram a sincronização](media/tutorial-add-device/settingssyncing.png)

2. Na página **Propriedades** do seu novo dispositivo real, o local do serviço e a data do último serviço são propriedades editáveis. Os campos de versão do número de série e de firmware estão vazios até que o dispositivo esteja ligado à aplicação. Esses valores somente leitura são enviados do dispositivo e não podem ser editados.

    ![Propriedades do Dispositivo para dispositivo real](media/tutorial-add-device/setproperties1.png)

3. Pode ver as páginas **Medidas**, **Regras** e **Dashboard** para o dispositivo real.

## <a name="prepare-the-client-code"></a>Preparar o código de cliente

O código de exemplo neste artigo é escrito em [node. js](https://nodejs.org/) e mostra código suficiente para um dispositivo para:

* Ligar à sua aplicação do Azure IoT Central.
* Enviar telemetria de temperatura como um dispositivo de ar condicionado ligado.
* Envie propriedades de dispositivo para a sua aplicação do Azure IoT Central.
* Responder a um operador que utiliza a definição **Definir Temperatura**.
* Processe o comando Echo a partir da sua aplicação do Azure IoT Central.

Os artigos listados na seção [próximas etapas](#next-steps) incluem exemplos mais completos e mostram outras linguagens de programação. Para obter mais informações sobre a forma como os dispositivos se ligam ao Azure IoT Central, veja o artigo [Conectividade de dispositivos](concepts-connectivity.md).

Os passos seguintes mostram como preparar o exemplo em [Node.js](https://nodejs.org/):

### <a name="get-the-device-connection-information"></a>Obter as informações de conexão do dispositivo

1. A cadeia de ligação para uma instância de dispositivo na sua aplicação é gerada a partir das informações do dispositivo concedidas pelo IoT Central.

   No ecrã do dispositivo do seu ar condicionado real, escolha **Ligar**.

   ![Página do dispositivo que mostra a ligação para informações de ligação](media/tutorial-add-device/connectionlink.png)

1. Na página **conexão do dispositivo** , anote a **ID do escopo**, a **ID do dispositivo** e os valores de **chave primária** . Você usará esses valores posteriormente neste tutorial.

   ![Detalhes da ligação](media/tutorial-add-device/device-connect.png)

### <a name="prepare-the-nodejs-project"></a>Preparar o projeto do node. js

1. Crie uma pasta chamada `connectedairconditioner` em seu computador de desenvolvimento.

1. No ambiente de linha de comandos, navegue para a pasta `connectedairconditioner` que criou.

1. Para inicializar o projeto em Node.js, execute o seguinte comando ao aceitar todas as predefinições:

    ```cmd/sh
    npm init
      ```

1. Para instalar os pacotes necessários, execute o seguinte comando:

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt azure-iot-provisioning-device-mqtt azure-iot-security-symmetric-key --save
    ```

1. Com um editor de texto, crie um ficheiro denominado **ConnectedAirConditioner.js** na pasta `connectedairconditioner`.

1. Adicione as seguintes instruções `require` no início do ficheiro **ConnectedAirConditioner.js**:

    ```javascript
    'use strict';

    var iotHubTransport = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var ProvisioningTransport = require('azure-iot-provisioning-device-mqtt').Mqtt;
    var SymmetricKeySecurityClient = require('azure-iot-security-symmetric-key').SymmetricKeySecurityClient;
    var ProvisioningDeviceClient = require('azure-iot-provisioning-device').ProvisioningDeviceClient;
    ```

1. Adicione as seguintes declarações de variável ao arquivo. Substitua os espaços reservados `{your Scope ID}`, `{your Device ID}` e `{your Primary Key}` pelas informações de conexão do dispositivo anotadas anteriormente:

    ```javascript
    var provisioningHost = 'global.azure-devices-provisioning.net';
    var idScope = '{your Scope ID}';
    var registrationId = '{your Device ID}';
    var symmetricKey = '{your Primary Key}';
    var provisioningSecurityClient = new SymmetricKeySecurityClient(registrationId, symmetricKey);
    var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvisioningTransport(), provisioningSecurityClient);
    var hubClient;
    var targetTemperature = 0;
    ```

1. Guarde as alterações efetuadas até ao momento, mas mantenha o ficheiro aberto.

## <a name="review-client-code"></a>Examinar o código do cliente

Na seção anterior, você criou um projeto de nó. js de esqueleto para um aplicativo de dispositivo que se conecta ao seu aplicativo de IoT Central do Azure. A próxima etapa é adicionar código a:

* Ligar à sua aplicação do Azure IoT Central.
* Enviar telemetria para a sua aplicação do Azure IoT Central.
* Envie propriedades de dispositivo para a sua aplicação do Azure IoT Central.
* Receber definições da sua aplicação do Azure IoT Central.
* Processe o comando Echo a partir da sua aplicação do Azure IoT Central.

1. Para enviar telemetria de temperatura para a sua aplicação do Azure IoT Central, adicione o seguinte código ao ficheiro **ConnectedAirConditioner.js**:

    ```javascript
    // Send device measurements.
    function sendTelemetry() {
      var temperature = targetTemperature + (Math.random() * 15);
      var data = JSON.stringify({ temperature: temperature });
      var message = new Message(data);
      hubClient.sendEvent(message, (err, res) => console.log(`Sent message: ${message.getData()}` +
        (err ? `; error: ${err.toString()}` : '') +
        (res ? `; status: ${res.constructor.name}` : '')));
    }
    ```

    O nome do campo no JSON que enviar tem de corresponder ao nome do campo especificado para a telemetria de temperatura no modelo de dispositivo. Neste exemplo, o nome do campo é **temperature**.

1. Para enviar as propriedades do dispositivo, como **firmwareVersion** e **serialNumber**, adicione a seguinte definição:

    ```javascript
    // Send device properties
    function sendDeviceProperties(twin) {
      var properties = {
        firmwareVersion: "9.75",
        serialNumber: "10001"
      };
      twin.properties.reported.update(properties, (errorMessage) =>
      console.log(` * Sent device properties ` + (errorMessage ? `Error: ${errorMessage.toString()}` : `(success)`)));
    }
    ```

1. Para estipular as definições suportadas pelo dispositivo, como **setTemperature**, adicione a seguinte definição:

    ```javascript
    // Add any settings your device supports
    // mapped to a function that is called when the setting is changed.
    var settings = {
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

1. Para processar as definições enviadas a partir do Azure IoT Central, adicione a seguinte função que localiza e executa o código de dispositivo adequado:

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

    Esta função:

    * Observa o Azure IoT Central a enviar uma propriedade desejada.
    * Localiza a função adequada à chamada para processar a alteração da definição.
    * Envia uma confirmação de volta para o aplicativo de IoT Central do Azure.

1. Para responder a um comando, como **echo** desde a sua aplicação do Azure IoT Central, adicione a seguinte definição:

    ```javascript
    // Respond to the echo command
    function onCommandEcho(request, response) {
      // Display console info
      console.log(' * Echo command received');
      // Respond
      response.send(10, 'Success', function (errorMessage) {});
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
        hubClient.onDeviceMethod('echo', onCommandEcho);

        // Send telemetry measurements to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);

        // Get device twin from Azure IoT Central.
        hubClient.getTwin((err, twin) => {
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
    ```

1. Registre e conecte o dispositivo ao seu aplicativo IoT Central:

    ```javascript
    // Start the device (connect it to Azure IoT Central).
    provisioningClient.register((err, result) => {
      if (err) {
        console.log("error registering device: " + err);
      } else {
        console.log('registration succeeded');
        console.log('assigned hub=' + result.assignedHub);
        console.log('deviceId=' + result.deviceId);
        var connectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';SharedAccessKey=' + symmetricKey;
        hubClient = Client.fromConnectionString(connectionString, iotHubTransport);

        hubClient.open(connectCallback);
      }
    });
    ```

1. Salve as alterações feitas.

## <a name="run-the-client-code"></a>Executar o código do cliente

Agora você pode executar o código do cliente e ver como ele interage com seu aplicativo IoT Central:

1. Para executar o exemplo, introduza o seguinte comando no ambiente de linha de comandos:

    ```cmd/sh
    node ConnectedAirConditioner.js
    ```

    > [!NOTE]
    > Certifique-se de que está na pasta `connectedairconditioner` quando executar este comando.

1. A aplicação imprime o resultado na consola:

   ![Resultado da aplicação cliente](media/tutorial-add-device/output.png)

1. Após cerca de 30 segundos, é apresentada a telemetria na página do dispositivo **Medidas**:

   ![Telemetria real](media/tutorial-add-device/realtelemetry.png)

1. Na página **Definições**, pode ver que a definição está agora sincronizada. Quando o dispositivo foi ligado pela primeira vez, recebeu o valor da definição e confirmou a alteração:

   ![Definição sincronizada](media/tutorial-add-device/settingsynced.png)

1. Na página **Definições**, defina a temperatura do dispositivo para **95** e selecione **Atualizar dispositivo**. A aplicação de exemplo recebe e processa esta alteração:

   ![Receber e processar a definição](media/tutorial-add-device/receivesetting.png)

   > [!NOTE]
   > Existem duas mensagens de "atualização da definição". Uma quando é enviado o estado `pending` e outra quando é enviado o estado `completed`.

1. Na página **Medidas**, pode ver que o dispositivo está a enviar valores de temperatura superiores:

    ![A telemetria de temperatura é agora superior](media/tutorial-add-device/highertemperature.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="nextstepaction"]
> * Adicionar um novo dispositivo real
> * Configurar o novo dispositivo
> * Obter a cadeia de ligação para o dispositivo real a partir da aplicação
> * Compreender a forma como o código de cliente mapeia para a aplicação
> * Configurar o código de cliente para o dispositivo real

Agora que você conectou um dispositivo real ao aplicativo IoT Central do Azure, aqui estão algumas próximas etapas sugeridas.

Como operador, vai aprender a:

* [Gerir os seus dispositivos](howto-manage-devices.md)
* [Utilizar conjuntos de dispositivos](howto-use-device-sets.md)
* [Criar análise personalizada](howto-use-device-sets.md)

Como programador de dispositivos, vai aprender a:

* [Preparar e conectar um dispositivo DevKit (C)](howto-connect-devkit.md)
* [Preparar e conectar um Raspberry Pi (Python)](howto-connect-raspberry-pi-python.md)
* [Preparar e conectar um Raspberry Pi (C#)](howto-connect-raspberry-pi-csharp.md)
* [Preparar e conectar um dispositivo Windows 10 IoT Core (C#)](howto-connect-windowsiotcore.md)
* [Ligar um cliente Node.js genérico à aplicação do Azure IoT Central](howto-connect-nodejs.md)
