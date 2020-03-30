---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 10/07/2019
ms.author: orspodek
ms.openlocfilehash: 3cee966f4d7851415e5ae17faf4f3fafce434cae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "72031555"
---
## <a name="clean-up-resources"></a>Limpar recursos

Para eliminar a ligação de dados, utilize o seguinte comando:

```csharp
kustoManagementClient.DataConnections.Delete(resourceGroupName, clusterName, databaseName, dataConnectionName);
```