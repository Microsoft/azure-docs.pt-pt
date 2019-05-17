---
title: Desenvolvimento em equipe com espaços de desenvolvimento do Azure com .NET Core e o VS Code
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/09/2018
ms.topic: tutorial
description: Desenvolvimento rápido da Kubernetes com contentores e microsserviços no Azure
keywords: 'Docker, o Kubernetes, o Azure, o AKS, o serviço Kubernetes do Azure, contentores, Helm, a malha de serviço, roteamento de malha do serviço, kubectl, k8s '
ms.openlocfilehash: 608f95234af68bad0b3c961baf61d3476d2126ea
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65765154"
---
[!INCLUDE [](../../includes/devspaces-team-development-1.md)]

### <a name="make-a-code-change"></a>Tornar um código alterar
Aceda a janela do VS Code para `mywebapi` e faça um código em Editar para o `string Get(int id)` método na `Controllers/ValuesController.cs`, por exemplo:

```csharp
[HttpGet("{id}")]
public string Get(int id)
{
    return "mywebapi now says something new";
}
```

[!INCLUDE [](../../includes/devspaces-team-development-2.md)]
