---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: 4ce048cafa4c20e3bbdbd8ecf1669748531ee122
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2019
ms.locfileid: "74129058"
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

As propriedades a seguir estão disponíveis no `extensionBundle`:

| Propriedade | Descrição |
| -------- | ----------- |
| ID | O namespace para pacotes de extensão do Microsoft Azure functions. |
| versão | A versão do pacote a ser instalado. O tempo de execução do Functions sempre escolhe a versão máxima permitida definida pelo intervalo de versão ou intervalo. O valor da versão acima permite todas as versões do pacote da 1.0.0 até, mas não incluindo 2.0.0. Para obter mais informações, consulte a [notação de intervalo para especificar intervalos de versão](/nuget/reference/package-versioning#version-ranges-and-wildcards). |

As versões do pacote são incrementadas como pacotes na alteração do pacote. As alterações de versão principal ocorrem quando os pacotes no pacote são incrementados por uma versão principal. As alterações de versão principais no pacote geralmente coincidem com uma alteração na versão principal do tempo de execução do functions.  

O conjunto atual de extensões instaladas pelo pacote padrão é enumerado neste [arquivo Extensions. JSON](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json).
