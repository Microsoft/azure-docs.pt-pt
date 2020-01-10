---
author: baanders
ms.author: baanders
ms.service: iot-pnp
ms.topic: include
ms.date: 11/15/2019
ms.openlocfilehash: d88d166e001e71cabdabc3a3d344adc4da19aa8c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453722"
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

Talvez você também queira remover os arquivos de exemplo clonados de seu computador de desenvolvimento.