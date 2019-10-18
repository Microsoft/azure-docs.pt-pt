---
title: Início rápido do node. js de fluxos de dispositivos do Hub IoT do Azure para SSH e RDP (visualização) | Microsoft Docs
description: Neste guia de início rápido, você executa um aplicativo node. js de exemplo que atua como um proxy para habilitar cenários SSH e RDP em fluxos de dispositivo do Hub IoT.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 4a6fd7dd40905a8a81a104c9d6ef22040ff88f15
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72516299"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-nodejs-proxy-application-preview"></a>Início rápido: habilitar SSH e RDP em um fluxo de dispositivo do Hub IoT usando um aplicativo de proxy do node. js (versão prévia)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Microsoft Azure Hub IoT atualmente dá suporte a fluxos de dispositivo como um [recurso de visualização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Os [fluxos de dispositivo do Hub IOT](./iot-hub-device-streams-overview.md) permitem que aplicativos de serviço e de dispositivo se comuniquem de maneira segura e amigável ao firewall. 

Este guia de início rápido descreve a execução de um aplicativo de proxy node. js em execução no lado do serviço para habilitar o tráfego de Secure Shell (SSH) e protocolo RDP (RDP) a ser enviado ao dispositivo por meio de um fluxo de dispositivo. Para obter uma visão geral da configuração, consulte [exemplo de proxy local](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp). 

Durante a visualização pública, o SDK do node. js dá suporte a fluxos de dispositivo somente no lado do serviço. Como resultado, este guia de início rápido aborda instruções para executar apenas o aplicativo de proxy de serviço local. Para executar o aplicativo de proxy de dispositivo local, consulte:  

   * [Habilitar o SSH e o RDP em fluxos de dispositivo do Hub IoT usando um aplicativo de proxy C](./quickstart-device-streams-proxy-c.md)
   * [Habilitar o SSH e o RDP em fluxos de dispositivo do Hub C# IOT usando um aplicativo proxy](./quickstart-device-streams-proxy-csharp.md)

Este artigo descreve a configuração para SSH (usando a porta 22) e descreve como modificar a configuração para RDP (que usa a porta 3389). Como os fluxos de dispositivo são independentes de aplicativo e de protocolo, você pode modificar o mesmo exemplo para acomodar outros tipos de tráfego de aplicativo cliente-servidor, geralmente modificando a porta de comunicação.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Atualmente, há suporte para a visualização de fluxos de dispositivo apenas para os hubs IoT criados nas seguintes regiões:

  * Centro dos E.U.A.
  * EUA Central EUAP

* Para executar o aplicativo de serviço local neste guia de início rápido, você precisa do node. js v10. x. x ou posterior em seu computador de desenvolvimento.
  * Baixe o [node. js](https://nodejs.org) para várias plataformas.
  * Verifique a versão atual do node. js em seu computador de desenvolvimento usando o seguinte comando:

   ```
   node --version
   ```

* Adicione a extensão do Azure IoT para CLI do Azure à instância do Cloud Shell executando o comando a seguir. A extensão de IOT adiciona comandos específicos do serviço de provisionamento de dispositivos IOT, IoT Edge e do Hub IoT ao CLI do Azure.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    ```

* Se você ainda não fez isso, [Baixe o projeto Node. js de exemplo](https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip) e extraia o arquivo zip.

## <a name="create-an-iot-hub"></a>Criar um hub IoT

Se concluiu o anterior [Guia de Início Rápido: Enviar telemetria a partir de um dispositivo para um hub IoT](quickstart-send-telemetry-node.md), pode ignorar este passo.

[!INCLUDE [iot-hub-include-create-hub-device-streams](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Registar um dispositivo

Se você concluiu o [início rápido: enviar telemetria de um dispositivo para um hub IOT](quickstart-send-telemetry-node.md), você pode ignorar esta etapa.

É necessário registar um dispositivo no hub IoT antes de o mesmo se poder ligar. Nesta seção, você usará Azure Cloud Shell para registrar um dispositivo simulado.

1. Para criar a identidade do dispositivo, execute o seguinte comando no Cloud Shell:

   > [!NOTE]
   > * Substitua o espaço reservado *nomedoseuhubiot* pelo nome que você escolheu para o Hub IOT.
   > * Para o nome do dispositivo que você está registrando, é recomendável usar *MyDevice* , conforme mostrado. Se você escolher um nome diferente para seu dispositivo, use esse nome em todo este artigo e atualize o nome do dispositivo nos aplicativos de exemplo antes de executá-los.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Para permitir que o aplicativo de back-end se conecte ao seu hub IoT e recupere as mensagens, você também precisa de uma *cadeia de conexão de serviço*. O comando a seguir recupera a cadeia de caracteres para o Hub IoT:

   > [!NOTE]
   > Substitua o espaço reservado *nomedoseuhubiot* pelo nome que você escolheu para o Hub IOT.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
    ```

   Observe a cadeia de conexão de serviço retornada para uso posterior neste guia de início rápido. O aspeto é igual ao do exemplo abaixo:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="ssh-to-a-device-via-device-streams"></a>SSH para um dispositivo por meio de fluxos de dispositivo

Nesta seção, você estabelece um fluxo de ponta a ponta para encapsular o tráfego SSH.

### <a name="run-the-device-local-proxy-application"></a>Executar o aplicativo de proxy de dispositivo local

Como mencionado anteriormente, o SDK node. js do Hub IoT dá suporte a fluxos de dispositivo somente no lado do serviço. Para o aplicativo de dispositivo local, use um aplicativo de proxy de dispositivo que esteja disponível em um dos seguintes guias de início rápido:

   * [Habilitar o SSH e o RDP em fluxos de dispositivo do Hub IoT usando um aplicativo de proxy C](./quickstart-device-streams-proxy-c.md)
   * [Habilitar o SSH e o RDP em fluxos de dispositivo do Hub C# IOT usando um aplicativo proxy](./quickstart-device-streams-proxy-csharp.md) 

Antes de prosseguir para a próxima etapa, verifique se o aplicativo de proxy local do dispositivo está em execução.

### <a name="run-the-service-local-proxy-application"></a>Executar o aplicativo de proxy local de serviço

Com o aplicativo de proxy de dispositivo local em execução, execute o aplicativo de proxy de serviço local que é gravado no node. js fazendo o seguinte em uma janela de terminal local:

1. Para variáveis de ambiente, forneça suas credenciais de serviço, a ID do dispositivo de destino onde o daemon SSH é executado e o número da porta para o proxy em execução no dispositivo.

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

   Altere o espaço reservado de objectconnectionstring para corresponder à cadeia de conexão de serviço e **MyDevice** para corresponder à ID do dispositivo se você tiver atribuído a ele um nome diferente.

1. Navegue até o diretório `Quickstarts/device-streams-service` em sua pasta de projeto descompactada. Use o código a seguir para executar o aplicativo de proxy de serviço local:

   ```
   cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service

   # Install the preview service SDK, and other dependencies
   npm install azure-iothub@streams-preview
   npm install

   # Run the service-local proxy application
   node proxy.js
   ```

### <a name="ssh-to-your-device-via-device-streams"></a>SSH para seu dispositivo por meio de fluxos de dispositivo

No Linux, execute o SSH usando `ssh $USER@localhost -p 2222` em um terminal. No Windows, use seu cliente SSH favorito (por exemplo, de saída).

Saída do console no serviço-local após a sessão SSH ser estabelecida (o aplicativo proxy local do serviço escuta na porta 2222):

![Saída de terminal SSH](./media/quickstart-device-streams-proxy-nodejs/service-console-output.png)

Saída do console do aplicativo cliente SSH (o cliente SSH se comunica com o daemon SSH conectando-se à porta 22, em que o aplicativo proxy local do serviço está escutando):

![Saída do cliente SSH](./media/quickstart-device-streams-proxy-nodejs/ssh-console-output.png)

### <a name="rdp-to-your-device-via-device-streams"></a>RDP para seu dispositivo por meio de fluxos de dispositivo

Agora, use seu aplicativo cliente RDP e conecte-se ao proxy de serviço na porta 2222, uma porta arbitrária que você escolheu anteriormente.

> [!NOTE]
> Verifique se o proxy do dispositivo está configurado corretamente para RDP e configurado com a porta RDP 3389.

![O cliente RDP se conecta ao aplicativo de proxy local de serviço](./media/quickstart-device-streams-proxy-nodejs/rdp-screen-capture.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você configura um hub IoT, registrou um dispositivo e implantou um aplicativo de proxy de serviço para habilitar o RDP e o SSH em um dispositivo IoT. O tráfego de RDP e SSH será encapsulado por meio de um fluxo de dispositivo por meio do Hub IoT. Esse processo elimina a necessidade de conectividade direta com o dispositivo.

Para saber mais sobre fluxos de dispositivo, consulte:

> [!div class="nextstepaction"]
> [Visão geral dos fluxos de dispositivo](./iot-hub-device-streams-overview.md)
