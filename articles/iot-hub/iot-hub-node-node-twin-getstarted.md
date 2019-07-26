---
title: Introdução ao dispositivo gêmeos do Hub IoT do Azure (nó) | Microsoft Docs
description: Como usar o dispositivo gêmeos do Hub IoT do Azure para adicionar marcas e, em seguida, usar uma consulta do Hub IoT. Use os SDKs do IoT do Azure para node. js para implementar o aplicativo de dispositivo simulado e um aplicativo de serviço que adiciona as marcas e executa a consulta do Hub IoT.
author: fsautomata
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 08/25/2017
ms.author: elioda
ms.openlocfilehash: 8dd5269532e9eb6139d8debb0ee9b503cd2e4354
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68404005"
---
# <a name="get-started-with-device-twins-node"></a>Introdução ao dispositivo gêmeos (nó)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

No final deste tutorial, você terá dois aplicativos de console do node. js:

* **AddTagsAndQuery. js**, um aplicativo de back-end do node. js, que adiciona marcas e consultas ao dispositivo gêmeos.

* **TwinSimulatedDevice. js**, um aplicativo node. js, que simula um dispositivo que se conecta ao Hub IOT com a identidade do dispositivo criada anteriormente e relata sua condição de conectividade.

> [!NOTE]
> O artigo [SDKs do IOT do Azure](iot-hub-devguide-sdks.md) fornece informações sobre os SDKs do IOT do Azure que você pode usar para compilar dispositivos e aplicativos de back-end.
>

Para concluir este tutorial, você precisará do seguinte:

* Node. js versão 10.0. x ou posterior.

* Uma conta ativa do Azure. (Se você não tiver uma conta, poderá criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.)

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="get-the-iot-hub-connection-string"></a>Obter a cadeia de conexão do Hub IoT

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>Criar o aplicativo de serviço

Nesta seção, você criará um aplicativo de console do node. js que adiciona metadados de local ao dispositivo de **dispositivos.** Em seguida, ele consulta o dispositivo gêmeos armazenado no Hub IoT selecionando os dispositivos localizados nos EUA e, em seguida, aqueles que estão relatando uma conexão celular.

1. Crie uma nova pasta vazia chamada **addtagsandqueryapp**. Na pasta **addtagsandqueryapp** , crie um novo arquivo Package. JSON usando o comando a seguir no prompt de comando. Aceite todas as predefinições:

    ```
    npm init
    ```

2. No prompt de comando, na pasta **addtagsandqueryapp** , execute o seguinte comando para instalar o pacote **Azure-iothub** :
   
    ```
    npm install azure-iothub --save
    ```

3. Usando um editor de texto, crie um novo arquivo **AddTagsAndQuery. js** na pasta **addtagsandqueryapp** .

4. Adicione o código a seguir ao arquivo **AddTagsAndQuery. js** e substitua o valor de espaço reservado **{do Hub IOT cadeia de conexão}** pela cadeia de conexão do Hub IOT que você copiou anteriormente em [obter a cadeia de conexão do Hub IOT](#get-the-iot-hub-connection-string):

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

    O objeto **Registry** expõe todos os métodos necessários para interagir com o dispositivo gêmeos do serviço. O código anterior Inicializa primeiro o objeto **do registro** e, em seguida, recupera o dispositivo de atualização para mydeviceid e, por fim, atualiza suas marcas com as informações de local desejadas.

    Depois de atualizar as marcas, ele chama a função **queryTwins** .

5. Adicione o seguinte código ao final de **AddTagsAndQuery. js** para implementar a função **queryTwins** :

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

    O código anterior executa duas consultas: a primeira seleciona apenas o dispositivo gêmeos de dispositivos localizados na fábrica **Redmond43** e o segundo refina a consulta para selecionar apenas os dispositivos que também estão conectados por meio da rede celular.

    O código anterior, quando ele cria o objeto de **consulta** , especifica um número máximo de documentos retornados. O objeto **Query** contém uma propriedade booliana **hasMoreResults** que você pode usar para invocar os métodos **os nextastwin** várias vezes para recuperar todos os resultados. Um método chamado **Next** está disponível para resultados que não são dispositivos gêmeos, por exemplo, resultados de consultas de agregação.

6. Execute o aplicativo com:

    ```
        node AddTagsAndQuery.js
    ```

   Você deve ver um dispositivo nos resultados para a consulta solicitando todos os dispositivos localizados em **Redmond43** e nenhum para a consulta que restringe os resultados para dispositivos que usam uma rede de celular.
   
    ![Veja um dispositivo nos resultados da consulta](media/iot-hub-node-node-twin-getstarted/service1.png)

Na próxima seção, você cria um aplicativo de dispositivo que relata as informações de conectividade e altera o resultado da consulta na seção anterior.

## <a name="create-the-device-app"></a>Criar o aplicativo do dispositivo

Nesta seção, você cria um aplicativo de console do node. js que se conecta ao seu Hub como mydeviceid e, em seguida, atualiza suas propriedades relatadas do seu dispositivo para conter as informações que ele está conectado usando uma rede de celular.

1. Crie uma nova pasta vazia chamada **reportconnectivity**. Na pasta **reportconnectivity** , crie um novo arquivo Package. JSON usando o comando a seguir no prompt de comando. Aceite todas as predefinições:
   
    ```
    npm init
    ```

2. No prompt de comando na pasta **reportconnectivity** , execute o seguinte comando para instalar o pacote **Azure-IOT-Device**e **Azure-IOT-Device-MQTT** :
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. Usando um editor de texto, crie um novo arquivo **ReportConnectivity. js** na pasta **ReportConnectivity** .

4. Adicione o código a seguir ao arquivo **ReportConnectivity. js** e substitua o espaço reservado **{Device Connection String}** pela cadeia de conexão do dispositivo que você copiou quando criou a identidade do dispositivo mydeviceid:

    ```
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

    O objeto **cliente** expõe todos os métodos necessários para interagir com dispositivos gêmeos do dispositivo. O código anterior, depois de inicializar o objeto de **cliente** , recupera o dispositivo "FileUp" para DeviceID e atualiza sua propriedade relatada com as informações de conectividade.

5. Executar o aplicativo do dispositivo

    ```   
        node ReportConnectivity.js
    ```

    Você deve ver a mensagem `twin state reported`.

6. Agora que o dispositivo relatou suas informações de conectividade, ele deve aparecer em ambas as consultas. Volte para a pasta **addtagsandqueryapp** e execute as consultas novamente:

    ```   
        node AddTagsAndQuery.js
    ```

    Dessa vez  , mydeviceid deve aparecer nos dois resultados da consulta.

    ![Mostrar DeviceID nos dois resultados da consulta](media/iot-hub-node-node-twin-getstarted/service2.png)

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, configurou um novo Hub IoT no portal do Azure e, em seguida, criou uma identidade de dispositivo no registo de identidades do Hub IoT. Você adicionou metadados de dispositivo como marcas de um aplicativo de back-end e gravou um aplicativo de dispositivo simulado para relatar informações de conectividade do dispositivo no dispositivo. Você também aprendeu como consultar essas informações usando a linguagem de consulta do Hub IoT do tipo SQL.

Use os seguintes recursos para aprender a:

* Enviar telemetria de dispositivos com o tutorial introdução [ao Hub IOT](quickstart-send-telemetry-node.md) ,

* configurar dispositivos usando as propriedades desejadas do dispositivo ' s ' com o tutorial [usar propriedades desejadas para configurar dispositivos](tutorial-device-twins.md) ,

* controlar dispositivos interativamente (como ativar um ventilador de um aplicativo controlado pelo usuário), com o tutorial [usar métodos diretos](quickstart-control-device-node.md) .