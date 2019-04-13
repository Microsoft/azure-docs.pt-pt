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
ms.openlocfilehash: 9f7c82a2bd35f06da096fa0dd9d5d1fa4d08011e
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2019
ms.locfileid: "59549172"
---
No Cloud Shell, crie uma [aplicação Web](../articles/app-service/overview.md) no plano do Serviço de Aplicações `myAppServicePlan`. Pode fazê-lo através do comando [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create). No exemplo seguinte, substitua  *\<nome da aplicação >* com um nome de aplicação globalmente exclusivo (carateres válidos são `a-z`, `0-9`, e `-`).

```azurecli-interactive
az webapp create --name <app-name> --resource-group myResourceGroup --plan myAppServicePlan --deployment-local-git
```

Quando a aplicação Web tiver sido criada, a CLI do Azure mostra informações semelhantes ao seguinte exemplo:

```json
Local git is configured with url of 'https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app-name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

Criou uma aplicação Web vazia, com a implementação de git ativada.

> [!NOTE]
> O URL do Git remoto é apresentado na propriedade `deploymentLocalGitUrl`, com o formato `https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git`. Guarde este URL, uma vez que vai precisar dele mais tarde.
>

Navegue para a aplicação Web recentemente criada.

```
http://<app-name>.azurewebsites.net
```

A aplicação Web deve ter o seguinte aspeto:
