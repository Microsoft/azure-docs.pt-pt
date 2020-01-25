---
title: Criar e testar um dispositivo de visualização de Plug and Play de IoT | Microsoft Docs
description: Como desenvolvedor de dispositivos, saiba como usar VS Code para criar e testar um novo modelo de capacidade de dispositivo para um dispositivo de visualização de IoT Plug and Play.
author: dominicbetts
ms.author: dobett
ms.date: 12/30/2019
ms.topic: tutorial
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 720b3e56e1dd45bd2940b337adefa6ebdaa2e5a1
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719726"
---
# <a name="tutorial-create-and-test-a-device-capability-model-using-visual-studio-code"></a>Tutorial: criar e testar um modelo de capacidade de dispositivo usando Visual Studio Code

Este tutorial mostra como, como desenvolvedor de dispositivos, usar Visual Studio Code para criar um modelo de _capacidade de dispositivo_. Você pode usar o modelo para gerar um código esqueleto para ser executado em um dispositivo que se conecta a uma instância do Hub IoT do Azure na nuvem.

A seção neste tutorial que descreve como criar o código esqueleto gerado pressupõe que você está usando o Windows.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um modelo de capacidade de dispositivo
> * Gerar o esqueleto do código de dispositivo do modelo
> * Implementar os stubs no código gerado
> * Executar o código para testar as interações com um hub IoT

## <a name="prerequisites"></a>Pré-requisitos

Para trabalhar com o modelo de funcionalidade do dispositivo neste tutorial, você precisa de:

* [Visual Studio Code](https://code.visualstudio.com/download): vs Code está disponível para várias plataformas
* [Ferramentas de IOT do Azure para vs Code pacote de](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) extensão. Use as etapas a seguir para instalar o pacote de extensão no VS Code:

    1. Em VS Code, selecione a guia **extensões** .
    1. Pesquise pelas **Ferramentas do Azure IOT**.
    1. Selecione **Instalar**.

Para criar o código C gerado no Windows neste tutorial, você precisará de:

* [Ferramentas de Build para Visual Studio](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16) com  **C++ ferramentas de compilação** e cargas de trabalho de **componente do Gerenciador de pacotes NuGet** . Ou, se você já tiver o [Visual Studio (Community, Professional ou Enterprise)](https://visualstudio.microsoft.com/downloads/) 2019, 2017 ou 2015 com as mesmas cargas de trabalho instaladas.
* [Git](https://git-scm.com/download)
* [CMake](https://cmake.org/download/)

Para testar o código do dispositivo neste tutorial, você precisará de:

* O [Azure IOT Explorer](https://github.com/Azure/azure-iot-explorer/releases).
* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="model-your-device"></a>Modelar seu dispositivo

Use a _linguagem de definição de entrelaçamento digital_ para criar um modelo de capacidade de dispositivo. Um modelo normalmente consiste em vários arquivos de definição de _interface_ e um único arquivo de modelo. As **Ferramentas do Azure IOT para vs Code** incluem ferramentas para ajudá-lo a criar e editar esses arquivos JSON.

### <a name="create-the-interface-file"></a>Criar o arquivo de interface

Para criar um arquivo de interface que define os recursos do seu dispositivo IoT no VS Code:

1. Crie uma pasta chamada **devicemodel**.

1. Inicie VS Code e use **Ctrl + Shift + P** para abrir a paleta de comandos.

1. Insira **plug and Play** e, em seguida, selecione o comando do **plug-in do IOT & Play: Create interface** .

1. Navegue até e selecione a pasta **devicemodel** que você criou.

1. Em seguida, insira **EnvironmentalSensor** como o nome da interface e pressione **Enter**. VS Code cria um arquivo de interface de exemplo chamado **EnvironmentalSensor. interface. JSON**.

1. Substitua o conteúdo deste arquivo pelo JSON a seguir e substitua `{your name}` no campo `@id` por um valor exclusivo. Use somente os caracteres a-z, A-Z, 0-9 e sublinhado. Para obter mais informações, consulte [formato do identificador de entrelaça digital](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format). A ID da interface deve ser exclusiva para salvar a interface no repositório:

    ```json
    {
      "@id": "urn:{your name}:EnvironmentalSensor:1",
      "@type": "Interface",
      "displayName": "Environmental Sensor",
      "description": "Provides functionality to report temperature, humidity. Provides telemetry, commands and read-write properties",
      "comment": "Requires temperature and humidity sensors.",
      "contents": [
        {
          "@type": "Property",
          "displayName": "Device State",
          "description": "The state of the device. Two states online/offline are available.",
          "name": "state",
          "schema": "boolean"
        },
        {
          "@type": "Property",
          "displayName": "Customer Name",
          "description": "The name of the customer currently operating the device.",
          "name": "name",
          "schema": "string",
          "writable": true
        },
        {
          "@type": "Property",
          "displayName": "Brightness Level",
          "description": "The brightness level for the light on the device. Can be specified as 1 (high), 2 (medium), 3 (low)",
          "name": "brightness",
          "writable": true,
          "schema": "long"
        },
        {
          "@type": [
            "Telemetry",
            "SemanticType/Temperature"
          ],
          "description": "Current temperature on the device",
          "displayName": "Temperature",
          "name": "temp",
          "schema": "double",
          "unit": "Units/Temperature/fahrenheit"
        },
        {
          "@type": [
            "Telemetry",
            "SemanticType/Humidity"
          ],
          "description": "Current humidity on the device",
          "displayName": "Humidity",
          "name": "humid",
          "schema": "double",
          "unit": "Units/Humidity/percent"
        },
        {
          "@type": "Telemetry",
          "name": "magnetometer",
          "displayName": "Magnetometer",
          "comment": "This shows a complex telemetry that contains a magnetometer reading.",
          "schema": {
            "@type": "Object",
            "fields": [
              {
                "name": "x",
                "schema": "integer"
              },
              {
                "name": "y",
                "schema": "integer"
              },
              {
                "name": "z",
                "schema": "integer"
              }
            ]
          }
        },
        {
          "@type": "Command",
          "name": "turnon",
          "response": {
            "name": "turnon",
            "schema": "string"
          },
          "comment": "This Commands will turn-on the LED light on the device.",
          "commandType": "synchronous"
        },
        {
          "@type": "Command",
          "name": "turnoff",
          "comment": "This Commands will turn-off the LED light on the device.",
          "response": {
            "name": "turnoff",
            "schema": "string"
          },
          "commandType": "synchronous"
        }
      ],
      "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
    }
    ```

    Essa interface define as propriedades do dispositivo, como **nome do cliente**, tipos de telemetria, como **temperatura**, e comandos como a **ativação**.

1. Adicione um recurso de comando chamado **piscar** no final deste arquivo de interface. Certifique-se de adicionar uma vírgula antes de adicionar o comando. Tente digitar a definição para ver como o IntelliSense, o preenchimento automático e a validação podem ajudá-lo a editar uma definição de interface:

    ```json
    {
      "@type": "Command",
      "description": "This command will begin blinking the LED for given time interval.",
      "name": "blink",
      "request": {
        "name": "blinkRequest",
        "schema": {
          "@type": "Object",
          "fields": [
            {
              "name": "interval",
              "schema": "long"
            }
          ]
        }
      },
      "response": {
        "name": "blinkResponse",
        "schema": "string"
      },
      "commandType": "synchronous"
    }
    ```

1. Guarde o ficheiro.

### <a name="create-the-model-file"></a>Criar o arquivo de modelo

O arquivo de modelo especifica as interfaces que seu dispositivo de Plug and Play de IoT implementa. Normalmente, há pelo menos duas interfaces em um modelo – uma ou mais que definem os recursos específicos do seu dispositivo e uma interface padrão que todos os dispositivos IoT Plug and Play devem implementar.

Para criar um arquivo de modelo que especifica as interfaces que o dispositivo de Plug and Play de IoT implementa no VS Code:

1. Use **Ctrl + Shift + P** para abrir a paleta de comandos.

1. Insira **plug and Play** e, em seguida, selecione o comando de **modelo de recurso do plug-in do IOT & Play: Create** . Em seguida, insira **SensorboxModel** como o nome do modelo. VS Code cria um arquivo de interface de exemplo chamado **SensorboxModel. capabilitymodel. JSON**.

1. Substitua o conteúdo deste arquivo pelo JSON a seguir e substitua `{your name}` no campo `@id` e na interface `EnvironmentalSensor` com o mesmo valor usado no arquivo **EnvironmentalSensor. interface. JSON** . A ID da interface deve ser exclusiva para salvar a interface no repositório:

    ```json
    {
      "@id": "urn:{your name}:SensorboxModel:1",
      "@type": "CapabilityModel",
      "displayName": "Environmental Sensorbox Model",
      "implements": [
        {
          "schema": "urn:{your name}:EnvironmentalSensor:1",
          "name": "environmentalSensor"
        },
        {
          "schema": "urn:azureiot:DeviceManagement:DeviceInformation:1",
          "name": "deviceinfo"
        }
      ],
      "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
    }
    ```

    O modelo define um dispositivo que implementa a interface **EnvironmentalSensor** e a interface **DeviceInformation** padrão.

1. Guarde o ficheiro.

### <a name="download-the-deviceinformation-interface"></a>Baixar a interface DeviceInformation

Antes de gerar o código esqueleto do modelo, você deve criar uma cópia local do **DeviceInformation** do *repositório de modelos públicos*. O repositório de modelos públicos já contém a interface **DeviceInformation** .

Para baixar a interface **DeviceInformation** do repositório de modelos públicos usando vs Code:

1. Use **Ctrl + Shift + P** para abrir a paleta de comandos.

1. Insira **plug and Play**, selecione o comando **abrir repositório de modelos** e, em seguida, selecione **abrir repositório de modelos públicos**.

1. Selecione **interfaces**e, em seguida, selecione a interface de informações do dispositivo com ID `urn:azureiot:DeviceManagement:DeviceInformation:1`e, em seguida, selecione **baixar**.

Agora você tem os três arquivos que compõem o modelo de capacidade do dispositivo:

* urn_azureiot_DeviceManagement_DeviceInformation_1.interface.json
* EnvironmentalSensor. interface. JSON
* SensorboxModel. capabilitymodel. JSON

## <a name="publish-the-model"></a>Publique o modelo

Para que a ferramenta do Azure IoT Explorer Leia o modelo de capacidade do dispositivo, você precisa publicá-lo no repositório da sua empresa. Para publicar de VS Code, você precisa da cadeia de conexão para o repositório da empresa:

1. Navegue até o [portal certificado pelo Azure para IOT](https://aka.ms/ACFI).

1. Use sua _conta corporativa_ da Microsoft para entrar no Portal.

1. Selecione **repositório da empresa** e **cadeias de conexão**.

1. Copie a cadeia de conexão.

Para abrir o repositório da sua empresa no VS Code:

1. Use **Ctrl + Shift + P** para abrir a paleta de comandos.

1. Insira **plug and Play** e, em seguida, selecione o comando **IOT plug & Play: abrir o repositório de modelos** .

1. Selecione **abrir repositório de modelo organizacional** e cole na cadeia de conexão.

1. Pressione **Enter** para abrir o repositório da sua empresa.

Para publicar o modelo de funcionalidade do dispositivo e as interfaces no repositório da sua empresa:

1. Use **Ctrl + Shift + P** para abrir a paleta de comandos.

1. Insira **plug and Play** e, em seguida, selecione o comando **plug & Play do IOT: enviar arquivos ao repositório de modelo** .

1. Selecione os arquivos **EnvironmentalSensor. interface. JSON** e **SensorboxModel. capabilitymodel. JSON** e selecione **OK**.

Os arquivos agora são armazenados no repositório da sua empresa.

## <a name="generate-code"></a>Gerar código

Você pode usar as **Ferramentas do Azure IOT para vs Code** para gerar o esqueleto do código C de seu modelo. Para gerar o código de esqueleto no VS Code:

1. Use **Ctrl + Shift + P** para abrir a paleta de comandos.

1. Insira **plug and Play** e, em seguida, selecione o comando de **stub de código do dispositivo IOT & Play: gerar**

1. Selecione o arquivo de modelo de funcionalidade **SensorboxModel. capabilitymodel. JSON** .

1. Insira **sensorbox_app** como o nome do projeto.

1. Escolha **ANSI C** como o idioma.

1. Escolha **por meio da cadeia de conexão do dispositivo do Hub IOT** como a maneira de se conectar.

1. Escolha **projeto CMake no Windows** como modelo de projeto.

1. Escolha **via Vcpkg** como uma maneira de incluir o SDK do dispositivo.

O Código VS gera o código C do esqueleto e guarda os ficheiros na pasta **sensorbox_app** na pasta de código de **modelo.** VS Code abre uma nova janela que contém os arquivos de código gerados.

## <a name="update-the-generated-code"></a>Atualizar o código gerado

Antes de criar e executar o código, você precisa implementar as propriedades, telemetria e comandos do fragmentado.

Para fornecer implementações para o código fragmentado no VS Code:

1. Abra o ficheiro **SensorboxModel_impl.c.**

1. Adicione o código para implementar as funções fragmentado.

1. Guarde as alterações.

## <a name="build-the-code"></a>Compilar o código

Antes de executar o código para testar seu dispositivo de Plug and Play de IoT com um hub IoT do Azure, você precisa compilar o código.

Siga as instruções no ficheiro **Readme.md** na pasta **sensorbox_app** para construir e executar o código no Windows. A seção a seguir inclui instruções para recuperar uma cadeia de conexão de dispositivo a ser usada quando você executar o código do dispositivo.

## <a name="test-the-code"></a>Testar o código

Quando você executa o código, ele se conecta ao Hub IoT e começa a enviar amostra de telemetria e valores de propriedade. O dispositivo também responde a comandos enviados do Hub IoT. Para verificar esse comportamento:

1. Para criar um hub IoT:

    ```azurecli-interactive
    az group create --name environmentalsensorresources --location centralus
    az iot hub create --name {your iot hub name} \
      --resource-group environmentalsensorresources --sku F1
    ```

1. Adicione um dispositivo ao Hub IoT e recupere sua cadeia de conexão:

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {your iot hub name} --device-id MyPnPDevice
    az iot hub device-identity show-connection-string --hub-name {your iot hub name} --device-id MyPnPDevice --output table
    ```

    Anote a cadeia de conexão.

1. Em um prompt de comando, navegue até a pasta **Azure-IOT-SDK-c** em que você criou o SDK e os exemplos. Em seguida, navegue para a **pasta\\sensorbox_app\\de lançamento** sensorbox_app.

1. Execute o seguinte comando:

    ```cmd
    sensorbox_app.exe {your device connection string}
    ```

1. Use a ferramenta do Azure IoT Explorer para interagir com o dispositivo de Plug and Play de IoT conectado ao Hub IoT. Para obter mais informações, consulte [instalar e usar o Azure IOT Explorer](./howto-install-iot-explorer.md).

## <a name="next-steps"></a>Passos seguintes

Agora que você criou um Plug and Play IoT pronto para certificação, saiba como:

> [!div class="nextstepaction"]
> [Criar um dispositivo pronto para certificação](tutorial-build-device-certification.md)
