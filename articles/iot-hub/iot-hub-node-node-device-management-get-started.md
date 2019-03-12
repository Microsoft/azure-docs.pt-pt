---
title: Introdução à gestão de dispositivos do IoT Hub do Azure (Node) | Documentos da Microsoft
description: Como utilizar a gestão de dispositivos do IoT Hub para iniciar um reinício do dispositivo remoto. Utilize o SDK do Azure IoT para node. js para implementar uma aplicação de dispositivo simulado que inclui um método direto e um serviço de aplicações que invoca o método direto.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/25/2017
ms.openlocfilehash: b1a224c4170349f0797d2d57acbf45e8b7649bd8
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57531577"
---
# <a name="get-started-with-device-management-node"></a>Introdução à gestão de dispositivo (Node)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Este tutorial mostrar-lhe como:

* Utilize o portal do Azure para criar um IoT Hub e criar uma identidade de dispositivo no IoT hub.
* Crie uma aplicação de dispositivo simulado que contém um método direto que reinicia o que o dispositivo. Os métodos diretos são invocados a partir da cloud.
* Crie uma aplicação de consola node. js que chama o método direto de reinício no aplicativo do dispositivo simulado através do IoT hub.

No final deste tutorial, tem duas aplicações de consola node. js:

**dmpatterns_getstarted_device.js**, que liga ao seu hub IoT com a identidade de dispositivo que criou anteriormente, recebe um método direto de reinicialização, simula uma reinicialização física e reporta o tempo para a última reinicialização.

**dmpatterns_getstarted_service.js**, que chama um método direto na aplicação do dispositivo simulado, apresenta a resposta e apresenta a atualização de propriedades comunicadas.

Para concluir este tutorial, precisa do seguinte:

* Versão node. js 4.0.x ou posterior, <br/>  [Preparar o ambiente de desenvolvimento] [ lnk-dev-setup] descreve como instalar o node. js para este tutorial no Windows ou Linux.
* Uma conta ativa do Azure. (Se não tiver uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.)

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Obter cadeia de ligação do IoT hub

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Criar uma aplicação de dispositivo simulada
Nesta secção, irá

* Criar uma aplicação de consola Node.js que responde a um método direto chamado pela cloud
* Acionar um reinício do dispositivo simulado
* Utilize as propriedades comunicadas para ativar consultas de gémeos de dispositivo identificar os dispositivos e quando duram reiniciada

1. Crie uma pasta vazia designada **manageddevice**.  Na pasta **manageddevice**, crie um ficheiro package.json com o seguinte comando na sua linha de comandos.  Aceite todas as predefinições:
   
    ```
    npm init
    ```
2. Sua linha de comandos do **manageddevice** pasta, execute o seguinte comando para instalar o **do azure-iot-device** pacote do SDK de dispositivo e **azure-iot-device-mqtt** pacote:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. Com um editor de texto, crie uma **dmpatterns_getstarted_device.js** de ficheiros a **manageddevice** pasta.
4. Adicione as seguintes declarações no início de "necessitam" a **dmpatterns_getstarted_device.js** ficheiro:
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```
5. Adicione uma variável **connectionString** e utilize-a para criar uma instância do **Cliente**.  Substitua a cadeia de ligação com a cadeia de ligação do dispositivo.  
   
    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myDeviceId;SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```
6. Adicione a seguinte função para implementar o método direto no dispositivo
   
    ```
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
7. Abra a ligação ao seu IoT hub e iniciar o serviço de escuta do método direto:
   
    ```
    client.open(function(err) {
        if (err) {
            console.error('Could not open IotHub client');
        }  else {
            console.log('Client opened.  Waiting for reboot method.');
            client.onDeviceMethod('reboot', onReboot);
        }
    });
    ```
8. Guarde e feche o **dmpatterns_getstarted_device.js** ficheiro.

> [!NOTE]
> Para facilitar, este tutorial não implementa nenhuma política de repetição. No código de produção, deve implementar as políticas de repetição (como um término exponencial), como sugerido no artigo [processamento de erros transitórios](/azure/architecture/best-practices/transient-faults).

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Acionar a reinicialização remota no dispositivo com um método direto
Nesta secção, vai criar uma aplicação de consola node. js que inicia uma reinicialização remota num dispositivo através de um método direto. A aplicação utiliza consultas de dispositivo duplo para detetar a última hora de reinício desse dispositivo.

1. Criar uma pasta vazia designada **triggerrebootondevice**.  Na **triggerrebootondevice** pasta, crie um ficheiro Package. JSON com o seguinte comando na sua linha de comandos.  Aceite todas as predefinições:
   
    ```
    npm init
    ```
2. Sua linha de comandos do **triggerrebootondevice** pasta, execute o seguinte comando para instalar o **do azure-iothub** pacote do SDK de dispositivo e **azure-iot-device-mqtt** pacote:
   
    ```
    npm install azure-iothub --save
    ```
3. Com um editor de texto, crie uma **dmpatterns_getstarted_service.js** de ficheiros a **triggerrebootondevice** pasta.
4. Adicione as seguintes declarações no início de "necessitam" a **dmpatterns_getstarted_service.js** ficheiro:
   
    ```
    'use strict';
   
    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```
5. Adicione as seguintes declarações de variável e substitua os valores de marcador de posição:
   
    ```
    var connectionString = '{iothubconnectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToReboot = 'myDeviceId';
    ```
6. Adicione a seguinte função para invocar o método de dispositivo para reiniciar o dispositivo de destino:
   
    ```
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
7. Adicione a seguinte função para consultar o dispositivo e obter a última hora de reinício:
   
    ```
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
8. Adicione o seguinte código para chamar as funções que acionam o método direto de reinício e a consulta para a última hora de reinício:
   
    ```
    startRebootDevice();
    setInterval(queryTwinLastReboot, 2000);
    ```
9. Guarde e feche o **dmpatterns_getstarted_service.js** ficheiro.

## <a name="run-the-apps"></a>Executar as aplicações
Já está pronto para executar as aplicações.

1. A linha de comandos do **manageddevice** pasta, execute o seguinte comando para começar a escutar o método direto de reinício.
   
    ```
    node dmpatterns_getstarted_device.js
    ```
2. A linha de comandos do **triggerrebootondevice** pasta, execute o seguinte comando para acionar a reinicialização remota e a consulta para o dispositivo duplo localizar a última hora de reinício.
   
    ```
    node dmpatterns_getstarted_service.js
    ```
3. Ver a resposta de dispositivo para o método direto na consola do.

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]

<!-- images and links -->
[img-output]: media/iot-hub-get-started-with-dm/image6.png
[img-dm-ui]: media/iot-hub-get-started-with-dm/dmui.png

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md

[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[Azure portal]: https://portal.azure.com/
[Using resource groups to manage your Azure resources]: ../azure-portal/resource-group-portal.md
[lnk-dm-github]: https://github.com/Azure/azure-iot-device-management

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
