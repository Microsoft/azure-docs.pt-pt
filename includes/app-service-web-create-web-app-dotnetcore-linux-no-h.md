---
title: incluir ficheiro
description: incluir ficheiro
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 04/22/2020
ms.author: cephalin
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 727e49e9086e5800fbbcb4e42a37a8b1a0c1c4b7
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107510727"
---
Crie uma [aplicação web](../articles/app-service/overview.md#app-service-on-linux) no `myAppServicePlan` plano de Serviço de Aplicações. 

Na Cloud Shell, podes usar o [`az webapp create`](/cli/azure/webapp) comando. No exemplo a seguir, substitua `<app-name>` com um nome de aplicação globalmente exclusivo (os carateres válidos são `a-z`, `0-9` e `-`). O runtime está definido como `DOTNETCORE|3.1`. Para ver todos os tempos de execução suportados, [`az webapp list-runtimes --linux`](/cli/azure/webapp) corra. 

```azurecli-interactive
# Bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "DOTNETCORE|3.1" --deployment-local-git
# PowerShell
az --% webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "DOTNETCORE|3.1" --deployment-local-git
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

O Youâ€™ve criou uma aplicação web vazia num recipiente Linux, com implementação de git ativada.

> [!NOTE]
> O URL do Git remoto é apresentado na propriedade `deploymentLocalGitUrl`, com o formato `https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git`. Guarde este URL, uma vez que vai precisar dele mais tarde.
>
