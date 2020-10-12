---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: b67e2bf2ae5af2feb334e898ce69fd5b959c7cf0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88689570"
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