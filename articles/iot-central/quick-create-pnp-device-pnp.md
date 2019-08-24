---
title: Conectar um dispositivo de visualização do Azure IoT Plug and Play ao IoT Central | Microsoft Docs
description: Use um modelo de funcionalidade de dispositivo para gerar o código do dispositivo. Em seguida, execute o código do dispositivo, consulte o dispositivo conectar-se ao seu aplicativo IoT Central e use as exibições geradas automaticamente.
author: dominicbetts
ms.author: dobett
ms.date: 08/08/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 48161f56a8885239525587b764c4a116ffc8539f
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/23/2019
ms.locfileid: "69997738"
---
# <a name="quickstart-use-a-device-capability-model-to-create-an-iot-plug-and-play-device-and-connect-it-to-your-iot-central-application"></a>Início rápido: Use um modelo de funcionalidade de dispositivo para criar um dispositivo de Plug and Play de IoT e conectá-lo ao seu aplicativo IoT Central

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Um DCM ( _modelo de funcionalidade de dispositivo_ ) descreve os recursos de um dispositivo de [plug and Play IOT](../iot-pnp/overview-iot-plug-and-play.md) . IoT Central pode usar um DCM para criar um modelo de dispositivo e visualizações para um dispositivo quando o dispositivo se conecta pela primeira vez. Este manual de início rápido mostra-lhe como:

* Use Visual Studio Code para criar um dispositivo IoT Plug and Play usando um DCM.
* Execute o código do dispositivo no Windows e veja se ele se conecta ao seu aplicativo IoT Central.
* Exiba a telemetria simulada que o dispositivo envia.

## <a name="prerequisites"></a>Pré-requisitos

Conclua o início rápido [criar um aplicativo do Azure IOT central (recursos de visualização)](./quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) para criar um aplicativo IOT central usando o modelo de **aplicativo de visualização** .

Para concluir este guia de início rápido, você precisa instalar o seguinte software em seu computador local:

* [Visual Studio (Comunidade, profissional ou empresa)](https://visualstudio.microsoft.com/downloads/) – certifique-se de incluir o componente **Gerenciador de pacotes NuGet** e o **desenvolvimento de desktop C++ com** carga de trabalho ao instalar o Visual Studio.
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/) – quando você instala o **CMake**, selecione a opção **Adicionar CMake ao caminho do sistema**.
* [Visual Studio Code](https://code.visualstudio.com/).
* [Node.js](https://nodejs.org/)
* O `dps-keygen` utilitário:

    ```cmd/sh
    npm i -g dps-keygen
    ```

### <a name="install-azure-iot-device-workbench"></a>Instalar o Azure IoT Device Workbench

Use as etapas a seguir para instalar a extensão do Azure IoT Device Workbench no VS Code:

1. Em VS Code, selecione a guia **extensões** .
1. Pesquise o **Azure IOT Device Workbench**.
1. Selecione **Instalar**.

## <a name="prepare-the-development-environment"></a>Preparar o ambiente de desenvolvimento

### <a name="get-azure-iot-device-sdk-for-c"></a>Obter o SDK do dispositivo IoT do Azure para C

Prepare um ambiente de desenvolvimento que você pode usar para criar o SDK do dispositivo C do Azure IoT.

1. Abra uma linha de comandos. Execute o seguinte comando para clonar o [SDK C do Azure IoT](https://github.com/Azure/azure-iot-sdk-c) no repositório do GitHub:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
    ```

    Esta operação deve demorar vários minutos a ser concluída.

1. Crie uma `central_app` pasta na raiz do clone local do repositório. Você usa essa pasta para os arquivos de modelo de dispositivo e o stub de código de dispositivo.

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir central_app
    ```

## <a name="generate-device-key"></a>Gerar chave do dispositivo

Para conectar um dispositivo a um aplicativo IoT Central, você precisa de uma chave de dispositivo. Para gerar uma chave de dispositivo:

1. Entre no aplicativo IoT Central que você criou no guia de início rápido anterior.

1. Vá para a página **Administração** e selecione **conexão do dispositivo**.

1. Anote a **ID do escopo** e a **chave primária**. Vai utilizar esses valores mais tarde neste início rápido.

    ![Conexão do dispositivo](./media/quick-create-pnp-device-pnp/device-connection.png)

1. Abra um prompt de comando e execute o seguinte comando para gerar uma chave de dispositivo:

    ```cmd/sh
    dps-keygen  -di:mxchip-01 -mk:{Primary Key from previous step}
    ```

    Anote a _chave do dispositivo_gerada, você usará esse valor em uma etapa posterior neste guia de início rápido.

## <a name="download-your-model"></a>Baixe seu modelo

Neste guia de início rápido, você usa o DCM público para um dispositivo MxChip IoT DevKit. Você não precisa de um dispositivo DevKit real para executar o código, neste guia de início rápido, você compila o código para ser executado no Windows.

1. Abra `azure-iot-sdk-c\central_app` a pasta com vs Code.

1. Use **Ctrl + Shift + P** para abrir a paleta de comandos, insira **plug and Play de IOT**e selecione **abrir repositório de modelos**. Selecione **repositório público**. VS Code mostra uma lista de DCMs no repositório de modelo público.

1. Selecione o **MXChip IOT devkit** DCM com ID `urn:mxchip:mxchip_iot_devkit:1`. Em seguida, selecione **baixar**. Agora você tem uma cópia do DCM na `central_app` pasta.

![Repositório de modelos e DCM](./media/quick-create-pnp-device-pnp/public-repository.png)

> [!NOTE]
> Para trabalhar com IoT Central, o modelo de funcionalidade do dispositivo deve ter todas as interfaces definidas embutidas no mesmo arquivo.

## <a name="generate-the-c-code-stub"></a>Gerar o stub do código C

Agora você tem o **MXChip IOT devkit** DCM e suas interfaces associadas, você pode gerar o código do dispositivo que implementa o modelo. Para gerar o stub do código C no VS Code:

1. Com a pasta com arquivos do DCM aberta, use **Ctrl + Shift + P** para abrir a paleta de comandos, insira **plug and Play de IOT**e selecione **gerar stub de código de dispositivo**.

    > [!NOTE]
    > Na primeira vez que você usar o utilitário gerador de código de Plug and Play IoT, levará alguns segundos para baixar.

1. Selecione o arquivo **MXChip IOT devkit** DCM que você acabou de baixar.

1. Insira o nome do projeto **devkit_device**.

1. Escolha **ANSI C** como seu idioma.

1. Escolha **projeto CMake** como seu tipo de projeto. Não escolha o **projeto MXChip IOT devkit**, essa opção é para quando você tem um dispositivo devkit real.

1. Escolha **por meio da chave simétrica do DPS (serviço de provisionamento de dispositivos)** como o método de conexão.

1. VS Code abre uma nova janela com arquivos stub de código de dispositivo gerados `devkit_device` na pasta.

![Código de dispositivo gerado](./media/quick-create-pnp-device-pnp/generated-code.png)

Para adicionar as informações de conexão ao código de dispositivo gerado:

1. Na janela de VS Code que contém o código C gerado. Abra o ficheiro `main.c`.

1. Substitua `[DPS Id Scope]` pela **ID do escopo** anotada anteriormente.

1. Substitua `[DPS symmetric key]` pela chave do dispositivo que você gerou em uma etapa anterior.

1. Substitua `[device registration Id]` por `mxchip-01`.

1. Guarde as alterações.

## <a name="build-the-code"></a>Compilar o código

Você usa o SDK do dispositivo para criar o stub do código de dispositivo gerado. O aplicativo que você cria simula um dispositivo **MXChip IOT devkit** e se conecta ao seu aplicativo IOT central. O aplicativo envia telemetria e propriedades e recebe comandos.

1. Em vs Code, abra o `CMakeLists.txt` arquivo `azure-iot-sdk-c` na pasta. Certifique-se de abrir `CMakeLists.txt` o arquivo `azure-iot-sdk-c` na pasta, `devkit_device` não aquele na pasta.

1. Adicione a linha abaixo na parte inferior do `CMakeLists.txt` arquivo para incluir a pasta stub do código do dispositivo ao compilar:

    ```txt
    add_subdirectory(central_app/devkit_device)
    ```

1. Crie uma `cmake` pasta `azure-iot-sdk-c` na pasta e navegue até essa pasta em um prompt de comando:

    ```cmd\sh
    mkdir cmake
    cd cmake
    ```

1. Execute os seguintes comandos para compilar o SDK do dispositivo e o stub do código gerado:

    ```cmd\sh
    cmake .. -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON
    cmake --build . -- /m /p:Configuration=Release
    ```

1. Depois que a compilação for concluída com êxito, no mesmo prompt de comando, execute seu aplicativo:

    ```cmd\sh
    .\central_app\devkit_device\Release\devkit_device.exe
    ```

1. O aplicativo do dispositivo começa a enviar dados para o aplicativo IoT Central.

## <a name="view-the-device"></a>Exibir o dispositivo

Depois que o código do dispositivo se conectar ao seu IoT Central, você poderá exibir as propriedades e a telemetria que ele envia:

1. No aplicativo IoT Central, vá para a página **dispositivos** e selecione o dispositivo **mxchip-01** . Este dispositivo foi adicionado automaticamente quando o código do dispositivo está conectado:

    ![Página de visão geral](./media/quick-create-pnp-device-pnp/overview-page.png)

    Após alguns minutos, essa página mostra gráficos da telemetria que o dispositivo está enviando.

1. Selecione a página **sobre** para ver os valores de propriedade que o dispositivo enviou.

1. Selecione a página **comandos** para chamar comandos no dispositivo. Você pode ver o dispositivo respondendo no prompt de comando que está executando o código do dispositivo.

1. Acesse a página **modelos de dispositivo** para ver o modelo que IOT central criado a partir do DCM no repositório público:

    ![Página modelos de dispositivo](./media/quick-create-pnp-device-pnp/device-template.png)

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você aprendeu a conectar um dispositivo de Plug and Play IoT que foi gerado de um DCM no repositório de modelo público.

Para saber mais sobre o DCMs e como criar seus próprios modelos, prossiga para o guia de instruções:

> [!div class="nextstepaction"]
> [Configurar e gerenciar um modelo de dispositivo](./howto-set-up-template-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
