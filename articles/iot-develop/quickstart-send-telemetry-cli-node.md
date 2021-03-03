---
title: Enviar telemetria do dispositivo para o quickstart do Azure IoT Hub (Node.js)
description: Neste arranque rápido, você usa o Azure IoT Hub Device SDK para Node.js enviar telemetria de um dispositivo para um hub Iot.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: node
ms.topic: quickstart
ms.date: 01/11/2021
ms.openlocfilehash: 6e0264bc0d1bea91a6094f90c4cb41f071e17dfc
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101712588"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-nodejs"></a>Quickstart: Enviar telemetria de um dispositivo para um hub IoT (Node.js)

**Aplica-se a**: [Desenvolvimento de aplicações de dispositivos](about-iot-develop.md#device-application-development)

Neste arranque rápido, você aprende um fluxo básico de desenvolvimento de aplicações de dispositivo ioT. Utiliza o CLI Azure para criar um hub Azure IoT e um dispositivo simulado, depois utiliza o Azure IoT Node.js SDK para aceder ao dispositivo e enviar telemetria para o centro.

## <a name="prerequisites"></a>Pré-requisitos
- Se não tiver uma subscrição do Azure, [crie uma gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- CLI do Azure. Pode executar todos os comandos neste quickstart usando o Azure Cloud Shell, uma concha CLI interativa que funciona no seu navegador. Se utilizar a Cloud Shell, não precisa de instalar nada. Se preferir utilizar o CLI localmente, este quickstart requer a versão 2.0.76 ou posterior do Azure CLI. Execute az --version para localizar a versão. Para instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).
- [Node.js 10+](https://nodejs.org). Se estiver a utilizar o Azure Cloud Shell, não atualize a versão instalada do Node.js. O Azure Cloud Shell já tem a versão mais recente Node.js.

    Verifique a versão atual de Node.js na sua máquina de desenvolvimento utilizando o seguinte comando:

    ```cmd/sh
        node --version
    ```

[!INCLUDE [iot-hub-include-create-hub-cli](../../includes/iot-hub-include-create-hub-cli.md)]

## <a name="use-the-nodejs-sdk-to-send-messages"></a>Utilize o Node.js SDK para enviar mensagens
Nesta secção, utilizará o Node.js SDK para enviar mensagens do seu dispositivo simulado para o seu hub IoT. 

1. Abra uma janela de terminal nova. Utilizará este terminal para instalar o Node.js SDK e trabalhará com Node.js código de amostra. Deve agora ter dois terminais abertos: o que abriu para trabalhar com Node.js, e a concha CLI que usou em secções anteriores para introduzir comandos Azure CLI. 

1. Copie as amostras do [dispositivo Azure IoT Node.js SDK](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples) para a sua máquina local:

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-node
    ```

1. Navegue para o diretório *azure-iot-sdk-node/dispositivo/amostras:*

    ```console
    cd azure-iot-sdk-node/device/samples
    ```
1. Instale o Azure IoT Node.js SDK e as dependências necessárias:

    ```console
    npm install
    ```
    Este comando instala as dependências adequadas, conforme especificado no *package.jsem* ficheiro no diretório de amostras do dispositivo.

1. Desaprote a cadeia de ligação do dispositivo como uma variável ambiente chamada `DEVICE_CONNECTION_STRING` . O valor da cadeia a utilizar é a cadeia que obteve na secção anterior depois de criar o seu dispositivo de Node.js simulado. 

    **Janelas (cmd)**

    ```console
    set DEVICE_CONNECTION_STRING=<your connection string here>
    ```

    > [!NOTE]
    > Para o Windows CMD não existem marcas de aspas em torno da cadeia de ligação.

    **Linux (bash)**

    ```bash
    export DEVICE_CONNECTION_STRING="<your connection string here>"
    ```

1. Na sua concha CLI aberta, corram o comando [de monitor-eventos do hub az iot](/cli/azure/ext/azure-iot/iot/hub?preserve-view=true&view=azure-cli-latest#ext-azure-iot-az-iot-hub-monitor-events) para começar a monitorizar eventos no seu dispositivo IoT simulado.  As mensagens de evento serão impressas no terminal à medida que chegam.

    ```azurecli
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```

1. No seu terminal Node.js, execute o código para o ficheiro de amostra instalado *simple_sample_device.js* . Este código acede ao dispositivo IoT simulado e envia uma mensagem para o hub IoT.

    Para executar a amostra de Node.js do terminal:
    ```console
    node ./simple_sample_device.js
    ```

    Opcionalmente, pode executar o código Node.js a partir da amostra no seu JavaScript IDE:
    ```javascript
    'use strict';

    const Protocol = require('azure-iot-device-mqtt').Mqtt;
    // Uncomment one of these transports and then change it in fromConnectionString to test other transports
    // const Protocol = require('azure-iot-device-amqp').AmqpWs;
    // const Protocol = require('azure-iot-device-http').Http;
    // const Protocol = require('azure-iot-device-amqp').Amqp;
    // const Protocol = require('azure-iot-device-mqtt').MqttWs;
    const Client = require('azure-iot-device').Client;
    const Message = require('azure-iot-device').Message;

    // String containing Hostname, Device Id & Device Key in the following formats:
    //  "HostName=<iothub_host_name>;DeviceId=<device_id>;SharedAccessKey=<device_key>"
    const deviceConnectionString = process.env.DEVICE_CONNECTION_STRING;
    let sendInterval;

    function disconnectHandler () {
    clearInterval(sendInterval);
    client.open().catch((err) => {
        console.error(err.message);
    });
    }

    // The AMQP and HTTP transports have the notion of completing, rejecting or abandoning the message.
    // For example, this is only functional in AMQP and HTTP:
    // client.complete(msg, printResultFor('completed'));
    // If using MQTT calls to complete, reject, or abandon are no-ops.
    // When completing a message, the service that sent the C2D message is notified that the message has been processed.
    // When rejecting a message, the service that sent the C2D message is notified that the message won't be processed by the device. the method to use is client.reject(msg, callback).
    // When abandoning the message, IoT Hub will immediately try to resend it. The method to use is client.abandon(msg, callback).
    // MQTT is simpler: it accepts the message by default, and doesn't support rejecting or abandoning a message.
    function messageHandler (msg) {
    console.log('Id: ' + msg.messageId + ' Body: ' + msg.data);
    client.complete(msg, printResultFor('completed'));
    }

    function generateMessage () {
    const windSpeed = 10 + (Math.random() * 4); // range: [10, 14]
    const temperature = 20 + (Math.random() * 10); // range: [20, 30]
    const humidity = 60 + (Math.random() * 20); // range: [60, 80]
    const data = JSON.stringify({ deviceId: 'myFirstDevice', windSpeed: windSpeed, temperature: temperature, humidity: humidity });
    const message = new Message(data);
    message.properties.add('temperatureAlert', (temperature > 28) ? 'true' : 'false');
    return message;
    }

    function errorCallback (err) {
    console.error(err.message);
    }

    function connectCallback () {
    console.log('Client connected');
    // Create a message and send it to the IoT Hub every two seconds
    sendInterval = setInterval(() => {
        const message = generateMessage();
        console.log('Sending message: ' + message.getData());
        client.sendEvent(message, printResultFor('send'));
    }, 2000);

    }

    // fromConnectionString must specify a transport constructor, coming from any transport package.
    let client = Client.fromConnectionString(deviceConnectionString, Protocol);

    client.on('connect', connectCallback);
    client.on('error', errorCallback);
    client.on('disconnect', disconnectHandler);
    client.on('message', messageHandler);

    client.open()
    .catch(err => {
    console.error('Could not connect: ' + err.message);
    });

    // Helper function to print results in the console
    function printResultFor(op) {
    return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
    };
    }
    ```

À medida que o código Node.js envia uma mensagem de telemetria simulada do seu dispositivo para o hub IoT, a mensagem aparece na sua concha CLI que está a monitorizar eventos:

```output
event:
  component: ''
  interface: ''
  module: ''
  origin: <your device name>
  payload: '{"deviceId":"myFirstDevice","windSpeed":11.853592092144627,"temperature":22.62484121157508,"humidity":66.17960805575937}'
```

O seu dispositivo está agora ligado de forma segura e a enviar telemetria para o Azure IoT Hub.

## <a name="clean-up-resources"></a>Limpar os recursos
Se já não precisar dos recursos Azure criados neste arranque rápido, pode utilizar o CLI Azure para os eliminar.

> [!IMPORTANT]
> A eliminação de um grupo de recursos é irreversível. O grupo de recursos e todos os recursos nele contidos são eliminados permanentemente. Confirme que não elimina acidentalmente o grupo de recursos ou recursos errados. 

Para eliminar um grupo de recursos por nome:
1. Executar o [comando de eliminação do grupo az.](/cli/azure/group?preserve-view=true&view=azure-cli-latest#az-group-delete) Este comando remove o grupo de recursos, o Hub IoT e o registo do dispositivo que criou.

    ```azurecli
    az group delete --name MyResourceGroup
    ```
1. Executar o comando [da lista de grupos az](/cli/azure/group?preserve-view=true&view=azure-cli-latest#az-group-list) para confirmar que o grupo de recursos é eliminado.  

    ```azurecli
    az group list
    ```

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu um fluxo básico de trabalho da aplicação Azure IoT para ligar um dispositivo de forma segura à nuvem e enviar telemetria de dispositivo para nuvem. Usou o CLI Azure para criar um hub IoT e um dispositivo simulado, depois usou o Azure IoT Node.js SDK para aceder ao dispositivo e enviar telemetria para o centro. 

Como próximo passo, explore o Azure IoT Node.js SDK através de amostras de aplicação.

- [Mais Node.js Amostras](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples): Este diretório contém mais amostras do repositório SDK Node.js para mostrar cenários do IoT Hub.