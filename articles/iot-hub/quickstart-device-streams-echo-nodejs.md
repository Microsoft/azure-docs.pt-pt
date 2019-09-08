---
title: Comunicar-se com um aplicativo de dispositivo no node. js por meio de fluxos de dispositivo do Hub IoT (visualização) | Microsoft Docs
description: Neste guia de início rápido, você executará aplicativos do lado do serviço node. js que se comunicam com um dispositivo IoT por meio de um fluxo de dispositivo.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: e85f2ea849aca9deeb92da7d7b2381d6c2b1b725
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/08/2019
ms.locfileid: "70802439"
---
# <a name="quickstart-communicate-to-a-device-application-in-nodejs-via-iot-hub-device-streams-preview"></a>Início rápido: Comunicar-se com um aplicativo de dispositivo no node. js por meio de fluxos de dispositivo do Hub IoT (versão prévia)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Microsoft Azure Hub IoT atualmente dá suporte a fluxos de dispositivo como um [recurso de visualização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Os [fluxos de dispositivo do Hub IOT](./iot-hub-device-streams-overview.md) permitem que aplicativos de serviço e de dispositivo se comuniquem de maneira segura e amigável ao firewall. Durante a visualização pública, o SDK do node. js dá suporte apenas a fluxos de dispositivo no lado do serviço. Como resultado, este início rápido só cobre instruções para executar o aplicativo do lado do serviço. Você deve executar um aplicativo do lado do dispositivo que acompanha um dos seguintes guias de início rápido:

* [Comunicar-se com aplicativos de dispositivo em C por meio de fluxos de dispositivo do Hub IoT](./quickstart-device-streams-echo-c.md)

* [Comunicar-se com aplicativos C# de dispositivo por meio de fluxos de dispositivo do Hub IOT](./quickstart-device-streams-echo-csharp.md).

O aplicativo node. js do lado do serviço neste guia de início rápido tem as seguintes funcionalidades:

* Cria um fluxo de dispositivo para um dispositivo IoT.

* Lê a entrada da linha de comando e a envia ao aplicativo do dispositivo, o que irá ecoar de volta.

O código demonstrará o processo de inicialização de um fluxo de dispositivo, além de como usá-lo para enviar e receber dados.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Atualmente, a visualização dos fluxos de dispositivo só tem suporte para hubs IoT criados nas seguintes regiões:

*  **EUA Central**

*  **EUA Central EUAP**

Para executar o aplicativo do lado do serviço neste início rápido, você precisa do node. js v10. x. x ou posterior em seu computador de desenvolvimento.

Você pode baixar o Node. js para várias plataformas do [NodeJS.org](https://nodejs.org).

Pode verificar qual a versão atual do Node.js no seu computador de desenvolvimento através do seguinte comando:

```cmd/sh
node --version
```

Execute o comando a seguir para adicionar a extensão de IoT Microsoft Azure para CLI do Azure à sua instância de Cloud Shell. A extensão de IOT adiciona comandos específicos do serviço de provisionamento de dispositivos IOT, IoT Edge e do Hub IoT a CLI do Azure.

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Se ainda não o fez, transfira o projeto Node.js de exemplo do https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip e extraia o arquivo ZIP.

## <a name="create-an-iot-hub"></a>Criar um hub IoT

Se você concluiu o [início rápido anterior: Enviar telemetria de um dispositivo para um hub](quickstart-send-telemetry-node.md)IOT, você pode ignorar esta etapa.

[!INCLUDE [iot-hub-include-create-hub-device-streams](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Registar um dispositivo

Se você concluiu o [início rápido anterior: Enviar telemetria de um dispositivo para um hub](quickstart-send-telemetry-node.md)IOT, você pode ignorar esta etapa.

É necessário registar um dispositivo no hub IoT antes de o mesmo se poder ligar. Neste início rápido, vai utilizar o Azure Cloud Shell para registar um dispositivo simulado.

1. Execute o comando a seguir em Azure Cloud Shell para criar a identidade do dispositivo.

   **YourIoTHubName**: Substitua esse espaço reservado abaixo pelo nome escolhido para o Hub IoT.

   **MyDevice**: Este é o nome fornecido para o dispositivo registrado. Use MyDevice conforme mostrado. Se escolher um nome diferente para o seu dispositivo, também irá precisar de utilizar esse nome através deste artigo, e atualize o nome do dispositivo em aplicações de exemplo antes de as executar.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

2. Também precisa de uma *cadeia de ligação do serviço* para permitir que a aplicação back-end se ligue ao seu hub IoT e obtenha as mensagens. O seguinte comando obtém a cadeia de ligação do serviço do seu hub IoT:

    **YourIoTHubName**: Substitua esse espaço reservado abaixo pelo nome escolhido para o Hub IoT.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name YourIoTHubName
    ```

    Anote o valor retornado, que tem a seguinte aparência:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-device-and-service-via-device-streams"></a>Comunicação entre o dispositivo e o serviço por meio de fluxos de dispositivo

Nesta seção, você executa o aplicativo do lado do dispositivo e o aplicativo do lado do serviço e se comunica entre os dois.

### <a name="run-the-device-side-application"></a>Executar o aplicativo do lado do dispositivo

Como mencionado anteriormente, o SDK do node. js do Hub IoT dá suporte apenas a fluxos de dispositivo no lado do serviço. Para aplicativo do lado do dispositivo, use o programa de dispositivo que o acompanha disponível em um destes guias de início rápido:

   * [Comunicar-se com aplicativos de dispositivo em C por meio de fluxos de dispositivo do Hub IoT](./quickstart-device-streams-echo-c.md)

   * [Comunicar-se com aplicativos C# de dispositivo por meio de fluxos de dispositivo do Hub IOT](./quickstart-device-streams-echo-csharp.md)

Verifique se o aplicativo do lado do dispositivo está em execução antes de prosseguir para a próxima etapa.

### <a name="run-the-service-side-application"></a>Executar o aplicativo do lado do serviço

Supondo que o aplicativo do lado do dispositivo esteja em execução, siga as etapas abaixo para executar o aplicativo do lado do serviço no node. js:

* Forneça suas credenciais de serviço e a ID do dispositivo como variáveis de ambiente.
 
   ```cmd/sh
   # In Linux
   export IOTHUB_CONNECTION_STRING="<provide_your_service_connection_string>"
   export STREAMING_TARGET_DEVICE="MyDevice"

   # In Windows
   SET IOTHUB_CONNECTION_STRING=<provide_your_service_connection_string>
   SET STREAMING_TARGET_DEVICE=MyDevice
   ```
  
   Altere `MyDevice` para a ID do dispositivo que você escolheu para seu dispositivo.

* Navegue até `Quickstarts/device-streams-service` em sua pasta de projeto descompactada e execute o exemplo usando o nó.

   ```cmd/sh
   cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service
    
   # Install the preview service SDK, and other dependencies
   npm install azure-iothub@streams-preview
   npm install

   node echo.js
   ```

No final da última etapa, o programa do lado do serviço iniciará um fluxo para seu dispositivo e, uma vez estabelecida, enviará um buffer de cadeia de caracteres para o serviço por meio do fluxo. Neste exemplo, o programa do lado do serviço simplesmente lê o `stdin` no terminal e o envia para o dispositivo, o que o ecoa de volta. Isso demonstra a comunicação bidirecional bem-sucedida entre os dois aplicativos.

![Saída do console do lado do serviço](./media/quickstart-device-streams-echo-nodejs/service-console-output.png)

Em seguida, você pode encerrar o programa pressionando Enter novamente.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste guia de início rápido, você configurou um hub IoT, registrou um dispositivo, estabeleceu um fluxo de dispositivo entre aplicativos no lado do dispositivo e do serviço e usou o fluxo para enviar dados entre os aplicativos.

Use os links abaixo para saber mais sobre os fluxos de dispositivo:

> [!div class="nextstepaction"]
> [Visão geral dos fluxos de dispositivo](./iot-hub-device-streams-overview.md) 