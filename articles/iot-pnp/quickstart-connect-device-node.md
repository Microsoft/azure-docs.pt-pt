---
title: Quickstart - Connect IoT Plug and Play sample Node.js código do dispositivo ao Azure IoT Hub / Microsoft Docs
description: Quickstart - Utilize Node.js para construir e executar o código do dispositivo de amostra IoT Plug e Play que se conecta a um hub IoT. Utilize a ferramenta exploradora Azure IoT para visualizar as informações enviadas pelo dispositivo para o hub.
author: ericmitt
ms.author: ericmitt
ms.date: 07/10/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc, devx-track-js
ms.openlocfilehash: 24f6cc1e9656957d6894ecb6b7c3e0476b233cf6
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421588"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-device-application-to-iot-hub-nodejs"></a>Quickstart: Ligue uma aplicação de dispositivo IoT Plug e Play à IoT Hub (Node.js)

[!INCLUDE [iot-pnp-quickstarts-device-selector.md](../../includes/iot-pnp-quickstarts-device-selector.md)]

Este quickstart mostra-lhe como construir uma aplicação de dispositivo IoT Plug e Play de amostra, conectá-la ao seu hub IoT e utilizar a ferramenta exploradora Azure IoT para visualizar a telemetria que envia. A aplicação da amostra está escrita em Node.js e está incluída no dispositivo Azure IoT SDK para Node.js. Um construtor de soluções pode utilizar a ferramenta exploradorA Azure IoT para compreender as capacidades de um dispositivo IoT Plug e Play sem a necessidade de visualizar qualquer código do dispositivo.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Para completar este arranque rápido, precisa de Node.js na sua máquina de desenvolvimento. Pode descarregar a versão mais recente recomendada para várias plataformas a partir de [nodejs.org](https://nodejs.org).

Pode verificar qual a versão atual do Node.js no seu computador de desenvolvimento através do seguinte comando:

```cmd/sh
node --version
```

## <a name="download-the-code"></a>Transferir o código

Neste arranque rápido, você prepara um ambiente de desenvolvimento que você pode usar para clonar e construir o Azure IoT Hub Device SDK para Node.js.

Abra um pedido de comando no diretório à sua escolha. Execute o seguinte comando para clonar o [Microsoft Azure IoT SDK para Node.js](https://github.com/Azure/azure-iot-sdk-node) repositório GitHub neste local:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node
```

## <a name="install-required-libraries"></a>Instalar bibliotecas necessárias

Utilize o dispositivo SDK para construir o código de amostra incluído. A aplicação que constrói simula um dispositivo que se conecta a um hub IoT. A aplicação envia telemetria e propriedades e recebe comandos.

1. Numa janela de terminal local, vá à pasta do seu repositório clonado e navegue para a pasta */azure-iot-sdk-node/dispositivo/samples/pnp.* Em seguida, executar o seguinte comando para instalar as bibliotecas necessárias:

    ```cmd/sh
    npm install
    ```

1. Configure a variável ambiente com a cadeia de ligação do dispositivo que fez uma nota anterior:

    ```cmd/sh
    set IOTHUB_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

## <a name="run-the-sample-device"></a>Executar o dispositivo de amostra

Esta amostra implementa um simples dispositivo de termóstato IoT Plug e Play. O modelo que esta amostra implementa não utiliza [componentes](concepts-components.md)IoT Plug e Play . O [ficheiro do modelo DTDL para o dispositivo termóstato](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) define a telemetria, as propriedades e o comando dos dispositivos.

Abra o ficheiro _simple_thermostat.js._ Neste ficheiro, pode ver como:

1. Importe as interfaces necessárias.
1. Escreva um manipulador de atualização de propriedade e um manipulador de comando.
1. Manuseie os patches de propriedade desejados e envie telemetria.
1. Opcionalmente, a provisione o seu dispositivo utilizando o Serviço de Provisionamento de Dispositivos Azure (DPS).

Na função principal, pode ver como tudo se conjuga:

1. Crie o dispositivo a partir da sua cadeia de ligação ou forneça-o utilizando DPS.)
1. Utilize a opção **modelID** para especificar o modelo do dispositivo IoT Plug e Play.
1. Ativar o controlador de comando.
1. Envie telemetria do dispositivo para o seu centro.
1. Obtenha os dispositivos gémeos e atualize as propriedades reportadas.
1. Ativar o manipulador de atualização de propriedade pretendido.

[!INCLUDE [iot-pnp-environment](../../includes/iot-pnp-environment.md)]

Para saber mais sobre a configuração da amostra, consulte o [produto de leitura](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/pnp/readme.md)da amostra .

Execute a aplicação de amostra para simular um dispositivo IoT Plug and Play que envia telemetria para o seu hub IoT. Para executar a aplicação da amostra, utilize o seguinte comando:

```cmd\sh
node simple_thermostat.js
```

Vê a seguinte saída, indicando que o dispositivo começou a enviar dados de telemetria para o hub, estando agora pronto para receber comandos e atualizações de propriedade.

![Mensagens de confirmação do dispositivo](media/quickstart-connect-device-node/device-confirmation-node.png)

Mantenha a amostra a funcionar à medida que completar os próximos passos.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Use o explorador Azure IoT para validar o código

Depois de iniciar a amostra do cliente do dispositivo, utilize a ferramenta exploradora Azure IoT para verificar se está a funcionar.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a ligar um dispositivo IoT Plug e Play a um hub IoT. Para saber mais sobre como construir uma solução que interage com os seus dispositivos IoT Plug e Play, consulte:

> [!div class="nextstepaction"]
> [Interaja com um dispositivo IoT Plug and Play que está ligado à sua solução](quickstart-service-node.md)
