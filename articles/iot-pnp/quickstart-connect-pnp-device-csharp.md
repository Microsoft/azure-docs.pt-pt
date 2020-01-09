---
title: Conectar o código do dispositivo de exemplo do IoT Plug and Play preview ao Hub IoT | Microsoft Docs
description: Usando C# o (.net), compile e execute o código do dispositivo de exemplo de visualização de IOT plug and Play que se conecta a um hub IOT. Use a ferramenta Azure IoT Explorer para exibir as informações enviadas pelo dispositivo para o Hub.
author: baanders
ms.author: baanders
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: b32fc103b4ed4d7058c8af42ffa126ee5527f45a
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75550869"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub-c"></a>Início rápido: conectar um aplicativo de dispositivo de visualização de Plug and Play de IoTC#de exemplo ao Hub IOT ()

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

Este guia de início rápido mostra como criar um aplicativo de dispositivo de Plug and Play de IoT de exemplo, conectá-lo ao Hub IoT e usar a ferramenta do Azure IoT Explorer para exibir as informações que ele envia para o Hub. O aplicativo de exemplo é escrito C# em (com .net) e é fornecido como parte da coleção de exemplos de IOT C# do Azure para (.net). Um desenvolvedor de soluções pode usar a ferramenta do Azure IoT Explorer para entender os recursos de um dispositivo de Plug and Play de IoT sem a necessidade de exibir qualquer código de dispositivo.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido, você precisa instalar o .NET Core 2,2 em seu computador de desenvolvimento. Você pode baixar esta versão do SDK do .NET Core para que várias plataformas [Baixem o .NET Core 2,2](https://dotnet.microsoft.com/download/dotnet-core/2.2).

Você pode verificar a versão do .NET que está em seu computador de desenvolvimento executando o seguinte comando em uma janela de terminal local: 

```cmd/sh
dotnet --version
```

### <a name="install-the-azure-iot-explorer"></a>Instalar o Azure IoT Explorer

Baixe e instale a versão mais recente do **Azure IOT Explorer** na página [repositório](https://github.com/Azure/azure-iot-explorer/releases) da ferramenta, selecionando o arquivo. msi em "ativos" para a atualização mais recente.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Execute o seguinte comando para obter a _cadeia de conexão do Hub IOT_ para o Hub (observação para uso posterior):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>Preparar o ambiente de desenvolvimento

Neste guia de início rápido, você prepara um ambiente de desenvolvimento que pode ser usado para clonar e compilar C# os exemplos de IOT do Azure para o (.net).

Abra um prompt de comando no diretório de sua escolha. Execute o seguinte comando para clonar os [exemplos do Azure C# IOT para](https://github.com/Azure-Samples/azure-iot-samples-csharp) o repositório do GitHub (.net) neste local:

```cmd/sh
git clone https://github.com/Azure-Samples/azure-iot-samples-csharp
```

Esta operação pode levar vários minutos para ser concluída.

## <a name="run-the-device-sample"></a>Executar o exemplo de dispositivo

Você usa o código de exemplo clonado para criar um aplicativo que simula um dispositivo que se conecta a um hub IoT. O aplicativo envia telemetria e propriedades e recebe comandos.

1. Em uma janela de terminal local, vá para a pasta do repositório clonado e navegue até a pasta **Azure-IOT-Samples-Csharp/digitaltwin/Samples/Device/EnvironmentalSensorSample** . 

1. Configure a _cadeia de conexão do dispositivo_:

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Execute um aplicativo de exemplo para simular um dispositivo de Plug and Play IoT que envia telemetria para o Hub IoT. Na mesma janela do terminal, para criar os pacotes necessários e executar o aplicativo de exemplo, use o seguinte comando:

    ```cmd\sh
        dotnet run
    ```

Você vê mensagens dizendo que o dispositivo foi registrado com êxito e está aguardando atualizações da nuvem. Isso indica que o dispositivo agora está pronto para receber comandos e atualizações de propriedade e começou a enviar dados de telemetria para o Hub. Mantenha o exemplo em execução enquanto você conclui as próximas etapas.

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Usar o Azure IoT Explorer para validar o código

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. Para garantir que a ferramenta possa ler as definições de modelo de interface do seu dispositivo, selecione **configurações**. No menu configurações, **no dispositivo conectado** pode já aparecer nas configurações de plug and Play; Se não tiver, selecione **+ Adicionar fonte de definição de módulo** e, em seguida, **no dispositivo conectado** para adicioná-lo.

1. De volta à página Visão geral de **dispositivos** , localize a identidade do dispositivo que você criou anteriormente. Com o aplicativo do dispositivo ainda em execução no prompt de comando, verifique se o **estado da conexão** do dispositivo no Azure IOT Explorer está relatando como _conectado_ (caso contrário, pressione **Atualizar** até que ele esteja). Selecione o dispositivo para exibir mais detalhes.

1. Expanda a interface com ID **urn: csharp_sdk_sample: EnvironmentalSensor: 1** para revelar a interface e a IOT plug and Play primitivos — Propriedades, comandos e telemetria.

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você aprendeu como conectar um dispositivo de Plug and Play IoT a um hub IoT. Para saber mais sobre como criar uma solução que interage com seus dispositivos de Plug and Play de IoT, confira:

> [!div class="nextstepaction"]
> [Como: conectar-se e interagir com um dispositivo de visualização de Plug and Play de IoT](howto-develop-solution.md)
