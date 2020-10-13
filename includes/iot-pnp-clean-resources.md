---
author: dominicbetts
ms.author: dominicbetts
ms.service: iot-pnp
ms.topic: include
ms.date: 11/15/2019
ms.openlocfilehash: 6cd983b6421fc821853aa22d4dc2c297f672d292
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87336905"
---
## <a name="clean-up-resources"></a>Limpar recursos

Se pretender continuar com artigos adicionais IoT Plug e Play, pode conservar e reutilizar os recursos utilizados neste artigo. Caso contrário, pode eliminar os recursos que criou neste artigo para evitar custos adicionais.

Pode eliminar o hub e o dispositivo registado de uma só vez, eliminando todo o grupo de recursos com o seguinte comando Azure CLI. Não utilize este comando se estes recursos estiverem a partilhar um grupo de recursos com outros recursos que pretende manter.

```azurecli-interactive
az group delete --name <YourResourceGroupName>
```

Para eliminar apenas o hub IoT, executar o seguinte comando utilizando Azure CLI:

```azurecli-interactive
az iot hub delete --name <YourIoTHubName>
```

Para eliminar apenas a identidade do dispositivo que registou no seu hub IoT, executar o seguinte comando utilizando Azure CLI:

```azurecli-interactive
az iot hub device-identity delete --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

Também pode querer remover os ficheiros de amostra clonados da sua máquina de desenvolvimento.
