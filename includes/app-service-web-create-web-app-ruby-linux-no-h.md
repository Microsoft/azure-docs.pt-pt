---
title: incluir ficheiro
description: incluir ficheiro
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 07/10/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 01c1c7c0bdf8e55b002b73682407a3e03ee30b8f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82085790"
---
Crie uma [aplicação web](../articles/app-service/containers/app-service-linux-intro.md) no plano de `myAppServicePlan` Serviço de Aplicações. 

Na Cloud Shell, podes [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest) usar o comando. No exemplo a seguir, substitua `<app-name>` com um nome de aplicação globalmente exclusivo (os carateres válidos são `a-z`, `0-9` e `-`). O runtime está definido como `RUBY|2.3`. Para ver todos os tempos de corrida suportados, corra. [`az webapp list-runtimes --linux`](/cli/azure/webapp?view=azure-cli-latest) 

```azurecli-interactive
# Bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "RUBY|2.6.2" --deployment-local-git
# PowerShell
az --% webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "RUBY|2.6.2" --deployment-local-git
```

Quando a aplicação Web tiver sido criada, a CLI do Azure mostra informações semelhantes ao seguinte exemplo:

<pre>
Local git is configured with url of 'https://&lt;username&gt;@&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;app-name&gt;.azurewebsites.net",
  "deploymentLocalGitUrl": "https://&lt;username&gt;@&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git",
  "enabled": true,
  &lt; JSON data removed for brevity. &gt;
}
</pre>

Criou uma nova aplicação Web vazia, com a implementação de git ativada.

> [!NOTE]
> O URL do Git remoto é apresentado na propriedade `deploymentLocalGitUrl`, com o formato `https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git`. Guarde este URL, uma vez que vai precisar dele mais tarde.
>
