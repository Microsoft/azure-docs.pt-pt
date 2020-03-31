---
author: baanders
ms.author: baanders
ms.service: iot-pnp
ms.topic: include
ms.date: 11/15/2019
ms.openlocfilehash: d88d166e001e71cabdabc3a3d344adc4da19aa8c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "75453722"
---
## <a name="clean-up-resources"></a>Limpar recursos

Se planeia continuar com artigos adicionais ioT Plug e Play, pode guardar e reutilizar os recursos utilizados neste arranque rápido. Caso contrário, pode eliminar os recursos que criou neste arranque rápido para evitar encargos adicionais.

Pode eliminar o hub e o dispositivo registado de uma só vez, eliminando todo o grupo de recursos com o seguinte comando para o Azure CLI. (Não use isto, no entanto, se estes recursos estão partilhando um grupo de recursos com outros recursos que você tem para diferentes propósitos.)

```azurecli-interactive
az group delete --name <YourResourceGroupName>
```
Para eliminar apenas o hub IoT, execute o seguinte comando utilizando o Azure CLI:

```azurecli-interactive
az iot hub delete --name <YourIoTHubName>
```

Para eliminar apenas a identidade do dispositivo registada no seu hub IoT, execute o seguinte comando utilizando o Azure CLI:

```azurecli-interactive
az iot hub device-identity delete --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

Também pode querer remover os ficheiros de amostraclonados da sua máquina de desenvolvimento.