---
title: Como construir e implantar a ponte IoT Plug e Play | Microsoft Docs
description: Identifique os componentes da ponte IoT Plug e Play. Aprenda a executá-lo em dispositivos IoT, gateways e como um módulo IoT Edge.
author: usivagna
ms.author: ugans
ms.date: 1/20/2021
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: c89427f83600d3b8091d5293b1757fa6f1a15ef1
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102202489"
---
# <a name="build-and-deploy-the-iot-plug-and-play-bridge"></a>Construa e implemente a ponte IoT Plug and Play

A [ponte IoT Plug and Play](concepts-iot-pnp-bridge.md#iot-plug-and-play-bridge-architecture) permite-lhe ligar os dispositivos existentes ligados a uma porta de entrada para o seu hub IoT. Utiliza a ponte para mapear interfaces IoT Plug e Play para os dispositivos anexados. Uma interface IoT Plug and Play define a telemetria que um dispositivo envia, as propriedades sincronizadas entre o dispositivo e a nuvem, e os comandos a que o dispositivo responde. Pode instalar e configurar a aplicação de ponte de código aberto nos gateways Windows ou Linux. Além disso, a ponte pode ser executada como um módulo de tempo de execução Azure IoT Edge.

Este artigo explica em detalhe como:

- Configure uma ponte.
- Como construir e gerir a ponte em vários ambientes.

Para um exemplo simples que mostra como usar a ponte, consulte [como ligar a amostra de ponte IoT Plug e Play que funciona no Linux ou Windows ao IoT Hub](howto-use-iot-pnp-bridge.md).

As orientações e amostras deste artigo assumem uma familiaridade básica com [as Gémeas Digitais Azure](../digital-twins/overview.md) e [ioT Plug and Play](overview-iot-plug-and-play.md).

## <a name="general-prerequisites"></a>Pré-requisitos gerais

### <a name="supported-os-platforms"></a>Plataformas de SO suportadas

As seguintes plataformas e versões de SO são suportadas:

|Plataforma  |Versões Suportadas  |
|---------|---------|
|Windows 10 |     Todos os SKUs do Windows são suportados. Por exemplo: IoT Enterprise, Server, Desktop, IoT Core. *Para a funcionalidade de monitorização de saúde da câmara, recomenda-se a construção de 20H1 ou posterior. Todas as outras funcionalidades estão disponíveis em todas as construções do Windows 10.*  |
|Linux     |Testada e suportada no Ubuntu 18.04, a funcionalidade em outras distribuições não foi testada.         |
||

### <a name="hardware"></a>Hardware

- Qualquer plataforma de hardware que suporte os SKUs e versões de OS acima.
- Os periféricos e sensores de série, USB, Bluetooth e Camera são suportados de forma nativa. A Ponte IoT Plug e Play Bridge pode ser estendida para suportar qualquer periférico ou sensor personalizado.

### <a name="azure-iot-products-and-tools"></a>Produtos e ferramentas Azure IoT

- **Azure IoT Hub** - Precisa de um [hub Azure IoT](../iot-hub/index.yml) na sua assinatura Azure para ligar o seu dispositivo. Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar. Se não tiver um hub IoT, [siga estas instruções para criar uma](../iot-hub/iot-hub-create-using-cli.md). O suporte IoT Plug e Play não está incluído em centros IoT de nível básico.
- **Explorador Azure IoT** - Para interagir com o seu dispositivo IoT Plug and Play, pode utilizar a ferramenta exploradora Azure IoT. [Descarregue e instale a mais recente versão do explorador Azure IoT](./howto-use-iot-explorer.md) para o seu sistema operativo. Configure a ferramenta exploradora Azure IoT para ligar ao seu hub IoT e procurar definições de modelo na pasta *pnpbridge\docs\schemas* na **ponte iot-plug-and-play-que** clonou.

## <a name="configure-a-bridge"></a>Configure uma ponte

Há duas maneiras de configurar a ponte:

- Se a ponte estiver a funcionar de forma nativa num dispositivo IoT ou gateway, utilize o ficheiro de configuração. Este cenário pode utilizar uma máquina Linux ou Windows.
- Se a ponte estiver a funcionar como um módulo IoT Edge no tempo de funcionamento IoT Edge, utilize a propriedade dupla do módulo desejada. Este cenário pressupõe que o tempo de execução IoT Edge é hospedado num ambiente Linux.

### <a name="configuration-file"></a>Ficheiro de configuração

Quando a ponte Plug and Play está a funcionar de forma nativa num dispositivo IoT ou gateway, utiliza um ficheiro de configuração para:

- Obtenha a informação de ligação IoT Central ou IoT Hub.
- Configure os dispositivos para os quais são publicadas interfaces IoT Plug e Play.

Utilize uma das seguintes opções para fornecer o ficheiro de configuração à ponte:

- Passe o caminho para o ficheiro de configuração como um parâmetro de linha de comando para a ponte executável.
- Utilize a *config.js* existente no ficheiro na pasta *pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console.*

O [esquema de ficheiros de configuração](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/src/pnpbridge/src/pnpbridge_config_schema.json) está disponível no repositório GitHub.

> [!TIP]
> Se editar um ficheiro de configuração de ponte no Código VS, pode utilizar este ficheiro de esquema para validar o seu ficheiro.

### <a name="iot-edge-module-configuration"></a>Configuração do módulo IoT Edge

Quando a ponte funciona como um módulo IoT Edge num tempo de execução IoT Edge, o ficheiro de configuração é enviado da nuvem como uma atualização para a `PnpBridgeConfig` propriedade desejada. A ponte aguarda esta atualização da propriedade antes de configurar os adaptadores e componentes.

## <a name="build-and-run-the-bridge-on-an-iot-device-or-gateway"></a>Construa e corra a ponte em um dispositivo IoT ou gateway

| Plataforma | Suportado |
| :-----------: | :-----------: |
| Windows |  Sim |
| Linux | Sim |

### <a name="prerequisites"></a>Pré-requisitos

Para completar esta secção, tem de instalar o seguinte software na sua máquina local:

- Um ambiente de desenvolvimento que suporta a compilação de C++ como [Visual Studio (Comunidade, Profissional ou Enterprise)](https://visualstudio.microsoft.com/downloads/) - certifique-se de que inclui o componente de gestor de pacotes NuGet e o Desktop Development com carga de trabalho **C++** quando instalar o Visual Studio.
- [CMake](https://cmake.org/download/) - quando instalar o CMake, selecione a opção **Adicionar CMake ao sistema PATH**.
- Se estiver a construir no Windows, também precisa de descarregar o [Windows 17763 SDK](https://developer.microsoft.com/windows/downloads/windows-10-sdk).

### <a name="source-code"></a>Código de origem

Clone o repositório [de ponte IoT Plug e Play](https://github.com/Azure/iot-plug-and-play-bridge) para a sua máquina local:

```console
git clone https://github.com/Azure/iot-plug-and-play-bridge.git

cd iot-plug-and-play-bridge

git submodule update --init --recursive
```

Espere que o comando anterior desem poucos minutos para correr.

> [!NOTE]
> Se tiver problemas com a `git submodule update` falha no Windows, tente o seguinte comando para resolver o problema: `git config --system core.longpaths true` .

### <a name="build-the-bridge-on-windows"></a>Construa a ponte sobre janelas

Abra o pedido de comando do **desenvolvedor para VS 2019** e navegue para a pasta que contém o repositório que clonou e execute os seguintes comandos:

```console
cd pnpbridge\scripts\windows

build.cmd
```

Espere que o comando anterior desem poucos minutos para correr.

Opcionalmente, pode abrir o ficheiro de solução *de solução de pnpbridge\cmake\pnpbridge_x86\azure_iot_pnp_bridge.sln* gerado no Visual Studio para editar, reconstruir e depurar o código.

> [!TIP]
> Este projeto utiliza a CMAKE para gerar os ficheiros do projeto. Quaisquer modificações que faça ao projeto no Visual Studio poderão ser perdidas se os ficheiros CMAKE apropriados não forem atualizados.

### <a name="build-the-bridge-on-linux"></a>Construa a ponte em Linux

Utilizando a casca de bata, navegue para a pasta que contém o repositório que clonou e execute os seguintes comandos:

```bash
cd scripts/linux

./setup.sh

./build.sh
```

### <a name="configure-the-generic-sensors"></a>Configurar os sensores genéricos

Modificar os seguintes parâmetros sob o `pnp_bridge_connection_parameters` nó noconfig.js *no* ficheiro no ficheiro *iot-plug-and-play-bridge\pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\consola\pasta de consola* no Windows ou na *iot-plug-and-play-bridge/pnpbridge/cmake/pnpbridge_linux\src/pnpbridge/samples/folder* no Windows:

Se estiver a utilizar uma cadeia de ligação para ligar ao seu hub IoT:

```JSON
{
  "connection_parameters": {
    "connection_type" : "connection_string",
    "connection_string" : "dtmi:com:example:RootPnpBridgeSampleDevice;1",
    "root_interface_model_id": "[To fill in]",
    "auth_parameters": {
      "auth_type": "symmetric_key",
      "symmetric_key": "[To fill in]"
    }
  }
}
```

> [!TIP]
> O `symmetric_key` valor deve coincidir com a tecla SAS na cadeia de ligação.

Se estiver a utilizar dPS para ligar ao seu hub IoT ou à aplicação IoT Central:

```JSON
{
  "connection_parameters": {
    "connection_type" : "dps",
    "root_interface_model_id": "dtmi:com:example:RootPnpBridgeSampleDevice;1",
    "auth_parameters" : {
      "auth_type" : "symmetric_key",
      "symmetric_key" : "[DEVICE KEY]"
    },
    "dps_parameters" : {
      "global_prov_uri" : "[GLOBAL PROVISIONING URI] - typically it is global.azure-devices-provisioning.net",
      "id_scope": "[IoT Central / IoT Hub ID SCOPE]",
      "device_id": "[DEVICE ID]"
    }
  }
}
```

Reveja o resto do ficheiro de configuração para ver quais os componentes de interface e parâmetros globais configurados nesta amostra.

### <a name="start-the-bridge-in-windows"></a>Inicie a ponte em Windows

Inicie a ponte executando-a na origem do comando:

```console
cd iot-plug-and-play-bridge\pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console

Debug\pnpbridge_bin.exe
```

> [!TIP]
> Para utilizar um ficheiro de configuração a partir de um local diferente, passe o seu caminho como um parâmetro de linha de comando quando executar a ponte.
  
> [!TIP]
> Se tiver uma câmara incorporada ou uma câmara USB ligada ao seu PC, pode iniciar uma aplicação que utilize câmaras, como a aplicação **de Câmara** incorporada. Quando a aplicação **Da Câmara** está em funcionamento, a saída da consola de ponte mostra as estatísticas de monitorização e a taxa de fotogramas da interface digital twin para o seu hub IoT.

## <a name="build-and-run-the-bridge-as-an-iot-edge-module"></a>Construa e corra a ponte como um módulo IoT Edge

| Plataforma | Suportado |
| :-----------: | :-----------: |
| Windows |  Não |
| Linux | Sim |

### <a name="prerequisites"></a>Pré-requisitos

Para completar esta secção, você precisa de um hub Azure IoT de nível padrão ou gratuito. Para inclinar-se a criar um hub IoT, consulte [Criar um hub IoT.](../iot-hub/iot-hub-create-through-portal.md)

Os passos nesta secção pressupõem que tem o seguinte ambiente de desenvolvimento numa máquina Do Windows 10. Estas ferramentas permitem-lhe construir e implantar um módulo IoT Edge no seu dispositivo IoT Edge:

- Subsistema Windows para Linux (WSL) 2 em execução Ubuntu 18.04 LTS. Para saber mais, consulte o subsistema Windows para o Guia de [Instalação do Linux para o Windows 10](/windows/wsl/install-win10).
- Docker Desktop para Windows configurado para utilizar O WSL 2. Para saber mais, consulte [o backend WSL 2 do Docker Desktop](https://docs.docker.com/docker-for-windows/wsl/).
- [Código visual do estúdio instalado no seu ambiente Windows](https://code.visualstudio.com/docs/setup/windows) com as seguintes três extensões instaladas:

  - [Pacote de extensão de Desenvolvimento Remoto](https://aka.ms/vscode-remote/download/extension) - esta extensão permite-lhe construir e executar código em WSL 2.
  - [Docker for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker) - esta extensão deve ser ativada no ambiente **WSL: Ubuntu-18.04** do Código VS.
  - [Ferramentas Azure IoT para Código do Estúdio Visual](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) - esta extensão deve ser ativada no ambiente **WSL: Ubuntu-18.04** do Vs Code.

- Executar os seguintes comandos no seu ambiente WSL 2 para instalar as ferramentas de construção necessárias:

  ```bash
  sudo apt-get update
  sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
  ```

- O [CLI Azure](/cli/azure/install-azure-cli-apt) instalado no seu ambiente WSL 2 para gerir os seus recursos Azure.

  > [!TIP]
  > Se preferir, pode executar os `az` comandos na [Azure Cloud Shell](https://shell.azure.com/) onde o CLI está pré-instalado.

### <a name="create-an-iot-edge-device"></a>Criar um dispositivo IoT Edge

Os comandos aqui criam um dispositivo IoT Edge a funcionar numa máquina virtual Azure. Executar um dispositivo IoT Edge numa máquina virtual é útil para testar a sua implementação se não tiver acesso a um dispositivo real.

Para criar um registo de dispositivo IoT Edge no seu hub IoT, execute os seguintes comandos no seu ambiente WSL 2. Utilize o `az login` comando para iniciar seduca na sua subscrição Azure:

```azurecli
az iot hub device-identity create --device-id bridge-edge-device --edge-enabled true --hub-name {your IoT hub name}
```

Para criar uma máquina virtual Azure com o tempo de execução IoT Edge instalado, execute os seguintes comandos. Atualizar os espaços reservados com valores adequados:

```azurecli
az group create --name bridge-edge-resources --location eastus
az deployment group create \
--resource-group bridge-edge-resources \
--template-uri "https://aka.ms/iotedge-vm-deploy" \
--parameters dnsLabelPrefix='{unique DNS name for virtual machine}' \
--parameters adminUsername='{admin user name}' \
--parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id bridge-edge-device --hub-name {your IoT hub name} -o tsv) \
--parameters authenticationType='password' \
--parameters adminPasswordOrKey="{admin password for virtual machine}"
```

Tem agora o tempo de funcionamento do IoT Edge a funcionar numa máquina virtual. Pode utilizar o seguinte comando para verificar se os **$edgeAgent** e **$edgeHub** estão em funcionamento no dispositivo:

```azurecli
az iot hub module-identity list --device-id bridge-edge-device -o table --hub-name {your IoT hub name}
```

> [!CAUTION]
> És cobrado enquanto esta máquina virtual está a funcionar. Certifique-se de desligá-lo quando não estiver a usá-lo e removê-lo quando terminar.

### <a name="download-the-source-code"></a>Descarregue o código fonte

Nos passos seguintes, constrói a imagem do Docker no seu ambiente WSL 2. Para clonar o repositório **iot-plug-and-play-bridge,** executar os seguintes comandos numa casca de bata WSL 2 numa pasta adequada:

```bash
git clone https://github.com/Azure/iot-plug-and-play-bridge.git

cd iot-plug-and-play-bridge

git submodule update --init --recursive
```

Espere que o comando anterior desem poucos minutos para correr.

### <a name="update-the-dockerfile"></a>Atualize o *Dockerfile*

Lançar código VS, abrir a paleta de comando, introduzir *A pasta Remote WSL: Abrir a pasta em WSL* e, em seguida, abrir a pasta *iot-plug-and-play-bridge* que contém o repositório clonado.

Abra o ficheiro *pnpbridge\Dockerfile.amd64.* Editar as definições variáveis ambientais da seguinte forma:

```dockerfile
ENV PNP_BRIDGE_ROOT_MODEL_ID="dtmi:com:example:RootPnpBridgeSampleDevice;1"
ENV PNP_BRIDGE_HUB_TRACING_ENABLED="false"
ENV IOTEDGE_WORKLOADURI="something"
```

> [!TIP]
> Pode utilizar o seguinte comando para recuperar a cadeia de ligação do dispositivo: `az iot hub device-identity connection-string show --device-id bridge-edge-device --hub-name {your IoT hub name}`

Há amostras *de Dockerfiles* para outras arquiteturas.

### <a name="build-the-iot-plug-and-play-bridge-module-image"></a>Construa a imagem do módulo de ponte IoT Plug e Play

Em CÓDIGO VS, abra a paleta de comando, *introduza registos docker: Faça login no Docker CLI,* selecione a subscrição do Azure e o registo do contentor. Este comando permite ao Docker ligar-se ao registo do seu contentor e carregar a imagem do módulo.

Abra o *pnpbridge/module.jsno* ficheiro. Adicione `{your container registry name}.azurecr.io/iotpnpbridge` como repositório de imagem do recipiente, e `v1` como a versão.

No Código VS, clique com o botão direito no *ficheiro pnpbridge/module.jsna* vista **Explorer,** selecione **Build and Push IoT Edge Module Image**, e selecione **amd64** como plataforma.

No Código VS, na vista **Docker,** pode navegar no conteúdo do seu registo de contentores que agora inclui a imagem do módulo **iotpnpbridge:V1-amd64.**

### <a name="create-a-container-registry"></a>Criar um registo de contentor

Um dispositivo IoT Edge descarrega as imagens do módulo a partir de um registo de contentores. Este exemplo utiliza um registo de contentores Azure.

Criar um registo de contentores Azure no grupo de recursos **de ponta da ponte.** Em seguida, ative o acesso de administrador ao seu registo de contentores e obtenha as credenciais de que o seu dispositivo IoT Edge necessita para descarregar as imagens do módulo:

```azurecli
az acr create -g bridge-edge-resources --sku Basic -n {your container registry name}
az acr update --admin-enabled true -n {your container registry name}
az acr credential show -n {your container registry name}
```

### <a name="create-the-deployment-manifest"></a>Criar o manifesto de implantação

Um manifesto de implantação IoT Edge especifica os módulos e valores de configuração para implantar num dispositivo IoT Edge.

Em Código VS, abra o *pnpbridge/deployment.template.jsem* arquivo:

- Atualize os `registryCredentials` valores do comando anterior. O `address` valor `{your container registry name}.azurecr.io` parece.
- Atualize o `image` valor para o `ModulePnpBridge` . A imagem do módulo parece: `{your container registry}.azurecr.io/iotpnpbridge:v1-amd64` .
- Substitua o `PnPBridgeConfig` seguinte JSON para configurar o adaptador de deteção de CO2:

  ```json
  "PnpBridgeConfig": {
    "pnp_bridge_interface_components": [
      {
        "_comment": "Component 1 - Modbus Device",
        "pnp_bridge_component_name": "Co2Detector1",
        "pnp_bridge_adapter_id": "modbus-pnp-interface",
        "pnp_bridge_adapter_config": {
          "unit_id": 1,
          "tcp": {
            "host": "10.159.29.2",
            "port": 502
          },
          "modbus_identity": "DL679"
        }
      }
    ],
    "pnp_bridge_adapter_global_configs": {
      "modbus-pnp-interface": {
        "DL679": {
          "telemetry": {
            "co2": {
              "startAddress": "40001",
              "length": 1,
              "dataType": "integer",
              "defaultFrequency": 5000,
              "conversionCoefficient": 1
            },
            "temperature": {
              "startAddress": "40003",
              "length": 1,
              "dataType": "decimal",
              "defaultFrequency": 5000,
              "conversionCoefficient": 0.01
            }
          },
          "properties": {
            "firmwareVersion": {
              "startAddress": "40482",
              "length": 1,
              "dataType": "hexstring",
              "defaultFrequency": 60000,
              "conversionCoefficient": 1,
              "access": 1
            },
            "modelName": {
              "startAddress": "40483",
              "length": 2,
              "dataType": "string",
              "defaultFrequency": 60000,
              "conversionCoefficient": 1,
              "access": 1
            },
            "alarmStatus_co2": {
              "startAddress": "00305",
              "length": 1,
              "dataType": "boolean",
              "defaultFrequency": 1000,
              "conversionCoefficient": 1,
              "access": 1
            },
            "alarmThreshold_co2": {
              "startAddress": "40225",
              "length": 1,
              "dataType": "integer",
              "defaultFrequency": 30000,
              "conversionCoefficient": 1,
              "access": 2
            }
          },
          "commands": {
            "clearAlarm_co2": {
              "startAddress": "00305",
              "length": 1,
              "dataType": "flag",
              "conversionCoefficient": 1
            }
          }
        }
      }
    }
  }
  ```

  Guarde as alterações.

No Código VS, clique à direita no *pnpbridge/deployment.template.jsno* ficheiro na vista **Explorer,** selecione **Generate IoT Edge Deployment Manifest**. Este comando gera o *pnpbridge/config/deployment.amd64.jsno* manifesto de implantação.

### <a name="deploy-the-bridge-to-your-iot-edge-device"></a>Coloque a ponte no seu dispositivo IoT Edge

Em CÓDIGO VS, abra a paleta de comando, insira *O Hub Azure IoT: Selecione IoT Hub,* selecione a sua subscrição e o hub IoT.

No Código VS, clique com o botão direito para o *pnpbridge/config/deployment.amd64.jsno* ficheiro na vista **Explorer,** selecione **Criar Implementação para Dispositivo Único** e selecione o dispositivo de borda de **ponte**.

Para visualizar o estado dos módulos no seu dispositivo, execute o seguinte comando:

```azurecli
az iot hub module-identity list --device-id bridge-edge-device -o table --hub-name {your IoT hub name}
```

A lista de módulos em execução inclui agora o módulo **ModulePnpBridge** que está configurado para utilizar o adaptador de deteção de CO2.

### <a name="tidy-up"></a>Arrumar tudo

Para remover a máquina virtual e o registo do contentor da sua assinatura Azure, execute o seguinte comando:

```azurecli
az group delete -n bridge-edge-resources
```

## <a name="folder-structure"></a>Estrutura de pasta

*pnpbridge\deps\azure-iot-sdk-c-pnp*: Submodulos de Git que contêm o Dispositivo Azure IoT SDK para C SDK.

*pnpbridge\scripts*: Construa scripts.

*pnpbridge\src*: Código de origem para ioT Plug e núcleo de ponte de reprodução.

*pnpbridge\src\adaptadores*: Código fonte para vários adaptadores de ponte IoT Plug e Play.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a ponte IoT Plug and Play, visite o repositório [IoT Plug e Play Bridge](https://github.com/Azure/iot-plug-and-play-bridge) GitHub.