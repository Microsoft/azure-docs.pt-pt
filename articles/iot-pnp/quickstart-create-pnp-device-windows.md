---
title: Criar um dispositivo de visualização de Plug and Play IoT (Windows) | Microsoft Docs
description: Use um modelo de funcionalidade de dispositivo para gerar o código do dispositivo. Em seguida, execute o código do dispositivo e veja o dispositivo conectar-se ao Hub IoT.
author: miagdp
ms.author: miag
ms.date: 12/30/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: b447a0328de210caeb23a1beb91c532853c180bc
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75550474"
---
# <a name="quickstart-use-a-device-capability-model-to-create-an-iot-plug-and-play-preview-device-windows"></a>Início rápido: usar um modelo de capacidade de dispositivo para criar um dispositivo de visualização de Plug and Play IoT (Windows)

[!INCLUDE [iot-pnp-quickstarts-1-selector.md](../../includes/iot-pnp-quickstarts-1-selector.md)]

Um DCM ( _modelo de funcionalidade de dispositivo_ ) descreve os recursos de um dispositivo de plug and Play IOT. Um DCM geralmente é associado a um SKU de produto. Os recursos definidos no DCM são organizados em interfaces reutilizáveis. Você pode gerar um esqueleto de código de dispositivo de um DCM. Este guia de início rápido mostra como usar VS Code no Windows para criar um dispositivo de Plug and Play de IoT usando um DCM.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido, você precisa instalar o seguinte software em seu computador local:

* [Ferramentas de Build para Visual Studio](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16) com  **C++ ferramentas de compilação** e cargas de trabalho de **componente do Gerenciador de pacotes NuGet** . Ou, se você já tiver o [Visual Studio (Community, Professional ou Enterprise)](https://visualstudio.microsoft.com/downloads/) 2019, 2017 ou 2015 com as mesmas cargas de trabalho instaladas.
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/).
* [Visual Studio Code](https://code.visualstudio.com/).

### <a name="install-azure-iot-tools"></a>Instalar as ferramentas do Azure IoT

Use as etapas a seguir para instalar as [Ferramentas do Azure IOT para](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) o pacote de extensão vs Code:

1. Em VS Code, selecione a guia **extensões** .
1. Pesquise pelas **Ferramentas do Azure IOT**.
1. Selecione **Instalar**.

### <a name="install-the-azure-iot-explorer"></a>Instalar o Azure IoT Explorer

Baixe e instale a versão mais recente do **Azure IOT Explorer** na página [repositório](https://github.com/Azure/azure-iot-explorer/releases) da ferramenta, selecionando o arquivo. msi em "ativos" para a atualização mais recente.

### <a name="get-the-connection-string-for-your-company-model-repository"></a>Obter a cadeia de conexão para o repositório de modelos da empresa

Você pode encontrar a _cadeia de conexão do repositório de modelos da empresa_ no portal do [portal do Azure Certified para IOT](https://preview.catalog.azureiotsolutions.com) ao entrar com uma conta corporativa ou de estudante da Microsoft, ou sua ID de parceiro da Microsoft, se você tiver uma. Depois de entrar, selecione **repositório da empresa** e, em seguida, **cadeias de conexão**.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Execute o seguinte comando para obter a _cadeia de conexão do Hub IOT_ para o Hub (observação para uso posterior):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>Preparar o ambiente de desenvolvimento

Neste guia de início rápido, você usa o Gerenciador de biblioteca do [Vcpkg](https://github.com/microsoft/vcpkg) para instalar o SDK do dispositivo do Azure IOT C em seu ambiente de desenvolvimento.

1. Abra uma linha de comandos. Execute o seguinte comando para instalar o Vcpkg:

    ```cmd
    git clone https://github.com/Microsoft/vcpkg.git
    cd vcpkg

    .\bootstrap-vcpkg.bat
    ```

    Em seguida, para conectar a [integração](https://github.com/microsoft/vcpkg/blob/master/docs/users/integration.md)de todo o usuário, execute o seguinte (Observação: requer administrador no primeiro uso):

    ```cmd
    .\vcpkg.exe integrate install
    ```

1. Instalar o SDK do dispositivo do Azure IoT C Vcpkg:

    ```cmd
    .\vcpkg.exe install azure-iot-sdk-c[public-preview,use_prov_client]
    ```

## <a name="author-your-model"></a>Crie seu modelo

Neste guia de início rápido, você usa um modelo de funcionalidade de dispositivo de exemplo existente e as interfaces associadas.

1. Crie um diretório `pnp_app` na unidade local. Você usa essa pasta para os arquivos de modelo de dispositivo e o stub de código de dispositivo.

1. Baixe o [modelo de funcionalidade do dispositivo e os arquivos de exemplo da interface](https://github.com/Azure/IoTPlugandPlay/blob/master/samples/SampleDevice.capabilitymodel.json) e o [exemplo de interface](https://github.com/Azure/IoTPlugandPlay/blob/master/samples/EnvironmentalSensor.interface.json) e salve os arquivos na pasta `pnp_app`.

    > [!TIP]
    > Para baixar um arquivo do GitHub, navegue até o arquivo, clique com o botão direito do mouse em **RAW**e selecione **Salvar link como**.

1. Abra `pnp_app` pasta com VS Code. Você pode exibir os arquivos com o IntelliSense:

    ![Modelo de funcionalidade do dispositivo](media/quickstart-create-pnp-device/dcm.png)

1. Nos arquivos que você baixou, substitua `<YOUR_COMPANY_NAME_HERE>` nos campos `@id` e `schema` com um valor exclusivo. Use somente os caracteres a-z, A-Z, 0-9 e sublinhado. Para obter mais informações, consulte [formato do identificador de entrelaça digital](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format).

## <a name="generate-the-c-code-stub"></a>Gerar o stub do código C

Agora que você tem um DCM e suas interfaces associadas, você pode gerar o código do dispositivo que implementa o modelo. Para gerar o stub do código C no VS Code:

1. Com a pasta `pnp_app` aberta no VS Code, use **Ctrl + Shift + P** para abrir a paleta de comandos, insira **plug and Play de IOT**e selecione **gerar stub de código de dispositivo**.

    > [!NOTE]
    > Na primeira vez que você usar a CLI do CodeGen do IoT Plug and Play, levará alguns segundos para baixar e instalar automaticamente.

1. Escolha o arquivo **SampleDevice. capabilitymodel. JSON** a ser usado para gerar o stub do código de dispositivo.

1. Insira o nome do projeto **sample_device**. Esse será o nome do aplicativo do dispositivo.

1. Escolha **ANSI C** como seu idioma.

1. Escolha **por meio da cadeia de conexão do dispositivo Hub IOT** como método de conexão.

1. Escolha **projeto CMake no Windows** como seu modelo de projeto.

1. Escolha **por meio de Vcpkg** como a maneira de incluir o SDK do dispositivo.

1. Uma nova pasta chamada **sample_device** é criada no mesmo local que o arquivo DCM e, nele, são os arquivos stub de código de dispositivo gerados. VS Code abre uma nova janela para exibi-las.
    ![o código do dispositivo](media/quickstart-create-pnp-device/device-code.png)

## <a name="build-and-run-the-code"></a>Compilar e executar o código

Você usa o código-fonte do SDK do dispositivo para criar o stub do código de dispositivo gerado. O aplicativo que você cria simula um dispositivo que se conecta a um hub IoT. O aplicativo envia telemetria e propriedades e recebe comandos.

1. Crie um subdiretório `cmake` na pasta `sample_device` e navegue até essa pasta:

    ```cmd
    mkdir cmake
    cd cmake
    ```

1. Execute os seguintes comandos para compilar o stub de código gerado (substituindo o espaço reservado pelo diretório do seu repositório Vcpkg):

    ```cmd
    cmake .. -G "Visual Studio 16 2019" -A Win32 -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"

    cmake --build .
    ```
    
    > [!NOTE]
    > Se você estiver usando o Visual Studio 2017 ou 2015, precisará especificar o gerador de CMake com base nas ferramentas de compilação que você está usando:
    >```cmd
    ># Either
    >cmake .. -G "Visual Studio 15 2017" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}\scripts\buildsystems\vcpkg.cmake"
    ># or
    >cmake .. -G "Visual Studio 14 2015" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}\scripts\buildsystems\vcpkg.cmake"
    >```

    > [!NOTE]
    > Se o CMake não conseguir C++ localizar seu compilador, você obterá erros de compilação ao executar o comando anterior. Se isso acontecer, tente executar esse comando no [prompt de comando do Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs).

1. Depois que a compilação for concluída com êxito, execute o aplicativo, passando a cadeia de conexão do dispositivo do Hub IoT como um parâmetro.

    ```cmd\sh
    .\Debug\sample_device.exe "<YourDeviceConnectionString>"
    ```

1. O aplicativo do dispositivo começa a enviar dados para o Hub IoT.

    ![Aplicativo de dispositivo em execução](media/quickstart-create-pnp-device/device-app-running.png)

## <a name="validate-the-code"></a>Validar o código

### <a name="publish-device-model-files-to-model-repository"></a>Publicar arquivos de modelo de dispositivo no repositório de modelos

Para validar o código do dispositivo com o **Azure IOT Explorer**, você precisa publicar os arquivos no repositório de modelo.

1. Com a pasta `pnp_app` aberta em VS Code, use **Ctrl + Shift + P** para abrir a paleta de comandos, digite e selecione **plug-in IOT & reproduzir: enviar arquivos ao repositório de modelos**.

1. Selecione `SampleDevice.capabilitymodel.json` e `EnvironmentalSensor.interface.json` arquivos.

1. Insira a cadeia de conexão do repositório de modelos da empresa.

    > [!NOTE]
    > A cadeia de conexão só é necessária na primeira vez que você se conecta ao repositório.

1. Em VS Code janela de saída e notificação, você pode verificar se os arquivos foram publicados com êxito.

    > [!NOTE]
    > Se você receber erros ao publicar os arquivos de modelo do dispositivo, poderá tentar usar o comando **IoT plug and Play: sair do repositório de modelos** para sair e passar pelas etapas novamente.

### <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Usar o Azure IoT Explorer para validar o código

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. Para adicionar o repositório de sua empresa, selecione **configurações**e **+ Adicionar fonte de definição de módulo**e **repositório da empresa**. Adicione a cadeia de conexão do repositório de modelos da empresa e selecione **salvar e conectar**.

1. De volta à página Visão geral de **dispositivos** , localize a identidade do dispositivo que você criou anteriormente. Com o aplicativo do dispositivo ainda em execução no prompt de comando, verifique se o **estado da conexão** do dispositivo no Azure IOT Explorer está relatando como _conectado_ (caso contrário, pressione **Atualizar** até que ele esteja). Selecione o dispositivo para exibir mais detalhes.

1. Expanda a interface com ID **urn: < YOUR_INTERFACE_NAME >: EnvironmentalSensor: 1** para ver os primitivos de plug and Play IOT-Propriedades, comandos e telemetria. O nome da interface que aparecerá é o nome que você colocou ao criar seu modelo.

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você aprendeu a criar um dispositivo de Plug and Play de IoT usando um DCM.

Para saber mais sobre o DCMs e como criar seus próprios modelos, prossiga para o tutorial:

> [!div class="nextstepaction"]
> [Tutorial: criar e testar um modelo de capacidade de dispositivo usando Visual Studio Code](tutorial-pnp-visual-studio-code.md)
