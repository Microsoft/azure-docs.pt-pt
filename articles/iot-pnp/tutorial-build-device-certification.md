---
title: Criar um dispositivo de visualização de Plug and Play IoT pronto para certificação | Microsoft Docs
description: Como desenvolvedor de dispositivos, saiba como você pode criar um dispositivo de visualização de IoT Plug and Play que está pronto para a certificação.
author: tbhagwat3
ms.author: tanmayb
ms.date: 12/28/2019
ms.topic: tutorial
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: ce7d3ee8a0d05d837bc0049cba688cffe14d8a8c
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721647"
---
# <a name="build-an-iot-plug-and-play-preview-device-thats-ready-for-certification"></a>Criar um dispositivo de visualização de Plug and Play IoT pronto para certificação

Este tutorial descreve como, como desenvolvedor de dispositivos, você pode criar um dispositivo de visualização de Plug and Play IoT que está pronto para a certificação.

Os testes de certificação verificam se:

- O código do dispositivo Plug and Play IoT está instalado em seu dispositivo.
- O código do dispositivo de Plug and Play de IoT é criado com o SDK do Azure IoT.
- O seu código de dispositivo suporta o Serviço de Provisionamento de [Dispositivos Hub Azure IoT](../iot-dps/about-iot-dps.md).
- O código do dispositivo implementa a interface de informações do dispositivo.
- O modelo de funcionalidade e o código do dispositivo funcionam com IoT Central.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

- [Visual Studio Code](https://code.visualstudio.com/download)
- [Ferramentas Azure IoT para](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) pacote de extensão de código VS

Também precisa de completar o modelo de capacidade do [dispositivo Use para criar um dispositivo](quickstart-create-pnp-device-windows.md) de arranque rápido para o Windows. O guia de início rápido mostra como configurar seu ambiente de desenvolvimento usando o Vcpkg e criar um projeto de exemplo.

## <a name="store-a-capability-model-and-interfaces"></a>Armazenar um modelo de funcionalidade e interfaces

Para dispositivos de Plug and Play IoT, você deve criar um modelo de funcionalidade e interfaces que definem os recursos do dispositivo como arquivos JSON.

Você pode armazenar esses arquivos JSON em três locais diferentes:

- O repositório de modelo público.
- O repositório de modelos da empresa.
- Em seu dispositivo.

Atualmente, para certificar seu dispositivo, os arquivos devem ser armazenados em seu repositório de modelo da empresa ou no repositório de modelo público.

## <a name="include-the-required-interfaces"></a>Incluir as interfaces necessárias

Para passar no processo de certificação, deve incluir e implementar a interface de **Informação** do Dispositivo no seu modelo de capacidade. Essa interface tem a seguinte identificação:

```json
"@id": "urn:azureiot:DeviceManagement:DeviceInformation:1"
```

> [!NOTE]
> Se tiver concluído o [Quickstart: Utilize um modelo](quickstart-create-pnp-device-windows.md)de capacidade de dispositivo para criar um dispositivo, já incluiu a interface de **Informação** do Dispositivo no seu modelo.

Para incluir a interface de **Informação** do Dispositivo no modelo do seu dispositivo, adicione o ID da interface à propriedade `implements` do modelo de capacidade:

```json
{
  "@id": "urn:yourcompanyname:sample:ThermostatDevice:1",
  "@type": "CapabilityModel",
  "displayName": "Thermostat T-1000",
  "implements": [
    "urn:yourcompanyname:sample:Thermostat:1",
    "urn:azureiot:DeviceManagement:DeviceInformation:1"
  ],
  "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
}
```

Para visualizar a interface de **informação** do dispositivo no Código VS:

1. Utilize **ctrl+Shift+P** para abrir a paleta de comando.

1. Introduza **plug and play** e, em seguida, selecione o comando de **repositório ioT plug e play Open Model Repositório.** Escolha **o Repositório de Modelo Público Aberto.** O repositório de modelo público é aberto no VS Code.

1. No repositório de modelos públicos, selecione o separador **Interfaces,** selecione o ícone do filtro e introduza **informações do dispositivo** no campo do filtro.

1. Para criar uma cópia local da interface informação do **dispositivo,** selecione-a na lista filtrada e, em seguida, selecione **Download**. VS Code exibe o arquivo de interface.

Para visualizar a interface de **informação** do dispositivo utilizando o Azure CLI:

1. [Instale a extensão CLI Azure IoT](howto-install-pnp-cli.md).

1. Use o seguinte comando CLI do Azure para mostrar uma interface com a ID da interface de informações do dispositivo:

    ```cmd/sh
    az iot pnp interface show --interface urn:azureiot:DeviceManagement:DeviceInformation:1
    ```

Para mais informações, consulte [Instalar e utilizar a extensão Azure IoT para o Azure CLI](howto-install-pnp-cli.md).

## <a name="update-device-code"></a>Atualizar código do dispositivo

### <a name="enable-device-provisioning-through-the-azure-iot-device-provisioning-service-dps"></a>Habilitar o provisionamento de dispositivos por meio do DPS (serviço de provisionamento de dispositivos) do Azure IoT

Para certificar o dispositivo, deve ativar o fornecimento através do Serviço de Provisionamento de [Dispositivos Azure IoT (DPS)](https://docs.microsoft.com/azure/iot-dps/about-iot-dps). Para adicionar a capacidade de usar o DPS, você pode gerar o stub do código C no VS Code. Siga estes passos.

1. Abra a pasta com o ficheiro DCM no Código VS, utilize **ctrl+Shift+P** para abrir a paleta de comando, introduzir plug **e reproduzir IoT,** e selecionar **Generate Device Code Stub**.

1. Escolha o arquivo DCM que você deseja usar para gerar o stub do código do dispositivo.

1. Introduza o nome do projeto, como **sample_device**. Este é o nome do seu aplicativo de dispositivo.

1. Escolha **ansi C** como língua.

1. Escolha **através da chave simétrica DPS (Device Provisioning Service)** como método de ligação.

1. Escolha o **CMake Project no Windows** como modelo de projeto.

1. Escolha **via Vcpkg** como forma de incluir o dispositivo SDK.

1. VS Code abre uma nova janela com arquivos stub de código de dispositivo gerados.

## <a name="build-and-run-the-code"></a>Compilar e executar o código

Use o pacote Vcpkg para criar o stub de código de dispositivo gerado. O aplicativo que você cria simula um dispositivo que se conecta a um hub IoT. O aplicativo envia telemetria e propriedades e recebe comandos.

1. Crie um subdiretório `cmake` na pasta `sample_device` e navegue para essa pasta:

    ```cmd
    mkdir cmake
    cd cmake
    ```

1. Execute os seguintes comandos para compilar o stub de código gerado (substituindo o espaço reservado pelo diretório do seu repositório Vcpkg):

    ```cmd
    cmake .. -G "Visual Studio 16 2019" -A Win32 -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"

    cmake --build .
    ```
    
    > [!NOTE]
    > Se você estiver usando o Visual Studio 2017 ou 2015, precisará especificar o gerador de CMake com base nas ferramentas de compilação que você está usando:
    >```cmd
    ># Either
    >cmake .. -G "Visual Studio 15 2017" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}\scripts\buildsystems\vcpkg.cmake"
    ># or
    >cmake .. -G "Visual Studio 14 2015" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}\scripts\buildsystems\vcpkg.cmake"
    >```

    > [!NOTE]
    > Se o CMake não conseguir C++ localizar seu compilador, você obterá erros de compilação ao executar o comando anterior. Se isso acontecer, tente executar este comando no comando do [Estúdio Visual.](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs)

1. Depois de a construção completar com sucesso, introduza as credenciais DPS **(DPS ID Scope,** **DPS Symmetric Key,** **Device Id**) como parâmetros para a aplicação. Para obter as credenciais do portal de certificação, consulte [Connect e teste o seu dispositivo IoT Plug and Play](tutorial-certification-test.md#connect-and-discover-interfaces).

    ```cmd\sh
    .\Debug\sample_device.exe [Device ID] [DPS ID Scope] [DPS symmetric key]
    ```

### <a name="implement-standard-interfaces"></a>Implementar interfaces padrão

#### <a name="implement-the-model-information-and-sdk-information-interfaces"></a>Implementar as informações do modelo e as interfaces de informações do SDK

O SDK do dispositivo IoT do Azure implementa as informações do modelo e as interfaces de informações do SDK. Se você usar a função de geração de código no VS Code, o código do dispositivo usará o SDK do dispositivo do IoT Plug and Play.

Se você optar por não usar o SDK do dispositivo IoT do Azure, poderá usar o código-fonte do SDK como referência para sua própria implementação.

#### <a name="implement-the-device-information-interface"></a>Implementar a interface de informações do dispositivo

Implemente a interface de **informação** do dispositivo no seu dispositivo e forneça informações específicas do dispositivo a partir do momento de execução.

Pode utilizar um exemplo de implementação da interface de **informação** do dispositivo para [o Linux](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) como referência.

### <a name="implement-all-the-capabilities-defined-in-your-model"></a>Implementar todos os recursos definidos em seu modelo

Durante a certificação, seu dispositivo é testado programaticamente para garantir que ele implemente os recursos definidos em suas interfaces. Use o código de status HTTP 501 para responder à propriedade de leitura/gravação e às solicitações de comando se o dispositivo não implementá-las.

## <a name="next-steps"></a>Passos Seguintes

Agora que você criou um dispositivo IoT Plug and Play pronto para a certificação, a próxima etapa sugerida é:

> [!div class="nextstepaction"]
> [Saiba como certificar o seu dispositivo](tutorial-certification-test.md)
