---
title: Gerir recipientes Linux personalizados com CLI
description: Saiba como gerir os recipientes Linux personalizados no Serviço de Aplicações Azure a partir da linha de comando. Automatizar o fornecimento ou manutenção de aplicações.
keywords: serviço de aplicativos azure, web app, cli, linux, oss
ms.topic: article
ms.date: 08/22/2017
ms.custom: seodec18
ms.openlocfilehash: 5ca5322467402af710df68c82d747f8f8d65e142
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "78255929"
---
# <a name="manage-web-app-for-containers-using-azure-cli"></a>Gerir a Aplicação Web para Contentores com a CLI do Azure

Utilizando os comandos deste artigo, é capaz de criar e gerir uma Aplicação Web para Contentores utilizando o CLI Azure.
Pode começar a utilizar a nova versão do CLI de duas formas:

* [Instalação do Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) na sua máquina.
* Usando [a casca de nuvem Azure (pré-visualização)](../../cloud-shell/overview.md)

## <a name="create-a-linux-app-service-plan"></a>Criar um Plano de Serviço de Aplicações Linux

Para criar um Plano de Serviço de Aplicações Linux, pode utilizar o seguinte comando:

```azurecli-interactive
az appservice plan create -n appname -g rgname --is-linux -l "South Central US" --sku S1 --number-of-workers 1
```

## <a name="create-a-custom-docker-container-web-app"></a>Crie uma aplicação web personalizada do recipiente Docker

Para criar uma aplicação web e configurar para executar um recipiente Personalizado Docker, pode utilizar o seguinte comando:

```azurecli-interactive
az webapp create -n sname -g rgname -p pname -i elnably/dockerimagetest
```

## <a name="activate-the-docker-container-logging"></a>Ativar o registo do contentor Docker

Para ativar o registo do contentor Docker, pode utilizar o seguinte comando:

```azurecli-interactive
az webapp log config -n sname -g rgname --web-server-logging filesystem
```

## <a name="change-the-custom-docker-container-for-an-existing-web-app-for-containers-app"></a>Mude o recipiente personalizado do Docker para uma app web existente para a App de Contentores

Para alterar uma aplicação previamente criada, da imagem atual do Docker para uma nova imagem, pode utilizar o seguinte comando:

```azurecli-interactive
az webapp config container set -n sname -g rgname -c apurvajo/mariohtml5
```

## <a name="using-docker-images-from-a-private-registry"></a>Usando imagens docker de um registo privado

Pode configurar a sua aplicação para utilizar imagens de um registo privado. Tem de fornecer o url para o seu registo, nome de utilizador e senha. Isto pode ser alcançado usando o seguinte comando:

```azurecli-interactive
az webapp config container set -n sname1 -g rgname -c <container name> -r <server url> -u <username> -p <password>
```

## <a name="enable-continuous-deployments-for-custom-docker-images"></a>Ativar implementações contínuas para imagens personalizadas do Docker

Com o seguinte comando pode ativar a funcionalidade do CD e obter o url webhook. Este url pode ser usado para configurar o seu dockerHub ou repos de registo de contentores Azure.

```azurecli-interactive
az webapp deployment container config -n sname -g rgname -e true
```

## <a name="create-a-web-app-for-containers-app-using-one-of-our-built-in-runtime-frameworks"></a>Crie uma App Web para contentores utilizando uma das nossas estruturas de tempo de execução incorporadas

Para criar uma App WEB PHP 5.6 para contentores que, pode utilizar o seguinte comando.

```azurecli-interactive
az webapp create -n sname -g rgname -p pname -r "php|5.6"
```

## <a name="change-framework-version-for-an-existing-web-app-for-containers-app"></a>Alterar versão-quadro para uma app web existente para a App de Contentores

Para alterar uma aplicação previamente criada, da versão-quadro atual para Node.js 6.11, pode utilizar o seguinte comando:

```azurecli-interactive
az webapp config set -n sname -g rgname --linux-fx-version "node|6.11"
```

## <a name="set-up-git-deployments-for-your-web-app"></a>Configurar implementações de Git para a sua Web App

Para configurar implementações git para a sua aplicação, pode utilizar o seguinte comando:

```azurecli-interactive
az webapp deployment source config -n sname -g rgname --repo-url <gitrepo url> --branch <branch>
```

## <a name="next-steps"></a>Próximos passos

* [O que é o Serviço de Aplicações Azure no Linux?](app-service-linux-intro.md)
* [Instalar O Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)
* [Concha de nuvem Azure (pré-visualização)](../../cloud-shell/overview.md)
* [Configurar ambientes de teste no Serviço de Aplicações do Azure](../../app-service/deploy-staging-slots.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Implementação contínua com aplicação web para contentores](app-service-linux-ci-cd.md)
