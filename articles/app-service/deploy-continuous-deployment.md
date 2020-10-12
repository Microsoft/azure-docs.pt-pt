---
title: Configurar a implementação contínua
description: Saiba como permitir o serviço de aplicações ci/CD do GitHub, BitBucket, Azure Repos ou outros repos. Selecione o gasoduto de construção que se adequa às suas necessidades.
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.topic: article
ms.date: 03/20/2020
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: b7730558e2a660b0cf00a5b6962d1e2275dd472c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90984392"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Implementação contínua para o Serviço de Aplicações Azure

[O Azure App Service](overview.md) permite a implementação contínua dos repositórios gitHub, BitBucket e [Azure Repos,](https://azure.microsoft.com/services/devops/repos/) puxando as últimas atualizações. Este artigo mostra-lhe como utilizar o portal Azure para implementar continuamente a sua aplicação através do serviço de construção Kudu ou [pipelines Azure](https://azure.microsoft.com/services/devops/pipelines/). 

Para obter mais informações sobre os serviços de controlo de origem, consulte [Criar um repo (GitHub)], [Criar um repo (BitBucket)]ou [Criar um novo git repo (Azure Repos)].

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="authorize-azure-app-service"></a>Autorizar o Serviço de Aplicações Azure 

Para utilizar o Azure Repos, certifique-se de que a sua organização Azure DevOps está ligada à sua subscrição Azure. Para obter mais informações, consulte [Configurar uma conta Azure DevOps Services para que possa ser implementada numa aplicação web.](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops&preserve-view=true)

Para o Bitbucket ou o GitHub, autorize o Serviço de Aplicações Azure a ligar-se ao seu repositório. Só precisa de autorizar um serviço de controlo de fonte uma vez. 

1. No [portal Azure,](https://portal.azure.com)procure serviços de  **aplicações** e selecione.

   ![Procure por serviços de aplicações.](media/app-service-continuous-deployment/search-for-app-services.png)

1. Selecione o Serviço de Aplicações que pretende implementar.

   ![Selecione a sua aplicação.](media/app-service-continuous-deployment/select-your-app.png)
   
1. Na página da aplicação, selecione **O Centro de Implementação** no menu esquerdo.
   
1. Na página **'Centro de Implantação',** selecione **GitHub** ou **Bitbucket**e, em seguida, selecione **Authorize**. 
   
   ![Selecione o serviço de controlo de fontes e, em seguida, selecione Authorize.](media/app-service-continuous-deployment/github-choose-source.png)
   
1. Inscreva-se no serviço se necessário e siga as indicações de autorização. 

## <a name="enable-continuous-deployment"></a>Ativar a implementação contínua 

Depois de autorizar um serviço de controlo de fontes, configuure a sua aplicação para implementação contínua através do servidor de construção do [Kudu App Service](#option-1-kudu-app-service) incorporado, ou através de [Azure Pipelines](#option-2-azure-pipelines). 

### <a name="option-1-kudu-app-service"></a>Opção 1: Serviço de Aplicações Kudu

Você pode usar o servidor de construção de aplicações Kudu incorporado para implementar continuamente a partir de GitHub, Bitbucket ou Azure Repos. 

1. No [portal Azure,](https://portal.azure.com)procure **serviços de aplicações**e, em seguida, selecione o Serviço de Aplicações que pretende implementar. 
   
1. Na página da aplicação, selecione **O Centro de Implementação** no menu esquerdo.
   
1. Selecione o seu fornecedor autorizado de controlo de fonte na página **'Centro de Implantação'** e selecione **Continue**. Para GitHub ou Bitbucket, também pode selecionar alterar a **conta Change** para alterar a conta autorizada. 
   
   > [!NOTE]
   > Para utilizar o Azure Repos, certifique-se de que a sua organização Azure DevOps Services está ligada à sua subscrição Azure. Para obter mais informações, consulte [Configurar uma conta Azure DevOps Services para que possa ser implementada numa aplicação web.](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops&preserve-view=true)
   
1. Para GitHub ou Azure Repos, na página do **fornecedor Build,** selecione o **serviço de construção do Serviço de Aplicações**e, em seguida, selecione **Continue**. A Bitbucket usa sempre o serviço de construção do Serviço de Aplicações.
   
   ![Selecione o serviço de construção do Serviço de Aplicações e, em seguida, selecione Continue.](media/app-service-continuous-deployment/choose-kudu.png)
   
1. Na página **Configure:**
   
   - Para o GitHub, desça e selecione a **Organização,** **Repositório**e **Ramo** que pretende implementar continuamente.
     
     > [!NOTE]
     > Se não vir nenhum repositório, poderá ter de autorizar o Serviço de Aplicações Azure no GitHub. Navegue no seu repositório GitHub e vá a **Configurações**  >  **Aplicações**  >  **OAuth Apps autorizadas**. Selecione **O Serviço de Aplicações Azure**e, em seguida, selecione **Grant**. Para repositórios de organização, você deve ser um proprietário da organização para conceder as permissões.
     
   - Para o Bitbucket, selecione a Bitbucket **Team,** **Repository**e **Branch** que pretende implementar continuamente.
     
   - Para Azure Repos, selecione a **Azure DevOps Organization**, **Project,** **Repository**e **Branch** que pretende implementar continuamente.
     
     > [!NOTE]
     > Se a sua organização do Azure DevOps não estiver listada, confirme se está associada à sua subscrição do Azure. Para obter mais informações, consulte [Configurar uma conta Azure DevOps Services para que possa ser implementada numa aplicação web](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops&preserve-view=true)..
     
1. Selecione **Continuar**.
   
   ![Preencha as informações do repositório e, em seguida, selecione Continue.](media/app-service-continuous-deployment/configure-kudu.png)
   
1. Depois de configurar o fornecedor de construção, reveja as definições na página **Resumo** e, em seguida, selecione **Terminar**.
   
1. Novos compromissos no repositório selecionado e ramo agora implementam-se continuamente na sua aplicação de Serviço de Aplicações. Pode rastrear os compromissos e implementações na página **Do Centro de Implantação.**
   
   ![A pista compromete-se e implementa no Centro de Implantação](media/app-service-continuous-deployment/github-finished.png)

### <a name="option-2-azure-pipelines"></a>Opção 2: Gasodutos Azure 

Se a sua conta tiver as permissões necessárias, pode configurar a Azure Pipelines para implantar continuamente a partir do GitHub ou Azure Repos. Para obter mais informações sobre a implementação através de Azure Pipelines, consulte [implementar uma aplicação web para Azure App Services](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps).

#### <a name="prerequisites"></a>Pré-requisitos

Para o Azure App Service criar uma entrega contínua utilizando a Azure Pipelines, a sua organização Azure DevOps deve ter as seguintes permissões: 

- A sua conta Azure deve ter permissões para escrever ao Azure Ative Directory e criar um serviço. 
  
- A sua conta Azure deve ter a função **de Proprietário** na sua subscrição Azure.

- Deve ser administrador no projeto Azure DevOps que pretende utilizar.

#### <a name="github--azure-pipelines"></a>Oleodutos GitHub + Azure

1. No [portal Azure,](https://portal.azure.com)procure **serviços de aplicações**e, em seguida, selecione o Serviço de Aplicações que pretende implementar. 
   
1. Na página da aplicação, selecione **O Centro de Implementação** no menu esquerdo.

1. Selecione **GitHub** como fornecedor de controlo de origem na página **do Centro de Implantação** e selecione **Continue**. Para **o GitHub,** pode selecionar Alterar a **Conta** para alterar a conta autorizada.

    :::image type="content" source="media/app-service-continuous-deployment/deployment-center-src-control.png" alt-text="Screenshot da página do Centro de Implementação do Serviço de Aplicações.":::
   
1. Na página **'Fornecedor de construção',** selecione **Azure Pipelines (Preview)** e, em seguida, selecione **Continue**.

    :::image type="content" source="media/app-service-continuous-deployment/select-build-provider.png" alt-text="Screenshot da página do Centro de Implementação do Serviço de Aplicações.":::
   
1. Na página **Configure,** na secção **Código,** selecione a **Organização,** **Repositório**e **Ramo** que pretende implementar continuamente e selecione **Continuar**.
     
     > [!NOTE]
     > Se não vir nenhum repositório, poderá ter de autorizar o Serviço de Aplicações Azure no GitHub. Navegue no seu repositório GitHub e vá a **Configurações**  >  **Aplicações**  >  **OAuth Apps autorizadas**. Selecione **O Serviço de Aplicações Azure**e, em seguida, selecione **Grant**. Para repositórios de organização, você deve ser um proprietário da organização para conceder as permissões.
       
    Na secção **Build,** especifique a Organização Azure DevOps, Projeto, enquadramento linguístico que os Gasodutos Azure devem utilizar para executar tarefas de construção e, em seguida, **selecione Continue**.

   :::image type="content" source="media/app-service-continuous-deployment/build-configure.png" alt-text="Screenshot da página do Centro de Implementação do Serviço de Aplicações.":::

1. Depois de configurar o fornecedor de construção, reveja as definições na página **Resumo** e, em seguida, selecione **Terminar**.

   :::image type="content" source="media/app-service-continuous-deployment/summary.png" alt-text="Screenshot da página do Centro de Implementação do Serviço de Aplicações.":::
   
1. Novos compromissos no repositório selecionado e ramo agora implementam-se continuamente no seu Serviço de Aplicações. Pode rastrear os compromissos e implementações na página **Do Centro de Implantação.**
   
   ![A pista compromete-se e implementa no Centro de Implantação](media/app-service-continuous-deployment/github-finished.png)

#### <a name="azure-repos--azure-pipelines"></a>Azure Repos + Gasodutos Azure

1. No [portal Azure,](https://portal.azure.com)procure **serviços de aplicações**e, em seguida, selecione o Serviço de Aplicações que pretende implementar. 
   
1. Na página da aplicação, selecione **O Centro de Implementação** no menu esquerdo.

1. Selecione **Azure Repos** como fornecedor de controlo de origem na página **do Centro de Implantação** e selecione **Continue**.

    :::image type="content" source="media/app-service-continuous-deployment/deployment-center-src-control.png" alt-text="Screenshot da página do Centro de Implementação do Serviço de Aplicações.":::

1. Na página **'Fornecedor de construção',** selecione **Azure Pipelines (Preview)** e, em seguida, selecione **Continue**.

    :::image type="content" source="media/app-service-continuous-deployment/azure-pipelines.png" alt-text="Screenshot da página do Centro de Implementação do Serviço de Aplicações.":::

1. Na página **Configure,** na secção **Código,** selecione a **Organização,** **Repositório**e **Ramo** que pretende implementar continuamente e selecione **Continuar**.

   > [!NOTE]
   > Se a sua organização Azure DevOps existente não estiver listada, poderá ter de ligá-la à sua subscrição Azure. Para obter mais informações, consulte [Definir o seu pipeline de desbloqueio de CD](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd).

   Na secção **Build,** especifique a Organização Azure DevOps, Projeto, enquadramento linguístico que os Gasodutos Azure devem utilizar para executar tarefas de construção e, em seguida, **selecione Continue**.

   :::image type="content" source="media/app-service-continuous-deployment/build-configure.png" alt-text="Screenshot da página do Centro de Implementação do Serviço de Aplicações.":::

1. Depois de configurar o fornecedor de construção, reveja as definições na página **Resumo** e, em seguida, selecione **Terminar**.  
     
   :::image type="content" source="media/app-service-continuous-deployment/summary-azure-pipelines.png" alt-text="Screenshot da página do Centro de Implementação do Serviço de Aplicações.":::

1. Novos compromissos no repositório selecionado e ramo agora implementam-se continuamente no seu Serviço de Aplicações. Pode rastrear os compromissos e implementações na página **Do Centro de Implantação.**

## <a name="disable-continuous-deployment"></a>Desativar a implementação contínua

Para desativar a implementação contínua, **selecione Desligar** na parte superior da página do Centro de **Implementação** da sua aplicação.

![Desativar a implementação contínua](media/app-service-continuous-deployment/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="use-unsupported-repos"></a>Use repos não suportados

Para aplicações windows, pode configurar manualmente a implementação contínua a partir de um repositório de Nuvem Git ou Mercurial que o portal não suporta diretamente, como [o GitLab.](https://gitlab.com/) Fá-lo escolhendo a caixa Externa na página **Centro de Implantação.** Para obter mais informações, consulte [Configurar a implementação contínua utilizando passos manuais.](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps)

## <a name="additional-resources"></a>Recursos adicionais

* [Investigar questões comuns com implantação contínua](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Utilizar o Azure PowerShell](/powershell/azure/)
* [Documentação de Git](https://git-scm.com/documentation)
* [Kudu do projeto](https://github.com/projectkudu/kudu/wiki)

[Criar um repositório (GitHub)]: https://help.github.com/articles/create-a-repo
[Criar um repositório (BitBucket)]: https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html
[Criar um novo git repo (Azure Repos)]: /azure/devops/repos/git/creatingrepo