---
title: Gerir recipientes linux personalizados com CLI
description: Saiba como gerir os recipientes linux personalizados no Serviço de Aplicações Azure a partir da linha de comando. Acasale o fornecimento ou manutenção de aplicativos de automatização.
keywords: serviço de aplicativos azure, web app, cli, linux, oss
ms.topic: article
ms.date: 08/22/2017
ms.custom: seodec18
ms.openlocfilehash: 5ca5322467402af710df68c82d747f8f8d65e142
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78255929"
---
# <a name="manage-web-app-for-containers-using-azure-cli"></a>Gerir a Web App para contentores utilizando o Azure CLI

Utilizando os comandos deste artigo é possível criar e gerir uma Aplicação Web para contentores utilizando o Azure CLI.
Pode começar a utilizar a nova versão do CLI de duas formas:

* [Instalação do Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) na sua máquina.
* Utilização da [casca de nuvem azure (pré-visualização)](../../cloud-shell/overview.md)

## <a name="create-a-linux-app-service-plan"></a>Crie um Plano de Serviço de Aplicativos Linux

Para criar um Plano de Serviço de Aplicações Linux, pode utilizar o seguinte comando:

```azurecli-interactive
az appservice plan create -n appname -g rgname --is-linux -l "South Central US" --sku S1 --number-of-workers 1
```

## <a name="create-a-custom-docker-container-web-app"></a>Criar uma aplicação web de contentores Docker personalizada

Para criar uma aplicação web e configurá-la para executar um recipiente Docker personalizado, pode utilizar o seguinte comando:

```azurecli-interactive
az webapp create -n sname -g rgname -p pname -i elnably/dockerimagetest
```

## <a name="activate-the-docker-container-logging"></a>Ativar a exploração de contentores Docker

Para ativar o registo do contentor Docker, pode utilizar o seguinte comando:

```azurecli-interactive
az webapp log config -n sname -g rgname --web-server-logging filesystem
```

## <a name="change-the-custom-docker-container-for-an-existing-web-app-for-containers-app"></a>Altere o recipiente personalizado do Docker para uma aplicação web existente para contentores

Para alterar uma aplicação previamente criada, desde a imagem atual do Docker até uma nova imagem, pode utilizar o seguinte comando:

```azurecli-interactive
az webapp config container set -n sname -g rgname -c apurvajo/mariohtml5
```

## <a name="using-docker-images-from-a-private-registry"></a>Usando imagens do Docker de um registo privado

Pode configurar a sua aplicação para utilizar imagens de um registo privado. Tem de fornecer o url para o seu registo, nome de utilizador e senha. Isto pode ser conseguido utilizando o seguinte comando:

```azurecli-interactive
az webapp config container set -n sname1 -g rgname -c <container name> -r <server url> -u <username> -p <password>
```

## <a name="enable-continuous-deployments-for-custom-docker-images"></a>Ativar implementações contínuas para imagens personalizadas do Docker

Com o seguinte comando pode ativar a funcionalidade de CD e obter o url webhook. Este url pode ser usado para configurar o DockerHub ou o Registo de Contentores Azure repos.

```azurecli-interactive
az webapp deployment container config -n sname -g rgname -e true
```

## <a name="create-a-web-app-for-containers-app-using-one-of-our-built-in-runtime-frameworks"></a>Crie uma Aplicação Web para contentores utilizando uma das nossas estruturas de tempo de execução incorporadas

Para criar uma Aplicação Web PHP 5.6 para contentores que, pode utilizar o seguinte comando.

```azurecli-interactive
az webapp create -n sname -g rgname -p pname -r "php|5.6"
```

## <a name="change-framework-version-for-an-existing-web-app-for-containers-app"></a>Alterar versão-quadro para uma aplicação web existente para contentores

Para alterar uma aplicação previamente criada, desde a versão-quadro atual para o Nó.js 6.11, pode utilizar o seguinte comando:

```azurecli-interactive
az webapp config set -n sname -g rgname --linux-fx-version "node|6.11"
```

## <a name="set-up-git-deployments-for-your-web-app"></a>Configurar as implementações da Git para a sua Web App

Para configurar as implementações git para a sua aplicação, pode utilizar o seguinte comando:

```azurecli-interactive
az webapp deployment source config -n sname -g rgname --repo-url <gitrepo url> --branch <branch>
```

## <a name="next-steps"></a>Passos seguintes

* [O que é o Serviço de Aplicações Azure no Linux?](app-service-linux-intro.md)
* [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli)
* [Concha de nuvem azure (pré-visualização)](../../cloud-shell/overview.md)
* [Configurar ambientes de teste no Serviço de Aplicações do Azure](../../app-service/deploy-staging-slots.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Implantação contínua com aplicação web para contentores](app-service-linux-ci-cd.md)
