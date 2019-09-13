---
title: Usar o Visual Studio e o Visual Studio Code para criar dispositivos de visualização de IoT Plug and Play | Microsoft Docs
description: Use o Visual Studio e Visual Studio Code para acelerar a criação de modelos de dispositivos IoT Plug and Play e implementar o código do dispositivo.
author: liydu
ms.author: liydu
ms.date: 09/10/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: d68a3f99096ca64b357239f61cf7ff17d6fc3f83
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/12/2019
ms.locfileid: "70935732"
---
# <a name="use-azure-iot-tools-for-visual-studio-code"></a>Usar as ferramentas de IoT do Azure para Visual Studio Code

As ferramentas do Azure IoT para Visual Studio Code fornecem um ambiente integrado para criar modelos de recursos de dispositivo (DCM) e interfaces, publicar em repositórios de modelo e gerar código de esqueleto C para implementar o aplicativo do dispositivo.

Este artigo mostra como:

- Gere o código do dispositivo e o projeto do aplicativo.
- Use o código gerado em seu projeto de dispositivo.
- Iterar gerando novamente o código esqueleto.

Para saber mais sobre como usar o VS Code para desenvolver dispositivos IoT, [https://github.com/microsoft/vscode-iot-workbench](https://github.com/microsoft/vscode-iot-workbench)consulte.

## <a name="prerequisites"></a>Pré-requisitos

Instale o [Visual Studio Code](https://code.visualstudio.com/).

Use as etapas a seguir para instalar o pacote de extensão no VS Code.

1. Em VS Code, selecione a guia **extensões** .
1. Pesquise e instale as **ferramentas de IOT do Azure** no Marketplace.

## <a name="generate-device-code-and-project"></a>Gerar o código do dispositivo e o projeto

Em VS Code, use **Ctrl + Shift + P** para abrir a paleta de comandos, digite **IOT plug and Play**e selecione **gerar stub de código de dispositivo** para configurar o código esqueleto e o tipo de projeto. A lista a seguir descreve cada etapa detalhadamente:

- **O arquivo DCM a ser usado para gerar o código**. Para gerar o esqueleto do código de dispositivo, abra a pasta que contém os arquivos de interface e DCM que ele implementa. Se o gerador de código não conseguir encontrar uma interface exigida por um DCM, ele o baixará do repositório de modelos, conforme necessário.

- **Idioma do código do dispositivo**. Atualmente, o gerador de código dá suporte apenas a ANSI C.

- **Nome do projeto**. O nome do projeto é usado como o nome da pasta para o código gerado e outros arquivos de projeto. A pasta é, por padrão, colocada ao lado do arquivo DCM. Para evitar a cópia manual da pasta de código gerada sempre que você atualizar o DCM e gerar novamente o código do dispositivo, mantenha o arquivo DCM na mesma pasta que a pasta do projeto.

- **Tipo de projeto**. O gerador de código também gera um arquivo de projeto para que você possa integrar o código em seu próprio projeto ou no projeto do SDK do dispositivo. Atualmente, os tipos de projeto com suporte são:

    - **Projeto CMake**: para um projeto de dispositivo que usa [CMake](https://cmake.org/) como sistema de compilação. Essa opção gera um `CMakeLists.txt` arquivo na mesma pasta que o código C.
    - **Projeto MXChip IOT devkit**: para um projeto de dispositivo executado em um dispositivo [MXChip IOT devkit](https://aka.ms/iot-devkit) . Essa opção gera um projeto Arduino que você pode [usar em vs Code](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) ou no IDE Arduino para compilar e executar em um dispositivo IOT devkit.

- **Método para se conectar ao Azure IOT**. Os arquivos gerados também contêm código para configurar o dispositivo para se conectar ao Hub IoT do Azure. Você pode optar por se conectar diretamente ao [Hub IOT do Azure](https://docs.microsoft.com/azure/iot-hub) ou usar o [serviço de provisionamento de dispositivos](https://docs.microsoft.com/azure/iot-dps).

    - **Por meio da cadeia de conexão do dispositivo do Hub IOT**: Especifique a cadeia de conexão do dispositivo para que o aplicativo do dispositivo se conecte diretamente ao Hub IOT.
    - **Por meio da chave simétrica do DPS**: ESPECIFIQUE a **ID do escopo**, a ID de **registro**e a **chave de SAS** para o aplicativo do dispositivo que são necessários para se conectar ao Hub IOT ou IOT central usando o DPS.

O gerador de código tenta usar o DCM e os arquivos de interface localizados na pasta local. Se os arquivos de interface não estiverem na pasta local, o gerador de código o procurará no repositório de modelo público ou no repositório de modelo da empresa. Os [arquivos de interface comuns](./concepts-common-interfaces.md) são armazenados no repositório de modelo público.

Após a conclusão da geração de código, a extensão abre uma nova janela de VS Code com o código. Se você abrir um arquivo gerado como **Main. c**, talvez ache que o IntelliSense relata que ele não pode abrir os arquivos de origem do SDK do c. Para habilitar o IntelliSense e a navegação de código corretos, use as seguintes etapas para incluir a origem do SDK do C:

1. Em VS Code, use **Ctrl + Shift + P** para abrir a paleta de comandos, digite e **selecione CC++/: Edite as configurações (** JSON) para abrir o arquivo **c_cpp_properties. JSON** .

1. Adicione o caminho do SDK do dispositivo na `includePath` seção:

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

Para criar o código do dispositivo junto com o SDK do dispositivo C usando o CMake em um ambiente Linux, como Ubuntu ou Debian:

1. Abra um aplicativo de terminal.

1. Instale o **gcc**, **git**, `cmake`e todas as dependências usando o `apt-get` comando:

    ```sh
    sudo apt-get update
    sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
    ```

    Verifique se a versão `cmake` do está acima de **2.8.12** e se a versão do **gcc** está acima de **4.4.7**.

    ```sh
    cmake --version
    gcc --version
    ```

1. Clone o repositório do [SDK do Azure IOT C](https://github.com/Azure/azure-iot-sdk-c) :

    ```sh
    git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
    ```

    Esta operação deve demorar vários minutos a ser concluída.

1. Copie a pasta que contém o código gerado na pasta raiz do SDK do dispositivo.

1. No vs Code, abra o `CMakeLists.txt` arquivo na pasta raiz do SDK do dispositivo.

1. Adicione a seguinte linha ao final do `CMakeLists.txt` arquivo para incluir a pasta stub do código do dispositivo ao compilar o SDK:

    ```txt
    add_subdirectory({generated_code_folder_name})
    ```

1. Crie uma pasta chamada `cmake` na pasta raiz do SDK do dispositivo e navegue até essa pasta.

    ```sh
    mkdir cmake
    cd cmake
    ```

1. Execute os seguintes comandos para usar o CMake para criar o SDK do dispositivo e o stub do código gerado:

    ```cmd\sh
    cmake -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON ..
    cmake --build .
    ```

1. Depois que a compilação for realizada com sucesso, execute o aplicativo especificando a cadeia de conexão do dispositivo do Hub IoT como parâmetro.

    ```cmd\sh
    cd azure-iot-sdk-c/cmake/{generated_code_folder_name}/
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

### <a name="windows"></a>Windows

Para criar o código do dispositivo junto com o SDK do dispositivo C no Windows usando o CMake e os compiladores C/C++ do Visual Studio na linha de comando, consulte o [início rápido do IOT plug and Play](./quickstart-create-pnp-device.md). As etapas a seguir mostram como criar o código do dispositivo junto com o SDK do dispositivo C como projeto CMake no Visual Studio.

1. Instalar o [Visual Studio 2019 (Comunidade, Professional ou Enterprise)](https://visualstudio.microsoft.com/downloads/) – certifique-se de incluir o componente **Gerenciador de pacotes NuGet** e o desenvolvimento de **desktop com C++**  carga de trabalho.

1. Abra o Visual Studio e, na **página Introdução,** selecione **clonar ou fazer check-out do código**:

1. Em **local do repositório**, clone o repositório do [SDK do Azure IOT C](https://github.com/Azure/azure-iot-sdk-c) :

    ```txt
    https://github.com/Azure/azure-iot-sdk-c
    ```

    Você deve esperar que essa operação demore alguns minutos para ser concluída, pode ver o progresso no painel de **Team Explorer** .

1. Abra o repositório **Azure-IOT-SDK-c** no **Team Explorer**, selecione **branches**, pesquise Branch **público-preview** e confira-o.

    ![Pré-visualização pública](media/howto-develop-with-vs-vscode/vs-public-preview.png)

1. Copie a pasta que contém o código gerado na pasta raiz do SDK do dispositivo.

1. Abra a `azure-iot-sdk-c` pasta no vs.

1. Abra o `CMakeLists.txt` arquivo na pasta raiz do SDK do dispositivo.

1. Adicione a seguinte linha ao final do `CMakeLists.txt` arquivo para incluir a pasta stub do código do dispositivo ao compilar o SDK:

    ```txt
    add_subdirectory({generated_code_folder_name})
    ```

1. Na barra de ferramentas **geral** , localize o menu suspenso **configurações** . Selecione **gerenciar configuração** para adicionar a configuração CMake para seu projeto.

    ![Gerenciar configuração](media/howto-develop-with-vs-vscode/vs-manage-config.png)

1. Nas **configurações do cmake**, adicione uma nova configuração e selecione **x64-Release** como destino.

1. Em **argumentos de comando CMake**, adicione a seguinte linha:

    ```txt
    -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON
    ```

1. Guarde o ficheiro.

1. Na **Gerenciador de soluções**, clique com o `CMakeLists.txt` botão direito do mouse na pasta raiz e selecione **Compilar** no menu de contexto para compilar o SDK do dispositivo e o stub do código gerado.

1. Depois que a compilação for realizada com sucesso, no prompt de comando, execute o aplicativo especificando a cadeia de conexão do dispositivo do Hub IoT como um parâmetro.

    ```cmd
    cd %USERPROFILE%\CMakeBuilds\{workspaceHash}\build\x64-Release\{generated_code_folder_name}\
    {generated_code_project_name}.exe "[IoT Hub device connection string]"
    ```

> [!TIP]
> Para saber mais sobre como usar o CMake no Visual Studio, consulte [Compilar projeto CMake](https://docs.microsoft.com/cpp/build/cmake-projects-in-visual-studio?view=vs-2019#building-cmake-projects) .

### <a name="macos"></a>macOS

As etapas a seguir mostram como criar o código do dispositivo junto com o SDK do dispositivo C no macOS usando o CMake:

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

1. Clone o repositório do [SDK do Azure IOT C](https://github.com/Azure/azure-iot-sdk-c) :

    ```bash
    git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
    ```

    Esta operação deve demorar vários minutos a ser concluída.

1. Copie a pasta que contém o código gerado na pasta raiz do SDK do dispositivo.

1. No vs Code, abra o `CMakeLists.txt` arquivo na pasta raiz do SDK do dispositivo.

1. Adicione a seguinte linha ao final do `CMakeLists.txt` arquivo para incluir a pasta stub do código do dispositivo ao compilar o SDK:

    ```txt
    add_subdirectory({generated_code_folder_name})
    ```

1. Crie uma pasta chamada `cmake` na pasta raiz do SDK do dispositivo e navegue até essa pasta.

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
    cd azure-iot-sdk-c/cmake/{generated_code_folder_name}/
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

## <a name="iterate-by-regenerating-the-skeleton-code"></a>Iteração ao regenerar o código de esqueleto

O gerador de código pode gerar o código novamente se você atualizar o DCM ou os arquivos de interface. Supondo que você já tenha gerado o código do dispositivo de um arquivo DCM, para gerar novamente o código:

1. Com a pasta com arquivos do DCM aberta, use **Ctrl + Shift + P** para abrir a paleta de comandos, insira **plug and Play de IOT**e selecione **gerar stub de código de dispositivo**.

1. Escolha o arquivo DCM que você atualizou.

1. Selecione **gerar código novamente para {nome do projeto}** .

1. O gerador de código usa a configuração anterior que você configurou e regenera o código. No entanto, ele não substitui os arquivos que podem conter código de `main.c` usuário `{project_name}_impl.c`, como e.

> [!NOTE]
> Se você atualizar a ID de URN em seu arquivo de interface, ela será tratada como uma nova interface. Quando você gera novamente o código, o gerador de código gera código para interface, mas não substitui o original no `{project_name}_impl.c` arquivo.

## <a name="problems-and-feedback"></a>Problemas e comentários

As ferramentas do Azure IoT são um projeto de código aberto no GitHub. Para quaisquer problemas e solicitações de recursos, você pode [criar um problema no GitHub](https://github.com/microsoft/vscode-azure-iot-tools/issues/new).

## <a name="next-steps"></a>Passos Seguintes

Neste artigo de instruções, você aprendeu a usar o Visual Studio e a Visual Studio Code para gerar o esqueleto do código C para implementar o aplicativo do dispositivo. Uma próxima etapa sugerida é aprender a [instalar e usar a ferramenta Azure IOT Explorer](./howto-install-iot-explorer.md) .
