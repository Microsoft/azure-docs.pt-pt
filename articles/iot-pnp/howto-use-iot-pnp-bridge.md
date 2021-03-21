---
title: Como ligar uma amostra de ponte IoT Plug e Play que funciona no Linux ou windows a um hub IoT | Microsoft Docs
description: Construa e corra a IoT Plug e jogue a ponte em Linux ou Windows que se conecta a um hub IoT. Utilize a ferramenta exploradora Azure IoT para visualizar as informações enviadas pelo dispositivo para o hub.
author: usivagna
ms.author: ugans
ms.date: 12/11/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 9bcf256b6144702254bbff4a57e5ff402abaa962
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99834107"
---
# <a name="how-to-connect-an--iot-plug-and-play-bridge-sample-running-on-linux-or-windows-to-iot-hub"></a>Como ligar uma amostra de ponte IoT Plug e Play que funciona no Linux ou windows ao IoT Hub

Este artigo mostra-lhe como construir o adaptador ambiental de amostra de IoT Plug e Play Bridge, conectá-lo ao seu hub IoT e usar a ferramenta exploradora Azure IoT para visualizar a telemetria que envia. A ponte IoT Plug and Play está escrita em C e inclui o dispositivo Azure IoT SDK para C. No final deste tutorial deverá ser capaz de executar a ponte IoT Plug and Play e vê-la reportar telemetria no explorador Azure IoT:

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-explorer-telemetry.png" alt-text="Uma imagem mostrando o explorador Azure IoT com uma tabela de telemetria reportada (humidade, temperatura) da ponte Iot Plug e Play.":::

## <a name="prerequisites"></a>Pré-requisitos

Pode executar a amostra no artigo no Windows ou Linux. Os comandos da concha neste guia seguem a convenção do Windows para separadores `\` de caminhos' ', se estiver a seguir o Linux certifique-se de trocar estes separadores por `/` '

### <a name="azure-iot-explorer"></a>Explorador de Azure IoT

Para interagir com o dispositivo de amostra na segunda parte deste artigo, utilize a ferramenta **exploradora Azure IoT.** [Descarregue e instale a mais recente versão do explorador Azure IoT](./howto-use-iot-explorer.md) para o seu sistema operativo.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Executar o seguinte comando para obter a _cadeia de ligação IoT Hub_ para o seu hub. Tome nota desta cadeia de ligação, use-a mais tarde neste artigo:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

Executar o seguinte comando para obter a _cadeia de ligação_ do dispositivo para o dispositivo que adicionou ao hub. Tome nota desta cadeia de ligação, use-a mais tarde neste artigo:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

## <a name="download-and-run-the-bridge"></a>Descarregue e corra a ponte

Neste artigo, tem duas opções para gerir a ponte. Pode:

- Faça o download de um executável pré-construído e execute-o como descrito nesta secção.
- Descarregue o código fonte e, em seguida, [construa e corra a ponte](#build-and-run-the-bridge) como descrito na secção seguinte.

Para descarregar e executar a ponte:

1. Aceda à página ioT Plug and [Play.](https://github.com/Azure/iot-plug-and-play-bridge/releases)
1. Descarregue o executável pré-incorporado para o seu sistema operativo: **pnpbridge_bin.exe** para Windows ou **pnpbridge_bin** para o Linux.
1. Descarregue a amostra [config.jsno](https://raw.githubusercontent.com/Azure/iot-plug-and-play-bridge/master/pnpbridge/src/adapters/samples/environmental_sensor/config.json) ficheiro de configuração para a amostra de sensores ambientais. Certifique-se de que o ficheiro de configuração está na mesma pasta que o executável.
1. Editar o *config.jsno* ficheiro:

    - Adicione o `connection-string` valor que é a cadeia de _ligação_ do dispositivo que fez anteriormente.
    - Adicione o `symmetric_key` valor que é valor de chave de acesso partilhado a partir da cadeia de _ligação_ do dispositivo .
    - Substitua o `root_interface_model_id` valor por `dtmi:com:example:PnpBridgeEnvironmentalSensor;1` .

    A primeira secção do *config.jsarquivado* agora parece o seguinte corte:

    ```json
    {
      "$schema": "../../../pnpbridge/src/pnpbridge_config_schema.json",
      "pnp_bridge_connection_parameters": {
        "connection_type" : "connection_string",
        "connection_string" : "HostName=youriothub.azure-devices.net;DeviceId=yourdevice;SharedAccessKey=TTrz8fR7ylHKt7DC/e/e2xocCa5VIcq5x9iQKxKFVa8=",
        "root_interface_model_id": "dtmi:com:example:PnpBridgeEnvironmentalSensor;1",
        "auth_parameters": {
            "auth_type": "symmetric_key",
            "symmetric_key": "TTrz8fR7ylHKt7DC/e/e2xocCa5VIcq5x9iQKxKFVa8="
        },
    ```

1. Execute o executável no ambiente da linha de comando. A ponte gera saída que se parece com:

    ```output
    c:\temp\temp-bridge>dir
     Volume in drive C is OSDisk
     Volume Serial Number is 38F7-DA4A
    
     Directory of c:\temp\temp-bridge
    
    10/12/2020  12:24    <DIR>          .
    10/12/2020  12:24    <DIR>          ..
    08/12/2020  15:26             1,216 config.json
    10/12/2020  12:21         3,617,280 pnpbridge_bin.exe
                   2 File(s)      3,618,496 bytes
                   2 Dir(s)  12,999,147,520 bytes free
    
    c:\temp\temp-bridge>pnpbridge_bin.exe
    Info:
     -- Press Ctrl+C to stop PnpBridge
    
    Info: Using default configuration location
    Info: Starting Azure PnpBridge
    Info: Pnp Bridge is running as am IoT egde device.
    Info: Pnp Bridge creation succeeded.
    Info: Connection_type is [connection_string]
    Info: Tracing is disabled
    Info: WARNING: SharedAccessKey is included in connection string. Ignoring symmetric_key in config file.
    Info: IoT Edge Device configuration initialized successfully
    Info: Building Pnp Bridge Adapter Manager, Adapters & Components
    Info: Adapter with identity environment-sensor-sample-pnp-adapter does not have any associated global parameters. Proceeding with adapter creation.
    Info: Pnp Adapter with adapter ID environment-sensor-sample-pnp-adapter has been created.
    Info: Pnp Adapter Manager created successfully.
    Info: Pnp components created successfully.
    Info: Pnp components built in model successfully.
    Info: Connected to Azure IoT Hub
    Info: Environmental Sensor: Starting Pnp Component
    Info: IoTHub client call to _SendReportedState succeeded
    Info: Environmental Sensor Adapter:: Sending device information property to IoTHub. propertyName=state, propertyValue=true
    Info: Pnp components started successfully.
    ```

## <a name="build-and-run-the-bridge"></a>Construir e executar a ponte

Se preferir construir o executável, pode baixar o código fonte e construir scripts.

Abra um pedido de comando numa pasta à sua escolha. Executar o seguinte comando para clonar o repositório [de IoT Plug e Play](https://github.com/Azure/iot-plug-and-play-bridge) GitHub neste local:

```cmd
git clone https://github.com/Azure/iot-plug-and-play-bridge.git
```

Depois de clonar o repositório, atualize os submodulos. Os submodulos incluem o Azure IoT SDK para C:

```cmd
cd iot-plug-and-play-bridge
git submodule update --init --recursive
```

Espere que esta operação leve vários minutos para ser concluída.

> [!TIP]
> Se tiver problemas com a falha na atualização do sub módulo do clone git, este é um problema conhecido com os caminhos de ficheiros do Windows. Pode tentar o seguinte comando para resolver o problema: `git config --system core.longpaths true`

Os pré-requisitos para a construção da ponte diferem por sistema operativo:

### <a name="windows"></a>Windows

Para construir a ponte IoT Plug e Play no Windows, instale o seguinte software:

* [Visual Studio (Comunidade, Profissional ou Enterprise)](https://visualstudio.microsoft.com/downloads/) - certifique-se de que inclui o Desktop Development com carga de trabalho **C++** quando [instalar](/cpp/build/vscpp-step-0-installation?preserve-view=true&view=vs-2019) o Visual Studio.
* [Git.](https://git-scm.com/download/)
* [CMake.](https://cmake.org/download/)

### <a name="linux"></a>Linux

Este artigo assume que estás a usar o Ubuntu Linux. Os passos deste artigo foram testados com Ubuntu 18.04.

Para construir a ponte IoT Plug and Play em Linux, instale **gCC,** **Git,** **cmake,** e todas as dependências necessárias utilizando o `apt-get` comando:

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

Verifique se a versão é `cmake` superior a **2.8.12** e a versão do **GCC** é superior a **4.4.7**.

```sh
cmake --version
gcc --version
```

### <a name="build-the-iot-plug-and-play-bridge"></a>Construa a ponte IoT Plug and Play

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
>No Windows, pode abrir a solução gerada pelo comando cmake no Visual Studio 2019. Abra o arquivo *azure_iot_pnp_bridge.sln* do projeto no diretório cmake e coloque o projeto *pnpbridge_bin* como o projeto de arranque na solução. Agora pode construir a amostra no Visual Studio e executá-la em modo de depurar.

### <a name="edit-the-configuration-file"></a>Editar o ficheiro de configuração

Pode saber mais sobre ficheiros config no [documento de conceitos IoT Plug e Play bridge.](concepts-iot-pnp-bridge.md)

Abra a *iot-plug-and-play-bridge\pnpbridge\src\adapters\samples\environmental_sensor\config.jsarquivada* num editor de texto.

- Adicione o `connection-string` valor que é a cadeia de _ligação_ do dispositivo que fez anteriormente.
- Adicione o `symmetric_key` valor que é valor de chave de acesso partilhado a partir da cadeia de _ligação_ do dispositivo .
- Substitua o `root_interface_model_id` valor por `dtmi:com:example:PnpBridgeEnvironmentalSensor;1` .

A primeira secção do *config.jsarquivado* agora parece o seguinte corte:

```json
{
  "$schema": "../../../pnpbridge/src/pnpbridge_config_schema.json",
  "pnp_bridge_connection_parameters": {
    "connection_type" : "connection_string",
    "connection_string" : "HostName=youriothub.azure-devices.net;DeviceId=yourdevice;SharedAccessKey=TTrz8fR7ylHKt7DC/e/e2xocCa5VIcq5x9iQKxKFVa8=",
    "root_interface_model_id": "dtmi:com:example:PnpBridgeEnvironmentalSensor;1",
    "auth_parameters": {
        "auth_type": "symmetric_key",
        "symmetric_key": "TTrz8fR7ylHKt7DC/e/e2xocCa5VIcq5x9iQKxKFVa8="
    },
```

### <a name="run-the-iot-plug-and-play-bridge"></a>Executar a ponte IoT Plug and Play

Inicie a amostra de sensores ambientais IoT Plug e Play Bridge. O parâmetro é o caminho para `config.json` o arquivar editado na secção anterior:

```cmd
REM Windows
cd iot-plug-and-play-bridge\pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console
Debug\pnpbridge_bin.exe ..\..\..\..\..\..\src\adapters\samples\environmental_sensor\config.json
```

A ponte gera saída que se parece com:

```output
c:\temp>cd iot-plug-and-play-bridge\pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console

c:\temp\iot-plug-and-play-bridge\pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console>Debug\pnpbridge_bin.exe ..\..\..\..\..\..\src\adapters\samples\environmental_sensor\config.json
Info:
 -- Press Ctrl+C to stop PnpBridge

Info: Using configuration from specified file path: ..\..\..\..\..\..\src\adapters\samples\environmental_sensor\config.json
Info: Starting Azure PnpBridge
Info: Pnp Bridge is running as am IoT egde device.
Info: Pnp Bridge creation succeeded.
Info: Connection_type is [connection_string]
Info: Tracing is disabled
Info: WARNING: SharedAccessKey is included in connection string. Ignoring symmetric_key in config file.
Info: IoT Edge Device configuration initialized successfully
Info: Building Pnp Bridge Adapter Manager, Adapters & Components
Info: Adapter with identity environment-sensor-sample-pnp-adapter does not have any associated global parameters. Proceeding with adapter creation.
Info: Pnp Adapter with adapter ID environment-sensor-sample-pnp-adapter has been created.
Info: Pnp Adapter Manager created successfully.
Info: Pnp components created successfully.
Info: Pnp components built in model successfully.
Info: Connected to Azure IoT Hub
Info: Environmental Sensor: Starting Pnp Component
Info: IoTHub client call to _SendReportedState succeeded
Info: Environmental Sensor Adapter:: Sending device information property to IoTHub. propertyName=state, propertyValue=true
Info: Pnp components started successfully.
Info: IoTHub client call to _SendEventAsync succeeded
Info: PnpBridge_PnpBridgeStateTelemetryCallback called, result=0, telemetry=PnpBridge configuration complete
Info: Processing property update for the device or module twin
Info: Environmental Sensor Adapter:: Successfully delivered telemetry message for <environmentalSensor>
```

Utilize os seguintes comandos para executar a ponte em Linux:

```bash
cd iot-plug-and-play-bridge/pnpbridge/cmake/pnpbridge_x86/src/pnpbridge/samples/console
./pnpbridge_bin ../../../../../../src/adapters/samples/environmental_sensor/config.json
```

## <a name="download-the-model-files"></a>Descarregue os ficheiros de modelos

Utilize o Azure IoT Explorer mais tarde para visualizar o dispositivo quando este se ligar ao seu hub IoT. O Azure IoT Explorer necessita de uma cópia local do ficheiro modelo que corresponda ao **Modelo ID** que o seu dispositivo envia. O ficheiro do modelo permite ao IoT Explorer exibir a telemetria, propriedades e comandos que o seu dispositivo implementa.

Para baixar os modelos para explorador Azure IoT:

1. Crie uma pasta chamada *modelos* na sua máquina local.
1. Guarde [EnvironmentalSensor.jsna](https://raw.githubusercontent.com/Azure/iot-plug-and-play-bridge/master/pnpbridge/docs/schemas/EnvironmentalSensor.json) pasta dos *modelos* que criou no passo anterior.
1. Se abrir este ficheiro de modelo num editor de texto, pode ver que o modelo define um componente com `dtmi:com:example:PnpBridgeEnvironmentalSensor;1` o seu ID. Este é o mesmo ID do modelo que usou no *config.jsficheiro.*

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Use o explorador Azure IoT para validar o código

Depois de a ponte começar, utilize a ferramenta exploradora Azure IoT para verificar se está a funcionar. Pode ver a telemetria, propriedades e comandos definidos no `dtmi:com:example:PnpBridgeEnvironmentalSensor;1` modelo.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a ligar um dispositivo IoT Plug and Play a um hub IoT. Para saber mais sobre como construir uma solução que interage com os seus dispositivos IoT Plug e Play, consulte:

* [O que é IoT Plug e Play bridge](./concepts-iot-pnp-bridge.md)
* [Construa, implemente e estenda a ponte IoT Plug e Play](howto-build-deploy-extend-pnp-bridge.md)
* [IoT Plug e Play bridge no GitHub](https://github.com/Azure/iot-plug-and-play-bridge)
