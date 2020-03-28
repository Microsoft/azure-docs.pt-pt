---
title: incluir ficheiro
description: incluir ficheiro
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: c1044f129b8f8e9b7e861633a58f3e894783de10
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "77537929"
---
Crie uma [aplicação web](../articles/app-service/containers/app-service-linux-intro.md) no plano de `myAppServicePlan` Serviço de Aplicações. 

Na Cloud Shell, podes [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) usar o comando. No exemplo a seguir, substitua `<app-name>` com um nome de aplicação globalmente exclusivo (os carateres válidos são `a-z`, `0-9` e `-`). 

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --deployment-local-git
```

Quando a aplicação Web tiver sido criada, a CLI do Azure mostra informações semelhantes ao seguinte exemplo:

```json
Local git is configured with url of 'https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "clientCertExclusionPaths": null,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app-name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

> [!NOTE]
> O URL do Git remoto é apresentado na propriedade `deploymentLocalGitUrl`, com o formato `https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git`. Guarde este URL, uma vez que vai precisar dele mais tarde.
>
