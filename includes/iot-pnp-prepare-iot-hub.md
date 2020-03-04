---
author: baanders
ms.author: baanders
ms.service: iot-pnp
ms.topic: include
ms.date: 10/24/2019
ms.openlocfilehash: 3f3e7c54ec59c42ed102da682fc9aa077709ef3f
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78262595"
---
## <a name="prepare-an-iot-hub"></a>Prepare um hub ioT

Você também precisa de um hub Azure IoT na sua subscrição Azure para completar este quickstart. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. Se não tiver um hub IoT, siga [estas instruções para criar um](../articles/iot-hub/iot-hub-create-using-cli.md).

> [!IMPORTANT]
> Durante a pré-visualização pública, as funcionalidades IoT Plug and Play só estão disponíveis nos centros IoT criados nas regiões **Centro dos EUA**, Norte da **Europa**e **Japão.**

Se estiver a utilizar o Azure CLI localmente, insera-se pela primeira vez na subscrição do Azure usando `az login`. Se estiver a comandar estes comandos na Casca de Nuvem Azure, está automaticamente inscrito.

Se estiver a utilizar o Azure CLI localmente, a versão `az` deve ser **2.0.73** ou mais tarde; o Azure Cloud Shell utiliza a versão mais recente. Utilize o comando `az --version` para verificar a versão instalada na sua máquina.

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