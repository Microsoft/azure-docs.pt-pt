---
ms.openlocfilehash: 0b0d2ead4a10d037b09c2a532eb21372ffd0cb82
ms.sourcegitcommit: e729629331ae10097a081a03029398525f4147a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/25/2019
ms.locfileid: "64511092"
---
Para referenciar os enlaces de padrão de 2.x as funções do Azure, abra a *Host. JSON* conteúdo do ficheiro e de atualização de acordo com o código a seguir.

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```