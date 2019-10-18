---
title: Comunicar-se com um aplicativo de dispositivo no node. js por meio de fluxos de dispositivo do Hub IoT (visualização) | Microsoft Docs
description: Neste guia de início rápido, você executará um aplicativo do lado do serviço node. js que se comunica com um dispositivo IoT por meio de um fluxo de dispositivo.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: c7257ec35f9a53f84edebd5e15b7144c49daf682
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72514950"
---
# <a name="quickstart-communicate-to-a-device-application-in-nodejs-via-iot-hub-device-streams-preview"></a>Início rápido: comunicar-se com um aplicativo de dispositivo no node. js por meio de fluxos de dispositivo do Hub IoT (versão prévia)

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

Para executar o aplicativo do lado do serviço neste guia de início rápido, você precisa do node. js v10. x. x ou posterior em seu computador de desenvolvimento.

Você pode baixar o Node. js para várias plataformas do [NodeJS.org](https://nodejs.org).

Pode verificar qual a versão atual do Node.js no seu computador de desenvolvimento através do seguinte comando:

```cmd/sh
node --version
```

Execute o comando a seguir para adicionar a extensão de IoT Microsoft Azure para CLI do Azure à sua instância de Cloud Shell. A extensão de IOT adiciona comandos de DPS (serviço de provisionamento de dispositivos IOT), IoT Edge e do Hub IoT para CLI do Azure.

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Se ainda não o fez, transfira o projeto Node.js de exemplo do https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip e extraia o arquivo ZIP.

## <a name="create-an-iot-hub"></a>Criar um hub IoT

Se concluiu o anterior [Guia de Início Rápido: Enviar telemetria a partir de um dispositivo para um hub IoT](quickstart-send-telemetry-node.md), pode ignorar este passo.

[!INCLUDE [iot-hub-include-create-hub-device-streams](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Registar um dispositivo

Se concluiu o anterior [Guia de Início Rápido: Enviar telemetria a partir de um dispositivo para um hub IoT](quickstart-send-telemetry-node.md), pode ignorar este passo.

É necessário registar um dispositivo no hub IoT antes de o mesmo se poder ligar. Neste início rápido, vai utilizar o Azure Cloud Shell para registar um dispositivo simulado.

1. Execute o comando a seguir em Azure Cloud Shell para criar a identidade do dispositivo.

   **Nomedoseuhubiot**: Substitua esse espaço reservado abaixo pelo nome escolhido para o Hub IOT.

   **MyDevice**: esse é o nome do dispositivo que você está registrando. É recomendável usar **MyDevice** , conforme mostrado. Se você escolher um nome diferente para seu dispositivo, também precisará usar esse nome em todo este artigo e atualizar o nome do dispositivo nos aplicativos de exemplo antes de executá-los.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

2. Também precisa de uma *cadeia de ligação do serviço* para permitir que a aplicação back-end se ligue ao seu hub IoT e obtenha as mensagens. O seguinte comando obtém a cadeia de ligação do serviço do seu hub IoT:

    **Nomedoseuhubiot**: Substitua esse espaço reservado abaixo pelo nome escolhido para o Hub IOT.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
    ```

    Observe a cadeia de conexão de serviço retornada para uso posterior neste guia de início rápido. O aspeto é igual ao do exemplo abaixo:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-device-and-service-via-device-streams"></a>Comunicação entre o dispositivo e o serviço por meio de fluxos de dispositivo

Nesta seção, você executa o aplicativo do lado do dispositivo e o aplicativo do lado do serviço e se comunica entre os dois.

### <a name="run-the-device-side-application"></a>Executar o aplicativo do lado do dispositivo

Como mencionado anteriormente, o SDK do node. js do Hub IoT dá suporte apenas a fluxos de dispositivo no lado do serviço. Para um aplicativo do lado do dispositivo, use um dos programas de dispositivo que o acompanham disponíveis nestes guias de início rápido:

   * [Comunicar-se com aplicativos de dispositivo em C por meio de fluxos de dispositivo do Hub IoT](./quickstart-device-streams-echo-c.md)

   * [Comunicar-se com aplicativos C# de dispositivo por meio de fluxos de dispositivo do Hub IOT](./quickstart-device-streams-echo-csharp.md)

Verifique se o aplicativo do lado do dispositivo está em execução antes de prosseguir para a próxima etapa.

### <a name="run-the-service-side-application"></a>Executar o aplicativo do lado do serviço

Supondo que o aplicativo do lado do dispositivo esteja em execução, siga as etapas abaixo em uma janela do terminal local para executar o aplicativo do lado do serviço no node. js:

* Forneça suas credenciais de serviço e a ID do dispositivo como variáveis de ambiente.
 
   ```cmd/sh
   # In Linux
   export IOTHUB_CONNECTION_STRING="{ServiceConnectionString}"
   export STREAMING_TARGET_DEVICE="MyDevice"

   # In Windows
   SET IOTHUB_CONNECTION_STRING={ServiceConnectionString}
   SET STREAMING_TARGET_DEVICE=MyDevice
   ```
  
   Altere o espaço reservado de objectconnectionstring para corresponder à cadeia de conexão de serviço e **MyDevice** para corresponder à ID do dispositivo se você tiver atribuído a ele um nome diferente.

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

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você configura um hub IoT, registrou um dispositivo, estabeleceu um fluxo de dispositivo entre aplicativos no lado do dispositivo e do serviço e usou o fluxo para enviar dados entre os aplicativos.

Use os links abaixo para saber mais sobre os fluxos de dispositivo:

> [!div class="nextstepaction"]
> [Visão geral dos fluxos de dispositivo](./iot-hub-device-streams-overview.md) 