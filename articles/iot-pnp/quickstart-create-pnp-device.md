---
title: Criar um dispositivo de visualização de Plug and Play do Azure IoT | Microsoft Docs
description: Use um modelo de funcionalidade de dispositivo para gerar o código do dispositivo. Em seguida, execute o código do dispositivo e veja o dispositivo conectar-se ao Hub IoT.
author: miagdp
ms.author: miag
ms.date: 08/02/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 7cfa6e63f74233e9a3fab8f235584fdbe01e67d9
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878216"
---
# <a name="quickstart-use-a-device-capability-model-to-create-an-iot-plug-and-play-device"></a>Início rápido: Usar um modelo de capacidade de dispositivo para criar um dispositivo de Plug and Play de IoT

Um DCM ( _modelo de funcionalidade de dispositivo_ ) descreve os recursos de um dispositivo de plug and Play IOT. Um DCM geralmente é associado a um SKU de produto. Os recursos definidos no DCM são organizados em interfaces reutilizáveis. Você pode gerar um esqueleto de código de dispositivo de um DCM. Este guia de início rápido mostra como usar VS Code para criar um dispositivo de Plug and Play de IoT usando um DCM.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido, você precisa instalar o seguinte software em seu computador local:

* [Visual Studio (Comunidade, profissional ou empresa)](https://visualstudio.microsoft.com/downloads/) – certifique-se de incluir o componente **Gerenciador de pacotes NuGet** e o **desenvolvimento de desktop C++ com** carga de trabalho ao instalar o Visual Studio.
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/).
* [Visual Studio Code](https://code.visualstudio.com/).

### <a name="install-azure-iot-device-workbench"></a>Instalar o Azure IoT Device Workbench

Use as etapas a seguir para instalar a extensão do Azure IoT Device Workbench no VS Code:

1. Em VS Code, selecione a guia **extensões** .
1. Pesquise o **Azure IOT Device Workbench**.
1. Selecione **Instalar**.

### <a name="install-the-azure-iot-explorer"></a>Instalar o Azure IoT Explorer

Baixe e instale a ferramenta do Azure IoT Explorer na página de [versão mais recente](https://github.com/Azure/azure-iot-explorer/releases) .

### <a name="get-the-connection-string-for-your-company-model-repository"></a>Obter a cadeia de conexão para o repositório de modelos da empresa

Você pode encontrar a _cadeia de conexão do repositório de modelos da empresa_ no portal do [portal do Azure Certified para IOT](https://preview.catalog.azureiotsolutions.com) ao entrar com uma conta corporativa ou de estudante da Microsoft, ou sua ID de parceiro da Microsoft, se você tiver uma. Depois de entrar, selecione **repositório da empresa** e, em seguida, cadeias de **conexão**.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-an-iot-hub"></a>Preparar um hub IoT

Você também precisa de um hub IoT do Azure em sua assinatura do Azure para concluir este guia de início rápido. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Adicione a extensão Microsoft Azure IoT para CLI do Azure:

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Execute o comando a seguir para criar a identidade do dispositivo em seu hub IoT. Substitua os espaços reservados **nomedoseuhubiot** e **YourDevice** por seus nomes reais. Se você não tiver um hub IoT, siga [estas instruções para criar um](../iot-hub/iot-hub-create-using-cli.md):

```azurecli-interactive
az iot hub device-identity create --hub-name [YourIoTHubName] --device-id [YourDevice]
```

Execute os seguintes comandos para obter a _cadeia de conexão do dispositivo_ para o dispositivo que você acabou de registrar:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name [YourIoTHubName] --device-id [YourDevice] --output table
```

Execute os seguintes comandos para obter a _cadeia de conexão do Hub IOT_ para o Hub:

```azurecli-interactive
az iot hub show-connection-string --hub-name [YourIoTHubName] --output table
```

## <a name="prepare-the-development-environment"></a>Preparar o ambiente de desenvolvimento

### <a name="get-azure-iot-device-sdk-for-c"></a>Obter o SDK do dispositivo IoT do Azure para C

Neste guia de início rápido, você prepara um ambiente de desenvolvimento que pode ser usado para clonar e compilar o SDK do dispositivo do Azure IoT C.

1. Abra uma linha de comandos. Execute o seguinte comando para clonar o [SDK C do Azure IoT](https://github.com/Azure/azure-iot-sdk-c) no repositório do GitHub:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
    ```

    Esta operação deve demorar vários minutos a ser concluída.

1. Crie um `pnp_app` subdiretório na raiz do clone local do repositório. Você usa essa pasta para os arquivos de modelo de dispositivo e o stub de código de dispositivo.

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir pnp_app
    ```

## <a name="author-your-model"></a>Crie seu modelo

Neste guia de início rápido, você usa um modelo de funcionalidade de dispositivo de exemplo existente e as interfaces associadas.

1. Baixe o [modelo de funcionalidade do dispositivo](https://github.com/Azure/IoTPlugandPlay/blob/master/samples/SampleDevice.capabilitymodel.json) e o [exemplo](https://github.com/Azure/IoTPlugandPlay/blob/master/samples/EnvironmentalSensor.interface.json) de interface `pnp_app` e salve os arquivos na pasta.

    > [!TIP]
    > Para baixar um arquivo do GitHub, navegue até o arquivo, clique com o botão direito do mouse em **RAW**e selecione **Salvar link como**.

1. Abra `pnp_app` a pasta com vs Code. Você pode exibir os arquivos com o IntelliSense:

    ![Modelo de funcionalidade do dispositivo](media/quickstart-create-pnp-device/dcm.png)

1. Nos arquivos que você baixou, `<YOUR_COMPANY_NAME_HERE>` substitua `@id` nos campos `schema` e por um valor exclusivo. Use somente os caracteres a-z, A-Z, 0-9 e sublinhado. Para obter mais informações, consulte [formato do identificador de entrelaça digital](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format).

## <a name="generate-the-c-code-stub"></a>Gerar o stub do código C

Agora você tem um DCM e suas interfaces associadas, você pode gerar o código do dispositivo que implementa o modelo. Para gerar o stub do código C no VS Code:

1. Com a pasta com arquivos do DCM aberta, use **Ctrl + Shift + P** para abrir a paleta de comandos, insira **plug and Play de IOT**e selecione **gerar stub de código de dispositivo**.

    > [!NOTE]
    > Na primeira vez que você usar o utilitário gerador de código de Plug and Play IoT, levará alguns segundos para baixar.

1. Escolha o arquivo DCM que você deseja usar para gerar o stub do código do dispositivo.

1. Insira o nome do projeto **sample_device**, ele será o nome do aplicativo do dispositivo.

1. Escolha **ANSI C** como seu idioma.

1. Escolha **projeto CMake** como seu tipo de projeto.

1. Escolha **por meio da cadeia de conexão do dispositivo Hub IOT** como método de conexão.

1. VS Code abre uma nova janela com arquivos stub de código de dispositivo gerados.
    ![Código do dispositivo](media/quickstart-create-pnp-device/device-code.png)

## <a name="build-the-code"></a>Compilar o código

Você usa o SDK do dispositivo para criar o stub do código de dispositivo gerado. O aplicativo que você cria simula um dispositivo que se conecta a um hub IoT. O aplicativo envia telemetria e propriedades e recebe comandos.

1. No vs Code, abra `CMakeLists.txt` na pasta raiz do SDK do dispositivo.

1. Adicione a linha abaixo na parte inferior do `CMakeLists.txt` arquivo para incluir a pasta stub do código do dispositivo ao compilar:

    ```txt
    add_subdirectory(pnp_app/sample_device)
    ```

1. Crie um subdiretório CMake na pasta raiz do SDK do dispositivo e navegue até essa pasta:

    ```cmd\sh
    mkdir cmake
    cd cmake
    ```

1. Execute os seguintes comandos para compilar o SDK do dispositivo e o stub do código gerado:

    ```cmd\sh
    cmake .. -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON
    cmake --build . -- /m /p:Configuration=Release
    ```

    > [!NOTE]
    > Se o CMake não conseguir C++ localizar seu compilador, você obterá erros de compilação ao executar o comando anterior. Se isso acontecer, tente executar esse comando no [prompt de comando do Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs).

1. Depois que a compilação for concluída com êxito, execute o aplicativo passando a cadeia de conexão do dispositivo do Hub IoT como parâmetro.

    ```cmd\sh
    cd azure-iot-sdk-c\cmake\pnp_app\sample_device\Release\
    sample_device.exe "[IoT Hub device connection string]"
    ```

1. O aplicativo do dispositivo começa a enviar dados para o Hub IoT.

    ![Aplicativo de dispositivo em execução](media/quickstart-create-pnp-device/device-app-running.png)

## <a name="validate-the-code"></a>Validar o código

### <a name="publish-device-model-files-to-model-repository"></a>Publicar arquivos de modelo de dispositivo no repositório de modelos

Para validar o código do dispositivo com o **Azure IOT Explorer**, você precisa publicar os arquivos no repositório de modelo.

1. Com a pasta com arquivos do DCM aberta, use **Ctrl + Shift + P** para abrir a paleta de comandos, digite **e selecione plug & Play de IOT: Enviar arquivos para o repositório**de modelos.

1. Selecionar `SampleDevice.capabilitymodel.json` e`EnvironmentalSensor.interface.json` arquivos.

1. Insira a cadeia de conexão do repositório de modelos da empresa.

    > [!NOTE]
    > A cadeia de conexão só é necessária na primeira vez que você se conecta ao repositório.

1. Em VS Code janela de saída e notificação, você pode verificar se os arquivos foram publicados com êxito.

    > [!NOTE]
    > Se você receber erros ao publicar os arquivos de modelo do dispositivo, poderá tentar usar **o comando IOT plug and Play: Desconecte o repositório** de modelos para sair e passar pelas etapas novamente.

### <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Usar o Azure IoT Explorer para validar o código

1. Abra o Azure IoT Explorer, você verá a página **configurações de aplicativo** .

1. Insira a cadeia de conexão do Hub IoT e clique em **conectar**.

1. Depois de se conectar, você verá a página Visão geral do dispositivo.

1. Para adicionar o repositório de sua empresa, selecione **configurações**, **+ novo**e, em seguida, **repositório da empresa**.

1. Adicione a cadeia de conexão do repositório de modelos da empresa. Selecione **Ligar**.

1. Na página Visão geral do dispositivo, localize a identidade do dispositivo que você criou anteriormente e selecione-a para exibir mais detalhes.

1. Expanda a interface com ID **urn: azureiot: EnvironmentalSensor: 1** para ver os primitivos de plug and Play IOT-Propriedades, comandos e telemetria.

1. Selecione a página telemetria para exibir os dados de telemetria que o dispositivo está enviando.

1. Selecione a página **Propriedades (não gravável)** para exibir as propriedades não graváveis relatadas pelo dispositivo.

1. Selecione a página **Propriedades (gravável)** para exibir as propriedades graváveis que você pode atualizar.

1. Expanda **nome**da propriedade, atualize com um novo nome e selecione **Atualizar propriedade gravável**. 
2. Para ver o novo nome aparece na coluna **Propriedade** relatada, clique no botão **Atualizar** na parte superior da página.

1. Selecione a página de **comando** para exibir todos os comandos aos quais o dispositivo dá suporte.

1. Expanda o comando **intermitência** e defina um novo intervalo de tempo de intermitência. Selecione **Enviar comando** para chamar o comando no dispositivo.

1. Vá para o dispositivo simulado para verificar se o comando foi executado conforme o esperado.

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você aprendeu a criar um dispositivo de Plug and Play de IoT usando um DCM.

Para saber mais sobre Plug and Play de IoT, prossiga para o tutorial:

> [!div class="nextstepaction"]
> [Criar e testar um modelo de capacidade de dispositivo usando Visual Studio Code](tutorial-pnp-visual-studio-code.md)
