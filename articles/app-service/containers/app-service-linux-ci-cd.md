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
ms.openlocfilehash: d43491de7500204ed470757a1b744017a8180b57
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "74687624"
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>Implementação contínua com a Aplicação Web para Contentores

Neste tutorial, configura a implementação contínua para uma imagem personalizada de um recipiente a partir de repositórios geridos do [Registo de Contentores Azure](https://azure.microsoft.com/services/container-registry/) ou [do Docker Hub.](https://hub.docker.com)

## <a name="enable-continuous-deployment-with-acr"></a>Ativar a implementação contínua com ACR

![Screenshot do webhook ACR](./media/app-service-webapp-service-linux-ci-cd/ci-cd-acr-02.png)

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione a opção **Serviço de Aplicações** no lado esquerdo da página.
3. Selecione o nome da aplicação para a qual pretende configurar a implementação contínua.
4. Na página Definições do **Contentor,** selecione **Um Único Recipiente**
5. Selecione **registo de contentores Azure**
6. Selecione **> de implementação contínua**
7. **Selecione Guardar** para ativar a implementação contínua.

## <a name="use-the-acr-webhook"></a>Use o webhook ACR

Uma vez ativada a Implementação Contínua, pode visualizar o webhook recém-criado na página webhooks do registo de contentores Azure.

![Screenshot do webhook ACR](./media/app-service-webapp-service-linux-ci-cd/ci-cd-acr-03.png)

No registo do seu contentor, clique em Webhooks para ver os webhooks atuais.

## <a name="enable-continuous-deployment-with-docker-hub-optional"></a>Ativar a implementação contínua com o Docker Hub (opcional)

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione a opção **Serviço de Aplicações** no lado esquerdo da página.
3. Selecione o nome da aplicação para a qual pretende configurar a implementação contínua.
4. Na página Definições do **Contentor,** selecione **Um Único Recipiente**
5. Selecione **Docker Hub**
6. Selecione **> de implementação contínua**
7. **Selecione Guardar** para ativar a implementação contínua.

![Screenshot da definição de aplicativo](./media/app-service-webapp-service-linux-ci-cd/ci-cd-docker-02.png)

Copie a URL webhook. Para adicionar um webhook para Docker Hub, siga <a href="https://docs.docker.com/docker-hub/webhooks/" target="_blank">os webhooks para Docker Hub</a>.

## <a name="next-steps"></a>Próximos passos

* [Introdução ao Serviço de Aplicações do Azure no Linux](./app-service-linux-intro.md)
* [Registo de Contentores do Azure](https://azure.microsoft.com/services/container-registry/)
* [Criar uma aplicação Web .NET Core no Serviço de Aplicações no Linux](quickstart-dotnetcore.md)
* [Criar uma aplicação web Ruby no Serviço de Aplicações em Linux](quickstart-ruby.md)
* [Implementar uma aplicação Web Docker/Go na Aplicação Web para Contentores](quickstart-docker-go.md)
* [FAQ do Serviço de Aplicações do Azure no Linux](./app-service-linux-faq.md)
* [Gerir a Aplicação Web para Contentores com a CLI do Azure](./app-service-linux-cli.md)
