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
ms.openlocfilehash: 7803ac9009af1657e7f162d656898492a694e28f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95997950"
---
Para aceder aos registos da consola gerados a partir do código da sua aplicação no Serviço de Aplicações, ative o registo de diagnósticos ao executar o seguinte comando no [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp log config --resource-group <resource-group-name> --name <app-name> --application-logging true --level Verbose
```

Os valores possíveis para `--level` são: `Error`, `Warning`, `Info` e `Verbose`. Cada nível subsequente inclui o nível anterior. Por exemplo: `Error` inclui apenas mensagens de erro e `Verbose` inclui todas as mensagens.

Depois de ativar o registo de diagnósticos, execute o seguinte comando para ver o fluxo de registos:

```azurecli-interactive
az webapp log tail --resource-group <resource-group-name> --name <app-name>
```

Se não vir os registos da consola imediatamente, volte a consultar dentro de 30 segundos.

> [!NOTE]
> Também pode inspecionar os ficheiros de registo no browser em `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

Para parar a transmissão de registos em qualquer altura, escreva `Ctrl`+`C`.
