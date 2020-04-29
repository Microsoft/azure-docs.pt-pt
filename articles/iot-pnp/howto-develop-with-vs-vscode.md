---
title: Use visual studio e visual studio code para construir dispositivos de pré-visualização IoT Plug e Play Microsoft Docs
description: Utilize o Visual Studio e o Visual Studio Code para acelerar a autoria dos modelos de dispositivos IoT Plug e Play e implementar o código do dispositivo.
author: liydu
ms.author: liydu
ms.date: 12/26/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 91e7b1c0be9a38c3d79440f07d944d182980dc10
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80159239"
---
# <a name="use-visual-studio-and-visual-studio-code-to-build-iot-plug-and-play-devices"></a>Use visual studio e código de estúdio visual para construir dispositivos IoT Plug e Play

O Código de Estúdio Azure IoT fornece um ambiente integrado aos modelos de capacidade de dispositivos de autor (DCM) e interfaces, publica para repositórios de modelos e gera código C esqueleto para implementar a aplicação do dispositivo.

Este artigo mostra-lhe como:

- Gerar código de dispositivo e projeto de aplicação.
- Utilize o código gerado no projeto do seu dispositivo.
- Iterado regenerando o código do esqueleto.

Para saber mais sobre a utilização do Código [https://github.com/microsoft/vscode-iot-workbench](https://github.com/microsoft/vscode-iot-workbench)VS para desenvolver dispositivos IoT, consulte .

## <a name="prerequisites"></a>Pré-requisitos

Instale o Código do [Estúdio Visual](https://code.visualstudio.com/).

Utilize os seguintes passos para instalar o pacote de extensão no Código VS.

1. No Código VS, selecione o separador **Extensões.**
1. Procure e **instale ferramentas Azure IoT** a partir do mercado.

## <a name="generate-device-code-and-project"></a>Gerar código e projeto do dispositivo

No Código VS, utilize **o Ctrl+Shift+P** para abrir a paleta de comando, introduzir plug **e reproduzir ioT,** e selecionar **Generate Device Code Stub** para configurar o código do esqueleto e o tipo de projeto. A seguinte lista descreve cada passo em detalhe:

- **Ficheiro DCM a utilizar para gerar o código**. Para gerar o código do dispositivo esqueleto, abra a pasta que contém o DCM e os ficheiros de interface que implementa. Se o gerador de código não conseguir encontrar uma interface que um DCM necessita, descarrega-o do repositório do modelo, conforme necessário.

- **Linguagem do código do dispositivo**. Atualmente, o gerador de código somente aPENAS o ANSI C.

- **Nome do projeto.** O nome do projeto é usado como nome de pasta para o código gerado e outros ficheiros do projeto. A pasta é, por defeito, colocada ao lado do ficheiro DCM. Para evitar ter de copiar manualmente a pasta de código gerada sempre que atualizar o seu DCM e regenerar o código do dispositivo, mantenha o ficheiro DCM na mesma pasta que a pasta do projeto.

- **Método de ligação ao Azure IoT**. Os ficheiros gerados também contêm código para configurar o dispositivo para ligar ao Hub Azure IoT. Pode optar por ligar-se diretamente ao [Hub Azure IoT](https://docs.microsoft.com/azure/iot-hub) ou utilizar o Serviço de [Provisionamento de Dispositivos](https://docs.microsoft.com/azure/iot-dps).

    - Através da cadeia de ligação do **dispositivo IoT Hub:** especifique a cadeia de ligação do dispositivo para a aplicação do dispositivo ligar diretamente ao IoT Hub.
    - Através da **tecla simétrica DPS:** especifique o âmbito de **identificação,** **chave simétrica** e ID do **dispositivo** para a aplicação do dispositivo que é necessário para ligar ao IoT Hub ou IoT Central utilizando DPS.

- **Tipo de projeto.** O gerador de código também gera um projeto CMake ou Arduino. Atualmente, os tipos de projetos suportados são:

    - **CMake Project no Windows**: para um projeto de dispositivo que utiliza [o CMake](https://cmake.org/) como sistema de construção no Windows. Esta opção `CMakeLists.txt` gera com configurações SDK do dispositivo na mesma pasta que o código C.
    - **CMake Project on Linux**: para um projeto de dispositivo que usa [o CMake](https://cmake.org/) como sistema de construção em Linux. Esta opção `CMakeLists.txt` gera com configurações SDK do dispositivo na mesma pasta que o código C.
    - **Projeto MXChip IoT DevKit**: para um projeto de dispositivo que funciona num dispositivo [MXChip IoT DevKit.](https://aka.ms/iot-devkit) Esta opção gera um projeto Arduino que pode [utilizar no Código VS](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) ou no Arduino IDE para construir e executar num dispositivo IoT DevKit.

- **Tipo SDK**do dispositivo . Se selecionar cMake como tipo de projeto, este é o passo para configurar como o código `CMakeLists.txt`gerado incluirá o dispositivo Azure IoT C SDK no :

    - **Via Código Fonte**: o código gerado baseia-se no [código fonte SDK](https://github.com/Azure/azure-iot-sdk-c) do dispositivo para incluir e construir juntamente com ele. Isto é recomendado quando tiver personalizado o código fonte SDK do dispositivo.
    - **Via Vcpkg**: o código gerado depende do [dispositivo SDK Vcpkg](https://github.com/microsoft/vcpkg/tree/master/ports/azure-iot-sdk-c) para incluir e construir em conjunto com ele. Esta é a forma recomendada para dispositivos que executam Windows, Linux ou macOS.

    > [!NOTE]
    > O suporte macOS para o dispositivo SDK Vcpkg do dispositivo Azure IoT C está a funcionar em andamento.

O gerador de códigotenta utilizar ficheiros DCM e interface localizados na pasta local. Se os ficheiros de interface não estiverem na pasta local, o gerador de código procura-os no repositório de modelos públicos ou no repositório do modelo da empresa. [Os ficheiros](./concepts-common-interfaces.md) de interface comuns são armazenados no repositório de modelos públicos.

Após o acabamento da geração de códigos, a extensão abre uma nova janela de Código VS com o código. Se abrir um ficheiro gerado, como **o main.c,** poderá descobrir que o IntelliSense informa que não pode abrir os ficheiros de origem C SDK. Para ativar o IntelliSense correto e a navegação de código, utilize os seguintes passos para incluir a fonte C SDK:

1. No Código VS, utilize **o Ctrl+Shift+P** para abrir a paleta de comando, escrever e selecionar **C/C++: Configurações de Edição (JSON)** para abrir o ficheiro **c_cpp_properties.json.**

1. Adicione o caminho do dispositivo SDK na `includePath` secção:

    ```json
    "includePath": [
        "${workspaceFolder}/**",
        "{path_of_device_c_sdk}/**"
    ]
    ```

1. Guarde o ficheiro.

## <a name="use-the-generated-code"></a>Use o código gerado

As seguintes instruções descrevem como usar o código gerado no seu próprio projeto de dispositivo em diferentes plataformas de máquinas de desenvolvimento. As instruções pressupõem que está a usar uma cadeia de ligação do dispositivo IoT Hub com o código gerado:

### <a name="linux"></a>Linux

Para construir o código do dispositivo juntamente com o dispositivo C SDK Vcpkg utilizando CMake num ambiente Linux como Ubuntu ou Debian:

1. Abra uma aplicação terminal.

1. Instale **GCC,** `cmake` **Git,** e todas `apt-get` as dependências utilizando o comando:

    ```bash
    sudo apt-get update
    sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
    ```

    Verifique se `cmake` a versão é superior a **2.8.12** e a versão do **CcG** está acima de **4.4.7**.

    ```bash
    cmake --version
    gcc --version
    ```

1. Instale vcpkg:

    ```bash
    git clone https://github.com/Microsoft/vcpkg.git
    cd vcpkg

    ./bootstrap-vcpkg.sh
    ```

    Em seguida, para ligar a [integração](https://github.com/microsoft/vcpkg/blob/master/docs/users/integration.md)em todo o utilizador, executar:

    ```bash
    ./vcpkg integrate install
    ```

1. Instale o dispositivo SDK Vcpkg do dispositivo Azure IoT C:

    ```bash
    ./vcpkg install azure-iot-sdk-c[public-preview,use_prov_client]
    ```

1. Criar `cmake` um subdiretório na pasta contém o código gerado e navegar para essa pasta:

    ```bash
    mkdir cmake
    cd cmake
    ```

1. Executar os seguintes comandos para utilizar o CMake para construir o dispositivo SDK e o código gerado:

    ```bash
    cmake .. -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}/scripts/buildsystems/vcpkg.cmake"

    cmake --build .
    ```

1. Depois de a construção ter sucesso, execute a aplicação especificando a cadeia de ligação do dispositivo IoT Hub como parâmetro.

    ```bash
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

### <a name="windows"></a>Windows

Para construir o código do dispositivo juntamente com o dispositivo C SDK no Windows utilizando os compiladores CMake e Visual Studio C/C++ na linha de comando, consulte o [IoT Plug e reprodução rápida](./quickstart-create-pnp-device-windows.md). Os seguintes passos mostram-lhe como construir o código do dispositivo juntamente com o dispositivo C SDK Vcpkg como projeto CMake em Estúdio Visual.

1. Siga os passos no [arranque rápido](https://docs.microsoft.com/azure/iot-pnp/quickstart-create-pnp-device-windows#prepare-the-development-environment) para instalar o dispositivo Azure IoT SDK para C via Vcpkg.

1. Instale o [Visual Studio 2019 (Comunidade, Profissional ou Empresa)](https://visualstudio.microsoft.com/downloads/) - certifique-se de que inclui a componente de **gestor de pacotes NuGet** e o Desenvolvimento do Ambiente de Trabalho com carga de trabalho **C+++**

1. Open Visual Studio, escolha File > Open `CMakeLists.txt` > **CMake...** para abrir a pasta contém código gerado.

1. Na barra de ferramentas **geral,** encontre as **Configurações** a cair. Selecione **'Gerir configuração'** para adicionar a definição cMake para o seu projeto.

    ![Gerir a configuração](media/howto-develop-with-vs-vscode/vs-manage-config.png)

1. Nas **Definições CMake,** adicione uma nova configuração e selecione **x86-Debug** como alvo.

1. Em **CMake Command Arguments,** adicione a seguinte linha:

    ```txt
    -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON
    ```

1. Guarde o ficheiro.

1. Mude para **x86-Debug** nas **Configurações** dropdown. Precisa de alguns segundos para o CMake gerar a cache para ele. Veja a janela de saída para ver o progresso.

    ![Saída de CMake](media/howto-develop-with-vs-vscode/vs-cmake-output.png)

1. No **Solution Explorer,** clique à `CMakeLists.txt` direita na pasta raiz e selecione **Construir** a partir do menu de contexto para construir o código gerado com o dispositivo SDK.

1. Depois da construção ter sucesso, no pedido de comando, executar a aplicação especificando a cadeia de ligação do dispositivo IoT Hub como parâmetro.

    ```cmd
    .\out\build\x86-Debug\{generated_code_project_name}.exe "[IoT Hub device connection string]"
    ```

> [!TIP]
> Para saber mais sobre a utilização do CMake no Estúdio Visual, consulte build [cMake project](https://docs.microsoft.com/cpp/build/cmake-projects-in-visual-studio?view=vs-2019#building-cmake-projects) .

### <a name="macos"></a>macOS

Os seguintes passos mostram como construir o código do dispositivo juntamente com o código fonte C SDK do dispositivo no macOS utilizando o CMake:

1. Aplicação de terminal aberto.

1. Utilize [homebrew](https://homebrew.sh) para instalar todas as dependências:

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

1. Crie uma `cmake` pasta chamada sob a pasta que contenha o código gerado e navegue para essa pasta.

    ```bash
    mkdir cmake
    cd cmake
    ```

1. Executar os seguintes comandos para utilizar o CMake para construir o dispositivo SDK e o código gerado:

    ```bash
    cmake -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DOPENSSL_ROOT_DIR:PATH=/usr/local/opt/openssl ..
    cmake --build .
    ```

1. Depois de a construção ter sucesso, execute a aplicação especificando a cadeia de ligação do dispositivo IoT Hub como parâmetro.

    ```bash
    cd {generated_code_folder_name}/cmake/
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

## <a name="iterate-by-regenerating-the-skeleton-code"></a>Iterado regenerando o código do esqueleto

O gerador de código pode regenerar o código se atualizar os ficheiros DCM ou interface. Assumindo que já gerou o código do seu dispositivo a partir de um ficheiro DCM, para regenerar o código:

1. Com a pasta com ficheiros DCM abertos, utilize **ctrl+Shift+P** para abrir a paleta de comando, **introduza plug e reprodução IoT**e selecione **Generate Device Code Stub**.

1. Escolha o ficheiro DCM que atualizou.

1. Selecione **Re-gerar código para {nome**do projeto} .

1. O gerador de código utiliza a definição anterior configurada e regenera o código. No entanto, não substitui os ficheiros que `main.c` podem `{project_name}_impl.c`conter código de utilizador, tais como e .

> [!NOTE]
> Se atualizar o ID URN no ficheiro da interface, é tratado como uma nova interface. Quando regera o código, o gerador de código gera código para interface, `{project_name}_impl.c` mas não substitui o original no ficheiro.

## <a name="problems-and-feedback"></a>Problemas e Feedback

A Azure IoT Tools é um projeto de código aberto no GitHub. Para quaisquer problemas e pedidos de funcionalidades, pode [criar um problema no GitHub](https://github.com/microsoft/vscode-azure-iot-tools/issues/new).

## <a name="next-steps"></a>Passos seguintes

Neste artigo de como fazer, aprendeu a usar o Visual Studio e o Visual Studio Code para gerar código C esqueleto para implementar a aplicação do dispositivo. Um próximo passo sugerido é aprender a instalar e usar a ferramenta [exploradora Azure IoT.](./howto-install-iot-explorer.md)
