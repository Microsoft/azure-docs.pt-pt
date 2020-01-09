---
title: Conectar o código do dispositivo de exemplo do IoT Plug and Play preview ao Hub IoT | Microsoft Docs
description: Usando o Java, compile e execute o código do dispositivo de exemplo de visualização de IoT Plug and Play que se conecta a um hub IoT. Use a ferramenta Azure IoT Explorer para exibir as informações enviadas pelo dispositivo para o Hub.
author: baanders
ms.author: baanders
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: e1454a197620cacaa8c8303e8f36a8d4a87aec00
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/28/2019
ms.locfileid: "75531314"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub-java"></a>Início rápido: conectar um aplicativo de dispositivo de visualização de Plug and Play de IoT de exemplo ao Hub IoT (Java)

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

Este guia de início rápido mostra como criar um aplicativo de dispositivo de Plug and Play de IoT de exemplo, conectá-lo ao Hub IoT e usar a ferramenta do Azure IoT Explorer para exibir as informações que ele envia para o Hub. O aplicativo de exemplo é escrito em Java e é fornecido como parte dos exemplos de IoT do Azure para a coleção Java. Um desenvolvedor de soluções pode usar a ferramenta do Azure IoT Explorer para entender os recursos de um dispositivo de Plug and Play de IoT sem a necessidade de exibir qualquer código de dispositivo.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido, você precisa do Java SE 8 em seu computador de desenvolvimento. Você também precisa instalar o Maven 3.

Para obter detalhes sobre como configurar com eles, consulte [preparar seu ambiente de desenvolvimento](https://github.com/Azure/azure-iot-sdk-java/blob/preview/doc/java-devbox-setup.md) no SDK do dispositivo IOT Microsoft Azure para Java.

### <a name="install-the-azure-iot-explorer"></a>Instalar o Azure IoT Explorer

Baixe e instale a versão mais recente do **Azure IOT Explorer** na página [repositório](https://github.com/Azure/azure-iot-explorer/releases) da ferramenta, selecionando o arquivo. msi em "ativos" para a atualização mais recente.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Execute o seguinte comando para obter a _cadeia de conexão do Hub IOT_ para o Hub (observação para uso posterior):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>Preparar o ambiente de desenvolvimento

Neste guia de início rápido, você prepara um ambiente de desenvolvimento que pode ser usado para clonar e compilar os exemplos de IoT do Azure para Java.

Abra uma janela de terminal no diretório de sua escolha. Execute o seguinte comando para clonar os [exemplos do Azure IOT para](https://github.com/Azure-Samples/azure-iot-samples-java) o repositório do GitHub Java neste local:

```cmd/sh
git clone https://github.com/Azure-Samples/azure-iot-samples-java
```

Esta operação pode levar vários minutos para ser concluída.

## <a name="build-the-code"></a>Compilar o código

Você usa o código de exemplo clonado para criar um aplicativo que simula um dispositivo que se conecta a um hub IoT. O aplicativo envia telemetria e propriedades e recebe comandos.

1. Em uma janela de terminal local, vá para a pasta do repositório clonado e navegue até a pasta **/Azure-IOT-samples-java/Digital-Twin/Samples/Device/JdkSample** Em seguida, execute o seguinte comando para instalar as bibliotecas necessárias e compilar o aplicativo de dispositivo simulado:

    ```cmd/sh
    mvn clean install -DskipTests
    ```

1. Configure a _cadeia de conexão do dispositivo_:

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

## <a name="run-the-device-sample"></a>Executar o exemplo de dispositivo

Execute um aplicativo de exemplo para simular um dispositivo de Plug and Play IoT que envia telemetria para o Hub IoT. Para executar o aplicativo de exemplo, use o seguinte comando:

```cmd\sh
java -jar environmental-sensor-sample\target\environmental-sensor-sample-with-deps.jar
```

Você vê mensagens dizendo que o dispositivo está conectado, executando várias etapas de configuração e aguardando atualizações de serviço, seguidos pelos logs de telemetria. Isso indica que o dispositivo agora está pronto para receber comandos e atualizações de propriedade e começou a enviar dados de telemetria para o Hub. Mantenha o exemplo em execução enquanto você conclui as próximas etapas.

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Usar o Azure IoT Explorer para validar o código

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. Para garantir que a ferramenta possa ler as definições de modelo de interface do seu dispositivo, selecione **configurações**. No menu configurações, **no dispositivo conectado** pode já aparecer nas configurações de plug and Play; Se não tiver, selecione **+ Adicionar fonte de definição de módulo** e, em seguida, **no dispositivo conectado** para adicioná-lo.

1. De volta à página Visão geral de **dispositivos** , localize a identidade do dispositivo que você criou anteriormente. Com o aplicativo do dispositivo ainda em execução no prompt de comando, verifique se o **estado da conexão** do dispositivo no Azure IOT Explorer está relatando como _conectado_ (caso contrário, pressione **Atualizar** até que ele esteja). Selecione o dispositivo para exibir mais detalhes.

1. Expanda a interface com ID **urn: java_sdk_sample: EnvironmentalSensor: 1** para revelar a interface e a IOT plug and Play primitivos — Propriedades, comandos e telemetria.

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você aprendeu como conectar um dispositivo de Plug and Play IoT a um hub IoT. Para saber mais sobre como criar uma solução que interage com seus dispositivos de Plug and Play de IoT, confira:

> [!div class="nextstepaction"]
> [Como: conectar-se e interagir com um dispositivo de visualização de Plug and Play de IoT](howto-develop-solution.md)
