---
title: Criar um dispositivo IoT Plug e Play Preview (Windows) Microsoft Docs
description: Utilize um modelo de capacidade do dispositivo para gerar código do dispositivo. Em seguida, executar o código do dispositivo e ver o dispositivo ligar-se ao seu Hub IoT.
author: miagdp
ms.author: miag
ms.date: 12/30/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 3d0a054e587a8f067be33913b05d2962b970fb1a
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/24/2020
ms.locfileid: "85321623"
---
# <a name="quickstart-use-a-device-capability-model-to-create-an-iot-plug-and-play-preview-device-windows"></a>Quickstart: Utilize um modelo de capacidade do dispositivo para criar um dispositivo IoT Plug e Play Preview (Windows)

[!INCLUDE [iot-pnp-quickstarts-1-selector.md](../../includes/iot-pnp-quickstarts-1-selector.md)]

Um _modelo de capacidade do dispositivo_ (DCM) descreve as capacidades de um dispositivo IoT Plug and Play. Um DCM é frequentemente associado a um produto SKU. As capacidades definidas no DCM são organizadas em interfaces reutilizáveis. Pode gerar código de dispositivo esqueleto a partir de um DCM. Este quickstart mostra-lhe como usar o Código VS no Windows para criar um dispositivo IoT Plug e Play utilizando um DCM.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este arranque rápido, tem de instalar o seguinte software na sua máquina local:

* [Construa ferramentas para o Estúdio Visual](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16) com **ferramentas de construção C++** e cargas de trabalho **de componentes de gestor de pacotes NuGet.** Ou se já tiver [o Visual Studio (Comunidade, Profissional ou Enterprise)](https://visualstudio.microsoft.com/downloads/) 2019, 2017 ou 2015 com as mesmas cargas de trabalho instaladas.
* [Git.](https://git-scm.com/download/)
* [CMake.](https://cmake.org/download/)
* [Visual Studio Code](https://code.visualstudio.com/).

### <a name="install-azure-iot-tools"></a>Instalar ferramentas Azure IoT

Utilize os seguintes passos para instalar as [ferramentas Azure IoT para](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) pacote de extensão vs código:

1. No código VS, selecione o **separador Extensões.**
1. Procure por **Ferramentas Azure IoT**.
1. Selecione **Instalar**.

### <a name="install-the-azure-iot-explorer"></a>Instale o explorador Azure IoT

Descarregue e instale o mais recente lançamento do **explorador Azure IoT** a partir da página de [repositório](https://github.com/Azure/azure-iot-explorer/releases) da ferramenta, selecionando o ficheiro .msi em "Ativos" para a mais recente atualização.

### <a name="get-the-connection-string-for-your-company-model-repository"></a>Obtenha a cadeia de ligação para o repositório de modelo da sua empresa

Pode encontrar a _cadeia de ligação do repositório_ do seu modelo de empresa no portal [Azure Certified for IoT](https://preview.catalog.azureiotsolutions.com) quando iniciar sedumento com uma conta de trabalho ou escola da Microsoft, ou com o seu ID do Microsoft Partner, se tiver um. Depois de iniciar sôs, selecione **o repositório da Empresa** e, em seguida, as cordas de **ligação**.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Executar o seguinte comando para obter a _cadeia de ligação do hub IoT_ para o seu hub (nota para utilização posterior):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>Preparar o ambiente de desenvolvimento

Neste arranque rápido, você usa o gestor da biblioteca [Vcpkg](https://github.com/microsoft/vcpkg) para instalar o dispositivo Azure IoT C SDK no seu ambiente de desenvolvimento.

1. Abra uma linha de comandos. Execute o seguinte comando para instalar vcpkg:

    ```cmd
    git clone https://github.com/Microsoft/vcpkg.git
    cd vcpkg

    .\bootstrap-vcpkg.bat
    ```

    Em seguida, para ligar a [integração](https://github.com/microsoft/vcpkg/blob/master/docs/users/integration.md)em todo o utilizador, executar o seguinte (nota: requer administração na primeira utilização):

    ```cmd
    .\vcpkg.exe integrate install
    ```

1. Instalar o dispositivo Azure IoT C SDK Vcpkg:

    ```cmd
    .\vcpkg.exe install azure-iot-sdk-c[public-preview,use_prov_client]
    ```

## <a name="author-your-model"></a>Autore o seu modelo

Neste arranque rápido, utiliza-se um modelo de capacidade de amostra existente e interfaces associadas.

1. Crie uma `pnp_app` pasta na unidade local. Utilize esta pasta para os ficheiros do modelo do dispositivo e para o código do dispositivo.

1. Descarregue o [modelo de capacidade do dispositivo e os ficheiros de amostras de interface](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/SampleDevice.model.json) e a amostra de [interface](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/EnvironmentalSensor.interface.json) e guarde os ficheiros na `pnp_app` pasta.

    > [!TIP]
    > Para descarregar um ficheiro do GitHub, navegue para o ficheiro, clique com o botão direito em **Raw**e, em seguida, selecione **Guardar link como**.

1. Abra `pnp_app` a pasta com o Código VS. Pode ver os ficheiros com o IntelliSense:

    ![Modelo de capacidade do dispositivo](media/quickstart-create-pnp-device/dcm.png)

1. Nos ficheiros que descarregou, substitua `<YOUR_COMPANY_NAME_HERE>` nos campos e campos por um valor `@id` `schema` único. Use apenas os caracteres a-z, A-Z, 0-9, e subsuse. Para mais informações, consulte [o formato do identificador Digital Twin.](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format)

## <a name="generate-the-c-code-stub"></a>Gerar o código C

Agora que tem um DCM e as suas interfaces associadas, pode gerar o código do dispositivo que implementa o modelo. Para gerar o código C no Código VS:

1. Com a `pnp_app` pasta aberta no Código VS, utilize **ctrl+Shift+P** para abrir a paleta de comando, **introduza ioT Plug e Play**, e selecione **'Gerar Código de Dispositivo' Stub**.

    > [!NOTE]
    > A primeira vez que utiliza o IoT Plug e o Play CodeGen CLI, demora alguns segundos a descarregar e instalar automaticamente.

1. Escolha o **SampleDevice.capabilitymodel.jsno** ficheiro para utilizar para gerar o código do dispositivo.

1. Insira o nome do projeto **sample_device**. Este é o nome da aplicação do seu dispositivo.

1. Escolha **ANSI C** como língua.

1. Escolha **a cadeia de ligação do dispositivo Via IoT Hub** como método de ligação.

1. Escolha **o Projeto CMake no Windows** como modelo de projeto.

1. Escolha **a Via Vcpkg** como a forma de incluir o dispositivo SDK.

1. Uma nova pasta chamada **sample_device** é criada no mesmo local que o ficheiro DCM, e nele estão os ficheiros de ficheiros de código do dispositivo gerados. O Código VS abre uma nova janela para os exibir.
    ![Código do dispositivo](media/quickstart-create-pnp-device/device-code.png)

## <a name="build-and-run-the-code"></a>Construir e executar o código

Utiliza-se o pacote Vcpkg para construir o código do dispositivo gerado. A aplicação que constrói simula um dispositivo que se conecta a um hub IoT. A aplicação envia telemetria e propriedades e recebe comandos.

1. Crie um `cmake` subdirecional na `sample_device` pasta e navegue para essa pasta:

    ```cmd
    mkdir cmake
    cd cmake
    ```

1. Executar os seguintes comandos para construir o código gerado (substituindo o espaço reservado pelo diretório do seu repo Vcpkg):

    ```cmd
    cmake .. -G "Visual Studio 16 2019" -A Win32 -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"

    cmake --build .
    ```
    
    > [!NOTE]
    > Se estiver a utilizar o Visual Studio 2017 ou 2015, tem de especificar o gerador CMake com base nas ferramentas de construção que está a utilizar:
    >```cmd
    ># Either
    >cmake .. -G "Visual Studio 15 2017" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}\scripts\buildsystems\vcpkg.cmake"
    ># or
    >cmake .. -G "Visual Studio 14 2015" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}\scripts\buildsystems\vcpkg.cmake"
    >```

    > [!NOTE]
    > Se o cmake não encontrar o seu compilador C++, obtém erros de construção quando executar o comando anterior. Se isso acontecer, tente executar este comando no comando do [Estúdio Visual.](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs)

1. Após a construção concluída com sucesso, execute a sua aplicação, passando a cadeia de ligação do dispositivo do hub IoT como parâmetro.

    ```cmd\sh
    .\Debug\sample_device.exe "<YourDeviceConnectionString>"
    ```

1. A aplicação do dispositivo começa a enviar dados para o IoT Hub.

    ![Aplicação de dispositivo em execução](media/quickstart-create-pnp-device/device-app-running.png)

## <a name="validate-the-code"></a>Validar o código

### <a name="publish-device-model-files-to-model-repository"></a>Publique ficheiros de modelos de dispositivo para modelar repositório

Para validar o código do dispositivo com **o Azure IoT Explorer,** é necessário publicar os ficheiros no repositório do modelo.

1. Com a `pnp_app` pasta aberta no Código VS, utilize **Ctrl+Shift+P** para abrir a paleta de comando, escreva e selecione **IoT Plug & Play: Submeta ficheiros ao Repositório de Modelos**.

1. Selecione `SampleDevice.capabilitymodel.json` e `EnvironmentalSensor.interface.json` arquiva.

1. Insira a cadeia de ligação do repositório do seu modelo de empresa.

    > [!NOTE]
    > A cadeia de ligação só é necessária na primeira vez que se liga ao repositório.

1. Na janela de saída e notificação do Código VS, pode verificar se os ficheiros foram publicados com sucesso.

    > [!NOTE]
    > Se tiver erros na publicação dos ficheiros dos modelos do dispositivo, pode tentar utilizar o comando **IoT Plug and Play: Assine o Model Repository** para assinar e passar novamente pelos passos.

### <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Utilize o explorador Azure IoT para validar o código

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. Para adicionar o repositório da sua empresa, selecione **Definições**, em seguida **, adicione fonte de definição**de módulo , em **seguida, repositório da empresa**. Adicione a cadeia de ligação do repositório do modelo da empresa e selecione **Save and Connect**.

1. De volta à página geral dos **Dispositivos,** encontre a identidade do dispositivo que criou anteriormente. Com a aplicação do dispositivo ainda em funcionamento na indicação de comando, verifique se o estado de **Ligação** do dispositivo no explorador IoT do Azure está a reportar como _Conectado_ (se não, acerte **refresh** até que seja). Selecione o dispositivo para ver mais detalhes.

1. Expandir a interface com **urna ID:<YOUR_INTERFACE_NAME>:EnvironmentalSensor:1** para ver os primitivos IoT Plug and Play - propriedades, comandos e telemetria. O nome da interface que vai aparecer é o nome que colocou na autoria do seu modelo.

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a criar um dispositivo IoT Plug and Play utilizando um DCM.

Para saber mais sobre os DCMs e como criar os seus próprios modelos, continue ao tutorial:

> [!div class="nextstepaction"]
> [Tutorial: Criar e testar um modelo de capacidade do dispositivo utilizando o Código do Estúdio Visual](tutorial-pnp-visual-studio-code.md)
