---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: f64892193eb6cfcce8f948b54e5557b5fa3d90ab
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80878255"
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

As seguintes propriedades estão disponíveis `extensionBundle` em:

| Propriedade | Descrição |
| -------- | ----------- |
| ID | O espaço de nome para pacotes de extensão Microsoft Azure Functions. |
| versão | A versão do pacote para instalar. O tempo de execução das Funções escolhe sempre a versão máxima admissível definida pela gama ou intervalo da versão. O valor da versão acima permite todas as versões do pacote de 1.0.0 até, mas não incluindo 2.0.0. Para obter mais informações, consulte a [notação de intervalo para especificar as gamas de versões](/nuget/reference/package-versioning#version-ranges). |

As versões de pacote incrementam como pacotes na mudança do pacote. As principais mudanças de versão ocorrem quando os pacotes no incremento do pacote por uma versão principal. As principais mudanças de versão no pacote geralmente coincidem com uma mudança na versão principal do tempo de execução das Funções.  

O conjunto atual de extensões instaladas pelo pacote predefinido é enumerado neste [extensions.jsem ficheiro](https://github.com/Azure/azure-functions-extension-bundles/blob/dev/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json).
