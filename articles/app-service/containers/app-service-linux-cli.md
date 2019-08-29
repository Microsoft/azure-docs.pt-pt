---
title: Gerenciar Aplicativo Web para Contêineres usando o serviço de Azure App de CLI do Azure | Microsoft Docs
description: Gerenciar Aplicativo Web para Contêineres usando CLI do Azure.
keywords: serviço de aplicativo do Azure, aplicativo Web, CLI, Linux, OSS
services: app-service
documentationCenter: ''
author: ahmedelnably
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/22/2017
ms.author: aelnably
ms.custom: seodec18
ms.openlocfilehash: 5481e9f28d8fc47936ad62bd8d974beb5ca85fcd
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70071301"
---
# <a name="manage-web-app-for-containers-using-azure-cli"></a>Gerenciar Aplicativo Web para Contêineres usando CLI do Azure

Usando os comandos neste artigo, você pode criar e gerenciar um Aplicativo Web para Contêineres usando o CLI do Azure.
Você pode começar a usar a nova versão da CLI de duas maneiras:

* [Instalação do CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) em seu computador.
* Usando [Azure cloud Shell (visualização)](../../cloud-shell/overview.md)

## <a name="create-a-linux-app-service-plan"></a>Criar um plano do serviço de aplicativo do Linux

Para criar um plano do serviço de aplicativo do Linux, você pode usar o seguinte comando:

```azurecli-interactive
az appservice plan create -n appname -g rgname --is-linux -l "South Central US" --sku S1 --number-of-workers 1
```

## <a name="create-a-custom-docker-container-web-app"></a>Criar um aplicativo Web de contêiner do Docker personalizado

Para criar um aplicativo Web e configurá-lo para executar um contêiner do Docker personalizado, você pode usar o seguinte comando:

```azurecli-interactive
az webapp create -n sname -g rgname -p pname -i elnably/dockerimagetest
```

## <a name="activate-the-docker-container-logging"></a>Ativar o registro em log do contêiner do Docker

Para ativar o registro em log do contêiner do Docker, você pode usar o seguinte comando:

```azurecli-interactive
az webapp log config -n sname -g rgname --web-server-logging filesystem
```

## <a name="change-the-custom-docker-container-for-an-existing-web-app-for-containers-app"></a>Alterar o contêiner do Docker personalizado para um aplicativo de Aplicativo Web para Contêineres existente

Para alterar um aplicativo criado anteriormente, da imagem do Docker atual para uma nova imagem, você pode usar o seguinte comando:

```azurecli-interactive
az webapp config container set -n sname -g rgname -c apurvajo/mariohtml5
```

## <a name="using-docker-images-from-a-private-registry"></a>Usando imagens do Docker de um registro privado

Você pode configurar seu aplicativo para usar imagens de um registro privado. Você precisa fornecer a URL para o registro, o nome de usuário e a senha. Isso pode ser feito usando o seguinte comando:

```azurecli-interactive
az webapp config container set -n sname1 -g rgname -c <container name> -r <server url> -u <username> -p <password>
```

## <a name="enable-continuous-deployments-for-custom-docker-images"></a>Habilitar implantações contínuas para imagens personalizadas do Docker

Com o comando a seguir, você pode habilitar a funcionalidade do CD e obter a URL do webhook. Essa URL pode ser usada para configurar DockerHub ou repositórios de registro de contêiner do Azure.

```azurecli-interactive
az webapp deployment container config -n sname -g rgname -e true
```

## <a name="create-a-web-app-for-containers-app-using-one-of-our-built-in-runtime-frameworks"></a>Criar um aplicativo Aplicativo Web para Contêineres usando uma das estruturas de tempo de execução internas

Para criar um aplicativo Aplicativo Web para Contêineres do PHP 5,6 que, você pode usar o comando a seguir.

```azurecli-interactive
az webapp create -n sname -g rgname -p pname -r "php|5.6"
```

## <a name="change-framework-version-for-an-existing-web-app-for-containers-app"></a>Alterar a versão da estrutura para um aplicativo de Aplicativo Web para Contêineres existente

Para alterar um aplicativo criado anteriormente, da versão atual do Framework para o Node. js 6,11, você pode usar o seguinte comando:

```azurecli-interactive
az webapp config set -n sname -g rgname --linux-fx-version "node|6.11"
```

## <a name="set-up-git-deployments-for-your-web-app"></a>Configurar implantações do git para seu aplicativo Web

Para configurar implantações do git para seu aplicativo, você pode usar o seguinte comando:

```azurecli-interactive
az webapp deployment source config -n sname -g rgname --repo-url <gitrepo url> --branch <branch>
```

## <a name="next-steps"></a>Passos seguintes

* [O que é o serviço Azure App no Linux?](app-service-linux-intro.md)
* [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli)
* [Azure Cloud Shell (versão prévia)](../../cloud-shell/overview.md)
* [Configurar ambientes de teste no Serviço de Aplicações do Azure](../../app-service/deploy-staging-slots.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Implantação contínua com Aplicativo Web para Contêineres](app-service-linux-ci-cd.md)
