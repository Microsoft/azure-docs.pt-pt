---
title: Ligue o código do dispositivo de pré-visualização IoT plug e reprodução de amostras de pré-visualização ao IoT Hub [ Hub ] Microsoft Docs
description: Utilizando o Node.js, construa e execute o código do dispositivo de amostra IoT Plug e Play Preview que se conecta a um hub IoT. Utilize a ferramenta exploradora Azure IoT para visualizar as informações enviadas pelo dispositivo para o centro.
author: dominicbetts
ms.author: dobett
ms.date: 12/26/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: c5d69ae21cd240b0c68b9694a55de2cf879a1966
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "76964792"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub-nodejs"></a>Quickstart: Ligue uma amostra IoT Plug e reprodução de dispositivo de pré-visualização ao IoT Hub (Node.js)

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

Este quickstart mostra-lhe como construir uma aplicação de dispositivo IoT Plug e Play, conectá-la ao seu hub IoT e utilizar a ferramenta exploradora Azure IoT para visualizar a informação que envia para o centro. A aplicação da amostra está escrita para Node.js e está incluída no Dispositivo Hub Azure IoT SDK para Node.js. Um desenvolvedor de soluções pode usar a ferramenta exploradora Azure IoT para entender as capacidades de um dispositivo IoT Plug and Play sem a necessidade de visualizar qualquer código do dispositivo.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para completar este arranque rápido, precisa de Node.js na sua máquina de desenvolvimento. Pode descarregar a versão mais recente recomendada para várias plataformas a partir de [nodejs.org](https://nodejs.org).

Pode verificar qual a versão atual do Node.js no seu computador de desenvolvimento através do seguinte comando:

```cmd/sh
node --version
```

### <a name="install-the-azure-iot-explorer"></a>Instale o explorador Azure IoT

Descarregue e instale o mais recente lançamento do **explorador Azure IoT** a partir da página de [repositório](https://github.com/Azure/azure-iot-explorer/releases) da ferramenta, selecionando o ficheiro .msi em "Assets" para a mais recente atualização.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Executar o seguinte comando para obter a cadeia de ligação do _hub IoT_ para o seu hub (nota para utilização posterior):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>Preparar o ambiente de desenvolvimento

Neste arranque rápido, você prepara um ambiente de desenvolvimento que você pode usar para clonar e construir o Azure IoT Hub Device SDK para Node.js.

Abra um pedido de comando no diretório à sua escolha. Execute o seguinte comando para clonar o [Microsoft Azure IoT SDK para o repositório Node.js](https://github.com/Azure/azure-iot-sdk-node) GitHub neste local:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node --recursive -b digitaltwins-preview
```

Esta operação pode demorar alguns minutos a ser concluída.

## <a name="install-required-libraries"></a>Instalar bibliotecas necessárias

Usa o dispositivo SDK para construir o código de amostra incluído. A aplicação que constrói simula um dispositivo que se conecta a um hub IoT. A aplicação envia telemetria e propriedades e recebe comandos.

1. Numa janela de terminal local, vá à pasta do seu repositório clonado e navegue até à pasta **/azure-iot-sdk-node/digitaltwins/samples/device/javascript.** Em seguida, executar o seguinte comando para instalar as bibliotecas necessárias:

    ```cmd/sh
    npm install
    ```
1. Configure a cadeia de ligação do _dispositivo:_

    ```cmd/sh
    set DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

## <a name="run-the-sample-device"></a>Executar o dispositivo de amostra

Execute uma aplicação de amostra no SDK para simular um dispositivo IoT Plug and Play que envia telemetria para o seu hub IoT. Para executar a aplicação da amostra, utilize o seguinte comando:

```cmd\sh
    node sample_device.js
```

Vê a seguinte saída, indicando que o dispositivo começou a enviar dados de telemetria para o centro, e está agora pronto para receber comandos e atualizações de propriedade.

   ![Mensagens de confirmação do dispositivo](media/quickstart-connect-pnp-device/device-confirmation-node.png)

 Mantenha a amostra a funcionar à medida que completar os próximos passos.

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Use o explorador Azure IoT para validar o código

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. Para garantir que a ferramenta pode ler as definições do modelo de interface a partir do seu dispositivo, selecione **Definições**. No menu Definições, **o dispositivo conectado** pode já aparecer nas configurações plug and play; se não o fizer, selecione + Adicione a fonte de **definição** do módulo **e, em** seguida, no dispositivo ligado para adicioná-lo.

1. De volta à página de visão geral dos **Dispositivos,** encontre a identidade do dispositivo que criou anteriormente. Com a aplicação do dispositivo ainda em funcionamento no pedido de comando, verifique se o estado de **Ligação** do dispositivo no explorador Azure IoT está a reportar como _Connected_ (se não for, acerte **em Refresh** até estar). Selecione o dispositivo para ver mais detalhes.

1. Expanda a interface com **urna ID:contoso:com:EnvironmentalSensor:1** para revelar a interface e IoT Plug e Reproduzir primitivos - propriedades, comandos e telemetria.

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a ligar um dispositivo IoT Plug and Play a um hub IoT. Para saber mais sobre como construir uma solução que interaja com os seus dispositivos IoT Plug and Play, consulte:

> [!div class="nextstepaction"]
> [Como: Ligar e interagir com um dispositivo de pré-visualização IoT Plug e Play](howto-develop-solution.md)
