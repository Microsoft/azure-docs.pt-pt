---
title: CI/CD para contêineres personalizados do Linux
description: Saiba como configurar a implantação contínua em um contêiner personalizado do Linux no serviço Azure App. A implantação contínua tem suporte para o Hub e ACR do Docker.
keywords: serviço de aplicativo do Azure, Linux, Docker, ACR, OSS
author: msangapu-msft
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.topic: article
ms.date: 11/08/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: d43491de7500204ed470757a1b744017a8180b57
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74687624"
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>Implantação contínua com Aplicativo Web para Contêineres

Neste tutorial, você configura a implantação contínua para uma imagem de contêiner personalizada de repositórios gerenciados [do registro de contêiner do Azure](https://azure.microsoft.com/services/container-registry/) ou do [Hub do Docker](https://hub.docker.com).

## <a name="enable-continuous-deployment-with-acr"></a>Habilitar implantação contínua com ACR

![Captura de tela de webhook ACR](./media/app-service-webapp-service-linux-ci-cd/ci-cd-acr-02.png)

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione a opção **serviço de aplicativo** no lado esquerdo da página.
3. Selecione o nome do aplicativo para o qual você deseja configurar a implantação contínua.
4. Na página **configurações do contêiner** , selecione **contêiner único**
5. Selecionar **registro de contêiner do Azure**
6. Selecione **> de implantação contínua em**
7. Selecione **salvar** para habilitar a implantação contínua.

## <a name="use-the-acr-webhook"></a>Usar o webhook ACR

Depois que a implantação contínua tiver sido habilitada, você poderá exibir o webhook recém-criado em sua página WebHooks do registro de contêiner do Azure.

![Captura de tela de webhook ACR](./media/app-service-webapp-service-linux-ci-cd/ci-cd-acr-03.png)

No registro de contêiner, clique em WebHooks para exibir os WebHooks atuais.

## <a name="enable-continuous-deployment-with-docker-hub-optional"></a>Habilitar a implantação contínua com o Hub do Docker (opcional)

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione a opção **serviço de aplicativo** no lado esquerdo da página.
3. Selecione o nome do aplicativo para o qual você deseja configurar a implantação contínua.
4. Na página **configurações do contêiner** , selecione **contêiner único**
5. Selecionar o **Hub do Docker**
6. Selecione **> de implantação contínua em**
7. Selecione **salvar** para habilitar a implantação contínua.

![Captura de tela da configuração do aplicativo](./media/app-service-webapp-service-linux-ci-cd/ci-cd-docker-02.png)

Copie a URL do webhook. Para adicionar um webhook ao Hub do Docker, siga <a href="https://docs.docker.com/docker-hub/webhooks/" target="_blank">WebHooks para o Hub do Docker</a>.

## <a name="next-steps"></a>Passos seguintes

* [Introdução ao serviço de Azure App no Linux](./app-service-linux-intro.md)
* [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
* [Criar uma aplicação Web .NET Core no Serviço de Aplicações no Linux](quickstart-dotnetcore.md)
* [Criar um aplicativo Web Ruby no serviço de aplicativo no Linux](quickstart-ruby.md)
* [Implantar um aplicativo Web Docker/go no Aplicativo Web para Contêineres](quickstart-docker-go.md)
* [FAQ do Serviço de Aplicações do Azure no Linux](./app-service-linux-faq.md)
* [Gerir a Aplicação Web para Contentores com a CLI do Azure](./app-service-linux-cli.md)
