---
author: baanders
ms.author: baanders
ms.service: iot-pnp
ms.topic: include
ms.date: 10/24/2019
ms.openlocfilehash: 6b6c481a7443953a70fe3db45121f5f9982d8710
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152011"
---
## <a name="prepare-an-iot-hub"></a>Preparar um hub IoT

Você também precisa de um hub IoT do Azure em sua assinatura do Azure para concluir este guia de início rápido. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. Se você não tiver um hub IoT, siga [estas instruções para criar um](../articles/iot-hub/iot-hub-create-using-cli.md).

> [!IMPORTANT]
> Durante a visualização pública, os recursos de Plug and Play de IoT só estão disponíveis em hubs IoT criados nas regiões **EUA Central**, **Europa setentrional**e **leste do Japão** .

Execute o comando a seguir para adicionar a extensão de IoT Microsoft Azure para CLI do Azure à sua instância de Cloud Shell:

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Execute o comando a seguir para criar a identidade do dispositivo em seu hub IoT. Substitua os espaços reservados **nomedoseuhubiot** e **iddoseudispositivo** por seu próprio _nome de Hub IOT_ e uma _ID de dispositivo_ de sua escolha.

```azurecli-interactive
az iot hub device-identity create --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

Execute o comando a seguir para obter a _cadeia de conexão do dispositivo_ para o dispositivo que você acabou de registrar (observação para uso posterior):

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDevice> --output table
```

Execute o seguinte comando para obter a _cadeia de conexão do Hub IOT_ para o Hub (observação para uso posterior):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```