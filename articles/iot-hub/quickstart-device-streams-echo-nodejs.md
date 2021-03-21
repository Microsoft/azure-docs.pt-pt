---
title: Quickstart - Comunique à aplicação do dispositivo em Node.js com streams de dispositivos Azure IoT Hub
description: Neste arranque rápido, irá executar uma aplicação Node.js do lado do serviço que comunica com um dispositivo IoT através de um fluxo de dispositivo.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 335014f032162866e4780bf1294ddcd108b4fd03
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98624393"
---
# <a name="quickstart-communicate-to-a-device-application-in-nodejs-via-iot-hub-device-streams-preview"></a>Quickstart: Comunicar a uma aplicação do dispositivo em Node.js através de fluxos de dispositivos IoT Hub (pré-visualização)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Neste arranque rápido, execute uma aplicação do lado do serviço e instale comunicação entre um dispositivo e um serviço utilizando fluxos de dispositivos. Os fluxos de dispositivos Azure IoT Hub permitem que as aplicações de serviço e dispositivos se comuniquem de forma segura e amiga da firewall. Durante a pré-visualização pública, o Node.js SDK suporta apenas os fluxos de dispositivos no lado de serviço. Como resultado, este quickstart cobre apenas instruções para executar a aplicação do lado do serviço.

## <a name="prerequisites"></a>Pré-requisitos

* Conclusão do [Comunicar para aplicações de dispositivos em C via IoT Hub streams](./quickstart-device-streams-echo-c.md) ou [Comunicar para aplicações de dispositivos em C# através de streams de dispositivos IoT Hub](./quickstart-device-streams-echo-csharp.md).

* Uma conta Azure com uma subscrição ativa. [Crie um de graça.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

* [Node.js 10+](https://nodejs.org).

    Pode verificar qual a versão atual do Node.js no seu computador de desenvolvimento através do seguinte comando:

    ```cmd/sh
    node --version
    ```

* [Uma amostra Node.js projeto.](https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

O Microsoft Azure IoT Hub suporta atualmente os fluxos de dispositivos como [uma funcionalidade de pré-visualização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

> [!IMPORTANT]
> Atualmente, a pré-visualização dos fluxos de dispositivos só é suportada para os Hubs IoT criados nas seguintes regiões:
>
> * E.U.A. Central
> * EUA Central EUAP
> * Europa do Norte
> * Sudeste Asiático

## <a name="create-an-iot-hub"></a>Criar um hub IoT

Se concluiu o anterior [Guia de Início Rápido: Enviar telemetria a partir de um dispositivo para um hub IoT](quickstart-send-telemetry-node.md), pode ignorar este passo.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registar um dispositivo

Se concluiu o anterior [Guia de Início Rápido: Enviar telemetria a partir de um dispositivo para um hub IoT](quickstart-send-telemetry-node.md), pode ignorar este passo.

É necessário registar um dispositivo no hub IoT antes de o mesmo se poder ligar. Neste início rápido, vai utilizar o Azure Cloud Shell para registar um dispositivo simulado.

1. Executar o seguinte comando em Azure Cloud Shell para criar a identidade do dispositivo.

   **Seu NomeIoTHubName**: Substitua este espaço reservado abaixo pelo nome que escolheu para o seu hub IoT.

   **MyDevice**: Este é o nome do dispositivo que está a registar. Recomenda-se que use **MyDevice** como mostrado. Se escolher um nome diferente para o seu dispositivo, também precisa de usar esse nome ao longo deste artigo e atualizar o nome do dispositivo nas aplicações da amostra antes de executá-los.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

2. Também precisa de uma *cadeia de ligação do serviço* para permitir que a aplicação back-end se ligue ao seu hub IoT e obtenha as mensagens. O seguinte comando obtém a cadeia de ligação do serviço do seu hub IoT:

    **Seu NomeIoTHubName**: Substitua este espaço reservado abaixo pelo nome que escolheu para o seu hub IoT.

    ```azurecli-interactive
    az iot hub connection-string show --policy-name service --name {YourIoTHubName} --output table
    ```

    Note o fio de ligação de serviço devolvido para posterior utilização neste arranque rápido. O aspeto é igual ao do exemplo abaixo:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-device-and-service-via-device-streams"></a>Comunicar entre dispositivo e serviço através de fluxos de dispositivos

Nesta secção, execute a aplicação do lado do dispositivo e a aplicação do lado do serviço e comunique entre os dois.

### <a name="run-the-device-side-application"></a>Executar a aplicação do lado do dispositivo

Como mencionado anteriormente, o IoT Hub Node.js SDK apenas suporta fluxos de dispositivos no lado de serviço. Para uma aplicação do lado do dispositivo, utilize um dos programas de dispositivos de acompanhamento disponíveis nestes arranques rápidos:

* [Comunicar para aplicativos de dispositivos em C via streams de dispositivo ioT Hub](./quickstart-device-streams-echo-c.md)

* [Comunicar para aplicativos de dispositivo em C# via streams de dispositivo ioT Hub](./quickstart-device-streams-echo-csharp.md)

Certifique-se de que a aplicação do lado do dispositivo está em funcionamento antes de passar ao passo seguinte.

### <a name="run-the-service-side-application"></a>Executar a aplicação do lado do serviço

A aplicação Node.js do lado do serviço neste quickstart tem as seguintes funcionalidades:

* Cria um fluxo de dispositivo para um dispositivo IoT.
* Lê a entrada da linha de comando e envia-a para a aplicação do dispositivo, que irá ecoá-la de volta.

O código demonstrará o processo de iniciação de um fluxo de dispositivos, bem como como usá-lo para enviar e receber dados.

Assumindo que a aplicação do lado do dispositivo está em execução, siga os passos abaixo numa janela do terminal local para executar a aplicação do lado do serviço em Node.js:

* Forneça as suas credenciais de serviço e iD do dispositivo como variáveis ambientais.
 
   ```cmd/sh
   # In Linux
   export IOTHUB_CONNECTION_STRING="{ServiceConnectionString}"
   export STREAMING_TARGET_DEVICE="MyDevice"

   # In Windows
   SET IOTHUB_CONNECTION_STRING={ServiceConnectionString}
   SET STREAMING_TARGET_DEVICE=MyDevice
   ```
  
   Altere o espaço reservado serviceConnectionString para combinar com a sua cadeia de ligação de serviço e **MyDevice** para combinar com o ID do seu dispositivo se tiver dado um nome diferente ao seu.

* Navegue `Quickstarts/device-streams-service` na pasta do projeto desapertado e execute a amostra com nó.

   ```cmd/sh
   cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service
    
   # Install the preview service SDK, and other dependencies
   npm install azure-iothub@streams-preview
   npm install

   node echo.js
   ```

No final do último passo, o programa do lado do serviço iniciará um fluxo para o seu dispositivo e uma vez estabelecido enviará um tampão de corda para o serviço através do fluxo. Nesta amostra, o programa do lado do serviço simplesmente lê `stdin` o no terminal e envia-o para o dispositivo, que depois ecoará de volta. Isto demonstra uma comunicação bidirecional bem sucedida entre as duas aplicações.

![Saída da consola do lado do serviço](./media/quickstart-device-streams-echo-nodejs/service-console-output.png)

Em seguida, pode terminar o programa premindo novamente a entrada.

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou um hub IoT, registou um dispositivo, estabeleceu um fluxo de dispositivos entre aplicações no dispositivo e lado de serviço, e utilizou o fluxo para enviar dados entre as aplicações.

Use os links abaixo para saber mais sobre os fluxos de dispositivos:

> [!div class="nextstepaction"]
> [Visão geral dos fluxos de dispositivos](./iot-hub-device-streams-overview.md)
