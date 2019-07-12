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
ms.date: 06/18/2019
ms.author: cephalin;dariagrigoriu
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 3a207f5513c24f2c33571df929f7490ec7f2eba5
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836749"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Implementação contínua no App Service do Azure

[Serviço de aplicações do Azure](overview.md) permite a implementação contínua do GitHub, BitBucket, e [repositórios de Azure](https://azure.microsoft.com/services/devops/repos/) repositórios Pegando as atualizações mais recentes. Este artigo mostra-lhe como utilizar o portal do Azure para implementar continuamente a sua aplicação através do serviço de compilação Kudu ou [Pipelines do Azure](https://azure.microsoft.com/services/devops/pipelines/). 

Para obter mais informações sobre os serviços de controle de origem, consulte [criar um repositório (GitHub)], [criar um repositório (BitBucket)], ou [Criar um novo repositório de Git (repositórios do Azure)].

Configurar manualmente a partir de um repositório de cloud que o portal não oferecer suporte direto, tais como a implementação contínua [GitLab](https://gitlab.com/), consulte [configurar a implementação contínua com passos manuais](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps).

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="authorize-azure-app-service"></a>Autorizar o serviço de aplicações do Azure 

Para utilizar o Azure repositórios, certifique-se de que a sua organização de DevOps do Azure está ligada à sua subscrição do Azure. Para obter mais informações, consulte [configurar uma conta dos serviços de DevOps do Azure, para que ele pode implementar uma aplicação web](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App).

Para o Bitbucket ou do GitHub, autorize o serviço de aplicações do Azure para ligar ao seu repositório. Apenas terá de autorizar com um serviço de controle de origem, uma vez. 

1. Selecione **dos serviços de aplicações** no [portal do Azure](https://portal.azure.com) deixou de navegação e, em seguida, selecione a aplicação web que pretende implementar. 
   
1. Na página da aplicação, selecione **Deployment Center** no menu à esquerda.
   
1. Sobre o **Deployment Center** página, selecione **GitHub** ou **Bitbucket**e, em seguida, selecione **autorizar**. 
   
   ![Selecione o serviço de controle de origem, em seguida, selecione autorizar.](media/app-service-continuous-deployment/github-choose-source.png)
   
1. Inicie sessão no serviço se necessário e siga as instruções de autorização. 

## <a name="enable-continuous-deployment"></a>Ativar a implementação contínua 

Depois de autorizar um serviço de controle de origem, configurar a sua aplicação para a implementação contínua através de incorporada [Kudu do serviço de aplicações de servidor de compilação](#option-1-use-the-app-service-build-service), ou pelo [Pipelines do Azure](#option-2-use-azure-pipelines). 

### <a name="option-1-use-the-app-service-build-service"></a>Opção 1: Utilizar o serviço de compilação do serviço de aplicações

Pode usar o incorporado Kudu do serviço de aplicações criar servidor para implementar continuamente a partir do GitHub, Bitbucket ou de repositórios do Azure. 

1. Selecione **dos serviços de aplicações** no [portal do Azure](https://portal.azure.com) deixou de navegação e, em seguida, selecione a aplicação web que pretende implementar. 
   
1. Na página da aplicação, selecione **Deployment Center** no menu à esquerda.
   
1. Selecione o seu fornecedor de controlo de origem autorizados no **Deployment Center** página e selecione **continuar**. Para o GitHub ou Bitbucket, também pode selecionar **alterar conta** para alterar a conta autorizada. 
   
   > [!NOTE]
   > Para utilizar o Azure repositórios, certifique-se de que a sua organização de serviços de DevOps do Azure está ligada à sua subscrição do Azure. Para obter mais informações, consulte [configurar uma conta dos serviços de DevOps do Azure, para que ele pode implementar uma aplicação web](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App).
   
1. Para o GitHub ou de repositórios do Azure, sobre o **fornecedor de compilação** página, selecione **serviço de compilação do serviço de aplicações**e, em seguida, selecione **continuar**. Bitbucket utiliza sempre o serviço de compilação do serviço de aplicações.
   
   ![Selecione o serviço de compilação do serviço de aplicações, em seguida, selecionar continuar.](media/app-service-continuous-deployment/choose-kudu.png)
   
1. Sobre o **configurar** página:
   
   - Para o GitHub, menu pendente e selecione o **organização**, **repositório**, e **ramo** deseja implantar continuamente.
     
     > [!NOTE]
     > Se não vir quaisquer repositórios, terá de autorizar o serviço de aplicações do Azure no GitHub. Navegue para o seu repositório do GitHub e aceda a **configurações** > **aplicativos** > **aplicações de autorização de OAuth**. Selecione **App Service do Azure**e, em seguida, selecione **concessão**.
     
   - Para o Bitbucket, selecione o Bitbucket **equipe**, **repositório**, e **ramo** deseja implantar continuamente.
     
   - Para repositórios do Azure, selecione o **organização de DevOps do Azure**, **projeto**, **repositório**, e **ramo** deseja implantar continuamente.
     
     > [!NOTE]
     > Se sua organização de DevOps do Azure não estiver listada, certifique-se de que está ligado à sua subscrição do Azure. Para obter mais informações, consulte [configurar uma conta dos serviços de DevOps do Azure, para que ele pode implementar uma aplicação web](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App)....
     
1. Selecione **Continuar**.
   
   ![Preencha as informações do repositório e, em seguida, selecionar continuar.](media/app-service-continuous-deployment/configure-kudu.png)
   
1. Depois de configurar o fornecedor de compilação, reveja as definições na **resumo** página e, em seguida, selecione **concluir**.
   
   Novas consolidações no repositório selecionado e o ramo agora implementam continuamente na sua aplicação de serviço de aplicações. Pode acompanhar o será confirmada e Implantações no **Deployment Center** página.
   
   ![Controlar será confirmada e Implantações no Centro de implementação](media/app-service-continuous-deployment/github-finished.png)

### <a name="option-2-use-azure-pipelines"></a>Opção 2: Utilize o Azure Pipelines 

Se a sua conta tiver as permissões necessárias, pode configurar Pipelines do Azure para implementar continuamente a partir de repositórios de GitHub ou de repositórios do Azure. Para obter mais informações sobre a implantação por meio de Pipelines do Azure, consulte [implementar uma aplicação web para serviços de aplicações do Azure](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps).

Para o serviço de aplicações do Azure para criar Pipelines do Azure a entrega contínua na sua organização de DevOps do Azure: 

- Sua conta do Azure tem de ter permissões para escrever para o Azure Active Directory e criar um serviço. 
  
- Sua conta do Azure tem de ter o **proprietário** função na sua subscrição do Azure.

- Tem de ser um administrador do projeto de DevOps do Azure que pretende utilizar.

Para configurar Pipelines do Azure (pré-visualização):

1. Selecione **dos serviços de aplicações** no [portal do Azure](https://portal.azure.com) deixou de navegação e, em seguida, selecione a aplicação web que pretende implementar. 
   
1. Na página da aplicação, selecione **Deployment Center** no menu à esquerda.
   
1. Sobre o **fornecedor de compilação** página, selecione **Pipelines do Azure (pré-visualização)** e, em seguida, selecione **continuar**. 
   
1. Na **configurar** página, além do **código** secção:
   
   - Para o GitHub, menu pendente e selecione o **organização**, **repositório**, e **ramo** deseja implantar continuamente.
     
     > [!NOTE]
     > Se não vir quaisquer repositórios, terá de autorizar o serviço de aplicações do Azure no GitHub. Navegue para o seu repositório do GitHub e aceda a **configurações** > **aplicativos** > **aplicações de autorização de OAuth**. Selecione **App Service do Azure**e, em seguida, selecione **concessão**.
     
   - Para repositórios do Azure, selecione o **organização de DevOps do Azure**, **projeto**, **repositório**, e **ramo** que pretende implementar continuamente, ou configurar uma nova organização do Azure DevOps.
     
     > [!NOTE]
     > Se não estiver listada a sua organização de DevOps do Azure existente, terá de ligá-lo à sua subscrição do Azure. Para obter mais informações, consulte [definir o seu pipeline de lançamento do CD](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd).
     
1. Selecione **Continuar**.
   
1. Para repositórios do Azure, no **crie** secção, especifique a estrutura de idioma que Pipelines do Azure devem utilizar para executar tarefas de compilação e, em seguida, selecione **continuar**.
   
1. Sobre o **teste** página, selecione se pretende ativar a testes de carga e, em seguida, selecione **continuar**.
   
1. Dependendo do seu plano do serviço de aplicações [escalão de preço](https://azure.microsoft.com/pricing/details/app-service/plans/), poderá ver um **implementar na transição** página. Escolha se pretende [ativar ranhuras de implementação](deploy-staging-slots.md)e, em seguida, selecione **continuar**.
   
   > [!NOTE]
   > Pipelines do Azure não permite a entrega contínua para o bloco de produção. Esta limitação impede acidentais implementações para produção. Configurar a entrega contínua para um bloco de teste, verifique se existem as alterações e, em seguida, trocar as ranhuras quando estiver pronto.
   
1. Depois de configurar o fornecedor de compilação, reveja as definições na **resumo** página e, em seguida, selecione **concluir**.
   
   Novas consolidações no repositório selecionado e o ramo agora implementam continuamente na sua aplicação de serviço de aplicações. Pode acompanhar o será confirmada e Implantações no **Deployment Center** página.
   
   ![Controlar será confirmada e Implantações no Centro de implementação](media/app-service-continuous-deployment/github-finished.png)

## <a name="disable-continuous-deployment"></a>Desativar a implementação contínua

Para desativar a implementação contínua, selecione **desligar** na parte superior da sua aplicação **Deployment Center** página.

![Desativar a implementação contínua](media/app-service-continuous-deployment/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="additional-resources"></a>Recursos adicionais

* [Investigar problemas comuns com a implementação contínua](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Utilizar o Azure PowerShell](/powershell/azureps-cmdlets-docs)
* [Documentação do Git](https://git-scm.com/documentation)
* [Kudu do projeto](https://github.com/projectkudu/kudu/wiki)

[Criar um repositório (GitHub)]: https://help.github.com/articles/create-a-repo
[Criar um repositório (BitBucket)]: https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html
[Criar um novo repositório de Git (repositórios do Azure)]: /azure/devops/repos/git/creatingrepo
