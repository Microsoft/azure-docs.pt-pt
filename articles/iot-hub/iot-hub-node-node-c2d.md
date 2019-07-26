---
title: Mensagens da nuvem para o dispositivo com o Hub IoT do Azure (nó) | Microsoft Docs
description: Como enviar mensagens da nuvem para o dispositivo para um dispositivo de um hub IoT do Azure usando os SDKs do IoT do Azure para node. js. Você modifica um aplicativo de dispositivo simulado para receber mensagens da nuvem para o dispositivo e modificar um aplicativo de back-end para enviar as mensagens da nuvem para o dispositivo.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: javascript
ms.topic: conceptual
ms.date: 06/16/2017
ms.openlocfilehash: b1aa8f2ce7d271187657d57993032069639ca9c7
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68404096"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-node"></a>Enviar mensagens da nuvem para o dispositivo com o Hub IoT (nó)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Introdução

O Hub IoT do Azure é um serviço totalmente gerenciado que ajuda a habilitar comunicações bidirecionais confiáveis e seguras entre milhões de dispositivos e um back-end de solução. O guia de início rápido [Enviar telemetria de um dispositivo para um hub IOT](quickstart-send-telemetry-node.md) mostra como criar um hub IOT, provisionar uma identidade do dispositivo nele e codificar um aplicativo de dispositivo simulado que envia mensagens do dispositivo para a nuvem.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Este tutorial se baseia no [envio de telemetria de um dispositivo para um hub IOT](quickstart-send-telemetry-node.md). Ele mostra como:

* De seu back-end da solução, envie mensagens da nuvem para o dispositivo para um único dispositivo por meio do Hub IoT.
* Receber mensagens da nuvem para o dispositivo em um dispositivo.
* Do seu back-end da solução, solicite a confirmação de entrega (*comentários*) para mensagens enviadas a um dispositivo do Hub IOT.

Você pode encontrar mais informações sobre as mensagens da nuvem para o dispositivo no [Guia do desenvolvedor do Hub IOT](iot-hub-devguide-messaging.md).

No final deste tutorial, você executará dois aplicativos de console do node. js:

* **SimulatedDevice**, uma versão modificada do aplicativo criado em [Enviar telemetria de um dispositivo para um hub IOT](quickstart-send-telemetry-node.md), que se conecta ao seu hub IOT e recebe mensagens da nuvem para o dispositivo.

* **SendCloudToDeviceMessage**, que envia uma mensagem da nuvem para o dispositivo para o aplicativo de dispositivo simulado por meio do Hub IOT e recebe sua confirmação de entrega.

> [!NOTE]
> O Hub IoT tem suporte do SDK para várias plataformas de dispositivo e linguagens (incluindo C, Java e JavaScript) por meio de SDKs do dispositivo IoT do Azure. Para obter instruções detalhadas sobre como conectar seu dispositivo ao código deste tutorial e, em geral, ao Hub IoT do Azure, consulte o centro de [desenvolvedores do Azure IOT](https://azure.microsoft.com/develop/iot).
>

Para concluir este tutorial, precisa do seguinte:

* Node. js versão 10.0. x ou posterior.
* Uma conta ativa do Azure. (Se você não tiver uma conta, poderá criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial) em apenas alguns minutos.)

## <a name="receive-messages-in-the-simulated-device-app"></a>Receber mensagens no aplicativo do dispositivo simulado

Nesta seção, você modificará o aplicativo de dispositivo simulado criado em [Enviar telemetria de um dispositivo para um hub IOT](quickstart-send-telemetry-node.md) para receber mensagens da nuvem para o dispositivo do Hub IOT.

1. Usando um editor de texto, abra o arquivo SimulatedDevice. js.

2. Modifique a função **connectCallback** para tratar as mensagens enviadas do Hub IOT. Neste exemplo, o dispositivo sempre invoca a função **Complete** para notificar o Hub IOT de que ele processou a mensagem. A nova versão da função **connectCallback** é semelhante ao seguinte trecho:

    ```javascript
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');
        client.on('message', function (msg) {
          console.log('Id: ' + msg.messageId + ' Body: ' + msg.data);
          client.complete(msg, printResultFor('completed'));
        });
        // Create a message and send it to the IoT Hub every second
        setInterval(function(){
            var temperature = 20 + (Math.random() * 15);
            var humidity = 60 + (Math.random() * 20);
            var data = JSON.stringify({ deviceId: 'myFirstNodeDevice', temperature: temperature, humidity: humidity });
            var message = new Message(data);
            message.properties.add('temperatureAlert', (temperature > 30) ? 'true' : 'false');
            console.log("Sending message: " + message.getData());
            client.sendEvent(message, printResultFor('send'));
        }, 1000);
      }
    };
    ```
  
   > [!NOTE]
   > Se você usar HTTPS em vez de MQTT ou AMQP como o transporte, a instância **DeviceClient** verificará mensagens do Hub IOT com pouca frequência (menos do que a cada 25 minutos). Para obter mais informações sobre as diferenças entre o suporte a MQTT, AMQP e HTTPS e a limitação do Hub IoT, consulte o [Guia do desenvolvedor do Hub IOT](iot-hub-devguide-messaging.md).
   >

## <a name="get-the-iot-hub-connection-string"></a>Obter a cadeia de conexão do Hub IoT

Neste artigo, você cria um serviço de back-end para enviar mensagens da nuvem para o dispositivo por meio do Hub IoT criado em [Enviar telemetria de um dispositivo para um hub IOT](quickstart-send-telemetry-node.md). Para enviar mensagens da nuvem para o dispositivo, seu serviço precisa da permissão de **conexão de serviço** . Por padrão, todo Hub IoT é criado com uma política de acesso compartilhado chamada **serviço** que concede essa permissão.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Enviar uma mensagem da nuvem para o dispositivo

Nesta seção, você cria um aplicativo de console do node. js que envia mensagens da nuvem para o dispositivo para o aplicativo do dispositivo simulado. Você precisa da ID do dispositivo que adicionou no guia de início rápido [Enviar telemetria de um dispositivo para um hub IOT](quickstart-send-telemetry-node.md) . Você também precisa da cadeia de conexão do Hub IoT que copiou anteriormente em [obter a cadeia de conexão do Hub IOT](#get-the-iot-hub-connection-string).

1. Crie uma pasta vazia chamada **sendcloudtodevicemessage**. Na pasta **sendcloudtodevicemessage** , crie um arquivo Package. JSON usando o comando a seguir no prompt de comando. Aceite todas as predefinições:

    ```shell
    npm init
    ```

2. No prompt de comando, na pasta **sendcloudtodevicemessage** , execute o seguinte comando para instalar o pacote **Azure-iothub** :

    ```shell
    npm install azure-iothub --save
    ```

3. Usando um editor de texto, crie um arquivo **SendCloudToDeviceMessage. js** na pasta **SendCloudToDeviceMessage** .

4. Adicione as seguintes `require` instruções no início do arquivo **SendCloudToDeviceMessage. js** :

    ```javascript
    'use strict';

    var Client = require('azure-iothub').Client;
    var Message = require('azure-iot-common').Message;
    ```

5. Adicione o código a seguir ao arquivo **SendCloudToDeviceMessage. js** . Substitua os valores de espaço reservado "{cadeia de conexão do Hub IOT}" e "{ID do dispositivo}" pela cadeia de conexão do Hub IoT e ID do dispositivo observado anteriormente:

    ```javascript
    var connectionString = '{iot hub connection string}';
    var targetDevice = '{device id}';

    var serviceClient = Client.fromConnectionString(connectionString);
    ```

6. Adicione a seguinte função para imprimir os resultados da operação para o console:

    ```javascript
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. Adicione a seguinte função para imprimir mensagens de comentários de entrega no console:

    ```javascript
    function receiveFeedback(err, receiver){
      receiver.on('message', function (msg) {
        console.log('Feedback message:')
        console.log(msg.getData().toString('utf-8'));
      });
    }
    ```

8. Adicione o código a seguir para enviar uma mensagem ao seu dispositivo e manipular a mensagem de comentários quando o dispositivo confirmar a mensagem da nuvem para o dispositivo:

    ```javascript
    serviceClient.open(function (err) {
      if (err) {
        console.error('Could not connect: ' + err.message);
      } else {
        console.log('Service client connected');
        serviceClient.getFeedbackReceiver(receiveFeedback);
        var message = new Message('Cloud to device message.');
        message.ack = 'full';
        message.messageId = "My Message ID";
        console.log('Sending message: ' + message.getData());
        serviceClient.send(targetDevice, message, printResultFor('send'));
      }
    });
    ```

9. Salve e feche o arquivo **SendCloudToDeviceMessage. js** .

## <a name="run-the-applications"></a>Executar as aplicações

Pode agora executar as aplicações.

1. No prompt de comando na pasta **simulateddevice** , execute o seguinte comando para enviar telemetria ao Hub IOT e para escutar mensagens da nuvem para o dispositivo:

    ```shell
    node SimulatedDevice.js
    ```

    ![Executar o aplicativo de dispositivo simulado](./media/iot-hub-node-node-c2d/receivec2d.png)

2. Em um prompt de comando na pasta **sendcloudtodevicemessage** , execute o seguinte comando para enviar uma mensagem da nuvem para o dispositivo e aguarde os comentários de confirmação:

    ```shell
    node SendCloudToDeviceMessage.js
    ```

    ![Executar o aplicativo para enviar o comando da nuvem para o dispositivo](./media/iot-hub-node-node-c2d/sendc2d.png)

   > [!NOTE]
   > Para simplificar, este tutorial não implementa nenhuma política de repetição. No código de produção, você deve implementar políticas de repetição (como retirada exponencial), conforme sugerido no artigo [tratamento de falhas transitórias](/azure/architecture/best-practices/transient-faults).
   >

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu a enviar e receber mensagens da nuvem para o dispositivo.

Para ver exemplos de soluções completas de ponta a ponta que usam o Hub IoT, consulte [acelerador de solução de monitoramento remoto de IOT do Azure](https://azure.microsoft.com/documentation/suites/iot-suite/).

Para saber mais sobre como desenvolver soluções com o Hub IoT, confira o [Guia do desenvolvedor do Hub IOT](iot-hub-devguide.md).
