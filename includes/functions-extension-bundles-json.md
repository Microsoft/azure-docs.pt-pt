---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: f47a543143c949715fe2a49adccf074759a346fa
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
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

As propriedades seguintes estão disponíveis em `extensionBundle`:

| Propriedade | Descrição |
| -------- | ----------- |
| ID | O espaço de nomes para os pacotes de extensões das Funções do Microsoft Azure. |
| versão | A versão do pacote a instalar. O runtime das Funções escolhe sempre a versão máxima permitida definida pelo intervalo de versões. O valor da versão acima permite todas as versões de pacote a partir da 1.0.0, mas não inclui a 2.0.0. Para obter mais informações, veja a [notação de intervalo para especificar intervalos de versões](/nuget/reference/package-versioning#version-ranges). |

As versões de pacote incrementam à medida que os pacotes mudam. As alterações às versões principais ocorrem quando os pacotes incrementam por versão principal. As alterações às versões principais no pacote geralmente coincidem com uma alteração na versão principal do runtime das Funções.  

O conjunto atual de extensões instaladas pelo pacote predefinido está enumerado neste [ficheiro .json](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json).
