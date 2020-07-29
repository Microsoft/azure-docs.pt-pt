---
title: Ligue o código IoT Plug e Play Preview ao IoT Hub - Java Microsoft Docs
description: Utilizando o código do dispositivo de amostra IoT Plug e Play Preview, que se conecta a um hub IoT. Utilize a ferramenta exploradora Azure IoT para visualizar as informações enviadas pelo dispositivo para o hub.
author: dominicbetts
ms.author: dobett
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc, devx-track-java
ms.openlocfilehash: 6400f36c12c8c579bcd019711242dd4edb8449b7
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87321025"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub-java"></a>Quickstart: Ligue uma aplicação de dispositivo de pré-visualização IoT Plug e Play Para reproduzir o IoT Hub (Java)

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

Este quickstart mostra-lhe como construir uma aplicação de dispositivo IoT Plug e Play de amostra, conectá-la ao seu hub IoT e utilizar a ferramenta exploradora Azure IoT para visualizar a informação que envia para o centro. O pedido de amostra é escrito em Java, e é fornecido como parte das amostras Azure IoT para a colheita de Java. Um desenvolvedor de soluções pode usar a ferramenta exploradorA Azure IoT para entender as capacidades de um dispositivo IoT Plug e Play sem a necessidade de ver qualquer código do dispositivo.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para completar este arranque rápido, precisa de Java SE 8 na sua máquina de desenvolvimento. Também precisa instalar o Maven 3.

Para obter detalhes sobre como ser configurado com estes, consulte [Prepare o seu ambiente de desenvolvimento](https://github.com/Azure/azure-iot-sdk-java/blob/preview/doc/java-devbox-setup.md) no dispositivo IoT da Microsoft Azure SDK para Java.

### <a name="install-the-azure-iot-explorer"></a>Instale o explorador Azure IoT

Descarregue e instale o mais recente lançamento do **explorador Azure IoT** a partir da página de [repositório](https://github.com/Azure/azure-iot-explorer/releases) da ferramenta, selecionando o ficheiro .msi em "Ativos" para a mais recente atualização.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Executar o seguinte comando para obter a _cadeia de ligação do hub IoT_ para o seu hub (nota para utilização posterior):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>Preparar o ambiente de desenvolvimento

Neste quickstart, você prepara um ambiente de desenvolvimento que você pode usar para clonar e construir as amostras de IoT Azure para Java.

Abra uma janela terminal no diretório à sua escolha. Execute o seguinte comando para clonar as [amostras Azure IoT para o](https://github.com/Azure-Samples/azure-iot-samples-java) repositório Java GitHub neste local:

```cmd/sh
git clone https://github.com/Azure-Samples/azure-iot-samples-java
```

Esta operação pode demorar vários minutos a ser concluída.

## <a name="build-the-code"></a>Compilar o código

Utiliza-se o código de amostra clonado para construir uma aplicação simulando um dispositivo que se conecta a um hub IoT. A aplicação envia telemetria e propriedades e recebe comandos.

1. Numa janela de terminal local, vá à pasta do seu repositório clonado e navegue para a pasta **/azure-iot-samples-java/digital-twin/samples/device/JdkSample.** Em seguida, executar o seguinte comando para instalar as bibliotecas necessárias e construir a aplicação do dispositivo simulado:

    ```cmd/sh
    mvn clean install -DskipTests
    ```

1. Configure a _cadeia de ligação_do dispositivo:

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

## <a name="run-the-device-sample"></a>Executar a amostra do dispositivo

Executar uma aplicação de amostra para simular um dispositivo IoT Plug and Play que envia telemetria para o seu hub IoT. Para executar a aplicação da amostra, utilize o seguinte comando:

```cmd\sh
java -jar environmental-sensor-sample\target\environmental-sensor-sample-with-deps.jar
```

Vê mensagens a dizer que o dispositivo está ligado, a executar vários passos de configuração e à espera de atualizações de serviço, seguidos de registos de telemetria. Isto indica que o dispositivo está agora pronto para receber comandos e atualizações de propriedade, e começou a enviar dados de telemetria para o hub. Mantenha a amostra a funcionar à medida que completar os próximos passos.

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Utilize o explorador Azure IoT para validar o código

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. Para garantir que a ferramenta pode ler as definições do modelo de interface a partir do seu dispositivo, selecione **Definições**. No menu Definições, **o dispositivo ligado** pode já aparecer nas configurações Plug and Play; se não o fizer, **selecione + Adicione a fonte de definição** do módulo e, em seguida, no dispositivo ligado para adicioná-lo. **On the connected device**

1. De volta à página geral dos **Dispositivos,** encontre a identidade do dispositivo que criou anteriormente. Com a aplicação do dispositivo ainda em funcionamento na indicação de comando, verifique se o estado de **Ligação** do dispositivo no explorador IoT do Azure está a reportar como _Conectado_ (se não, acerte **refresh** até que seja). Selecione o dispositivo para ver mais detalhes.

1. Expandir a interface com **urna ID:java_sdk_sample:EnvironmentalSensor:1** para revelar a interface e ioT Plug e Play primitivos — propriedades, comandos e telemetria.

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a ligar um dispositivo IoT Plug e Play a um hub IoT. Para saber mais sobre como construir uma solução que interage com os seus dispositivos IoT Plug e Play, consulte:

> [!div class="nextstepaction"]
> [Como fazer: Ligar e interagir com um dispositivo IoT Plug e Play Preview](howto-develop-solution.md)
