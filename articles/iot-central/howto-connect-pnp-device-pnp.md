---
title: Conectar um dispositivo de Plug and Play IoT ao IoT Central | Microsoft Docs
description: Como desenvolvedor de dispositivos, saiba como usar Visual Studio Code para criar e testar um dispositivo de Plug and Play de IoT que se conecta ao IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 07/10/2019
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 6eb73c1c2b768d82143890415702950905f261f6
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/23/2019
ms.locfileid: "69997230"
---
# <a name="use-visual-studio-code-to-create-an-iot-plug-and-play-device-that-connects-to-iot-central"></a>Use Visual Studio Code para criar um dispositivo de Plug and Play IoT que se conecta ao IoT Central

Este guia de instruções mostra como:

* Como um operador, para adicionar e configurar um dispositivo real em seu aplicativo de IoT Central do Azure.

* Como desenvolvedor de dispositivos, para usar Visual Studio Code para criar um dispositivo de [plug and Play IOT](../iot-pnp/overview-iot-plug-and-play.md) que se conecta ao seu aplicativo IOT central.

Você usa um [modelo de capacidade de dispositivo](./concepts-architecture-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) para definir o dispositivo que se conecta ao IOT central. Neste guia, você usa um modelo que define um dispositivo de sensor ambiental.

O desenvolvedor do dispositivo usa o modelo para gerar o código do cliente do dispositivo e o construtor usa o modelo para [criar um modelo de dispositivo](./howto-set-up-template-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) no IOT central. O modelo atua como um contrato entre seu dispositivo e seu IoT Central aplicativo.

A seção neste guia que descreve como criar o código gerado pressupõe que você está usando o Windows.

Neste guia, ficará a saber como:

* Importar um modelo de capacidade de dispositivo para um modelo de dispositivo no IoT Central
* Adicionar um painel ao modelo que exibe a telemetria do dispositivo
* Adicionar um dispositivo real do modelo
* Importar um modelo de capacidade de dispositivo para o Visual Studio Code
* Gerar um aplicativo cliente do dispositivo C a partir do modelo
* Criar o aplicativo cliente do dispositivo C e conectá-lo ao IoT Central

## <a name="prerequisites"></a>Pré-requisitos

Para testar o código do dispositivo neste guia, você precisa de um aplicativo IoT Central criado a partir do modelo de aplicativo de **Visualização** . Se você ainda não tiver um aplicativo IoT Central para usar, conclua o início rápido [criar um aplicativo de IOT central do Azure (recursos de visualização)](./quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json):

Para trabalhar com o modelo de funcionalidade do dispositivo neste guia, você precisa:

* [Visual Studio Code](https://code.visualstudio.com/download): Visual Studio Code está disponível para várias plataformas
* Extensão do Azure IoT Device Workbench para Visual Studio Code. Use as etapas a seguir para instalar a extensão do Azure IoT Device Workbench no VS Code:

    1. Em VS Code, selecione a guia **extensões** .
    1. Pesquise o **Azure IOT Device Workbench**.
    1. Selecione **Instalar**.

    > [!NOTE]
    > A versão atual do gerador de código na extensão não dá suporte aos tipos de esquema geopoint e **vector** ou aos tipos de semântica **Acceleration**, **Velocity**e **Location** . Esses tipos de esquema e semânticos têm suporte pelo IoT Central.

    > [!NOTE]
    > Para trabalhar com IoT Central, o modelo de funcionalidade do dispositivo deve ter todas as interfaces definidas embutidas no mesmo arquivo.

Para criar o código C gerado no Windows neste guia, você precisa:

* [Visual Studio (Comunidade, profissional ou empresa)](https://visualstudio.microsoft.com/downloads/) – certifique-se de incluir o componente **Gerenciador de pacotes NuGet** e o **desenvolvimento de desktop C++ com** carga de trabalho ao instalar o Visual Studio.
* [Git](https://git-scm.com/download)
* [CMake](https://cmake.org/download/) – quando você instala o **CMake**, selecione a opção **Adicionar CMake ao caminho do sistema**.
* Uma cópia local do SDK do Azure IoT C:

    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive -b public-preview
    ```

Para seguir as instruções do dispositivo-primeiro no final deste artigo, você também precisa instalar:

* [Node.js](https://nodejs.org)
* A [ferramenta DPS-keygen](https://www.npmjs.com/package/dps-keygen):

    ```cmd/sh
    npm i -g dps-keygen
    ```

## <a name="create-device-template"></a>Criar modelo de dispositivo

Crie a pasta chamada **pnp_app** na pasta **Azure-IOT-SDK-c** que contém o SDK do Azure IOT c clonado. Baixe o modelo de capacidade de dispositivo [EnvironmentalSensor. capabilitymodel. JSON](https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensorInline.capabilitymodel.json) do GitHub e salve o arquivo na pasta **pnp_app** . Substitua as duas instâncias de `<YOUR_COMPANY_NAME_HERE>` pelo seu nome. Use somente os caracteres a-z, A-Z, 0-9 e sublinhado. Anote o valor completo do `"@id"` campo que identifica exclusivamente o modelo de capacidade do dispositivo, você precisará dele mais tarde. Esse modelo inclui duas interfaces:

* A interface comum **DeviceManagement** .
* a interface personalizada **EnvironmentalSensor** .

Para criar um modelo de dispositivo para o dispositivo sensor ambiental no IoT Central:

1. Vá para a página **modelos de dispositivo** e selecione **+ novo**. Em seguida, escolha **personalizado**.

1. Insira o **sensor ambiental** como o nome do modelo de dispositivo.

1. Escolha **Importar modelo de funcionalidade**. Em seguida, localize o **EnvironmentalSensor. capabilitymodel. JSON** que você criou e selecione **abrir**. As duas interfaces, **as informações do dispositivo** e o **sensor ambiental**são exibidas no **modelo de capacidade do sensor ambiental**.

1. Selecione **exibições** e, em seguida, **visualizando o dispositivo**.

1. Na lista de campos que você pode adicionar ao painel, selecione os dois valores de telemetria, **umidade** e **temperatura** e selecione **combinar**. Em seguida, escolha **Guardar**.

Agora você tem um modelo de dispositivo que usa o modelo de **sensor ambiental** e que tem um painel simples para exibir a telemetria de um dispositivo.

## <a name="publish-the-template-and-add-a-real-device"></a>Publicar o modelo e adicionar um dispositivo real

Para publicar o modelo e adicionar um dispositivo real, vá para a página **modelos de dispositivo** e selecione o modelo de dispositivo sensor de **ambiente** . Selecione **publicar**, examine as informações e selecione **publicar**.

Antes de conectar um aplicativo cliente, você precisa adicionar um dispositivo na página **dispositivos** e obter suas informações de conexão:

1. Vá para a página **dispositivos** e selecione **sensor ambiental**. A página **dispositivos** permite que você gerencie os dispositivos que podem se conectar ao seu aplicativo.

1. Para adicionar um dispositivo real, escolha **+ novo**, altere a ID do dispositivo para **Sensor01**e selecione **criar**. Verifique se **Simulated** está **desativado**.

1. Na lista de dispositivos, selecione o dispositivo do **sensor de ambiente** . Em seguida, selecione **conectar**.

1. Anote a **ID do escopo**, a **ID do dispositivo**e a **chave primária**. Em seguida, selecione **fechar**.

## <a name="generate-a-device-client-application"></a>Gerar um aplicativo cliente do dispositivo

Você usa Visual Studio Code para gerar um aplicativo cliente de dispositivo de esqueleto do modelo de funcionalidade do dispositivo:

1. Inicie o Visual Studio Code e abra a pasta **pnp_app** .

1. Use **Ctrl + Shift + P** para abrir a paleta de comandos.

1. Insira **plug and Play** e, em seguida, selecione **gerar stub de código de dispositivo**.

1. Selecione o arquivo de modelo de capacidade de dispositivo **EnvironmentalSensor. capabilitymodel. JSON** .

1. Insira **sensor_app** como o nome do projeto.

1. Escolha **ANSI C** como o idioma.

1. Escolha **projeto CMake** como o destino.

1. Escolha **por meio da chave simétrica do DPS (serviço de provisionamento de dispositivos)** como o método de conexão.

Visual Studio Code abre uma nova janela com o código C gerado na pasta **sensor_app**

## <a name="add-connection-details-to-the-device-client"></a>Adicionar detalhes de conexão ao cliente do dispositivo

Para adicionar as informações de conexão ao cliente do dispositivo, abra **Main. c** na pasta que contém o código gerado:

1. Substitua `[DPS Id Scope]` pelo valor da **ID de escopo** anotado anteriormente.

1. Substitua `[DPS symmetric key]` pelo valor de **chave primária** anotado anteriormente.

1. Substitua `[device registration Id]` pelo valor da **ID do dispositivo** anotado anteriormente.

1. Guarde as alterações.

## <a name="build-and-run-the-client"></a>Compilar e executar o cliente

Para compilar e executar o cliente, você precisa personalizar a compilação para o SDK do Azure IoT C:

1. Abra o arquivo **CMakeLists. txt** na raiz da pasta **Azure-IOT-SDK-c** .

1. Adicione a seguinte linha ao final deste arquivo para incluir o novo aplicativo cliente na compilação:

    ```txt
    add_subdirectory(pnp_app/sensor_app)
    ```

1. Guarde as alterações.

1. Abra um prompt de comando e navegue até a pasta **Azure-IOT-SDK-c** .

1. Para compilar o aplicativo, execute os seguintes comandos:

    ```cmd
    mkdir cmake
    cd cmake
    cmake .. -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -Dskip_samples:BOOL=ON
    cmake --build . -- /m /p:Configuration=Release
    ```

1. Para executar o aplicativo, execute o seguinte comando no mesmo prompt de comando:

    ```cmd
    pnp_app\sensor_app\Release\sensor_app.exe
    ```

## <a name="view-the-telemetry"></a>Exibir a telemetria

Após alguns minutos, você poderá ver a telemetria do dispositivo no painel do dispositivo em seu aplicativo IoT Central.

## <a name="connect-device-first"></a>Conectar dispositivo-primeiro

Você pode conectar um dispositivo IoT Plug and Play por meio de uma conexão do dispositivo-primeiro, conforme descrito em [conectividade](concepts-connectivity-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json). O processo de descoberta segue esta ordem:

1. Associar com o modelo de dispositivo se ele já estiver publicado no aplicativo IoT Central.

1. Busca o modelo de capacidade do [repositório público](https://aka.ms/ACFI) de modelos de recursos publicados e certificados.

Usando Visual Studio Code e o comando **gerar stub de código de dispositivo** mencionado acima, você pode seguir estas etapas para conectar o dispositivo primeiro e automaticamente colocar em seu modelo de funcionalidade de dispositivo publicado do repositório público em IOT central:

1. Use um modelo de funcionalidade de dispositivo existente que tenha sido publicado no repositório público. Você precisa do modelo de funcionalidade de dispositivo completo e anotar o URN desse modelo.

1. Siga as etapas acima para [gerar um aplicativo cliente de dispositivo](#generate-a-device-client-application) para usar Visual Studio Code e gerar o código do dispositivo.

1. Em seu aplicativo IoT Central, vá para a guia **Administração** e selecione a seção **conexão do dispositivo** . Anote os valores de **ID do escopo** e **chave primária** para seu aplicativo.

    > [!NOTE]
    > A **chave primária** mostrada nesta página é uma assinatura de acesso compartilhado de grupo que você pode usar para gerar várias chaves de dispositivo para seu aplicativo.

1. Use a ferramenta [DPS keygen](https://www.npmjs.com/package/dps-keygen) para gerar uma chave de dispositivo a partir da chave primária anotada anteriormente. Para gerar a chave do dispositivo, execute o seguinte comando:

    ```cmd/sh
    dps-keygen -mk:<Primary_Key> -di:<device_id>
    ```

1. Siga as etapas na seção anterior [adicionar detalhes de conexão ao cliente do dispositivo](#add-connection-details-to-the-device-client) para adicionar a **ID do escopo**, a **chave primária**e a **ID do dispositivo**.

1. Siga as etapas na seção anterior para [Compilar e executar o cliente](#build-and-run-the-client).

1. Agora você vê o dispositivo conectar-se ao seu aplicativo IoT Central e automaticamente colocar o modelo de capacidade do dispositivo do repositório público como um modelo de dispositivo.

## <a name="next-steps"></a>Passos Seguintes

Agora que você aprendeu como conectar um dispositivo real ao IoT Central, uma próxima etapa sugerida é saber mais sobre modelos de dispositivo em [configurar um modelo de dispositivo](howto-set-up-template-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
