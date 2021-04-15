---
title: Agendar empregos com Azure IoT Hub (Nó) | Microsoft Docs
description: Como agendar um trabalho do Azure IoT Hub para invocar um método direto em vários dispositivos. Utiliza os SDKs Azure IoT para Node.js implementar as aplicações simuladas do dispositivo e uma aplicação de serviço para executar o trabalho.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 08/16/2019
ms.custom: mqtt, devx-track-js, devx-track-azurecli
ms.openlocfilehash: dc0ea9817b9cbc27816354c48abbe26d79a83f04
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107477915"
---
# <a name="schedule-and-broadcast-jobs-nodejs"></a>Horários e trabalhos de transmissão (Node.js)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

O Azure IoT Hub é um serviço totalmente gerido que permite a uma aplicação back-end criar e rastrear empregos que programam e atualizam milhões de dispositivos.  Os postos de trabalho podem ser utilizados para as seguintes ações:

* Atualizar as propriedades pretendidas
* Etiquetas de atualização
* Invocar métodos diretos

Conceptualmente, um trabalho envolve uma destas ações e acompanha o progresso da execução contra um conjunto de dispositivos, que é definido por uma consulta dupla do dispositivo.  Por exemplo, uma aplicação back-end pode usar um trabalho para invocar um método de reboot em 10.000 dispositivos, especificado por uma consulta de twin do dispositivo e agendado para um futuro. Esta aplicação pode então acompanhar o progresso à medida que cada um desses dispositivos recebe e executa o método de reinicialização.

Saiba mais sobre cada uma destas capacidades nestes artigos:

* Dispositivo twin e propriedades: [Começar com os gémeos do dispositivo](iot-hub-node-node-twin-getstarted.md) e [tutorial: Como usar propriedades gémeas do dispositivo](tutorial-device-twins.md)

* Métodos diretos: [Guia de desenvolvimento do IoT Hub - métodos diretos](iot-hub-devguide-direct-methods.md) e [tutorial: métodos diretos](quickstart-control-device-node.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Este tutorial mostrar-lhe como:

* Crie uma aplicação Node.js simulada de dispositivo que tenha um método direto, que permite **o lockDoor**, que pode ser chamado pela solução traseira.

* Crie uma aplicação de consola Node.js que chame o método direto **lockDoor** na aplicação do dispositivo simulado usando um trabalho e atualiza as propriedades desejadas usando uma tarefa de dispositivo.

No final deste tutorial, você tem duas Node.js apps:

* **simDevice.js**, que se conecta ao seu hub IoT com a identidade do dispositivo e recebe um método direto **lockDoor.**

* **scheduleJobService.js**, que chama um método direto na aplicação do dispositivo simulado e atualiza as propriedades desejadas pelo dispositivo twin usando um trabalho.

## <a name="prerequisites"></a>Pré-requisitos

* Node.js versão 10.0.x ou posterior. [Prepare o seu ambiente de desenvolvimento](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) descreve como instalar Node.js para este tutorial no Windows ou linux.

* Uma conta ativa do Azure. (Se não tiver uma conta, pode criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.)

* Certifique-se de que a porta 8883 está aberta na sua firewall. A amostra do dispositivo neste artigo utiliza o protocolo MQTT, que comunica sobre a porta 8883. Este porto pode ser bloqueado em alguns ambientes de rede corporativa e educacional. Para obter mais informações e formas de contornar esta questão, consulte [Connecting to IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registar um novo dispositivo no hub IoT

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Criar uma aplicação de dispositivo simulada

Nesta secção, cria-se uma aplicação de consola Node.js que responde a um método direto chamado pela nuvem, que desencadeia um método **de lockDoor** simulado.

1. Criar uma nova pasta vazia chamada **simDevice**.  Na pasta **simDevice,** crie uma package.jsno ficheiro utilizando o seguinte comando no seu pedido de comando.  Aceite todas as predefinições:

   ```console
   npm init
   ```

2. Na sua instrução de comando na pasta **simDevice,** executar o seguinte comando para instalar o pacote SDK do **dispositivo azure-iot-device** e o pacote **azure-iot-device-mqtt:**

   ```console
   npm install azure-iot-device azure-iot-device-mqtt --save
   ```

3. Utilizando um editor de texto, crie um novo ficheiro **simDevice.js** na pasta **simDevice.**

4. Adicione as seguintes declarações de "exigir" no início do ficheiro **simDevice.js:**

    ```javascript
    'use strict';

    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. Adicione uma variável **connectionString** e utilize-a para criar uma instância do **Cliente**. Substitua o `{yourDeviceConnectionString}` valor do espaço reservado pela cadeia de ligação do dispositivo que copiou anteriormente.

    ```javascript
    var connectionString = '{yourDeviceConnectionString}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. Adicione a seguinte função para manusear o método **lockDoor.**

    ```javascript
    var onLockDoor = function(request, response) {

        // Respond the cloud app for the direct method
        response.send(200, function(err) {
            if (err) {
                console.error('An error occurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });

        console.log('Locking Door!');
    };
    ```

7. Adicione o seguinte código para registar o manipulador para o método **lockDoor.**

   ```javascript
   client.open(function(err) {
        if (err) {
            console.error('Could not connect to IotHub client.');
        }  else {
            console.log('Client connected to IoT Hub. Register handler for lockDoor direct method.');
            client.onDeviceMethod('lockDoor', onLockDoor);
        }
   });
   ```

8. Guarde e feche o ficheiro **simDevice.js.**

> [!NOTE]
> Para facilitar, este tutorial não implementa nenhuma política de repetição. No código de produção, deve implementar políticas de reequipante (como um backoff exponencial), como sugerido no artigo, [Transient Fault Handling](/azure/architecture/best-practices/transient-faults).
>

## <a name="get-the-iot-hub-connection-string"></a>Obtenha a cadeia de conexão do hub IoT

[!INCLUDE [iot-hub-howto-schedule-jobs-shared-access-policy-text](../../includes/iot-hub-howto-schedule-jobs-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Agende postos de trabalho para chamar um método direto e atualizar as propriedades de um dispositivo twin

Nesta secção, cria-se uma aplicação de consola Node.js que inicia um **lockDoor** remoto num dispositivo utilizando um método direto e atualiza as propriedades do dispositivo Twin.

1. Criar uma nova pasta vazia chamada **scheduleJobService**.  Na pasta Do Trabalho de **Programação,** crie uma package.jsno ficheiro utilizando o seguinte comando no seu pedido de comando.  Aceite todas as predefinições:

    ```console
    npm init
    ```

2. No seu comando, na pasta **Do ProgramaJobService,** execute o seguinte comando para instalar o pacote SDK do dispositivo **azure-iothub** e o pacote **azure-iot-device-mqtt:**

    ```console
    npm install azure-iothub uuid --save
    ```

3. Utilizando um editor de texto, crie um novo ficheiro **scheduleJobService.js** na **pasta DoJobService.**

4. Adicione as seguintes declarações de "exigir" no início do ficheiro **scheduleJobService.js:**

    ```javascript
    'use strict';

    var uuid = require('uuid');
    var JobClient = require('azure-iothub').JobClient;
    ```

5. Adicione as seguintes declarações variáveis. Substitua o `{iothubconnectionstring}` valor do espaço reservado pelo valor copiado na cadeia de [ligação do hub IoT](#get-the-iot-hub-connection-string). Se registou um dispositivo diferente do **myDeviceId,** não se esqueça de alterá-lo na condição de consulta.

    ```javascript
    var connectionString = '{iothubconnectionstring}';
    var queryCondition = "deviceId IN ['myDeviceId']";
    var startTime = new Date();
    var maxExecutionTimeInSeconds =  300;
    var jobClient = JobClient.fromConnectionString(connectionString);
    ```

6. Adicione a seguinte função que é usada para monitorizar a execução do trabalho:

    ```javascript
    function monitorJob (jobId, callback) {
        var jobMonitorInterval = setInterval(function() {
            jobClient.getJob(jobId, function(err, result) {
            if (err) {
                console.error('Could not get job status: ' + err.message);
            } else {
                console.log('Job: ' + jobId + ' - status: ' + result.status);
                if (result.status === 'completed' || result.status === 'failed' || result.status === 'cancelled') {
                clearInterval(jobMonitorInterval);
                callback(null, result);
                }
            }
            });
        }, 5000);
    }
    ```

7. Adicione o seguinte código para agendar o trabalho que chama o método do dispositivo:
  
    ```javascript
    var methodParams = {
        methodName: 'lockDoor',
        payload: null,
        responseTimeoutInSeconds: 15 // Timeout after 15 seconds if device is unable to process method
    };

    var methodJobId = uuid.v4();
    console.log('scheduling Device Method job with id: ' + methodJobId);
    jobClient.scheduleDeviceMethod(methodJobId,
                                queryCondition,
                                methodParams,
                                startTime,
                                maxExecutionTimeInSeconds,
                                function(err) {
        if (err) {
            console.error('Could not schedule device method job: ' + err.message);
        } else {
            monitorJob(methodJobId, function(err, result) {
                if (err) {
                    console.error('Could not monitor device method job: ' + err.message);
                } else {
                    console.log(JSON.stringify(result, null, 2));
                }
            });
        }
    });
    ```

8. Adicione o seguinte código para agendar o trabalho para atualizar o dispositivo twin:

    ```javascript
    var twinPatch = {
       etag: '*',
       properties: {
           desired: {
               building: '43',
               floor: 3
           }
       }
    };

    var twinJobId = uuid.v4();

    console.log('scheduling Twin Update job with id: ' + twinJobId);
    jobClient.scheduleTwinUpdate(twinJobId,
                                queryCondition,
                                twinPatch,
                                startTime,
                                maxExecutionTimeInSeconds,
                                function(err) {
        if (err) {
            console.error('Could not schedule twin update job: ' + err.message);
        } else {
            monitorJob(twinJobId, function(err, result) {
                if (err) {
                    console.error('Could not monitor twin update job: ' + err.message);
                } else {
                    console.log(JSON.stringify(result, null, 2));
                }
            });
        }
    });
    ```

9. Guarde e feche o ficheiro **scheduleJobService.js.**

## <a name="run-the-applications"></a>Executar as aplicações

Pode agora executar as aplicações.

1. Na solicitação de comando na pasta **simDevice,** executar o seguinte comando para começar a ouvir o método direto de reinicialização.

    ```console
    node simDevice.js
    ```

2. No pedido de comando na **pasta Do ProgramaJobService,** executar o seguinte comando para ativar os trabalhos para trancar a porta e atualizar o gémeo

    ```console
    node scheduleJobService.js
    ```

3. Vê a resposta do dispositivo ao método direto e ao estado do trabalho na consola.

   O seguinte mostra a resposta do dispositivo ao método direto:

   ![Saída de aplicação de dispositivo simulado](./media/iot-hub-node-node-schedule-jobs/sim-device.png)

   O seguinte mostra os trabalhos de agendamento de serviços para o método direto e a atualização dupla do dispositivo, bem como os postos de trabalho em execução:

   ![Executar a aplicação de dispositivo simulado](./media/iot-hub-node-node-schedule-jobs/schedule-job-service.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, utilizou um trabalho para agendar um método direto para um dispositivo e a atualização das propriedades do dispositivo Twin.

Para continuar a trabalhar com o IoT Hub e padrões de gestão de dispositivos como o controlo remoto sobre a atualização do firmware aéreo, consulte [Tutorial: Como fazer uma atualização de firmware](tutorial-firmware-update.md).

Para continuar a começar com o IoT Hub, consulte [Começar com a Azure IoT Edge.](../iot-edge/quickstart-linux.md)
