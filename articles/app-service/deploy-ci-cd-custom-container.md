---
title: CI/CD para recipientes Linux personalizados
description: Saiba como configurar a implementação contínua de um recipiente Linux personalizado no Azure App Service. A implementação contínua é suportada para Docker Hub e ACR.
keywords: serviço de aplicativos azure, linux, docker, acr,oss
author: msangapu-msft
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.topic: article
ms.date: 11/08/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 8a51fbcb7b7504b9a16e8d0025856c2b007070a9
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2020
ms.locfileid: "92928013"
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>Implementação contínua com a Aplicação Web para Contentores

Neste tutorial, configura a implementação contínua para uma imagem personalizada de um recipiente a partir de repositórios geridos do [Registo de Contentores Azure](https://azure.microsoft.com/services/container-registry/) ou [do Docker Hub.](https://hub.docker.com)

## <a name="enable-continuous-deployment-with-acr"></a>Ativar a implementação contínua com ACR

![Screenshot do webhook ACR](./media/deploy-ci-cd-custom-container/ci-cd-acr-02.png)

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione a opção **Serviço de Aplicações** no lado esquerdo da página.
3. Selecione o nome da aplicação para a qual pretende configurar a implementação contínua.
4. Na página Definições do **Contentor,** selecione **Um Único Recipiente**
5. Selecione **registo de contentores Azure**
6. Selecione **> de implementação contínua**
7. **Selecione Guardar** para ativar a implementação contínua.

## <a name="use-the-acr-webhook"></a>Use o webhook ACR

Uma vez ativada a Implementação Contínua, pode visualizar o webhook recém-criado na página webhooks do registo de contentores Azure.

![Screenshot que mostra onde você pode ver o webhook recém-criado na sua página de webhooks do Registo de Contentores Azure.](./media/deploy-ci-cd-custom-container/ci-cd-acr-03.png)

No registo do seu contentor, clique em Webhooks para ver os webhooks atuais.

## <a name="enable-continuous-deployment-with-docker-hub-optional"></a>Ativar a implementação contínua com o Docker Hub (opcional)

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione a opção **Serviço de Aplicações** no lado esquerdo da página.
3. Selecione o nome da aplicação para a qual pretende configurar a implementação contínua.
4. Na página Definições do **Contentor,** selecione **Um Único Recipiente**
5. Selecione **Docker Hub**
6. Selecione **> de implementação contínua**
7. **Selecione Guardar** para ativar a implementação contínua.

![Screenshot da definição de aplicativo](./media/deploy-ci-cd-custom-container/ci-cd-docker-02.png)

Copie a URL webhook. Para adicionar um webhook para Docker Hub, siga <a href="https://docs.docker.com/docker-hub/webhooks/" target="_blank">os webhooks para Docker Hub</a>.

## <a name="automate-with-cli"></a>Automatize com CLI

Para configurar o CI/CD utilizando o CLI Azure, executar o comando de configuração do [contentor de implementação az webapp](/cli/azure/webapp/deployment/container?view=azure-cli-latest#az-webapp-deployment-container-config) para gerar o URL webhook. O URL pode ser usado para configurar o seu Registo de Contentores DockerHub ou Azure.

```azurecli-interactive
az webapp deployment container config --name <app-name> --resource-group <group-name> --enable-cd true
```

## <a name="next-steps"></a>Passos seguintes

* [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
* [Criar uma aplicação Web .NET Core no Serviço de Aplicações no Linux](quickstart-dotnetcore.md?pivots=platform-linux)
* [Criar uma aplicação web Ruby no Serviço de Aplicações em Linux](quickstart-ruby.md)
* [Quickstart: Executar um recipiente personalizado no Serviço de Aplicações](quickstart-custom-container.md?pivots=container-linux)
* [FAQ do Serviço de Aplicações no Linux](faq-app-service-linux.md)
* [Configurar recipientes Linux personalizados](configure-custom-container.md)