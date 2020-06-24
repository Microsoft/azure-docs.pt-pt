---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 0dd6618bdee8e6810d414d4b04b16a1e0a9c90ed
ms.sourcegitcommit: 34eb5e4d303800d3b31b00b361523ccd9eeff0ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2020
ms.locfileid: "84905631"
---
Pode aceder aos registos de consola gerados a partir do interior do contentor. Primeiro, ligue a registo de contentores executando o seguinte comando na Cloud Shell:

```azurecli-interactive
az webapp log config --name <app-name> --resource-group myResourceGroup --docker-container-logging filesystem
```

Uma vez ligado o registo do contentor, erguia o seguinte comando para ver o fluxo de registo:

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group myResourceGroup
```

Se não vir os registos da consola imediatamente, volte a consultar dentro de 30 segundos.

> [!NOTE]
> Também pode inspecionar os ficheiros de registo do navegador em `https://<app-name>.scm.azurewebsites.net/api/logs/docker` .

Para parar o streaming de registo a qualquer momento, escreva `Ctrl` + `C` .
