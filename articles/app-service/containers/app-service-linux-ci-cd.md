---
title: CI/CD para recipientes Linux personalizados
description: Aprenda a configurar a implantação contínua para um recipiente Linux personalizado no Serviço de Aplicações Azure. A implantação contínua é suportada para Docker Hub e ACR.
keywords: serviço de aplicativos azure, linux, estivador, acr,oss
author: msangapu-msft
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.topic: article
ms.date: 11/08/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: d43491de7500204ed470757a1b744017a8180b57
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74687624"
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>Implementação contínua com a Aplicação Web para Contentores

Neste tutorial, configura a implantação contínua para uma imagem de recipiente personalizada dos repositórios de registo de [contentores do Azure](https://azure.microsoft.com/services/container-registry/) geridos ou [do Docker Hub](https://hub.docker.com).

## <a name="enable-continuous-deployment-with-acr"></a>Permitir a implantação contínua com a ACR

![Screenshot do webhook aCR](./media/app-service-webapp-service-linux-ci-cd/ci-cd-acr-02.png)

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Selecione a opção **Serviço de Aplicações** no lado esquerdo da página.
3. Selecione o nome da aplicação para a qual pretende configurar a implementação contínua.
4. Na página **definições** do **recipiente,** selecione Recipiente Único
5. Selecione **Registo de Contentores Azure**
6. Selecione **> de implantação contínua on**
7. Selecione **Guardar** para ativar a implantação contínua.

## <a name="use-the-acr-webhook"></a>Use o webhook ACR

Uma vez ativada a Implantação Contínua, pode visualizar o webhook recém-criado na página de webhooks do Registo de Contentores Azure.

![Screenshot do webhook aCR](./media/app-service-webapp-service-linux-ci-cd/ci-cd-acr-03.png)

No seu Registo de Contentores, clique em Webhooks para ver os ganchos web atuais.

## <a name="enable-continuous-deployment-with-docker-hub-optional"></a>Ativar a implantação contínua com o Docker Hub (opcional)

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Selecione a opção **Serviço de Aplicações** no lado esquerdo da página.
3. Selecione o nome da aplicação para a qual pretende configurar a implementação contínua.
4. Na página **definições** do **recipiente,** selecione Recipiente Único
5. Selecione **Docker Hub**
6. Selecione **> de implantação contínua on**
7. Selecione **Guardar** para ativar a implantação contínua.

![Screenshot da definição de aplicativo](./media/app-service-webapp-service-linux-ci-cd/ci-cd-docker-02.png)

Copie o URL webhook. Para adicionar um webhook para Docker Hub, siga <a href="https://docs.docker.com/docker-hub/webhooks/" target="_blank">os webhooks para Docker Hub</a>.

## <a name="next-steps"></a>Passos seguintes

* [Introdução ao Serviço de Aplicações do Azure no Linux](./app-service-linux-intro.md)
* [Registo de Contentores Azure](https://azure.microsoft.com/services/container-registry/)
* [Criar uma aplicação Web .NET Core no Serviço de Aplicações no Linux](quickstart-dotnetcore.md)
* [Crie uma aplicação web Ruby no Serviço de Aplicações no Linux](quickstart-ruby.md)
* [Implementar uma aplicação Web Docker/Go na Aplicação Web para Contentores](quickstart-docker-go.md)
* [FAQ do Serviço de Aplicações do Azure no Linux](./app-service-linux-faq.md)
* [Gerir a Aplicação Web para Contentores com a CLI do Azure](./app-service-linux-cli.md)
