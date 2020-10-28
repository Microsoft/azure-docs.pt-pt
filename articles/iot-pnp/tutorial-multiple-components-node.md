---
title: Ligue o código do dispositivo Node.js de componentes IoT Plug e Play Microsoft Docs
description: Construa e execute a amostra IoT Plug e Play Node.js código do dispositivo que utiliza vários componentes e se conecta a um hub IoT. Utilize a ferramenta exploradora Azure IoT para visualizar as informações enviadas pelo dispositivo para o hub.
author: olivakar
ms.author: olkar
ms.date: 07/10/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 2f0bf3b4b6e7091222b98e1743f0fb4f431fdd12
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92736056"
---
# <a name="tutorial-connect-a-sample-iot-plug-and-play-multiple-component-device-application-to-iot-hub-nodejs"></a>Tutorial: Ligue uma amostra IoT Plug e jogue a aplicação de vários componentes do dispositivo ao IoT Hub (Node.js)

[!INCLUDE [iot-pnp-tutorials-device-selector.md](../../includes/iot-pnp-tutorials-device-selector.md)]

Este tutorial mostra-lhe como construir uma aplicação de dispositivo IoT Plug e Play com componentes, conectá-la ao seu hub IoT e utilizar a ferramenta exploradora Azure IoT para visualizar a informação que envia para o centro. A aplicação da amostra está escrita para Node.js e está incluída no Azure IoT Hub Device SDK para Node.js. Um construtor de soluções pode utilizar a ferramenta exploradorA Azure IoT para compreender as capacidades de um dispositivo IoT Plug e Play sem a necessidade de visualizar qualquer código do dispositivo.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Para completar este tutorial, precisa de Node.js na sua máquina de desenvolvimento. Pode descarregar a versão mais recente recomendada para várias plataformas a partir de [nodejs.org](https://nodejs.org).

Pode verificar qual a versão atual do Node.js no seu computador de desenvolvimento através do seguinte comando:

```cmd/sh
node --version
```

## <a name="download-the-code"></a>Transferir o código

Se tiver concluído [o Quickstart: Ligue uma aplicação de dispositivo IoT Plug e Play de amostra que está a decorrer no Windows to IoT Hub (Nó)](quickstart-connect-device-node.md), já clonou o repositório.

Abra um pedido de comando no diretório à sua escolha. Execute o seguinte comando para clonar o [Microsoft Azure IoT SDK para Node.js](https://github.com/Azure/azure-iot-sdk-node) repositório GitHub neste local:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node
```

## <a name="install-required-libraries"></a>Instalar bibliotecas necessárias

Utilize o dispositivo SDK para construir o código de amostra incluído. A aplicação que constrói simula um dispositivo Plug and Play com vários componentes que se conectam a um hub IoT. A aplicação envia telemetria e propriedades e recebe comandos.

1. Numa janela de terminal local, vá à pasta do seu repositório clonado e navegue para a pasta */azure-iot-sdk-node/dispositivo/samples/pnp.* Em seguida, executar o seguinte comando para instalar as bibliotecas necessárias:

```cmd/sh
npm install
```

Isto instalará os ficheiros npm relevantes necessários para executar as amostras na pasta.

## <a name="review-the-code"></a>Rever o código

Navegue para a pasta *azure-iot-sdk-node\device\samples\pnp.*

A pasta *azure-iot-sdk-node\device\samples\pnp* contém o código de amostra para o dispositivo de controlador de temperatura IoT Plug e Play.

O código no ficheiro *pnpTemperatureController.js* implementa um dispositivo de controlador de temperatura IoT Plug e Play. O modelo que esta amostra implementa utiliza [vários componentes.](concepts-components.md) O ficheiro de definição de linguagem de [definição de Gémeos Digitais (DTDL) para o dispositivo de temperatura](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) define a telemetria, propriedades e comanda os dispositivos.

Abra o ficheiro *pnpTemperatureController.js* num editor de código à sua escolha. O código de amostra mostra como:

- Defina o `modelId` que é o DTMI para o dispositivo que está a ser implementado. Este DTMI é definido pelo utilizador e deve coincidir com o DTMI do [modelo DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json)do controlador de temperatura .

- Implementar os componentes definidos no modelo DTDL do controlador de temperatura. Os componentes de um controlador de temperatura real devem implementar estas duas interfaces. Estas duas interfaces já estão publicadas num repositório central. Nesta amostra, as duas interfaces são:

  - Termóstato
  - Informação do dispositivo desenvolvida pela Azure

- Defina nomes de componentes. Esta amostra tem dois termostatos e um componente de informação do dispositivo.

- Defina o nome de comando. Estes são os comandos a que o dispositivo responde.

- Defina a `serialNumber` constante. O `serialNumber` dispositivo é fixado.

- Defina os manipuladores de comando.

- Defina as funções para enviar respostas de comando.

- Defina funções de ajudante para registar pedidos de comando.

- Defina uma função de ajudante para criar as propriedades.

- Defina um ouvinte para atualizações de propriedade.

- Defina uma função para enviar telemetria a partir deste dispositivo. Tanto os termostatos como o componente predefinido enviam telemetria. Esta função recebe o nome do componente como parâmetro.

- Definir uma `main` função que:

  - Utiliza o dispositivo SDK para criar um cliente do dispositivo e ligar-se ao seu hub IoT. O dispositivo fornece o `modelId` modo de IoT Hub identificar o dispositivo como um dispositivo IoT Plug and Play.

  - Começa a ouvir pedidos de comando utilizando a `onDeviceMethod` função. A função configura um ouvinte para pedidos de comando do serviço:

    - O dispositivo DTDL define os `reboot` comandos e `getMaxMinReport` comandos.
    - A `commandHandler` função define como o dispositivo responde a um comando.

  - Começa a enviar telemetria utilizando `setInterval` e `sendTelemetry` .

  - Utiliza a `helperCreateReportedPropertiesPatch` função para criar as propriedades e `updateComponentReportedProperties` atualizar as propriedades.

  - Usa `desiredPropertyPatchListener` para ouvir atualizações de propriedade.

  - Desativa todos os ouvintes e tarefas e sai do loop quando pressiona **Q** ou **q** .

[!INCLUDE [iot-pnp-environment](../../includes/iot-pnp-environment.md)]

Para saber mais sobre a configuração da amostra, consulte o [produto de leitura](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/pnp/readme.md)da amostra .

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
> [Guia de desenvolvedores de modelação IoT Plug e Play](concepts-developer-guide-device-csharp.md)
