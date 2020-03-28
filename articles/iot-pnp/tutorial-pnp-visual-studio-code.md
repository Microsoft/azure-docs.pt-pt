---
title: Criar e testar um dispositivo de pré-visualização IoT Plug e Play Microsoft Docs
description: Como desenvolvedor de dispositivos, aprenda a usar o Código VS para criar e testar um novo modelo de capacidade de dispositivo para um dispositivo IoT Plug e Play Preview.
author: dominicbetts
ms.author: dobett
ms.date: 12/30/2019
ms.topic: tutorial
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 720b3e56e1dd45bd2940b337adefa6ebdaa2e5a1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "76719726"
---
# <a name="tutorial-create-and-test-a-device-capability-model-using-visual-studio-code"></a>Tutorial: Criar e testar um modelo de capacidade de dispositivo usando o Código do Estúdio Visual

Este tutorial mostra-lhe como, como desenvolvedor de dispositivos, usar o Código do Estúdio Visual para criar um modelo de capacidade de _dispositivo_. Pode usar o modelo para gerar código de esqueleto para funcionar num dispositivo que se conecta a uma instância do Hub Azure IoT na nuvem.

A secção deste tutorial que descreve como construir o código de esqueleto gerado assume que está a usar o Windows.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um modelo de capacidade de dispositivo
> * Gerar código de dispositivo esqueleto a partir do modelo
> * Implementar os canhotos no código gerado
> * Executar o código para testar as interações com um hub IoT

## <a name="prerequisites"></a>Pré-requisitos

Para trabalhar com o modelo de capacidade do dispositivo neste tutorial, você precisa:

* [Código do Estúdio Visual](https://code.visualstudio.com/download): Código VS está disponível para várias plataformas
* [Ferramentas Azure IoT para](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) pacote de extensão vs código. Utilize os seguintes passos para instalar o pacote de extensão no Código VS:

    1. No Código VS, selecione o separador **Extensões.**
    1. Pesquisa de **ferramentas Azure IoT**.
    1. Selecione **Instalar**.

Para construir o código C gerado no Windows neste tutorial, você precisa:

* [Construa ferramentas para estúdio visual](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16) com **ferramentas de construção C++** e cargas de trabalho componentes de **gestor de pacotes NuGet.** Ou se já tem [o Visual Studio (Comunidade, Profissional ou Empresa)](https://visualstudio.microsoft.com/downloads/) 2019, 2017 ou 2015 com as mesmas cargas de trabalho instaladas.
* [Git](https://git-scm.com/download)
* [CMake](https://cmake.org/download/)

Para testar o código do seu dispositivo neste tutorial, precisa de:

* O [explorador Azure IoT.](https://github.com/Azure/azure-iot-explorer/releases)
* Uma subscrição do Azure. Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="model-your-device"></a>Modele o seu dispositivo

Usa a _linguagem de definição dupla digital_ para criar um modelo de capacidade de dispositivo. Um modelo consiste tipicamente em ficheiros de definição de _interface_ múltipla e um ficheiro de um único modelo. As **ferramentas Azure IoT para o Código VS** incluem ferramentas para o ajudar a criar e editar estes ficheiros JSON.

### <a name="create-the-interface-file"></a>Criar o ficheiro de interface

Para criar um ficheiro de interface que defina as capacidades do seu dispositivo IoT no Código VS:

1. Criar uma pasta chamada **modelo de dispositivos**.

1. Lance o Código VS e utilize **o Ctrl+Shift+P** para abrir a paleta de comando.

1. Introduza **plug and play** e, em seguida, selecione a ficha **IoT & Reproduzir: Criar** o comando interface.

1. Navegue e selecione a pasta de modelo de **dispositivoque** criou.

1. Em **seguida,** introduza o EnvironmentalSensor como o nome da interface e prima **Enter**. O Código VS cria um ficheiro de interface de amostra chamado **EnvironmentalSensor.interface.json**.

1. Substitua o conteúdo deste ficheiro pelo seguinte `{your name}` JSON e substitua no `@id` campo por um valor único. Use apenas os caracteres a-z, A-Z, 0-9, e sublinhe. Para mais informações, consulte o [formato de identificador Digital Twin.](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format) O ID da interface deve ser único para guardar a interface no repositório:

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

    Esta interface define propriedades do dispositivo como **Nome do Cliente,** tipos de telemetria como **temperatura,** e comandos como **o turnon**.

1. Adicione uma capacidade de comando chamada **piscar** no final deste ficheiro de interface. Certifique-se de adicionar uma vírina antes de adicionar o comando. Tente escrever a definição para ver como o intelecto, a autocompleta e a validação podem ajudá-lo a editar uma definição de interface:

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

### <a name="create-the-model-file"></a>Criar o ficheiro modelo

O ficheiro do modelo especifica as interfaces que o seu dispositivo IoT Plug e Play implementa. Existem normalmente pelo menos duas interfaces num modelo - uma ou mais que definem as capacidades específicas do seu dispositivo, e uma interface padrão que todos os dispositivos IoT Plug e Play devem implementar.

Para criar um ficheiro modelo que especifica as interfaces que o seu dispositivo IoT Plug e Play implementa no Código VS:

1. Utilize **ctrl+Shift+P** para abrir a paleta de comando.

1. Introduza **plug and play** e, em seguida, selecione a ficha **IoT & Reproduzir: Criar** o comando do Modelo de Capacidade. Em seguida, introduza **sensorboxModel** como o nome do modelo. O Código VS cria um ficheiro de interface de amostra chamado **SensorboxModel.capabilitymodel.json**.

1. Substitua o conteúdo deste ficheiro pelo seguinte `{your name}` JSON e substitua-o `@id` no campo e na `EnvironmentalSensor` interface pelo mesmo valor utilizado no ficheiro **EnvironmentalSensor.interface.json.** O ID da interface deve ser único para guardar a interface no repositório:

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

    O modelo define um dispositivo que implementa a interface **AmbientalSensor** e a interface padrão **de Informação do Dispositivo.**

1. Guarde o ficheiro.

### <a name="download-the-deviceinformation-interface"></a>Descarregue a interface DeInformação do Dispositivo

Antes de poder gerar código de esqueleto a partir do modelo, tem de criar uma cópia local da **Informação** do Dispositivo a partir do *repositório de modelos públicos*. O repositório de modelopúblico já contém a interface **DeviceInformation.**

Para descarregar a interface **DeInformação** do reporitório de modelos públicos utilizando o Código VS:

1. Utilize **ctrl+Shift+P** para abrir a paleta de comando.

1. Introduza **plug and play,** selecione o comando **repositório** de modelo aberto e, em seguida, selecione **Repositório de Modelo Público Aberto**.

1. Selecione **Interfaces,** depois selecione a interface de informação do dispositivo com ID, `urn:azureiot:DeviceManagement:DeviceInformation:1`e, em seguida, selecione **Download**.

Tem agora os três ficheiros que compõem o seu modelo de capacidade do dispositivo:

* urn_azureiot_DeviceManagement_DeviceInformation_1.interface.json
* EnvironmentalSensor.interface.json
* SensorboxModel.capabilitymodel.json

## <a name="publish-the-model"></a>Publicar o modelo

Para que a ferramenta de explorador Azure IoT leia o seu modelo de capacidade de dispositivo, precisa publicá-lo no repositório da sua empresa. Para publicar a partir do Código VS, precisa da cadeia de ligação para o repositório da empresa:

1. Navegue para o [Azure Certified para portal IoT](https://aka.ms/ACFI).

1. Utilize a sua conta de _trabalho_ da Microsoft para iniciar sessão no portal.

1. Selecione **repositório da empresa** e, em seguida, **ligações**de cordas .

1. Copie a corda de ligação.

Para abrir o seu repositório de empresa em Código VS:

1. Utilize **ctrl+Shift+P** para abrir a paleta de comando.

1. Introduza **plug and play** e, em seguida, selecione a ficha **IoT & Play: Comando de Repositório** de Modelo Aberto.

1. Selecione **Repositório de Modelos organizacionais abertos** e cola na sua cadeia de ligação.

1. Pressione **Enter** para abrir o seu repositório da empresa.

Para publicar o seu modelo de capacidade de dispositivo e interfaces no seu repositório da empresa:

1. Utilize **ctrl+Shift+P** para abrir a paleta de comando.

1. Introduza **plug and play** e, em seguida, selecione a ficha **IoT & Reproduzir: Enviar ficheiros para o comando de repositório modelo.**

1. Selecione os **ficheiros EnvironmentalSensor.interface.json** e **SensorboxModel.capabilitymodel.json** e selecione **OK**.

Os seus ficheiros estão agora guardados no seu repositório da empresa.

## <a name="generate-code"></a>Gerar código

Pode utilizar as **ferramentas Azure IoT para o Código VS** para gerar código C esqueleto a partir do seu modelo. Para gerar o código do esqueleto no Código VS:

1. Utilize **ctrl+Shift+P** para abrir a paleta de comando.

1. Introduza **a Ficha e a Reprodução** e, em seguida, selecione a ficha **IoT & reproduzir: Gere** o comando do Código do Dispositivo.

1. Selecione o ficheiro do modelo **sensorboxModel.capabilitymodel.json.**

1. Insira **sensorbox_app** como o nome do projeto.

1. Escolha **ansi C** como língua.

1. Escolha a cadeia de ligação do **dispositivo IoT Hub** como forma de se ligar.

1. Escolha **o CMake Project no Windows** como modelo de projeto.

1. Escolha **via Vcpkg** como forma de incluir o dispositivo SDK.

O Código VS gera o código C do esqueleto e guarda os ficheiros na pasta **sensorbox_app** na pasta de código de **modelo.** O Código VS abre uma nova janela que contém os ficheiros de código gerados.

## <a name="update-the-generated-code"></a>Atualizar o código gerado

Antes de poder construir e executar o código, precisa implementar as propriedades, telemetria e comandos.

Para fornecer implementações para o código destubbed no Código VS:

1. Abra o ficheiro **SensorboxModel_impl.c.**

1. Adicione código para implementar as funções de stubbed.

1. Guarde as alterações.

## <a name="build-the-code"></a>Compilar o código

Antes de executar o código para testar o seu dispositivo IoT Plug and Play com um hub Azure IoT, precisa de compilar o código.

Siga as instruções no ficheiro **Readme.md** na pasta **sensorbox_app** para construir e executar o código no Windows. A secção seguinte inclui instruções para recuperar uma cadeia de ligação do dispositivo a utilizar quando executar o código do dispositivo.

## <a name="test-the-code"></a>Testar o código

Quando executa o código, liga-se ao IoT Hub e começa a enviar valores de telemetria e propriedade. O dispositivo também responde aos comandos enviados do IoT Hub. Para verificar este comportamento:

1. Para criar um hub IoT:

    ```azurecli-interactive
    az group create --name environmentalsensorresources --location centralus
    az iot hub create --name {your iot hub name} \
      --resource-group environmentalsensorresources --sku F1
    ```

1. Adicione um dispositivo ao seu hub IoT e recupere a sua cadeia de ligação:

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {your iot hub name} --device-id MyPnPDevice
    az iot hub device-identity show-connection-string --hub-name {your iot hub name} --device-id MyPnPDevice --output table
    ```

    Tome nota da corda de ligação.

1. Num pedido de comando, navegue até à pasta **azure-iot-sdk-c** onde construiu o SDK e amostras. Em seguida, navegue para a **pasta\\de\\** lançamento sensorbox_app.

1. Execute o seguinte comando:

    ```cmd
    sensorbox_app.exe {your device connection string}
    ```

1. Utilize a ferramenta exploradora Azure IoT para interagir com o dispositivo IoT Plug and Play ligado ao seu hub IoT. Para mais informações, consulte Instalar e utilizar o [explorador Azure IoT](./howto-install-iot-explorer.md).

## <a name="next-steps"></a>Passos seguintes

Agora que construíste um IoT Plug e Play prontos para a certificação, aprende a:

> [!div class="nextstepaction"]
> [Criar um dispositivo pronto para certificação](tutorial-build-device-certification.md)
