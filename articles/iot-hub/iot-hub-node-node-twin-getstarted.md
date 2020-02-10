---
title: Começar com gémeos de dispositivo Shub Azure IoT (Nó)  Microsoft Docs
description: Como utilizar gémeos de dispositivo SoT Hub Azure ioT para adicionar tags e, em seguida, usar uma consulta IoT Hub. Utiliza os SDKs Azure IoT para o Node.js para implementar a aplicação simulada do dispositivo e uma aplicação de serviço que adiciona as tags e executa a consulta do IoT Hub.
author: fsautomata
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: elioda
ms.openlocfilehash: 55dc7f73a3e5bbff2e6e331ba0bd7d4088a86536
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/09/2020
ms.locfileid: "77110840"
---
# <a name="get-started-with-device-twins-nodejs"></a>Começar com gémeos dispositivo (Node.js)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

No final deste tutorial, terás duas aplicações de consola Node.js:

* **AddTagsAndQuery.js**, uma aplicação back-end Node.js, que adiciona tags e consultas de dispositivo gémeos.

* **TwinSimulatedDevice.js**, uma aplicação Node.js, que simula um dispositivo que se conecta ao seu hub IoT com a identidade do dispositivo criada anteriormente, e relata a sua condição de conectividade.

> [!NOTE]
> O artigo [Azure IoT SDKs](iot-hub-devguide-sdks.md) fornece informações sobre os SDKs Azure IoT que pode usar para construir aplicações de dispositivos e back-end.
>

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

* Node.js versão 10.0.x ou mais tarde.

* Uma conta ativa do Azure. (Se não tiver uma conta, pode criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.)

* Certifique-se de que a porta 8883 está aberta na sua firewall. A amostra do dispositivo neste artigo utiliza o protocolo MQTT, que comunica sobre a porta 8883. Este porto pode estar bloqueado em alguns ambientes de rede corporativa e educativa. Para obter mais informações e formas de resolver este problema, consulte [A Ligação ao IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registe um novo dispositivo no hub IoT

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="get-the-iot-hub-connection-string"></a>Obter a cadeia de conexão do Hub IoT

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>Criar a app de serviço

Nesta secção, cria-se uma aplicação de consola Node.js que adiciona metadados de localização ao dispositivo twin associado ao **myDeviceId**. Em seguida, questiona os gémeos do dispositivo armazenados no hub IoT selecionando os dispositivos localizados nos EUA, e depois os que estão reportando uma ligação celular.

1. Crie uma nova pasta vazia chamada **addtagsandqueryapp**. Na pasta **addtagsandqueryapp,** crie um novo ficheiro package.json utilizando o seguinte comando no seu pedido de comando. O parâmetro `--yes` aceita todos os incumprimentos.

    ```cmd/sh
    npm init --yes
    ```

2. Ao seu pedido de comando na pasta **addtagsandqueryapp,** execute o seguinte comando para instalar o pacote **azure-iothub:**

    ```cmd/sh
    npm install azure-iothub --save
    ```

3. Utilizando um editor de texto, crie um novo ficheiro **AddTagsAndQuery.js** na pasta **addtagsandqueryapp.**

4. Adicione o seguinte código ao ficheiro **AddTagsAndQuery.js.** Substitua `{iot hub connection string}` com a cadeia de ligação IoT Hub que copiou na cadeia de ligação do [hub IoT](#get-the-iot-hub-connection-string).

   ``` javascript
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
   ```

    O objeto **de registo** expõe todos os métodos necessários para interagir com gémeos dispositivos do serviço. O código anterior inicialmente inicia o objeto **de Registo,** depois recupera o dispositivo twin para **myDeviceId**, e finalmente atualiza as suas etiquetas com as informações de localização desejadas.

    Depois de atualizar as etiquetas, chama a função **consultasTwins.**

5. Adicione o seguinte código no final de **AddTagsAndQuery.js** para implementar a função **consultasTwins:**

   ```javascript
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
   ```

    O código anterior executa duas consultas: a primeira seleciona apenas os gémeos dispositivos localizados na fábrica **redmond43,** e o segundo refina a consulta para selecionar apenas os dispositivos que também estão ligados através da rede celular.

    Quando o código cria o objeto **de consulta,** especifica o número máximo de documentos devolvidos no segundo parâmetro. O objeto **de consulta** contém uma propriedade boolean **hasMoreResults** que você pode usar para invocar os **métodos AsTwin seguintes** várias vezes para recuperar todos os resultados. Um método chamado **next** está disponível para resultados que não são gémeos dispositivo, por exemplo, os resultados de consultas de agregação.

6. Executar a aplicação com:

    ```cmd/sh
        node AddTagsAndQuery.js
    ```

   Deve ver um dispositivo nos resultados da consulta que pede todos os dispositivos localizados em **Redmond43** e nenhum para a consulta que restringe os resultados a dispositivos que utilizam uma rede celular.

   ![Veja o único dispositivo nos resultados da consulta](media/iot-hub-node-node-twin-getstarted/service1.png)

Na secção seguinte, cria-se uma aplicação de dispositivo que reporta as informações de conectividade e altera o resultado da consulta na secção anterior.

## <a name="create-the-device-app"></a>Criar a aplicação do dispositivo

Nesta secção, cria-se uma aplicação de consola Node.js que se conecta ao seu hub como **myDeviceId**, e depois atualiza as propriedades reportadas do seu dispositivo twin para conter a informação de que está conectado através de uma rede celular.

1. Crie uma nova pasta vazia chamada **reportconectividade**. Na pasta de **conectividade reporte,** crie um novo ficheiro package.json utilizando o seguinte comando no seu pedido de comando. O parâmetro `--yes` aceita todos os incumprimentos.

    ```cmd/sh
    npm init --yes
    ```

2. Ao seu pedido de comando na pasta de conectividade de **reporte,** execute o seguinte comando para instalar o **dispositivo azure-iot**e pacotes **azure-iot-device-mqtt:**

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. Utilizando um editor de texto, crie um novo ficheiro **ReportConnectivity.js** na pasta de conectividade de **relatórios.**

4. Adicione o seguinte código ao ficheiro **ReportConnectivity.js.** Substitua `{device connection string}` com a cadeia de ligação do dispositivo que copiou quando criou a identidade do **dispositivo myDeviceId** no [Registo de um novo dispositivo no hub IoT](#register-a-new-device-in-the-iot-hub).

    ```javascript
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
    ```

    O objeto **Cliente** expõe todos os métodos necessários para interagir com gémeos dispositivos do dispositivo. O código anterior, depois de inicializar o objeto **Cliente,** recupera o dispositivo twin para **myDeviceId** e atualiza a sua propriedade reportada com a informação de conectividade.

5. Executar a aplicação do dispositivo

    ```cmd/sh
        node ReportConnectivity.js
    ```

    Devia ver a mensagem `twin state reported`.

6. Agora que o dispositivo reportou a sua informação de conectividade, deve aparecer em ambas as consultas. Volte para a pasta **addtagsandqueryapp** e volte a executar as consultas:

    ```cmd/sh
        node AddTagsAndQuery.js
    ```

    Desta vez, o **myDeviceId** deve aparecer em ambos os resultados da consulta.

    ![Mostre o myDeviceId em ambos os resultados da consulta](media/iot-hub-node-node-twin-getstarted/service2.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, configurou um novo Hub IoT no portal do Azure e, em seguida, criou uma identidade de dispositivo no registo de identidades do Hub IoT. Adicionou metadados de dispositivos como tags de uma aplicação de back-end e escreveu uma aplicação simulada de dispositivo para relatar informações de conectividade do dispositivo no dispositivo twin. Também aprendeu a consultar esta informação usando a linguagem de consulta IoT Hub semelhante a SQL.

Utilize os seguintes recursos para aprender a:

* enviar telemetria a partir de dispositivos com o [Get iniciado com ioT Hub](quickstart-send-telemetry-node.md) tutorial,

* configurar dispositivos utilizando as propriedades desejadas pelo dispositivo twin com as [propriedades desejadas para configurar o](tutorial-device-twins.md) tutorial de dispositivos,

* dispositivos de controlo interactivamente (como ligar um ventilador a partir de uma aplicação controlada pelo utilizador), com o tutorial [de métodos diretos use.](quickstart-control-device-node.md)
