---
title: Conectar o código do dispositivo de exemplo do IoT Plug and Play preview ao Hub IoT (Linux) | Microsoft Docs
description: Compile e execute o código do dispositivo de exemplo de visualização de IoT Plug and Play no Linux que se conecta a um hub IoT. Use o CLI do Azure para exibir as informações enviadas pelo dispositivo para o Hub.
author: dominicbetts
ms.author: dobett
ms.date: 09/10/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 38a6deb8d021c5e6a20d765cc7aee5b86042f557
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73586671"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-running-on-linux-to-iot-hub"></a>Início rápido: conectar um aplicativo de dispositivo de visualização de IoT Plug and Play de exemplo em execução no Hub do Linux para IoT

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

## <a name="prepare-an-iot-hub"></a>Preparar um hub IoT

Você também precisa de um hub IoT do Azure em sua assinatura do Azure para concluir este guia de início rápido. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

> [!IMPORTANT]
> Durante a visualização pública, os recursos de Plug and Play de IoT só estão disponíveis em hubs IoT criados nas regiões **EUA Central**, **Europa setentrional**e **leste do Japão** .

Se você estiver usando o CLI do Azure localmente, a versão `az` deverá ser **2.0.73** ou posterior, a Azure cloud Shell usará a versão mais recente. Use o comando `az --version` para verificar a versão instalada em seu computador.

Adicione a extensão Microsoft Azure IoT para CLI do Azure:

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

As etapas neste início rápido exigem a versão **0.8.5** ou posterior da extensão. Use o comando `az extension list` para verificar a versão que você instalou e o comando `az extension update` para atualizar, se necessário.

Se você estiver usando a CLI localmente, entre em sua assinatura do Azure com o seguinte comando:

```bash
az login
```

Se você estiver usando Azure Cloud Shell, você já está conectado automaticamente.

Se você não tiver um hub IoT, siga [estas instruções para criar um](../iot-hub/iot-hub-create-using-cli.md). Durante a visualização pública, a IoT Plug and Play está disponível nas regiões Europa Setentrional, EUA Central e leste do Japão. Certifique-se de criar o Hub em uma dessas regiões.

Execute o comando a seguir para criar a identidade do dispositivo em seu hub IoT. Substitua o espaço reservado **nomedoseuhubiot** pelo nome do seu hub IOT real:

```azurecli-interactive
az iot hub device-identity create --hub-name [YourIoTHubName] --device-id mydevice
```

Execute os seguintes comandos para obter a _cadeia de conexão do dispositivo_ para o dispositivo que você acabou de registrar:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name [YourIoTHubName] --device-id mydevice --output table
```

## <a name="prepare-the-development-environment"></a>Preparar o ambiente de desenvolvimento

Neste guia de início rápido, você prepara um ambiente de desenvolvimento que pode ser usado para clonar e compilar o SDK do dispositivo do Azure IoT C.

Abra uma linha de comandos. Execute o seguinte comando para clonar o [SDK C do Azure IoT](https://github.com/Azure/azure-iot-sdk-c) no repositório do GitHub:

```bash
git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
```

Esse comando leva vários minutos para ser concluído.

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

1. Selecione **repositório da empresa** e **cadeias de conexão**. Anote a primeira cadeia de conexão do repositório de modelos da empresa, você a usará posteriormente neste guia de início rápido.

## <a name="run-the-sample"></a>Executar o exemplo

Execute um aplicativo de exemplo no SDK para simular um dispositivo de Plug and Play IoT que envia a telemetria para o Hub IoT. Para executar o aplicativo de exemplo:

1. Na pasta `cmake`, navegue até a pasta que contém o arquivo executável:

    ```bash
    cd digitaltwin_client/samples/digitaltwin_sample_device
    ```

1. Execute o arquivo executável:

    ```bash
    ./digitaltwin_sample_device "{your device connection string}"
    ```

O dispositivo simulado inicia o envio de telemetria, a escuta de comandos e a escuta de atualizações de propriedade.

### <a name="use-the-azure-iot-cli-to-validate-the-code"></a>Usar a CLI do Azure IoT para validar o código

Depois que o exemplo de cliente do dispositivo for iniciado, verifique se ele está funcionando com o CLI do Azure.

Use o comando a seguir para exibir a telemetria que o dispositivo de exemplo está enviando. Talvez seja necessário aguardar um ou dois minutos antes de ver qualquer telemetria na saída:

```azurecli-interactive
az iot dt monitor-events --hub-name {your IoT hub} --device-id mydevice
```

Use o seguinte comando para exibir as propriedades enviadas pelo dispositivo:

```azurecli-interactive
az iot dt list-properties --hub-name {your IoT hub} --device-id mydevice --interface sensor --source private --repo-login "{your company model repository connection string}"
```

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você aprendeu como conectar um dispositivo de Plug and Play IoT a um hub IoT. Para saber mais sobre como criar uma solução que interage com seus dispositivos de Plug and Play de IoT, confira:

> [!div class="nextstepaction"]
> [Como: conectar-se e interagir com um dispositivo](howto-develop-solution.md)
