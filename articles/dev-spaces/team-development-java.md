---
title: Desenvolvimento em equipe com espaços de desenvolvimento do Azure com Java e o VS Code
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: stepro
ms.author: stephpr
ms.date: 08/01/2018
ms.topic: tutorial
description: Desenvolvimento rápido da Kubernetes com contentores e microsserviços no Azure
keywords: 'Docker, o Kubernetes, o Azure, o AKS, o serviço Kubernetes do Azure, contentores, Helm, a malha de serviço, roteamento de malha do serviço, kubectl, k8s '
manager: mmontwil
ms.openlocfilehash: 29a50a871702985b64c594d5bd3700d4e29f40df
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60368243"
---
[!INCLUDE [](../../includes/devspaces-team-development-1.md)]

### <a name="make-a-code-change"></a>Tornar um código alterar
Aceda a janela do VS Code para `mywebapi` e faça um código em Editar para o `String index()` método na `src/main/java/com/ms/sample/mywebapi/Application.java`, por exemplo:

```java
@RequestMapping(value = "/", produces = "text/plain")
public String index() {
    return "Hello from mywebapi says something new";
}
```

[!INCLUDE [](../../includes/devspaces-team-development-2.md)]
