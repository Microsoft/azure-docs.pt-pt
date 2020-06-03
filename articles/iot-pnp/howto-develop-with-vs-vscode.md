---
title: Use o Visual Studio e o Visual Studio Code para construir dispositivos IoT Plug e Play Preview Microsoft Docs
description: Utilize o Visual Studio e o Visual Studio Code para acelerar os modelos de dispositivo ioT plug e play e implementar o código do dispositivo.
author: liydu
ms.author: liydu
ms.date: 12/26/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 038d9ff39f388d1ef7b09b951c09dbe3420858b7
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/02/2020
ms.locfileid: "84298232"
---
# <a name="use-visual-studio-and-visual-studio-code-to-build-iot-plug-and-play-devices"></a>Use o Visual Studio e o Visual Studio Code para construir dispositivos IoT Plug e Play

O Azure IoT Tools for Visual Studio Code fornece um ambiente integrado aos modelos de capacidade do dispositivo de autor (DCM) e interfaces, publica para repositórios de modelos e gera código C esqueleto para implementar a aplicação do dispositivo.

Este artigo mostra-lhe como:

- Gere o código do dispositivo e o projeto de aplicação.
- Utilize o código gerado no projeto do seu dispositivo.
- Iterate regenerando o código do esqueleto.

Para saber mais sobre a utilização do Código VS para desenvolver dispositivos IoT, consulte [https://github.com/microsoft/vscode-iot-workbench](https://github.com/microsoft/vscode-iot-workbench) .

## <a name="prerequisites"></a>Pré-requisitos

Instale [o Código do Estúdio Visual](https://code.visualstudio.com/).

Utilize os seguintes passos para instalar o pacote de extensões no Código VS.

1. No código VS, selecione **Separador extensões.**
1. Procure e instale **ferramentas Azure IoT** do mercado.

## <a name="generate-device-code-and-project"></a>Gerar código e projeto de dispositivo

No Código VS, utilize **ctrl+Shift+P** para abrir a paleta de comando, **introduza ioT Plug e Play**, e selecione **'Gerar Código de Dispositivo' para** configurar o código de esqueleto e o tipo de projeto. A seguinte lista descreve cada passo em detalhe:

- **Ficheiro DCM a ser utilizado para gerar o código**. Para gerar o código do dispositivo esqueleto, abra a pasta que contém o DCM e os ficheiros de interface que implementa. Se o gerador de código não conseguir encontrar uma interface que um DCM exija, descarrega-a do repositório de modelos, conforme necessário.

- **Linguagem de código do dispositivo**. Atualmente, o gerador de código só suporta ANSI C.

- **Nome do projeto**. O nome do projeto é usado como nome de pasta para o código gerado e outros ficheiros do projeto. A pasta é, por defeito, colocada ao lado do ficheiro DCM. Para evitar ter de copiar manualmente a pasta de código gerada sempre que atualizar o seu DCM e regenerar o código do dispositivo, mantenha o ficheiro DCM na mesma pasta que a pasta do projeto.

- **Método de ligação ao Azure IoT**. Os ficheiros gerados também contêm código para configurar o dispositivo para ligar ao Azure IoT Hub. Pode optar por ligar-se diretamente ao [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) ou utilizar o [Serviço de Provisionamento de Dispositivos](https://docs.microsoft.com/azure/iot-dps).

    - **Cabo de ligação do dispositivo IoT Hub**: especifique a cadeia de ligação do dispositivo para a aplicação do dispositivo ligar diretamente ao IoT Hub.
    - **Através da tecla simétrica DPS:** especifique o **ID Scope**, **a Chave Simétrica** e o **ID** do dispositivo para a aplicação do dispositivo que são necessários para ligar ao IoT Hub ou à IoT Central utilizando o DPS.

- **Tipo de projeto**. O gerador de código também gera um projeto CMake ou Arduino. Atualmente, os tipos de projeto suportados são:

    - **CMake Project on Windows**: para um projeto de dispositivo que utiliza [o CMake](https://cmake.org/) como sistema de construção no Windows. Esta opção gera `CMakeLists.txt` com configurações SDK do dispositivo na mesma pasta que o código C.
    - **Projeto CMake no Linux:** para um projeto de dispositivo que usa [o CMake](https://cmake.org/) como sistema de construção em Linux. Esta opção gera `CMakeLists.txt` com configurações SDK do dispositivo na mesma pasta que o código C.
    - **Projeto MXChip IoT DevKit**: para um projeto de dispositivo que funciona com um dispositivo [MXChip IoT DevKit.](https://aka.ms/iot-devkit) Esta opção gera um projeto Arduino que pode [utilizar no Código VS](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) ou no IDE Arduino para construir e executar num dispositivo IoT DevKit.

- **Dispositivo tipo SDK**. Se selecionar o CMake como tipo de projeto, este é o passo para configurar como o código gerado incluirá o dispositivo Azure IoT C SDK no `CMakeLists.txt` :

    - **Via Código Fonte:** o código gerado baseia-se no [código fonte SDK](https://github.com/Azure/azure-iot-sdk-c) do dispositivo para incluir e construir juntamente com ele. Isto é recomendado quando tiver personalizado o código fonte SDK do dispositivo.
    - **Via Vcpkg:** o código gerado baseia-se no [dispositivo SDK Vcpkg](https://github.com/microsoft/vcpkg/tree/master/ports/azure-iot-sdk-c) para incluir e construir juntamente com ele. Esta é a forma recomendada para dispositivos com windows, Linux ou macOS.

    > [!NOTE]
    > O suporte do macOS para o dispositivo Azure IoT C SDK Vcpkg está a funcionar.

O gerador de código tenta utilizar ficheiros DCM e interfaces localizados na pasta local. Se os ficheiros de interface não estiverem na pasta local, o gerador de códigos procura-os no repositório de modelos públicos ou no repositório de modelos da empresa. [Os ficheiros de interface comuns](./concepts-common-interfaces.md) são armazenados no repositório de modelos públicos.

Após o fim da geração de código, a extensão abre uma nova janela do Código VS com o código. Se abrir um ficheiro gerado como **o main.c,** poderá descobrir que o IntelliSense informa que não pode abrir os ficheiros de origem C SDK. Para ativar o IntelliSense correto e a navegação por código, utilize os seguintes passos para incluir a fonte C SDK:

1. No Código VS, utilize **Ctrl+Shift+P** para abrir a paleta de comando, escreva e selecione **C/C++: Editar Configurações (JSON)** para abrir o ficheiro **c_cpp_properties.json.**

1. Adicione o caminho do dispositivo SDK na `includePath` secção:

    ```json
    "includePath": [
        "${workspaceFolder}/**",
        "{path_of_device_c_sdk}/**"
    ]
    ```

1. Guarde o ficheiro.

## <a name="use-the-generated-code"></a>Utilize o código gerado

As seguintes instruções descrevem como utilizar o código gerado no seu próprio projeto de dispositivo em diferentes plataformas de máquinas de desenvolvimento. As instruções pressupõem que está a usar uma cadeia de ligação do dispositivo IoT Hub com o código gerado:

### <a name="linux"></a>Linux

Para construir o código do dispositivo juntamente com o dispositivo C SDK Vcpkg utilizando CMake em um ambiente Linux como Ubuntu ou Debian:

1. Abra uma aplicação terminal.

1. Instale **GCC,** **Git** `cmake` , e todas as dependências utilizando o `apt-get` comando:

    ```bash
    sudo apt-get update
    sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
    ```

    Verifique se a versão é `cmake` superior a **2.8.12** e a versão do **GCC** é superior a **4.4.7**.

    ```bash
    cmake --version
    gcc --version
    ```

1. Instalar Vcpkg:

    ```bash
    git clone https://github.com/Microsoft/vcpkg.git
    cd vcpkg

    ./bootstrap-vcpkg.sh
    ```

    Em seguida, para ligar a [integração](https://github.com/microsoft/vcpkg/blob/master/docs/users/integration.md)em todo o utilizador, corra:

    ```bash
    ./vcpkg integrate install
    ```

1. Instalar o dispositivo Azure IoT C SDK Vcpkg:

    ```bash
    ./vcpkg install azure-iot-sdk-c[public-preview,use_prov_client]
    ```

1. Criar um `cmake` subdirecional na pasta contém o bloco de códigos gerado e navegar para essa pasta:

    ```bash
    mkdir cmake
    cd cmake
    ```

1. Executar os seguintes comandos para usar o CMake para construir o dispositivo SDK e o código gerado:

    ```bash
    cmake .. -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}/scripts/buildsystems/vcpkg.cmake"

    cmake --build .
    ```

1. Após a construção ter sucesso, execute a aplicação especificando a cadeia de ligação do dispositivo IoT Hub como parâmetro.

    ```bash
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

### <a name="windows"></a>Windows

Para construir o código do dispositivo juntamente com o dispositivo C SDK no Windows utilizando cMake e os compiladores Visual Studio C/C++ na linha de comando, consulte o [IoT Plug e play quickstart](./quickstart-create-pnp-device-windows.md). Os passos seguintes mostram-lhe como construir o código do dispositivo juntamente com o dispositivo C SDK Vcpkg como projeto CMake no Visual Studio.

1. Siga os passos no [arranque rápido](https://docs.microsoft.com/azure/iot-pnp/quickstart-create-pnp-device-windows#prepare-the-development-environment) para instalar o dispositivo Azure IoT SDK para C via Vcpkg.

1. Instale [o Visual Studio 2019 (Comunidade, Profissional ou Enterprise)](https://visualstudio.microsoft.com/downloads/) - certifique-se de que inclui o componente **de gestor de pacotes NuGet** e o Desktop Development com carga de trabalho **C++.**

1. Abra o Estúdio Visual, escolha **O Ficheiro > Open > CMake...** para abrir a pasta contém código `CMakeLists.txt` gerado.

1. Na barra de ferramentas **Geral,** encontre as **configurações** para descer. **Selecione 'Conseguir' Configuração** para adicionar a definição de CMake para o seu projeto.

    ![Gerir a configuração](media/howto-develop-with-vs-vscode/vs-manage-config.png)

1. Nas **Definições CMake,** adicione uma nova configuração e selecione **x86-Debug** como alvo.

1. Nos **Argumentos de Comando CMake,** adicione a seguinte linha:

    ```txt
    -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON
    ```

1. Guarde o ficheiro.

1. Mude para **x86-Debug** no dropdown **de Configurações.** Precisa de alguns segundos para o CMake gerar a cache para ele. Veja a janela de saída para ver o progresso.

    ![Saída CMake](media/howto-develop-with-vs-vscode/vs-cmake-output.png)

1. No **Solution Explorer**, clique à direita `CMakeLists.txt` na pasta raiz e selecione **Construir** a partir do menu de contexto para construir o código gerado com o dispositivo SDK.

1. Após a construção ter sucesso, na localização do comando, executar a aplicação especificando a cadeia de ligação do dispositivo IoT Hub como parâmetro.

    ```cmd
    .\out\build\x86-Debug\{generated_code_project_name}.exe "[IoT Hub device connection string]"
    ```

> [!TIP]
> Para saber mais sobre a utilização do CMake no Visual Studio, consulte [o projeto Build CMake.](https://docs.microsoft.com/cpp/build/cmake-projects-in-visual-studio?view=vs-2019#building-cmake-projects)

### <a name="macos"></a>macOS

Os seguintes passos mostram-lhe como construir o código do dispositivo juntamente com o dispositivo C SDK código fonte no macOS usando CMake:

1. Aplicação de terminal aberto.

1. Use [o Homebrew](https://brew.sh) para instalar todas as dependências:

    ```bash
    brew update
    brew install git cmake pkgconfig openssl ossp-uuid
    ```

1. Verifique se [o CMake](https://cmake.org/) é pelo menos a versão **2.8.12:**

    ```bash
    cmake --version
    ```

1. [Patch CURL](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#upgrade-curl-on-mac-os) para a versão mais recente disponível.

1. Na pasta que contém o código gerado, clone o repositório [Azure IoT C SDK:](https://github.com/Azure/azure-iot-sdk-c)

    ```bash
    git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
    ```

    Esta operação deve demorar vários minutos a ser concluída.

1. Crie uma pasta chamada `cmake` por baixo da pasta que contenha o código gerado e navegue nessa pasta.

    ```bash
    mkdir cmake
    cd cmake
    ```

1. Executar os seguintes comandos para usar o CMake para construir o dispositivo SDK e o código gerado:

    ```bash
    cmake -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DOPENSSL_ROOT_DIR:PATH=/usr/local/opt/openssl ..
    cmake --build .
    ```

1. Após a construção ter sucesso, execute a aplicação especificando a cadeia de ligação do dispositivo IoT Hub como parâmetro.

    ```bash
    cd {generated_code_folder_name}/cmake/
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

## <a name="iterate-by-regenerating-the-skeleton-code"></a>Iterate regenerando o código do esqueleto

O gerador de códigos pode regenerar o código se atualizar os seus ficheiros DCM ou interface. Assumindo que já gerou o seu código de dispositivo a partir de um ficheiro DCM, para regenerar o código:

1. Com a pasta com ficheiros DCM aberta, utilize **ctrl+Shift+P** para abrir a paleta de comando, **introduza ioT Plug e Play**, e selecione **'Gerar Código de Dispositivo Stub'**.

1. Escolha o ficheiro DCM atualizado.

1. Selecione **Re-generate code for {project name}**.

1. O gerador de código utiliza a configuração anterior que configura e regenera o código. No entanto, não substitui os ficheiros que podem conter códigos de utilizador tais como `main.c` `{project_name}_impl.c` .

> [!NOTE]
> Se atualizar o id URN no seu ficheiro de interface, é tratado como uma nova interface. Quando recova o código, o gerador de código gera código para interface, mas não substitui o original no `{project_name}_impl.c` ficheiro.

## <a name="problems-and-feedback"></a>Problemas e Feedback

Azure IoT Tools é um projeto de origem aberta no GitHub. Para quaisquer problemas e pedidos de funcionalidades, pode [criar um problema no GitHub](https://github.com/microsoft/vscode-azure-iot-tools/issues/new).

## <a name="next-steps"></a>Passos seguintes

Neste artigo de como fazer, aprendeu a usar o Visual Studio e o Visual Studio Code para gerar código C de esqueleto para implementar a aplicação do dispositivo. Um próximo passo sugerido é aprender a instalar e usar a ferramenta [exploradora Azure IoT.](./howto-install-iot-explorer.md)
