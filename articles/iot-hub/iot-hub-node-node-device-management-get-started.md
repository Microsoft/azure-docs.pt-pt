---
title: Introdução ao gerenciamento de dispositivos do Hub IoT do Azure (nó) | Microsoft Docs
description: Como usar o gerenciamento de dispositivos do Hub IoT para iniciar uma reinicialização remota do dispositivo. Você usa o SDK do IoT do Azure para node. js para implementar um aplicativo de dispositivo simulado que inclui um método direto e um aplicativo de serviço que invoca o método direto.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: 98ec53d384186968d69c3f84cdfa12fbdbe92b71
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147453"
---
# <a name="get-started-with-device-management-nodejs"></a>Introdução ao gerenciamento de dispositivos (Node. js)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Este tutorial mostrar-lhe como:

* Use o [portal do Azure](https://portal.azure.com) para criar um hub IOT e criar uma identidade de dispositivo no Hub IOT.

* Crie um aplicativo de dispositivo simulado que contenha um método direto que reinicie esse dispositivo. Os métodos diretos são invocados da nuvem.

* Crie um aplicativo de console do node. js que chame o método direto de reinicialização no aplicativo de dispositivo simulado por meio do Hub IoT.

No final deste tutorial, você tem dois aplicativos de console do node. js:

* **dmpatterns_getstarted_device. js**, que se conecta ao seu hub IOT com a identidade do dispositivo criada anteriormente, recebe um método direto de reinicialização, simula uma reinicialização física e informa a hora da última reinicialização.

* **dmpatterns_getstarted_service. js**, que chama um método direto no aplicativo de dispositivo simulado, exibe a resposta e exibe as propriedades relatadas atualizadas.

## <a name="prerequisites"></a>Pré-requisitos

* Node. js versão 10.0. x ou posterior. [Preparar seu ambiente de desenvolvimento](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) descreve como instalar o Node. js para este tutorial no Windows ou no Linux.

* Uma conta ativa do Azure. (Se você não tiver uma conta, poderá criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.)

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrar um novo dispositivo no Hub IoT

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Criar uma aplicação de dispositivo simulada

Nesta secção, pode:

* Criar uma aplicação de consola Node.js que responde a um método direto chamado pela cloud

* Disparar uma reinicialização de dispositivo simulado

* Usar as propriedades relatadas para habilitar consultas de dispositivo de dispositivos para identificar os dispositivos e quando eles foram reinicializados pela última vez

1. Crie uma pasta vazia designada **manageddevice**.  Na pasta **manageddevice**, crie um ficheiro package.json com o seguinte comando na sua linha de comandos.  Aceite todas as predefinições:

    ```cmd/sh
    npm init
    ```

2. No prompt de comando na pasta **manageddevice** , execute o seguinte comando para instalar o pacote do SDK do dispositivo **Azure-IOT-Device** e o pacote **Azure-IOT-Device-MQTT** :

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. Usando um editor de texto, crie um arquivo **dmpatterns_getstarted_device. js** na pasta **manageddevice** .

4. Adicione as seguintes instruções "require" no início do arquivo **dmpatterns_getstarted_device. js** :

    ```javascript
    'use strict';

    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. Adicione uma variável **connectionString** e utilize-a para criar uma instância do **Cliente**.  Substitua o `{yourdeviceconnectionstring}` valor do espaço reservado pela cadeia de conexão do dispositivo que você copiou anteriormente no [registro de um novo dispositivo no Hub IOT](#register-a-new-device-in-the-iot-hub).  

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

7. Abra a conexão com o Hub IoT e inicie o ouvinte de método direto:

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

8. Salve e feche o arquivo **dmpatterns_getstarted_device. js** .

> [!NOTE]
> Para facilitar, este tutorial não implementa nenhuma política de repetição. No código de produção, você deve implementar políticas de repetição (como uma retirada exponencial), conforme sugerido no artigo [tratamento de falhas transitórias](/azure/architecture/best-practices/transient-faults).

## <a name="get-the-iot-hub-connection-string"></a>Obter a cadeia de conexão do Hub IoT

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Disparar uma reinicialização remota no dispositivo usando um método direto

Nesta seção, você cria um aplicativo de console do node. js que inicia uma reinicialização remota em um dispositivo usando um método direto. O aplicativo usa consultas de dispositivo de dispositivos para descobrir a hora da última reinicialização para esse dispositivo.

1. Crie uma pasta vazia chamada **triggerrebootondevice**. Na pasta **triggerrebootondevice** , crie um arquivo Package. JSON usando o comando a seguir no prompt de comando. Aceite todas as predefinições:

    ```cmd/sh
    npm init
    ```

2. No prompt de comando na pasta **triggerrebootondevice** , execute o seguinte comando para instalar o pacote do SDK do dispositivo **Azure-iothub** e o pacote **Azure-IOT-Device-MQTT** :

    ```cmd/sh
    npm install azure-iothub --save
    ```

3. Usando um editor de texto, crie um arquivo **dmpatterns_getstarted_service. js** na pasta **triggerrebootondevice** .

4. Adicione as seguintes instruções "require" no início do arquivo **dmpatterns_getstarted_service. js** :

    ```javascript
    'use strict';

    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```

5. Adicione as seguintes declarações de variável e substitua `{iothubconnectionstring}` o valor de espaço reservado pela cadeia de conexão do Hub IOT que você copiou anteriormente em [obter a cadeia de conexão do Hub IOT](#get-the-iot-hub-connection-string):

    ```javascript
    var connectionString = '{iothubconnectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToReboot = 'myDeviceId';
    ```

6. Adicione a seguinte função para invocar o método de dispositivo para reinicializar o dispositivo de destino:

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

7. Adicione a seguinte função para consultar o dispositivo e obter a hora da última reinicialização:

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

8. Adicione o seguinte código para chamar as funções que disparam o método direto de reinicialização e a consulta para a hora da última reinicialização:

    ```javascript
    startRebootDevice();
    setInterval(queryTwinLastReboot, 2000);
    ```

9. Salve e feche o arquivo **dmpatterns_getstarted_service. js** .

## <a name="run-the-apps"></a>Executar as aplicações

Agora você está pronto para executar os aplicativos.

1. No prompt de comando na pasta **manageddevice** , execute o comando a seguir para começar a escutar o método direto de reinicialização.

    ```cmd/sh
    node dmpatterns_getstarted_device.js
    ```

2. No prompt de comando na pasta **triggerrebootondevice** , execute o comando a seguir para disparar a reinicialização e a consulta remotas para o dispositivo de pesquisa para localizar a hora da última reinicialização.

    ```cmd/sh
    node dmpatterns_getstarted_service.js
    ```

3. Você vê a resposta do dispositivo para o método direto de reinicialização e o status de reinicialização no console do.

   O seguinte mostra a resposta do dispositivo para o método direto de reinicialização enviado pelo serviço:

   ![saída do aplicativo manageddevice](./media/iot-hub-node-node-device-management-get-started/device.png)

   O seguinte mostra o serviço que dispara a reinicialização e a sondagem do dispositivo para o horário da última reinicialização:

   ![saída do aplicativo triggerrebootondevice](./media/iot-hub-node-node-device-management-get-started/service.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
