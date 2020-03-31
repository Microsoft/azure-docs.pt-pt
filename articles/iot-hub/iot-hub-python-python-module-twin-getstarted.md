---
title: Identidade do módulo Azure IoT Hub e módulo twin (Python)
description: Aprenda a criar a identidade do módulo e a tualização do módulo twin usando SDKs IoT para Python.
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: menchi
ms.openlocfilehash: e18d448d9aee0137f1167d23a2bbf53486d0c764
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73953850"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-python"></a>Inicie com identidade do módulo IoT Hub e módulo twin (Python)

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> As [identidades de módulo e os módulos duplos](iot-hub-devguide-module-twins.md) assemelham-se à identidade do dispositivo e ao dispositivo duplo do Hub IoT do Azure, exceto no facto de oferecerem melhor granularidade. Enquanto a identidade do dispositivo e o dispositivo duplo do Hub IoT do Azure permitem que a aplicação de back-end configure um dispositivo e conferem visibilidade às condições do dispositivo, uma identidade de módulo e o módulo duplo fornecem estas capacidades para componentes individuais de um dispositivo. Em dispositivos compatíveis com vários componentes, tais como dispositivos baseados no sistema operativo ou dispositivos de firmware, permitem a configuração e condições isoladas para cada componente.
>

No final deste tutorial, você tem duas aplicações Python:

* **CreateIdentities**, que cria uma identidade de dispositivo, uma identidade de módulo e a chave de segurança associada para ligar os clientes do dispositivo e do módulo.

* **UpdateModuleTwinReportedProperties**, que envia as propriedades reportadas do módulo duplo atualizadas para o Hub IoT.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>Obtenha a cadeia de ligação do hub IoT

[!INCLUDE [iot-hub-howto-module-twin-shared-access-policy-text](../../includes/iot-hub-howto-module-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="create-a-device-identity-and-a-module-identity-in-iot-hub"></a>Criar uma identidade de dispositivo e uma identidade de módulo no IoT Hub

Nesta secção, cria-se uma aplicação Python que cria uma identidade de dispositivo e uma identidade de módulo no registo de identidade no seu hub IoT. Não é possível ligar um dispositivo ou módulo ao hub IoT, exceto se tiver uma entrada no registo de identidade. Para mais informações, consulte a secção "Registo de Identidade" do guia de [desenvolvimento do IoT Hub](iot-hub-devguide-identity-registry.md). Ao executar esta aplicação de consola, será gerado um ID e uma chave exclusivos para o dispositivo e o módulo. O dispositivo e o módulo utilizam estes valores para se identificarem quando enviam mensagens do dispositivo para cloud para o Hub IoT. Os IDs são sensíveis às maiúsculas e minúsculas.

Adicione o seguinte código ao seu ficheiro Python:

```python
import sys
import iothub_service_client
from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod, IoTHubError

CONNECTION_STRING = "YourConnString"
DEVICE_ID = "myFirstDevice"
MODULE_ID = "myFirstModule"

try:
    # RegistryManager
    iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

    # CreateDevice
    primary_key = ""
    secondary_key = ""
    auth_method = IoTHubRegistryManagerAuthMethod.SHARED_PRIVATE_KEY
    new_device = iothub_registry_manager.create_device(
        DEVICE_ID, primary_key, secondary_key, auth_method)
    print("new_device <" + DEVICE_ID +
          "> has primary key = " + new_device.primaryKey)

    # CreateModule
    new_module = iothub_registry_manager.create_module(
        DEVICE_ID, primary_key, secondary_key, MODULE_ID, auth_method)
    print("device/new_module <" + DEVICE_ID + "/" + MODULE_ID +
          "> has primary key = " + new_module.primaryKey)

except IoTHubError as iothub_error:
    print("Unexpected error {0}".format(iothub_error))
except KeyboardInterrupt:
    print("IoTHubRegistryManager sample stopped")
```

Esta aplicação cria uma identidade de dispositivo com ID **myFirstDevice** e uma identidade de módulo com ID **myFirstModule** no dispositivo **myFirstDevice**. (Se esse id do módulo já existir no registo de identidade, o código simplesmente recupera as informações do módulo existentes.) A aplicação exibe então a chave principal para essa identidade. Esta chave vai ser utilizada na aplicação do módulo simulado para ligar ao seu hub IoT.

> [!NOTE]
> O registo de identidade do Hub IoT apenas armazena identidades de dispositivos e módulos para permitir um acesso seguro ao hub IoT. O registo de identidades armazena os IDs de dispositivo e as chaves para utilizar como credenciais de segurança. O registo de identidades também armazena um sinalizador ativado/desativado para cada dispositivo que pode utilizar para desativar o acesso a esse dispositivo. Se a sua aplicação tiver de armazenar outros metadados específicos do dispositivo, deverá utilizar um armazenamento específico da aplicação.  Não existe nenhum sinalizador ativado/desativado para identidades de módulo. Para obter mais informações, veja o [IoT Hub developer guide (Guia do programador do Hub IoT)](iot-hub-devguide-identity-registry.md).
>

## <a name="update-the-module-twin-using-python-device-sdk"></a>Atualize o módulo twin usando o dispositivo Python SDK

Nesta secção, cria-se uma aplicação Python no seu dispositivo simulado que atualiza as propriedades reportadas pelo módulo Twin.

1. **Obtenha a corda de ligação do módulo-** agora se assinar no [portal Azure](https://portal.azure.com/). Navegue até ao seu Hub IoT e clique em Dispositivos IoT. Encontre o meu FirstDevice, abra-o e veja que o myFirstModule foi criado com sucesso. Copie a cadeia de ligação do módulo. É necessária para o próximo passo.

   ![Detalhe do módulo no portal do Azure](./media/iot-hub-python-python-module-twin-getstarted/module-detail.png)

2. **Criar app UpdateModuleTwinReportedProperties**

   Adicione as `using` seguintes declarações na parte superior do ficheiro **Program.cs:**

    ```python
    import sys
    import iothub_service_client
    from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod, IoTHubDeviceTwin, IoTHubError

    CONNECTION_STRING = "FILL IN CONNECTION STRING"
    DEVICE_ID = "MyFirstDevice"
    MODULE_ID = "MyFirstModule"

    UPDATE_JSON = "{\"properties\":{\"desired\":{\"telemetryInterval\":122}}}"

    try:
        iothub_twin = IoTHubDeviceTwin(CONNECTION_STRING)

        twin_info = iothub_twin.get_twin(DEVICE_ID, MODULE_ID)
        print ( "" )
        print ( "Twin before update    :" )
        print ( "{0}".format(twin_info) )

        twin_info_updated = iothub_twin.update_twin(DEVICE_ID, MODULE_ID, UPDATE_JSON)
        print ( "" )
        print ( "Twin after update     :" )
        print ( "{0}".format(twin_info_updated) )

    except IoTHubError as iothub_error:
        print ( "Unexpected error {0}".format(iothub_error) )
    except KeyboardInterrupt:
        print ( "IoTHubRegistryManager sample stopped" )
    ```

Este código de exemplo mostra como obter o módulo duplo e atualizar as propriedades reportadas com o protocolo AMQP.

## <a name="get-updates-on-the-device-side"></a>Obtenha atualizações sobre o lado do dispositivo

Além do código acima, pode adicionar abaixo o bloco de código para obter a mensagem de atualização gémea no seu dispositivo.

```python
import time
import threading
from azure.iot.device import IoTHubModuleClient

CONNECTION_STRING = "{deviceConnectionString}"


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

## <a name="next-steps"></a>Passos seguintes

Para continuar a introdução ao Hub IoT e explorar outros cenários de IoT, veja:

* [Getting started with device management (Introdução à gestão de dispositivos)](iot-hub-node-node-device-management-get-started.md)

* [Introdução ao IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)