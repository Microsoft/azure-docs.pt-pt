---
title: Como ligar uma amostra de ponte IoT Plug e Play que corre no Linux ou no Windows a um hub IoT Microsoft Docs
description: Construa e corra a IoT Plug e jogue a ponte em Linux ou Windows que se conecta a um hub IoT. Utilize a ferramenta exploradora Azure IoT para visualizar as informações enviadas pelo dispositivo para o hub.
author: usivagna
ms.author: ugans
ms.date: 09/22/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: eedd19189d1e1ccedd3d505aecf407aca8fca831
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94413365"
---
# <a name="how-to-connect-an--iot-plug-and-play-bridge-sample-running-on-linux-or-windows-to-iot-hub"></a>Como ligar uma amostra de ponte IoT Plug e Play que funciona no Linux ou windows ao IoT Hub

Este modo de fazer-lhe mostrar como construir o adaptador ambiental de amostra ioT Plug e Play Bridge, conectá-lo ao seu hub IoT e utilizar a ferramenta exploradora Azure IoT para visualizar a telemetria que envia. A ponte IoT Plug and Play está escrita em C e inclui o dispositivo Azure IoT SDK para C. No final deste tutorial deverá ser capaz de executar a ponte IoT Plug and Play e vê-la reportar telemetria no explorador Azure IoT: :::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-explorer-telemetry.png" alt-text="Uma imagem de ecrã mostrando o explorador Azure IoT com uma tabela de telemetria reportada (humidade, temperatura) da ponte Iot Plug e Play.":::

## <a name="prerequisites"></a>Pré-requisitos

Pode executar este arranque rápido no Linux ou Windows. Os comandos da concha neste guia seguem a convenção do Windows para separadores `\` de caminhos' ', se estiver a seguir o Linux certifique-se de trocar estes separadores por `/` '

Os pré-requisitos diferem por sistema operativo:

### <a name="linux"></a>Linux

Este quickstart assume que estás a usar o Ubuntu Linux. Os passos neste quickstart foram testados com ubuntu 18.04.

Para completar este arranque rápido no Linux, instale o seguinte software no seu ambiente Linux local:

Instale **GCC,** **Git,** **cmake,** e todas as dependências necessárias utilizando o `apt-get` comando:

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

Verifique se a versão é `cmake` superior a **2.8.12** e a versão do **GCC** é superior a **4.4.7**.

```sh
cmake --version
gcc --version
```

### <a name="windows"></a>Windows

Para completar este arranque rápido no Windows, instale o seguinte software no ambiente local do Windows:

* [Visual Studio (Comunidade, Profissional ou Enterprise)](https://visualstudio.microsoft.com/downloads/) - certifique-se de que inclui o Desktop Development com carga de trabalho **C++** quando [instalar](/cpp/build/vscpp-step-0-installation?preserve-view=true&view=vs-2019) o Visual Studio.
* [Git.](https://git-scm.com/download/)
* [CMake.](https://cmake.org/download/)

### <a name="azure-iot-explorer"></a>Explorador de Azure IoT

Para interagir com o dispositivo de amostra na segunda parte deste arranque rápido, utilize a ferramenta **exploradora Azure IoT.** [Descarregue e instale a mais recente versão do explorador Azure IoT](./howto-use-iot-explorer.md) para o seu sistema operativo.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Executar o seguinte comando para obter a _cadeia de ligação IoT Hub_ para o seu hub. Tome nota desta cadeia de ligação, use-a mais tarde neste arranque rápido:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> Também pode utilizar a ferramenta exploradora Azure IoT para encontrar a cadeia de ligação do hub IoT.

Executar o seguinte comando para obter a _cadeia de ligação_ do dispositivo para o dispositivo que adicionou ao hub. Tome nota desta cadeia de ligação, use-a mais tarde neste arranque rápido:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

## <a name="view-the-model"></a>Ver o modelo

Utilizará o Azure IoT Explorer em etapas posteriores para visualizar o dispositivo quando este se ligar ao seu hub IoT. O Azure IoT Explorer necessitará de uma cópia local do ficheiro modelo que corresponda ao **Modelo ID** que o seu dispositivo envia. O ficheiro do modelo permite ao IoT Explorer exibir a telemetria, propriedades e comandos que o seu dispositivo implementa.

Quando descarrega o código no degrau abaixo, inclui os ficheiros dos modelos de amostra sob a `pnpbridge/docs/schema` pasta. Para preparar o explorador Azure IoT:

1. Crie uma pasta chamada *modelos* na sua máquina local.
1. Ver [EnvironmentalSensor.jsligado](https://aka.ms/iot-pnp-bridge-env-model) e guarde o ficheiro JSON na pasta dos *modelos*
1. Veja [RootBridgeSampleDevice.jsligado](https://aka.ms/iot-pnp-bridge-root-model) e guarde o ficheiro JSON na pasta dos *modelos.*

## <a name="download-the-code"></a>Transferir o código

Abra um pedido de comando no diretório à sua escolha. Execute o seguinte comando para clonar o repositório [de IoT Plug e Play](https://aka.ms/iotplugandplaybridge) GitHub neste local:

```cmd
git clone https://github.com/Azure/iot-plug-and-play-bridge.git
```

Depois de clonar o repo de ponte IoT Plug e Play para a sua máquina, abra um pedido administrativo cmd e navegue para o diretório do repo clonado:

```cmd
cd pnpbridge
git submodule update --init --recursive
```

Espere que esta operação leve vários minutos para ser concluída.

>[!NOTE]
> Se tiver problemas com a atualização do sub módulo do clone git falhando, este é um problema conhecido com os caminhos de ficheiros do Windows e git ver: https://github.com/msysgit/git/pull/110 . Pode tentar o seguinte comando para resolver o problema: `git config --system core.longpaths true`

## <a name="setting-up-the-configuration-json"></a>Configuração do JSON de configuração

Depois de clonar o repo de ponte IoT Plug e Play para a sua máquina, navegue até ao `pnpbridge/docs/schema` diretório do repositório clonado onde pode encontrar a [configuração JSON](https://aka.ms/iot-pnp-bridge-env-config) ou `config.json` para a amostra de sensores ambientais da ponte. Pode saber mais sobre ficheiros config no [documento de conceitos IoT Plug e Play bridge.](concepts-iot-pnp-bridge.md)

Para o `root-_interface_model_id` campo terá de copiar o IoT Plug e o ID do modelo de reprodução que identifica o modelo do seu dispositivo. Neste exemplo, é `dtmi:com:example:SampleDevice;1`. Modificar os seguintes parâmetros **sob pnp_bridge_parameters** nó no `config.json` ficheiro:

* connection_string 
* symmetric_key 

>[!NOTE]
> O symmetric_key deve coincidir com a chave SAS na cadeia de ligação.

  ```JSON
    {
      "connection_parameters": {
        "connection_type" : "connection_string",
        "connection_string" : "[CONNECTION STRING]",
        "root_interface_model_id": "[To fill in]",
        "auth_parameters" : {
          "auth_type" : "symmetric_key",
          "symmetric_key" : "[SYMMETRIC KEY]"
        }
      }
    }
  }
  ```

 Uma vez preenchido, o `config.json` ficheiro deve assemelhar-se:

   ```JSON
    {
      "connection_parameters": {
        "connection_type" : "connection_string",
        "connection_string" : "[CONNECTION STRING]",
        "root_interface_model_id": "dtmi:com:example:SampleDevice;1",
        "auth_parameters" : {
          "auth_type" : "symmetric_key",
          "symmetric_key" : "[SYMMETRIC KEY]"
        }
      }
    }
  }
```

 Uma vez construída a ponte, terá de colocar isto `config.json` no mesmo diretório que a ponte ou especificar o seu caminho quando é executado.

## <a name="build-the-iot-plug-and-play-bridge"></a>Construa a ponte IoT Plug and Play

Navegue para a pasta *pnpbridge* no diretório do repositório.

Para o Windows executar o seguinte num [pedido de comando do desenvolvedor para o estúdio visual:](/dotnet/framework/tools/developer-command-prompt-for-vs)

```cmd
cd scripts\windows
build.cmd
```

Da mesma forma para o Linux executar o seguinte:

```bash
cd scripts/linux
./setup.sh
./build.sh
```

>[!TIP]
>No Windows, pode abrir a solução gerada pelo comando cmake no Visual Studio 2019. Abra o arquivo do projeto *azure_iot_pnp_bridge.sln* no diretório cmake e coloque o projeto *pnpbridge_bin* como o projeto de arranque na solução. Agora pode construir a amostra no Visual Studio e executá-la em modo de depurar.

## <a name="start-the-iot-plug-and-play-bridge"></a>Inicie a ponte IoT Plug and Play

 Inicie a amostra de ponte IoT Plug e Play para sensores ambientais navegando na pasta *pnpbridge* e executando o seguinte num pedido de comando:

```bash
 cd cmake/pnpbridge_linux/src/adapters/samples/environmental_sensor/
./pnpbridge_environmentalsensor

```

```cmd
REM Windows
cd cmake\pnpbridge_x86\src\adapters\samples\environmental_sensor
Debug\pnpbridge_environmentalsensor.exe
```

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Use o explorador Azure IoT para validar o código

Depois de iniciar a amostra do cliente do dispositivo, utilize a ferramenta exploradora Azure IoT para verificar se está a funcionar.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a ligar um dispositivo IoT Plug e Play a um hub IoT. Para saber mais sobre como construir uma solução que interage com os seus dispositivos IoT Plug e Play, consulte:

* [O que é IoT Plug e Play bridge](./concepts-iot-pnp-bridge.md)
* [Consulte a referência do GitHub Developer para ioT plug and play bridge](https://aka.ms/iot-pnp-bridge-dev-doc)
* [IoT Plug e Play bridge no GitHub](https://aka.ms/iotplugandplaybridge)
