---
title: Guia de introdução do node. js de fluxos de dispositivos no IoT Hub do Azure para SSH e RDP (pré-visualização) | Documentos da Microsoft
description: Neste início rápido, irá executar uma aplicação de node. js de exemplo que age como um proxy para ativar cenários RDP e SSH ao longo do IoT Hub fluxos de dispositivo.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 83339273d9161c3947df191d10e788980db39b28
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445978"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-nodejs-proxy-application-preview"></a>Início rápido: Ativar o RDP e SSH ao longo de um fluxo de dispositivo do IoT Hub utilizando uma aplicação de proxy do node. js (pré-visualização)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Atualmente, o IoT Hub do Microsoft Azure suporta fluxos de dispositivo como uma [funcionalidade de pré-visualização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Fluxos de dispositivo do IoT Hub](./iot-hub-device-streams-overview.md) permitem que os aplicativos de serviço e dispositivo comunicar de forma segura e compatível com firewall. 

Este início rápido descreve a execução de um aplicativo de proxy de node. js que está em execução no lado do serviço para ativar o Secure Shell (SSH) e tráfego de protocolo de ambiente de trabalho remoto (RDP) a serem enviados para o dispositivo através de um fluxo de dispositivo. Para uma descrição geral da configuração, consulte [exemplo de Proxy Local](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp). 

Durante a pré-visualização pública, o SDK de node. js suporta fluxos de dispositivo no lado do serviço apenas. Como resultado, este Guia Rápido abrange as instruções para executar apenas a aplicação do proxy de serviço local. Para executar a aplicação do proxy de dispositivo local, consulte:  

   * [Permitir RDP e SSH sobre fluxos de dispositivo do IoT Hub através de uma aplicação de proxy C](./quickstart-device-streams-proxy-c.md)
   * [Permitir RDP e SSH sobre fluxos de dispositivo do IoT Hub através de um C# proxy de aplicações](./quickstart-device-streams-proxy-csharp.md)

Este artigo descreve a configuração de SSH (ao utilizar a porta 22) e, em seguida, descreve como modificar a configuração de RDP (que utiliza a porta 3389). Como os fluxos de dispositivo são aplicações - e indiferente a protocolo, pode modificar o mesmo exemplo para acomodar outros tipos de tráfego de aplicativo de cliente-servidor, normalmente, ao modificar a porta de comunicação.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* A pré-visualização de fluxos de dispositivo é atualmente suportada apenas para os hubs IoT que são criados nas seguintes regiões:

  * EUA Central
  * E.U.A. central EUAP

* Para executar a aplicação de serviço local neste início rápido, terá de node. js v10.x.x ou posterior no seu computador de desenvolvimento.
  * Baixe [node. js](https://nodejs.org) para várias plataformas.
  * Verificar a versão atual do node. js no computador de desenvolvimento com o seguinte comando:

   ```
   node --version
   ```

* Adicione a extensão de IoT do Azure para a CLI do Azure à sua instância do Cloud Shell, executando o seguinte comando. A extensão de IOT adiciona o IoT Hub, o IoT Edge e o serviço aprovisionamento de dispositivos IoT (DPS)-comandos específicos para a CLI do Azure.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    ```

* Se ainda não fez isso, [transfira o projeto de node. js de exemplo](https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip) e extraia o arquivo ZIP.

## <a name="create-an-iot-hub"></a>Criar um hub IoT

Se concluiu o anterior [início rápido: Enviar telemetria a partir de um dispositivo para um hub IoT](quickstart-send-telemetry-node.md), pode ignorar este passo.

[!INCLUDE [iot-hub-include-create-hub-device-streams](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Registar um dispositivo

Se concluiu [início rápido: Enviar telemetria a partir de um dispositivo para um hub IoT](quickstart-send-telemetry-node.md), pode ignorar este passo.

É necessário registar um dispositivo no hub IoT antes de o mesmo se poder ligar. Nesta secção, utilize o Azure Cloud Shell para registar um dispositivo simulado.

1. Para criar a identidade de dispositivo, execute o seguinte comando no Cloud Shell:

   > [!NOTE]
   > * Substitua a *YourIoTHubName* espaço reservado com o nome que escolher para o seu hub IoT.
   > * Uso *MyDevice*, conforme mostrado. É o nome fornecido para o dispositivo registado. Se escolher um nome diferente para o seu dispositivo, utilize esse nome ao longo deste artigo e atualizar o nome do dispositivo em aplicativos de exemplo, antes de gerá-los.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

1. Para ativar a aplicação de back-end para se ligar ao seu hub IoT e obter as mensagens, também tem um *cadeia de ligação de serviço*. O comando seguinte obtém a cadeia de caracteres para o seu hub IoT:

   > [!NOTE]
   > Substitua a *YourIoTHubName* espaço reservado com o nome que escolher para o seu hub IoT.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name YourIoTHubName
    ```

    Tenha em atenção o valor devolvido para utilizar mais tarde neste início rápido. O aspeto é igual ao do exemplo abaixo:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="ssh-to-a-device-via-device-streams"></a>SSH para um dispositivo por meio de fluxos de dispositivo

Nesta secção, estabelecer um fluxo de ponto-a-ponto para encaminhar o tráfego SSH.

### <a name="run-the-device-local-proxy-application"></a>Executar a aplicação de proxy local do dispositivo

Como mencionado anteriormente, o SDK de node. js do Hub IoT suporta fluxos de dispositivo no lado do serviço apenas. Para a aplicação de dispositivo local, utilize uma aplicação de proxy de dispositivo que está disponível dos inícios rápidos:

   * [Permitir RDP e SSH sobre fluxos de dispositivo do IoT Hub através de uma aplicação de proxy C](./quickstart-device-streams-proxy-c.md)
   * [Permitir RDP e SSH sobre fluxos de dispositivo do IoT Hub através de um C# proxy de aplicações](./quickstart-device-streams-proxy-csharp.md) 

Antes de avançar para o próximo passo, certifique-se de que a aplicação do proxy de dispositivo local está em execução.

### <a name="run-the-service-local-proxy-application"></a>Executar a aplicação do proxy de serviço local

Com o dispositivo local do proxy de aplicações em execução, execute a aplicação do proxy de serviço local que é escrita em node. js, fazendo o seguinte:

1. Variáveis de ambiente, forneça as credenciais do serviço, o ID de dispositivo de destino em que o daemon de SSH é executada e o número de porta para o proxy que está a executar no dispositivo.

   ```
   # In Linux
   export IOTHUB_CONNECTION_STRING="<provide_your_service_connection_string>"
   export STREAMING_TARGET_DEVICE="MyDevice"
   export PROXY_PORT=2222

   # In Windows
   SET IOTHUB_CONNECTION_STRING=<provide_your_service_connection_string>
   SET STREAMING_TARGET_DEVICE=MyDevice
   SET PROXY_PORT=2222
   ```

   Altere os valores anteriores para corresponder à sua cadeia de ligação e ID de dispositivo.

1. Vá para o *inícios rápidos/dispositivo-fluxos-serviço* diretório na sua pasta do projeto descompactado e execute a aplicação do proxy de serviço local.

   ```
   cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service

   # Install the preview service SDK, and other dependencies
   npm install azure-iothub@streams-preview
   npm install

   # Run the service-local proxy application
   node proxy.js
   ```

### <a name="ssh-to-your-device-via-device-streams"></a>SSH para o seu dispositivo por meio de fluxos de dispositivo

No Linux, execute o SSH com o `ssh $USER@localhost -p 2222` num terminal. No Windows, utilize o cliente SSH favorito (por exemplo, PuTTY).

Resultado da consola do serviço-local após o estabelecimento de sessão SSH (a escuta de aplicação de proxy de serviço local na porta 2222):

![Saída de terminal SSH](./media/quickstart-device-streams-proxy-nodejs/service-console-output.png)

Saída da aplicação de cliente SSH da consola (cliente SSH comunica com o daemon de SSH ao ligar-se a porta 22, que a aplicação do proxy de serviço local está a escutar):

![Saída do cliente SSH](./media/quickstart-device-streams-proxy-nodejs/ssh-console-output.png)

### <a name="rdp-to-your-device-via-device-streams"></a>RDP para o seu dispositivo por meio de fluxos de dispositivo

Agora, utilize a sua aplicação de cliente RDP e ligar-se para o proxy de serviço na porta 2222, uma porta arbitrária escolhidos anteriormente.

> [!NOTE]
> Certifique-se de que o proxy de dispositivo está configurado corretamente para RDP e configurado com a porta RDP 3389.

![O cliente RDP liga-se para a aplicação do proxy de serviço local](./media/quickstart-device-streams-proxy-nodejs/rdp-screen-capture.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, configurar um hub IoT, registou um dispositivo e implementou uma aplicação de proxy de serviço para ativar o RDP e SSH num dispositivo IoT. O tráfego RDP e SSH irá ser encapsulado por meio de um fluxo do dispositivo através do hub IoT. Este processo elimina a necessidade de conectividade direta para o dispositivo.

Para saber mais sobre fluxos de dispositivo, veja:

> [!div class="nextstepaction"]
> [Descrição geral de fluxos de dispositivo](./iot-hub-device-streams-overview.md)
