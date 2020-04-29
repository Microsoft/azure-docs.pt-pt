---
title: Criar um dispositivo de pré-visualização IoT Plug e Play (Windows) [ Microsoft Docs
description: Utilize um modelo de capacidade do dispositivo para gerar código de dispositivo. Em seguida, execute o código do dispositivo e veja o dispositivo ligado ao seu Hub IoT.
author: miagdp
ms.author: miag
ms.date: 12/30/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: e80194f53a406b8b378d0fb787df627937125a27
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75867490"
---
# <a name="quickstart-use-a-device-capability-model-to-create-an-iot-plug-and-play-preview-device-windows"></a>Quickstart: Utilize um modelo de capacidade do dispositivo para criar um dispositivo de pré-visualização ioT Plug e Play (Windows)

[!INCLUDE [iot-pnp-quickstarts-1-selector.md](../../includes/iot-pnp-quickstarts-1-selector.md)]

Um modelo de capacidade de _dispositivo_ (DCM) descreve as capacidades de um dispositivo IoT Plug and Play. Um DCM é frequentemente associado a um Produto SKU. As capacidades definidas no DCM são organizadas em interfaces reutilizáveis. Pode gerar código de dispositivo esqueleto a partir de um DCM. Este quickstart mostra-lhe como utilizar o Código VS no Windows para criar um dispositivo IoT Plug and Play utilizando um DCM.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este arranque rápido, é necessário instalar o seguinte software na sua máquina local:

* [Construa ferramentas para estúdio visual](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16) com **ferramentas de construção C++** e cargas de trabalho componentes de **gestor de pacotes NuGet.** Ou se já tem [o Visual Studio (Comunidade, Profissional ou Empresa)](https://visualstudio.microsoft.com/downloads/) 2019, 2017 ou 2015 com as mesmas cargas de trabalho instaladas.
* [Git.](https://git-scm.com/download/)
* [Cmake.](https://cmake.org/download/)
* [Código de estúdio visual.](https://code.visualstudio.com/)

### <a name="install-azure-iot-tools"></a>Instalar ferramentas Azure IoT

Utilize os seguintes passos para instalar as [ferramentas Azure IoT para](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) a embalagem de extensão do código VS:

1. No Código VS, selecione o separador **Extensões.**
1. Pesquisa de **ferramentas Azure IoT**.
1. Selecione **Instalar**.

### <a name="install-the-azure-iot-explorer"></a>Instale o explorador Azure IoT

Descarregue e instale o mais recente lançamento do **explorador Azure IoT** a partir da página de [repositório](https://github.com/Azure/azure-iot-explorer/releases) da ferramenta, selecionando o ficheiro .msi em "Assets" para a mais recente atualização.

### <a name="get-the-connection-string-for-your-company-model-repository"></a>Obtenha a cadeia de ligação para o seu repositório modelo da empresa

Pode encontrar a sua cadeia de _ligação repositório_ modelo da empresa no [portal Azure Certified for IoT](https://preview.catalog.azureiotsolutions.com) quando iniciar sessão com uma conta de trabalho ou escola da Microsoft, ou o seu ID do Microsoft Partner, se tiver um. Depois de iniciar sessão, selecione **repositório da Empresa** e, em seguida, ligacordas de **ligação**.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Executar o seguinte comando para obter a cadeia de ligação do _hub IoT_ para o seu hub (nota para utilização posterior):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>Preparar o ambiente de desenvolvimento

Neste arranque rápido, utiliza o gestor da biblioteca [Vcpkg](https://github.com/microsoft/vcpkg) para instalar o dispositivo Azure IoT C SDK no seu ambiente de desenvolvimento.

1. Abra uma linha de comandos. Executar o seguinte comando para instalar vcpkg:

    ```cmd
    git clone https://github.com/Microsoft/vcpkg.git
    cd vcpkg

    .\bootstrap-vcpkg.bat
    ```

    Em seguida, para ligar a [integração](https://github.com/microsoft/vcpkg/blob/master/docs/users/integration.md)em todo o utilizador, executar o seguinte (nota: requer administrador na primeira utilização):

    ```cmd
    .\vcpkg.exe integrate install
    ```

1. Instale o dispositivo SDK Vcpkg do dispositivo Azure IoT C:

    ```cmd
    .\vcpkg.exe install azure-iot-sdk-c[public-preview,use_prov_client]
    ```

## <a name="author-your-model"></a>Autor do seu modelo

Neste arranque rápido, utiliza-se um modelo de capacidade de dispositivo de amostra existente e interfaces associadas.

1. Crie `pnp_app` uma pasta na sua unidade local. Utiliza esta pasta para os ficheiros do modelo do dispositivo e para o código do dispositivo.

1. Descarregue o modelo de capacidade do [dispositivo e experimente](https://github.com/Azure/IoTPlugandPlay/blob/master/samples/SampleDevice.capabilitymodel.json) a amostra de [interface](https://github.com/Azure/IoTPlugandPlay/blob/master/samples/EnvironmentalSensor.interface.json) e guarde os ficheiros na `pnp_app` pasta.

    > [!TIP]
    > Para descarregar um ficheiro do GitHub, navegue para o ficheiro, clique à direita em **Raw**, e, em seguida, selecione **o link Guardar como**.

1. Abra `pnp_app` pasta com Código VS. Pode ver os ficheiros com o IntelliSense:

    ![Modelo de capacidade do dispositivo](media/quickstart-create-pnp-device/dcm.png)

1. Nos ficheiros que descarregou, `@id` `schema` substitua `<YOUR_COMPANY_NAME_HERE>` nos campos e campos por um valor único. Use apenas os caracteres a-z, A-Z, 0-9, e sublinhe. Para mais informações, consulte o [formato de identificador Digital Twin.](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format)

## <a name="generate-the-c-code-stub"></a>Gerar o código C

Agora que tem um DCM e as suas interfaces associadas, pode gerar o código do dispositivo que implementa o modelo. Para gerar o código C no Código VS:

1. Com `pnp_app` a pasta aberta no Código VS, utilize **o Ctrl+Shift+P** para abrir a paleta de comando, **introduza a Ficha IoT e a Reprodução,** e selecione **Generate Device Code Stub**.

    > [!NOTE]
    > A primeira vez que utiliza o IoT Plug e reprodução do CodeGen CLI, demora alguns segundos a descarregar e instalar automaticamente.

1. Escolha o ficheiro **SampleDevice.capabilitymodel.json** para utilizar para gerar o código do dispositivo.

1. Introduza o nome do projeto **sample_device**. Este é o nome da aplicação do seu dispositivo.

1. Escolha **ansi C** como língua.

1. Escolha a cadeia de ligação do **dispositivo IoT Hub** como método de ligação.

1. Escolha o **CMake Project no Windows** como modelo de projeto.

1. Escolha **via Vcpkg** como forma de incluir o dispositivo SDK.

1. Uma nova pasta chamada **sample_device** é criada no mesmo local que o ficheiro DCM, e nele estão os ficheiros de código do dispositivo gerados. O Código VS abre uma nova janela para exibição destes.
    ![Código do dispositivo](media/quickstart-create-pnp-device/device-code.png)

## <a name="build-and-run-the-code"></a>Construir e executar o código

Usa a embalagem vcpkg para construir o código do dispositivo gerado. A aplicação que constrói simula um dispositivo que se conecta a um hub IoT. A aplicação envia telemetria e propriedades e recebe comandos.

1. Crie `cmake` um subdiretório na `sample_device` pasta e navegue para essa pasta:

    ```cmd
    mkdir cmake
    cd cmake
    ```

1. Executar os seguintes comandos para construir o tubo de código gerado (substituindo o espaço reservado pelo diretório do seu repo vcpkg):

    ```cmd
    cmake .. -G "Visual Studio 16 2019" -A Win32 -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"

    cmake --build .
    ```
    
    > [!NOTE]
    > Se estiver a utilizar o Visual Studio 2017 ou 2015, precisa especificar o gerador CMake com base nas ferramentas de construção que está a utilizar:
    >```cmd
    ># Either
    >cmake .. -G "Visual Studio 15 2017" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}\scripts\buildsystems\vcpkg.cmake"
    ># or
    >cmake .. -G "Visual Studio 14 2015" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}\scripts\buildsystems\vcpkg.cmake"
    >```

    > [!NOTE]
    > Se o cmake não conseguir encontrar o seu compilador C++, obtém erros de construção quando executa o comando anterior. Se isso acontecer, tente executar este comando no comando do [Estúdio Visual.](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs)

1. Depois de a construção completar com sucesso, execute a sua aplicação, passando a cadeia de ligação do dispositivo do hub IoT como parâmetro.

    ```cmd\sh
    .\Debug\sample_device.exe "<YourDeviceConnectionString>"
    ```

1. A aplicação do dispositivo começa a enviar dados para o IoT Hub.

    ![Aplicativo de dispositivo em execução](media/quickstart-create-pnp-device/device-app-running.png)

## <a name="validate-the-code"></a>Validar o código

### <a name="publish-device-model-files-to-model-repository"></a>Publique ficheiros de modelo seletiva seletivas para o repositório de modelos

Para validar o código do dispositivo com o **Azure IoT Explorer,** é necessário publicar os ficheiros no repositório do modelo.

1. Com `pnp_app` a pasta aberta no Código VS, utilize **ctrl+Shift+P** para abrir a paleta de comando, digitar e selecionar **plug IoT & Reproduzir: Submeter ficheiros ao Repositório**modelo .

1. Selecione `SampleDevice.capabilitymodel.json` e `EnvironmentalSensor.interface.json` ficheiros.

1. Introduza a sua cadeia de ligação repositório modelo da empresa.

    > [!NOTE]
    > A corda de ligação só é necessária na primeira vez que se liga ao repositório.

1. Na janela e notificação de saída do Código VS, pode verificar se os ficheiros foram publicados com sucesso.

    > [!NOTE]
    > Se tiver erros na publicação dos ficheiros do modelo do dispositivo, pode tentar utilizar o comando **IoT Plug e Play: Assine o Repositório modelo** para assinar e passar novamente pelos degraus.

### <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Use o explorador Azure IoT para validar o código

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. Para adicionar o repositório da empresa, selecione **Definições,** em seguida + Adicione a fonte de definição de **módulo,** em seguida, **repositório da empresa**. Adicione a corda de ligação repositório do modelo da empresa e selecione **Guardar e Ligar**.

1. De volta à página de visão geral dos **Dispositivos,** encontre a identidade do dispositivo que criou anteriormente. Com a aplicação do dispositivo ainda em funcionamento no pedido de comando, verifique se o estado de **Ligação** do dispositivo no explorador Azure IoT está a reportar como _Connected_ (se não for, acerte **em Refresh** até estar). Selecione o dispositivo para ver mais detalhes.

1. Expanda a interface com **urna ID:<YOUR_INTERFACE_NAME>:EnvironmentalSensor:1** para ver os primitivos IoT Plug e Play - propriedades, comandos e telemetria. O nome da interface que aparecerá é o nome que coloca ao escrever o seu modelo.

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a criar um dispositivo IoT Plug and Play utilizando um DCM.

Para saber mais sobre dcms e como criar os seus próprios modelos, continue para o tutorial:

> [!div class="nextstepaction"]
> [Tutorial: Criar e testar um modelo de capacidade de dispositivo usando o Código do Estúdio Visual](tutorial-pnp-visual-studio-code.md)
