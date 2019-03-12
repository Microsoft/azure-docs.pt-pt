---
title: Introdução aos duplos de dispositivo do IoT Hub do Azure (Node) | Documentos da Microsoft
description: Como utilizar o gémeos de dispositivo do IoT Hub do Azure para adicionar etiquetas e, em seguida, utilizar uma consulta do IoT Hub. Utilize os SDKs IoT do Azure para node. js para implementar a aplicação de dispositivo simulado e uma aplicação de serviço que adiciona as etiquetas e executa a consulta do IoT Hub.
author: fsautomata
manager: ''
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 08/25/2017
ms.author: elioda
ms.openlocfilehash: 397f4f48437b48934171cc0b90ad04715f2ee21b
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57541269"
---
# <a name="get-started-with-device-twins-node"></a>Começar a utilizar dispositivos duplos (Node)
[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

No final deste tutorial, terá duas aplicações de consola node. js:

* **AddTagsAndQuery.js**, uma aplicação de back-end de node. js, que adiciona as etiquetas e consultas de gémeos de dispositivo.
* **TwinSimulatedDevice.js**, uma aplicação de node. js, que simula um dispositivo que liga ao seu hub IoT com a identidade de dispositivo que criou anteriormente e reporta sua condição de conectividade.

> [!NOTE]
> O artigo [SDKs do Azure IoT] [ lnk-hub-sdks] fornece informações sobre os SDKs IoT do Azure que pode utilizar para criar aplicações de dispositivos e back-end.
> 
> 

Para concluir este tutorial, precisa do seguinte:

* Versão 4.0.x ou posterior do Node.js.
* Uma conta ativa do Azure. (Se não tiver uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.)

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Obter cadeia de ligação do IoT hub

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-the-service-app"></a>Criar a aplicação de serviço
Nesta secção, vai criar uma aplicação de consola node. js que adiciona os metadados de localização para o dispositivo duplo associado **myDeviceId**. Ele consulta, em seguida, os dispositivos duplos armazenados no hub IoT selecionar os dispositivos localizados nos Estados Unidos e, em seguida, aqueles que estiverem a comunicar uma conexão por celular.

1. Criar uma nova pasta vazia designada **addtagsandqueryapp**. Na **addtagsandqueryapp** pasta, crie um novo ficheiro Package. JSON com o seguinte comando na sua linha de comandos. Aceite todas as predefinições:
   
    ```
    npm init
    ```
2. Sua linha de comandos do **addtagsandqueryapp** pasta, execute o seguinte comando para instalar o **do azure-iothub** pacote:
   
    ```
    npm install azure-iothub --save
    ```
3. Com um editor de texto, crie um novo **AddTagsAndQuery.js** de ficheiros a **addtagsandqueryapp** pasta.
4. Adicione o seguinte código para o **AddTagsAndQuery.js** do ficheiro e substitua o **{cadeia de ligação do hub iot}** marcador de posição pela cadeia de ligação do IoT Hub que copiou quando criou o seu hub:
   
        'use strict';
        var iothub = require('azure-iothub');
        var connectionString = '{iot hub connection string}';
        var registry = iothub.Registry.fromConnectionString(connectionString);
   
        registry.getTwin('myDeviceId', function(err, twin){
            if (err) {
                console.error(err.constructor.name + ': ' + err.message);
            } else {
                var patch = {
                    tags: {
                        location: {
                            region: 'US',
                            plant: 'Redmond43'
                      }
                    }
                };
   
                twin.update(patch, function(err) {
                  if (err) {
                    console.error('Could not update twin: ' + err.constructor.name + ': ' + err.message);
                  } else {
                    console.log(twin.deviceId + ' twin updated successfully');
                    queryTwins();
                  }
                });
            }
        });
   
    O **Registro** objeto expõe todos os métodos necessários para interagir com dispositivos duplos do serviço. O código anterior primeiro inicializa a **Registro** de objeto, em seguida, obtém o dispositivo duplo para **myDeviceId**e finalmente atualiza as marcas com as informações de localização pretendida.
   
    Depois de atualizar as etiquetas chama o **queryTwins** função.
5. Adicione o seguinte código no final da **AddTagsAndQuery.js** para implementar a **queryTwins** função:
   
        var queryTwins = function() {
            var query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });
   
            query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43 using cellular network: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });
        };
   
    O código anterior executa duas consultas: o primeiro seleciona apenas os dispositivos duplos de dispositivos localizados no **Redmond43** plant e o segundo Refina a consulta para selecionar apenas os dispositivos que também estão ligados através de rede celular.
   
    O código anterior, quando cria o **consulta** de objeto, especifica um número máximo de documentos devolvidos. O **consulta** objeto contém um **hasMoreResults** propriedade booleana que pode usar para invocar o **nextAsTwin** métodos múltiplas vezes para obter todos os resultados. Um método chamado **seguinte** está disponível para os resultados que são não dispositivos duplos, por exemplo, os resultados de consultas de agregação.
6. Execute a aplicação com:
   
        node AddTagsAndQuery.js
   
    Deverá ver um dispositivo nos resultados para fazer a consulta para todos os dispositivos localizados num **Redmond43** e nenhuma durante a consulta que restringe os resultados para os dispositivos que utilizam uma rede celular.
   
    ![][1]

A secção seguinte, vai criar uma aplicação de dispositivo que reporta as informações de conectividade e altera o resultado da consulta na secção anterior.

## <a name="create-the-device-app"></a>Criar a aplicação de dispositivo
Nesta secção, vai criar uma aplicação de consola node. js que liga ao seu hub como **myDeviceId**e, em seguida, atualizações de seu dispositivo duplo do comunicado propriedades para conter as informações que está ligado através de uma rede celular.


1. Criar uma nova pasta vazia designada **reportconnectivity**. Na **reportconnectivity** pasta, crie um novo ficheiro Package. JSON com o seguinte comando na sua linha de comandos. Aceite todas as predefinições:
   
    ```
    npm init
    ```
2. Sua linha de comandos do **reportconnectivity** pasta, execute o seguinte comando para instalar o **do azure-iot-device**, e **azure-iot-device-mqtt** pacote:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. Com um editor de texto, crie um novo **ReportConnectivity.js** de ficheiros a **reportconnectivity** pasta.
4. Adicione o seguinte código para o **ReportConnectivity.js** do ficheiro e substitua o **{cadeia de ligação do dispositivo}** marcador de posição pela cadeia de ligação de dispositivo que copiou quando criou o **myDeviceId** identidade de dispositivo:
   
        'use strict';
        var Client = require('azure-iot-device').Client;
        var Protocol = require('azure-iot-device-mqtt').Mqtt;
   
        var connectionString = '{device connection string}';
        var client = Client.fromConnectionString(connectionString, Protocol);
   
        client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');
   
            client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                var patch = {
                    connectivity: {
                        type: 'cellular'
                    }
                };
   
                twin.properties.reported.update(patch, function(err) {
                    if (err) {
                        console.error('could not update twin');
                    } else {
                        console.log('twin state reported');
                        process.exit();
                    }
                });
            }
            });
        }
        });
   
    O **cliente** objeto expõe todos os métodos necessários para interagir com dispositivos duplos do dispositivo. O código anterior, depois, ele inicializa o **cliente** objeto, obtém o dispositivo duplo **myDeviceId** e atualiza a respetiva propriedade comunicada com as informações de conectividade.
5. Executar a aplicação de dispositivo
   
        node ReportConnectivity.js
   
    Deverá ver a mensagem `twin state reported`.
6. Agora que o dispositivo comunicado suas informações de conectividade, deverá aparecer em ambas as consultas. Aceda de volta a **addtagsandqueryapp** pasta e execute novamente as consultas:
   
        node AddTagsAndQuery.js
   
    Neste momento **myDeviceId** deve aparecer em ambos os resultados da consulta.
   
    ![][3]

## <a name="next-steps"></a>Passos Seguintes
Neste tutorial, configurou um novo Hub IoT no portal do Azure e, em seguida, criou uma identidade de dispositivo no registo de identidades do Hub IoT. Adicionados metadados do dispositivo como etiquetas a partir de uma aplicação de back-end e escreveu uma aplicação de dispositivo simulado para informações de conectividade do dispositivo de relatório no dispositivo duplo. Também aprendeu como consultar essas informações usando a linguagem de consulta do Hub de IoT de tipo SQL.

Utilize os seguintes recursos para saber como:

* Enviar telemetria a partir de dispositivos com o [introdução ao IoT Hub] [ lnk-iothub-getstarted] tutorial,
* configurar dispositivos com as propriedades pretendidas do duplo do dispositivo com o [propriedades para configurar os dispositivos de pretendidas de utilização] [ lnk-twin-how-to-configure] tutorial,
* Controlar dispositivos interativamente (como ativar um fã de uma aplicação controlada pelo utilizador), com o [utilizar métodos diretos] [ lnk-methods-tutorial] tutorial.

<!-- images -->
[1]: media/iot-hub-node-node-twin-getstarted/service1.png
[3]: media/iot-hub-node-node-twin-getstarted/service2.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-identity]: iot-hub-devguide-identity-registry.md

[lnk-iothub-getstarted]: quickstart-send-telemetry-node.md
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: ../iot-edge/quickstart-linux.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/

[lnk-twin-how-to-configure]: tutorial-device-twins.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md

[lnk-methods-tutorial]: quickstart-control-device-node.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
