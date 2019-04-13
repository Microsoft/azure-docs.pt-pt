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
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2019
ms.locfileid: "59551221"
---
Pode acessar os registos de consola gerados a partir de dentro do contentor. Em primeiro lugar, ativar o registo de contentor ao executar o seguinte comando no Cloud Shell:

```azurecli-interactive
az webapp log config --name <app-name> --resource-group myResourceGroup --docker-container-logging filesystem
```

Assim que o registo de contentor está ativado, execute o seguinte comando para ver o fluxo de registo:

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group myResourceGroup
```

Se não vir os registos da consola imediatamente, volte a consultar dentro de 30 segundos.

> [!NOTE]
> Também pode inspecionar os ficheiros de registo do navegador em `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

Para parar a transmissão de registos em qualquer altura, escreva `Ctrl` + `C`.
