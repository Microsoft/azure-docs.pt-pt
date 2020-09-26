---
title: Ligue a amostra de IoT Plug e Play Preview Node.js código do dispositivo componente ao IoT Hub [ Microsoft Docs
description: Construa e execute a amostra de IoT Plug e Play Preview Node.js código do dispositivo que utiliza vários componentes e se conecta a um hub IoT. Utilize a ferramenta exploradora Azure IoT para visualizar as informações enviadas pelo dispositivo para o hub.
author: olivakar
ms.author: olkar
ms.date: 07/10/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.custom: devx-track-js
ms.openlocfilehash: 24bfbf4199671da497844444a57e566e66eb8c90
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91308236"
---
# <a name="tutorial-connect-a-sample-iot-plug-and-play-preview-multiple-component-device-application-to-iot-hub-nodejs"></a>Tutorial: Conecte uma aplicação de dispositivo de componente ioT plug e play de amostra ao IoT Hub (Node.js)

[!INCLUDE [iot-pnp-tutorials-device-selector.md](../../includes/iot-pnp-tutorials-device-selector.md)]

Este tutorial mostra-lhe como construir uma aplicação de dispositivo IoT Plug e Play com componentes e interface de raiz, conectá-la ao seu hub IoT e utilizar a ferramenta exploradora Azure IoT para visualizar a informação que envia para o centro. A aplicação da amostra está escrita para Node.js e está incluída no Azure IoT Hub Device SDK para Node.js. Um construtor de soluções pode utilizar a ferramenta exploradorA Azure IoT para compreender as capacidades de um dispositivo IoT Plug e Play sem a necessidade de visualizar qualquer código do dispositivo.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, precisa de Node.js na sua máquina de desenvolvimento. Pode descarregar a versão mais recente recomendada para várias plataformas a partir de [nodejs.org](https://nodejs.org).

Pode verificar qual a versão atual do Node.js no seu computador de desenvolvimento através do seguinte comando:

```cmd/sh
node --version
```

### <a name="azure-iot-explorer"></a>Explorador de Azure IoT

Para interagir com o dispositivo de amostra na segunda parte deste tutorial, utilize a ferramenta **exploradora Azure IoT.** [Descarregue e instale a mais recente versão do explorador Azure IoT](./howto-use-iot-explorer.md) para o seu sistema operativo.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Executar o seguinte comando para obter a _cadeia de ligação do hub IoT_ para o seu hub. Tome nota desta cadeia de ligação, use-a mais tarde neste tutorial:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> Também pode utilizar a ferramenta exploradora Azure IoT para encontrar a cadeia de ligação do hub IoT.

Executar o seguinte comando para obter a _cadeia de ligação_ do dispositivo para o dispositivo que adicionou ao hub. Tome nota desta cadeia de ligação, use-a mais tarde neste tutorial:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

[!INCLUDE [iot-pnp-download-models.md](../../includes/iot-pnp-download-models.md)]

## <a name="download-the-code"></a>Transferir o código

Neste tutorial, você prepara um ambiente de desenvolvimento que você pode usar para clonar e construir o Azure IoT Hub Device SDK para Node.js.

Abra um pedido de comando no diretório à sua escolha. Execute o seguinte comando para clonar o [Microsoft Azure IoT SDK para Node.js](https://github.com/Azure/azure-iot-sdk-node) repositório GitHub neste local:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node
```

Esta operação pode demorar vários minutos a ser concluída.

## <a name="install-required-libraries"></a>Instalar bibliotecas necessárias

Utilize o dispositivo SDK para construir o código de amostra incluído. A aplicação que constrói simula um dispositivo Plug and Play com múltiplos componentes e interface de raiz que se conecta a um hub IoT. A aplicação envia telemetria e propriedades e recebe comandos.

1. Numa janela de terminal local, vá à pasta do seu repositório clonado e navegue para a pasta */azure-iot-sdk-node/dispositivo/samples/pnp.* Em seguida, executar o seguinte comando para instalar as bibliotecas necessárias:

```cmd/sh
npm install
```

Isto instalará os ficheiros npm relevantes necessários para executar as amostras na pasta.

1. Configure a variável ambiente com a cadeia de ligação do dispositivo que fez uma nota anterior:

```cmd/sh
set DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
```

## <a name="review-the-code"></a>Rever o código

Navegue para a pasta *azure-iot-sdk-node\device\samples\pnp.*

A pasta *azure-iot-sdk-node\device\samples\pnp* contém o código de amostra para o dispositivo de controlador de temperatura IoT Plug e Play.

O código no ficheiro *pnpTemperatureController.js* implementa um dispositivo de controlador de temperatura IoT Plug e Play. O modelo que esta amostra implementa utiliza [vários componentes.](concepts-components.md) O ficheiro de definição de linguagem de [definição de Gémeos Digitais (DTDL) para o dispositivo de temperatura](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) define a telemetria, propriedades e comanda os dispositivos.

Abra o ficheiro *pnpTemperatureController.js* num editor de código à sua escolha. O código de amostra mostra como:

1. Defina o `modelId` que é o DTMI para o dispositivo que está a ser implementado. Este DTMI é definido pelo utilizador e deve coincidir com o DTMI do [modelo DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json)do controlador de temperatura .

2. Implementar os componentes definidos no modelo DTDL do controlador de temperatura. Os componentes de um controlador de temperatura real devem implementar estas duas interfaces. Estas duas interfaces já estão publicadas num repositório central. Nesta amostra, as duas interfaces são:
  - Termóstato
  - Informação do dispositivo desenvolvida pela Azure

3. Defina nomes de componentes. Esta amostra tem dois termostatos e um componente de informação do dispositivo.

4. Defina o nome de comando. Estes são os comandos a que o dispositivo responde.

5. Defina a `serialNumber` constante. O `serialNumber` dispositivo é fixado.

6. Defina os manipuladores de comando.

7. Defina as funções para enviar respostas de comando.

8. Defina funções de ajudante para registar pedidos de comando.

9. Defina uma função de ajudante para criar as propriedades.

10. Defina um ouvinte para atualizações de propriedade.

11. Defina uma função para enviar telemetria a partir deste dispositivo. Tanto os termostatos como o componente da raiz enviam telemetria. Esta função recebe o nome do componente como parâmetro.

12. Definir uma `main` função que:

    1. Utiliza o dispositivo SDK para criar um cliente do dispositivo e ligar-se ao seu hub IoT. O dispositivo fornece o `modelId` modo de IoT Hub identificar o dispositivo como um dispositivo IoT Plug and Play.

    1. Começa a ouvir pedidos de comando utilizando a `onDeviceMethod` função. A função configura um ouvinte para pedidos de comando do serviço:
        - O dispositivo DTDL define os `reboot` comandos e `getMaxMinReport` comandos.
        - A `commandHandler` função define como o dispositivo responde a um comando.

    1. Começa a enviar telemetria utilizando `setInterval` e `sendTelemetry` .

    1. Utiliza a `helperCreateReportedPropertiesPatch` função para criar as propriedades e `updateComponentReportedProperties` atualizar as propriedades.

    1. Usa `desiredPropertyPatchListener` para ouvir atualizações de propriedade.

    1. Desativa todos os ouvintes e tarefas e sai do loop quando pressiona **Q** ou **q**.

Agora que viu o código, use o seguinte comando para executar a amostra:

```cmd\sh
node pnpTemperatureController.js
```

Vê a seguinte saída, indicando que o dispositivo começou a enviar dados de telemetria para o hub, estando agora pronto para receber comandos e atualizações de propriedade.

![Mensagens de confirmação do dispositivo](media/tutorial-multiple-components-node/multiple-component.png)

Mantenha a amostra a funcionar à medida que completar os próximos passos.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Use o explorador Azure IoT para validar o código

Depois de iniciar a amostra do cliente do dispositivo, utilize a ferramenta exploradora Azure IoT para verificar se está a funcionar.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a ligar um dispositivo IoT Plug and Play com componentes a um hub IoT. Para saber mais sobre os modelos ioT Plug e Play, consulte:

> [!div class="nextstepaction"]
> [Guia de modelação IoT Plug e Play Preview](concepts-developer-guide.md)
