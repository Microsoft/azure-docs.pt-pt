---
title: Dispositivo Azure IoT Hub transmite node.js quickstart para SSH e RDP
description: Neste arranque rápido, executa uma aplicação Node.js de amostra que funciona como um proxy para permitir cenários de SSH e RDP sobre streams de dispositivos IoT Hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: f7dfa1bf391e4affba52fc40a8c22ea9b5f4b4df
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77470688"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-nodejs-proxy-application-preview"></a>Quickstart: Ative o SSH e o RDP sobre um fluxo de dispositivos IoT Hub utilizando uma aplicação de proxy Node.js (pré-visualização)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Neste arranque rápido, ativa o tráfego secure Shell (SSH) e Remote Desktop Protocol (RDP) para ser enviado para o dispositivo através de um fluxo de dispositivos. Os streams de dispositivos Azure IoT Hub permitem que as aplicações de serviço e dispositivo saem de forma segura e compatível com firewall. Este quickstart descreve a execução de uma aplicação de procuração Node.js que está a funcionar no lado do serviço. Durante a pré-visualização pública, o Node.js SDK suporta fluxos de dispositivos apenas no lado do serviço. Como resultado, este quickstart cobre instruções para executar apenas o pedido de procuração local de serviço.

## <a name="prerequisites"></a>Pré-requisitos

* Conclusão do [Enable SSH e RDP sobre os fluxos de dispositivos IoT Hub utilizando uma aplicação](./quickstart-device-streams-proxy-c.md) de procuração C ou [ativar o SSH e o RDP sobre os fluxos de dispositivos IoT Hub utilizando uma C# aplicação proxy](./quickstart-device-streams-proxy-csharp.md).

* Uma conta Azure com uma subscrição ativa. [Crie um de graça.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

* [Node.js 10+](https://nodejs.org).

* [Um projeto de node.js de amostra.](https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip)

Pode verificar a versão atual do Node.js na sua máquina de desenvolvimento utilizando o seguinte comando:

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

Adicione a extensão Azure IoT para Azure CLI à sua instância Cloud Shell executando o seguinte comando. A extensão IoT adiciona comandos específicos do IoT Hub, IoT Edge e IoT Device Provisioning Service (DPS) específicos para o Azure CLI.

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

## <a name="create-an-iot-hub"></a>Criar um hub IoT

Se concluiu o anterior [Guia de Início Rápido: Enviar telemetria a partir de um dispositivo para um hub IoT](quickstart-send-telemetry-node.md), pode ignorar este passo.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registar um dispositivo

Se tiver concluído [o Quickstart: Envie a telemetria de um dispositivo para um hub IoT,](quickstart-send-telemetry-node.md)pode saltar este passo.

É necessário registar um dispositivo no hub IoT antes de o mesmo se poder ligar. Nesta secção, utiliza-se a Azure Cloud Shell para registar um dispositivo simulado.

1. Para criar a identidade do dispositivo, execute o seguinte comando na Cloud Shell:

   > [!NOTE]
   > * Substitua o espaço reservado *YourIoTHubName* pelo nome que escolheu para o seu hub IoT.
   > * Para o nome do dispositivo que está a registar, é aconselhável utilizar o *MyDevice* como mostrado. Se escolher um nome diferente para o seu dispositivo, utilize esse nome ao longo deste artigo e atualize o nome do dispositivo nas aplicações da amostra antes de os executar.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Para permitir que a aplicação back-end se ligue ao seu hub IoT e recupere as mensagens, também precisa de uma cadeia de *ligação*de serviço . O seguinte comando recupera a corda para o seu hub IoT:

   > [!NOTE]
   > Substitua o espaço reservado *YourIoTHubName* pelo nome que escolheu para o seu hub IoT.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
    ```

   Note a cadeia de ligação de serviço devolvida para posterior utilização neste arranque rápido. O aspeto é igual ao do exemplo abaixo:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="ssh-to-a-device-via-device-streams"></a>SSH para um dispositivo através de fluxos de dispositivos

Nesta secção, estabelece-se um fluxo de ponta a ponta para o túnel de tráfego ssh.

### <a name="run-the-device-local-proxy-application"></a>Executar a aplicação de procuração local de dispositivo

Como mencionado anteriormente, o IoT Hub Node.js SDK suporta fluxos de dispositivos apenas no lado do serviço. Para a aplicação local do dispositivo, utilize uma aplicação proxy do dispositivo que esteja disponível num dos seguintes arranques rápidos:

   * [Ativar sSH e RDP sobre fluxos de dispositivos IoT Hub utilizando uma aplicação de procuração C](./quickstart-device-streams-proxy-c.md)
   * [Ativar sSH e RDP sobre fluxos de C# dispositivos IoT Hub utilizando uma aplicação proxy](./quickstart-device-streams-proxy-csharp.md) 

Antes de passar para o próximo passo, certifique-se de que a aplicação de procuração local do dispositivo está em execução. Para uma visão geral da configuração, consulte a [Amostra de Procuração Local](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp).

### <a name="run-the-service-local-proxy-application"></a>Executar a aplicação de procuração local de serviço

Este artigo descreve a configuração para SSH (utilizando a porta 22) e descreve como modificar a configuração para RDP (que utiliza a porta 3389). Como os fluxos de dispositivos são aplicação e protocolo-agnóstico, pode modificar a mesma amostra para acomodar outros tipos de tráfego de aplicações do servidor cliente, geralmente modificando a porta de comunicação.

Com a aplicação de procuração local do dispositivo em execução, execute a aplicação de procuração local de serviço que está escrita no Node.js fazendo o seguinte numa janela de terminal local:

1. Para variáveis ambientais, forneça as suas credenciais de serviço, o ID do dispositivo-alvo onde o daemon SSH corre, e o número de porta para o proxy que está em execução no dispositivo.

   ```
   # In Linux
   export IOTHUB_CONNECTION_STRING="{ServiceConnectionString}"
   export STREAMING_TARGET_DEVICE="MyDevice"
   export PROXY_PORT=2222

   # In Windows
   SET IOTHUB_CONNECTION_STRING={ServiceConnectionString}
   SET STREAMING_TARGET_DEVICE=MyDevice
   SET PROXY_PORT=2222
   ```

   Altere o espaço reservado ServiceConnectionString para combinar com a sua cadeia de ligação de serviço e **o MyDevice** para combinar com o ID do seu dispositivo se tiver dado um nome diferente ao seu.

1. Navegue para o diretório `Quickstarts/device-streams-service` na sua pasta de projeto sem fecho. Utilize o seguinte código para executar a aplicação de procuração local de serviço:

   ```
   cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service

   # Install the preview service SDK, and other dependencies
   npm install azure-iothub@streams-preview
   npm install

   # Run the service-local proxy application
   node proxy.js
   ```

### <a name="ssh-to-your-device-via-device-streams"></a>SSH para o seu dispositivo através de fluxos de dispositivos

Em Linux, execute o SSH utilizando `ssh $USER@localhost -p 2222` num terminal. No Windows, utilize o seu cliente SSH favorito (por exemplo, PuTTY).

Saída de consola no serviço local após a sessão SSH ser estabelecida (a aplicação de procuração local de serviço ouve na porta 2222):

![Saída do terminal SSH](./media/quickstart-device-streams-proxy-nodejs/service-console-output.png)

Saída de consola da aplicação cliente SSH (cliente SSH comunica com daemon SSH ligando-se à porta 22, onde a aplicação de procuração local de serviço está a ouvir):

![Saída de cliente SSH](./media/quickstart-device-streams-proxy-nodejs/ssh-console-output.png)

### <a name="rdp-to-your-device-via-device-streams"></a>RDP para o seu dispositivo através de fluxos de dispositivos

Utilize agora a sua aplicação de cliente RDP e ligue-se ao proxy de serviço na porta 2222, uma porta arbitrária que escolheu anteriormente.

> [!NOTE]
> Certifique-se de que o seu proxy do dispositivo está configurado corretamente para RDP e configurado com a porta RDP 3389.

![O cliente RDP liga-se à aplicação de procuração local de serviço](./media/quickstart-device-streams-proxy-nodejs/rdp-screen-capture.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou um hub IoT, registou um dispositivo e implementou uma aplicação de procuração de serviço para permitir rdp e SSH num dispositivo IoT. O tráfego rdp e SSH será escavado através de um fluxo de dispositivo através do centro IoT. Este processo elimina a necessidade de conectividade direta com o dispositivo.

Para saber mais sobre os fluxos de dispositivos, consulte:

> [!div class="nextstepaction"]
> [Visão geral dos fluxos de dispositivos](./iot-hub-device-streams-overview.md)
