---
title: Tutorial - Ligue um dispositivo IoT Plug and Play (pré-visualização) ao Azure IoT Central
description: Este tutorial mostra como usar um modelo de capacidade de dispositivo para gerar o código do dispositivo. Em seguida, execute o código do dispositivo, consulte o dispositivo conectar-se ao seu aplicativo IoT Central e use as exibições geradas automaticamente.
author: dominicbetts
ms.author: dobett
ms.date: 12/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: ca2ac62892d1c8d438cc37bffcbfede14058bc23
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77027721"
---
# <a name="tutorial-use-a-device-capability-model-to-create-an-iot-plug-and-play-preview-device-and-connect-it-to-your-iot-central-application"></a>Tutorial: Utilize um modelo de capacidade do dispositivo para criar um dispositivo IoT Plug and Play (pré-visualização) e conectá-lo à sua aplicação IoT Central

Um modelo de capacidade de _dispositivo_ (DCM) descreve as capacidades de um dispositivo [IoT Plug and Play (pré-visualização).](../../iot-pnp/overview-iot-plug-and-play.md) IoT Central pode usar um DCM para criar um modelo de dispositivo e visualizações para um dispositivo quando o dispositivo se conecta pela primeira vez.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Utilize o Código do Estúdio Visual para criar um dispositivo IoT Plug e Play (pré-visualização) utilizando um DCM.
> * Execute o código do dispositivo no Windows e veja se ele se conecta ao seu aplicativo IoT Central.
> * Exiba a telemetria simulada que o dispositivo envia.

## <a name="prerequisites"></a>Pré-requisitos

Complete a [aplicação Create a Azure IoT Central](./quick-deploy-iot-central.md) para criar uma aplicação IoT Central utilizando o modelo de **aplicação Personalizado > Custom.**

Para concluir este tutorial, você precisa instalar o seguinte software em seu computador local:

* [Ferramentas de Build para Visual Studio](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16) com  **C++ ferramentas de compilação** e cargas de trabalho de **componente do Gerenciador de pacotes NuGet** . Ou, se você já tiver o [Visual Studio (Community, Professional ou Enterprise)](https://visualstudio.microsoft.com/downloads/) 2019, 2017 ou 2015 com as mesmas cargas de trabalho instaladas.
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/) – quando você instala o **CMake**, selecione a opção **Adicionar CMake ao caminho do sistema**.
* [Visual Studio Code](https://code.visualstudio.com/).
* [Node.js](https://nodejs.org/)
* O utilitário `dps-keygen`:

    ```cmd/sh
    npm i -g dps-keygen
    ```

### <a name="install-azure-iot-tools"></a>Instalar as ferramentas do Azure IoT

Use as etapas a seguir para instalar o pacote de extensão das ferramentas do Azure IoT no VS Code:

1. Em VS Code, selecione a guia **extensões** .
1. Pesquise pelas **Ferramentas do Azure IOT**.
1. Selecione **Instalar**.

## <a name="prepare-the-development-environment"></a>Preparar o ambiente de desenvolvimento

Neste tutorial, você usará o Gerenciador de biblioteca do [Vcpkg](https://github.com/microsoft/vcpkg) para instalar o SDK do dispositivo do Azure IOT C em seu ambiente de desenvolvimento.

1. Abra uma linha de comandos. Execute o seguinte comando para instalar o Vcpkg:

    ```cmd
    git clone https://github.com/Microsoft/vcpkg.git
    cd vcpkg

    .\bootstrap-vcpkg.bat
    ```

    Em seguida, para conectar a [integração](https://github.com/microsoft/vcpkg/blob/master/docs/users/integration.md)de todo o usuário, execute o comando a seguir. Na primeira vez que você executar esse comando, ele exigirá direitos administrativos:

    ```cmd
    .\vcpkg.exe integrate install
    ```

1. Instalar o SDK do dispositivo do Azure IoT C Vcpkg:

    ```cmd
    .\vcpkg.exe install azure-iot-sdk-c[public-preview,use_prov_client]
    ```

## <a name="generate-device-key"></a>Gerar chave do dispositivo

Para conectar um dispositivo a um aplicativo IoT Central, você precisa de uma chave de dispositivo. Para gerar uma chave de dispositivo:

1. Inicie sessão na aplicação IoT Central que criou utilizando o modelo de **aplicação Custom app > Preview** no Quickstart da [aplicação Create a Azure IoT Central.](./quick-deploy-iot-central.md)

1. Vá para a página **Administração** e selecione **conexão do dispositivo**.

1. Anote o **escopo da ID** e a **chave primária** que você vê ao selecionar **Exibir chaves**. Usa estes valores mais tarde neste tutorial.

    ![Conexão do dispositivo](./media/tutorial-connect-pnp-device/device-connection.png)

1. Abra um prompt de comando e execute o seguinte comando para gerar uma chave de dispositivo:

    ```cmd/sh
    dps-keygen -di:mxchip-001 -mk:{Primary Key from previous step}
    ```

    Anote a _chave do dispositivo_gerada, você usará esse valor em uma etapa posterior neste tutorial.

## <a name="download-your-model"></a>Baixe seu modelo

Neste tutorial, você usa o DCM público para um dispositivo MxChip IoT DevKit. Você não precisa de um dispositivo DevKit real para executar o código, neste tutorial, você compila o código para ser executado no Windows.

1. Crie uma pasta chamada `central_app` e abra-a no Código VS.

1. Use **Ctrl + Shift + P** para abrir a paleta de comandos, insira **plug and Play de IOT**e selecione **abrir repositório de modelos**. Selecione **repositório público**. VS Code mostra uma lista de DCMs no repositório de modelo público.

1. Selecione o **DCM MXChip IoT DevKit** com id `urn:mxchip:mxchip_iot_devkit:1`. Em seguida, selecione **baixar**. Tem agora uma cópia do DCM na pasta `central_app`.

![Repositório de modelos e DCM](./media/tutorial-connect-pnp-device/public-repository.png)

> [!NOTE]
> Para trabalhar com IoT Central, o modelo de funcionalidade do dispositivo deve ter todas as interfaces definidas embutidas no mesmo arquivo.

## <a name="generate-the-c-code-stub"></a>Gerar o stub do código C

Agora você tem o **MXChip IOT devkit** DCM e suas interfaces associadas, você pode gerar o código do dispositivo que implementa o modelo. Para gerar o stub do código C no VS Code:

1. Com a pasta com arquivos do DCM aberta, use **Ctrl + Shift + P** para abrir a paleta de comandos, insira **plug and Play de IOT**e selecione **gerar stub de código de dispositivo**.

    > [!NOTE]
    > Na primeira vez que você usar o utilitário gerador de código de Plug and Play IoT, levará alguns segundos para baixar.

1. Selecione o arquivo **MXChip IOT devkit** DCM que você acabou de baixar.

1. Introduza o nome do projeto **devkit_device**.

1. Escolha **ANSI C** como seu idioma.

1. Escolha **por meio da chave simétrica do DPS (serviço de provisionamento de dispositivos)** como o método de conexão.

1. Escolha **projeto CMake no Windows** como o tipo de projeto. Não escolha o **projeto MXChip IOT devkit**, essa opção é para quando você tem um dispositivo devkit real.

1. Escolha **por meio de Vcpkg** como a maneira de incluir o SDK.

1. O Código VS abre uma nova janela com ficheiros de código de dispositivo gerados na pasta `devkit_device`.

![Código de dispositivo gerado](./media/tutorial-connect-pnp-device/generated-code.png)

## <a name="build-the-code"></a>Compilar o código

Você usa o SDK do dispositivo para criar o stub do código de dispositivo gerado. O aplicativo que você cria simula um dispositivo **MXChip IOT devkit** e se conecta ao seu aplicativo IOT central. O aplicativo envia telemetria e propriedades e recebe comandos.

1. Num pedido de comando, crie um subdiretório `cmake` na pasta `devkit_device` e navegue para essa pasta:

    ```cmd
    mkdir cmake
    cd cmake
    ```

1. Execute os comandos a seguir para compilar o stub de código gerado. Substitua o espaço reservado `<directory of your Vcpkg repo>` pelo caminho para a sua cópia do repositório **Vcpkg** :

    ```cmd
    cmake .. -G "Visual Studio 16 2019" -A Win32 -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"

    cmake --build . -- /p:Configuration=Release
    ```

    Se você estiver usando o Visual Studio 2017 ou 2015, precisará especificar o gerador de CMake com base nas ferramentas de compilação que você está usando:

    ```cmd
    # Either
    cmake .. -G "Visual Studio 15 2017" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"
    # or
    cmake .. -G "Visual Studio 14 2015" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"
    ```

1. Depois que a compilação for concluída com êxito, no mesmo prompt de comando, execute o aplicativo. Substitua `<scopeid>` e `<primarykey>` pelos valores que você anotou anteriormente:

    ```cmd
    .\Release\devkit_device.exe mxchip-001 <scopeid> <primarykey>
    ```

1. O aplicativo do dispositivo começa a enviar dados para o Hub IoT. Às vezes, você vê o erro `Error registering device for DPS` na primeira vez que executa o comando anterior. Se você vir esse erro, repita o comando.

## <a name="view-the-device"></a>Exibir o dispositivo

Depois que o código do dispositivo se conectar ao seu IoT Central, você poderá exibir as propriedades e a telemetria que ele envia:

1. No aplicativo IoT Central, vá para a página **dispositivos** e selecione o dispositivo **mxchip-01** . Este dispositivo foi adicionado automaticamente quando o código do dispositivo está conectado:

    ![Página de visão geral](./media/tutorial-connect-pnp-device/overview-page.png)

    Após alguns minutos, essa página mostra gráficos da telemetria que o dispositivo está enviando.

1. Selecione a página **sobre** para ver os valores de propriedade que o dispositivo enviou.

1. Selecione a página **comandos** para chamar comandos no dispositivo. Você pode ver o dispositivo respondendo no prompt de comando que está executando o código do dispositivo.

1. Acesse a página **modelos de dispositivo** para ver o modelo que IOT central criado a partir do DCM no repositório público:

    ![Página modelos de dispositivo](./media/tutorial-connect-pnp-device/device-template.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a ligar um dispositivo IoT Plug and Play (pré-visualização) que foi gerado a partir de um DCM no repositório de modelos públicos.

Para saber mais sobre o DCMs e como criar seus próprios modelos, prossiga para o guia de instruções:

> [!div class="nextstepaction"]
> [Defina um novo tipo de dispositivo IoT](./howto-set-up-template.md)
