---
title: Crie um dispositivo de pré-visualização azure IoT Plug e Play (Linux) [ Microsoft Docs
description: Utilize um modelo de capacidade do dispositivo para gerar código de dispositivo. Em seguida, execute o código do dispositivo e veja o dispositivo ligado ao seu Hub IoT.
author: dominicbetts
ms.author: dobett
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: d2cc440572d6f33480972c15f5c498cc384cb2e3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "75550490"
---
# <a name="quickstart-use-a-device-capability-model-to-create-an-iot-plug-and-play-preview-device-linux"></a>Quickstart: Utilize um modelo de capacidade do dispositivo para criar um dispositivo de pré-visualização IoT Plug e Play (Linux)

[!INCLUDE [iot-pnp-quickstarts-1-selector.md](../../includes/iot-pnp-quickstarts-1-selector.md)]

Um modelo de capacidade de _dispositivo_ (DCM) descreve as capacidades de um dispositivo IoT Plug and Play. Um DCM é frequentemente associado a um Produto SKU. As capacidades definidas no DCM são organizadas em interfaces reutilizáveis. Pode gerar código de dispositivo esqueleto a partir de um DCM. Este quickstart mostra-lhe como usar o Código VS no Ubuntu Linux para criar um dispositivo IoT Plug and Play utilizando um DCM.

## <a name="prerequisites"></a>Pré-requisitos

Este quickstart assume que está a usar Ubuntu Linux com um ambiente de trabalho. Os passos deste tutorial foram testados usando Ubuntu 18.04.

Para completar este arranque rápido, precisa de instalar o seguinte software na sua máquina Linux local:

* Instale **GCC,** **Git,** **cmake,** e `apt-get` todas as dependências utilizando o comando:

    ```sh
    sudo apt-get update
    sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
    ```

    Verifique se `cmake` a versão é superior a **2.8.12** e a versão do **CcG** está acima de **4.4.7**.

    ```sh
    cmake --version
    gcc --version
    ```

* [Código de estúdio visual.](https://code.visualstudio.com/)

### <a name="install-azure-iot-tools"></a>Instalar ferramentas Azure IoT

Utilize os seguintes passos para instalar as [ferramentas Azure IoT para](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) a embalagem de extensão do código VS:

1. No Código VS, selecione o separador **Extensões.**
1. Pesquisa de **ferramentas Azure IoT**.
1. Selecione **Instalar**.

### <a name="get-the-connection-string-for-your-company-model-repository"></a>Obtenha a cadeia de ligação para o seu repositório modelo da empresa

Pode encontrar a sua cadeia de _ligação repositório_ modelo da empresa no [portal Azure Certified for IoT](https://preview.catalog.azureiotsolutions.com) quando iniciar sessão com uma conta de trabalho ou escola da Microsoft, ou o seu ID do Microsoft Partner, se tiver um. Depois de iniciar sessão, selecione **repositório da Empresa** e, em seguida, ligacordas de **ligação**.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

## <a name="prepare-the-development-environment"></a>Preparar o ambiente de desenvolvimento

Neste arranque rápido, utiliza o gestor da biblioteca [Vcpkg](https://github.com/microsoft/vcpkg) para instalar o dispositivo Azure IoT C SDK no seu ambiente de desenvolvimento.

Abra uma shell. Executar o seguinte comando para instalar vcpkg:

```bash
cd ~
git clone https://github.com/microsoft/vcpkg
cd vcpkg
./bootstrap-vcpkg.sh
./vcpkg install azure-iot-sdk-c[public-preview,use_prov_client]
```

Esta operação deve demorar vários minutos a ser concluída.

## <a name="author-your-model"></a>Autor do seu modelo

Neste arranque rápido, utiliza-se um modelo de capacidade de dispositivo de amostra existente e interfaces associadas.

1. Crie `pnp_app` um diretório na sua unidade local. Utiliza esta pasta para os ficheiros do modelo do dispositivo e para o código do dispositivo.

    ```bash
    cd ~
    mkdir pnp_app
    ```

1. Descarregue o modelo de capacidade `pnp_app` do dispositivo e os ficheiros da amostra de interface para a pasta.

    ```bash
    cd pnp_app
    curl -O -L https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/SampleDevice.capabilitymodel.json
    curl -O -L https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensor.interface.json
    ```

1. Abra `pnp_app` pasta com Código VS. Pode ver os ficheiros com o IntelliSense:

    ![Modelo de capacidade do dispositivo](media/quickstart-create-pnp-device-linux/dcm.png)

1. Nos ficheiros que descarregou, `@id` `schema` substitua `<YOUR_COMPANY_NAME_HERE>` nos campos e campos por um valor único. Use apenas os caracteres a-z, A-Z, 0-9, e sublinhe. Para mais informações, consulte o [formato de identificador Digital Twin.](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format)

## <a name="generate-the-c-code-stub"></a>Gerar o código C

Agora que tem um DCM e as suas interfaces associadas, pode gerar o código do dispositivo que implementa o modelo. Para gerar o código C no Código VS:

1. Com `pnp_app` a pasta aberta no Código VS, utilize **o Ctrl+Shift+P** para abrir a paleta de comando, **introduza a Ficha IoT e a Reprodução,** e selecione **Generate Device Code Stub**.

    > [!NOTE]
    > A primeira vez que utiliza o utilitário IoT Plug and Play Code Generator, demora alguns segundos a descarregar e instalar automaticamente.

1. Escolha o ficheiro **SampleDevice.capabilitymodel.json** para utilizar para gerar o código do dispositivo.

1. Introduza o nome do projeto **sample_device**. Este será o nome da aplicação do seu dispositivo.

1. Escolha **ansi C** como língua.

1. Escolha a cadeia de ligação do **dispositivo IoT Hub** como método de ligação.

1. Escolha **o CMake Project no Linux** como modelo de projeto.

1. Escolha **via Vcpkg** como forma de incluir o dispositivo SDK.

1. Uma nova pasta chamada **sample_device** é criada no mesmo local que o ficheiro DCM, e nele estão os ficheiros de código do dispositivo gerados. O Código VS abre uma nova janela para exibição destes.
    ![Código do dispositivo](media/quickstart-create-pnp-device-linux/device-code.png)

## <a name="build-and-run-the-code"></a>Construir e executar o código

Utiliza o código fonte SDK do dispositivo para construir o código de código do dispositivo gerado. A aplicação que constrói simula um dispositivo que se conecta a um hub IoT. A aplicação envia telemetria e propriedades e recebe comandos.

1. Criar uma pasta de construção **CMake** para a aplicação **sample_device:**

    ```bash
    cd ~/pnp_app/sample_device
    mkdir cmake
    cd cmake
    ```

1. Executar CMake para construir a sua aplicação com o SDK. O seguinte comando pressupõe que instalou **vcpkg** na sua pasta inicial:

    ```bash
    cmake .. -DCMAKE_TOOLCHAIN_FILE=~/vcpkg/scripts/buildsystems/vcpkg.cmake -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON
    cmake --build .
    ```

1. Depois de a construção se concluir com sucesso, execute a sua aplicação passando a cadeia de ligação do dispositivo do hub IoT como parâmetro.

    ```sh
    cd ~/pnp_app/sample_device/cmake
    ./sample_device "<YourDeviceConnectionString>"
    ```

1. A aplicação do dispositivo começa a enviar dados para o IoT Hub.

    ![Aplicativo de dispositivo em execução](media/quickstart-create-pnp-device-linux/device-app-running.png)

## <a name="validate-the-code"></a>Validar o código

### <a name="publish-device-model-files-to-model-repository"></a>Publique ficheiros de modelo seletiva seletivas para o repositório de modelos

Para validar o código do dispositivo com o **az** CLI, é necessário publicar os ficheiros no repositório do modelo.

1. Com `pnp_app` a pasta aberta no código VS, utilize **o Ctrl+Shift+P** para abrir a paleta de comando, digitar e selecionar **plug IoT & Reproduzir: Submeter ficheiros ao Repositório**modelo .

1. Selecione `SampleDevice.capabilitymodel.json` e `EnvironmentalSensor.interface.json` ficheiros.

1. Introduza a sua cadeia de ligação repositório modelo da empresa.

    > [!NOTE]
    > A corda de ligação só é necessária na primeira vez que se liga ao repositório.

1. Na janela e notificação de saída do Código VS, pode verificar se os ficheiros foram publicados com sucesso.

    > [!NOTE]
    > Se tiver erros na publicação dos ficheiros do modelo do dispositivo, pode tentar utilizar o comando **IoT Plug e Play: Assine o Repositório modelo** para assinar e passar novamente pelos degraus.

### <a name="use-the-azure-iot-cli-to-validate-the-code"></a>Utilize o CLI Azure IoT para validar o código

Após o início da amostra do cliente do dispositivo, pode verificar se está a trabalhar com o Azure CLI.

Utilize o seguinte comando para visualizar a telemetria que o dispositivo de amostra está a enviar. Pode ter de esperar um minuto ou dois antes de ver qualquer telemetria na saída:

```azurecli-interactive
az iot dt monitor-events --hub-name <YourIoTHubNme> --device-id <YourDeviceID>
```

Utilize o seguinte comando para visualizar todas as propriedades enviadas pelo dispositivo:

```azurecli-interactive
az iot dt list-properties --device-id <YourDeviceID> --hub-name <YourIoTHubNme> --source private --repo-login "<YourCompanyModelRepositoryConnectionString>"
```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a criar um dispositivo IoT Plug and Play utilizando um DCM.

Para saber mais sobre dcms e como criar os seus próprios modelos, continue para o tutorial:

> [!div class="nextstepaction"]
> [Tutorial: Criar e testar um modelo de capacidade de dispositivo usando o Código do Estúdio Visual](tutorial-pnp-visual-studio-code.md)
