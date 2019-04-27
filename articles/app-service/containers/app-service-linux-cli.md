---
title: Gerir a aplicação Web para contentores com a CLI do Azure - serviço de aplicações do Azure | Documentos da Microsoft
description: Gerir a aplicação Web para contentores com a CLI do Azure.
keywords: serviço de aplicações do Azure, aplicação web, da cli, linux, oss
services: app-service
documentationCenter: ''
author: ahmedelnably
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: aelnably
ms.custom: seodec18
ms.openlocfilehash: 21f6963fbaada4524f27602454d38e7252a5e8b9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60850089"
---
# <a name="manage-web-app-for-containers-using-azure-cli"></a>Gerir a aplicação Web para contentores com a CLI do Azure

Com os comandos neste artigo, que pode criar e gerir uma aplicação Web para contentores com a CLI do Azure.
Pode começar a utilizar a nova versão da CLI de duas formas:

* [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) no seu computador.
* Usando [Azure Cloud Shell (pré-visualização)](../../cloud-shell/overview.md)

## <a name="create-a-linux-app-service-plan"></a>Criar um plano de serviço de aplicações do Linux

Para criar um plano de serviço de aplicações do Linux, pode utilizar o seguinte comando:

```azurecli-interactive
az appservice plan create -n appname -g rgname --is-linux -l "South Central US" --sku S1 --number-of-workers 1
```

## <a name="create-a-custom-docker-container-web-app"></a>Criar um contentor de Docker personalizado aplicação Web

Para criar uma aplicação web e configurá-lo para executar um contentor de Docker personalizado, pode utilizar o seguinte comando:

```azurecli-interactive
az webapp create -n sname -g rgname -p pname -i elnably/dockerimagetest
```

## <a name="activate-the-docker-container-logging"></a>Ativar o registo de contentor de Docker

Para ativar o registo de contentor do Docker, pode usar o seguinte comando:

```azurecli-interactive
az webapp log config -n sname -g rgname --web-server-logging filesystem
```

## <a name="change-the-custom-docker-container-for-an-existing-web-app-for-containers-app"></a>Alterar o contentor de Docker personalizado para uma aplicação Web existente para a aplicação de contentores

Para alterar uma aplicação criada anteriormente, da imagem do Docker atual para uma nova imagem, pode utilizar o seguinte comando:

```azurecli-interactive
az webapp config container set -n sname -g rgname -c apurvajo/mariohtml5
```

## <a name="using-docker-images-from-a-private-registry"></a>Utilizar imagens do Docker a partir de um registo privado

Pode configurar a aplicação para utilizar imagens de um registo privado. Tem de fornecer o url para o seu registo, nome de utilizador e palavra-passe. Isso pode ser obtido com o seguinte comando:

```azurecli-interactive
az webapp config container set -n sname1 -g rgname -c <container name> -r <server url> -u <username> -p <password>
```

## <a name="enable-continuous-deployments-for-custom-docker-images"></a>Ativar implementações contínuas para imagens personalizadas do Docker

Com o seguinte comando pode ativar a funcionalidade de CD e obter o url do webhook. Este url pode ser utilizado para configurar o repos DockerHub ou o Azure Container Registry.

```azurecli-interactive
az webapp deployment container config -n sname -g rgname -e true
```

## <a name="create-a-web-app-for-containers-app-using-one-of-our-built-in-runtime-frameworks"></a>Criar uma aplicação Web para a aplicação de contentores com um dos nossos estruturas de tempo de execução interno

Para criar uma aplicação Web em PHP 5.6 para a aplicação de contentores o que, pode usar o seguinte comando.

```azurecli-interactive
az webapp create -n sname -g rgname -p pname -r "php|5.6"
```

## <a name="change-framework-version-for-an-existing-web-app-for-containers-app"></a>Alterar versão do framework para uma aplicação Web existente para a aplicação de contentores

Para alterar uma aplicação criada anteriormente, da atual versão do framework para 6.11 de node. js, pode utilizar o seguinte comando:

```azurecli-interactive
az webapp config set -n sname -g rgname --linux-fx-version "node|6.11"
```

## <a name="set-up-git-deployments-for-your-web-app"></a>Configurar as implementações do Git para a sua aplicação Web

Para configurar a implementações do Git para a sua aplicação, pode utilizar o seguinte comando:

```azurecli-interactive
az webapp deployment source config -n sname -g rgname --repo-url <gitrepo url> --branch <branch>
```

## <a name="next-steps"></a>Passos Seguintes

* [O que é o serviço de aplicações do Azure no Linux?](app-service-linux-intro.md)
* [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli)
* [Azure Cloud Shell (pré-visualização)](../../cloud-shell/overview.md)
* [Configurar ambientes de teste no Serviço de Aplicações do Azure](../../app-service/deploy-staging-slots.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Implementação contínua com a aplicação Web para contentores](app-service-linux-ci-cd.md)
