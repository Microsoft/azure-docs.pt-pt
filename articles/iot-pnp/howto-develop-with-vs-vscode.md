---
title: Usar o Visual Studio e o Visual Studio Code para criar dispositivos de visualização de IoT Plug and Play | Microsoft Docs
description: Use o Visual Studio e Visual Studio Code para acelerar a criação de modelos de dispositivos IoT Plug and Play e implementar o código do dispositivo.
author: liydu
ms.author: liydu
ms.date: 12/26/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: b4325aa6f379dc0b281d06cb593c28448698c71b
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/28/2019
ms.locfileid: "75531331"
---
# <a name="use-visual-studio-and-visual-studio-code-to-build-iot-plug-and-play-devices"></a>Usar o Visual Studio e Visual Studio Code para criar dispositivos de Plug and Play IoT

As ferramentas do Azure IoT para Visual Studio Code fornecem um ambiente integrado para criar modelos de recursos de dispositivo (DCM) e interfaces, publicar em repositórios de modelo e gerar código de esqueleto C para implementar o aplicativo do dispositivo.

Este artigo mostra-lhe como:

- Gere o código do dispositivo e o projeto do aplicativo.
- Use o código gerado em seu projeto de dispositivo.
- Iterar gerando novamente o código esqueleto.

Para saber mais sobre como usar o VS Code para desenvolver dispositivos IoT, consulte [https://github.com/microsoft/vscode-iot-workbench](https://github.com/microsoft/vscode-iot-workbench).

## <a name="prerequisites"></a>Pré-requisitos

Instalar o [Visual Studio Code](https://code.visualstudio.com/).

Use as etapas a seguir para instalar o pacote de extensão no VS Code.

1. Em VS Code, selecione a guia **extensões** .
1. Pesquise e instale as **ferramentas de IOT do Azure** no Marketplace.

## <a name="generate-device-code-and-project"></a>Gerar o código do dispositivo e o projeto

Em VS Code, use **Ctrl + Shift + P** para abrir a paleta de comandos, digite **IOT plug and Play**e selecione **gerar stub de código de dispositivo** para configurar o código esqueleto e o tipo de projeto. A lista a seguir descreve cada etapa detalhadamente:

- **O arquivo DCM a ser usado para gerar o código**. Para gerar o esqueleto do código de dispositivo, abra a pasta que contém os arquivos de interface e DCM que ele implementa. Se o gerador de código não conseguir encontrar uma interface exigida por um DCM, ele o baixará do repositório de modelos, conforme necessário.

- **Idioma do código do dispositivo**. Atualmente, o gerador de código dá suporte apenas a ANSI C.

- **Nome do projeto**. O nome do projeto é usado como o nome da pasta para o código gerado e outros arquivos de projeto. A pasta é, por padrão, colocada ao lado do arquivo DCM. Para evitar a cópia manual da pasta de código gerada sempre que você atualizar o DCM e gerar novamente o código do dispositivo, mantenha o arquivo DCM na mesma pasta que a pasta do projeto.

- **Método para se conectar ao Azure IOT**. Os arquivos gerados também contêm código para configurar o dispositivo para se conectar ao Hub IoT do Azure. Você pode optar por se conectar diretamente ao [Hub IOT do Azure](https://docs.microsoft.com/azure/iot-hub) ou usar o [serviço de provisionamento de dispositivos](https://docs.microsoft.com/azure/iot-dps).

    - **Por meio da cadeia de conexão do dispositivo do Hub IOT**: Especifique a cadeia de conexão do dispositivo para que o aplicativo do dispositivo se conecte diretamente ao Hub IOT.
    - **Por meio da chave simétrica do DPS**: especifique o **escopo da ID**, a **chave simétrica** e a **ID do dispositivo** para o aplicativo do dispositivo que são necessários para se conectar ao Hub IOT ou IOT central usando o DPS.

- **Tipo de projeto**. O gerador de código também gera um projeto CMake ou Arduino. Atualmente, os tipos de projeto com suporte são:

    - **Projeto CMake no Windows**: para um projeto de dispositivo que usa o [CMake](https://cmake.org/) como sistema de compilação no Windows. Essa opção gera `CMakeLists.txt` com as configurações do SDK do dispositivo na mesma pasta que o código C.
    - **Projeto CMake no Linux**: para um projeto de dispositivo que usa o [CMake](https://cmake.org/) como sistema de compilação no Linux. Essa opção gera `CMakeLists.txt` com as configurações do SDK do dispositivo na mesma pasta que o código C.
    - **Projeto MXChip IOT devkit**: para um projeto de dispositivo executado em um dispositivo [MXChip IOT devkit](https://aka.ms/iot-devkit) . Essa opção gera um projeto Arduino que você pode [usar em vs Code](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) ou no IDE Arduino para compilar e executar em um dispositivo IOT devkit.

- **Tipo de SDK do dispositivo**. Se você selecionar CMake como tipo de projeto, esta é a etapa para configurar como o código gerado incluirá o SDK do dispositivo do Azure IoT C no `CMakeLists.txt`:

    - **Por meio do código-fonte**: o código gerado depende do [código-fonte do SDK do dispositivo](https://github.com/Azure/azure-iot-sdk-c) para incluir e criar junto com ele. Isso é recomendado quando você personalizou o código-fonte do SDK do dispositivo.
    - **Via Vcpkg**: o código gerado depende do SDK do [dispositivo Vcpkg](https://github.com/microsoft/vcpkg/tree/master/ports/azure-iot-sdk-c) para incluir no e criar junto com ele. Essa é a maneira recomendada para dispositivos que executam Windows, Linux ou macOS.

    > [!NOTE]
    > o suporte do macOS para o SDK do dispositivo do Azure IoT C Vcpkg está funcionando em andamento.

O gerador de código tenta usar o DCM e os arquivos de interface localizados na pasta local. Se os arquivos de interface não estiverem na pasta local, o gerador de código o procurará no repositório de modelo público ou no repositório de modelo da empresa. Os [arquivos de interface comuns](./concepts-common-interfaces.md) são armazenados no repositório de modelo público.

Após a conclusão da geração de código, a extensão abre uma nova janela de VS Code com o código. Se você abrir um arquivo gerado como **Main. c**, talvez ache que o IntelliSense relata que ele não pode abrir os arquivos de origem do SDK do c. Para habilitar o IntelliSense e a navegação de código corretos, use as seguintes etapas para incluir a origem do SDK do C:

1. Em vs Code, use **Ctrl + Shift + P** para abrir a paleta de comandos, digite e selecione **CC++/: Editar configurações (JSON)** para abrir o arquivo **c_cpp_properties. JSON** .

1. Adicione o caminho do SDK do dispositivo na seção `includePath`:

    ```json
    "includePath": [
        "${workspaceFolder}/**",
        "{path_of_device_c_sdk}/**"
    ]
    ```

1. Guarde o ficheiro.

## <a name="use-the-generated-code"></a>Usar o código gerado

As instruções a seguir descrevem como usar o código gerado em seu próprio projeto de dispositivo em diferentes plataformas de máquina de desenvolvimento. As instruções pressupõem que você está usando uma cadeia de conexão de dispositivo do Hub IoT com o código gerado:

### <a name="linux"></a>Linux

Para criar o código do dispositivo junto com o SDK do dispositivo C Vcpkg usando o CMake em um ambiente Linux, como Ubuntu ou Debian:

1. Abra um aplicativo de terminal.

1. Instale o **gcc**, **git**, `cmake`e todas as dependências usando o comando `apt-get`:

    ```bash
    sudo apt-get update
    sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
    ```

    Verifique se a versão do `cmake` está acima de **2.8.12** e se a versão do **gcc** está acima de **4.4.7**.

    ```bash
    cmake --version
    gcc --version
    ```

1. Instalar o Vcpkg:

    ```bash
    git clone https://github.com/Microsoft/vcpkg.git
    cd vcpkg

    ./bootstrap-vcpkg.sh
    ```

    Em seguida, para conectar a [integração](https://github.com/microsoft/vcpkg/blob/master/docs/users/integration.md)de todo o usuário, execute:

    ```bash
    ./vcpkg integrate install
    ```

1. Instalar o SDK do dispositivo do Azure IoT C Vcpkg:

    ```bash
    ./vcpkg install azure-iot-sdk-c[public-preview,use_prov_client]
    ```

1. Crie um subdiretório `cmake` na pasta que contém o stub de código gerado e navegue até essa pasta:

    ```bash
    mkdir cmake
    cd cmake
    ```

1. Execute os seguintes comandos para usar o CMake para criar o SDK do dispositivo e o stub do código gerado:

    ```bash
    cmake .. -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}/scripts/buildsystems/vcpkg.cmake"

    cmake --build .
    ```

1. Depois que a compilação for realizada com sucesso, execute o aplicativo especificando a cadeia de conexão do dispositivo do Hub IoT como parâmetro.

    ```bash
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

### <a name="windows"></a>Windows

Para criar o código do dispositivo junto com o SDK do dispositivo C no Windows usando o CMake e os compiladores C/C++ do Visual Studio na linha de comando, consulte o [início rápido do IOT plug and Play](./quickstart-create-pnp-device-windows.md). As etapas a seguir mostram como criar o código do dispositivo junto com o SDK do dispositivo C Vcpkg como projeto CMake no Visual Studio.

1. Siga as etapas no guia de [início rápido](https://docs.microsoft.com/azure/iot-pnp/quickstart-create-pnp-device-windows#prepare-the-development-environment) para instalar o SDK do dispositivo IOT do Azure para C via Vcpkg.

1. Instalar o [Visual Studio 2019 (Comunidade, Professional ou Enterprise)](https://visualstudio.microsoft.com/downloads/) – certifique-se de incluir o componente **Gerenciador de pacotes NuGet** e o desenvolvimento de **desktop com C++**  carga de trabalho.

1. Abra o Visual Studio, escolha **arquivo > abrir > CMake...** para abrir o `CMakeLists.txt` na pasta contém o código gerado.

1. Na barra de ferramentas **geral** , localize o menu suspenso **configurações** . Selecione **gerenciar configuração** para adicionar a configuração CMake para seu projeto.

    ![Gerenciar configuração](media/howto-develop-with-vs-vscode/vs-manage-config.png)

1. Nas **configurações do cmake**, adicione uma nova configuração e selecione **x86-depurar** como destino.

1. Em **argumentos de comando CMake**, adicione a seguinte linha:

    ```txt
    -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON
    ```

1. Guarde o ficheiro.

1. Alterne para **depurador x86** na lista suspensa **configurações** . Ele precisa de alguns segundos para que o CMake gere o cache para ele. Exiba a janela saída para ver o progresso.

    ![Saída de CMake](media/howto-develop-with-vs-vscode/vs-cmake-output.png)

1. Na **Gerenciador de soluções**, clique com o botão direito do mouse na `CMakeLists.txt` na pasta raiz e selecione **Compilar** no menu de contexto para criar o stub de código gerado com o SDK do dispositivo.

1. Depois que a compilação for realizada com sucesso, no prompt de comando, execute o aplicativo especificando a cadeia de conexão do dispositivo do Hub IoT como um parâmetro.

    ```cmd
    .\out\build\x86-Debug\{generated_code_project_name}.exe "[IoT Hub device connection string]"
    ```

> [!TIP]
> Para saber mais sobre como usar o CMake no Visual Studio, consulte [Compilar projeto CMake](https://docs.microsoft.com/cpp/build/cmake-projects-in-visual-studio?view=vs-2019#building-cmake-projects) .

### <a name="macos"></a>macOS

As etapas a seguir mostram como criar o código do dispositivo junto com o código-fonte do SDK do dispositivo C no macOS usando o CMake:

1. Abra o aplicativo de terminal.

1. Use [homebrew](https://homebrew.sh) para instalar todas as dependências:

    ```bash
    brew update
    brew install git cmake pkgconfig openssl ossp-uuid
    ```

1. Verifique se [CMake](https://cmake.org/) é pelo menos a versão **2.8.12**:

    ```bash
    cmake --version
    ```

1. [Correção de patch](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#upgrade-curl-on-mac-os) para a versão mais recente disponível.

1. Na pasta que contém o código gerado, clone o repositório do [SDK do Azure IOT C](https://github.com/Azure/azure-iot-sdk-c) :

    ```bash
    git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
    ```

    Esta operação deve demorar vários minutos a ser concluída.

1. Crie uma pasta chamada `cmake` na pasta que contém o código gerado e navegue até essa pasta.

    ```bash
    mkdir cmake
    cd cmake
    ```

1. Execute os seguintes comandos para usar o CMake para criar o SDK do dispositivo e o stub do código gerado:

    ```bash
    cmake -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DOPENSSL_ROOT_DIR:PATH=/usr/local/opt/openssl ..
    cmake --build .
    ```

1. Depois que a compilação for realizada com sucesso, execute o aplicativo especificando a cadeia de conexão do dispositivo do Hub IoT como parâmetro.

    ```bash
    cd {generated_code_folder_name}/cmake/
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

## <a name="iterate-by-regenerating-the-skeleton-code"></a>Iteração ao regenerar o código de esqueleto

O gerador de código pode gerar o código novamente se você atualizar o DCM ou os arquivos de interface. Supondo que você já tenha gerado o código do dispositivo de um arquivo DCM, para gerar novamente o código:

1. Com a pasta com arquivos do DCM aberta, use **Ctrl + Shift + P** para abrir a paleta de comandos, insira **plug and Play de IOT**e selecione **gerar stub de código de dispositivo**.

1. Escolha o arquivo DCM que você atualizou.

1. Selecione **gerar código novamente para {nome do projeto}** .

1. O gerador de código usa a configuração anterior que você configurou e regenera o código. No entanto, ele não substitui os arquivos que podem conter código de usuário, como `main.c` e `{project_name}_impl.c`.

> [!NOTE]
> Se você atualizar a ID de URN em seu arquivo de interface, ela será tratada como uma nova interface. Quando você gera novamente o código, o gerador de código gera o código para a interface, mas não substitui o original no arquivo de `{project_name}_impl.c`.

## <a name="problems-and-feedback"></a>Problemas e comentários

As ferramentas do Azure IoT são um projeto de código aberto no GitHub. Para quaisquer problemas e solicitações de recursos, você pode [criar um problema no GitHub](https://github.com/microsoft/vscode-azure-iot-tools/issues/new).

## <a name="next-steps"></a>Passos seguintes

Neste artigo de instruções, você aprendeu a usar o Visual Studio e a Visual Studio Code para gerar o esqueleto do código C para implementar o aplicativo do dispositivo. Uma próxima etapa sugerida é aprender a [instalar e usar a ferramenta Azure IOT Explorer](./howto-install-iot-explorer.md) .
