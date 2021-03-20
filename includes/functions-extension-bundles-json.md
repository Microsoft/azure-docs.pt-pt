---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: b67e2bf2ae5af2feb334e898ce69fd5b959c7cf0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
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