---
title: Conecte o código do dispositivo de amostra IoT Plug e reprodução de pré-visualização para o IoT Hub (Windows) [ Microsoft Docs
description: Construa e execute o código de dispositivo de amostra IoT Plug e Play Preview no Windows que se conecta a um hub IoT. Utilize a ferramenta exploradora Azure IoT para visualizar as informações enviadas pelo dispositivo para o centro.
author: Philmea
ms.author: philmea
ms.date: 12/26/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 080820024db1302ff5a841761428442396b90040
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769856"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-running-on-windows-to-iot-hub-c-windows"></a>Quickstart: Ligue uma aplicação de dispositivo de pré-visualização IoT da amostra e reprodução em execução no Windows para IoT Hub (C Windows)

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

Este quickstart mostra-lhe como construir uma aplicação de dispositivo IoT Plug e Play, conectá-la ao seu hub IoT e utilizar a ferramenta exploradora Azure IoT para visualizar a informação que envia para o centro. A aplicação da amostra está escrita em C e está incluída no Dispositivo Hub Azure IoT C SDK. Um desenvolvedor de soluções pode usar a ferramenta exploradora Azure IoT para entender as capacidades de um dispositivo IoT Plug and Play sem a necessidade de visualizar qualquer código do dispositivo.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para completar este arranque rápido, é necessário instalar o seguinte software na sua máquina local:

* [Visual Studio (Comunidade, Profissional ou Empresa)](https://visualstudio.microsoft.com/downloads/) - certifique-se de que inclui o componente de gestor de **pacotes NuGet** e o Desenvolvimento do Ambiente de Trabalho com carga de trabalho **C++** quando instalar o Estúdio Visual.
* [Git.](https://git-scm.com/download/)
* [Cmake.](https://cmake.org/download/)

### <a name="install-the-azure-iot-explorer"></a>Instale o explorador Azure IoT

Descarregue e instale o mais recente lançamento do **explorador Azure IoT** a partir da página de [repositório](https://github.com/Azure/azure-iot-explorer/releases) da ferramenta, selecionando o ficheiro .msi em "Assets" para a mais recente atualização.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Executar o seguinte comando para obter a cadeia de ligação do _hub IoT_ para o seu hub (nota para utilização posterior):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>Preparar o ambiente de desenvolvimento

Neste arranque rápido, você prepara um ambiente de desenvolvimento que você pode usar para clonar e construir o Azure IoT Hub Device C SDK.

Abra um pedido de comando no diretório à sua escolha. Execute o seguinte comando para clonar o cesitório [Azure IoT C SDKs e Bibliotecas](https://github.com/Azure/azure-iot-sdk-c) GitHub neste local:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
```

Esta operação deve demorar vários minutos a ser concluída.

## <a name="build-the-code"></a>Compilar o código

Usa o dispositivo SDK para construir o código de amostra incluído. A aplicação que constrói simula um dispositivo que se conecta a um hub IoT. A aplicação envia telemetria e propriedades e recebe comandos.

1. Crie `cmake` um subdiretório na pasta raiz SDK do dispositivo e navegue para essa pasta:

    ```cmd\sh
    cd <root folder>\azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Executar os seguintes comandos para construir o dispositivo SDK e o código gerado:

    ```cmd\sh
    cmake ..
    cmake --build . -- /m /p:Configuration=Release
    ```

    > [!NOTE]
    > Se o cmake não conseguir encontrar o seu compilador C++, obtém erros de construção quando executa o comando anterior. Se isso acontecer, tente executar este comando no comando do [Estúdio Visual.](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs)

## <a name="run-the-device-sample"></a>Executar a amostra do dispositivo

Execute uma aplicação de amostra no SDK para simular um dispositivo IoT Plug and Play que envia telemetria para o seu hub IoT. Para executar a aplicação da amostra, utilize estes comandos e passe a cadeia de _ligação_ do dispositivo como parâmetro.

```cmd\sh
cd digitaltwin_client\samples\digitaltwin_sample_device\Release
copy ..\EnvironmentalSensor.interface.json .
digitaltwin_sample_device.exe "<YourDeviceConnectionString>"
```

O dispositivo está agora pronto para receber comandos e atualizações de propriedade, e começou a enviar dados de telemetria para o centro. Mantenha a amostra a funcionar à medida que completar os próximos passos.

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Use o explorador Azure IoT para validar o código

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. Para garantir que a ferramenta pode ler as definições do modelo de interface a partir do seu dispositivo, selecione **Definições**. No menu Definições, **o dispositivo conectado** pode já aparecer nas configurações plug and play; se não o fizer, selecione + Adicione a fonte de **definição** do módulo **e, em** seguida, no dispositivo ligado para adicioná-lo.

1. De volta à página de visão geral dos **Dispositivos,** encontre a identidade do dispositivo que criou anteriormente. Com a aplicação do dispositivo ainda em funcionamento no pedido de comando, verifique se o estado de **Ligação** do dispositivo no explorador Azure IoT está a reportar como _Connected_ (se não for, acerte **em Refresh** até estar). Selecione o dispositivo para ver mais detalhes.

1. Expanda a interface com **urna ID:YOUR_COMPANY_NAME_HERE:EnvironmentalSensor:1** para revelar a interface e ioT Plug e Reproduzir primitivos - propriedades, comandos e telemetria.

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a ligar um dispositivo IoT Plug and Play a um hub IoT. Para saber mais sobre como construir uma solução que interaja com os seus dispositivos IoT Plug and Play, consulte:

> [!div class="nextstepaction"]
> [Como: Ligar e interagir com um dispositivo de pré-visualização IoT Plug e Play](howto-develop-solution.md)
