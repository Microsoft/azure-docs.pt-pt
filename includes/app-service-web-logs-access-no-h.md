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
ms.openlocfilehash: e6c4b07d01a4992e22107cb7d524646f439c37c6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84905872"
---
Para aceder aos registos de consola gerados a partir do seu código de aplicação no Serviço de Aplicações, ligue o registo de diagnósticos executando o seguinte comando na [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp log config --resource-group <resource-group-name> --name <app-name> --application-logging true --level Verbose
```

Os valores possíveis `--level` são: `Error` , , e `Warning` `Info` `Verbose` . Cada nível subsequente inclui o nível anterior. Por exemplo: `Error` inclui apenas mensagens de erro e `Verbose` inclui todas as mensagens.

Uma vez ligado o registo de diagnóstico, executar o seguinte comando para ver o fluxo de registo:

```azurecli-interactive
az webapp log tail --resource-group <resource-group-name> --name <app-name>
```

Se não vir os registos da consola imediatamente, volte a consultar dentro de 30 segundos.

> [!NOTE]
> Também pode inspecionar os ficheiros de registo do navegador em `https://<app-name>.scm.azurewebsites.net/api/logs/docker` .

Para parar o streaming de registo a qualquer momento, escreva `Ctrl` + `C` .
