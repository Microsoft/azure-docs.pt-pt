---
title: Interaja com um dispositivo IoT Plug and Play ligado à sua solução Azure IoT (Node.js) Microsoft Docs
description: Utilize Node.js para ligar e interagir com um dispositivo IoT Plug and Play que esteja ligado à sua solução Azure IoT.
author: elhorton
ms.author: elhorton
ms.date: 08/11/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc, devx-track-js
ms.openlocfilehash: 6ad6e48642e7b7df4b93b37b5ef66381833d8bbc
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/30/2020
ms.locfileid: "91574998"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-device-thats-connected-to-your-solution-nodejs"></a>Quickstart: Interaja com um dispositivo IoT Plug and Play que está ligado à sua solução (Node.js)

[!INCLUDE [iot-pnp-quickstarts-service-selector.md](../../includes/iot-pnp-quickstarts-service-selector.md)]

O IoT Plug and Play simplifica o IoT, permitindo-lhe interagir com as capacidades de um dispositivo sem ter conhecimento da implementação do dispositivo subjacente. Este quickstart mostra-lhe como usar Node.js para ligar e controlar um dispositivo IoT Plug and Play que está ligado à sua solução.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

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

[!INCLUDE [iot-pnp-environment](../../includes/iot-pnp-environment.md)]

Para saber mais sobre a configuração da amostra, consulte o [produto de leitura](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/pnp/readme.md)da amostra .

Neste arranque rápido, pode utilizar um dispositivo termóstato de amostra que está escrito em Node.js como o dispositivo IoT Plug and Play. Para executar o dispositivo de amostragem:

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

Em [Configurar o seu ambiente para os quickstarts e tutoriais IoT Plug and Play](set-up-environment.md) criou duas variáveis ambientais para configurar a amostra para ligar ao seu hub e dispositivo IoT:

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

1. Abra o ficheiro *update_digital_twin.js* num editor de código.

1. Reveja o código de amostra. Pode ver como criar um patch JSON para atualizar o twin digital do seu dispositivo. Nesta amostra, o código substitui a temperatura do termóstato pelo valor 42:

    ```javascript
    const patch = [{
        op: 'add',
        path: '/targetTemperature',
        value: '42'
      }]
    ```

1. No terminal **de serviço,** utilize o seguinte comando para executar a amostra para atualizar o imóvel:

    ```cmd/sh
    node update_digital_twin.js
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
    Properties have been reported for component
    ```

1. No seu terminal **de serviço,** executar o seguinte comando para confirmar que a propriedade está atualizada:

    ```cmd/sh
    node get_digital_twin.js
    ```

1. Na saída do terminal de **serviço,** na resposta digital gémea sob o `thermostat1` componente, vê-se a temperatura-alvo atualizada reportada. Pode demorar algum tempo até que o dispositivo termine a atualização. Repita este passo até que o dispositivo tenha processado a atualização da propriedade:

    ```json
    targetTemperature: 42,
    ```

### <a name="invoke-a-command"></a>Invocar um comando

1. Abra o ficheiro *invoke_command.js* e reveja o código.

1. Vai para o terminal **de serviço.** Utilize o seguinte comando para executar a amostra para invocar o comando:

    ```cmd/sh
    set IOTHUB_COMMAND_NAME=getMaxMinReport
    set IOTHUB_COMMAND_PAYLOAD=commandpayload
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
    MaxMinReport commandpayload
    Response to method 'getMaxMinReport' sent successfully.
    ```

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a ligar um dispositivo IoT Plug e Play a uma solução IoT. Para saber mais sobre os modelos ioT Plug e Play, consulte:

> [!div class="nextstepaction"]
> [Guia de desenvolvedores de modelação IoT Plug e Play](concepts-developer-guide-device-csharp.md)
