---
title: Identidade do módulo Azure IoT Hub e módulo twin (Python)
description: Aprenda a criar a identidade do módulo e atualizar o módulo twin utilizando SDKs IoT para Python.
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: menchi
ms.custom: devx-track-python
ms.openlocfilehash: 665281adc892e6b3655c0b1d0533cb3148e62940
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92139399"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-python"></a>Começa com a identidade do módulo IoT Hub e módulo twin (Python)

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [As identidades dos módulos e os gémeos módulos](iot-hub-devguide-module-twins.md) são semelhantes às identidades do dispositivo Azure IoT Hub e aos gémeos do dispositivo, mas fornecem uma granularidade mais fina. Enquanto as identidades do dispositivo Azure IoT Hub e os gémeos do dispositivo permitem uma aplicação traseira para configurar um dispositivo e fornecer visibilidade sobre as condições do dispositivo, as identidades dos módulos e os gémeos do módulo fornecem estas capacidades para componentes individuais de um dispositivo. Em dispositivos capazes com múltiplos componentes, como dispositivos baseados no sistema operativo ou dispositivos de firmware, permitem uma configuração isolada e condições para cada componente.
>

No final deste tutorial, você tem três aplicativos Python:

* **CreateModule,** que cria uma identidade do dispositivo, uma identidade de módulo e chaves de segurança associadas para ligar os clientes do seu dispositivo e módulos.

* **UpdateModuleTwinDesiredProperties**, que envia propriedades atualizadas do módulo twin desejadas para o seu IoT Hub.

* **ReceberModuleTwinDesiredPropertiesPatch,** que recebe o patch de propriedades duplas do módulo no seu dispositivo.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>Obtenha a cadeia de conexão do hub IoT

Neste artigo, cria-se um serviço back-end que adiciona um dispositivo no registo de identidade e, em seguida, adiciona um módulo a esse dispositivo. Este serviço requer a permissão **de escrita de registo** (que também inclui leitura de **registo).** Também cria um serviço que adiciona propriedades desejadas ao módulo twin para o módulo recém-criado. Este serviço necessita da permissão **de ligação de serviço.** Embora existam políticas de acesso partilhado por defeito que concedem estas permissões individualmente, nesta secção, cria uma política de acesso partilhado personalizado que contém ambas as permissões.

[!INCLUDE [iot-hub-include-find-service-regrw-connection-string](../../includes/iot-hub-include-find-service-regrw-connection-string.md)]

## <a name="create-a-device-identity-and-a-module-identity-in-iot-hub"></a>Crie uma identidade de dispositivo e uma identidade de módulo no IoT Hub

Nesta secção, cria-se uma aplicação de serviço Python que cria uma identidade de dispositivo e uma identidade de módulo no registo de identidade no seu hub IoT. Um dispositivo ou módulo não pode ligar-se ao hub IoT a menos que tenha uma entrada no registo de identidade. Para mais informações, consulte [o registo de identidade no seu hub IoT](iot-hub-devguide-identity-registry.md). Ao executar esta aplicação de consola, será gerado um ID e uma chave exclusivos para o dispositivo e o módulo. O dispositivo e o módulo utilizam estes valores para se identificarem quando enviam mensagens do dispositivo para cloud para o Hub IoT. Os IDs são sensíveis às maiúsculas e minúsculas.

1. No seu comando, executar o seguinte comando para instalar o pacote **azure-iot-hub:**

    ```cmd/sh
    pip install azure-iot-hub
    ```

1. No seu comando, executar o seguinte comando para instalar o pacote **de enso:** Precisa deste pacote para apanhar as **exceções HTTPOperationError.**

    ```cmd/sh
    pip install msrest
    ```

1. Utilizando um editor de texto, crie um ficheiro chamado **CreateModule.py** no seu diretório de trabalho.

1. Adicione o seguinte código ao seu ficheiro Python. Substitua *o SeuIoTHubConnectionString* com a cadeia de ligação copiada na [Obter a cadeia de ligação do hub IoT](#get-the-iot-hub-connection-string).

    ```python
    import sys
    from msrest.exceptions import HttpOperationError
    from azure.iot.hub import IoTHubRegistryManager

    CONNECTION_STRING = "YourIotHubConnectionString"
    DEVICE_ID = "myFirstDevice"
    MODULE_ID = "myFirstModule"

    try:
        # RegistryManager
        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

        try:
            # CreateDevice - let IoT Hub assign keys
            primary_key = ""
            secondary_key = ""
            device_state = "enabled"
            new_device = iothub_registry_manager.create_device_with_sas(
                DEVICE_ID, primary_key, secondary_key, device_state
            )
        except HttpOperationError as ex:
            if ex.response.status_code == 409:
                # 409 indicates a conflict. This happens because the device already exists.
                new_device = iothub_registry_manager.get_device(DEVICE_ID)
            else:
                raise

        print("device <" + DEVICE_ID +
              "> has primary key = " + new_device.authentication.symmetric_key.primary_key)

        try:
            # CreateModule - let IoT Hub assign keys
            primary_key = ""
            secondary_key = ""
            managed_by = ""
            new_module = iothub_registry_manager.create_module_with_sas(
                DEVICE_ID, MODULE_ID, managed_by, primary_key, secondary_key
            )
        except HttpOperationError as ex:
            if ex.response.status_code == 409:
                # 409 indicates a conflict. This happens because the module already exists.
                new_module = iothub_registry_manager.get_module(DEVICE_ID, MODULE_ID)
            else:
                raise

        print("device/module <" + DEVICE_ID + "/" + MODULE_ID +
              "> has primary key = " + new_module.authentication.symmetric_key.primary_key)

    except Exception as ex:
        print("Unexpected error {0}".format(ex))
    except KeyboardInterrupt:
        print("IoTHubRegistryManager sample stopped")
    ```

1. No seu comando, executar o seguinte comando:

    ```cmd/sh
    python CreateModule.py
    ```

Esta aplicação cria uma identidade do dispositivo com ID **myFirstDevice** e uma identidade de módulo com ID **myFirstModule** sob o dispositivo **myFirstDevice**. (Se o dispositivo ou o ID do módulo já existirem no registo de identidade, o código simplesmente recupera as informações existentes do dispositivo ou módulo.) A aplicação exibe o ID e a chave primária para cada identidade.

> [!NOTE]
> O registo de identidade do Hub IoT apenas armazena identidades de dispositivos e módulos para permitir um acesso seguro ao hub IoT. O registo de identidades armazena os IDs de dispositivo e as chaves para utilizar como credenciais de segurança. O registo de identidades também armazena um sinalizador ativado/desativado para cada dispositivo que pode utilizar para desativar o acesso a esse dispositivo. Se a sua aplicação tiver de armazenar outros metadados específicos do dispositivo, deverá utilizar um armazenamento específico da aplicação.  Não existe nenhum sinalizador ativado/desativado para identidades de módulo. Para mais informações, consulte [o registo de identidade no seu hub IoT](iot-hub-devguide-identity-registry.md).
>

## <a name="update-the-module-twin-using-python-service-sdk"></a>Atualize o módulo twin usando o serviço Python SDK

Nesta secção, cria-se uma aplicação de serviço Python que atualiza as propriedades desejadas pelo módulo Twin.

1. No seu comando, executar o seguinte comando para instalar o pacote **azure-iot-hub.** Pode saltar este passo se instalar o pacote **azure-iot-hub** na secção anterior.

    ```cmd/sh
    pip install azure-iot-hub
    ```

1. Utilizando um editor de texto, crie um ficheiro chamado **UpdateModuleTwinDesiredProperties.py** no seu diretório de trabalho.

1. Adicione o seguinte código ao seu ficheiro Python. Substitua *o SeuIoTHubConnectionString* com a cadeia de ligação copiada na [Obter a cadeia de ligação do hub IoT](#get-the-iot-hub-connection-string).

    ```python
    import sys
    from azure.iot.hub import IoTHubRegistryManager
    from azure.iot.hub.models import Twin, TwinProperties

    CONNECTION_STRING = "YourIoTHubConnectionString"
    DEVICE_ID = "myFirstDevice"
    MODULE_ID = "myFirstModule"

    try:
        # RegistryManager
        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

        module_twin = iothub_registry_manager.get_module_twin(DEVICE_ID, MODULE_ID)
        print ( "" )
        print ( "Module twin properties before update    :" )
        print ( "{0}".format(module_twin.properties) )

        # Update twin
        twin_patch = Twin()
        twin_patch.properties = TwinProperties(desired={"telemetryInterval": 122})
        updated_module_twin = iothub_registry_manager.update_module_twin(
            DEVICE_ID, MODULE_ID, twin_patch, module_twin.etag
        )
        print ( "" )
        print ( "Module twin properties after update     :" )
        print ( "{0}".format(updated_module_twin.properties) )

    except Exception as ex:
        print ( "Unexpected error {0}".format(ex) )
    except KeyboardInterrupt:
        print ( "IoTHubRegistryManager sample stopped" )
    ```

## <a name="get-updates-on-the-device-side"></a>Obtenha atualizações do lado do dispositivo

Nesta secção, cria-se uma aplicação Python para obter a atualização de propriedades desejadas pelo módulo twin no seu dispositivo.

1. Obtenha a sua cadeia de ligação do módulo. No [portal Azure,](https://portal.azure.com/)navegue para o seu IoT Hub e selecione **dispositivos IoT** no painel esquerdo. Selecione **myFirstDevice** na lista de dispositivos e abra-o. Sob **as identidades do Módulo,** selecione **myFirstModule**. Copie a cadeia de ligação do módulo. Precisa de um passo seguinte.

   ![Detalhe do módulo no portal do Azure](./media/iot-hub-python-python-module-twin-getstarted/module-detail.png)

1. No seu comando, executar o seguinte comando para instalar o pacote de **dispositivo azure-iot:**

    ```cmd/sh
    pip install azure-iot-device
    ```

1. Utilizando um editor de texto, crie um ficheiro chamado **ReceiveModuleTwinDesiredPropertiesPatch.py** no seu diretório de trabalho.

1. Adicione o seguinte código ao seu ficheiro Python. Substitua *o Seu Abre-chaves de LigaçãoModule* com a cadeia de ligação do módulo copiada no passo 1.

    ```python
    import time
    import threading
    from azure.iot.device import IoTHubModuleClient

    CONNECTION_STRING = "YourModuleConnectionString"


    def twin_update_listener(client):
        while True:
            patch = client.receive_twin_desired_properties_patch()  # blocking call
            print("")
            print("Twin desired properties patch received:")
            print(patch)

    def iothub_client_sample_run():
        try:
            module_client = IoTHubModuleClient.create_from_connection_string(CONNECTION_STRING)

            twin_update_listener_thread = threading.Thread(target=twin_update_listener, args=(module_client,))
            twin_update_listener_thread.daemon = True
            twin_update_listener_thread.start()

            while True:
                time.sleep(1000000)

        except KeyboardInterrupt:
            print("IoTHubModuleClient sample stopped")


    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "IoTHubModuleClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

## <a name="run-the-apps"></a>Executar as aplicações

Nesta secção, você executou a aplicação do dispositivo **ReceiveModuleTwinDesiredPropertiesPatch** e, em seguida, executou a aplicação de serviço **UpdateModuleTwinDesiredProperties** para atualizar as propriedades desejadas do seu módulo.

1. Abra uma solicitação de comando e execute a aplicação do dispositivo:

    ```cmd/sh
    python ReceiveModuleTwinDesiredPropertiesPatch.py
    ```

   ![Saída inicial da aplicação do dispositivo](./media/iot-hub-python-python-module-twin-getstarted/device-1.png)

1. Abra uma solicitação de comando separada e execute a aplicação de serviço:

    ```cmd/sh
    python UpdateModuleTwinDesiredProperties.py
    ```

    Note que a propriedade desejada **telemetriaInterval** aparece no módulo twin atualizado na saída da sua aplicação de serviço:

   ![Saída de aplicativo de serviço](./media/iot-hub-python-python-module-twin-getstarted/service.png)

    A mesma propriedade aparece no patch de propriedades pretendido recebido na saída da aplicação do seu dispositivo:

   ![Saída da aplicação do dispositivo mostra patch de propriedades desejadas](./media/iot-hub-python-python-module-twin-getstarted/device-2.png)

## <a name="next-steps"></a>Passos seguintes

Para continuar a introdução ao Hub IoT e explorar outros cenários de IoT, veja:

* [Getting started with device management (Introdução à gestão de dispositivos)](iot-hub-node-node-device-management-get-started.md)

* [Introdução ao IoT Edge](../iot-edge/quickstart-linux.md)