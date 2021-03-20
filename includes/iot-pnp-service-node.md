---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 7ab17f0d34ba7682778120e11aab562e106b5df7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "95510610"
---
O IoT Plug and Play simplifica o IoT, permitindo-lhe interagir com as capacidades de um dispositivo sem ter conhecimento da implementação do dispositivo subjacente. Este quickstart mostra-lhe como usar Node.js para ligar e controlar um dispositivo IoT Plug and Play que está ligado à sua solução.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Para completar este arranque rápido, precisa de Node.js na sua máquina de desenvolvimento. Pode descarregar a versão mais recente recomendada para várias plataformas a partir de [nodejs.org](https://nodejs.org).

Pode verificar qual a versão atual do Node.js no seu computador de desenvolvimento através do seguinte comando:

```cmd/sh
node --version
```

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>Clone o repositório SDK com o código de amostra

Clone as amostras de um [repositório de Node SDK.](https://github.com/Azure/azure-iot-sdk-node) Abra uma janela de terminal numa pasta à sua escolha. Executar o seguinte comando para clonar o [Microsoft Azure IoT SDK para Node.js](https://github.com/Azure/azure-iot-sdk-node) repositório GitHub:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node
```

## <a name="run-the-sample-device"></a>Executar o dispositivo de amostra

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

Para saber mais sobre a configuração da amostra, consulte o [produto de leitura](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/pnp/readme.md)da amostra .

Neste arranque rápido, utilize um dispositivo termóstato de amostra que está escrito em Node.js como o dispositivo IoT Plug and Play. Para executar o dispositivo de amostragem:

1. Abra uma janela de terminal e navegue para a pasta local que contém o Microsoft Azure IoT SDK para Node.js repositório que clonou do GitHub.

1. Esta janela de terminal é utilizada como terminal do seu **dispositivo.** Vá à pasta do seu repositório clonado e navegue para a pasta */azure-iot-sdk-node/dispositivo/samples/pnp.* Instale todas as dependências executando o seguinte comando:

    ```cmd/sh
    npm install
    ```

1. Executar o dispositivo termóstato da amostra com o seguinte comando:

    ```cmd/sh
    node simple_thermostat.js
    ```

1. Vê mensagens a dizer que o dispositivo enviou algumas informações e reportou-se online. Estas mensagens indicam que o dispositivo começou a enviar dados de telemetria para o hub, estando agora pronto para receber comandos e atualizações de propriedade. Não feche este terminal, precisa dele para confirmar que a amostra de serviço está a funcionar.

## <a name="run-the-sample-solution"></a>Executar a solução de amostra

Em [Configurar o seu ambiente para os quickstarts e tutoriais IoT Plug and Play](../articles/iot-pnp/set-up-environment.md) criou duas variáveis ambientais para configurar a amostra para ligar ao seu hub e dispositivo IoT:

* **IOTHUB_CONNECTION_STRING**: a cadeia de ligação do hub IoT que fez uma nota anterior.
* **IOTHUB_DEVICE_ID:** `"my-pnp-device"` .

Neste arranque rápido, utilize uma solução IoT de amostra em Node.js para interagir com o dispositivo de amostra que acabou de configurar.

1. Abra outra janela do terminal para utilizar como terminal **de serviço.**

1. No repositório SDK clonado, navegue para a pasta */azure-iot-sdk-node/service/samples/javascript.* Instale todas as dependências executando o seguinte comando:

    ```cmd/sh
    npm install
    ```

### <a name="read-a-property"></a>Ler uma propriedade

1. Quando analisou o dispositivo do termóstato da amostra no terminal do **dispositivo,** viu as seguintes mensagens indicando o seu estado online:

    ```cmd/sh
    properties have been reported for component
    sending telemetry message 0...
    ```

1. Vá ao terminal **de serviço** e use o seguinte comando para executar a amostra para obter informações do dispositivo de leitura:

    ```cmd/sh
    node twin.js
    ```

1. Na saída do terminal de **serviço,** note a resposta do dispositivo twin. Vê o ID do modelo do dispositivo e as propriedades associadas relatadas:

    ```json
    Model Id: dtmi:com:example:Thermostat;1
    {
      "deviceId": "my-pnp-device",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag": "Njc3MDMxNDcy",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00Z",
      "connectionState": "Connected",
      "lastActivityTime": "0001-01-01T00:00:00Z",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "modelId": "dtmi:com:example:Thermostat;1",
      "version": 4,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "2020-10-05T11:35:19.4574755Z"
          },
          "$version": 1
        },
        "reported": {
          "maxTempSinceLastReboot": 31.343640523762232,
          "serialNumber": "123abc",
          "$metadata": {
            "$lastUpdated": "2020-10-05T11:35:23.7339042Z",
            "maxTempSinceLastReboot": {
              "$lastUpdated": "2020-10-05T11:35:23.7339042Z"
            },
            "serialNumber": {
              "$lastUpdated": "2020-10-05T11:35:23.7339042Z"
            }
          },
          "$version": 3
        }
      },
      "capabilities": {
        "iotEdge": false
      },
      "tags": {}
    }
    ```

1. O seguinte corte mostra o código em *twin.js* que recupera o ID do modelo do dispositivo:

    ```javascript
    var registry = Registry.fromConnectionString(connectionString);
    registry.getTwin(deviceId, function(err, twin) {
      if (err) {
        console.error(err.message);
      } else {
        console.log('Model Id: ' + twin.modelId);
        //...
      }
      //...
    }
    ```

Neste cenário, `Model Id: dtmi:com:example:Thermostat;1` produz.

> [!NOTE]
> Estas amostras de serviço utilizam a classe **registro** do cliente de **serviço IoT Hub.** Para saber mais sobre as APIs, incluindo as gémeas digitais API, consulte o [guia de desenvolvedores de serviços.](../articles/iot-pnp/concepts-developer-guide-service.md)

### <a name="update-a-writable-property"></a>Atualizar uma propriedade writable

1. Abra o ficheiro *twin.js* num editor de código.

1. Reveja o código de amostra, mostra-lhe duas formas de atualizar o dispositivo twin. Para utilizar a primeira forma, modifique a `twinPatch` variável da seguinte forma:

    ```javascript
    var twinPatch = {
      tags: {
        city: "Redmond"
      },
      properties: {
        desired: {
          targetTemperature: 42
        }
      }
    };
    ```

    A `targetTemperature` propriedade é definida como uma propriedade writable no modelo do dispositivo termóstato.

1. No terminal **de serviço,** utilize o seguinte comando para executar a amostra para atualizar o imóvel:

    ```cmd/sh
    node twin.js
    ```

1. No terminal do seu **dispositivo,** vê que o dispositivo recebeu a atualização:

    ```cmd/sh
    The following properties will be updated for the default component:
    {
      targetTemperature: {
        value: 42,
        ac: 200,
        ad: 'Successfully executed patch for targetTemperature',
        av: 2
      }
    }
    updated the property
    ```

1. No seu terminal **de serviço,** executar o seguinte comando para confirmar que a propriedade está atualizada:

    ```cmd/sh
    node twin.js
    ```

1. Na saída do terminal **de serviço,** na `reported` secção de propriedades, vê-se a temperatura-alvo atualizada reportada. Pode demorar algum tempo até que o dispositivo termine a atualização. Repita este passo até que o dispositivo tenha processado a atualização da propriedade:

    ```json
    "reported": {
      //...
      "targetTemperature": {
        "value": 42,
        "ac": 200,
        "ad": "Successfully executed patch for targetTemperature",
        "av": 4
      },
      //...
    }
    ```

### <a name="invoke-a-command"></a>Invocar um comando

1. Abra o ficheiro *device_method.js* e reveja o código.

1. Vai para o terminal **de serviço.** Utilize o seguinte comando para executar a amostra para invocar o comando:

    ```cmd/sh
    set IOTHUB_METHOD_NAME=getMaxMinReport
    set IOTHUB_METHOD_PAYLOAD=commandpayload
    node device_method.js
    ```

1. A saída no terminal **de serviço** mostra a seguinte confirmação:

    ```cmd/sh
    getMaxMinReport on my-pnp-device:
    {
      "status": 200,
      "payload": {
        "maxTemp": 23.460596940801928,
        "minTemp": 23.460596940801928,
        "avgTemp": 23.460596940801928,
        "endTime": "2020-10-05T12:48:08.562Z",
        "startTime": "2020-10-05T12:47:54.450Z"
      }
    }
    ```

1. No terminal do **dispositivo,** vê-se que o comando é reconhecido:

    ```cmd/sh
    MaxMinReport commandpayload
    Response to method 'getMaxMinReport' sent successfully.
    ```
