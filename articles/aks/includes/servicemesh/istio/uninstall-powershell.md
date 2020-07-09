---
author: paulbouwer
ms.topic: include
ms.date: 11/15/2019
ms.author: pabouwer
ms.openlocfilehash: 4bd2af67ed0e570e8d171da678b69d0908627374
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77594033"
---
Para eliminar os segredos, executar o seguinte comando:

```powershell
(kubectl get secret --all-namespaces -o json | ConvertFrom-Json).items.metadata |% { if ($_.name -match "istio.") { "Deleting {0}.{1}" -f $_.namespace, $_.name; kubectl delete secret -n $_.namespace $_.name } }
```
