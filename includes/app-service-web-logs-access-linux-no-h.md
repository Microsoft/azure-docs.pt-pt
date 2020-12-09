---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: e2c5794e5ce6e23b60bff513562f69c9333d6e34
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92743794"
---
Pode aceder aos registos de consola gerados a partir do interior do contentor.

Primeiro, ligue a registo de contentores executando o seguinte comando:

```azurecli-interactive
az webapp log config --name <app-name> --resource-group <resource-group-name> --docker-container-logging filesystem
```

Substitua `<app-name>` e `<resource-group-name>` pelos nomes apropriados para a sua aplicação web.

Uma vez ligado o registo do contentor, erguia o seguinte comando para ver o fluxo de registo:

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group <resource-group-name>
```

Se não vir os registos da consola imediatamente, volte a consultar dentro de 30 segundos.

Para parar o streaming de registo a qualquer momento, digite **Ctrl** + **C**.

Também pode inspecionar os ficheiros de registo num browser em `https://<app-name>.scm.azurewebsites.net/api/logs/docker` .
