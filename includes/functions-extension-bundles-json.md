---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: f47a543143c949715fe2a49adccf074759a346fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79382099"
---
```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```

As seguintes propriedades `extensionBundle`estão disponíveis em:

| Propriedade | Descrição |
| -------- | ----------- |
| ID | O espaço de nome para os pacotes de extensão das funções Microsoft Azure. |
| versão | A versão do pacote para instalar. O tempo de funcionamento das Funções escolhe sempre a versão máxima admissível definida pelo intervalo ou intervalo da versão. O valor da versão acima permite todas as versões de bundle de 1.0.0 até, mas não incluindo 2.0.0. Para mais informações, consulte a notação de [intervalo para especificar intervalos](/nuget/reference/package-versioning#version-ranges)de versão . |

As versões do bundle incrementam à medida que os pacotes na mudança de pacote. As principais alterações de versão ocorrem quando os pacotes no incremento do pacote por uma versão importante. As principais alterações de versão no pacote geralmente coincidem com uma mudança na versão principal do tempo de execução das Funções.  

O conjunto atual de extensões instalados pelo pacote predefinido é enumerado neste [ficheiro extensions.json](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json).
