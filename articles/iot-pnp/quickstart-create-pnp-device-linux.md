---
title: Criar um dispositivo de visualização do Azure IoT Plug and Play (Linux) | Microsoft Docs
description: Use um modelo de funcionalidade de dispositivo para gerar o código do dispositivo. Em seguida, execute o código do dispositivo e veja o dispositivo conectar-se ao Hub IoT.
author: dominicbetts
ms.author: dobett
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: d2cc440572d6f33480972c15f5c498cc384cb2e3
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75550490"
---
# <a name="quickstart-use-a-device-capability-model-to-create-an-iot-plug-and-play-preview-device-linux"></a>Início rápido: usar um modelo de capacidade de dispositivo para criar um dispositivo de visualização de Plug and Play IoT (Linux)

[!INCLUDE [iot-pnp-quickstarts-1-selector.md](../../includes/iot-pnp-quickstarts-1-selector.md)]

Um DCM ( _modelo de funcionalidade de dispositivo_ ) descreve os recursos de um dispositivo de plug and Play IOT. Um DCM geralmente é associado a um SKU de produto. Os recursos definidos no DCM são organizados em interfaces reutilizáveis. Você pode gerar um esqueleto de código de dispositivo de um DCM. Este guia de início rápido mostra como usar VS Code em Ubuntu Linux para criar um dispositivo de Plug and Play de IoT usando um DCM.

## <a name="prerequisites"></a>Pré-requisitos

Este guia de início rápido pressupõe que você está usando Ubuntu Linux com um ambiente de área de trabalho. As etapas neste tutorial foram testadas usando o Ubuntu 18, 4.

Para concluir este guia de início rápido, você precisa instalar o seguinte software em seu computador Linux local:

* Instale o **gcc**, **git**, **CMake**e todas as dependências usando o comando `apt-get`:

    ```sh
    sudo apt-get update
    sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
    ```

    Verifique se a versão do `cmake` está acima de **2.8.12** e se a versão do **gcc** está acima de **4.4.7**.

    ```sh
    cmake --version
    gcc --version
    ```

* [Visual Studio Code](https://code.visualstudio.com/).

### <a name="install-azure-iot-tools"></a>Instalar as ferramentas do Azure IoT

Use as etapas a seguir para instalar as [Ferramentas do Azure IOT para](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) o pacote de extensão vs Code:

1. Em VS Code, selecione a guia **extensões** .
1. Pesquise pelas **Ferramentas do Azure IOT**.
1. Selecione **Instalar**.

### <a name="get-the-connection-string-for-your-company-model-repository"></a>Obter a cadeia de conexão para o repositório de modelos da empresa

Você pode encontrar a _cadeia de conexão do repositório de modelos da empresa_ no portal do [portal do Azure Certified para IOT](https://preview.catalog.azureiotsolutions.com) ao entrar com uma conta corporativa ou de estudante da Microsoft, ou sua ID de parceiro da Microsoft, se você tiver uma. Depois de entrar, selecione **repositório da empresa** e, em seguida, **cadeias de conexão**.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

## <a name="prepare-the-development-environment"></a>Preparar o ambiente de desenvolvimento

Neste guia de início rápido, você usa o Gerenciador de biblioteca do [Vcpkg](https://github.com/microsoft/vcpkg) para instalar o SDK do dispositivo do Azure IOT C em seu ambiente de desenvolvimento.

Abra uma shell. Execute o seguinte comando para instalar o Vcpkg:

```bash
cd ~
git clone https://github.com/microsoft/vcpkg
cd vcpkg
./bootstrap-vcpkg.sh
./vcpkg install azure-iot-sdk-c[public-preview,use_prov_client]
```

Esta operação deve demorar vários minutos a ser concluída.

## <a name="author-your-model"></a>Crie seu modelo

Neste guia de início rápido, você usa um modelo de funcionalidade de dispositivo de exemplo existente e as interfaces associadas.

1. Crie um diretório `pnp_app` na unidade local. Você usa essa pasta para os arquivos de modelo de dispositivo e o stub de código de dispositivo.

    ```bash
    cd ~
    mkdir pnp_app
    ```

1. Baixe o modelo de funcionalidade do dispositivo e os arquivos de exemplo de interface para a pasta `pnp_app`.

    ```bash
    cd pnp_app
    curl -O -L https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/SampleDevice.capabilitymodel.json
    curl -O -L https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensor.interface.json
    ```

1. Abra `pnp_app` pasta com VS Code. Você pode exibir os arquivos com o IntelliSense:

    ![Modelo de funcionalidade do dispositivo](media/quickstart-create-pnp-device-linux/dcm.png)

1. Nos arquivos que você baixou, substitua `<YOUR_COMPANY_NAME_HERE>` nos campos `@id` e `schema` com um valor exclusivo. Use somente os caracteres a-z, A-Z, 0-9 e sublinhado. Para obter mais informações, consulte [formato do identificador de entrelaça digital](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format).

## <a name="generate-the-c-code-stub"></a>Gerar o stub do código C

Agora que você tem um DCM e suas interfaces associadas, você pode gerar o código do dispositivo que implementa o modelo. Para gerar o stub do código C no VS Code:

1. Com a pasta `pnp_app` aberta no VS Code, use **Ctrl + Shift + P** para abrir a paleta de comandos, insira **plug and Play de IOT**e selecione **gerar stub de código de dispositivo**.

    > [!NOTE]
    > Na primeira vez que você usar o utilitário gerador de código de Plug and Play IoT, levará alguns segundos para baixar e instalar automaticamente.

1. Escolha o arquivo **SampleDevice. capabilitymodel. JSON** a ser usado para gerar o stub do código de dispositivo.

1. Insira o nome do projeto **sample_device**. Esse será o nome do aplicativo do dispositivo.

1. Escolha **ANSI C** como seu idioma.

1. Escolha **por meio da cadeia de conexão do dispositivo Hub IOT** como método de conexão.

1. Escolha **projeto CMake no Linux** como seu modelo de projeto.

1. Escolha **por meio de Vcpkg** como a maneira de incluir o SDK do dispositivo.

1. Uma nova pasta chamada **sample_device** é criada no mesmo local que o arquivo DCM e, nele, são os arquivos stub de código de dispositivo gerados. VS Code abre uma nova janela para exibi-las.
    ![o código do dispositivo](media/quickstart-create-pnp-device-linux/device-code.png)

## <a name="build-and-run-the-code"></a>Compilar e executar o código

Você usa o código-fonte do SDK do dispositivo para criar o stub do código de dispositivo gerado. O aplicativo que você cria simula um dispositivo que se conecta a um hub IoT. O aplicativo envia telemetria e propriedades e recebe comandos.

1. Crie uma pasta de Build do **CMake** para o aplicativo **sample_device** :

    ```bash
    cd ~/pnp_app/sample_device
    mkdir cmake
    cd cmake
    ```

1. Execute CMake para compilar seu aplicativo com o SDK. O comando a seguir pressupõe que você instalou o **vcpkg** em sua pasta base:

    ```bash
    cmake .. -DCMAKE_TOOLCHAIN_FILE=~/vcpkg/scripts/buildsystems/vcpkg.cmake -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON
    cmake --build .
    ```

1. Depois que a compilação for concluída com êxito, execute o aplicativo passando a cadeia de conexão do dispositivo do Hub IoT como parâmetro.

    ```sh
    cd ~/pnp_app/sample_device/cmake
    ./sample_device "<YourDeviceConnectionString>"
    ```

1. O aplicativo do dispositivo começa a enviar dados para o Hub IoT.

    ![Aplicativo de dispositivo em execução](media/quickstart-create-pnp-device-linux/device-app-running.png)

## <a name="validate-the-code"></a>Validar o código

### <a name="publish-device-model-files-to-model-repository"></a>Publicar arquivos de modelo de dispositivo no repositório de modelos

Para validar o código do dispositivo com a CLI **AZ** , você precisa publicar os arquivos no repositório de modelos.

1. Com a pasta `pnp_app` aberta no VS Code, use **Ctrl + Shift + P** para abrir a paleta de comandos, digite e selecione **plug-in IOT & Play: enviar arquivos para o repositório de modelos**.

1. Selecione `SampleDevice.capabilitymodel.json` e `EnvironmentalSensor.interface.json` arquivos.

1. Insira a cadeia de conexão do repositório de modelos da empresa.

    > [!NOTE]
    > A cadeia de conexão só é necessária na primeira vez que você se conecta ao repositório.

1. Em VS Code janela de saída e notificação, você pode verificar se os arquivos foram publicados com êxito.

    > [!NOTE]
    > Se você receber erros ao publicar os arquivos de modelo do dispositivo, poderá tentar usar o comando **IoT plug and Play: sair do repositório de modelos** para sair e passar pelas etapas novamente.

### <a name="use-the-azure-iot-cli-to-validate-the-code"></a>Usar a CLI do Azure IoT para validar o código

Depois que o exemplo de cliente do dispositivo for iniciado, você poderá verificar se ele está funcionando com o CLI do Azure.

Use o comando a seguir para exibir a telemetria que o dispositivo de exemplo está enviando. Talvez seja necessário aguardar um ou dois minutos antes de ver qualquer telemetria na saída:

```azurecli-interactive
az iot dt monitor-events --hub-name <YourIoTHubNme> --device-id <YourDeviceID>
```

Use o seguinte comando para exibir todas as propriedades enviadas pelo dispositivo:

```azurecli-interactive
az iot dt list-properties --device-id <YourDeviceID> --hub-name <YourIoTHubNme> --source private --repo-login "<YourCompanyModelRepositoryConnectionString>"
```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você aprendeu a criar um dispositivo de Plug and Play de IoT usando um DCM.

Para saber mais sobre o DCMs e como criar seus próprios modelos, prossiga para o tutorial:

> [!div class="nextstepaction"]
> [Tutorial: criar e testar um modelo de capacidade de dispositivo usando Visual Studio Code](tutorial-pnp-visual-studio-code.md)
