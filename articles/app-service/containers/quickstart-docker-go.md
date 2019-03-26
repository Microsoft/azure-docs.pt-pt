---
title: Criar aplicação Docker/Go no Linux - serviço de aplicações do Azure
description: Como implementar uma imagem de Docker a executar uma aplicação Go para uma Aplicação Web para Contentores.
keywords: serviço de aplicações do azure, aplicação web, go, docker, contentor
services: app-service
author: msangapu
manager: jeconnoc
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.devlang: go
ms.topic: quickstart
ms.date: 01/17/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 3335163c15319e39858003032ae7113c9c26a3fd
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58438585"
---
# <a name="deploy-a-dockergo-web-app-in-web-app-for-containers"></a>Implementar uma aplicação Web Docker/Go na Aplicação Web para Contentores

[O Serviço de Aplicações (Linux)](app-service-linux-intro.md) dispõe de pilhas de aplicação predefinidas no Linux, com suporte para linguagens como .NET, PHP, Node.js, etc. Também pode utilizar uma imagem personalizada do Docker para executar a aplicação Web numa pilha de aplicação que ainda não esteja definida no Azure. Este início rápido mostra como criar uma aplicação Web e implementar uma imagem Go a partir do Hub do Docker. Irá criar a aplicação Web com a [CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).

![Aplicação de exemplo em execução no Azure](media/quickstart-docker-go/hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>Criar uma aplicação Web

Crie uma [aplicação Web](../overview.md) no plano do `myAppServicePlan`Serviço de Aplicações com o comando[az webapp create](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create). Não se esqueça de substituir `<app name>` por um nome de aplicação globalmente único.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app name> --deployment-container-image-name microsoft/azure-appservices-go-quickstart
```

No comando anterior, `--deployment-container-image-name` aponta para a imagem pública do Hub do Docker [microsoft/azure-appservices-go-quickstart](https://hub.docker.com/r/microsoft/azure-appservices-go-quickstart/).

Quando a aplicação Web tiver sido criada, a CLI do Azure mostra informações semelhantes ao seguinte exemplo:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app name>.scm.azurewebsites.net/<app name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

## <a name="browse-to-the-app"></a>Navegar para a aplicação

```bash
http://<app_name>.azurewebsites.net/hello
```

![Aplicação de exemplo em execução no Azure](media/quickstart-docker-go/hello-world-in-browser.png)

**Parabéns!** Implementou uma imagem personalizada de Docker a executar uma aplicação Go para uma Aplicação Web para Contentores.

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Utilizar uma imagem personalizada do Docker](tutorial-custom-docker-image.md)