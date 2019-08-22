---
title: Conectar o código do dispositivo de exemplo do IoT Plug and Play preview ao Hub IoT | Microsoft Docs
description: Compile e execute o código do dispositivo de exemplo de visualização de IoT Plug and Play que se conecta a um hub IoT. Use a ferramenta Azure IoT Explorer para exibir as informações enviadas pelo dispositivo para o Hub.
author: ChrisGMsft
ms.author: chrisgre
ms.date: 08/02/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: b233fcecfe80d1ce4464d2d02fdddb188f9265a7
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878244"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub"></a>Início rápido: Conectar um aplicativo de dispositivo de visualização de IoT Plug and Play de exemplo ao Hub IoT

Este guia de início rápido mostra como criar um aplicativo de dispositivo de Plug and Play de IoT de exemplo, conectá-lo ao Hub IoT e usar a ferramenta do Azure IoT Explorer para exibir as informações que ele envia para o Hub. O aplicativo de exemplo é escrito em C e está incluído no SDK do dispositivo IoT do Azure para C. Um desenvolvedor de soluções pode usar a ferramenta do Azure IoT Explorer para entender os recursos de um dispositivo de Plug and Play de IoT sem a necessidade de exibir qualquer código de dispositivo.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido, você precisa instalar o seguinte software em seu computador local:

* [Visual Studio (Comunidade, profissional ou empresa)](https://visualstudio.microsoft.com/downloads/) – certifique-se de incluir o componente **Gerenciador de pacotes NuGet** e o **desenvolvimento de desktop C++ com** carga de trabalho ao instalar o Visual Studio.
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/).

### <a name="install-the-azure-iot-explorer"></a>Instalar o Azure IoT Explorer

Baixe e instale a ferramenta do Azure IoT Explorer na página de [versão mais recente](https://github.com/Azure/azure-iot-explorer/releases) .

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

Abra uma linha de comandos. Execute o seguinte comando para clonar o [SDK C do Azure IoT](https://github.com/Azure/azure-iot-sdk-c) no repositório do GitHub:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
```

Esta operação deve demorar vários minutos a ser concluída.

## <a name="build-the-code"></a>Compilar o código

O aplicativo que você cria simula um dispositivo que se conecta a um hub IoT. O aplicativo envia telemetria e propriedades e recebe comandos.

1. Crie um `cmake` subdiretório na pasta raiz do SDK do dispositivo e navegue até essa pasta:

    ```cmd\sh
    cd <root folder>\azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Execute os seguintes comandos para compilar o SDK do dispositivo e o stub do código gerado:

    ```cmd\sh
    cmake ..
    cmake --build . -- /m /p:Configuration=Release
    ```

    > [!NOTE]
    > Se o CMake não conseguir C++ localizar seu compilador, você obterá erros de compilação ao executar o comando anterior. Se isso acontecer, tente executar esse comando no [prompt de comando do Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs).

## <a name="run-the-device-sample"></a>Executar o exemplo de dispositivo

Execute o aplicativo passando a cadeia de conexão do dispositivo do Hub IoT como parâmetro.

```cmd\sh
cd digitaltwin_client\samples\digitaltwin_sample_device\Release
copy ..\EnvironmentalSensor.interface.json .
digitaltwin_sample_device.exe "[IoT Hub device connection string]"
```

O aplicativo do dispositivo começa a enviar dados para o Hub IoT.

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Usar o Azure IoT Explorer para validar o código

1. Abra o Azure IoT Explorer, você verá a página **configurações de aplicativo** .

1. Insira a cadeia de conexão do Hub IoT e clique em **conectar**.

1. Depois de se conectar, você verá a página Visão geral do dispositivo.

1. Para adicionar o repositório de sua empresa, selecione **configurações**, **+ novo**e, em seguida, **no dispositivo conectado**.

1. Na página Visão geral do dispositivo, localize a identidade do dispositivo que você criou anteriormente e selecione-a para exibir mais detalhes.

1. Expanda a interface com ID **urn: YOUR_COMPANY_NAME_HERE: EnvironmentalSensor: 1** para ver os primitivos de plug and Play IOT-Propriedades, comandos e telemetria.

1. Selecione a página telemetria para exibir os dados de telemetria que o dispositivo está enviando.

1. Selecione a página **Propriedades (não gravável)** para exibir as propriedades não graváveis relatadas pelo dispositivo.

1. Selecione a página **Propriedades (gravável)** para exibir as propriedades graváveis que você pode atualizar.

1. Expanda **nome**da propriedade, atualize com um novo nome e selecione **Atualizar propriedade gravável**. 

1. Para ver o novo nome aparece na coluna **Propriedade** relatada, clique no botão **Atualizar** na parte superior da página.

1. Selecione a página de **comando** para exibir todos os comandos aos quais o dispositivo dá suporte.

1. Expanda o comando **intermitência** e defina um novo intervalo de tempo de intermitência. Selecione **Enviar comando** para chamar o comando no dispositivo.

1. Vá para o dispositivo simulado para verificar se o comando foi executado conforme o esperado.

## <a name="next-steps"></a>Passos Seguintes

Neste guia de início rápido, você aprendeu como conectar um dispositivo de Plug and Play IoT a um hub IoT. Para saber mais sobre como criar uma solução que interage com seus dispositivos de Plug and Play de IoT, confira:

> [!div class="nextstepaction"]
> [Procedimento: Conectar-se a um dispositivo de visualização de Plug and Play IoT e interagir com ele](howto-develop-solution.md)
