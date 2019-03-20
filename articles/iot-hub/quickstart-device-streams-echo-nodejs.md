---
title: Guia de introdução do node. js (pré-visualização) de fluxos de dispositivos no IoT Hub do Azure | Documentos da Microsoft
description: Neste início rápido, irá executar um aplicativo do lado do serviço node. js que se comunique com um dispositivo de IoT através de um fluxo de dispositivo.
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: rezas
ms.openlocfilehash: 1e7efe28918cafb3fa9547c144be3360768d549c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58079900"
---
# <a name="quickstart-communicate-to-a-device-application-in-nodejs-via-iot-hub-device-streams-preview"></a>Início rápido: Comunicar com um aplicativo de dispositivo node. js através de fluxos de dispositivo do IoT Hub (pré-visualização)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Atualmente, o IoT Hub do Microsoft Azure suporta fluxos de dispositivo como uma [funcionalidade de pré-visualização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Fluxos de dispositivo do IoT Hub](./iot-hub-device-streams-overview.md) permitem que os aplicativos de serviço e dispositivo comunicar de forma segura e compatível com firewall. Durante a pré-visualização pública, o SDK node. js só suporta fluxos de dispositivo no lado do serviço. Como resultado, este início rápido abrange apenas as instruções para executar a aplicação do lado do serviço. Deve executar uma aplicação que acompanha este artigo do lado do dispositivo que está disponível no [guia de introdução do C](./quickstart-device-streams-echo-c.md) ou [ C# início rápido](./quickstart-device-streams-echo-csharp.md) guias.

A aplicação de node. js do lado do serviço neste guia de introdução tem as seguintes funcionalidades:

* Cria um fluxo de dispositivo para um dispositivo de IoT.

* Lê a entrada de linha de comandos e envia-os para o aplicativo de dispositivo, que irá recuperá-lo.

O código demonstra o processo de inicialização de um fluxo de dispositivo, bem como usá-lo para enviar e receber dados.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.


## <a name="prerequisites"></a>Pré-requisitos

A pré-visualização de fluxos de dispositivo é atualmente suportado apenas para os Hubs IoT criado nas seguintes regiões:

  - **E.U.A. central**
  - **E.U.A. central EUAP**

Para executar a aplicação do lado do serviço neste guia de introdução tem de node. js v4.x.x ou posterior no seu computador de desenvolvimento.

Pode baixar o node. js para várias plataformas a partir [Node.js.org](https://nodejs.org).

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


## <a name="communicate-between-device-and-service-via-device-streams"></a>Comunicação entre o serviço por meio de fluxos de dispositivo e dispositivo

### <a name="run-the-device-side-application"></a>Executar a aplicação do lado do dispositivo

Como mencionado anteriormente, o SDK de node. js do Hub IoT suporta apenas fluxos de dispositivo no lado do serviço. Para a aplicação do lado do dispositivo, utilize os programas de dispositivo que acompanha este artigo disponíveis no [guia de introdução do C](./quickstart-device-streams-echo-c.md) ou [ C# início rápido](./quickstart-device-streams-echo-csharp.md) guias. Certifique-se de que a aplicação do lado do dispositivo está em execução antes de prosseguir para o passo seguinte.


### <a name="run-the-service-side-application"></a>Executar a aplicação do lado do serviço

Partindo do princípio de que está a executar a aplicação do lado do dispositivo, siga os passos abaixo para executar a aplicação do lado do serviço em node. js:

- Forneça as suas credenciais de serviço e ID de dispositivo como variáveis de ambiente.
  ```
  # In Linux
  export IOTHUB_CONNECTION_STRING="<provide_your_service_connection_string>"
  export STREAMING_TARGET_DEVICE="MyDevice"

  # In Windows
  SET IOTHUB_CONNECTION_STRING=<provide_your_service_connection_string>
  SET STREAMING_TARGET_DEVICE=MyDevice
  ```
  Alteração `MyDevice` para o ID de dispositivo que escolheu para o seu dispositivo.

- Navegue para `Quickstarts/device-streams-service` sua descompactado na pasta do projeto e execute o exemplo com o nó.
  ```
  cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service
  
  # Install the preview service SDK, and other dependencies
  npm install azure-iothub@streams-preview
  npm install

  node echo.js
  ```

No final do último passo, o programa do lado do serviço irá iniciar um fluxo para o seu dispositivo e depois de estabelecido irá enviar um buffer de cadeia de caracteres para o serviço sobre o fluxo. Neste exemplo, o programa do lado do serviço simplesmente lê o stdin no terminal e envia-os para o dispositivo, que será, em seguida, retorná-lo. Isso demonstra a comunicação bidirecional bem-sucedida entre as duas aplicações.

Saída no lado do serviço da consola: ![texto alternativo](./media/quickstart-device-streams-echo-nodejs/service-console-output.PNG "saída no lado do serviço da consola")


Em seguida, pode terminar o programa pressionando Introduza novamente.


## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]


## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, ter configurar um hub IoT, registou um dispositivo, estabelecer um fluxo de dispositivo entre as aplicações no lado do dispositivo e o serviço e utilizado o fluxo para enviar dados entre os aplicativos.

Use os links abaixo para saber mais sobre fluxos de dispositivo:

> [!div class="nextstepaction"]
> [Descrição geral de fluxos de dispositivo](./iot-hub-device-streams-overview.md)
