---
title: Interagir com um dispositivo de visualização de Plug and Play IoT conectado à solução de IoT do Azure | Microsoft Docs
description: Use o Node. js para se conectar e interagir com um dispositivo de visualização de Plug and Play IoT que está conectado à sua solução de IoT do Azure.
author: miagdp
ms.author: miag
ms.date: 08/02/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: a082e4b7896b317bf2b28971d3693bada95a3445
ms.sourcegitcommit: b8578b14c8629c4e4dea4c2e90164e42393e8064
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/09/2019
ms.locfileid: "70806552"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution"></a>Início rápido: Interagir com um dispositivo de visualização de Plug and Play IoT que está conectado à sua solução

O IoT Plug and Play Preview simplifica a IoT, permitindo que você interaja com os recursos de um dispositivo sem conhecer a implementação do dispositivo subjacente. Este guia de início rápido mostra como usar o Node. js para se conectar e controlar um dispositivo de Plug and Play de IoT que está conectado à sua solução.

## <a name="prerequisites"></a>Pré-requisitos

Baixe e instale o Node. js em [NodeJS.org](https://nodejs.org).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-an-iot-hub"></a>Preparar um hub IoT

Você também precisa de um hub IoT do Azure em sua assinatura do Azure para concluir este guia de início rápido. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

> [!NOTE]
> Durante a visualização pública, os recursos de Plug and Play de IoT só estão disponíveis em hubs IoT criados nas regiões **EUA Central**, **Europa setentrional**e **leste do Japão** .

Adicione a extensão Microsoft Azure IoT para CLI do Azure:

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Execute o comando a seguir para criar a identidade do dispositivo em seu hub IoT. Substitua **nomedoseuhubiot** e **YourDevice** por seus nomes reais. Se você não tiver um hub IoT, siga [estas instruções](../iot-hub/iot-hub-create-using-cli.md) para criar um:

```azurecli-interactive
az iot hub device-identity create --hub-name [YourIoTHubName] --device-id [YourDevice]
```

Execute os seguintes comandos para obter a _cadeia de conexão do dispositivo_ para o dispositivo que você acabou de registrar:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name [YourIoTHubName] --device-id [YourDevice] --output table
```

Execute os seguintes comandos para obter a _cadeia de conexão do Hub IOT_ para o Hub:

```azurecli-interactive
az iot hub show-connection-string --hub-name [YourIoTHubName] --output table
```

## <a name="connect-your-device"></a>Ligue o seu dispositivo

Neste guia de início rápido, você usa um sensor ambiental de exemplo que é escrito em node. js como o dispositivo de Plug and Play de IoT. As instruções a seguir mostram como instalar e executar o dispositivo:

1. Clone o repositório GitHub:

    ```cmd/sh
    git clone https://github.com/azure-samples/azure-iot-samples-node
    ```

1. Em um terminal, vá para a pasta raiz do seu repositório clonado, navegue até a pasta **/Azure-IOT-Samples-node/digital-Twins/QuickStarts/Device** e, em seguida, instale todas as dependências executando o seguinte comando:

    ```cmd/sh
    npm install
    ```

1. Configure a _cadeia de conexão do dispositivo_:

    ```cmd/sh
    set DEVICE_CONNECTION_STRING=<your device connection string>
    ```

1. Execute o exemplo com o seguinte comando:

    ```cmd/sh
    node sample_device.js
    ```

1. Você vê mensagens dizendo que o dispositivo enviou telemetria e suas propriedades. Agora, o dispositivo está pronto para receber comandos e atualizações de propriedade. Não feche este terminal, você precisará dele mais tarde para confirmar se os exemplos de serviço também funcionaram.

## <a name="build-the-solution"></a>Compilar a solução

Neste guia de início rápido, você usa uma solução de IoT de exemplo no node. js para interagir com o dispositivo de exemplo.

1. Abra outro terminal. Vá para a pasta do repositório clonado e navegue até a pasta **/Azure-IOT-Samples-node/digital-Twins/QuickStarts/Service** . Instale todas as dependências executando o seguinte comando:

    ```cmd/sh
    npm install
    ```

1. Configurar a _cadeia de conexão do Hub_:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<your hub connection string>
    ```

### <a name="read-a-property"></a>Ler uma propriedade

1. Ao conectar o dispositivo no terminal, você verá a seguinte mensagem:

    ```cmd/sh
    reported state property as online
    ```

1. Abra o arquivo **get_digital_twin. js**. `deviceID` Substitua por sua ID do dispositivo e salve o arquivo.

1. Vá para o terminal que você abriu para executar o exemplo de serviço e execute o seguinte comando:

    ```cmd/sh
    node get_digital_twin.js
    ```

1. Na saída, no componente _environmentalSensor_ , você vê que o mesmo estado foi relatado:

    ```JSON
    reported state property as online
    ```

### <a name="update-a-writable-property"></a>Atualizar uma propriedade gravável

1. Abra o arquivo **update_digital_twin_property. js**.

1. No início do arquivo, há um conjunto de constantes definidas com espaços reservados em maiúsculas. Substitua o **DeviceID** pela sua ID de dispositivo real, atualize as constantes com os seguintes valores e salve o arquivo:

    ```javascript
    const componentName = 'environmentalSensor';
    const propertyName = 'brightness';
    const propertyValue = 60;
    ```

1. Vá para o terminal que você abriu para executar o exemplo de serviço e use o seguinte comando para executar o exemplo:

    ```cmd/sh
    node update_digital_twin_property.js
    ```

1. No terminal, você vê as informações de teledigitais associadas ao seu dispositivo. Localize o componente _environmentalSensor_, você verá o novo valor de brilho 60.

    ```json
        "environmentalSensor": {
        "name": "environmentalSensor",
        "properties": {
          "brightness": {
            "reported": {
              "value": 60,
              "desiredState": {
                "code": 200,
                "version": 14,
                "description": "helpful descriptive text"
              }
            },
            "desired": {
              "value": 60
            }
          },
          "state": {
            "reported": {
              "value": "online"
            }
          }
        }
      }
    ```

1. Vá para o terminal do _dispositivo_ , você verá que o dispositivo recebeu a atualização:

    ```cmd/sh
    Received an update for brightness: 60
    updated the property
    ```
2. Volte para o terminal do _serviço_ , execute o comando abaixo novamente para confirmar que a propriedade foi atualizada.
    
    ```cmd/sh
    node get_digital_twin.js
    ```
3. Na saída, no componente environmentalSensor, você verá que o valor de brilho atualizado foi relatado. Observação: pode levar algum tempo para que o dispositivo conclua a atualização. Você pode repetir essa etapa até que o dispositivo realmente processe a atualização de propriedade.
    
    ```json
      "brightness": {
        "reported": {
          "value": 60,
          }
       }
    ```

### <a name="invoke-a-command"></a>Invocar um comando

1. Abra o arquivo **invoke_command. js**.

1. No início do arquivo, substitua `deviceID` por sua ID de dispositivo real. Atualize as constantes com os seguintes valores e salve o arquivo:

    ```javascript
    const interfaceInstanceName = 'environmentalSensor';
    const commandName = 'blink';
    ```

1. Vá para o terminal que você abriu para executar o exemplo de serviço. Use o seguinte comando para executar o exemplo:

    ```cmd/sh
    node invoke_command.js
    ```

1. No terminal, o sucesso é semelhante ao seguinte resultado:

    ```cmd/sh
    invoking command blink on component environmentalSensor for device test...
    {
      "result": "helpful response text",
      "statusCode": 200,
      "requestId": "33e536d3-14f7-4105-88f3-629b9933851c",
      "_response": "helpful response text"
    }
    ```

1. Vá para o terminal do _dispositivo_ , você verá que o comando foi confirmado:

    ```cmd/sh
    received command: blink for component: environmentalSensor
    acknowledgement succeeded.
    ```

## <a name="clean-up-resources"></a>Limpar recursos

Se você planeja continuar com artigos posteriores, você pode manter os recursos usados neste guia de início rápido. Caso contrário, você pode excluir os recursos que criou para este guia de início rápido para evitar encargos adicionais.

Para excluir o Hub e o dispositivo registrado, conclua as seguintes etapas usando o CLI do Azure:

```azurecli-interactive
az group delete --name <Your group name>
```

Para excluir apenas o dispositivo registrado com o Hub IoT, conclua as seguintes etapas usando o CLI do Azure:

```azurecli-interactive
az iot hub device-identity delete --hub-name [YourIoTHubName] --device-id [YourDevice]
```

## <a name="next-steps"></a>Passos Seguintes

Neste guia de início rápido, você aprendeu a conectar um dispositivo de Plug and Play IoT a uma solução de IoT. Para saber mais sobre como criar uma solução que interage com seus dispositivos de Plug and Play de IoT, confira:

> [!div class="nextstepaction"]
> [Procedimento: Conectar-se a um dispositivo e interagir com ele](howto-develop-solution.md)
