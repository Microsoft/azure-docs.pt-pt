---
title: Comece com a identidade do módulo Azure IoT Hub & módulo twin (Node.js)
description: Aprenda a criar a identidade do módulo e a atualização do módulo twin utilizando SDKs IoT para Node.js.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/26/2018
ms.custom: amqp
ms.openlocfilehash: 8e1599b1bd5db5e410e8bbd76fffbe0beb5f066e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81732291"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-nodejs"></a>Inicie com identidade do módulo IoT Hub e módulo twin (Node.js)

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> As [identidades de módulo e os módulos duplos](iot-hub-devguide-module-twins.md) assemelham-se à identidade do dispositivo e ao dispositivo duplo do Hub IoT do Azure, exceto no facto de oferecerem melhor granularidade. Enquanto a identidade do dispositivo Azure IoT Hub e o dispositivo twin permitem que a aplicação back-end configure um dispositivo e ofereça visibilidade sobre as condições do dispositivo, uma identidade de módulo e um módulo twin fornecem estas capacidades para componentes individuais de um dispositivo. Em dispositivos compatíveis com vários componentes, tais como dispositivos baseados no sistema operativo ou dispositivos de firmware, permitem a configuração e condições isoladas para cada componente.

No final deste tutorial, você tem duas aplicações Node.js:

* **CreateIdentities**, que cria uma identidade de dispositivo, uma identidade de módulo e a chave de segurança associada para ligar os clientes do dispositivo e do módulo.

* **UpdateModuleTwinReportedProperties**, que envia as propriedades reportadas do módulo duplo atualizadas para o Hub IoT.

> [!NOTE]
> Para informações sobre os SDKs do Azure IoT que pode utilizar para criar quer as aplicações a executar em dispositivos, quer a sua solução de back-end, veja [Azure IoT SDKs](iot-hub-devguide-sdks.md).

## <a name="prerequisites"></a>Pré-requisitos

* Node.js versão 10.0.x ou mais tarde. [Prepare o seu ambiente](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) de desenvolvimento descreve como instalar o Node.js para este tutorial no Windows ou no Linux.

* Uma conta ativa do Azure. (Se não tiver uma conta, pode criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.)

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>Obtenha a cadeia de ligação do hub IoT

[!INCLUDE [iot-hub-howto-module-twin-shared-access-policy-text](../../includes/iot-hub-howto-module-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="create-a-device-identity-and-a-module-identity-in-iot-hub"></a>Criar uma identidade de dispositivo e uma identidade de módulo no IoT Hub

Nesta secção, cria uma aplicação Node.js que cria uma identidade de dispositivo e uma identidade de módulo no registo de identidade no seu hub IoT. Não é possível ligar um dispositivo ou módulo ao hub IoT, exceto se tiver uma entrada no registo de identidade. Para mais informações, consulte a secção "Registo de Identidade" do guia de [desenvolvimento do IoT Hub](iot-hub-devguide-identity-registry.md). Ao executar esta aplicação de consola, será gerado um ID e uma chave exclusivos para o dispositivo e o módulo. O dispositivo e o módulo utilizam estes valores para se identificarem quando enviam mensagens do dispositivo para cloud para o Hub IoT. Os IDs são sensíveis às maiúsculas e minúsculas.

1. Crie um diretório para manter o seu código.

2. Dentro desse diretório, primeiro corra **npm init -y** para criar um pacote vazio.json com incumprimentos. Este é o ficheiro do projeto para o seu código.

3. Executar **a instalação de npm -S azure-iothub\@módulos-pré-visualização** para instalar o serviço SDK dentro do subdiretório **node_modules.**

    > [!NOTE]
    > O nome do subdiretório node_modules usa o módulo da palavra para significar "uma biblioteca de nó". O termo aqui não tem nada a ver com módulos IoT Hub.

4. Crie o seguinte ficheiro .js no seu diretório. Chame-o **de add.js**. Copie e cole o seu fio de ligação ao hub e o nome do hub.

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

Esta aplicação cria uma identidade de dispositivo com ID **myFirstDevice** e uma identidade de módulo com ID **myFirstModule** no dispositivo **myFirstDevice**. (Se esse id do módulo já existir no registo de identidade, o código simplesmente recupera as informações do módulo existentes.) A aplicação exibe então a chave principal para essa identidade. Esta chave vai ser utilizada na aplicação do módulo simulado para ligar ao seu hub IoT.

Execute isto usando add.js do nó. Ele lhe dará uma cadeia de ligação para a identidade do seu dispositivo e outra para a sua identidade de módulo.

> [!NOTE]
> O registo de identidade do Hub IoT apenas armazena identidades de dispositivos e módulos para permitir um acesso seguro ao hub IoT. O registo de identidades armazena os IDs de dispositivo e as chaves para utilizar como credenciais de segurança. O registo de identidades também armazena um sinalizador ativado/desativado para cada dispositivo que pode utilizar para desativar o acesso a esse dispositivo. Se a sua aplicação tiver de armazenar outros metadados específicos do dispositivo, deverá utilizar um armazenamento específico da aplicação.  Não existe nenhum sinalizador ativado/desativado para identidades de módulo. Para obter mais informações, veja o [IoT Hub developer guide (Guia do programador do Hub IoT)](iot-hub-devguide-identity-registry.md).

## <a name="update-the-module-twin-using-nodejs-device-sdk"></a>Atualize o módulo twin utilizando o dispositivo Node.js SDK

Nesta secção, cria uma aplicação Node.js no seu dispositivo simulado que atualiza as propriedades reportadas pelo módulo Twin.

1. **Obtenha a corda** de ligação do módulo - Inscreva-se no [portal Azure](https://portal.azure.com/). Navegue até ao seu Hub IoT e clique em Dispositivos IoT. Encontre o meu FirstDevice, abra-o e veja que o myFirstModule foi criado com sucesso. Copie a cadeia de ligação do módulo. É necessária para o próximo passo.

   ![Detalhe do módulo no portal do Azure](./media/iot-hub-node-node-module-twin-getstarted/module-detail.png)

2. Semelhante ao que fez no passo acima, crie um diretório para o código do seu dispositivo e utilize o NPM para inicializá-lo e instalar o dispositivo SDK **(npm instalar -S azure-iot-device-amqp\@módulos-pré-visualização).**

   > [!NOTE]
   > O comando de instalação npm pode parecer lento. Tenha paciência, está a retirar muito código do repositório do pacote.

   > [!NOTE]
   > Se vir um erro que diga npm ERR! erro de registo parsing json, este é seguro para ignorar. Se vir um erro que diga npm ERR! erro de registo parsing json, este é seguro para ignorar.

3. Crie um ficheiro chamado Twin.Js. Copie e cole a corda de identidade do módulo.

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

4. Agora, executa isto usando o nó de comando **twin.js**.

   ```cmd/sh
   F:\temp\module_twin>node twin.js
   ```

   Verá então:

   ```console
   client opened
   twin contents:
   { reported: { update: [Function: update], '$version': 1 },
     desired: { '$version': 1 } }
   new desired properties received:
   {"$version":1}
   twin state reported
   ```

## <a name="next-steps"></a>Passos seguintes

Para continuar a introdução ao Hub IoT e explorar outros cenários de IoT, veja:

* [Getting started with device management (Introdução à gestão de dispositivos)](iot-hub-node-node-device-management-get-started.md)

* [Introdução ao IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)