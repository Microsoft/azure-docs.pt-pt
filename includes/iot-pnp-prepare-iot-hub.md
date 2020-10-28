---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 03/17/2020
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 3ac72360db5fb3d075b4caa3208f372f22441c7b
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92755454"
---
## <a name="prepare-an-iot-hub"></a>Prepare um hub IoT

Você precisa de um hub Azure IoT na sua assinatura Azure para completar os passos neste artigo. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Se estiver a utilizar o Azure CLI localmente, inscreva-se pela primeira vez na sua assinatura Azure utilizando `az login` . Se estiver a executar estes comandos na Azure Cloud Shell, está a fazer o seu contrato automaticamente.

Se estiver a utilizar o Azure CLI localmente, a `az` versão deve ser **2.8.0** ou mais tarde; o Azure Cloud Shell utiliza a versão mais recente. Utilize o `az --version` comando para verificar a versão instalada na sua máquina.

Executar o seguinte comando para adicionar a extensão IoT do Microsoft Azure para Azure CLI ao seu exemplo:

```azurecli-interactive
az extension add --name azure-iot
```

Se ainda não tiver um hub IoT para utilizar, execute os seguintes comandos para criar um grupo de recursos e um hub IoT de nível livre na sua subscrição. Substitua `<YourIoTHubName>` por um nome central à sua escolha:

```azurecli-interactive
az group create --name my-pnp-resourcegroup \
    --location centralus
az iot hub create --name <YourIoTHubName> \
    --resource-group my-pnp-resourcegroup --sku F1
```

> [!NOTE]
> O IoT Plug and Play está atualmente disponível em hubs IoT criados nas regiões centro-americana, norte da Europa e Japão Oriental. O suporte IoT Plug e Play não está incluído em centros IoT de nível básico.

Executar o seguinte comando para criar a identidade do dispositivo no seu hub IoT. Substitua os `<YourIoTHubName>` espaços reservados e `<YourDeviceID>` os espaços reservados pelo seu próprio nome _IoT Hub_ e um _ID_ do dispositivo à sua escolha.

```azurecli-interactive
az iot hub device-identity create --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```
