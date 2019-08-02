---
title: Introdução à identidade do módulo do Hub IoT do Azure e ao módulo (Python) | Microsoft Docs
description: Saiba como criar a identidade do módulo e atualizar o módulo/atualização usando SDKs de IoT para Python.
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: menchi
ms.openlocfilehash: 2c388ff86e782c916916bfb08c7a55ec5c845b13
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68667918"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-python"></a>Introdução à identidade e módulo do módulo do Hub IoT (Python)

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> As [identidades de módulo e os módulos duplos](iot-hub-devguide-module-twins.md) assemelham-se à identidade do dispositivo e ao dispositivo duplo do Hub IoT do Azure, exceto no facto de oferecerem melhor granularidade. Enquanto a identidade do dispositivo e o dispositivo duplo do Hub IoT do Azure permitem que a aplicação de back-end configure um dispositivo e conferem visibilidade às condições do dispositivo, uma identidade de módulo e o módulo duplo fornecem estas capacidades para componentes individuais de um dispositivo. Em dispositivos compatíveis com vários componentes, tais como dispositivos baseados no sistema operativo ou dispositivos de firmware, permitem a configuração e condições isoladas para cada componente.
>

No final deste tutorial, você tem dois aplicativos Python:

* **CreateIdentities**, que cria uma identidade de dispositivo, uma identidade de módulo e a chave de segurança associada para ligar os clientes do dispositivo e do módulo.

* **UpdateModuleTwinReportedProperties**, que envia as propriedades reportadas do módulo duplo atualizadas para o Hub IoT.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

Veja a seguir as instruções de instalação para os pré-requisitos.

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>Obter a cadeia de conexão do Hub IoT

[!INCLUDE [iot-hub-howto-module-twin-shared-access-policy-text](../../includes/iot-hub-howto-module-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="create-a-device-identity-and-a-module-identity-in-iot-hub"></a>Criar uma identidade de dispositivo e uma identidade de módulo no Hub IoT

Nesta seção, você cria um aplicativo Python que cria uma identidade de dispositivo e uma identidade de módulo no registro de identidade em seu hub IoT. Não é possível ligar um dispositivo ou módulo ao hub IoT, exceto se tiver uma entrada no registo de identidade. Para obter mais informações, consulte a seção "registro de identidade" do [Guia do desenvolvedor do Hub IOT](iot-hub-devguide-identity-registry.md). Ao executar esta aplicação de consola, será gerado um ID e uma chave exclusivos para o dispositivo e o módulo. O dispositivo e o módulo utilizam estes valores para se identificarem quando enviam mensagens do dispositivo para cloud para o Hub IoT. Os IDs são sensíveis às maiúsculas e minúsculas.

Adicione o seguinte código ao seu arquivo Python:

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

Este aplicativo cria uma identidade de dispositivo com a ID **myFirstDevice** e uma identidade de módulo com ID **MyFirstModule** em dispositivo **myFirstDevice**. (Se o ID desse módulo já existir no registo de identidade, o código apenas obtém as informações do módulo existente.) De seguida, a aplicação irá apresentar a chave primária para essa identidade. Esta chave vai ser utilizada na aplicação do módulo simulado para ligar ao seu hub IoT.

> [!NOTE]
> O registo de identidade do Hub IoT apenas armazena identidades de dispositivos e módulos para permitir um acesso seguro ao hub IoT. O registo de identidades armazena os IDs de dispositivo e as chaves para utilizar como credenciais de segurança. O registo de identidades também armazena um sinalizador ativado/desativado para cada dispositivo que pode utilizar para desativar o acesso a esse dispositivo. Se a sua aplicação tiver de armazenar outros metadados específicos do dispositivo, deverá utilizar um armazenamento específico da aplicação. Não existe nenhum sinalizador ativado/desativado para identidades de módulo. Para obter mais informações, consulte [Guia do desenvolvedor do Hub IOT](iot-hub-devguide-identity-registry.md).
>

## <a name="update-the-module-twin-using-python-device-sdk"></a>Atualizar o módulo "/" usando o SDK do dispositivo Python

Nesta seção, você cria um aplicativo Python no dispositivo simulado que atualiza as propriedades relatadas do módulo.

1. **Obtenha a cadeia de conexão do módulo** – agora se você entrar no [portal do Azure](https://portal.azure.com/). Navegue até ao seu Hub IoT e clique em Dispositivos IoT. Localize myFirstDevice, abra-o e você verá que o myFirstModule foi criado com êxito. Copie a cadeia de ligação do módulo. É necessária para o próximo passo.

   ![Detalhe do módulo no portal do Azure](./media/iot-hub-python-python-module-twin-getstarted/module-detail.png)

2. **Criar aplicativo UpdateModuleTwinReportedProperties**

   Adicione as seguinte declarações `using` na parte superior do ficheiro **Program.cs**:

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

## <a name="get-updates-on-the-device-side"></a>Obter atualizações no lado do dispositivo

Além do código acima, você pode adicionar o bloco de código abaixo para obter a mensagem de atualização de mensagens em um dispositivo.

```python
import random
import time
import sys
import iothub_client
from iothub_client import IoTHubModuleClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult

PROTOCOL = IoTHubTransportProvider.AMQP
CONNECTION_STRING = ""


def module_twin_callback(update_state, payload, user_context):
    print("")
    print("Twin callback called with:")
    print("updateStatus: %s" % update_state)
    print("context: %s" % user_context)
    print("payload: %s" % payload)


try:
    module_client = IoTHubModuleClient(CONNECTION_STRING, PROTOCOL)
    module_client.set_module_twin_callback(module_twin_callback, 1234)

    print("Waiting for incoming twin messages.  Hit Control-C to exit.")
    while True:

        time.sleep(1000000)

except IoTHubError as iothub_error:
    print("Unexpected error {0}".format(iothub_error))
except KeyboardInterrupt:
    print("module client sample stopped")
```

## <a name="next-steps"></a>Passos seguintes

Para continuar a introdução ao Hub IoT e explorar outros cenários de IoT, veja:

* [Introdução ao gerenciamento de dispositivos](iot-hub-node-node-device-management-get-started.md)

* [Introdução ao IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)