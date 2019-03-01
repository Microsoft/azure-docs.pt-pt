---
title: Agendar tarefas com o IoT Hub do Azure (Node) | Documentos da Microsoft
description: Como agendar um trabalho do IoT Hub do Azure para invocar um método direto em vários dispositivos. Utilize os SDKs IoT do Azure para node. js para implementar as aplicações de dispositivo simulado e uma aplicação de serviço para executar a tarefa.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 10/06/2017
ms.openlocfilehash: 8f2130c1927522a587136fde3423e971a0555026
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "57010043"
---
# <a name="schedule-and-broadcast-jobs-node"></a>Agendar e difundir tarefas (Node)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

O IoT Hub do Azure é um serviço totalmente gerido que permite uma aplicação de back-end criar e controlar tarefas que agendem e atualizar milhões de dispositivos.  Tarefas podem ser utilizadas para as seguintes ações:

* Atualizar as propriedades pretendidas
* Etiquetas de atualização
* Invocar métodos diretos

Conceitualmente, uma tarefa encapsula uma destas ações e controla o progresso de execução com um conjunto de dispositivos, que é definido por uma consulta do dispositivo duplo.  Por exemplo, uma aplicação de back-end pode utilizar uma tarefa para invocar um método de reinício em 10 000 dispositivos, especificado por uma consulta do dispositivo duplo e agendado num momento futuro.  Esse aplicativo, em seguida, pode controlar o progresso à medida que cada um desses dispositivos receber e executar o método de reinício.

Saiba mais sobre cada uma destas capacidades nestes artigos:

* Dispositivo duplo e propriedades: [Introdução aos dispositivos duplos] [ lnk-get-started-twin] e [Tutorial: Como utilizar propriedades dos dispositivos duplos][lnk-twin-props]
* Métodos diretos: [Guia do programador do IoT Hub - métodos diretos] [ lnk-dev-methods] e [Tutorial: métodos diretos][lnk-c2d-methods]

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Este tutorial mostrar-lhe como:

* Criar uma aplicação de dispositivo simulado do node. js que possui um método direto, que permite **lockDoor**, que pode ser chamado pela solução de back-end.
* Criar uma aplicação de consola node. js que chama o **lockDoor** método direto na aplicação do dispositivo simulado utilizando uma tarefa e atualizações as propriedades pretendidas, usar um trabalho do dispositivo.

No final deste tutorial, tem duas aplicações node. js:

**simDevice.js**, que liga ao seu hub IoT com a identidade de dispositivo e recebe um **lockDoor** método direto.

**scheduleJobService.js**, que chama um método direto na aplicação do dispositivo simulado e atualiza o dispositivo duplo pretendido propriedades usando uma tarefa.

Para concluir este tutorial, precisa do seguinte:

* Versão node. js 4.0.x ou posterior, <br/>  [Preparar o ambiente de desenvolvimento] [ lnk-dev-setup] descreve como instalar o node. js para este tutorial no Windows ou Linux.
* Uma conta ativa do Azure. (Se não tiver uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.)

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Obter cadeia de ligação do IoT hub

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Criar uma aplicação de dispositivo simulada
Nesta secção, vai criar uma aplicação de consola node. js que responde a um método direto chamado pela cloud, que dispara um simulado **lockDoor** método.

1. Criar uma nova pasta vazia designada **simDevice**.  Na **simDevice** pasta, crie um ficheiro Package. JSON com o seguinte comando na sua linha de comandos.  Aceite todas as predefinições:
   
    ```
    npm init
    ```
2. Sua linha de comandos do **simDevice** pasta, execute o seguinte comando para instalar o **do azure-iot-device** pacote do SDK de dispositivo e **azure-iot-device-mqtt** pacote:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. Com um editor de texto, crie um novo **simDevice.js** de ficheiros a **simDevice** pasta.
4. Adicione as seguintes declarações no início de "necessitam" a **simDevice.js** ficheiro:
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```
5. Adicione uma variável **connectionString** e utilize-a para criar uma instância do **Cliente**.  
   
    ```
    var connectionString = 'HostName={youriothostname};DeviceId={yourdeviceid};SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```
6. Adicione a seguinte função para processar a **lockDoor** método.
   
    ```
    var onLockDoor = function(request, response) {
   
        // Respond the cloud app for the direct method
        response.send(200, function(err) {
            if (!err) {
                console.error('An error occurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
   
        console.log('Locking Door!');
    };
    ```
7. Adicione o seguinte código para registrar o manipulador para o **lockDoor** método.
   
    ```
    client.open(function(err) {
        if (err) {
            console.error('Could not connect to IotHub client.');
        }  else {
            console.log('Client connected to IoT Hub. Register handler for lockDoor direct method.');
            client.onDeviceMethod('lockDoor', onLockDoor);
        }
    });
    ```
8. Guarde e feche o **simDevice.js** ficheiro.

> [!NOTE]
> Para facilitar, este tutorial não implementa nenhuma política de repetição. No código de produção, deve implementar as políticas de repetição (como um término exponencial), como sugerido no artigo [processamento de erros transitórios](/azure/architecture/best-practices/transient-faults).
> 
> 

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Agendar tarefas para chamar um método direto e a atualizar as propriedades de um dispositivo duplo
Nesta secção, vai criar uma aplicação de consola node. js que inicia um remoto **lockDoor** num dispositivo através de um método direto e atualizar as propriedades do dispositivo duplo.

1. Criar uma nova pasta vazia designada **scheduleJobService**.  Na **scheduleJobService** pasta, crie um ficheiro Package. JSON com o seguinte comando na sua linha de comandos.  Aceite todas as predefinições:
   
    ```
    npm init
    ```
2. Sua linha de comandos do **scheduleJobService** pasta, execute o seguinte comando para instalar o **do azure-iothub** pacote do SDK de dispositivo e **azure-iot-device-mqtt** pacote:
   
    ```
    npm install azure-iothub uuid --save
    ```
3. Com um editor de texto, crie um novo **scheduleJobService.js** de ficheiros a **scheduleJobService** pasta.
4. Adicione as seguintes declarações no início de "necessitam" a **dmpatterns_gscheduleJobServiceetstarted_service.js** ficheiro:
   
    ```
    'use strict';
   
    var uuid = require('uuid');
    var JobClient = require('azure-iothub').JobClient;
    ```
5. Adicione as seguintes declarações de variável e substitua os valores de marcador de posição:
   
    ```
    var connectionString = '{iothubconnectionstring}';
    var queryCondition = "deviceId IN ['myDeviceId']";
    var startTime = new Date();
    var maxExecutionTimeInSeconds =  300;
    var jobClient = JobClient.fromConnectionString(connectionString);
    ```
6. Adicione a seguinte função que é utilizada para monitorizar a execução da tarefa:
   
    ```
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
7. Adicione o seguinte código para agendar a tarefa que chama o método de dispositivo:
   
    ```
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
8. Adicione o seguinte código para agendar a tarefa para atualizar o dispositivo duplo:
   
    ```
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
9. Guarde e feche o **scheduleJobService.js** ficheiro.

## <a name="run-the-applications"></a>Executar as aplicações
Pode agora executar as aplicações.

1. A linha de comandos do **simDevice** pasta, execute o seguinte comando para começar a escutar o método direto de reinício.
   
    ```
    node simDevice.js
    ```
2. A linha de comandos do **scheduleJobService** pasta, execute o seguinte comando para acionar as tarefas para a porta de bloqueio e atualizar o duplo
   
    ```
    node scheduleJobService.js
    ```
3. Ver a resposta de dispositivo para o método direto na consola do.

## <a name="next-steps"></a>Passos Seguintes
Neste tutorial, utilizou uma tarefa para agendar um método direto a um dispositivo e a atualização das propriedades do dispositivo duplo.

Para continuar a introdução ao IoT Hub e padrões de gestão de dispositivos como remota sobre a atualização de firmware do ar, consulte:

[Tutorial: Como fazer uma atualização de firmware][lnk-fwupdate]

Para continuar a introdução ao IoT Hub, veja [introdução ao Azure IoT Edge][lnk-iot-edge].

[lnk-get-started-twin]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-props]: tutorial-device-twins.md
[lnk-c2d-methods]: quickstart-control-device-node.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-fwupdate]: tutorial-firmware-update.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
