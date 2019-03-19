---
title: Guia de introdução do node. js de fluxos de dispositivos no IoT Hub do Azure para SSH/RDP (pré-visualização) | Documentos da Microsoft
description: Neste início rápido, irá executar uma aplicação de node. js de exemplo que age como um proxy para permitir cenários SSH/RDP sobre fluxos de dispositivo do IoT Hub.
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: rezas
ms.openlocfilehash: a737413f6692b4ee811d0590351a385552cc9a8f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58085580"
---
# <a name="quickstart-sshrdp-over-iot-hub-device-streams-using-nodejs-proxy-application-preview"></a>Início rápido: SSH/RDP sobre fluxos de dispositivo do IoT Hub com node. js do proxy de aplicações (pré-visualização)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Atualmente, o IoT Hub do Microsoft Azure suporta fluxos de dispositivo como uma [funcionalidade de pré-visualização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Fluxos de dispositivo do IoT Hub](./iot-hub-device-streams-overview.md) permitem que os aplicativos de serviço e dispositivo comunicar de forma segura e compatível com firewall. Este guia de início rápido descreve a execução de uma aplicação node. js proxy em execução no lado do serviço para permitir o tráfego RDP e SSH para ser enviado para o dispositivo através de um fluxo de dispositivo. Ver [aqui](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp) para uma descrição geral da configuração. Durante a pré-visualização pública, o SDK node. js só suporta fluxos de dispositivo no lado do serviço. Como resultado, este guia de início rápido abrange apenas as instruções para executar o proxy de serviço local. Deve executar um proxy que acompanha este artigo do dispositivo local que está disponível no [guia de introdução do C](./quickstart-device-streams-proxy-c.md) ou [ C# início rápido](./quickstart-device-streams-proxy-csharp.md) guias.

Descrevemos primeiro a configuração de SSH (utilizar a porta 22). Descrevemos, em seguida, como modificar a configuração de RDP (que utiliza a porta 3389). Uma vez que os fluxos de dispositivo são o aplicativo e independente de protocolo, o mesmo exemplo pode ser modificado para acomodar outros tipos de tráfego de aplicativo de cliente/servidor (normalmente, ao modificar a porta de comunicação).


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.


## <a name="prerequisites"></a>Pré-requisitos

A pré-visualização de fluxos de dispositivo é atualmente suportado apenas para os Hubs IoT criado nas seguintes regiões:

  - **E.U.A. central**
  - **E.U.A. central EUAP**

Para executar a aplicação de serviço local neste guia de introdução tem de node. js v4.x.x ou posterior no seu computador de desenvolvimento.

Pode transferir o Node.js para múltiplas plataformas em [nodejs.org](https://nodejs.org).

Pode verificar qual a versão atual do Node.js no seu computador de desenvolvimento através do seguinte comando:

```
node --version
```

Se ainda não o fez, transfira o projeto Node.js de exemplo do https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip e extraia o arquivo ZIP.


## <a name="create-an-iot-hub"></a>Criar um hub IoT

Se concluiu o anterior [início rápido: Enviar telemetria a partir de um dispositivo para um hub IoT](quickstart-send-telemetry-node.md), pode ignorar este passo.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub-device-streams.md)]


## <a name="register-a-device"></a>Registar um dispositivo

Se concluiu o anterior [início rápido: Enviar telemetria a partir de um dispositivo para um hub IoT](quickstart-send-telemetry-node.md), pode ignorar este passo.

É necessário registar um dispositivo no hub IoT antes de o mesmo se poder ligar. Neste início rápido, vai utilizar o Azure Cloud Shell para registar um dispositivo simulado.

1. Execute os seguintes comandos no Azure Cloud Shell para adicionar a extensão da CLI do Hub IoT e para criar a identidade do dispositivo. 

   **YourIoTHubName**: Substitua este marcador de posição abaixo com o nome que escolheu para o seu hub IoT.

   **MyDevice**: Este é o nome fornecido para o dispositivo registado. Utilize MyDevice, conforme mostrado. Se escolher um nome diferente para o seu dispositivo, também irá precisar de utilizar esse nome através deste artigo, e atualize o nome do dispositivo em aplicações de exemplo antes de as executar.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

2. Também precisa de uma _cadeia de ligação do serviço_ para permitir que a aplicação back-end se ligue ao seu hub IoT e obtenha as mensagens. O seguinte comando obtém a cadeia de ligação do serviço do seu hub IoT:

    **YourIoTHubName**: Substitua este marcador de posição abaixo com o nome que escolheu para o seu hub IoT.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name YourIoTHubName
    ```

    Anote o valor retornado, o que é semelhante a este:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`


## <a name="ssh-to-a-device-via-device-streams"></a>SSH para um dispositivo por meio de fluxos de dispositivo

### <a name="run-the-device-local-proxy"></a>Execute o proxy local do dispositivo

Como mencionado anteriormente, o SDK de node. js do Hub IoT suporta apenas fluxos de dispositivo no lado do serviço. Para a aplicação de dispositivo local, utilize os programas de proxy de dispositivo que acompanha este artigo, disponíveis no [guia de introdução do C](./quickstart-device-streams-proxy-c.md) ou [ C# início rápido](./quickstart-device-streams-proxy-csharp.md) guias. Certifique-se de que o proxy local com o dispositivo está em execução antes de prosseguir para o passo seguinte.


### <a name="run-the-service-local-proxy"></a>Execute o proxy de serviço local

Supondo que o [local do dispositivo proxy](#run-the-device-local-proxy) está em execução, siga os passos abaixo para executar o proxy de serviço local escrito em node. js.

- Forneça as credenciais do serviço, o ID de dispositivo de destino em que o daemon de SSH é executada e o número de porta para o proxy em execução no dispositivo, como variáveis de ambiente.
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
  Altere os valores acima de acordo com sua cadeia de ligação e ID de dispositivo.

- Navegue para `Quickstarts/device-streams-service` na sua pasta do projeto descompactado e executar o proxy de serviço local.
  ```
  cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service

  # Install the preview service SDK, and other dependencies
  npm install azure-iothub@streams-preview
  npm install

  # Run the service-local proxy application
  node proxy.js
  ```

### <a name="ssh-to-your-device-via-device-streams"></a>SSH para o seu dispositivo por meio de fluxos de dispositivo
No Linux, execute através de SSH `ssh $USER@localhost -p 2222` num terminal. No Windows, utilize o cliente SSH favorito (por exemplo, do PuTTY).

Resultado da consola do serviço-local após o estabelecimento de sessão SSH (a escuta de proxy de serviço local na porta 2222): ![Texto alternativo](./media/quickstart-device-streams-proxy-nodejs/service-console-output.PNG "SSH de saída terminal")


Saída do programa de cliente SSH da consola (cliente SSH comunica com o daemon de SSH ao ligar-se a porta 22, em que o proxy de serviço local está a escutar): ![Texto alternativo](./media/quickstart-device-streams-proxy-nodejs/ssh-console-output.PNG "saída do cliente SSH")


### <a name="rdp-to-your-device-via-device-streams"></a>RDP para o seu dispositivo por meio de fluxos de dispositivo

Agora, use o seu programa de cliente RDP e estabelecer ligação ao proxy de serviço na porta 2222 (que era uma porta disponível arbitrária que selecionou anteriormente).

> [!NOTE]
> Certifique-se de que o proxy de dispositivo está configurado corretamente para RDP e configurado com a porta RDP 3389.

![Texto alternativo](./media/quickstart-device-streams-proxy-nodejs/rdp-screen-capture.PNG "estabelece ligação RDP ao proxy de serviço local.")


## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, tenha de configurar um hub IoT, registou um dispositivo e, implementado um programa de proxy de serviço para ativar o RDP e SSH para um dispositivo de IoT. O tráfego RDP e SSH irá ser encapsulado por meio de um fluxo do dispositivo através do IoT Hub. Isso elimina a necessidade de conectividade direta para o dispositivo.

Use os links abaixo para saber mais sobre fluxos de dispositivo:

> [!div class="nextstepaction"]
> [Descrição geral de fluxos de dispositivo](./iot-hub-device-streams-overview.md)
