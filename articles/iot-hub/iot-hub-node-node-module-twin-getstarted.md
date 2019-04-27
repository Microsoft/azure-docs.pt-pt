---
title: Introdução ao IoT Hub do Azure módulo identidade e o módulo duplo (node. js) | Documentos da Microsoft
description: Saiba como criar a identidade do módulo e atualizar o módulo duplo com os SDKs para node. js.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: node
ms.topic: conceptual
ms.date: 04/26/2018
ms.openlocfilehash: 312d3abad2ee2c9e668f8b354aaba96f8a652698
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60626219"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-nodejs-back-end-and-nodejs-device"></a>Introdução ao IoT Hub módulo identidade e o módulo duplo usando o back-end de node. js e de dispositivo node. js

> [!NOTE]
> As [identidades de módulo e os módulos duplos](iot-hub-devguide-module-twins.md) assemelham-se à identidade do dispositivo e ao dispositivo duplo do Hub IoT do Azure, exceto no facto de oferecerem melhor granularidade. Enquanto a identidade do dispositivo e o dispositivo duplo do Hub IoT do Azure permitem que a aplicação de back-end configure um dispositivo e conferem visibilidade às condições do dispositivo, uma identidade de módulo e o módulo duplo fornecem estas capacidades para componentes individuais de um dispositivo. Em dispositivos compatíveis com vários componentes, tais como dispositivos baseados no sistema operativo ou dispositivos de firmware, permitem a configuração e condições isoladas para cada componente.

No final deste tutorial, tem duas aplicações node. js:

* **CreateIdentities**, que cria uma identidade de dispositivo, uma identidade de módulo e a chave de segurança associada para ligar os clientes do dispositivo e do módulo.

* **UpdateModuleTwinReportedProperties**, que envia as propriedades reportadas do módulo duplo atualizadas para o Hub IoT.

> [!NOTE]
> Para obter informações sobre os SDKs IoT do Azure que pode utilizar para criar quer as aplicações a executar em dispositivos e a sua solução de back-end, consulte [do Azure IoT SDKs](iot-hub-devguide-sdks.md).

Para concluir este tutorial, precisa do seguinte:

* Uma conta ativa do Azure. (Se não tiver uma conta, pode criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.)
* Um Hub IoT.
* Instalar a versão mais recente [SDK node. js](https://github.com/Azure/azure-iot-sdk-node).

Criou o seu hub IoT e tem agora o nome de anfitrião e a cadeia de ligação do Hub IoT de que precisa para concluir este tutorial.

## <a name="create-a-device-identity-and-a-module-identity-in-iot-hub"></a>Criar uma identidade de dispositivo e uma identidade de módulo no IoT Hub

Nesta secção, vai criar uma aplicação node. js que cria uma identidade de dispositivo e uma identidade de módulo no registo de identidade do IoT hub. Não é possível ligar um dispositivo ou módulo ao hub IoT, exceto se tiver uma entrada no registo de identidade. Para obter mais informações, consulte a secção "Registo de identidade" a [Guia do programador do IoT Hub](iot-hub-devguide-identity-registry.md). Ao executar esta aplicação de consola, será gerado um ID e uma chave exclusivos para o dispositivo e o módulo. O dispositivo e o módulo utilizam estes valores para se identificarem quando enviam mensagens do dispositivo para cloud para o Hub IoT. Os IDs são sensíveis às maiúsculas e minúsculas.

1. Crie um diretório para armazenar seu código.

2. Dentro desse diretório, execute primeiro **npm init -y** para criar um Package. JSON vazio com as predefinições. Este é o ficheiro de projeto para o seu código.

3. Execute **npm instalar azure-iothub - S\@módulos-pré-visualização** para instalar o SDK do serviço dentro do **node_modules** subdiretório.

    > [!NOTE]
    > O node_modules de nome de subdiretório utiliza o módulo do word para significar "uma biblioteca de nó". O termo aqui não tem nada a ver com módulos do IoT Hub.

4. Crie o arquivo. js seguinte no seu diretório. Chamá-lo **add.js**. Copie e cole a cadeia de ligação do hub e o nome de hub.

    ```javascript
    var Registry = require('azure-iothub').Registry;
    var uuid = require('uuid');
    // Copy/paste your connection string and hub name here
    var serviceConnectionString = '<hub connection string from portal>';
    var hubName = '<hub name>.azure-devices.net';
    // Create an instance of the IoTHub registry
    var registry = Registry.fromConnectionString(serviceConnectionString);
    // Insert your device ID and moduleId here.
    var deviceId = 'myFirstDevice';
    var moduleId = 'myFirstModule';
    // Create your device as a SAS authentication device
    var primaryKey = new Buffer(uuid.v4()).toString('base64');
    var secondaryKey = new Buffer(uuid.v4()).toString('base64');
    var deviceDescription = {
      deviceId: deviceId,
      status: 'enabled',
      authentication: {
        type: 'sas',
        symmetricKey: {
          primaryKey: primaryKey,
          secondaryKey: secondaryKey
        }
      }
    };

    // First, create a device identity
    registry.create(deviceDescription, function(err) {
      if (err) {
        console.log('Error creating device identity: ' + err);
        process.exit(1);
      }
      console.log('device connection string = "HostName=' + hubName + ';DeviceId=' + deviceId + ';SharedAccessKey=' + primaryKey + '"');

      // Then add a module to that device
      registry.addModule({ deviceId: deviceId, moduleId: moduleId }, function(err) {
        if (err) {
          console.log('Error creating module identity: ' + err);
          process.exit(1);
        }

        // Finally, retrieve the module details from the hub so we can construct the connection string
        registry.getModule(deviceId, moduleId, function(err, foundModule) {
          if (err) {
            console.log('Error getting module back from hub: ' + err);
            process.exit(1);
          }
          console.log('module connection string = "HostName=' + hubName + ';DeviceId=' + foundModule.deviceId + ';ModuleId='+foundModule.moduleId+';SharedAccessKey=' + foundModule.authentication.symmetricKey.primaryKey + '"');
          process.exit(0);
        });
      });
    });

    ```

Esta aplicação cria uma identidade de dispositivo com o ID **myFirstDevice** e uma identidade de módulo com o ID **myFirstModule** em dispositivos **myFirstDevice**. (Se o ID desse módulo já existir no registo de identidade, o código apenas obtém as informações do módulo existente.) De seguida, a aplicação irá apresentar a chave primária para essa identidade. Esta chave vai ser utilizada na aplicação do módulo simulado para ligar ao seu hub IoT.

Executá-lo usando add.js de nó. Isso lhe dará uma cadeia de ligação para a sua identidade de dispositivo e outro para a sua identidade de módulo.

> [!NOTE]
> O registo de identidade do Hub IoT apenas armazena identidades de dispositivos e módulos para permitir um acesso seguro ao hub IoT. O registo de identidades armazena os IDs de dispositivo e as chaves para utilizar como credenciais de segurança. O registo de identidades também armazena um sinalizador ativado/desativado para cada dispositivo que pode utilizar para desativar o acesso a esse dispositivo. Se a sua aplicação tiver de armazenar outros metadados específicos do dispositivo, deverá utilizar um armazenamento específico da aplicação.  Não existe nenhum sinalizador ativado/desativado para identidades de módulo. Para obter mais informações, consulte [Guia do programador do IoT Hub](iot-hub-devguide-identity-registry.md).

## <a name="update-the-module-twin-using-nodejs-device-sdk"></a>Atualizar o módulo duplo usando o SDK de dispositivo node. js

Nesta secção, vai criar um node. js propriedades comunicadas de aplicação no seu dispositivo simulado que atualiza o duplo do módulo.

1. **Obter a cadeia de ligação do módulo** – inicie sessão para o [portal do Azure](https://portal.azure.com/). Navegue até ao seu Hub IoT e clique em Dispositivos IoT. Encontre myFirstDevice, abra-lo e ver myFirstModule foi criado com êxito. Copie a cadeia de ligação do módulo. É necessária para o próximo passo.

   ![Detalhe do módulo no portal do Azure](./media/iot-hub-node-node-module-twin-getstarted/module-detail.png)

2. É semelhante ao que fez no passo acima, crie um diretório para o seu código de dispositivo e utilize NPM para inicializá-la e instalar o SDK do dispositivo (**npm instalar -S azure-iot-device-amqp\@módulos-pré-visualização**).

   > [!NOTE]
   > O comando de instalação de npm pode parecer lenta. Seja paciente, ele está puxando vários códigos partir do repositório do pacote.

   > [!NOTE]
   > Se vir um erro que diz o npm ERR! registo erro análise json, isso é seguro ignorar. Se vir um erro que diz o npm ERR! registo erro análise json, isso é seguro ignorar.

3. Crie um ficheiro chamado twin.js. Copie e cole a cadeia de identidade do módulo.

    ```javascript
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-amqp').Amqp;
    // Copy/paste your module connection string here.
    var connectionString = '<insert module connection string here>';
    // Create a client using the Amqp protocol.
    var client = Client.fromConnectionString(connectionString, Protocol);
    client.on('error', function (err) {
      console.error(err.message);
    });
    // connect to the hub
    client.open(function(err) {
      if (err) {
        console.error('error connecting to hub: ' + err);
        process.exit(1);
      }
      console.log('client opened');
    // Create device Twin
      client.getTwin(function(err, twin) {
        if (err) {
          console.error('error getting twin: ' + err);
          process.exit(1);
        }
        // Output the current properties
        console.log('twin contents:');
        console.log(twin.properties);
        // Add a handler for desired property changes
        twin.on('properties.desired', function(delta) {
            console.log('new desired properties received:');
            console.log(JSON.stringify(delta));
        });
        // create a patch to send to the hub
        var patch = {
          updateTime: new Date().toString(),
          firmwareVersion:'1.2.1',
          weather:{
            temperature: 72,
            humidity: 17
          }
        };
        // send the patch
        twin.properties.reported.update(patch, function(err) {
          if (err) throw err;
          console.log('twin state reported');
        });
      });
    });
    ```

4. Agora, executá-lo utilizando o comando **nó twin.js**.

    ```
    F:\temp\module_twin>node twin.js
    client opened
    twin contents:
    { reported: { update: [Function: update], '$version': 1 },
      desired: { '$version': 1 } }
    new desired properties received:
    {"$version":1}
    twin state reported
    ```

## <a name="next-steps"></a>Passos Seguintes

Para continuar a introdução ao Hub IoT e explorar outros cenários de IoT, veja:

* [Introdução à gestão de dispositivos](iot-hub-node-node-device-management-get-started.md)

* [Introdução ao IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)