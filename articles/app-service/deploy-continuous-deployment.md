---
title: Implementação contínua – serviço de aplicações do Azure | Documentos da Microsoft
description: Saiba como ativar a implementação contínua para o Serviço de Aplicações do Azure.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/03/2018
ms.author: cephalin;dariagrigoriu
ms.custom: seodec18
ms.openlocfilehash: fcb2c270b36d5efbe7b799787cf2a123b51bea5c
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58337549"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Implementação contínua no App Service do Azure
Este artigo mostra-lhe como configurar a implementação contínua para [App Service do Azure](overview.md). Serviço de aplicações permite a implementação contínua do BitBucket, GitHub, e [serviços do Azure DevOps](https://www.visualstudio.com/team-services/) Pegando as atualizações mais recentes do seu repositório existente em um destes serviços.

Para obter informações sobre como configurar a implementação contínua manualmente a partir de um repositório de cloud não listado pelo portal do Azure (por exemplo, [GitLab](https://gitlab.com/)), veja [configuração de implementação contínua com passos manuais](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps).

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

Publicar o seu repositório preparado para um dos serviços com suporte. Para obter mais informações sobre a publicação de seu projeto para estes serviços, consulte [criar um repositório (GitHub)], [criar um repositório (BitBucket)], e [Introdução aos serviços do Azure DevOps].

## <a name="deploy-continuously-from-github"></a>Implementar continuamente a partir do GitHub

Para ativar a implementação contínua com o GitHub, navegue até à página da aplicação do serviço de aplicações na [portal do Azure](https://portal.azure.com).

No menu à esquerda, clique em **Deployment Center** > **GitHub** > **autorizar**. Siga as instruções de autorização. 

![](media/app-service-continuous-deployment/github-choose-source.png)

Apenas terá de autorizar com o GitHub, uma vez. Se já estiver autorizado, basta clicar **continuar**. Pode alterar a conta do GitHub autorizada clicando **alterar conta**.

![](media/app-service-continuous-deployment/github-continue.png)

Na **fornecedor de compilação** página, escolha o fornecedor de compilação e clique em > **continuar**.

### <a name="option-1-use-app-service-kudu-build-server"></a>Opção 1: utilizar o servidor de compilação Kudu do serviço de aplicações

Na **configurar** , selecione a organização, o repositório e o ramo do qual deseja implantar continuamente. Quando terminar, clique em **continuar**.

Para implementar a partir de um repositório numa organização do GitHub, navegue para o GitHub e aceda a **configurações** > **aplicativos** > **autorizado a aplicações de OAuth**. Em seguida, clique em "App Service do Azure".

![Definições > aplicações > OAuth aplicações autorizadas > serviço de aplicações do Azure](media/app-service-continuous-deployment/github-settings-navigation.png)

Na página seguinte, conceda acesso de serviço de aplicações para repositórios da sua organização ao clicar no botão "Concessão" no lado direito.

![Clique em "Concessão" para conceder acesso de serviço de aplicações para repositórios da organização](media/app-service-continuous-deployment/grant-access.png)

Sua organização deve agora mostrar na lista de "Organização" no **configurar** página do Centro de implementação.

### <a name="option-2-use-azure-pipelines-preview"></a>Opção 2: utilizar Pipelines do Azure (pré-visualização)

> [!NOTE]
> Para o serviço de aplicações para criar Pipelines do Azure necessários na sua organização de serviços do Azure DevOps, sua conta do Azure tem de ter a função de **proprietário** na sua subscrição do Azure.
>

No **configurar** página, além da **código** secção, selecione a organização, o repositório e o ramo do qual deseja implantar continuamente. Quando terminar, clique em **continuar**.

Na **configurar** página, além do **criar** secção, configurar uma nova organização de serviços de DevOps do Azure ou especificar uma organização existente. Quando terminar, clique em **continuar**.

> [!NOTE]
> Se pretender utilizar uma organização de serviços de DevOps do Azure existente que não esteja listada, terá [associar a organização de serviços de DevOps do Azure à sua subscrição do Azure](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App).

Na **teste** página, escolha se pretende ativar a testes de carga, em seguida, clique em **continuar**.

Consoante a [escalão de preço](https://azure.microsoft.com/pricing/details/app-service/plans/) do seu plano do serviço de aplicações, também poderá ver um **implementar na transição** página. Escolha se pretende [ativar ranhuras de implementação](deploy-staging-slots.md), em seguida, clique em **continuar**.

### <a name="finish-configuration"></a>Concluir a configuração

Na **resumo** página, verifique as suas opções e clique em **concluir**.

Quando tiver concluído a configuração, novas consolidações no repositório selecionado são continuamente implementadas na sua aplicação de serviço de aplicações.

![](media/app-service-continuous-deployment/github-finished.png)

## <a name="deploy-continuously-from-bitbucket"></a>Implementar continuamente do BitBucket

Para ativar a implementação contínua com o BitBucket, navegue até à página da aplicação do serviço de aplicações na [portal do Azure](https://portal.azure.com).

No menu à esquerda, clique em **Deployment Center** > **BitBucket** > **autorizar**. Siga as instruções de autorização. 

![](media/app-service-continuous-deployment/bitbucket-choose-source.png)

Apenas terá de autorizar com o BitBucket, uma vez. Se já estiver autorizado, basta clicar **continuar**. Pode alterar a conta autorizado do BitBucket clicando **alterar conta**.

![](media/app-service-continuous-deployment/bitbucket-continue.png)

Na **configurar** , selecione o repositório e ramo a partir do qual deseja implantar continuamente. Quando terminar, clique em **continuar**.

Na **resumo** página, verifique as suas opções e clique em **concluir**.

Quando tiver concluído a configuração, novas consolidações no repositório selecionado são continuamente implementadas na sua aplicação de serviço de aplicações.

## <a name="deploy-continuously-from-azure-repos-devops-services"></a>Implementar continuamente a partir de repositórios do Azure (Serviços de DevOps)

Para ativar a implementação contínua com a partir [Azure repositórios](https://docs.microsoft.com/azure/devops/repos/index), navegue até à página da aplicação do serviço de aplicações na [portal do Azure](https://portal.azure.com).

No menu à esquerda, clique em **Deployment Center** > **repositórios do Azure** > **continuar**. 

![](media/app-service-continuous-deployment/vsts-choose-source.png)

Na **fornecedor de compilação** página, escolha o fornecedor de compilação e clique em > **continuar**.

> [!NOTE]
> Se pretender utilizar uma organização de serviços de DevOps do Azure existente que não esteja listada, terá [associar a organização de serviços de DevOps do Azure à sua subscrição do Azure](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App).

### <a name="option-1-use-app-service-kudu-build-server"></a>Opção 1: utilizar o servidor de compilação Kudu do serviço de aplicações

Na **configurar** , selecione a organização de serviços do Azure DevOps, projeto, repositório e ramo partir do qual deseja implantar continuamente. Quando terminar, clique em **continuar**.

### <a name="option-2-use-azure-devops-services-continuous-delivery"></a>Opção 2: utilizar a entrega contínua de serviços do Azure DevOps

> [!NOTE]
> Para o serviço de aplicações para criar Pipelines do Azure necessários na sua organização de serviços do Azure DevOps, sua conta do Azure tem de ter a função de **proprietário** na sua subscrição do Azure.
>

No **configurar** página, além da **código** secção, selecione a organização de serviços do Azure DevOps, projeto, repositório e ramo partir do qual deseja implantar continuamente. Quando terminar, clique em **continuar**.

Na **configurar** página, além do **criar** secção, especifique a estrutura de idioma que os serviços do Azure DevOps deve utilizar para executar as tarefas de compilação para o repositório selecionado. Quando terminar, clique em **continuar**.

Na **teste** página, escolha se pretende ativar a testes de carga, em seguida, clique em **continuar**.

Consoante a [escalão de preço](https://azure.microsoft.com/pricing/details/app-service/plans/) do seu plano do serviço de aplicações, também poderá ver um **implementar na transição** página. Escolha se pretende [ativar ranhuras de implementação](deploy-staging-slots.md), em seguida, clique em **continuar**. 

### <a name="finish-configuration"></a>Concluir a configuração

Na **resumo** página, verifique as suas opções e clique em **concluir**.

Quando tiver concluído a configuração, novas consolidações no repositório selecionado são continuamente implementadas na sua aplicação de serviço de aplicações.

## <a name="disable-continuous-deployment"></a>Desativar a implementação contínua

Para desativar a implementação contínua, navegue até à página da aplicação do serviço de aplicações na [portal do Azure](https://portal.azure.com).

No menu à esquerda, clique em **Deployment Center** > **GitHub** ou **serviços do Azure DevOps** ou **BitBucket**  >  **Desligar**.

![](media/app-service-continuous-deployment/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="additional-resources"></a>Recursos Adicionais

* [Como investigar problemas comuns da implementação contínua](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Como utilizar o PowerShell para o Azure]
* [Documentação do Git]
* [Kudu do projeto](https://github.com/projectkudu/kudu/wiki)
* [Utilize o Azure para gerar automaticamente um pipeline de CI/CD para implementar uma aplicação de ASP.NET 4](https://www.visualstudio.com/docs/build/get-started/aspnet-4-ci-cd-azure-automatic)

[Azure portal]: https://portal.azure.com
[Azure DevOps portal]: https://azure.microsoft.com/services/devops/
[Installing Git]: https://git-scm.com/book/en/Getting-Started-Installing-Git
[Como utilizar o PowerShell para o Azure]: /powershell/azureps-cmdlets-docs
[Documentação do Git]: https://git-scm.com/documentation

[Criar um repositório (GitHub)]: https://help.github.com/articles/create-a-repo
[Criar um repositório (BitBucket)]: https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html
[Introdução aos serviços do Azure DevOps]: https://docs.microsoft.com/azure/devops/user-guide/devops-alm-overview
