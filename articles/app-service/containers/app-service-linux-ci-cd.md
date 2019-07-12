---
title: Implementação contínua com a aplicação Web para contentores - serviço de aplicações do Azure | Documentos da Microsoft
description: Como configurar a implementação contínua na aplicação Web para contentores.
keywords: serviço de aplicações do Azure, linux, docker, acr, oss
services: app-service
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: be3768aadb3e256aa7b0ba7fe3d72cf4169e9bb2
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705708"
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>Implementação contínua com a aplicação Web para contentores

Neste tutorial, vai configurar a implementação contínua para uma imagem de contentor personalizado do managed [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) repositórios ou [Docker Hub](https://hub.docker.com).

## <a name="enable-continuous-deployment-with-acr"></a>Ativar a implementação contínua com o ACR

![Captura de ecrã do ACR webhook](./media/app-service-webapp-service-linux-ci-cd/ci-cd-acr-02.png)

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione o **serviço de aplicações** opção no lado esquerdo da página.
3. Selecione o nome da aplicação para o qual pretende configurar a implementação contínua.
4. Sobre o **definições de contentor** página, selecione **contentor único**
5. Selecione **do Azure Container Registry**
6. Selecione **implementação contínua > em**
7. Selecione **guardar** para ativar a implementação contínua.

## <a name="use-the-acr-webhook"></a>Utilizar o ACR webhook

Assim que tiver sido ativada a implementação contínua, pode ver o webhook criado recentemente na sua página de webhooks de registo de contentor do Azure.

![Captura de ecrã do ACR webhook](./media/app-service-webapp-service-linux-ci-cd/ci-cd-acr-03.png)

No seu registo de contentor, clique em Webhooks para ver os webhooks atuais.

## <a name="enable-continuous-deployment-with-docker-hub-optional"></a>Ativar a implementação contínua com o Docker Hub (opcional)

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione o **serviço de aplicações** opção no lado esquerdo da página.
3. Selecione o nome da aplicação para o qual pretende configurar a implementação contínua.
4. Sobre o **definições de contentor** página, selecione **contentor único**
5. Selecione **Hub do Docker**
6. Selecione **implementação contínua > em**
7. Selecione **guardar** para ativar a implementação contínua.

![Captura de ecrã da definição de aplicação](./media/app-service-webapp-service-linux-ci-cd/ci-cd-docker-02.png)

Copie o URL do Webhook. Para adicionar um webhook para o Docker Hub, siga <a href="https://docs.docker.com/docker-hub/webhooks/" target="_blank">webhooks para Docker Hub</a>.

## <a name="next-steps"></a>Passos Seguintes

* [Introdução ao serviço de aplicações do Azure no Linux](./app-service-linux-intro.md)
* [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
* [Criar uma aplicação Web .NET Core no Serviço de Aplicações no Linux](quickstart-dotnetcore.md)
* [Criar uma aplicação Ruby web no serviço de aplicações no Linux](quickstart-ruby.md)
* [Implementar uma aplicação web do Docker/Go na aplicação Web para contentores](quickstart-docker-go.md)
* [FAQ do Serviço de Aplicações do Azure no Linux](./app-service-linux-faq.md)
* [Gerir a Aplicação Web para Contentores com a CLI do Azure](./app-service-linux-cli.md)
