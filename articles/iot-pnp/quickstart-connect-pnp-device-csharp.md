---
title: Ligue o código do dispositivo de pré-visualização IoT plug e reprodução de amostras de pré-visualização ao IoT Hub [ Hub ] Microsoft Docs
description: Utilizando C# (.NET), construa e execute o código do dispositivo de amostra IoT Plug e Play Preview que se conecta a um hub IoT. Utilize a ferramenta exploradora Azure IoT para visualizar as informações enviadas pelo dispositivo para o centro.
author: dominicbetts
ms.author: dobett
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 90d39635ac6302f816f39ca19cc00a39cfbbf850
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77121009"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub-c"></a>Quickstart: Ligue uma amostra IoT Plug e reproduçãoC#de dispositivo de pré-visualização ao IoT Hub ()

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

Este quickstart mostra-lhe como construir uma aplicação de dispositivo IoT Plug e Play, conectá-la ao seu hub IoT e utilizar a ferramenta exploradora Azure IoT para visualizar a informação que envia para o centro. A aplicação da C# amostra é escrita em (com .NET), e é C# fornecida como parte das amostras Azure IoT para (.NET) coleção. Um desenvolvedor de soluções pode usar a ferramenta exploradora Azure IoT para entender as capacidades de um dispositivo IoT Plug and Play sem a necessidade de visualizar qualquer código do dispositivo.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para completar este arranque rápido, é necessário instalar .NET Core 3.0 na sua máquina de desenvolvimento. Pode descarregar esta versão do .NET Core SDK para várias plataformas a partir de [Download .NET Core 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0).

Pode verificar a versão de .NET que está na sua máquina de desenvolvimento executando o seguinte comando numa janela de terminal local: 

```cmd/sh
dotnet --version
```

### <a name="install-the-azure-iot-explorer"></a>Instale o explorador Azure IoT

Descarregue e instale o mais recente lançamento do **explorador Azure IoT** a partir da página de [repositório](https://github.com/Azure/azure-iot-explorer/releases) da ferramenta, selecionando o ficheiro .msi em "Assets" para a mais recente atualização.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Executar o seguinte comando para obter a cadeia de ligação do _hub IoT_ para o seu hub (nota para utilização posterior):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>Preparar o ambiente de desenvolvimento

Neste arranque rápido, você prepara um ambiente de desenvolvimento que você pode usar C# para clonar e construir as amostras Azure IoT para (.NET).

Abra um pedido de comando no diretório à sua escolha. Execute o seguinte comando para clonar as [amostras Azure C# IoT para (.NET)](https://github.com/Azure-Samples/azure-iot-samples-csharp) Repositório GitHub neste local:

```cmd/sh
git clone https://github.com/Azure-Samples/azure-iot-samples-csharp
```

Esta operação pode demorar alguns minutos a ser concluída.

## <a name="run-the-device-sample"></a>Executar a amostra do dispositivo

Usa o código da amostra clonado para construir uma aplicação que simula um dispositivo que se liga a um hub IoT. A aplicação envia telemetria e propriedades e recebe comandos.

1. Numa janela de terminal local, vá à pasta do seu repositório clonado e navegue para a pasta **azure-iot-samples-csharp/digitaltwin/Samples/device/EnvironmentalSensorSample.** 

1. Configure a cadeia de ligação do _dispositivo:_

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Execute uma aplicação de amostra para simular um dispositivo IoT Plug and Play que envia telemetria para o seu hub IoT. Na mesma janela terminal, para construir as embalagens necessárias e executar a aplicação da amostra, utilize o seguinte comando:

    ```cmd\sh
    dotnet run --framework=netcoreapp3.0
    ```

Vê mensagens a dizer que o dispositivo se registou com sucesso e está à espera de atualizações a partir da nuvem. Isto indica que o dispositivo está agora pronto para receber comandos e atualizações de propriedade, e começou a enviar dados de telemetria para o centro. Mantenha a amostra a funcionar à medida que completar os próximos passos.

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Use o explorador Azure IoT para validar o código

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. Para garantir que a ferramenta pode ler as definições do modelo de interface a partir do seu dispositivo, selecione **Definições**. No menu Definições, **o dispositivo conectado** pode já aparecer nas configurações plug and play; se não o fizer, selecione + Adicione a fonte de **definição** do módulo **e, em** seguida, no dispositivo ligado para adicioná-lo.

1. De volta à página de visão geral dos **Dispositivos,** encontre a identidade do dispositivo que criou anteriormente. Com a aplicação do dispositivo ainda em funcionamento no pedido de comando, verifique se o estado de **Ligação** do dispositivo no explorador Azure IoT está a reportar como _Connected_ (se não for, acerte **em Refresh** até estar). Selecione o dispositivo para ver mais detalhes.

1. Expanda a interface com **urna ID:csharp_sdk_sample:EnvironmentalSensor:1** para revelar a interface e ioT Plug e Reproduzir primitivos - propriedades, comandos e telemetria.

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a ligar um dispositivo IoT Plug and Play a um hub IoT. Para saber mais sobre como construir uma solução que interaja com os seus dispositivos IoT Plug and Play, consulte:

> [!div class="nextstepaction"]
> [Como: Ligar e interagir com um dispositivo de pré-visualização IoT Plug e Play](howto-develop-solution.md)
