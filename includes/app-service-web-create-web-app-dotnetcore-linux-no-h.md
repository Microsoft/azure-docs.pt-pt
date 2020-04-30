---
title: incluir ficheiro
description: incluir ficheiro
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 04/22/2020
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: f397a3df7280b9277b2b7205368ef5788ed321aa
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82206681"
---
Crie uma [aplicação web](../articles/app-service/containers/app-service-linux-intro.md) no plano de `myAppServicePlan` Serviço de Aplicações. 

Na Cloud Shell, podes [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest) usar o comando. No exemplo a seguir, substitua `<app-name>` com um nome de aplicação globalmente exclusivo (os carateres válidos são `a-z`, `0-9` e `-`). O tempo de `DOTNETCORE|LTS`execução está definido para , que é .NET Core 3.1. Para ver todos os tempos de corrida suportados, corra. [`az webapp list-runtimes --linux`](/cli/azure/webapp?view=azure-cli-latest) 

```azurecli-interactive
# Bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "DOTNETCORE|LTS" --deployment-local-git
# PowerShell
az --% webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "DOTNETCORE|LTS" --deployment-local-git
```

Quando a aplicação Web tiver sido criada, a CLI do Azure mostra informações semelhantes ao seguinte exemplo:

<pre>
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
</pre>

Criou uma aplicação Web vazia num contentor Linux, com a implementação de git ativada.

> [!NOTE]
> O URL do Git remoto é apresentado na propriedade `deploymentLocalGitUrl`, com o formato `https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git`. Guarde este URL, uma vez que vai precisar dele mais tarde.
>

Atualmente, é necessário executar o seguinte comando extra para configurar `<app-name>` corretamente a versão .NET Core (substitua-a pela do passo anterior):

```azurecli-interactive
# Bash
az webapp config set --resource-group myResourceGroup --name <app-name> --linux-fx-version "DOTNETCORE|3.1"
# PowerShell
az --% webapp config set --resource-group myResourceGroup --name <app-name> --linux-fx-version "DOTNETCORE|3.1"
```
