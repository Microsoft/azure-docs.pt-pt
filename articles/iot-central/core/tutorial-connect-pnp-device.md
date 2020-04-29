---
title: Tutorial - Ligue um dispositivo IoT Plug and Play (pré-visualização) ao Azure IoT Central
description: Este tutorial mostra-lhe como usar um modelo de capacidade do dispositivo para gerar código de dispositivo. Em seguida, execute o código do dispositivo, consulte a ligação do dispositivo à sua aplicação IoT Central e utilize as vistas autogeradas.
author: dominicbetts
ms.author: dobett
ms.date: 12/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 42098d54725cc12691839b63c508efbecf042aa0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80064424"
---
# <a name="tutorial-use-a-device-capability-model-to-create-an-iot-plug-and-play-preview-device-and-connect-it-to-your-iot-central-application"></a>Tutorial: Utilize um modelo de capacidade do dispositivo para criar um dispositivo IoT Plug and Play (pré-visualização) e conectá-lo à sua aplicação IoT Central

Um modelo de capacidade de _dispositivo_ (DCM) descreve as capacidades de um dispositivo [IoT Plug and Play (pré-visualização).](../../iot-pnp/overview-iot-plug-and-play.md) A IoT Central pode usar um DCM para criar um modelo de dispositivo e visualizações para um dispositivo quando o dispositivo se conecta pela primeira vez.

O suporte para [IoT Plug and Play](../../iot-pnp/overview-iot-plug-and-play.md) está em pré-visualização e só é suportado em regiões selecionadas.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Utilize o Código do Estúdio Visual para criar um dispositivo IoT Plug e Play (pré-visualização) utilizando um DCM.
> * Execute o código do dispositivo no Windows e consulte-o ligado à sua aplicação IoT Central.
> * Veja a telemetria simulada que o dispositivo envia.

## <a name="prerequisites"></a>Pré-requisitos

Complete a [aplicação Create a Azure IoT Central](./quick-deploy-iot-central.md) rapidamente para criar uma aplicação IoT Central utilizando a aplicação Custom > modelo de **aplicação Personalizada.**

Para completar este tutorial, é necessário instalar o seguinte software na sua máquina local:

* [Construa ferramentas para estúdio visual](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16) com ferramentas de **construção C++** e cargas de trabalho componentes de **gestor de pacotes Nuget.** Ou se já tem [o Visual Studio (Comunidade, Profissional ou Empresa)](https://visualstudio.microsoft.com/downloads/) 2019, 2017 ou 2015 com as mesmas cargas de trabalho instaladas.
* [Git.](https://git-scm.com/download/)
* [CMake](https://cmake.org/download/) - quando instalar **cMake**, selecione a opção **Adicionar CMake ao sistema PATH**.
* [Código de estúdio visual.](https://code.visualstudio.com/)
* [Node.js](https://nodejs.org/)
* A `dps-keygen` utilidade:

    ```cmd/sh
    npm i -g dps-keygen
    ```

### <a name="install-azure-iot-tools"></a>Instalar ferramentas Azure IoT

Utilize os seguintes passos para instalar o pacote de extensão Azure IoT Tools no Código VS:

1. No Código VS, selecione o separador **Extensões.**
1. Pesquisa de **ferramentas Azure IoT**.
1. Selecione **Instalar**.

## <a name="prepare-the-development-environment"></a>Preparar o ambiente de desenvolvimento

Neste tutorial, você usa o gestor da biblioteca [Vcpkg](https://github.com/microsoft/vcpkg) para instalar o dispositivo Azure IoT C SDK no seu ambiente de desenvolvimento.

1. Abra uma linha de comandos. Executar o seguinte comando para instalar vcpkg:

    ```cmd
    git clone https://github.com/Microsoft/vcpkg.git
    cd vcpkg

    .\bootstrap-vcpkg.bat
    ```

    Em seguida, para ligar a [integração](https://github.com/microsoft/vcpkg/blob/master/docs/users/integration.md)em todo o utilizador, executar o seguinte comando. A primeira vez que dirige este comando requer direitos administrativos:

    ```cmd
    .\vcpkg.exe integrate install
    ```

1. Instale o dispositivo SDK Vcpkg do dispositivo Azure IoT C:

    ```cmd
    .\vcpkg.exe install azure-iot-sdk-c[public-preview,use_prov_client]
    ```

## <a name="generate-device-key"></a>Gerar chave do dispositivo

Para ligar um dispositivo a uma aplicação IoT Central, precisa de uma chave de dispositivo. Para gerar uma chave de dispositivo:

1. Inicie sessão na aplicação IoT Central que criou utilizando o modelo de **aplicação Personalizado** na [aplicação Create a Azure IoT Central.](./quick-deploy-iot-central.md)

1. Vá à página **da Administração** e selecione Ligação de **Dispositivos**.

1. Tome nota do Âmbito de **IDENTIFICAção** e da **Chave Primária** que você vê quando selecionar **Teclas de visualização**. Usa estes valores mais tarde neste tutorial.

    ![Ligação do dispositivo](./media/tutorial-connect-pnp-device/device-connection.png)

1. Abra um pedido de comando e execute o seguinte comando para gerar uma chave do dispositivo:

    ```cmd/sh
    dps-keygen -di:mxchip-001 -mk:{Primary Key from previous step}
    ```

    Tome nota da _chave_do dispositivo gerada, utilize este valor num passo posterior neste tutorial.

## <a name="download-your-model"></a>Descarregue o seu modelo

Neste tutorial, utiliza o DCM público para um dispositivo MxChip IoT DevKit. Não é necessário um dispositivo DevKit real para executar o código, neste tutorial compila o código para executar no Windows.

1. Crie uma `central_app` pasta chamada e abra-a no Código VS.

1. Utilize **ctrl+Shift+P** para abrir a paleta de comando, introduzir **plug e reproduzir IoT,** e selecione **Repositório de Modelo Aberto**. Selecione **repositório público**. O Código VS mostra uma lista dos DCMs no repositório de modelos públicos.

1. Selecione o **DCM MXChip IoT DevKit** com ID `urn:mxchip:mxchip_iot_devkit:1`. Em seguida, selecione **Download**. Tem agora uma cópia do DCM na `central_app` pasta.

![Repositório de modelos e DCM](./media/tutorial-connect-pnp-device/public-repository.png)

> [!NOTE]
> Para funcionar com a IoT Central, o modelo de capacidade do dispositivo deve ter todas as interfaces definidas inline no mesmo ficheiro.

## <a name="generate-the-c-code-stub"></a>Gerar o código C

Agora tem o **DCM MXChip IoT DevKit** E as suas interfaces associadas, pode gerar o código do dispositivo que implementa o modelo. Para gerar o código C no código VS:

1. Com a pasta com ficheiros DCM abertos, utilize **ctrl+Shift+P** para abrir a paleta de comando, **introduza plug e reprodução IoT**e selecione **Generate Device Code Stub**.

    > [!NOTE]
    > A primeira vez que utiliza o utilitário IoT Plug and Play Code Generator, demora alguns segundos a descarregar.

1. Selecione o ficheiro **MXChip IoT DevKit** DCM que acabou de descarregar.

1. Introduza o nome do projeto **devkit_device**.

1. Escolha **ansi C** como língua.

1. Escolha **através da chave simétrica DPS (Device Provisioning Service)** como método de ligação.

1. Escolha o **CMake Project no Windows** como o seu tipo de projeto. Não escolha o **MXChip IoT DevKit Project**, esta opção é para quando tiver um verdadeiro dispositivo DevKit.

1. Escolha **via Vcpkg** como forma de incluir o SDK.

1. O Código VS abre uma nova janela com ficheiros de código de dispositivo gerados na `devkit_device` pasta.

![Código de dispositivo gerado](./media/tutorial-connect-pnp-device/generated-code.png)

## <a name="build-the-code"></a>Compilar o código

Utiliza o dispositivo SDK para construir o código de código do dispositivo gerado. A aplicação que constrói simula um dispositivo **MXChip IoT DevKit** e liga-se à sua aplicação IoT Central. A aplicação envia telemetria e propriedades, e recebe comandos.

1. Num pedido de comando, crie `cmake` `devkit_device` um subdiretório na pasta e navegue para essa pasta:

    ```cmd
    mkdir cmake
    cd cmake
    ```

1. Executar os seguintes comandos para construir o código gerado. Substitua `<directory of your Vcpkg repo>` o espaço reservado pelo caminho para a sua cópia do repositório **vcpkg:**

    ```cmd
    cmake .. -G "Visual Studio 16 2019" -A Win32 -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"

    cmake --build . -- /p:Configuration=Release
    ```

    Se estiver a utilizar o Visual Studio 2017 ou 2015, precisa especificar o gerador CMake com base nas ferramentas de construção que está a usar:

    ```cmd
    # Either
    cmake .. -G "Visual Studio 15 2017" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"
    # or
    cmake .. -G "Visual Studio 14 2015" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"
    ```

1. Depois de a construção completar com sucesso, no mesmo comando executar a sua aplicação. `<scopeid>` Substitua `<devicekey>` e com os valores que observou anteriormente:

    ```cmd
    .\Release\devkit_device.exe mxchip-001 <scopeid> <devicekey>
    ```

1. A aplicação do dispositivo começa a enviar dados para o IoT Hub. Às vezes `Error registering device for DPS` vemos o erro da primeira vez que executamos o comando anterior. Se vir este erro, tente novamente o comando.

## <a name="view-the-device"></a>Ver o dispositivo

Depois de o código do seu dispositivo se ligar à sua Central IoT, pode ver as propriedades e telemetria que envia:

1. Na sua aplicação IoT Central, aceda à página **dispositivos** e selecione o dispositivo **mxchip-01.** Este dispositivo foi automaticamente adicionado quando o código do dispositivo foi ligado:

    ![Página de visão geral](./media/tutorial-connect-pnp-device/overview-page.png)

    Após alguns minutos, esta página mostra gráficos da telemetria que o dispositivo está a enviar.

1. Selecione a página **Sobre** para ver os valores de propriedade que o dispositivo enviou.

1. Selecione a página **Comandos** para ligar para os comandos no dispositivo. Pode ver o dispositivo a responder no pedido de comando que está a executar o código do dispositivo.

1. Vá à página de **modelos** do Dispositivo para ver o modelo que a IoT Central criou a partir do DCM no repositório público:

    ![Página de modelos de dispositivo](./media/tutorial-connect-pnp-device/device-template.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a ligar um dispositivo IoT Plug and Play (pré-visualização) que foi gerado a partir de um DCM no repositório de modelos públicos.

Para saber mais sobre dcms e como criar os seus próprios modelos, continue a orientar::

> [!div class="nextstepaction"]
> [Defina um novo tipo de dispositivo IoT](./howto-set-up-template.md)
