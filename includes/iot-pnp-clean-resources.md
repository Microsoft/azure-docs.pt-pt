---
author: dominicbetts
ms.author: dominicbetts
ms.service: iot-pnp
ms.topic: include
ms.date: 11/15/2019
ms.openlocfilehash: 647226091c3e15a19bf8262c23e62d95018488b3
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2021
ms.locfileid: "99831212"
---
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
