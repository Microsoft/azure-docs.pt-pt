---
title: Ligue o código do dispositivo de pré-visualização IoT plug e reprodução para ioT Hub (Linux) [ Microsoft Docs
description: Construa e execute o código do dispositivo de amostra De visualização IoT Plug e Play Preview no Linux que se conecta a um hub IoT. Utilize o Azure CLI para visualizar as informações enviadas pelo dispositivo para o centro.
author: dominicbetts
ms.author: dobett
ms.date: 12/23/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 8134c0a97f6350cfa2cf616695c5990618455393
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "75531274"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-running-on-linux-to-iot-hub-c-linux"></a>Quickstart: Ligue uma aplicação de dispositivo de pré-visualização IoT plug e play de amostra em linha em Linux a IoT Hub (C Linux)

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

Este quickstart mostra-lhe como construir uma aplicação de dispositivo IoT Plug e Play no Linux, conectá-la ao seu bub IoT e utilizar o Azure CLI para ver a informação que envia para o centro. A aplicação da amostra está escrita em C e está incluída no dispositivo Azure IoT SDK para C. Um desenvolvedor de soluções pode usar o Azure CLI para entender as capacidades de um dispositivo IoT Plug and Play sem a necessidade de visualizar qualquer código do dispositivo.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Pré-requisitos

Este quickstart assume que estás a usar o Ubuntu Linux. Os passos deste tutorial foram testados usando Ubuntu 18.04.

Para completar este arranque rápido, precisa de instalar o seguinte software na sua máquina Linux local:

Instale **GCC,** **Git,** **cmake,** e `apt-get` todas as dependências utilizando o comando:

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

Verifique se `cmake` a versão é superior a **2.8.12** e a versão do **CcG** está acima de **4.4.7**.

```sh
cmake --version
gcc --version
```

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

## <a name="prepare-the-development-environment"></a>Preparar o ambiente de desenvolvimento

Neste arranque rápido, você prepara um ambiente de desenvolvimento que você pode usar para clonar e construir o Azure IoT Hub Device C SDK.

Abra um pedido de comando no diretório à sua escolha. Execute o seguinte comando para clonar o cesitório [Azure IoT C SDKs e Bibliotecas](https://github.com/Azure/azure-iot-sdk-c) GitHub neste local:

```bash
git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
```

Esta operação deve demorar vários minutos a ser concluída.

## <a name="build-the-code"></a>Compilar o código

Usa o dispositivo SDK para construir o código de amostra incluído. A aplicação que constrói simula um dispositivo que se conecta a um hub IoT. A aplicação envia telemetria e propriedades e recebe comandos.

1. Crie `cmake` um subdiretório na pasta raiz SDK do dispositivo e navegue para essa pasta:

    ```bash
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Executar os seguintes comandos para construir o dispositivo SDK e o código gerado:

    ```bash
    cmake ..
    cmake --build .
    ```

## <a name="update-your-model-repository"></a>Atualize o seu repositório de modelos

Antes de executar a amostra, adicione o modelo de capacidade do dispositivo e definições de interface ao repositório do modelo da empresa:

1. Inscreva-se no [portal Azure Certified para IoT](https://preview.catalog.azureiotsolutions.com) com a sua conta de trabalho ou escola da Microsoft, ou o seu ID do Microsoft Partner se tiver um.

1. Selecione **repositório da Empresa** e, em seguida, **modelos Capability**.

1. Selecione **Novo** e, em seguida, **carregue**.

1. Selecione `SampleDevice.capabilitymodel.json` o `digitaltwin_client/samples` ficheiro na pasta na pasta SDK do dispositivo. Selecione **Abrir** e, em seguida, **Guardar** para carregar o ficheiro do modelo para o seu repositório.

1. Selecione **repositório da Empresa** e, em seguida, **Interfaces**.

1. Selecione **Novo** e, em seguida, **carregue**.

1. Selecione `EnvironmentalSensor.interface.json` o `digitaltwin_client/samples/digitaltwin_sample_environmental_sensor` ficheiro na pasta na pasta SDK do dispositivo. Selecione **Abrir** e, em seguida, **Guardar** para carregar o ficheiro de interface para o seu repositório.

1. Selecione **repositório da empresa** e, em seguida, **ligações**de cordas . Tome nota da primeira cadeia de _ligação repositório_modelo da empresa , uma vez que a utiliza mais tarde neste arranque rápido.

## <a name="run-the-device-sample"></a>Executar a amostra do dispositivo

Execute uma aplicação de amostra no SDK para simular um dispositivo IoT Plug and Play que envia telemetria para o seu hub IoT. Para executar a aplicação da amostra:

1. A `cmake` partir da pasta, navegue até à pasta que contém o ficheiro executável:

    ```bash
    cd digitaltwin_client/samples/digitaltwin_sample_device
    ```

1. Execute o ficheiro executável:

    ```bash
    ./digitaltwin_sample_device "<YourDeviceConnectionString>"
    ```

O dispositivo está agora pronto para receber comandos e atualizações de propriedade, e começou a enviar dados de telemetria para o centro. Mantenha a amostra a funcionar à medida que completar os próximos passos.

### <a name="use-the-azure-iot-cli-to-validate-the-code"></a>Utilize o CLI Azure IoT para validar o código

Após o início da amostra do cliente do dispositivo, verifique se está a trabalhar com o Azure CLI.

Utilize o seguinte comando para visualizar a telemetria que o dispositivo de amostra está a enviar. Pode ter de esperar um minuto ou dois antes de ver qualquer telemetria na saída:

```azurecli-interactive
az iot dt monitor-events --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

Utilize o seguinte comando para visualizar as propriedades enviadas pelo dispositivo:

```azurecli-interactive
az iot dt list-properties --hub-name <YourIoTHubName> --device-id <YourDeviceID> --interface sensor --source private --repo-login "<YourCompanyModelRepositoryConnectionString>"
```
[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a ligar um dispositivo IoT Plug and Play a um hub IoT. Para saber mais sobre como construir uma solução que interaja com os seus dispositivos IoT Plug and Play, consulte:

> [!div class="nextstepaction"]
> [Como: Ligar e interagir com um dispositivo](howto-develop-solution.md)
