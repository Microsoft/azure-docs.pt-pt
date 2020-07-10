---
title: Tutorial - Ligue um dispositivo IoT Plug and Play (pré-visualização) ao Azure IoT Central
description: Este tutorial mostra-lhe como usar um modelo de capacidade do dispositivo para gerar código do dispositivo. Em seguida, executar o código do dispositivo, ver o dispositivo ligar-se à sua aplicação IoT Central e utilizar as vistas autogeradas.
author: dominicbetts
ms.author: dobett
ms.date: 12/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 6727a2c45187e0e6bb583bb65e176024067d81e2
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2020
ms.locfileid: "86219997"
---
# <a name="tutorial-use-a-device-capability-model-to-create-an-iot-plug-and-play-preview-device-and-connect-it-to-your-iot-central-application"></a>Tutorial: Utilize um modelo de capacidade do dispositivo para criar um dispositivo IoT Plug and Play (pré-visualização) e conectá-lo à sua aplicação IoT Central

Um _modelo de capacidade do dispositivo_ (DCM) descreve as capacidades de um dispositivo [IoT Plug and Play (pré-visualização).](../../iot-pnp/overview-iot-plug-and-play.md) O IoT Central pode utilizar um DCM para criar um modelo de dispositivo e visualizações para um dispositivo quando o dispositivo se conecta pela primeira vez.

O suporte para [IoT Plug e Play](../../iot-pnp/overview-iot-plug-and-play.md) está em pré-visualização e só é suportado em regiões selecionadas.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Utilize o Código do Estúdio Visual para criar um dispositivo IoT Plug and Play (pré-visualização) utilizando um DCM.
> * Executar o código do dispositivo no Windows e vê-lo ligar-se à sua aplicação IoT Central.
> * Ver a telemetria simulada que o dispositivo envia.

## <a name="prerequisites"></a>Pré-requisitos

Complete o quickstart [da aplicação Create azure IoT Central](./quick-deploy-iot-central.md) para criar uma aplicação IoT Central utilizando o modelo **de aplicação Custom > Custom.**

Para completar este tutorial, tem de instalar o seguinte software na sua máquina local:

* [Construa ferramentas para o Estúdio Visual](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16) com **ferramentas de construção C++** e cargas de trabalho **de componentes de gestor de pacotes Nuget.** Ou se já tiver [o Visual Studio (Comunidade, Profissional ou Enterprise)](https://visualstudio.microsoft.com/downloads/) 2019, 2017 ou 2015 com as mesmas cargas de trabalho instaladas.
* [Git.](https://git-scm.com/download/)
* [CMake](https://cmake.org/download/) - quando instalar **o CMake**, selecione a opção **Adicionar CMake ao sistema PATH**.
* [Visual Studio Code](https://code.visualstudio.com/).
* [Node.js](https://nodejs.org/)
* A `dps-keygen` utilidade:

    ```cmd/sh
    npm i -g dps-keygen
    ```

### <a name="install-azure-iot-tools"></a>Instalar ferramentas Azure IoT

Utilize os seguintes passos para instalar o pacote de extensões Azure IoT Tools no Código VS:

1. No código VS, selecione o **separador Extensões.**
1. Procure por **Ferramentas Azure IoT**.
1. Selecione **Install** (Instalar).

## <a name="prepare-the-development-environment"></a>Preparar o ambiente de desenvolvimento

Neste tutorial, você usa o gestor da biblioteca [Vcpkg](https://github.com/microsoft/vcpkg) para instalar o dispositivo Azure IoT C SDK no seu ambiente de desenvolvimento.

1. Abra uma linha de comandos. Execute o seguinte comando para instalar vcpkg:

    ```cmd
    git clone https://github.com/Microsoft/vcpkg.git
    cd vcpkg

    .\bootstrap-vcpkg.bat
    ```

    Em seguida, para ligar a [integração](https://github.com/microsoft/vcpkg/blob/master/docs/users/integration.md)em todo o utilizador, executar o seguinte comando. A primeira vez que dirige este comando requer direitos administrativos:

    ```cmd
    .\vcpkg.exe integrate install
    ```

1. Instalar o dispositivo Azure IoT C SDK Vcpkg:

    ```cmd
    .\vcpkg.exe install azure-iot-sdk-c[public-preview,use_prov_client]
    ```

## <a name="generate-device-key"></a>Gerar chave de dispositivo

Para ligar um dispositivo a uma aplicação IoT Central, precisa de uma chave do dispositivo. Para gerar uma chave de dispositivo:

1. Inicie sessão na aplicação IoT Central que criou usando o modelo **de aplicação Personalizado** no arranque rápido da [aplicação Azure IoT Central.](./quick-deploy-iot-central.md)

1. Aceda à página **Administração** e selecione **a Ligação do Dispositivo**.

1. Tome nota do **ID Scope**. Use este valor mais tarde neste tutorial.

1. Selecione o grupo de inscrição **SAS-IoT-Devices.** Tome nota da **Chave Primária.** Use este valor mais tarde neste tutorial.

    ![Ligação do dispositivo](./media/tutorial-connect-pnp-device/device-connection.png)

1. Abra uma solicitação de comando e executar o seguinte comando para gerar uma chave de dispositivo:

    ```cmd/sh
    dps-keygen -di:mxchip-001 -mk:{Primary Key from previous step}
    ```

    Tome nota da _tecla_do dispositivo gerado, utilize este valor num passo posterior neste tutorial.

## <a name="download-your-model"></a>Transferir o seu modelo

Neste tutorial, você usa o DCM público para um dispositivo MxChip IoT DevKit. Não precisa de um dispositivo DevKit real para executar o código, neste tutorial compila o código para ser executado no Windows.

1. Crie uma pasta chamada `central_app` e abra-a no Código VS.

1. Utilize **ctrl+Shift+P** para abrir a paleta de comando, **introduza ioT Plug and Play**e selecione o **Repositório de Modelo Aberto**. Selecione **repositório público**. O Código VS mostra uma lista dos DCMs no repositório de modelos públicos.

1. Selecione o **MXChip IoT DevKit** DCM com ID `urn:mxchip:mxchip_iot_devkit:1` . Em seguida, **selecione Download**. Tem agora uma cópia do DCM na `central_app` pasta.

![Repositório de modelos e DCM](./media/tutorial-connect-pnp-device/public-repository.png)

> [!NOTE]
> Para trabalhar com a IoT Central, o modelo de capacidade do dispositivo deve ter todas as interfaces definidas em linha no mesmo ficheiro.

## <a name="generate-the-c-code-stub"></a>Gerar o código C

Agora tem o **MXChip IoT DevKit** DCM e as suas interfaces associadas, pode gerar o código do dispositivo que implementa o modelo. Para gerar o código C no código VS:

1. Com a pasta com ficheiros DCM aberta, utilize **ctrl+Shift+P** para abrir a paleta de comando, **introduza ioT Plug e Play**, e selecione **'Gerar Código de Dispositivo Stub'**.

    > [!NOTE]
    > A primeira vez que utiliza o utilitário IoT Plug e Play Code Generator, demora alguns segundos a descarregar.

1. Selecione o ficheiro **MXChip IoT DevKit** DCM que acabou de descarregar.

1. Insira o nome do projeto **devkit_device**.

1. Escolha **ANSI C** como língua.

1. Escolha **a tecla simétrica via DPS (Serviço de Provisionamento de Dispositivos)** como método de ligação.

1. Escolha **o Projeto CMake no Windows** como o seu tipo de projeto. Não escolha **o MXChip IoT DevKit Project,** esta opção é para quando tiver um verdadeiro dispositivo DevKit.

1. Escolha **a Via Vcpkg** como a forma de incluir o SDK.

1. O Código VS abre uma nova janela com ficheiros de ficheiros de código de dispositivo gerados na `devkit_device` pasta.

![Código do dispositivo gerado](./media/tutorial-connect-pnp-device/generated-code.png)

## <a name="build-the-code"></a>Compilar o código

Utilize o dispositivo SDK para construir o código do dispositivo gerado. A aplicação que constrói simula um dispositivo **MXChip IoT DevKit** e liga-se à sua aplicação IoT Central. A aplicação envia telemetria e propriedades e recebe comandos.

1. Num pedido de comando, crie um `cmake` subdirecional na `devkit_device` pasta e navegue para essa pasta:

    ```cmd
    mkdir cmake
    cd cmake
    ```

1. Executar os seguintes comandos para construir o código gerado. Substitua o `<directory of your Vcpkg repo>` espaço reservado pelo caminho para a sua cópia do repositório **Vcpkg:**

    ```cmd
    cmake .. -G "Visual Studio 16 2019" -A Win32 -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"

    cmake --build . -- /p:Configuration=Release
    ```

    Se estiver a utilizar o Visual Studio 2017 ou 2015, tem de especificar o gerador CMake com base nas ferramentas de construção que está a utilizar:

    ```cmd
    # Either
    cmake .. -G "Visual Studio 15 2017" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"
    # or
    cmake .. -G "Visual Studio 14 2015" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"
    ```

1. Após a construção concluída com sucesso, no mesmo comando, executar a sua aplicação. Substitua `<scopeid>` e `<devicekey>` pelos valores que observou anteriormente:

    ```cmd
    .\Release\devkit_device.exe mxchip-001 <scopeid> <devicekey>
    ```

1. A aplicação do dispositivo começa a enviar dados para o IoT Hub. Às vezes vê-se o erro `Error registering device for DPS` da primeira vez que se dirige o comando anterior. Se vir este erro, recandidato ao comando.

## <a name="view-the-device"></a>Ver o dispositivo

Depois de o código do dispositivo ligar à sua IoT Central, pode visualizar as propriedades e a telemetria que envia:

1. Na sua aplicação IoT Central, vá à página **Dispositivos** e selecione o dispositivo **mxchip-01.** Este dispositivo foi automaticamente adicionado quando o código do dispositivo ligou:

    ![Página geral](./media/tutorial-connect-pnp-device/overview-page.png)

    Após alguns minutos, esta página mostra gráficos da telemetria que o dispositivo está a enviar.

1. Selecione a página **'Cerca'** para ver os valores de propriedade que o dispositivo enviou.

1. Selecione a página **Comandos** para chamar comandos no dispositivo. Pode ver o dispositivo a responder na localização do comando que está a executar o código do dispositivo.

1. Vá à página **de modelos do Dispositivo** para ver o modelo que o IoT Central criou a partir do DCM no repositório público:

    ![Página de modelos de dispositivo](./media/tutorial-connect-pnp-device/device-template.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a ligar um dispositivo IoT Plug and Play (pré-visualização) que foi gerado a partir de um DCM no repositório de modelos públicos.

Para saber mais sobre os DCMs e como criar os seus próprios modelos, continue a orientar:

> [!div class="nextstepaction"]
> [Defina um novo tipo de dispositivo IoT](./howto-set-up-template.md)
