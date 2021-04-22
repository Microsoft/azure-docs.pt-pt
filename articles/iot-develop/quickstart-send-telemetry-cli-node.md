---
title: Enviar telemetria do dispositivo para o quickstart do Azure IoT Hub (Node.js)
description: Neste arranque rápido, você usa o Azure IoT Hub Device SDK para Node.js enviar telemetria de um dispositivo para um hub Iot.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: node
ms.topic: quickstart
ms.date: 03/25/2021
ms.openlocfilehash: 0e1c99124228da9490abaa17ecc41b931631d9fb
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876981"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-nodejs"></a>Quickstart: Enviar telemetria de um dispositivo para um hub IoT (Node.js)

**Aplica-se a**: [Desenvolvimento de aplicações de dispositivos](about-iot-develop.md#device-application-development)

Neste arranque rápido, você aprende um fluxo básico de desenvolvimento de aplicações de dispositivo ioT. Utiliza o CLI Azure para criar um hub Azure IoT e um dispositivo simulado, depois utiliza o Azure IoT Node.js SDK para aceder ao dispositivo e enviar telemetria para o centro.

## <a name="prerequisites"></a>Pré-requisitos
- Se não tiver uma subscrição do Azure, [crie uma gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- CLI do Azure. Pode executar todos os comandos neste quickstart usando o Azure Cloud Shell, uma concha CLI interativa que funciona no seu navegador. Se utilizar a Cloud Shell, não precisa de instalar nada. Se preferir utilizar o CLI localmente, este quickstart requer a versão 2.0.76 ou posterior do Azure CLI. Execute az --version para localizar a versão. Para instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).
- [Node.js 10+](https://nodejs.org). Se estiver a utilizar o Azure Cloud Shell, não atualize a versão instalada do Node.js. O Azure Cloud Shell já tem a versão mais recente Node.js.

    Verifique a versão atual de Node.js na sua máquina de desenvolvimento utilizando o seguinte comando:

    ```cmd/sh
        node --version
    ```

[!INCLUDE [iot-hub-include-create-hub-cli](../../includes/iot-hub-include-create-hub-cli.md)]

## <a name="use-the-nodejs-sdk-to-send-messages"></a>Utilize o Node.js SDK para enviar mensagens
Nesta secção, utilizará o Node.js SDK para enviar mensagens do seu dispositivo simulado para o seu hub IoT. 

1. Abra uma janela de terminal nova. Utilizará este terminal para instalar o Node.js SDK e trabalhará com Node.js código de amostra. Deve agora ter dois terminais abertos: o que abriu para trabalhar com Node.js, e a concha CLI que usou em secções anteriores para introduzir comandos Azure CLI.

1. Copie as amostras do [dispositivo Azure IoT Node.js SDK](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples) para a sua máquina local:

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-node
    ```

1. Navegue para o *azure-iot-sdk-node/dispositivo/samples/pnp* diretório:

    ```console
    cd azure-iot-sdk-node/device/samples/pnp
    ```

1. Instale o Azure IoT Node.js SDK e as dependências necessárias:

    ```console
    npm install
    ```

    Este comando instala as dependências adequadas, conforme especificado no *package.jsem* ficheiro no diretório de amostras do dispositivo.

1. Desloja ambas as seguintes variáveis ambientais, para permitir que o seu dispositivo simulado se conecte ao Azure IoT.
    * Desaprova uma variável ambiental chamada `IOTHUB_DEVICE_CONNECTION_STRING` . Para o valor variável, utilize a cadeia de ligação do dispositivo que guardou na secção anterior.
    * Desaprova uma variável ambiental chamada `IOTHUB_DEVICE_SECURITY_TYPE` . Para a variável, use o valor literal da `connectionString` corda.

    **Janelas (cmd)**

    ```console
    set IOTHUB_DEVICE_CONNECTION_STRING=<your connection string here>
    ```
    ```console
    set IOTHUB_DEVICE_SECURITY_TYPE=connectionString
    ```

    > [!NOTE]
    > Para o Windows CMD não existem marcas de aspas em torno dos valores de cadeia para cada variável.

    **PowerShell**

    ```azurepowershell
    $env:IOTHUB_DEVICE_CONNECTION_STRING='<your connection string here>'
    ```
    ```azurepowershell
    $env:IOTHUB_DEVICE_SECURITY_TYPE='connectionString'
    ```

    **Bash (Linux ou Windows)**

    ```bash
    export IOTHUB_DEVICE_CONNECTION_STRING="<your connection string here>"
    ```
    ```bash
    export IOTHUB_DEVICE_SECURITY_TYPE="connectionString"
    ```
1. Na sua concha CLI aberta, corram o comando [de monitor-eventos do hub az iot](/cli/azure/iot/hub#az_iot_hub_monitor_events) para começar a monitorizar eventos no seu dispositivo IoT simulado.  As mensagens de evento serão impressas no terminal à medida que chegam.

    ```azurecli
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```

1. No seu terminal Node.js, execute o código para o ficheiro de amostra instalado *simple_thermostat.js* . Este código acede ao dispositivo IoT simulado e envia uma mensagem para o hub IoT.

    Para executar a amostra de Node.js do terminal:
    ```console
    node ./simple_thermostat.js
    ```
    > [!NOTE]
    > Esta amostra de código utiliza o Azure IoT Plug and Play, que permite integrar dispositivos inteligentes nas suas soluções sem qualquer configuração manual.  Por predefinição, a maioria das amostras desta documentação utilizam IoT Plug e Play. Para saber mais sobre as vantagens do IoT PnP, e os casos para usá-lo ou não, veja [o que é IoT Plug e Play?](../iot-pnp/overview-iot-plug-and-play.md)

À medida que o código Node.js envia uma mensagem de telemetria simulada do seu dispositivo para o hub IoT, a mensagem aparece na sua concha CLI que está a monitorizar eventos:

```output
Starting event monitor, use ctrl-c to stop...
event:
  component: ''
  interface: dtmi:com:example:Thermostat;1
  module: ''
  origin: <your device ID>
  payload:
    temperature: 36.87027777131555
```

O seu dispositivo está agora ligado de forma segura e a enviar telemetria para o Azure IoT Hub.

## <a name="clean-up-resources"></a>Limpar os recursos
Se já não precisar dos recursos Azure criados neste arranque rápido, pode utilizar o CLI Azure para os eliminar.

> [!IMPORTANT]
> A eliminação de um grupo de recursos é irreversível. O grupo de recursos e todos os recursos nele contidos são eliminados permanentemente. Confirme que não elimina acidentalmente o grupo de recursos ou recursos errados. 

Para eliminar um grupo de recursos por nome:
1. Executar o [comando de eliminação do grupo az.](/cli/azure/group#az_group_delete) Este comando remove o grupo de recursos, o Hub IoT e o registo do dispositivo que criou.

    ```azurecli
    az group delete --name MyResourceGroup
    ```
1. Executar o comando [da lista de grupos az](/cli/azure/group#az_group_list) para confirmar que o grupo de recursos é eliminado.  

    ```azurecli
    az group list
    ```

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu um fluxo básico de trabalho da aplicação Azure IoT para ligar um dispositivo de forma segura à nuvem e enviar telemetria de dispositivo para nuvem. Usou o CLI Azure para criar um hub IoT e um dispositivo simulado, depois usou o Azure IoT Node.js SDK para aceder ao dispositivo e enviar telemetria para o centro. 

Como próximo passo, explore o Azure IoT Node.js SDK através de amostras de aplicação.

- [Mais Node.js Amostras](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples): Este diretório contém mais amostras do repositório SDK Node.js para mostrar cenários do IoT Hub.