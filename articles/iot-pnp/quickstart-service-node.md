---
title: Interaja com um dispositivo IoT Plug and Play Preview ligado à sua solução Azure IoT (Node.js) Microsoft Docs
description: Utilize Node.js para ligar e interagir com um dispositivo IoT Plug e Play Preview que esteja ligado à sua solução Azure IoT.
author: elhorton
ms.author: elhorton
ms.date: 07/13/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc, devx-track-javascript
ms.openlocfilehash: 511a61fb1069ce10e94e24ecd3ba6d60470ca40f
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87424448"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-nodejs"></a>Quickstart: Interaja com um dispositivo IoT Plug e Play Preview que esteja ligado à sua solução (Node.js)

[!INCLUDE [iot-pnp-quickstarts-service-selector.md](../../includes/iot-pnp-quickstarts-service-selector.md)]

IoT Plug e Play Preview simplifica o IoT, permitindo-lhe interagir com as capacidades de um dispositivo sem conhecimento da implementação do dispositivo subjacente. Este quickstart mostra-lhe como usar Node.js para ligar e controlar um dispositivo IoT Plug and Play que está ligado à sua solução.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para completar este arranque rápido, precisa de Node.js na sua máquina de desenvolvimento. Pode descarregar a versão mais recente recomendada para várias plataformas a partir de [nodejs.org](https://nodejs.org).

Pode verificar qual a versão atual do Node.js no seu computador de desenvolvimento através do seguinte comando:

```cmd/sh
node --version
```

Instale o [serviço de nó SDK com suporte IoT Plug e Play](https://www.npmjs.com/package/azure-iot-digitaltwins-service) executando o seguinte comando:

```cmd/sh
npm i azure-iot-digitaltwins-service
```

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Executar o seguinte comando para obter a _cadeia de ligação do hub IoT_ para o seu hub. Tome nota desta cadeia de ligação, use-a mais tarde neste arranque rápido:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

Executar o seguinte comando para obter a _cadeia de ligação_ do dispositivo para o dispositivo que adicionou ao hub. Tome nota desta cadeia de ligação, use-a mais tarde neste arranque rápido:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output
```

## <a name="run-the-sample-device"></a>Executar o dispositivo de amostra

Neste arranque rápido, pode utilizar um dispositivo termóstato de amostra que está escrito em Node.js como o dispositivo IoT Plug and Play. Para executar o dispositivo de amostragem:

1. Abra uma janela de terminal numa pasta à sua escolha. Executar o seguinte comando para clonar o [Microsoft Azure IoT SDK para Node.js](https://github.com/Azure/azure-iot-sdk-node) repositório GitHub neste local:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-node
    ```

1. Esta janela de terminal é utilizada como terminal do seu **dispositivo.** Vá à pasta do seu repositório clonado e navegue para a pasta */azure-iot-sdk-node/dispositivo/samples/pnp.* Instale todas as dependências executando o seguinte comando:

    ```cmd/sh
    npm install
    ```

1. Configure a _cadeia de ligação_do dispositivo:

    ```cmd/sh
    set IOTHUB_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Executar o dispositivo termóstato da amostra com o seguinte comando:

    ```cmd/sh
    node simple_thermostat.js
    ```

1. Vê mensagens a dizer que o dispositivo enviou algumas informações e reportou-se online. Estas mensagens indicam que o dispositivo começou a enviar dados de telemetria para o hub, estando agora pronto para receber comandos e atualizações de propriedade. Não feche este terminal, precisa dele para confirmar que a amostra de serviço está a funcionar.

## <a name="run-the-sample-solution"></a>Executar a solução de amostra

Neste arranque rápido, utilize uma solução IoT de amostra em Node.js para interagir com o dispositivo de amostra que acabou de configurar.

1. Abra outra janela do terminal para utilizar como terminal **de serviço.** O serviço SDK está em pré-visualização, por isso precisa clonar as amostras de um [ramo de pré-visualização do Nó SDK](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh):

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-node -b public-preview-pnp
    ```

1. Vá à pasta deste ramo de repositório clonado e navegue para a pasta */azure-iot-samples-node/digital-twins/samples/service/javascript.* Instale todas as dependências executando o seguinte comando:

    ```cmd/sh
    npm install
    ```

1. Configure variáveis ambientais para o seu dispositivo ID e _ioT Hub cadeia de conexão_:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIOTHubConnectionString>
    set IOTHUB_DEVICE_ID=<Your device ID>
    ```

### <a name="read-a-property"></a>Ler uma propriedade

1. Quando analisou o dispositivo do termóstato da amostra no terminal do **dispositivo,** viu as seguintes mensagens indicando o seu estado online:

    ```cmd/sh
    properties have been reported for component
    sending telemetry message 0...
    ```

1. Vá ao terminal **de serviço** e use o seguinte comando para executar a amostra para obter informações do dispositivo de leitura:

    ```cmd/sh
    node get_digital_twin.js
    ```

1. Na saída do terminal de **serviço,** note a resposta do gémeo digital. Vê o ID do modelo do dispositivo e as propriedades associadas relatadas:

    ```json
    "$dtId": "mySimpleThermostat",
    "serialNumber": "123abc",
    "maxTempSinceLastReboot": 51.96167432818655,
    "$metadata": {
      "$model": "dtmi:com:example:Thermostat;1",
      "serialNumber": { "lastUpdateTime": "2020-07-09T14:04:00.6845182Z" },
      "maxTempSinceLastReboot": { "lastUpdateTime": "2020-07-09T14:04:00.6845182" }
    }
    ```

1. O seguinte corte mostra o código em *get_digital_twin.js* que recupera o ID do modelo do dispositivo:

    ```javascript
    console.log("Model Id: " + inspect(digitalTwin.$metadata.$model))
    ```

Neste cenário, `Model Id: dtmi:com:example:Thermostat;1` produz.

### <a name="update-a-writable-property"></a>Atualizar uma propriedade writable

1. Abra o ficheiro *update_digital_twin_property.js* num editor de código.

1. Reveja o código de amostra. Pode ver como criar um patch JSON para atualizar o twin digital do seu dispositivo. Nesta amostra, o código substitui a temperatura do termóstato pelo valor 42:

    ```javascript
    const patch = [{
        op: 'add',
        path: 'targetTemperature',
        value: '42'
      }]
    ```

1. No terminal **de serviço,** utilize o seguinte comando para executar a amostra para atualizar o imóvel:

    ```cmd/sh
    node update_digital_twin_property.js
    ```

1. A saída do terminal de **serviço** mostra as informações atualizadas do dispositivo. Percorra o `thermostat1` componente para ver o novo valor de `targetTemperature` 42:

    ```json
    "modelId": "dtmi:com:example:Thermostat;1",
        "version": 12,
        "properties": {
            "desired": {
                "targetTemperature": "42",
                "$metadata": {
                    "$lastUpdated": "2020-07-09T13:55:50.7976985Z",
                    "$lastUpdatedVersion": 5,
                    "targetTemperature": {
                        "$lastUpdated": "2020-07-09T13:55:50.7976985Z",
                        "$lastUpdatedVersion": 5
                    }
                },
                "$version": 5
            },
            "reported": {
                "serialNumber": "123abc",
                "maxTempSinceLastReboot": 32.279942997143785,
                "targetTemperature": {
                    "value": "42",
                    "ac": 200,
                    "ad": "Successfully executed patch for targetTemperature",
                    "av": 2
                },
    ```

1. No terminal do seu **dispositivo,** vê que o dispositivo recebeu a atualização:

    ```cmd/sh
    Received an update for targetTemperature: 42
    updated the property
    ```

1. No seu terminal **de serviço,** executar o seguinte comando para confirmar que a propriedade está atualizada:

    ```cmd/sh
    node get_digital_twin.js
    ```

1. Na saída do terminal de **serviço,** na resposta digital gémea sob o `thermostat1` componente, vê-se a temperatura-alvo atualizada reportada. Pode demorar algum tempo até que o dispositivo termine a atualização. Repita este passo até que o dispositivo tenha processado a atualização da propriedade:

    ```json
    "$model": "dtmi:com:example:Thermostat;1",
    "targetTemperature": {
      "desiredValue": 42,
      "desiredVersion": 4,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "Successfully executed patch for targetTemperature",
      "lastUpdateTime": "2020-07-09T13:55:30.5062641Z"
    }
    ```

### <a name="invoke-a-command"></a>Invocar um comando

1. Abra o ficheiro *invoke_command.js* e reveja o código.

1. Vai para o terminal **de serviço.** Utilize o seguinte comando para executar a amostra para invocar o comando:

    ```cmd/sh
    node invoke_command.js
    ```

1. A saída no terminal **de serviço** mostra a seguinte confirmação:

    ```cmd/sh
    {
        xMsCommandStatuscode: 200,  
        xMsRequestId: 'ee9dd3d7-4405-4983-8cee-48b4801fdce2',  
        connection: 'close',  'content-length': '18',  
        'content-type': 'application/json; charset=utf-8',  
        date: 'Thu, 09 Jul 2020 15:05:14 GMT',  
        server: 'Microsoft-HTTPAPI/2.0',  vary: 'Origin',  
        body: 'min/max response'
    }
    ```

1. No terminal do **dispositivo,** vê-se que o comando é reconhecido:

    ```cmd/sh
    MaxMinReport [object Object]
    Response to method 'getMaxMinReport' sent successfully.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Próximos passos

Neste arranque rápido, aprendeu a ligar um dispositivo IoT Plug e Play a uma solução IoT. Para saber mais sobre os modelos ioT Plug e Play, consulte:

> [!div class="nextstepaction"]
> [Guia de modelação IoT Plug e Play Preview](concepts-developer-guide.md)
