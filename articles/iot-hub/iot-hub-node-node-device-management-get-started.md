---
title: Inicie-se com a gestão de dispositivos Azure IoT Hub (Nó)  Microsoft Docs
description: Como usar o gerenciamento de dispositivos do Hub IoT para iniciar uma reinicialização remota do dispositivo. Utiliza o Azure IoT SDK para o Node.js implementar uma aplicação simulada de dispositivo que inclui um método direto e uma aplicação de serviço que invoca o método direto.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: 5b2e4c03347020b5d5fc67927165403f06854e0b
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/09/2020
ms.locfileid: "77110910"
---
# <a name="get-started-with-device-management-nodejs"></a>Começar com a gestão do dispositivo (Node.js)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Este tutorial mostrar-lhe como:

* Utilize o [portal Azure](https://portal.azure.com) para criar um Hub IoT e criar uma identidade de dispositivo no seu hub IoT.

* Crie um aplicativo de dispositivo simulado que contenha um método direto que reinicie esse dispositivo. Os métodos diretos são invocados da nuvem.

* Crie uma aplicação de consola Node.js que chame o método direto de reinicialização na aplicação simulada do dispositivo através do seu hub IoT.

No final deste tutorial, tens duas aplicações de consola Node.js:

* **dmpatterns_getstarted_device.js**, que se conecta ao seu hub IoT com a identidade do dispositivo criada anteriormente, recebe um método direto de reinicialização, simula um reboot físico e reporta o tempo para o último reboot.

* **dmpatterns_getstarted_service.js**, que chama um método direto na aplicação simulada do dispositivo, exibe a resposta e exibe as propriedades reportadas atualizadas.

## <a name="prerequisites"></a>Pré-requisitos

* Node.js versão 10.0.x ou mais tarde. [Prepare o seu ambiente](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) de desenvolvimento descreve como instalar o Node.js para este tutorial no Windows ou no Linux.

* Uma conta ativa do Azure. (Se não tiver uma conta, pode criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.)

* Certifique-se de que a porta 8883 está aberta na sua firewall. A amostra do dispositivo neste artigo utiliza o protocolo MQTT, que comunica sobre a porta 8883. Este porto pode estar bloqueado em alguns ambientes de rede corporativa e educativa. Para obter mais informações e formas de resolver este problema, consulte [A Ligação ao IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registe um novo dispositivo no hub IoT

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Criar uma aplicação de dispositivo simulada

Nesta secção, pode:

* Criar uma aplicação de consola Node.js que responde a um método direto chamado pela cloud

* Desencadear um reboot simulado do dispositivo

* Usar as propriedades relatadas para habilitar consultas de dispositivo de dispositivos para identificar os dispositivos e quando eles foram reinicializados pela última vez

1. Crie uma pasta vazia designada **manageddevice**.  Na pasta **manageddevice**, crie um ficheiro package.json com o seguinte comando na sua linha de comandos.  Aceite todas as predefinições:

    ```cmd/sh
    npm init
    ```

2. Ao seu pedido de comando na pasta do **dispositivo gerido,** execute o seguinte comando para instalar a embalagem SDK do **dispositivo de dispositivo azul-iot** e a embalagem **azure-iot-device-mqtt:**

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. Utilizando um editor de texto, crie um ficheiro **dmpatterns_getstarted_device.js** na pasta **do dispositivo gerido.**

4. Adicione as seguintes declarações 'requirer' no início do ficheiro **dmpatterns_getstarted_device.js:**

    ```javascript
    'use strict';

    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. Adicione uma variável **connectionString** e utilize-a para criar uma instância do **Cliente**.  Substitua o valor do espaço reservado `{yourdeviceconnectionstring}` pela cadeia de ligação do dispositivo que copiou anteriormente no [Registo de um novo dispositivo no hub IoT](#register-a-new-device-in-the-iot-hub).  

    ```javascript
    var connectionString = '{yourdeviceconnectionstring}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. Adicione a seguinte função para implementar o método direto no dispositivo

    ```javascript
    var onReboot = function(request, response) {

        // Respond the cloud app for the direct method
        response.send(200, 'Reboot started', function(err) {
            if (err) {
                console.error('An error occurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });

        // Report the reboot before the physical restart
        var date = new Date();
        var patch = {
            iothubDM : {
                reboot : {
                    lastReboot : date.toISOString(),
                }
            }
        };

        // Get device Twin
        client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                console.log('twin acquired');
                twin.properties.reported.update(patch, function(err) {
                    if (err) throw err;
                    console.log('Device reboot twin state reported')
                });  
            }
        });

        // Add your device's reboot API for physical restart.
        console.log('Rebooting!');
    };
    ```

7. Abra a ligação ao seu hub IoT e inicie o ouvinte de método direto:

    ```javascript
    client.open(function(err) {
        if (err) {
            console.error('Could not open IotHub client');
        }  else {
            console.log('Client opened.  Waiting for reboot method.');
            client.onDeviceMethod('reboot', onReboot);
        }
    });
    ```

8. Guarde e feche o ficheiro **dmpatterns_getstarted_device.js.**

> [!NOTE]
> Para facilitar, este tutorial não implementa nenhuma política de repetição. No código de produção, deve implementar políticas de retry (como um backoff exponencial), como sugerido no artigo, [Transient Fault Handling](/azure/architecture/best-practices/transient-faults).

## <a name="get-the-iot-hub-connection-string"></a>Obter a cadeia de conexão do Hub IoT

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Disparar uma reinicialização remota no dispositivo usando um método direto

Nesta secção, cria-se uma aplicação de consola Node.js que inicia um reboot remoto num dispositivo utilizando um método direto. O aplicativo usa consultas de dispositivo de dispositivos para descobrir a hora da última reinicialização para esse dispositivo.

1. Crie uma pasta vazia chamada **triggerrebootondevice**. Na pasta **do triggerrebootondevice,** crie um ficheiro package.json utilizando o seguinte comando no seu pedido de comando. Aceite todas as predefinições:

    ```cmd/sh
    npm init
    ```

2. Ao seu pedido de comando na pasta do dispositivo de arranque de **gatilho,** execute o seguinte comando para instalar a embalagem SDK do dispositivo **azure-iothub** e a embalagem **azure-iot-device-mqtt:**

    ```cmd/sh
    npm install azure-iothub --save
    ```

3. Utilizando um editor de texto, crie um ficheiro **dmpatterns_getstarted_service.js** na pasta **do triggerrebootondevice.**

4. Adicione as seguintes declarações 'requirer' no início do ficheiro **dmpatterns_getstarted_service.js:**

    ```javascript
    'use strict';

    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```

5. Adicione as seguintes declarações variáveis e substitua o valor do espaço reservado `{iothubconnectionstring}` pela cadeia de ligação do hub IoT que copiou anteriormente na cadeia de ligação do [hub IoT:](#get-the-iot-hub-connection-string)

    ```javascript
    var connectionString = '{iothubconnectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToReboot = 'myDeviceId';
    ```

6. Adicione a seguinte função para invocar o método do dispositivo para reiniciar o dispositivo-alvo:

    ```javascript
    var startRebootDevice = function(twin) {

        var methodName = "reboot";

        var methodParams = {
            methodName: methodName,
            payload: null,
            timeoutInSeconds: 30
        };

        client.invokeDeviceMethod(deviceToReboot, methodParams, function(err, result) {
            if (err) {
                console.error("Direct method error: "+err.message);
            } else {
                console.log("Successfully invoked the device to reboot.");  
            }
        });
    };
    ```

7. Adicione a seguinte função à consulta do dispositivo e obtenha o último tempo de reinicialização:

    ```javascript
    var queryTwinLastReboot = function() {

        registry.getTwin(deviceToReboot, function(err, twin){

            if (twin.properties.reported.iothubDM != null)
            {
                if (err) {
                    console.error('Could not query twins: ' + err.constructor.name + ': ' + err.message);
                } else {
                    var lastRebootTime = twin.properties.reported.iothubDM.reboot.lastReboot;
                    console.log('Last reboot time: ' + JSON.stringify(lastRebootTime, null, 2));
                }
            } else 
                console.log('Waiting for device to report last reboot time.');
        });
    };
    ```

8. Adicione o seguinte código para ligar para as funções que desencadeiam o método direto de reinicialização e consulta para o último tempo de reinicialização:

    ```javascript
    startRebootDevice();
    setInterval(queryTwinLastReboot, 2000);
    ```

9. Guarde e feche o ficheiro **dmpatterns_getstarted_service.js.**

## <a name="run-the-apps"></a>Executar as aplicações

Agora você está pronto para executar os aplicativos.

1. No pedido de comando na pasta do **dispositivo gerido,** execute o seguinte comando para começar a ouvir o método direct de reinicialização.

    ```cmd/sh
    node dmpatterns_getstarted_device.js
    ```

2. No pedido de comando na pasta do dispositivo de arranque do **gatilho,** execute o seguinte comando para acionar o reboot remoto e a consulta para que o dispositivo gémeo encontre o último tempo de reinício.

    ```cmd/sh
    node dmpatterns_getstarted_service.js
    ```

3. Vê a resposta do dispositivo ao método de reinicialização direta e ao estado de reinício na consola.

   O seguinte mostra a resposta do dispositivo ao método direto de reinicialização enviado pelo serviço:

   ![saída de aplicativo gerido dispositivo](./media/iot-hub-node-node-device-management-get-started/device.png)

   O seguinte mostra o serviço que desencadeia o reboot e vota o dispositivo twin para o último tempo de reinicialização:

   ![saída de aplicações triggerrebootondevice](./media/iot-hub-node-node-device-management-get-started/service.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
