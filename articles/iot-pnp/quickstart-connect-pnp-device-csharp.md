---
title: Ligue o código de pré-visualização IoT Plug e Play ao IoT Hub - C# Microsoft Docs
description: Utilizando C# (.NET), construa e execute o código do dispositivo de amostra IoT Plug e Play Preview que se conecta a um hub IoT. Utilize a ferramenta exploradora Azure IoT para visualizar as informações enviadas pelo dispositivo para o hub.
author: dominicbetts
ms.author: dobett
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: d598bbb792c071db281ebdc562402ba0198305ab
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87044194"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub-c"></a>Quickstart: Ligue uma aplicação de dispositivo de pré-visualização IoT Plug e Play Para reproduzir a aplicação do dispositivo IoT Hub (C#)

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

Este quickstart mostra-lhe como construir uma aplicação de dispositivo IoT Plug e Play de amostra, conectá-la ao seu hub IoT e utilizar a ferramenta exploradora Azure IoT para visualizar a informação que envia para o centro. O pedido de amostra é escrito em C# (com.NET), e é fornecido como parte da coleção Azure IoT Samples for C# (.NET). Um desenvolvedor de soluções pode usar a ferramenta exploradorA Azure IoT para entender as capacidades de um dispositivo IoT Plug e Play sem a necessidade de ver qualquer código do dispositivo.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para completar este arranque rápido, tem de instalar .NET Core 3.0 na sua máquina de desenvolvimento. Pode descarregar esta versão do .NET Core SDK para várias plataformas a partir do [Download .NET Core 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0).

Pode verificar a versão de .NET que está na sua máquina de desenvolvimento executando o seguinte comando numa janela de terminal local: 

```cmd/sh
dotnet --version
```

### <a name="install-the-azure-iot-explorer"></a>Instale o explorador Azure IoT

Descarregue e instale o mais recente lançamento do **explorador Azure IoT** a partir da página de [repositório](https://github.com/Azure/azure-iot-explorer/releases) da ferramenta, selecionando o ficheiro .msi em "Ativos" para a mais recente atualização.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Executar o seguinte comando para obter a _cadeia de ligação do hub IoT_ para o seu hub (nota para utilização posterior):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>Preparar o ambiente de desenvolvimento

Neste quickstart, você prepara um ambiente de desenvolvimento que pode usar para clonar e construir as Amostras Azure IoT para C# (.NET).

Abra um pedido de comando no diretório à sua escolha. Execute o seguinte comando para clonar as [amostras Azure IoT para C# (.NET)](https://github.com/Azure-Samples/azure-iot-samples-csharp) GitHub repositório neste local:

```cmd/sh
git clone https://github.com/Azure-Samples/azure-iot-samples-csharp
```

Esta operação pode demorar vários minutos a ser concluída.

## <a name="run-the-device-sample"></a>Executar a amostra do dispositivo

Utiliza-se o código de amostra clonado para construir uma aplicação simulando um dispositivo que se conecta a um hub IoT. A aplicação envia telemetria e propriedades e recebe comandos.

1. Numa janela de terminal local, vá à pasta do seu repositório clonado e navegue para a pasta **azure-iot-samples-csharp/digitaltwin/Samples/device/EnvironmentalSensorSample.** 

1. Configure a _cadeia de ligação_do dispositivo:

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Executar uma aplicação de amostra para simular um dispositivo IoT Plug and Play que envia telemetria para o seu hub IoT. Na mesma janela do terminal, para construir as embalagens necessárias e executar a aplicação da amostra, utilize o seguinte comando:

    ```cmd\sh
    dotnet run --framework=netcoreapp3.0
    ```

Vê mensagens a dizer que o dispositivo registou com sucesso e está à espera de atualizações a partir da nuvem. Isto indica que o dispositivo está agora pronto para receber comandos e atualizações de propriedade, e começou a enviar dados de telemetria para o hub. Mantenha a amostra a funcionar à medida que completar os próximos passos.

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Utilize o explorador Azure IoT para validar o código

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. Para garantir que a ferramenta pode ler as definições do modelo de interface a partir do seu dispositivo, selecione **Definições**. No menu Definições, **o dispositivo ligado** pode já aparecer nas configurações Plug and Play; se não o fizer, **selecione + Adicione a fonte de definição** do módulo e, em seguida, no dispositivo ligado para adicioná-lo. **On the connected device**

1. De volta à página geral dos **Dispositivos,** encontre a identidade do dispositivo que criou anteriormente. Com a aplicação do dispositivo ainda em funcionamento na indicação de comando, verifique se o estado de **Ligação** do dispositivo no explorador IoT do Azure está a reportar como _Conectado_ (se não, acerte **refresh** até que seja). Selecione o dispositivo para ver mais detalhes.

1. Expandir a interface com **urna ID:csharp_sdk_sample:EnvironmentalSensor:1** para revelar a interface e ioT Plug e Play primitivos — propriedades, comandos e telemetria.

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a ligar um dispositivo IoT Plug e Play a um hub IoT. Para saber mais sobre como construir uma solução que interage com os seus dispositivos IoT Plug e Play, consulte:

> [!div class="nextstepaction"]
> [Como fazer: Ligar e interagir com um dispositivo IoT Plug e Play Preview](howto-develop-solution.md)
