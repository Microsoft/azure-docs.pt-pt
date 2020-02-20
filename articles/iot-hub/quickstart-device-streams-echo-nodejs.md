---
title: Comunicar para dispositivo app em Node.js com streams de dispositivos Azure IoT Hub
description: Neste arranque rápido, você executará uma aplicação do lado do serviço Node.js que comunica com um dispositivo IoT através de um fluxo de dispositivo.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 3bc5dc754509260591acf7c5d5809d5e85794d9b
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77471929"
---
# <a name="quickstart-communicate-to-a-device-application-in-nodejs-via-iot-hub-device-streams-preview"></a>Quickstart: Comunicar a uma aplicação de dispositivo no Node.js através de streams de dispositivos IoT Hub (pré-visualização)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Neste arranque rápido, executa uma aplicação do lado do serviço e configura a comunicação entre um dispositivo e o serviço utilizando fluxos de dispositivos. Os streams de dispositivos Azure IoT Hub permitem que as aplicações de serviço e dispositivo saem de forma segura e compatível com firewall. Durante a pré-visualização pública, o Node.js SDK apenas suporta streams de dispositivos no lado do serviço. Como resultado, este arranque rápido apenas cobre instruções para executar a aplicação do lado do serviço.

## <a name="prerequisites"></a>Pré-requisitos

* Conclusão da Comunicação para aplicações de dispositivos em C através de fluxos de [dispositivos IoT Hub](./quickstart-device-streams-echo-c.md) ou Comunicar para dispositivos aplicações através [ C# de streams de dispositivos IoT Hub](./quickstart-device-streams-echo-csharp.md).

* Uma conta Azure com uma subscrição ativa. [Crie um de graça.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

* [Node.js 10+](https://nodejs.org).

* [Um projeto de node.js de amostra.](https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip)

Pode verificar qual a versão atual do Node.js no seu computador de desenvolvimento através do seguinte comando:

```cmd/sh
node --version
```

O Microsoft Azure IoT Hub suporta atualmente os streams do dispositivo como uma funcionalidade de [pré-visualização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

> [!IMPORTANT]
> A visualização dos fluxos de dispositivos é atualmente suportada apenas para os Hubs IoT criados nas seguintes regiões:
>
> * E.U.A. Central
> * EUA Centrais EUA
> * Europa do Norte
> * Ásia Sudeste

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="add-azure-iot-extension"></a>Adicionar extensão Azure IoT

Execute o seguinte comando para adicionar a extensão Microsoft Azure IoT para Azure CLI à sua instância Cloud Shell. A extensão IoT adiciona comandos do IoT Hub, IoT Edge e IoT Device Provisioning Service (DPS) ao Azure CLI.

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

## <a name="create-an-iot-hub"></a>Criar um hub IoT

Se concluiu o anterior [Guia de Início Rápido: Enviar telemetria a partir de um dispositivo para um hub IoT](quickstart-send-telemetry-node.md), pode ignorar este passo.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registar um dispositivo

Se concluiu o anterior [Guia de Início Rápido: Enviar telemetria a partir de um dispositivo para um hub IoT](quickstart-send-telemetry-node.md), pode ignorar este passo.

É necessário registar um dispositivo no hub IoT antes de o mesmo se poder ligar. Neste início rápido, vai utilizar o Azure Cloud Shell para registar um dispositivo simulado.

1. Executar o seguinte comando em Azure Cloud Shell para criar a identidade do dispositivo.

   **YourIoTHubName**: Substitua este espaço reservado abaixo com o nome que escolheu para o seu hub IoT.

   **MyDevice**: Este é o nome do dispositivo que está a registar. É aconselhável usar o **MyDevice** como mostrado. Se escolher um nome diferente para o seu dispositivo, também precisa de usar esse nome ao longo deste artigo e atualizar o nome do dispositivo nas aplicações da amostra antes de os executar.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

2. Também precisa de uma *cadeia de ligação do serviço* para permitir que a aplicação back-end se ligue ao seu hub IoT e obtenha as mensagens. O seguinte comando obtém a cadeia de ligação do serviço do seu hub IoT:

    **YourIoTHubName**: Substitua este espaço reservado abaixo com o nome que escolheu para o seu hub IoT.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
    ```

    Note a cadeia de ligação de serviço devolvida para posterior utilização neste arranque rápido. O aspeto é igual ao do exemplo abaixo:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-device-and-service-via-device-streams"></a>Comunicar entre dispositivo e serviço através de fluxos de dispositivos

Nesta secção, executa tanto a aplicação do lado do dispositivo como a aplicação do lado do serviço e comunica entre os dois.

### <a name="run-the-device-side-application"></a>Executar a aplicação do lado do dispositivo

Como mencionado anteriormente, o IoT Hub Node.js SDK apenas suporta fluxos de dispositivos no lado do serviço. Para uma aplicação do lado do dispositivo, utilize um dos programas de dispositivos que o acompanham disponíveis nestes arranques rápidos:

* [Comunicar com aplicações de dispositivos em C através de fluxos de dispositivos IoT Hub](./quickstart-device-streams-echo-c.md)

* [Comunicar com aplicações de dispositivos através C# de fluxos de dispositivos IoT Hub](./quickstart-device-streams-echo-csharp.md)

Certifique-se de que a aplicação do lado do dispositivo está em execução antes de passar para o próximo passo.

### <a name="run-the-service-side-application"></a>Executar a aplicação do lado do serviço

A aplicação node.js do lado do serviço neste quickstart tem as seguintes funcionalidades:

* Cria um fluxo de dispositivo para um dispositivo IoT.
* Lê a entrada da linha de comando e envia-a para a aplicação do dispositivo, que irá ecoá-la de volta.

O código demonstrará o processo de iniciação de um fluxo de dispositivos, bem como como usá-lo para enviar e receber dados.

Assumindo que a aplicação do lado do dispositivo está em execução, siga os passos abaixo numa janela de terminal local para executar a aplicação do lado do serviço no Node.js:

* Forneça as suas credenciais de serviço e id do dispositivo como variáveis ambientais.
 
   ```cmd/sh
   # In Linux
   export IOTHUB_CONNECTION_STRING="{ServiceConnectionString}"
   export STREAMING_TARGET_DEVICE="MyDevice"

   # In Windows
   SET IOTHUB_CONNECTION_STRING={ServiceConnectionString}
   SET STREAMING_TARGET_DEVICE=MyDevice
   ```
  
   Altere o espaço reservado ServiceConnectionString para combinar com a sua cadeia de ligação de serviço e **o MyDevice** para combinar com o ID do seu dispositivo se tiver dado um nome diferente ao seu.

* Navegue para `Quickstarts/device-streams-service` na pasta do projeto desapertado e execute a amostra utilizando o nó.

   ```cmd/sh
   cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service
    
   # Install the preview service SDK, and other dependencies
   npm install azure-iothub@streams-preview
   npm install

   node echo.js
   ```

No final do último passo, o programa do lado do serviço iniciará um fluxo para o seu dispositivo e uma vez estabelecido enviará um tampão de cadeia para o serviço através do fluxo. Nesta amostra, o programa do lado do serviço simplesmente lê o `stdin` no terminal e envia-o para o dispositivo, que depois irá ecoá-lo de volta. Isto demonstra uma comunicação bidirecional bem sucedida entre as duas aplicações.

![Saída de consola do lado do serviço](./media/quickstart-device-streams-echo-nodejs/service-console-output.png)

Em seguida, pode terminar o programa pressionando a entrada novamente.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou um hub IoT, registou um dispositivo, estabeleceu um fluxo de dispositivos entre aplicações no dispositivo e no lado do serviço, e utilizou o fluxo para enviar dados para trás e para a frente entre as aplicações.

Utilize os links abaixo para saber mais sobre os fluxos de dispositivos:

> [!div class="nextstepaction"]
> [Visão geral dos fluxos de dispositivos](./iot-hub-device-streams-overview.md) 