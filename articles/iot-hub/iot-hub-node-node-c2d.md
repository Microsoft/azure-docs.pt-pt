---
title: Mensagens em nuvem-para-dispositivo com Azure IoT Hub (Nó) Microsoft Docs
description: Como enviar mensagens nuvem-para-dispositivo para um dispositivo a partir de um hub Azure IoT usando os SDKs Azure IoT para Node.js. Modifica uma aplicação de dispositivo simulada para receber mensagens nuvem-a-dispositivo e modifica uma aplicação de back-end para enviar as mensagens cloud-to-device.
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
- devx-track-js
ms.openlocfilehash: e398138f12c38e5235a0004679d9574dbde607db
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91446882"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-nodejs"></a>Envie mensagens nuvem-para-dispositivo com IoT Hub (Node.js)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

O Azure IoT Hub é um serviço totalmente gerido que ajuda a permitir comunicações biducionais fiáveis e seguras entre milhões de dispositivos e uma solução traseira. O [envio de telemetria de um dispositivo para um quickstart do hub IoT](quickstart-send-telemetry-node.md) mostra como criar um hub IoT, fornecê-lo e codificar uma aplicação de dispositivo simulado que envia mensagens dispositivo-a-nuvem.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Este tutorial baseia-se no [Envio de telemetria de um dispositivo para um hub IoT](quickstart-send-telemetry-node.md). Mostra como:

* A partir da parte de trás da sua solução, envie mensagens nuvem-dispositivo para um único dispositivo através do IoT Hub.
* Receba mensagens nuvem-dispositivo num dispositivo.
* A partir da sua solução back end, solicite o reconhecimento de entrega *(feedback)* para mensagens enviadas para um dispositivo do IoT Hub.

Pode encontrar mais informações sobre mensagens nuvem-dispositivo no [guia de desenvolvimento do IoT Hub.](iot-hub-devguide-messaging.md)

No final deste tutorial, você executou duas aplicações Node.js para consolas:

* **SimulatedDevice**, uma versão modificada da app criada em [Enviar telemetria de um dispositivo para um hub IoT](quickstart-send-telemetry-node.md), que se conecta ao seu hub IoT e recebe mensagens nuvem-dispositivo.

* **SendCloudToDeviceMessage**, que envia uma mensagem nuvem-para-dispositivo para a aplicação do dispositivo simulado através do IoT Hub, e depois recebe o seu reconhecimento de entrega.

> [!NOTE]
> O IoT Hub tem suporte SDK para muitas plataformas e idiomas de dispositivos (incluindo C, Java, Python e Javascript) através de SDKs de dispositivoS Azure IoT. Para obter instruções passo a passo sobre como ligar o seu dispositivo ao código deste tutorial, e geralmente ao Azure IoT Hub, consulte o [Azure IoT Developer Center](https://azure.microsoft.com/develop/iot).
>

## <a name="prerequisites"></a>Pré-requisitos

* Node.js versão 10.0.x ou posterior. [Prepare o seu ambiente de desenvolvimento](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) descreve como instalar Node.js para este tutorial no Windows ou linux.

* Uma conta ativa do Azure. (Se não tiver uma conta, pode criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial) em apenas alguns minutos.)

* Certifique-se de que a porta 8883 está aberta na sua firewall. A amostra do dispositivo neste artigo utiliza o protocolo MQTT, que comunica sobre a porta 8883. Este porto pode ser bloqueado em alguns ambientes de rede corporativa e educacional. Para obter mais informações e formas de contornar esta questão, consulte [Connecting to IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="receive-messages-in-the-simulated-device-app"></a>Receba mensagens na aplicação do dispositivo simulado

Nesta secção, modifica a aplicação de dispositivo simulado criada em [Enviar telemetria de um dispositivo para um hub IoT](quickstart-send-telemetry-node.md) para receber mensagens nuvem-dispositivo do hub IoT.

1. Utilizando um editor de texto, abra o ficheiro **SimulatedDevice.js.** Este ficheiro encontra-se na pasta **iot-hub\Quickstarts\simulated device** fora da pasta raiz do Node.js código de amostra que descarregou no [Enviar telemetria de um dispositivo para um quickstart do hub IoT.](quickstart-send-telemetry-node.md)

2. Registe um manipulador com o cliente do dispositivo para receber mensagens enviadas do IoT Hub. Adicione a chamada `client.on` logo após a linha que cria o cliente do dispositivo como no seguinte corte:

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

Neste exemplo, o dispositivo invoca a função **completa** para notificar o IoT Hub de que processou a mensagem e que pode ser removido com segurança da fila do dispositivo. A chamada para **completar** não é necessária se estiver a utilizar o transporte MQTT e puder ser omitida. É necessário para AMQP e HTTPS.

Com AMQP e HTTPS, mas não MQTT, o dispositivo também pode:

* Abandone uma mensagem, o que resulta em IoT Hub manter a mensagem na fila do dispositivo para consumo futuro.
* Rejeite uma mensagem que remova permanentemente a mensagem da fila do dispositivo.

Se algo acontecer que impeça o dispositivo de completar, abandonar ou rejeitar a mensagem, o IoT Hub irá, após um período de tempo fixo, fazer fila para a entrega novamente. Por este motivo, a lógica de processamento de mensagens na aplicação do dispositivo deve ser *idempotente*– de modo a que receber a mesma mensagem várias vezes produza o mesmo resultado.

Para obter informações mais detalhadas sobre como o IoT Hub processa mensagens nuvem-dispositivo, incluindo detalhes do ciclo de vida da mensagem nuvem-para-dispositivo, consulte [Enviar mensagens cloud-to-device a partir de um hub IoT](iot-hub-devguide-messages-c2d.md).
  
> [!NOTE]
> Se utilizar HTTPS em vez de MQTT ou AMQP como transporte, o **exemplo de DeviceClient** verifica frequentemente as mensagens do IoT Hub (um mínimo de cada 25 minutos). Para obter mais informações sobre as diferenças entre suporte MQTT, AMQP e HTTPS, consulte [a orientação de comunicações Cloud-to-device](iot-hub-devguide-c2d-guidance.md) e [Escolha um protocolo de comunicação](iot-hub-devguide-protocols.md).
>

## <a name="get-the-iot-hub-connection-string"></a>Obtenha a cadeia de conexão do hub IoT

Neste artigo, cria um serviço de backend para enviar mensagens nuvem-a-dispositivo através do hub IoT que criou Enviar por email o artigo [Telemetria para um hub IoT](quickstart-send-telemetry-node.md). Para enviar mensagens nuvem-para-dispositivo, o seu serviço necessita da permissão **de ligação de serviço.** Por padrão, cada IoT Hub é criado com uma política de acesso compartilhado chamada **serviço** que concede esta permissão.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Envie uma mensagem nuvem-a-dispositivo

Nesta secção, cria-se uma aplicação de consola Node.js que envia mensagens nuvem-para-dispositivo para a aplicação do dispositivo simulado. Precisa do dispositivo ID do dispositivo que adicionou na [telemetria Enviar telemetria de um dispositivo para um quickstart do hub IoT.](quickstart-send-telemetry-node.md) Também precisa da cadeia de ligação do hub IoT que copiou anteriormente na [cadeia de ligação do hub IoT](#get-the-iot-hub-connection-string).

1. Crie uma pasta vazia chamada **sendcloudtodevicemessage**. Na pasta **de assistência enviada,** crie uma package.jsno ficheiro utilizando o seguinte comando no seu pedido de comando. Aceite todas as predefinições:

    ```shell
    npm init
    ```

2. No seu comando, na pasta **sendcloudtodevicemssage,** execute o seguinte comando para instalar o pacote **azure-iothub:**

    ```shell
    npm install azure-iothub --save
    ```

3. Utilizando um editor de texto, crie um ficheiro **SendCloudToDeviceMessage.js** na pasta **de assistência enviada.**

4. Adicione as `require` seguintes declarações no início do ficheiro **SendCloudToDeviceMessage.js:**

    ```javascript
    'use strict';

    var Client = require('azure-iothub').Client;
    var Message = require('azure-iot-common').Message;
    ```

5. Adicione o seguinte código ao **SendCloudToDeviceMessage.js** ficheiro. Substitua os valores do espaço reservado "{iot hub"} e "{device id}" pela cadeia de ligação do hub IoT e pelo ID do dispositivo que observou anteriormente:

    ```javascript
    var connectionString = '{iot hub connection string}';
    var targetDevice = '{device id}';

    var serviceClient = Client.fromConnectionString(connectionString);
    ```

6. Adicione a seguinte função aos resultados de funcionamento de impressão à consola:

    ```javascript
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. Adicione a seguinte função para imprimir mensagens de feedback de entrega na consola:

    ```javascript
    function receiveFeedback(err, receiver){
      receiver.on('message', function (msg) {
        console.log('Feedback message:')
        console.log(msg.getData().toString('utf-8'));
      });
    }
    ```

8. Adicione o seguinte código para enviar uma mensagem para o seu dispositivo e manuseie a mensagem de feedback quando o dispositivo reconhecer a mensagem nuvem-para-dispositivo:

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

9. Guarde e feche **SendCloudToDeviceMessage.js** ficheiro.

## <a name="run-the-applications"></a>Executar as aplicações

Pode agora executar as aplicações.

1. Na instrução de comando na pasta do **dispositivo simulado,** executar o seguinte comando para enviar telemetria para ioT Hub e para ouvir mensagens nuvem-dispositivo:

    ```shell
    node SimulatedDevice.js
    ```

    ![Executar a aplicação de dispositivo simulado](./media/iot-hub-node-node-c2d/receivec2d.png)

2. Numa mensagem de comando na pasta **de assistência enviada,** executar o seguinte comando para enviar uma mensagem nuvem-a-dispositivo e aguardar o feedback do reconhecimento:

    ```shell
    node SendCloudToDeviceMessage.js
    ```

    ![Executar a aplicação para enviar o comando nuvem-para-dispositivo](./media/iot-hub-node-node-c2d/sendc2d.png)

   > [!NOTE]
   > Para simplificar, este tutorial não implementa nenhuma política de retenção. No código de produção, deve implementar políticas de reequipante (como o backoff exponencial), como sugerido no artigo, [Transient Fault Handling](/azure/architecture/best-practices/transient-faults).
   >

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a enviar e receber mensagens cloud-to-device.

Para ver exemplos de soluções completas de ponta a ponta que utilizam o Hub IoT, consulte o [acelerador de solução de monitorização remota Azure IoT](https://azure.microsoft.com/documentation/suites/iot-suite/).

Para saber mais sobre o desenvolvimento de soluções com o IoT Hub, consulte o guia de desenvolvimento do [IoT Hub.](iot-hub-devguide.md)
