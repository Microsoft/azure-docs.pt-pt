---
title: Mensagens cloud-to-device com Azure IoT Hub (Nó) [ Microsoft Docs
description: Como enviar mensagens cloud-to-device para um dispositivo a partir de um hub Azure IoT usando os SDKs Azure IoT para Node.js. Modifica uma aplicação simulada de dispositivopara receber mensagens cloud-to-device e modificar uma aplicação de back-end para enviar as mensagens cloud-to-device.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: javascript
ms.topic: conceptual
ms.date: 06/16/2017
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: a1e0e3623692321e5c69e4b9c5a26ff82a1c47a0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81732343"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-nodejs"></a>Envie mensagens cloud-to-device com IoT Hub (Node.js)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

O Azure IoT Hub é um serviço totalmente gerido que ajuda a permitir comunicações bidirecionais fiáveis e seguras entre milhões de dispositivos e uma solução traseira. A [telemetria Enviar de um dispositivo para um quickstart de hub IoT](quickstart-send-telemetry-node.md) mostra como criar um hub IoT, fornecer uma identidade de dispositivo no mesmo, e codificar uma aplicação simulada de dispositivo que envia mensagens dispositivo-para-nuvem.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Este tutorial baseia-se em [Enviar a telemetria de um dispositivo para um hub IoT](quickstart-send-telemetry-node.md). Mostra como:

* A partir da sua solução traseira, envie mensagens cloud-to-device para um único dispositivo através do IoT Hub.
* Receba mensagens cloud-to-device num dispositivo.
* A partir da sua solução na parte de trás, solicite reconhecimento de entrega *(feedback)* para mensagens enviadas para um dispositivo do IoT Hub.

Pode encontrar mais informações sobre mensagens cloud-to-device no guia de [desenvolvimento do IoT Hub](iot-hub-devguide-messaging.md).

No final deste tutorial, executa duas aplicações de consola Node.js:

* **Simulado Dispositivo**, uma versão modificada da aplicação criada em [Enviar telemetria de um dispositivo para um hub IoT](quickstart-send-telemetry-node.md), que se conecta ao seu hub IoT e recebe mensagens cloud-to-device.

* **SendCloudToDeviceMessage**, que envia uma mensagem cloud-to-device para a aplicação simulada do dispositivo através do IoT Hub, e recebe o seu reconhecimento de entrega.

> [!NOTE]
> O IoT Hub tem suporte SDK para muitas plataformas e idiomas de dispositivos (incluindo C, Java, Python e Javascript) através de SDKs do dispositivo Azure IoT. Para obter instruções passo a passo sobre como ligar o seu dispositivo ao código deste tutorial e, em geral, ao Azure IoT Hub, consulte o Centro de [Desenvolvimento Azure IoT](https://azure.microsoft.com/develop/iot).
>

## <a name="prerequisites"></a>Pré-requisitos

* Node.js versão 10.0.x ou mais tarde. [Prepare o seu ambiente](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) de desenvolvimento descreve como instalar o Node.js para este tutorial no Windows ou no Linux.

* Uma conta ativa do Azure. (Se não tiver uma conta, pode criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial) em apenas alguns minutos.)

* Certifique-se de que a porta 8883 está aberta na sua firewall. A amostra do dispositivo neste artigo utiliza o protocolo MQTT, que comunica sobre a porta 8883. Este porto pode estar bloqueado em alguns ambientes de rede corporativa e educativa. Para obter mais informações e formas de resolver este problema, consulte [A Ligação ao IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="receive-messages-in-the-simulated-device-app"></a>Receber mensagens na aplicação de dispositivo simulado

Nesta secção, modifica a aplicação simulada de dispositivo que criou no [Enviar telemetria de um dispositivo para um hub IoT](quickstart-send-telemetry-node.md) para receber mensagens cloud-to-device do hub IoT.

1. Utilizando um editor de texto, abra o ficheiro **SimulatedDevice.js.** Este ficheiro está localizado na pasta do **iot-hub\Quickstarts\simulado** dispositivo fora da pasta raiz do código de amostra Node.js que descarregou na [telemetria Enviar de um dispositivo para um hub IoT](quickstart-send-telemetry-node.md) quickstart.

2. Registe um manipulador com o cliente do dispositivo para receber mensagens enviadas do IoT Hub. Adicione a `client.on` chamada logo após a linha que cria o cliente do dispositivo como no seguinte corte:

    ```javascript
    var client = DeviceClient.fromConnectionString(connectionString, Mqtt);

    client.on('message', function (msg) {
      console.log('Id: ' + msg.messageId + ' Body: ' + msg.data);
      client.complete(msg, function (err) {
        if (err) {
          console.error('complete error: ' + err.toString());
        } else {
          console.log('complete sent');
        }
      });
    });
    ```

    Neste exemplo, o dispositivo invoca a função **completa** para notificar o IoT Hub de que processou a mensagem. A chamada para **ser concluída** não é necessária se estiver a utilizar o transporte MQTT e puder ser omitida. É necessário para HTTPS e AMQP.
  
   > [!NOTE]
   > Se utilizar https em vez de MQTT ou AMQP como transporte, a instância **Do Cliente do Dispositivo** verifica frequentemente mensagens do IoT Hub (menos do que a cada 25 minutos). Para obter mais informações sobre as diferenças entre o suporte MQTT, AMQP e HTTPS, e a aceleração do [IoT Hub,](iot-hub-devguide-messaging.md)consulte o guia de desenvolvimento do IoT Hub .
   >

## <a name="get-the-iot-hub-connection-string"></a>Obtenha a cadeia de ligação do hub IoT

Neste artigo, cria um serviço de backend para enviar mensagens cloud-to-device através do hub IoT que criou em [Enviar telemetria de um dispositivo para um hub IoT](quickstart-send-telemetry-node.md). Para enviar mensagens cloud-to-device, o seu serviço necessita da permissão de ligação do **serviço.** Por padrão, cada Hub IoT é criado com uma política de acesso partilhado chamada **serviço** que concede esta permissão.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Envie uma mensagem cloud-to-device

Nesta secção, cria-se uma aplicação de consola Node.js que envia mensagens cloud-to-device para a aplicação simulada do dispositivo. Precisa da identificação do dispositivo que adicionou na [telemetria Enviar de um dispositivo para um hub IoT](quickstart-send-telemetry-node.md) arranque rápido. Você também precisa da cadeia de ligação do hub IoT que copiou anteriormente na cadeia de ligação do [hub IoT](#get-the-iot-hub-connection-string).

1. Crie uma pasta vazia chamada **mensagem enviar cloudtodevice**. Na pasta **enviar cloudtodevicemessage,** crie um ficheiro package.json utilizando o seguinte comando no seu pedido de comando. Aceite todas as predefinições:

    ```shell
    npm init
    ```

2. Ao seu pedido de comando na pasta **enviarcloudtodevicemessage,** execute o seguinte comando para instalar a embalagem **azure-iothub:**

    ```shell
    npm install azure-iothub --save
    ```

3. Utilizando um editor de texto, crie um ficheiro **SendCloudToDeviceMessage.js** na pasta **enviarmensagens de envio.**

4. Adicione as `require` seguintes declarações no início do ficheiro **SendCloudToDeviceMessage.js:**

    ```javascript
    'use strict';

    var Client = require('azure-iothub').Client;
    var Message = require('azure-iot-common').Message;
    ```

5. Adicione o seguinte código ao ficheiro **EnviarCloudToDeviceMessage.js.** Substitua os valores do espaço de ligação "{iot hub}" e "{device id}" com a cadeia de ligação do hub IoT e o ID do dispositivo que tenha notado anteriormente:

    ```javascript
    var connectionString = '{iot hub connection string}';
    var targetDevice = '{device id}';

    var serviceClient = Client.fromConnectionString(connectionString);
    ```

6. Adicione a seguinte função aos resultados de operação de impressão à consola:

    ```javascript
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. Adicione a seguinte função à impressão de mensagens de feedback de entrega à consola:

    ```javascript
    function receiveFeedback(err, receiver){
      receiver.on('message', function (msg) {
        console.log('Feedback message:')
        console.log(msg.getData().toString('utf-8'));
      });
    }
    ```

8. Adicione o seguinte código para enviar uma mensagem ao seu dispositivo e manuseie a mensagem de feedback quando o dispositivo reconhecer a mensagem cloud-to-device:

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

9. Guardar e fechar o ficheiro **SendCloudToDeviceMessage.js.**

## <a name="run-the-applications"></a>Executar as aplicações

Pode agora executar as aplicações.

1. No pedido de comando na pasta do **dispositivo simulado,** execute o seguinte comando para enviar telemetria para o Hub IoT e para ouvir mensagens cloud-to-device:

    ```shell
    node SimulatedDevice.js
    ```

    ![Executar a aplicação de dispositivo simulado](./media/iot-hub-node-node-c2d/receivec2d.png)

2. Numa solicitação de comando na pasta **de mensagem de envio** de dispositivos, execute o seguinte comando para enviar uma mensagem cloud-to-device e aguarde o feedback de reconhecimento:

    ```shell
    node SendCloudToDeviceMessage.js
    ```

    ![Executar a aplicação para enviar o comando cloud-to-device](./media/iot-hub-node-node-c2d/sendc2d.png)

   > [!NOTE]
   > Para a simplicidade, este tutorial não implementa qualquer política de retenção. No código de produção, deve implementar políticas de retry (como backoff exponencial), como sugerido no artigo, [Transient Fault Handling](/azure/architecture/best-practices/transient-faults).
   >

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a enviar e a receber mensagens cloud-to-device.

Para ver exemplos de soluções completas de ponta a ponta que utilizam o Hub IoT, consulte o acelerador de solução de [monitorização remota Azure IoT](https://azure.microsoft.com/documentation/suites/iot-suite/).

Para saber mais sobre o desenvolvimento de soluções com o IoT Hub, consulte o guia de [desenvolvimento do IoT Hub.](iot-hub-devguide.md)
