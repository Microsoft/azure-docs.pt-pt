---
title: Conectar o código do dispositivo de exemplo do IoT Plug and Play preview ao Hub IoT (Windows) | Microsoft Docs
description: Compile e execute o código do dispositivo de exemplo de visualização de IoT Plug and Play no Windows que se conecta a um hub IoT. Use a ferramenta Azure IoT Explorer para exibir as informações enviadas pelo dispositivo para o Hub.
author: ChrisGMsft
ms.author: chrisgre
ms.date: 08/02/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 0d89be9da55c97a5b49157251896d3a513c2c6db
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152065"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-running-on-windows-to-iot-hub-c-windows"></a>Início rápido: conectar um aplicativo de dispositivo de visualização de Plug and Play de IoT de exemplo em execução no Windows para o Hub IoT (C Windows)

Este guia de início rápido mostra como criar um aplicativo de dispositivo de Plug and Play de IoT de exemplo, conectá-lo ao Hub IoT e usar a ferramenta do Azure IoT Explorer para exibir as informações que ele envia para o Hub. O aplicativo de exemplo é escrito em C e está incluído no SDK do dispositivo C do Hub IoT do Azure. Um desenvolvedor de soluções pode usar a ferramenta do Azure IoT Explorer para entender os recursos de um dispositivo de Plug and Play de IoT sem a necessidade de exibir qualquer código de dispositivo.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido, você precisa instalar o seguinte software em seu computador local:

* [Visual Studio (Comunidade, profissional ou empresa)](https://visualstudio.microsoft.com/downloads/) – certifique-se de incluir o componente **Gerenciador de pacotes NuGet** e o **desenvolvimento de desktop C++ com** carga de trabalho ao instalar o Visual Studio.
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/).

### <a name="install-the-azure-iot-explorer"></a>Instalar o Azure IoT Explorer

Baixe e instale a versão mais recente do **Azure IOT Explorer** na página [repositório](https://github.com/Azure/azure-iot-explorer/releases) da ferramenta, selecionando o arquivo. msi em "ativos" para a atualização mais recente.

[!INCLUDE [iot-pnp-prepare-iot-hub-windows.md](../../includes/iot-pnp-prepare-iot-hub-windows.md)]

## <a name="prepare-the-development-environment"></a>Preparar o ambiente de desenvolvimento

Neste guia de início rápido, você prepara um ambiente de desenvolvimento que pode ser usado para clonar e criar o SDK do dispositivo C do Hub IoT do Azure.

Abra um prompt de comando no diretório de sua escolha. Execute o seguinte comando para clonar o repositório GitHub de [SDKs e bibliotecas do Azure IOT C](https://github.com/Azure/azure-iot-sdk-c) neste local:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
```

Esta operação deve demorar vários minutos a ser concluída.

## <a name="build-the-code"></a>Compilar o código

Você usa o SDK do dispositivo para criar o código de exemplo incluído. O aplicativo que você cria simula um dispositivo que se conecta a um hub IoT. O aplicativo envia telemetria e propriedades e recebe comandos.

1. Crie um subdiretório `cmake` na pasta raiz do SDK do dispositivo e navegue até essa pasta:

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

Execute um aplicativo de exemplo no SDK para simular um dispositivo de Plug and Play IoT que envia a telemetria para o Hub IoT. Para executar o aplicativo de exemplo, use esses comandos e passe a _cadeia de conexão do dispositivo_ como um parâmetro.

```cmd\sh
cd digitaltwin_client\samples\digitaltwin_sample_device\Release
copy ..\EnvironmentalSensor.interface.json .
digitaltwin_sample_device.exe "<YourDeviceConnectionString>"
```

Agora, o dispositivo está pronto para receber comandos e atualizações de propriedade e começou a enviar dados de telemetria para o Hub. Mantenha o exemplo em execução enquanto você conclui as próximas etapas.

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Usar o Azure IoT Explorer para validar o código

1. Abra o Azure IoT Explorer. Você verá a página **configurações de aplicativo** .

1. Insira a _cadeia de conexão do Hub IOT_ e selecione **conectar**.

1. Depois de se conectar, você verá a página Visão geral de **dispositivos** .

1. Para garantir que a ferramenta possa ler as definições de modelo de interface do seu dispositivo, selecione **configurações**. No menu configurações, **no dispositivo conectado** pode já aparecer nas configurações de plug and Play; Se não tiver, selecione **+ Adicionar fonte de definição de módulo** e, em seguida, **no dispositivo conectado** para adicioná-lo.

1. De volta à página Visão geral de **dispositivos** , localize a identidade do dispositivo que você criou anteriormente. Com o aplicativo do dispositivo ainda em execução no prompt de comando, verifique se o **estado da conexão** do dispositivo no Azure IOT Explorer está relatando como _conectado_ (caso contrário, pressione **Atualizar** até que ele esteja). Selecione o dispositivo para exibir mais detalhes.

1. Expanda a interface com ID **urn: YOUR_COMPANY_NAME_HERE: EnvironmentalSensor: 1** para revelar a interface e a IOT plug and Play primitivos — Propriedades, comandos e telemetria.

1. Selecione a página **telemetria** e clique em _Iniciar_ para exibir os dados de telemetria que o dispositivo está enviando.

1. Selecione a página **Propriedades (não gravável)** para exibir as propriedades não graváveis relatadas pelo dispositivo.

1. Selecione a página **Propriedades (gravável)** para exibir as propriedades graváveis que você pode atualizar.

1. Expanda **nome**da propriedade, atualize com um novo nome e selecione **Atualizar propriedade gravável**. 

1. Para ver o novo nome aparecer na coluna **Propriedade relatada** , selecione o botão **Atualizar** na parte superior da página.

1. Selecione a página **comandos** para exibir todos os comandos aos quais o dispositivo dá suporte.

1. Expanda o comando **intermitência** e defina um novo intervalo de tempo de intermitência. Selecione **Enviar comando** para chamar o comando no dispositivo.

1. Acesse o prompt de comando do dispositivo simulado e leia as mensagens de confirmação impressas para verificar se os comandos foram executados conforme o esperado.

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você aprendeu como conectar um dispositivo de Plug and Play IoT a um hub IoT. Para saber mais sobre como criar uma solução que interage com seus dispositivos de Plug and Play de IoT, confira:

> [!div class="nextstepaction"]
> [Como: conectar-se e interagir com um dispositivo de visualização de Plug and Play de IoT](howto-develop-solution.md)
