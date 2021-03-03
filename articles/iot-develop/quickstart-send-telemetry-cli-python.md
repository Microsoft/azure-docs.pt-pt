---
title: Enviar telemetria do dispositivo para o quickstart Azure IoT Hub (Python)
description: Neste arranque rápido, você usa o Azure IoT Hub Device SDK para python para enviar telemetria de um dispositivo para um hub Iot.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: python
ms.topic: quickstart
ms.date: 01/11/2021
ms.openlocfilehash: ccf7f272069516d53732b10ec1af5bb4841f1208
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101730427"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-azure-iot-hub-python"></a>Quickstart: Enviar telemetria de um dispositivo para um hub Azure IoT (Python)

**Aplica-se a**: [Desenvolvimento de aplicações de dispositivos](about-iot-develop.md#device-application-development)

Neste arranque rápido, você aprende um fluxo básico de desenvolvimento de aplicações de dispositivo ioT. Você usa o CLI Azure para criar um hub Azure IoT e um dispositivo, em seguida, você usa o Azure IoT Python SDK para construir um dispositivo de cliente simulado e enviar telemetria para o centro. 

## <a name="prerequisites"></a>Pré-requisitos
- Se não tiver uma subscrição do Azure, [crie uma gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- CLI do Azure. Pode executar todos os comandos neste quickstart usando o Azure Cloud Shell, uma concha CLI interativa que funciona no seu navegador. Se utilizar a Cloud Shell, não precisa de instalar nada. Se preferir utilizar o CLI localmente, este quickstart requer a versão 2.0.76 ou posterior do Azure CLI. Execute az --version para localizar a versão. Para instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).
- [Python 3.7+](https://www.python.org/downloads/). Para outras versões de Python suportadas, consulte [as funcionalidades do dispositivo Azure IoT](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device#azure-iot-device-features).
    
    Para garantir que a sua versão Python está atualizada, corra `python --version` . Se tiver ambos Python 2 e Python 3 instalados, e estiver a utilizar um ambiente Python 3, instale todas as bibliotecas usando `pip3` . Isto garante que as bibliotecas estão instaladas no seu tempo de funcionação Python 3.
    > [!IMPORTANT]
    > No instalador Python, selecione a opção **de adicionar Python ao PATH**. Se já tiver Python 3.7 ou superior instalado, confirme que adicionou a pasta de instalação Python à `PATH` variável ambiente.

[!INCLUDE [iot-hub-include-create-hub-cli](../../includes/iot-hub-include-create-hub-cli.md)]

## <a name="use-the-python-sdk-to-send-messages"></a>Use o Python SDK para enviar mensagens
Nesta secção, utilizará o Python SDK para enviar mensagens do seu dispositivo simulado para o seu hub IoT.

1. Abra uma janela de terminal nova. Você usará este terminal para instalar o Python SDK e trabalhar com o código de amostra Python. Deve agora ter dois terminais abertos: o que abriu para trabalhar com python, e a concha CLI que usou em secções anteriores para introduzir comandos Azure CLI.       

1. Copie as amostras do [dispositivo Azure IoT Python SDK](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples) para a sua máquina local:

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```

    e navegando para o diretório *azure-iot-sdk-python/azure-iot-device/samples:*

    ```console
    cd azure-iot-sdk-python/azure-iot-device/samples
    ```
1. Instale o Azure IoT Python SDK:

    ```console
    pip install azure-iot-device
    ```
1. Desaprote a cadeia de ligação do dispositivo como uma variável ambiente chamada `IOTHUB_DEVICE_CONNECTION_STRING` . Esta é a cadeia que obteve na secção anterior depois de criar o seu dispositivo Python simulado.

    **Janelas (cmd)**

    ```console
    set IOTHUB_DEVICE_CONNECTION_STRING=<your connection string here>
    ```

    > [!NOTE]
    > Para o Windows CMD não existem marcas de aspas em torno da cadeia de ligação.

    **Linux (bash)**

    ```bash
    export IOTHUB_DEVICE_CONNECTION_STRING="<your connection string here>"
    ```

1. Na sua concha CLI aberta, corram o comando [de monitor-eventos do hub az iot](/cli/azure/ext/azure-iot/iot/hub?preserve-view=true&view=azure-cli-latest#ext-azure-iot-az-iot-hub-monitor-events) para começar a monitorizar eventos no seu dispositivo IoT simulado.  As mensagens de evento serão impressas no terminal à medida que chegam.

    ```azurecli
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```

1. No seu terminal Python, execute o código para o ficheiro de amostra instalado *simple_send_message.py* . Este código acede ao dispositivo IoT simulado e envia uma mensagem para o hub IoT.

    Para executar a amostra python do terminal:
    ```console
    python ./simple_send_message.py
    ```

    Opcionalmente, pode executar o código Python a partir da amostra no seu Python IDE:
    ```python
    import os
    import asyncio
    from azure.iot.device.aio import IoTHubDeviceClient


    async def main():
        # Fetch the connection string from an environment variable
        conn_str = os.getenv("IOTHUB_DEVICE_CONNECTION_STRING")

        # Create instance of the device client using the authentication provider
        device_client = IoTHubDeviceClient.create_from_connection_string(conn_str)

        # Connect the device client.
        await device_client.connect()

        # Send a single message
        print("Sending message...")
        await device_client.send_message("This is a message that is being sent")
        print("Message successfully sent!")

        # finally, disconnect
        await device_client.disconnect()


    if __name__ == "__main__":
        asyncio.run(main())
    ```

Como o código Python envia uma mensagem do seu dispositivo para o hub IoT, a mensagem aparece na sua concha CLI que está a monitorizar eventos:

```output
Starting event monitor, use ctrl-c to stop...
event:
origin: <your Device name>
payload: This is a message that is being sent
```

O seu dispositivo está agora ligado de forma segura e a enviar telemetria para o Azure IoT Hub.

## <a name="clean-up-resources"></a>Limpar os recursos
Se já não precisar dos recursos Azure criados neste arranque rápido, pode utilizar o CLI Azure para os eliminar.

> [!IMPORTANT]
> A eliminação de um grupo de recursos é irreversível. O grupo de recursos e todos os recursos nele contidos são eliminados permanentemente. Confirme que não elimina acidentalmente o grupo de recursos ou recursos errados.

Para eliminar um grupo de recursos por nome:
1. Executar o [comando de eliminação do grupo az.](/cli/azure/group?preserve-view=true&view=azure-cli-latest#az-group-delete) Isto remove o grupo de recursos, o Hub IoT e o registo do dispositivo que criou.

    ```azurecli
    az group delete --name MyResourceGroup
    ```
1. Executar o comando [da lista de grupos az](/cli/azure/group?preserve-view=true&view=azure-cli-latest#az-group-list) para confirmar que o grupo de recursos é eliminado.  

    ```azurecli
    az group list
    ```

## <a name="next-steps"></a>Passos seguintes
Neste arranque rápido, aprendeu um fluxo básico de trabalho da aplicação Azure IoT para ligar um dispositivo de forma segura à nuvem e enviar telemetria de dispositivo para nuvem. Usou o Azure CLI para criar um hub IoT e um dispositivo, depois usou o Azure IoT Python SDK para construir um dispositivo simulado e enviar telemetria para o centro. 

Como próximo passo, explore o Azure IoT Python SDK através de amostras de aplicação.

- [Amostras assíncronos](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-hub-scenarios): Este diretório contém amostras de Python assíncronos para cenários adicionais do Hub IoT.
- [Amostras sincronizadas](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/sync-samples): Este diretório contém amostras python para utilização com python 2.7 ou cenários de compatibilidade sincronizados para Python 3.5+
- [Amostras IoT Edge](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-edge-scenarios): Este diretório contém amostras python para trabalhar com módulos Edge e dispositivos a jusante.