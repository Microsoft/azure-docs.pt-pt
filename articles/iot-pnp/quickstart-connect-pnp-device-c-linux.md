---
title: Conectar o código do dispositivo de exemplo do IoT Plug and Play preview ao Hub IoT (Linux) | Microsoft Docs
description: Compile e execute o código do dispositivo de exemplo de visualização de IoT Plug and Play no Linux que se conecta a um hub IoT. Use o CLI do Azure para exibir as informações enviadas pelo dispositivo para o Hub.
author: dominicbetts
ms.author: dobett
ms.date: 12/23/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 8134c0a97f6350cfa2cf616695c5990618455393
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/28/2019
ms.locfileid: "75531274"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-running-on-linux-to-iot-hub-c-linux"></a>Início rápido: conectar um aplicativo de dispositivo de visualização de IoT Plug and Play de exemplo em execução no Hub do Linux para IoT (C Linux)

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

Este guia de início rápido mostra como criar um aplicativo de dispositivo de Plug and Play de IoT de exemplo no Linux, conectá-lo ao Bub de IoT e usar o CLI do Azure para exibir as informações que ele envia para o Hub. O aplicativo de exemplo é escrito em C e está incluído no SDK do dispositivo IoT do Azure para C. Um desenvolvedor de soluções pode usar o CLI do Azure para entender os recursos de um dispositivo de Plug and Play IoT sem a necessidade de exibir qualquer código de dispositivo.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Pré-requisitos

Este guia de início rápido pressupõe que você está usando Ubuntu Linux. As etapas neste tutorial foram testadas usando o Ubuntu 18, 4.

Para concluir este guia de início rápido, você precisa instalar o seguinte software em seu computador Linux local:

Instale o **gcc**, **git**, **CMake**e todas as dependências usando o comando `apt-get`:

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

Verifique se a versão do `cmake` está acima de **2.8.12** e se a versão do **gcc** está acima de **4.4.7**.

```sh
cmake --version
gcc --version
```

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

## <a name="prepare-the-development-environment"></a>Preparar o ambiente de desenvolvimento

Neste guia de início rápido, você prepara um ambiente de desenvolvimento que pode ser usado para clonar e criar o SDK do dispositivo C do Hub IoT do Azure.

Abra um prompt de comando no diretório de sua escolha. Execute o seguinte comando para clonar o repositório GitHub de [SDKs e bibliotecas do Azure IOT C](https://github.com/Azure/azure-iot-sdk-c) neste local:

```bash
git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
```

Esta operação deve demorar vários minutos a ser concluída.

## <a name="build-the-code"></a>Compilar o código

Você usa o SDK do dispositivo para criar o código de exemplo incluído. O aplicativo que você cria simula um dispositivo que se conecta a um hub IoT. O aplicativo envia telemetria e propriedades e recebe comandos.

1. Crie um subdiretório `cmake` na pasta raiz do SDK do dispositivo e navegue até essa pasta:

    ```bash
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Execute os seguintes comandos para compilar o SDK do dispositivo e o stub do código gerado:

    ```bash
    cmake ..
    cmake --build .
    ```

## <a name="update-your-model-repository"></a>Atualizar seu repositório de modelos

Antes de executar o exemplo, adicione o modelo de funcionalidade do dispositivo e as definições de interface ao repositório de modelos da empresa:

1. Entre no portal do [portal do Azure Certified para IOT](https://preview.catalog.azureiotsolutions.com) com sua conta corporativa ou de estudante da Microsoft, ou sua ID de parceiro da Microsoft, se você tiver uma.

1. Selecione **repositório da empresa** e, em seguida, **modelos de funcionalidade**.

1. Selecione **novo** e **carregar**.

1. Selecione o arquivo `SampleDevice.capabilitymodel.json` na pasta `digitaltwin_client/samples` na pasta raiz do SDK do dispositivo. Selecione **abrir** e, em seguida, **salvar** para carregar o arquivo de modelo em seu repositório.

1. Selecione **repositório da empresa** e, em seguida, **interfaces**.

1. Selecione **novo** e **carregar**.

1. Selecione o arquivo `EnvironmentalSensor.interface.json` na pasta `digitaltwin_client/samples/digitaltwin_sample_environmental_sensor` na pasta raiz do SDK do dispositivo. Selecione **abrir** e, em seguida, **salvar** para carregar o arquivo de interface em seu repositório.

1. Selecione **repositório da empresa** e **cadeias de conexão**. Anote a primeira _cadeia de conexão do repositório de modelo da empresa_, pois você a usará posteriormente neste guia de início rápido.

## <a name="run-the-device-sample"></a>Executar o exemplo de dispositivo

Execute um aplicativo de exemplo no SDK para simular um dispositivo de Plug and Play IoT que envia a telemetria para o Hub IoT. Para executar o aplicativo de exemplo:

1. Na pasta `cmake`, navegue até a pasta que contém o arquivo executável:

    ```bash
    cd digitaltwin_client/samples/digitaltwin_sample_device
    ```

1. Execute o arquivo executável:

    ```bash
    ./digitaltwin_sample_device "<YourDeviceConnectionString>"
    ```

Agora, o dispositivo está pronto para receber comandos e atualizações de propriedade e começou a enviar dados de telemetria para o Hub. Mantenha o exemplo em execução enquanto você conclui as próximas etapas.

### <a name="use-the-azure-iot-cli-to-validate-the-code"></a>Usar a CLI do Azure IoT para validar o código

Depois que o exemplo de cliente do dispositivo for iniciado, verifique se ele está funcionando com o CLI do Azure.

Use o comando a seguir para exibir a telemetria que o dispositivo de exemplo está enviando. Talvez seja necessário aguardar um ou dois minutos antes de ver qualquer telemetria na saída:

```azurecli-interactive
az iot dt monitor-events --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

Use o seguinte comando para exibir as propriedades enviadas pelo dispositivo:

```azurecli-interactive
az iot dt list-properties --hub-name <YourIoTHubName> --device-id <YourDeviceID> --interface sensor --source private --repo-login "<YourCompanyModelRepositoryConnectionString>"
```
[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você aprendeu como conectar um dispositivo de Plug and Play IoT a um hub IoT. Para saber mais sobre como criar uma solução que interage com seus dispositivos de Plug and Play de IoT, confira:

> [!div class="nextstepaction"]
> [Como: conectar-se e interagir com um dispositivo](howto-develop-solution.md)
