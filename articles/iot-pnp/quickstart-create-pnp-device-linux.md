---
title: Criar um dispositivo de visualização do Azure IoT Plug and Play (Linux) | Microsoft Docs
description: Use um modelo de funcionalidade de dispositivo para gerar o código do dispositivo. Em seguida, execute o código do dispositivo e veja o dispositivo conectar-se ao Hub IoT.
author: dominicbetts
ms.author: dobett
ms.date: 09/10/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: bc4a64985d19daf9d2f6bb86b6cfb4814f141e4b
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152054"
---
# <a name="quickstart-use-a-device-capability-model-to-create-an-iot-plug-and-play-preview-device-linux"></a>Início rápido: usar um modelo de capacidade de dispositivo para criar um dispositivo de visualização de Plug and Play IoT (Linux)

Um DCM ( _modelo de funcionalidade de dispositivo_ ) descreve os recursos de um dispositivo de plug and Play IOT. Um DCM geralmente é associado a um SKU de produto. Os recursos definidos no DCM são organizados em interfaces reutilizáveis. Você pode gerar um esqueleto de código de dispositivo de um DCM. Este guia de início rápido mostra como usar VS Code em Ubuntu Linux para criar um dispositivo de Plug and Play de IoT usando um DCM.

## <a name="prerequisites"></a>Pré-requisitos

Este guia de início rápido pressupõe que você está usando Ubuntu Linux com um ambiente de área de trabalho. As etapas neste tutorial foram testadas usando o Ubuntu 18, 4.

Para concluir este guia de início rápido, você precisa instalar o seguinte software em seu computador Linux local:

* Instale o **gcc**, **git**, **CMake**e todas as dependências usando o comando `apt-get`:

    ```sh
    sudo apt-get update
    sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
    ```

    Verifique se a versão do `cmake` está acima de **2.8.12** e se a versão do **gcc** está acima de **4.4.7**.

    ```sh
    cmake --version
    gcc --version
    ```

* [Visual Studio Code](https://code.visualstudio.com/).

### <a name="install-azure-iot-tools"></a>Instalar as ferramentas do Azure IoT

Use as etapas a seguir para instalar as [Ferramentas do Azure IOT para](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) o pacote de extensão vs Code:

1. Em VS Code, selecione a guia **extensões** .
1. Pesquise pelas **Ferramentas do Azure IOT**.
1. Selecione **Instalar**.

### <a name="get-the-connection-string-for-your-company-model-repository"></a>Obter a cadeia de conexão para o repositório de modelos da empresa

Você pode encontrar a _cadeia de conexão do repositório de modelos da empresa_ no portal do [portal do Azure Certified para IOT](https://preview.catalog.azureiotsolutions.com) ao entrar com uma conta corporativa ou de estudante da Microsoft, ou sua ID de parceiro da Microsoft, se você tiver uma. Depois de entrar, selecione **repositório da empresa** e, em seguida, **cadeias de conexão**.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-an-iot-hub"></a>Preparar um hub IoT

Você também precisa de um hub IoT do Azure em sua assinatura do Azure para concluir este guia de início rápido. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. Se você ainda não tiver um hub IoT para usar, siga o restante desta seção para criar uma.

Se você estiver usando o CLI do Azure localmente, a versão `az` deverá ser **2.0.75** ou posterior, a Azure cloud Shell usará a versão mais recente. Use o comando `az --version` para verificar a versão instalada em seu computador.

Execute o comando a seguir para adicionar a extensão de IoT Microsoft Azure para CLI do Azure à sua instância de Cloud Shell:

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

As etapas neste início rápido exigem a versão **0.8.5** ou posterior da extensão. Use o comando `az extension list` para verificar a versão que você instalou e o comando `az extension update` para atualizar, se necessário.

Se não tiver um hub IoT, crie um usando os comandos a seguir, substituindo `<YourIoTHubName>` por um nome exclusivo de sua escolha. Se você estiver executando esses comandos localmente, primeiro entre na sua assinatura do Azure usando `az login`. Se você estiver executando esses comandos no Azure cloud Shell, você estará conectado automaticamente:

  ```azurecli-interactive
  az group create --name pnpquickstarts_rg --location centralus
  az iot hub create --name <YourIoTHubName> \
    --resource-group pnpquickstarts_rg --sku S1
  ```

Os comandos anteriores criam um grupo de recursos chamado `pnpquickstarts_rg` e um hub IoT na região central dos EUA.

> [!IMPORTANT]
> Durante a visualização pública, os recursos de Plug and Play de IoT só estão disponíveis em hubs IoT criados nas regiões **EUA Central**, **Europa setentrional**e **leste do Japão** .

Execute o comando a seguir para criar uma identidade de dispositivo no Hub IoT. Substitua os espaços reservados **nomedoseuhubiot** e **iddoseudispositivo** por seu próprio _nome de Hub IOT_ e uma _ID de dispositivo_ de sua escolha.

```azurecli-interactive
az iot hub device-identity create --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

Execute os comandos a seguir para obter a _cadeia de conexão do dispositivo_ para o dispositivo que você acabou de registrar (observação para uso posterior).

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDevice> --output table
```

## <a name="author-your-model"></a>Crie seu modelo

Neste guia de início rápido, você usa um modelo de funcionalidade de dispositivo de exemplo existente e as interfaces associadas.

1. Crie um diretório `pnp_app` na unidade local. Você usa essa pasta para os arquivos de modelo de dispositivo e o stub de código de dispositivo.

    ```bash
    cd ~
    mkdir pnp_app
    ```

1. Baixe o modelo de funcionalidade do dispositivo e os arquivos de exemplo de interface para a pasta `pnp_app`.

    ```bash
    cd pnp_app
    curl -O -L https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/SampleDevice.capabilitymodel.json
    curl -O -L https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensor.interface.json
    ```

1. Abra `pnp_app` pasta com VS Code. Você pode exibir os arquivos com o IntelliSense:

    ![Modelo de funcionalidade do dispositivo](media/quickstart-create-pnp-device-linux/dcm.png)

1. Nos arquivos que você baixou, substitua `<YOUR_COMPANY_NAME_HERE>` nos campos `@id` e `schema` com um valor exclusivo. Use somente os caracteres a-z, A-Z, 0-9 e sublinhado. Para obter mais informações, consulte [formato do identificador de entrelaça digital](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format).

## <a name="generate-the-c-code-stub"></a>Gerar o stub do código C

Agora que você tem um DCM e suas interfaces associadas, você pode gerar o código do dispositivo que implementa o modelo. Para gerar o stub do código C no VS Code:

1. Com a pasta `pnp_app` aberta no VS Code, use **Ctrl + Shift + P** para abrir a paleta de comandos, insira **plug and Play de IOT**e selecione **gerar stub de código de dispositivo**.

    > [!NOTE]
    > Na primeira vez que você usar o utilitário gerador de código de Plug and Play IoT, levará alguns segundos para baixar e instalar automaticamente.

1. Escolha o arquivo **SampleDevice. capabilitymodel. JSON** a ser usado para gerar o stub do código de dispositivo.

1. Insira o nome do projeto **sample_device**. Esse será o nome do aplicativo do dispositivo.

1. Escolha **ANSI C** como seu idioma.

1. Escolha **por meio da cadeia de conexão do dispositivo Hub IOT** como método de conexão.

1. Escolha **projeto CMake no Linux** como seu modelo de projeto.

1. Escolha **por meio do código-fonte** como a maneira de incluir o SDK do dispositivo.

1. Uma nova pasta chamada **sample_device** é criada no mesmo local que o arquivo DCM e, nele, são os arquivos stub de código de dispositivo gerados. VS Code abre uma nova janela para exibi-las.
    ![o código do dispositivo](media/quickstart-create-pnp-device-linux/device-code.png)

## <a name="build-and-run-the-code"></a>Compilar e executar o código

Você usa o código-fonte do SDK do dispositivo para criar o stub do código de dispositivo gerado. O aplicativo que você cria simula um dispositivo que se conecta a um hub IoT. O aplicativo envia telemetria e propriedades e recebe comandos.

1. Execute os seguintes comandos para baixar o código-fonte do SDK do dispositivo:

    ```bash
    cd ~/pnp_app/sample_device
    git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
    ```

1. Crie uma pasta de Build do **CMake** para o aplicativo **sample_device** :

    ```bash
    cd ~/pnp_app/sample_device
    mkdir cmake
    cd cmake
    ```

1. Execute CMake para compilar seu aplicativo com o SDK:

    ```bash
    cmake .. -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -Dskip_samples:BOOL=ON
    cmake --build .
    ```

1. Depois que a compilação for concluída com êxito, execute o aplicativo passando a cadeia de conexão do dispositivo do Hub IoT como parâmetro.

    ```sh
    cd ~/pnp_app/sample_device/cmake
    ./sample_device "<YourDeviceConnectionString>"
    ```

1. O aplicativo do dispositivo começa a enviar dados para o Hub IoT.

    ![Aplicativo de dispositivo em execução](media/quickstart-create-pnp-device-linux/device-app-running.png)

## <a name="validate-the-code"></a>Validar o código

### <a name="publish-device-model-files-to-model-repository"></a>Publicar arquivos de modelo de dispositivo no repositório de modelos

Para validar o código do dispositivo com a CLI **AZ** , você precisa publicar os arquivos no repositório de modelos.

1. Com a pasta `pnp_app` aberta no VS Code, use **Ctrl + Shift + P** para abrir a paleta de comandos, digite e selecione **plug-in IOT & Play: enviar arquivos para o repositório de modelos**.

1. Selecione `SampleDevice.capabilitymodel.json` e `EnvironmentalSensor.interface.json` arquivos.

1. Insira a cadeia de conexão do repositório de modelos da empresa.

    > [!NOTE]
    > A cadeia de conexão só é necessária na primeira vez que você se conecta ao repositório.

1. Em VS Code janela de saída e notificação, você pode verificar se os arquivos foram publicados com êxito.

    > [!NOTE]
    > Se você receber erros ao publicar os arquivos de modelo do dispositivo, poderá tentar usar o comando **IoT plug and Play: sair do repositório de modelos** para sair e passar pelas etapas novamente.

### <a name="use-the-azure-iot-cli-to-validate-the-code"></a>Usar a CLI do Azure IoT para validar o código

Depois que o exemplo de cliente do dispositivo for iniciado, você poderá verificar se ele está funcionando com o CLI do Azure.

Use o comando a seguir para exibir a telemetria que o dispositivo de exemplo está enviando. Talvez seja necessário aguardar um ou dois minutos antes de ver qualquer telemetria na saída:

```azurecli-interactive
az iot dt monitor-events --hub-name <YourIoTHubNme> --device-id <YourDevice>
```

Use o seguinte comando para exibir todas as propriedades enviadas pelo dispositivo:

```azurecli-interactive
az iot dt list-properties --device-id <YourDevice> --hub-name <YourIoTHubNme> --source private --repo-login "<YourCompanyModelRepositoryConnectionString>"
```
[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você aprendeu a criar um dispositivo de Plug and Play de IoT usando um DCM.

Para saber mais sobre o DCMs e como criar seus próprios modelos, prossiga para o tutorial:

> [!div class="nextstepaction"]
> [Tutorial: criar e testar um modelo de capacidade de dispositivo usando Visual Studio Code](tutorial-pnp-visual-studio-code.md)
