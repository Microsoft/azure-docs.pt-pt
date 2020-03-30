---
author: baanders
ms.author: baanders
ms.service: iot-pnp
ms.topic: include
ms.date: 10/24/2019
ms.openlocfilehash: a3340eb9e53afa83c35109bad7d22f81413dd644
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "80234152"
---
## <a name="prepare-an-iot-hub"></a>Prepare um hub ioT

Você também precisa de um hub Azure IoT na sua subscrição Azure para completar este quickstart. Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. Se não tiver um hub IoT, siga [estas instruções para criar um](../articles/iot-hub/iot-hub-create-using-cli.md).

Se estiver a utilizar o Azure CLI localmente, insera-se primeiro na subscrição do Azure utilizando `az login`. Se estiver a comandar estes comandos na Casca de Nuvem Azure, está automaticamente inscrito.

Se estiver a utilizar o Azure `az` CLI localmente, a versão deve ser **2.0.73** ou mais tarde; o Azure Cloud Shell utiliza a versão mais recente. Utilize `az --version` o comando para verificar a versão instalada na sua máquina.

Execute o seguinte comando para adicionar a extensão Microsoft Azure IoT para O CLI Azure à sua instância:

```azurecli-interactive
az extension add --name azure-iot
```

Execute o seguinte comando para criar a identidade do dispositivo no seu hub IoT. Substitua os espaços reservados **YourIoTHubName** e **YourDeviceID** pelo seu próprio _nome IoT Hub_ e um id de _dispositivo_ à sua escolha.

```azurecli-interactive
az iot hub device-identity create --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

Executar o seguinte comando para obter a _cadeia de ligação_ do dispositivo para o dispositivo que acabou de registar (nota para utilização posterior):

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```
