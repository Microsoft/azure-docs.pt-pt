---
title: Quickstart - Configurar os recursos IoT de que precisa para ioT Plug e Play | Microsoft Docs
description: Quickstart - Crie uma instância de Serviço de Provisionamento de Hub ioT e dispositivo para usar com os quickstarts e tutoriais IoT Plug e Play.
author: dominicbetts
ms.author: dobett
ms.date: 08/11/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: b2b17cffb8abd748315c437f46000995c4cb598a
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2021
ms.locfileid: "99831914"
---
# <a name="quickstart---set-up-your-environment-for-the-iot-plug-and-play-quickstarts-and-tutorials"></a>Quickstart - Crie o seu ambiente para o IoT Plug e jogue quickstarts e tutoriais

Antes de completar qualquer um dos quickstarts e tutoriais IoT Plug and Play, tem de configurar um hub IoT e o Serviço de Provisionamento de Dispositivos (DPS) na sua subscrição Azure. Também necessitará de cópias locais dos ficheiros-modelo utilizados pelas aplicações da amostra e pela ferramenta exploradora Azure IoT.

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Para evitar a necessidade de instalar o Azure CLI localmente, pode utilizar o Azure Cloud Shell para configurar os serviços na nuvem.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-resources"></a>Criar os recursos

Criar um grupo de recursos Azure para os recursos:

```azurecli-interactive
az group create --name my-pnp-resourcegroup --location centralus
```

Criar um hub IoT. O seguinte comando usa o nome `my-pnp-hub` como um exemplo para o nome do hub IoT para criar. Escolha um nome único para o seu hub IoT para usar no lugar `my-pnp-hub` de:

```azurecli-interactive
az iot hub create --name my-pnp-hub --resource-group my-pnp-resourcegroup --sku F1 --partition-count 2
```

Criar uma instância DPS. O seguinte comando usa o nome `my-pnp-dps` como exemplo para o nome da instância DPS para criar. Escolha um nome único para a sua instância DPS a utilizar no lugar `my-pnp-dps` de:

```azurecli-interactive
az iot dps create --name my-pnp-dps --resource-group my-pnp-resourcegroup
```

Para ligar a instância DPS ao seu hub IoT, utilize os seguintes comandos. Substitua `my-pnp-dps` e `my-pnp-hub` pelos nomes únicos que escolheu anteriormente:

```azurecli-interactive
hubConnectionString=$(az iot hub connection-string show -n my-pnp-hub --key primary --query connectionString -o tsv)
az iot dps linked-hub create --dps-name my-pnp-dps --resource-group my-pnp-resourcegroup --location centralus --connection-string $hubConnectionString
```

## <a name="retrieve-the-settings"></a>Recupere as definições

Alguns quickstarts e tutoriais usam a cadeia de ligação para o seu hub IoT. Também precisa da cadeia de ligação quando configurar a ferramenta exploradora Azure IoT. Recupere a corda de ligação e tome nota agora. `my-pnp-hub`Substitua-o pelo nome único que escolheu para o seu hub IoT:

```azurecli-interactive
az iot hub connection-string show -n my-pnp-hub --key primary --query connectionString
```

A maioria dos quickstarts e tutoriais usam o âmbito de *identificação* da sua configuração DPS. Recupere o âmbito de identificação e tome nota agora. `my-pnp-dps`Substitua-o pelo nome único que escolheu para a sua instância DPS:

```azurecli-interactive
az iot dps show --name my-pnp-dps --query properties.idScope
```

Todos os quickstarts e tutoriais usam uma inscrição de dispositivo DPS. Utilize o seguinte comando para criar uma `my-pnp-device` *inscrição individual* de dispositivo na sua instância DPS. `my-pnp-dps`Substitua-o pelo nome único que escolheu para a sua instância DPS. Tome nota do ID de registo e dos valores-chave primários a utilizar nos quickstarts e tutoriais:

```azurecli-interactive
az iot dps enrollment create --attestation-type symmetrickey --dps-name my-pnp-dps --resource-group my-pnp-resourcegroup --enrollment-id my-pnp-device --device-id my-pnp-device --query '{registrationID:registrationId,primaryKey:attestation.symmetricKey.primaryKey}'
```

## <a name="create-environment-variables"></a>Criar variáveis de ambiente

Crie cinco variáveis ambientais para configurar as amostras nos quickstarts e tutoriais para utilizar o Serviço de Provisionamento de Dispositivos (DPS) para ligar ao seu hub IoT:

* **IOTHUB_DEVICE_SECURITY_TYPE:** o `DPS` valor.
* **IOTHUB_DEVICE_DPS_ID_SCOPE:** o âmbito de identificação do DPS que fez anteriormente.
* **IOTHUB_DEVICE_DPS_DEVICE_ID:** o `my-pnp-device` valor.
* **IOTHUB_DEVICE_DPS_DEVICE_KEY:** a chave primária de inscrição que fez anteriormente.
* **IOTHUB_DEVICE_DPS_ENDPOINT:** o valor `global.azure-devices-provisioning.net`

As amostras de serviço necessitam das seguintes variáveis ambientais para identificar o centro e o dispositivo para se conectarem a:

* **IOTHUB_CONNECTION_STRING**: a cadeia de ligação do hub IoT que fez uma nota anterior.
* **IOTHUB_DEVICE_ID:** `my-pnp-device` .

Por exemplo, numa casca de bash Linux:

```bash
export IOTHUB_DEVICE_SECURITY_TYPE="DPS"
export IOTHUB_DEVICE_DPS_ID_SCOPE="<Your ID scope>"
export IOTHUB_DEVICE_DPS_DEVICE_ID="my-pnp-device"
export IOTHUB_DEVICE_DPS_DEVICE_KEY="<Your enrolment primary key>"
export IOTHUB_DEVICE_DPS_ENDPOINT="global.azure-devices-provisioning.net"
export IOTHUB_CONNECTION_STRING="<Your IoT hub connection string>"
export IOTHUB_DEVICE_ID="my-pnp-device"
```

Por exemplo, na linha de comando do Windows:

```cmd
set IOTHUB_DEVICE_SECURITY_TYPE=DPS
set IOTHUB_DEVICE_DPS_ID_SCOPE=<Your ID scope>
set IOTHUB_DEVICE_DPS_DEVICE_ID=my-pnp-device
set IOTHUB_DEVICE_DPS_DEVICE_KEY=<Your enrolment primary key>
set IOTHUB_DEVICE_DPS_ENDPOINT=global.azure-devices-provisioning.net
set IOTHUB_CONNECTION_STRING=<Your IoT hub connection string>
set IOTHUB_DEVICE_ID=my-pnp-device
```

## <a name="download-the-model-files"></a>Descarregue os ficheiros de modelos

Os quickstarts e os tutoriais utilizam ficheiros de modelos de amostra para o controlador de temperatura e dispositivos do termóstato. Para descarregar os ficheiros dos modelos de amostra:

1. Crie uma pasta chamada *modelos* na sua máquina local.

1. Clique [TemperatureController.js](https://raw.githubusercontent.com/Azure/opendigitaltwins-dtdl/master/DTDL/v2/samples/TemperatureController.json) à direita e guarde o ficheiro JSON na pasta dos *modelos.*

1. Clique [Thermostat.js](https://raw.githubusercontent.com/Azure/opendigitaltwins-dtdl/master/DTDL/v2/samples/Thermostat.json) à direita e guarde o ficheiro JSON na pasta dos *modelos.*

## <a name="install-the-azure-iot-explorer"></a>Instale o explorador Azure IoT

Os quickstarts e tutoriais usam a ferramenta **exploradora Azure IoT.** Vá ao [Azure IoT explorer lança](https://github.com/Azure/azure-iot-explorer/releases) e expanda a lista de ativos para o lançamento mais recente. Faça o download e instale a versão mais recente da aplicação para o seu sistema operativo.

A primeira vez que executar a ferramenta, é solicitado para a cadeia de ligação do hub IoT. Utilize a cadeia de ligação que fez anteriormente.

Configure a ferramenta para utilizar os ficheiros de modelo que descarregou anteriormente. A partir da página inicial da ferramenta, selecione **IoT Plug e Play Settings**, em seguida **, + Adicione > pasta Local**. Selecione a pasta *de modelos* que criou anteriormente. Em seguida, **selecione Guardar** para guardar as definições.

Para saber mais, consulte [instalar e utilizar o explorador Azure IoT](howto-use-iot-explorer.md).

## <a name="clean-up-resources"></a>Limpar os recursos

Você pode usar o hub IoT e a instância DPS para todos os quickstarts e tutoriais IoT Plug e Play, por isso só precisa de completar os passos deste artigo uma vez. Quando terminar, pode removê-las da sua subscrição com o seguinte comando:

```azurecli-interactive
az group delete --name my-pnp-resourcegroup
```

## <a name="next-steps"></a>Passos seguintes

Agora que montou o seu ambiente, pode experimentar um dos rápidos ou tutoriais como:

> [!div class="nextstepaction"]
> [Ligue uma aplicação de dispositivo IoT Plug e Play de amostra ao IoT Hub](quickstart-connect-device.md)
