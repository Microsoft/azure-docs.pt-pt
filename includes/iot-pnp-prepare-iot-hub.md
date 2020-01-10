---
author: baanders
ms.author: baanders
ms.service: iot-pnp
ms.topic: include
ms.date: 10/24/2019
ms.openlocfilehash: 7a0ea2330e682e1e60eca6540151b0f5f620571c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453727"
---
## <a name="prepare-an-iot-hub"></a>Preparar um hub IoT

Você também precisa de um hub IoT do Azure em sua assinatura do Azure para concluir este guia de início rápido. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. Se você não tiver um hub IoT, siga [estas instruções para criar um](../articles/iot-hub/iot-hub-create-using-cli.md).

> [!IMPORTANT]
> Durante a visualização pública, os recursos de Plug and Play de IoT só estão disponíveis em hubs IoT criados nas regiões **EUA Central**, **Europa setentrional**e **leste do Japão** .

Se você estiver usando o CLI do Azure localmente, primeiro entre na sua assinatura do Azure usando `az login`. Se você estiver executando esses comandos no Azure Cloud Shell, você estará conectado automaticamente.

Se você estiver usando o CLI do Azure localmente, a versão `az` deverá ser **2.0.73** ou posterior; o Azure Cloud Shell usa a versão mais recente. Use o comando `az --version` para verificar a versão instalada em seu computador.

Execute o comando a seguir para adicionar a extensão Microsoft Azure IoT para CLI do Azure à sua instância:

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Execute o comando a seguir para criar a identidade do dispositivo em seu hub IoT. Substitua os espaços reservados **nomedoseuhubiot** e **iddoseudispositivo** por seu próprio _nome de Hub IOT_ e uma _ID de dispositivo_ de sua escolha.

```azurecli-interactive
az iot hub device-identity create --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

Execute o comando a seguir para obter a _cadeia de conexão do dispositivo_ para o dispositivo que você acabou de registrar (observação para uso posterior):

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```