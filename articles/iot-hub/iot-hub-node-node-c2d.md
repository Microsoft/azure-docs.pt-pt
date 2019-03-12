---
title: Mensagens de cloud para o dispositivo com o IoT Hub do Azure (Node) | Documentos da Microsoft
description: Como enviar mensagens da cloud para dispositivo a um dispositivo de um hub de IoT do Azure com os SDKs IoT do Azure para node. js. Modificar uma aplicação de dispositivo simulado para receber mensagens da cloud para o dispositivo e modificar uma aplicação de back-end para enviar as mensagens de cloud-para-dispositivo.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: javascript
ms.topic: conceptual
ms.date: 06/16/2017
ms.openlocfilehash: 643fd4558c8edd65047eb9d9b780424076d9ea3b
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57530982"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-node"></a>Enviar mensagens da cloud para dispositivo hub IoT (Node)
[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Introdução
O IoT Hub do Azure é um serviço totalmente gerido que ajuda a habilitar comunicações bidirecionais fiáveis e seguras entre milhões de dispositivos e uma solução de back-end. O [introdução ao IoT Hub] tutorial mostra como criar um hub IoT, aprovisionar uma identidade de dispositivo no mesmo e código de uma aplicação de dispositivo simulado que envia mensagens do dispositivo para a cloud.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Este tutorial baseia-se nas [introdução ao IoT Hub]. Mostra-lhe como para:

* Da sua solução back-end, envie mensagens de cloud-para-dispositivo para um único dispositivo através do IoT Hub.
* Receba mensagens da cloud para o dispositivo num dispositivo.
* Da sua solução back-end, pedir confirmação de entrega (*comentários*) para mensagens enviadas para um dispositivo do IoT Hub.

Pode encontrar mais informações sobre mensagens de cloud para o dispositivo na [Guia do programador do IoT Hub][IoT Hub developer guide - C2D].

No final deste tutorial, executa duas aplicações de consola node. js:

* **SimulatedDevice**, uma versão modificada da aplicação que criou no [introdução ao IoT Hub], que liga ao seu hub IoT e recebe mensagens de cloud-para-dispositivo.
* **SendCloudToDeviceMessage**, que envia uma mensagem de cloud-para-dispositivo para a aplicação de dispositivo simulado através do IoT Hub e, em seguida, recebe a confirmação de entrega.

> [!NOTE]
> IoT Hub tem suporte SDK para muitas plataformas de dispositivos e linguagens (incluindo C, Java e Javascript) através de SDKs de dispositivo do IoT do Azure. Para obter instruções passo a passo sobre como ligar o seu dispositivo ao código do tutorial e, em geral, hub IoT do Azure, consulte a [Centro de Programadores do Azure IoT].
> 
> 

Para concluir este tutorial, precisa do seguinte:

* Versão 4.0.x ou posterior do Node.js.
* Uma conta ativa do Azure. (Se não tiver uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.)

## <a name="receive-messages-in-the-simulated-device-app"></a>Receber mensagens na aplicação do dispositivo simulado
Nesta secção, modificar a aplicação de dispositivo simulado que criou no [introdução ao IoT Hub] para receber mensagens da cloud para dispositivo do IoT hub.

1. Com um editor de texto, abra o ficheiro de simulateddevice. js.
2. Modificar a **connectCallback** função para processar as mensagens enviadas do IoT Hub. Neste exemplo, o dispositivo sempre invoca o **concluída** função para notificar o IoT Hub que processou a mensagem. A nova versão dos **connectCallback** função se parece o seguinte fragmento:
   
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
   > Se utilizar HTTPS em vez de MQTT ou AMQP como o transporte, o **DeviceClient** instância verifica a existência de mensagens do Hub de IoT com pouca frequência (menos de 25 minutos). Para obter mais informações sobre as diferenças entre o suporte para MQTT, AMQP e HTTPS e a limitação do IoT Hub, consulte a [Guia do programador do IoT Hub][IoT Hub developer guide - C2D].
   > 
   > 

## <a name="send-a-cloud-to-device-message"></a>Enviar uma mensagem de cloud-para-dispositivo
Nesta secção, vai criar uma aplicação de consola node. js que envia mensagens de cloud-para-dispositivo para a aplicação de dispositivo simulado. Precisa do ID de dispositivo do dispositivo que adicionou no [introdução ao IoT Hub] tutorial. Também precisa da cadeia de ligação do IoT Hub para o hub que pode encontrar no [portal do Azure].

1. Criar uma pasta vazia designada **sendcloudtodevicemessage**. Na **sendcloudtodevicemessage** pasta, crie um ficheiro Package. JSON com o seguinte comando na sua linha de comandos. Aceite todas as predefinições:
   
    ```shell
    npm init
    ```
2. Sua linha de comandos do **sendcloudtodevicemessage** pasta, execute o seguinte comando para instalar o **do azure-iothub** pacote:
   
    ```shell
    npm install azure-iothub --save
    ```
3. Com um editor de texto, crie uma **SendCloudToDeviceMessage.js** de ficheiros a **sendcloudtodevicemessage** pasta.
4. Adicione as seguintes `require` instruções no início do **SendCloudToDeviceMessage.js** ficheiro:
   
    ```javascript
    'use strict';
   
    var Client = require('azure-iothub').Client;
    var Message = require('azure-iot-common').Message;
    ```
5. Adicione o seguinte código para **SendCloudToDeviceMessage.js** ficheiro. Substitua o valor de marcador de posição de "{iot hub connection string}" com a cadeia de ligação do IoT Hub para o hub que criou na [introdução ao IoT Hub] tutorial. Substitua o marcador de posição de "{id de dispositivo}" com o ID de dispositivo do dispositivo que adicionou no [introdução ao IoT Hub] tutorial:
   
    ```javascript
    var connectionString = '{iot hub connection string}';
    var targetDevice = '{device id}';
   
    var serviceClient = Client.fromConnectionString(connectionString);
    ```
6. Adicione a seguinte função para imprimir os resultados da operação para a consola:
   
    ```javascript
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```
7. Adicione a seguinte função para imprimir mensagens de comentários de entrega para a consola:
   
    ```javascript
    function receiveFeedback(err, receiver){
      receiver.on('message', function (msg) {
        console.log('Feedback message:')
        console.log(msg.getData().toString('utf-8'));
      });
    }
    ```
8. Adicione o seguinte código para enviar uma mensagem para o seu dispositivo e manipular a mensagem de comentários, quando o dispositivo reconhece a mensagem de cloud para o dispositivo:
   
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
9. Guardar e fechar **SendCloudToDeviceMessage.js** ficheiro.

## <a name="run-the-applications"></a>Executar as aplicações
Pode agora executar as aplicações.

1. A linha de comandos do **simulateddevice** pasta, execute o seguinte comando para enviar telemetria para o IoT Hub e para escutar as mensagens da cloud para o dispositivo:
   
    ```shell
    node SimulatedDevice.js 
    ```
   
    ![Executar a aplicação de dispositivo simulado][img-simulated-device]
2. Uma linha de comandos do **sendcloudtodevicemessage** pasta, execute o comando seguinte para enviar uma mensagem de cloud-para-dispositivo e espere até que os comentários de confirmação:
   
    ```shell
    node SendCloudToDeviceMessage.js 
    ```
   
    ![Executar a aplicação para enviar o comando do cloud-para-dispositivo][img-send-command]
   
   > [!NOTE]
   > Para simplificar, este tutorial não implementa nenhuma política de repetição. No código de produção, deve implementar as políticas de repetição (como um término exponencial), como sugerido no artigo [processamento de erros transitórios](/azure/architecture/best-practices/transient-faults).
   > 
   > 

## <a name="next-steps"></a>Passos Seguintes
Neste tutorial, aprendeu a enviar e receber mensagens da cloud para o dispositivo. 

Para ver exemplos de soluções ponto-a-ponto completas que utilizam o IoT Hub, veja [Acelerador de solução monitorização remota do IoT do Azure].

Para saber mais sobre o desenvolvimento de soluções com o IoT Hub, veja a [guia para programadores do IoT Hub].

<!-- Images -->
[img-simulated-device]: media/iot-hub-node-node-c2d/receivec2d.png
[img-send-command]:  media/iot-hub-node-node-c2d/sendc2d.png

<!-- Links -->

[Introdução ao IoT Hub]: quickstart-send-telemetry-node.md
[IoT Hub developer guide - C2D]: iot-hub-devguide-messaging.md
[guia para programadores do IoT Hub]: iot-hub-devguide.md
[Centro de Programadores do Azure IoT]: https://azure.microsoft.com/develop/iot
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[Portal do Azure]: https://portal.azure.com
[Acelerador de solução monitorização remota do IoT do Azure]: https://azure.microsoft.com/documentation/suites/iot-suite/
