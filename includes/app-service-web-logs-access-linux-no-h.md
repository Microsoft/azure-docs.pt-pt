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
ms.openlocfilehash: df71f0804b62eb4b17ff8d2f652b076b5c64c959
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91822808"
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
