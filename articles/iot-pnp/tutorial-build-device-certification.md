---
title: Construa um dispositivo IoT Plug e Play Preview que esteja pronto para certificação / Microsoft Docs
description: Como desenvolvedor de dispositivos, saiba como pode construir um dispositivo IoT Plug e Play Preview que esteja pronto para a certificação.
author: tbhagwat3
ms.author: tanmayb
ms.date: 12/28/2019
ms.topic: tutorial
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: e97aa07d2a43a03805fd881c674157ee676c37b4
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80239912"
---
# <a name="build-an-iot-plug-and-play-preview-device-thats-ready-for-certification"></a>Construa um dispositivo ioT Plug e Play Preview que esteja pronto para certificação

Este tutorial descreve como, como desenvolvedor de dispositivos, pode construir um dispositivo IoT Plug e Play Preview que está pronto para a certificação.

Os testes de certificação verificam que:

- O seu código de dispositivo IoT Plug and Play está instalado no seu dispositivo.
- O seu código de dispositivo IoT Plug and Play foi construído com O SDK Azure IoT.
- O seu código de dispositivo suporta o Serviço de Provisionamento de [Dispositivos Hub Azure IoT](../iot-dps/about-iot-dps.md).
- O código do dispositivo implementa a interface de informação do dispositivo.
- O modelo de capacidade e o código do dispositivo funcionam com a IoT Central.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

- [Visual Studio Code](https://code.visualstudio.com/download)
- [Ferramentas Azure IoT para](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) pacote de extensão de código VS

Também precisa de completar o modelo de capacidade do [dispositivo Use para criar um dispositivo](quickstart-create-pnp-device-windows.md) de arranque rápido para o Windows. O quickstart mostra-lhe como configurar o seu ambiente de desenvolvimento usando vcpkg e criar um projeto de amostra.

## <a name="store-a-capability-model-and-interfaces"></a>Armazenar um modelo de capacidade e interfaces

Para dispositivos IoT Plug and Play, deve ser autor de um modelo de capacidade e interfaces que definam as capacidades do dispositivo como ficheiros JSON.

Pode armazenar estes ficheiros JSON em três locais diferentes:

- O repositório de modelos públicos.
- O seu repositório modelo da empresa.
- No seu dispositivo.

Atualmente, para certificar o seu dispositivo, os ficheiros devem ser armazenados quer no repositório do modelo da empresa, quer no repositório de modelos públicos.

## <a name="include-the-required-interfaces"></a>Incluir as interfaces necessárias

Para passar no processo de certificação, deve incluir e implementar a interface de **Informação** do Dispositivo no seu modelo de capacidade. Esta interface tem a seguinte identificação:

```json
"@id": "urn:azureiot:DeviceManagement:DeviceInformation:1"
```

> [!NOTE]
> Se tiver concluído o [Quickstart: Utilize um modelo](quickstart-create-pnp-device-windows.md)de capacidade de dispositivo para criar um dispositivo, já incluiu a interface de **Informação** do Dispositivo no seu modelo.

Para incluir a interface de **Informação** do Dispositivo `implements` no modelo do seu dispositivo, adicione o ID da interface à propriedade do modelo de capacidade:

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

1. Introduza **plug and play** e, em seguida, selecione o comando de **repositório ioT plug e play Open Model Repositório.** Escolha **o Repositório de Modelo Público Aberto.** O repositório de modelopúblico abre no Código VS.

1. No repositório de modelos públicos, selecione o separador **Interfaces,** selecione o ícone do filtro e introduza **informações do dispositivo** no campo do filtro.

1. Para criar uma cópia local da interface informação do **dispositivo,** selecione-a na lista filtrada e, em seguida, selecione **Download**. O Código VS exibe o ficheiro de interface.

Para visualizar a interface de **informação** do dispositivo utilizando o Azure CLI:

1. [Instale a extensão CLI Azure IoT](howto-install-pnp-cli.md).

1. Utilize o seguinte comando Azure CLI para mostrar uma interface com o ID da interface de informação do dispositivo:

    ```azurecli
    az iot pnp interface show --interface urn:azureiot:DeviceManagement:DeviceInformation:1
    ```

Para mais informações, consulte [Instalar e utilizar a extensão Azure IoT para o Azure CLI](howto-install-pnp-cli.md).

## <a name="update-device-code"></a>Atualizar código do dispositivo

### <a name="enable-device-provisioning-through-the-azure-iot-device-provisioning-service-dps"></a>Ativar o fornecimento de dispositivos através do Serviço de Provisionamento de Dispositivos Azure IoT (DPS)

Para certificar o dispositivo, deve ativar o fornecimento através do Serviço de Provisionamento de [Dispositivos Azure IoT (DPS)](https://docs.microsoft.com/azure/iot-dps/about-iot-dps). Para adicionar a capacidade de utilização de DPS, pode gerar o código C no código VS. Siga estes passos.

1. Abra a pasta com o ficheiro DCM no Código VS, utilize **ctrl+Shift+P** para abrir a paleta de comando, introduzir plug **e reproduzir IoT,** e selecionar **Generate Device Code Stub**.

1. Escolha o ficheiro DCM que pretende utilizar para gerar o código do dispositivo.

1. Introduza o nome do projeto, como **sample_device**. Este é o nome da aplicação do seu dispositivo.

1. Escolha **ansi C** como língua.

1. Escolha **através da chave simétrica DPS (Device Provisioning Service)** como método de ligação.

1. Escolha o **CMake Project no Windows** como modelo de projeto.

1. Escolha **via Vcpkg** como forma de incluir o dispositivo SDK.

1. O Código VS abre uma nova janela com ficheiros de código de dispositivo gerados.

## <a name="build-and-run-the-code"></a>Construir e executar o código

Usa a embalagem vcpkg para construir o código do dispositivo gerado. A aplicação que constrói simula um dispositivo que se conecta a um hub IoT. A aplicação envia telemetria e propriedades e recebe comandos.

1. Crie `cmake` um subdiretório na `sample_device` pasta e navegue para essa pasta:

    ```cmd
    mkdir cmake
    cd cmake
    ```

1. Executar os seguintes comandos para construir o tubo de código gerado (substituindo o espaço reservado pelo diretório do seu repo vcpkg):

    ```cmd
    cmake .. -G "Visual Studio 16 2019" -A Win32 -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"

    cmake --build .
    ```
    
    > [!NOTE]
    > Se estiver a utilizar o Visual Studio 2017 ou 2015, precisa especificar o gerador CMake com base nas ferramentas de construção que está a utilizar:
    >```cmd
    ># Either
    >cmake .. -G "Visual Studio 15 2017" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}\scripts\buildsystems\vcpkg.cmake"
    ># or
    >cmake .. -G "Visual Studio 14 2015" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}\scripts\buildsystems\vcpkg.cmake"
    >```

    > [!NOTE]
    > Se o cmake não conseguir encontrar o seu compilador C++, obtém erros de construção quando executa o comando anterior. Se isso acontecer, tente executar este comando no comando do [Estúdio Visual.](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs)

1. Depois de a construção completar com sucesso, introduza as credenciais DPS **(DPS ID Scope,** **DPS Symmetric Key,** **Device Id**) como parâmetros para a aplicação. Para obter as credenciais do portal de certificação, consulte [Connect e teste o seu dispositivo IoT Plug and Play](tutorial-certification-test.md#connect-and-discover-interfaces).

    ```cmd\sh
    .\Debug\sample_device.exe [Device ID] [DPS ID Scope] [DPS symmetric key]
    ```

### <a name="implement-standard-interfaces"></a>Implementar interfaces padrão

#### <a name="implement-the-model-information-and-sdk-information-interfaces"></a>Implementar as interfaces de Informação do Modelo e SDK

O dispositivo Azure IoT SDK implementa as interfaces de Informação do Modelo e SDK. Se utilizar a função de geração de códigos no Código VS, o código do dispositivo utiliza o Dispositivo IoT Plug e Play Device SDK.

Se optou por não utilizar o dispositivo Azure IoT SDK, pode utilizar o código fonte SDK como referência para a sua própria implementação.

#### <a name="implement-the-device-information-interface"></a>Implementar a interface de informação do dispositivo

Implemente a interface de **informação** do dispositivo no seu dispositivo e forneça informações específicas do dispositivo a partir do momento de execução.

Pode utilizar um exemplo de implementação da interface de **informação** do dispositivo para [o Linux](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) como referência.

### <a name="implement-all-the-capabilities-defined-in-your-model"></a>Implementar todas as capacidades definidas no seu modelo

Durante a certificação, o seu dispositivo é testado programáticamente para garantir que implementa as capacidades definidas nas suas interfaces. Utilize o código de estado HTTP 501 para responder a pedidos de propriedade e comando de leitura se o seu dispositivo não os implementar.

## <a name="next-steps"></a>Passos seguintes

Agora que construiu um dispositivo IoT Plug and Play pronto para certificação, o próximo passo sugerido é:

> [!div class="nextstepaction"]
> [Saiba como certificar o seu dispositivo](tutorial-certification-test.md)
