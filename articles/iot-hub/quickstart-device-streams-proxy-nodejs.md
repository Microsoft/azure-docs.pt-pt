---
title: Quickstart - Azure IoT Hub streams Node.js quickstart para SSH e RDP
description: Neste quickstart, você executou uma amostra Node.js aplicação que funciona como um proxy para permitir cenários de SSH e RDP sobre fluxos de dispositivos IoT Hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: references_regions
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: ef45a6277adeff09a34fe22b7abeb21d3e603167
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98624359"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-nodejs-proxy-application-preview"></a>Quickstart: Ative o SSH e o RDP sobre um fluxo de dispositivos IoT Hub utilizando uma aplicação de procuração Node.js (pré-visualização)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Neste arranque rápido, ativa-se o tráfego de Secure Shell (SSH) e Remote Desktop Protocol (RDP) para o dispositivo através de uma corrente de dispositivo. Os fluxos de dispositivos Azure IoT Hub permitem que as aplicações de serviço e dispositivos se comuniquem de forma segura e amiga da firewall. Este quickstart descreve a execução de uma aplicação de procuração Node.js que está a decorrer no lado de serviço. Durante a pré-visualização pública, o Node.js SDK suporta fluxos de dispositivo apenas no lado do serviço. Como resultado, este quickstart cobre instruções para executar apenas a aplicação de procuração local de serviço.

## <a name="prerequisites"></a>Pré-requisitos

* Conclusão do [Enable SSH e RDP sobre fluxos de dispositivos IoT Hub utilizando uma aplicação de procuração C](./quickstart-device-streams-proxy-c.md) ou [ativar fluxos de dispositivoS SSH e RDP sobre fluxos de dispositivos IoT Hub utilizando uma aplicação de procuração C#](./quickstart-device-streams-proxy-csharp.md).

* Uma conta Azure com uma subscrição ativa. [Crie um de graça.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

* [Node.js 10+](https://nodejs.org).

    Pode verificar a versão atual de Node.js na sua máquina de desenvolvimento utilizando o seguinte comando:

    ```cmd/sh
    node --version
    ```

* [Uma amostra Node.js projeto.](https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

O Microsoft Azure IoT Hub suporta atualmente os fluxos de dispositivos como [uma funcionalidade de pré-visualização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

> [!IMPORTANT]
> Atualmente, a pré-visualização dos fluxos de dispositivos só é suportada para os Hubs IoT criados nas seguintes regiões:
>
> * E.U.A. Central
> * EUA Central EUAP
> * Europa do Norte
> * Sudeste Asiático

### <a name="add-azure-iot-extension"></a>Adicionar extensão Azure IoT

Adicione a extensão Azure IoT para Azure CLI à sua instância Cloud Shell executando o seguinte comando. A extensão IoT adiciona comandos específicos do IoT Hub, IoT Edge e IoT Device Provisioning Service (DPS) ao Azure CLI.

```azurecli-interactive
az extension add --name azure-iot
```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Criar um hub IoT

Se concluiu o anterior [Guia de Início Rápido: Enviar telemetria a partir de um dispositivo para um hub IoT](quickstart-send-telemetry-node.md), pode ignorar este passo.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registar um dispositivo

Se tiver completado [Quickstart: Enviar telemetria de um dispositivo para um hub IoT,](quickstart-send-telemetry-node.md)pode saltar este passo.

É necessário registar um dispositivo no hub IoT antes de o mesmo se poder ligar. Nesta secção, utilize a Azure Cloud Shell para registar um dispositivo simulado.

1. Para criar a identidade do dispositivo, executar o seguinte comando em Cloud Shell:

   > [!NOTE]
   > * Substitua o espaço reservado *YourIoTHubName* pelo nome que escolheu para o seu hub IoT.
   > * Para o nome do dispositivo que está a registar, é aconselhável utilizar *o MyDevice* como mostrado. Se escolher um nome diferente para o seu dispositivo, use esse nome ao longo deste artigo e atualize o nome do dispositivo nas aplicações da amostra antes de executá-los.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Para ativar a aplicação back-end para ligar ao seu hub IoT e recuperar as mensagens, também precisa de uma cadeia de *ligação de serviço*. O seguinte comando recupera a corda para o seu hub IoT:

   > [!NOTE]
   > Substitua o espaço reservado *YourIoTHubName* pelo nome que escolheu para o seu hub IoT.

    ```azurecli-interactive
    az iot hub connection-string show --policy-name service --hub-name {YourIoTHubName} --output table
    ```

   Note o fio de ligação de serviço devolvido para posterior utilização neste arranque rápido. O aspeto é igual ao do exemplo abaixo:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="ssh-to-a-device-via-device-streams"></a>SSH para um dispositivo através de fluxos de dispositivos

Nesta secção, estabelece-se um fluxo de ponta a ponta para o tráfego do túnel SSH.

### <a name="run-the-device-local-proxy-application"></a>Executar a aplicação de procuração local do dispositivo

Como mencionado anteriormente, o IoT Hub Node.js SDK suporta fluxos de dispositivo apenas no lado do serviço. Para a aplicação local do dispositivo, utilize uma aplicação proxy do dispositivo disponível num dos seguintes quickstarts:

   * [Ativar fluxos de dispositivos SSH e RDP através de uma aplicação de procuração C](./quickstart-device-streams-proxy-c.md)
   * [Ativar fluxos de dispositivos SSH e RDP através de uma aplicação de procuração C#](./quickstart-device-streams-proxy-csharp.md) 

Antes de avançar para o passo seguinte, certifique-se de que a aplicação de procuração local do dispositivo está em execução. Para obter uma visão geral da configuração, consulte a [Amostra de Procuração Local](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp).

### <a name="run-the-service-local-proxy-application"></a>Executar a aplicação de procuração local de serviço

Este artigo descreve a configuração para SSH (utilizando a porta 22) e, em seguida, descreve como modificar a configuração para RDP (que utiliza a porta 3389). Como os fluxos de dispositivos são aplicações e protocolo-agnósticos, pode modificar a mesma amostra para acomodar outros tipos de tráfego de aplicações do servidor cliente, normalmente modificando a porta de comunicação.

Com a aplicação de procuração local do dispositivo em execução, executa a aplicação de procuração local de serviço que está escrita em Node.js fazendo o seguinte numa janela de terminal local:

1. Para variáveis ambientais, forneça as suas credenciais de serviço, o ID do dispositivo alvo onde funciona o daemon SSH e o número de porta para o proxy que está em execução no dispositivo.

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

   Altere o espaço reservado serviceConnectionString para combinar com a sua cadeia de ligação de serviço e **MyDevice** para combinar com o ID do seu dispositivo se tiver dado um nome diferente ao seu.

1. Navegue para o `Quickstarts/device-streams-service` diretório na sua pasta de projeto desapertado. Utilize o seguinte código para executar a aplicação de procuração local de serviço:

   ```
   cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service

   # Install the preview service SDK, and other dependencies
   npm install azure-iothub@streams-preview
   npm install

   # Run the service-local proxy application
   node proxy.js
   ```

### <a name="ssh-to-your-device-via-device-streams"></a>SSH para o seu dispositivo através de fluxos de dispositivos

Em Linux, executar SSH utilizando `ssh $USER@localhost -p 2222` um terminal. No Windows, utilize o seu cliente SSH favorito (por exemplo, PuTTY).

Saída da consola no serviço local após a sessão SSH (a aplicação de procuração local de serviço ouve na porta 2222):

![Saída do terminal SSH](./media/quickstart-device-streams-proxy-nodejs/service-console-output.png)

Saída da consola da aplicação do cliente SSH (cliente SSH comunica ao daemon SSH ligando-se à porta 22, onde a aplicação de procuração local de serviço está a ouvir):

![Saída do cliente SSH](./media/quickstart-device-streams-proxy-nodejs/ssh-console-output.png)

### <a name="rdp-to-your-device-via-device-streams"></a>RDP para o seu dispositivo através de fluxos de dispositivos

Agora use a sua aplicação de cliente RDP e ligue-se ao representante de serviço na porta 2222, uma porta arbitrária que escolheu anteriormente.

> [!NOTE]
> Certifique-se de que o seu dispositivo está configurado corretamente para RDP e configurado com a porta RDP 3389.

![O cliente RDP conecta-se à aplicação de procuração local de serviço](./media/quickstart-device-streams-proxy-nodejs/rdp-screen-capture.png)

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, criou um hub IoT, registou um dispositivo e implementou uma aplicação de procuração de serviço para permitir o RDP e o SSH num dispositivo IoT. O tráfego RDP e SSH será escavado através de um fluxo de dispositivo através do hub IoT. Este processo elimina a necessidade de conectividade direta com o dispositivo.

Para saber mais sobre os fluxos de dispositivos, consulte:

> [!div class="nextstepaction"]
> [Visão geral dos fluxos de dispositivos](./iot-hub-device-streams-overview.md)
