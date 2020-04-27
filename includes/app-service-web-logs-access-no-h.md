---
title: incluir ficheiro
description: incluir ficheiro
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 0dd6618bdee8e6810d414d4b04b16a1e0a9c90ed
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/26/2020
ms.locfileid: "67184735"
---
Pode aceder aos registos de consolas gerados a partir do interior do recipiente. Primeiro, ligue a exploração do contentor executando o seguinte comando na Cloud Shell:

```azurecli-interactive
az webapp log config --name <app-name> --resource-group myResourceGroup --docker-container-logging filesystem
```

Quando o registo do contentor estiver ligado, faça o seguinte comando para ver o fluxo de registo:

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group myResourceGroup
```

Se não vir os registos da consola imediatamente, volte a consultar dentro de 30 segundos.

> [!NOTE]
> Também pode inspecionar os ficheiros `https://<app-name>.scm.azurewebsites.net/api/logs/docker`de registo do navegador em .

Para parar o streaming de `Ctrl` + `C`registo a qualquer momento, escreva .
