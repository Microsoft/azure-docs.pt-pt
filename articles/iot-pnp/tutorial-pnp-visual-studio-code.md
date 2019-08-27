---
title: Criar e testar um dispositivo de visualização de Plug and Play de IoT | Microsoft Docs
description: Como desenvolvedor de dispositivos, saiba como usar VS Code para criar e testar um novo modelo de capacidade de dispositivo para um dispositivo de visualização de IoT Plug and Play.
author: dominicbetts
ms.author: dobett
ms.date: 07/10/2019
ms.topic: tutorial
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 10eb9655371177a52d1c1a5a9118665015076b35
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/27/2019
ms.locfileid: "70047994"
---
# <a name="tutorial-create-and-test-a-device-capability-model-using-visual-studio-code"></a>Tutorial: Criar e testar um modelo de capacidade de dispositivo usando Visual Studio Code

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

* [Visual Studio Code](https://code.visualstudio.com/download): VS Code está disponível para várias plataformas
* Extensão do Azure IoT Device Workbench no VS Code. Use as etapas a seguir para instalar a extensão do Azure IoT Device Workbench no VS Code:

    1. Em VS Code, selecione a guia **extensões** .
    1. Pesquise o **Azure IOT Device Workbench**.
    1. Selecione **Instalar**.

Para criar o código C gerado no Windows neste tutorial, você precisará de:

* [Visual Studio (Comunidade, profissional ou empresa)](https://visualstudio.microsoft.com/downloads/) – certifique-se de incluir o componente **Gerenciador de pacotes NuGet** e o **desenvolvimento de desktop C++ com** carga de trabalho ao instalar o Visual Studio.
* [Git](https://git-scm.com/download)
* [CMake](https://cmake.org/download/)
* Uma cópia local do SDK do Azure IoT C:

    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive -b public-preview
    ```

Para testar o código do dispositivo neste tutorial, você precisará de:

* O [Azure IOT Explorer](https://github.com/Azure/azure-iot-explorer/releases).
* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="model-your-device"></a>Modelar seu dispositivo

Use a _linguagem de definição de entrelaçamento digital_ para criar um modelo de capacidade de dispositivo. Um modelo normalmente consiste em vários arquivos de definição de _interface_ e um único arquivo de modelo. A **extensão do Azure IOT Device Workbench para vs Code** inclui ferramentas para ajudá-lo a criar e editar esses arquivos JSON.

### <a name="create-the-interface-file"></a>Criar o arquivo de interface

Para criar um arquivo de interface que define os recursos do seu dispositivo IoT no VS Code:

1. Crie uma pasta chamada **devicemodel**.

1. Inicie VS Code e use **Ctrl + Shift + P** para abrir a paleta de comandos.

1. Insira **plug and Play** e, em seguida **, selecione o plug-in do IOT & Play: Comando Create** interface.

1. Navegue até e selecione a pasta **devicemodel** que você criou.

1. Em seguida, insira **EnvironmentalSensor** como o nome da interface e pressione **Enter**. VS Code cria um arquivo de interface de exemplo chamado **EnvironmentalSensor. interface. JSON**.

1. Substitua o conteúdo deste arquivo pelo JSON a seguir e substitua `{your name}` `@id` no campo por um valor exclusivo. Use somente os caracteres a-z, A-Z, 0-9 e sublinhado. Para obter mais informações, consulte [formato do identificador de entrelaça digital](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format). A ID da interface deve ser exclusiva para salvar a interface no repositório:

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
      "@context": "http://azureiot.com/v1/contexts/Interface.json"
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

1. Insira **plug and Play** e, em seguida **, selecione o plug-in do IOT & Play: Criar comando de** modelo de funcionalidade. Em seguida, insira **SensorboxModel** como o nome do modelo. VS Code cria um arquivo de interface de exemplo chamado **SensorboxModel. capabilitymodel. JSON**.

1. Substitua o conteúdo desse arquivo pelo JSON a seguir `{your name}` e substitua `@id` -o pelo campo e na `EnvironmentalSensor` interface com o mesmo valor usado no arquivo **EnvironmentalSensor. interface. JSON** . A ID da interface deve ser exclusiva para salvar a interface no repositório:

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
      "@context": "http://azureiot.com/v1/contexts/CapabilityModel.json"
    }
    ```

    O modelo define um dispositivo que implementa a interface **EnvironmentalSensor** e a interface **DeviceInformation** padrão.

1. Guarde o ficheiro.

### <a name="download-the-deviceinformation-interface"></a>Baixar a interface DeviceInformation

Antes de gerar o código esqueleto do modelo, você deve criar uma cópia local do **DeviceInformation** do *repositório de modelos públicos*. O repositório de modelos públicos já contém a interface **DeviceInformation** .

Para baixar a interface **DeviceInformation** do repositório de modelos públicos usando vs Code:

1. Use **Ctrl + Shift + P** para abrir a paleta de comandos.

1. Insira **plug and Play**, selecione o comando **abrir repositório de modelos** e, em seguida, selecione **abrir repositório de modelos públicos**.

1. Selecione **interfaces**, selecione a interface de informações do dispositivo com `urn:azureiot:DeviceManagement:DeviceInformation:1`ID e, em seguida, selecione **baixar**.

Agora você tem os três arquivos que compõem o modelo de capacidade do dispositivo:

* urn_azureiot_DeviceManagement_DeviceInformation_1. interface. JSON
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

1. Insira **plug and Play** e, em seguida **, selecione o plug-in do IOT & Play: Abra o comando** de repositório de modelos.

1. Selecione **abrir repositório de modelo organizacional** e cole na cadeia de conexão.

1. Pressione **Enter** para abrir o repositório da sua empresa.

Para publicar o modelo de funcionalidade do dispositivo e as interfaces no repositório da sua empresa:

1. Use **Ctrl + Shift + P** para abrir a paleta de comandos.

1. Insira **plug and Play** e, em seguida **, selecione o plug-in do IOT & Play: Comando enviar arquivos para o** repositório de modelos.

1. Selecione os arquivos **EnvironmentalSensor. interface. JSON** e **SensorboxModel. capabilitymodel. JSON** e selecione **OK**.

Os arquivos agora são armazenados no repositório da sua empresa.

## <a name="generate-code"></a>Gerar código

Você pode usar a **extensão do Azure IOT Device Workbench para vs Code** para gerar o esqueleto do código C de seu modelo. Para gerar o código de esqueleto no VS Code:

1. Use **Ctrl + Shift + P** para abrir a paleta de comandos.

1. Insira **plug and Play** e, em seguida **, selecione o plug-in do IOT & Play: Gerar comando de stub** de código de dispositivo.

1. Selecione o arquivo de modelo de funcionalidade **SensorboxModel. capabilitymodel. JSON** .

1. Insira **sensorbox_app** como o nome do projeto.

1. Escolha **ANSI C** como o idioma.

1. Escolha **projeto CMake** como o destino.

1. Escolha **por meio da cadeia de conexão do dispositivo do Hub IOT** como a maneira de se conectar.

VS Code gera o código de esqueleto C e salva os arquivos na pasta **sensorbox_app** na pasta **modelcode** VS Code abre uma nova janela que contém os arquivos de código gerados.

## <a name="update-the-generated-code"></a>Atualizar o código gerado

Antes de criar e executar o código, você precisa implementar as propriedades, telemetria e comandos do fragmentado.

Para fornecer implementações para o código fragmentado no VS Code:

1. Abra o arquivo **SensorboxModel_impl. c** .

1. Adicione o código para implementar as funções fragmentado.

1. Guarde as alterações.

## <a name="build-the-code"></a>Compilar o código

Antes de executar o código para testar seu dispositivo de Plug and Play de IoT com um hub IoT do Azure, você precisa compilar o código.

Siga as instruções no arquivo **README.MD** na pasta **sensorbox_app** para compilar e executar o código no Windows. A seção a seguir inclui instruções para recuperar uma cadeia de conexão de dispositivo a ser usada quando você executar o código do dispositivo.

## <a name="test-the-code"></a>Testar o código

Quando você executa o código, ele se conecta ao Hub IoT e começa a enviar amostra de telemetria e valores de propriedade. O dispositivo também responde a comandos enviados do Hub IoT. Para verificar esse comportamento:

1. Para criar um hub IoT:

    ```azurecli-interactive
    az group create --name environmentalsensorresources --location eastus
    az iot hub create --name {your iot hub name} \
      --resource-group environmentalsensorresources --sku F1
    ```

1. Adicione um dispositivo ao Hub IoT e recupere sua cadeia de conexão:

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {your iot hub name} --device-id MyPnPDevice
    az iot hub device-identity show-connection-string --hub-name {your iot hub name} --device-id MyPnPDevice --output table
    ```

    Anote a cadeia de conexão.

1. Em um prompt de comando, navegue até a pasta **Azure-IOT-SDK-c** em que você criou o SDK e os exemplos. Em seguida, navegue até a pasta **\\CMake sensorbox_app\\Release** .

1. Execute o seguinte comando:

    ```cmd
    sensorbox_app.exe {your device connection string}
    ```

1. Use a ferramenta do Azure IoT Explorer para interagir com o dispositivo de Plug and Play de IoT conectado ao Hub IoT. Para obter mais informações, consulte [instalar e usar o Azure IOT Explorer](./howto-install-iot-explorer.md).

## <a name="next-steps"></a>Passos Seguintes

Agora que você criou um Plug and Play IoT pronto para certificação, saiba como:

> [!div class="nextstepaction"]
> [Criar um dispositivo pronto para certificação](tutorial-build-device-certification.md)
