---
author: baanders
ms.author: baanders
ms.service: iot-pnp
ms.topic: include
ms.date: 11/15/2019
ms.openlocfilehash: e6ca830a7224326c17003924b76192b95ba6bcb2
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152003"
---
## <a name="clean-up-resources"></a>Limpar recursos

Se você planeja continuar com artigos adicionais de Plug and Play IoT, poderá manter e reutilizar os recursos usados neste guia de início rápido. Caso contrário, você pode excluir os recursos criados neste guia de início rápido para evitar encargos adicionais.

Você pode excluir o Hub e o dispositivo registrado ao mesmo tempo excluindo o grupo de recursos inteiro com o comando a seguir para CLI do Azure. (No entanto, não use isso se esses recursos estiverem compartilhando um grupo de recursos com outros recursos que você tem para finalidades diferentes.)

```azurecli-interactive
az group delete --name <YourResourceGroupName>
```
Para excluir apenas o Hub IoT, execute o seguinte comando usando CLI do Azure:

```azurecli-interactive
az iot hub delete --name <YourIoTHubName>
```

Para excluir apenas a identidade do dispositivo que você registrou com o Hub IoT, execute o seguinte comando usando CLI do Azure:

```azurecli-interactive
az iot hub device-identity delete --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

Talvez você também queira remover os arquivos do SDK clonados do seu computador de desenvolvimento.