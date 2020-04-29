---
title: Configurar a implementação contínua
description: Saiba como ativar o SERVIÇO de Aplicações Ci/CD do Azure App Service do GitHub, BitBucket, Azure Repos ou outros repos. Selecione o pipeline de construção que se adequa às suas necessidades.
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.topic: article
ms.date: 03/20/2020
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 847de2c2c8916558d542473d9b7c80fd5552dbf7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80437254"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Implantação contínua para o Serviço de Aplicações Azure

O Serviço de [Aplicações Azure](overview.md) permite a implantação contínua dos repositórios GitHub, BitBucket e [Azure Repos,](https://azure.microsoft.com/services/devops/repos/) puxando as últimas atualizações. Este artigo mostra-lhe como usar o portal Azure para implantar continuamente a sua app através do serviço de construção Kudu ou [dos Oleodutos Azure](https://azure.microsoft.com/services/devops/pipelines/). 

Para obter mais informações sobre os serviços de controlo de fontes, consulte [Criar um repo (GitHub)], [Criar um repo (BitBucket)]ou [Criar um novo Repo Git (Azure Repos)].

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="authorize-azure-app-service"></a>Autorizar o Serviço de Aplicações Azure 

Para utilizar o Azure Repos, certifique-se de que a sua organização Azure DevOps está ligada à sua subscrição Azure. Para mais informações, consulte [A configuração de uma conta Azure DevOps Services](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops)para que possa ser implementada para uma aplicação web .

Para bitbucket ou GitHub, autorize o Serviço de Aplicações Azure a ligar-se ao seu repositório. Só precisa autorizar com um serviço de controlo de fontes uma vez. 

1. No [portal Azure,](https://portal.azure.com)procure serviços de **aplicações** e selecione.

   ![Pesquisa de serviços de Aplicações.](media/app-service-continuous-deployment/search-for-app-services.png)

1. Selecione o Serviço de Aplicações que pretende implementar.

   ![Selecione a sua aplicação.](media/app-service-continuous-deployment/select-your-app.png)
   
1. Na página da aplicação, selecione **Deployment Center** no menu esquerdo.
   
1. Na página do Centro de **Implementação,** selecione **GitHub** ou **Bitbucket,** e, em seguida, selecione **Autorizar**. 
   
   ![Selecione o serviço de controlo de fontee, em seguida, selecione Autorizar.](media/app-service-continuous-deployment/github-choose-source.png)
   
1. Inscreva-se no serviço se necessário e siga as indicações de autorização. 

## <a name="enable-continuous-deployment"></a>Ativar a implementação contínua 

Depois de autorizar um serviço de controlo de fontes, configure a sua aplicação para uma implementação contínua através do servidor de construção de [apps Kudu](#option-1-kudu-app-service) incorporado, ou através de [Pipelines Azure](#option-2-azure-pipelines). 

### <a name="option-1-kudu-app-service"></a>Opção 1: Serviço de Aplicações Kudu

Pode utilizar o servidor de construção de aplicativos Kudu incorporado para implantar continuamente a partir de GitHub, Bitbucket ou Azure Repos. 

1. No [portal Azure,](https://portal.azure.com)procure serviços de **aplicações**e, em seguida, selecione o Serviço de Aplicações que pretende implementar. 
   
1. Na página da aplicação, selecione **Deployment Center** no menu esquerdo.
   
1. Selecione o seu fornecedor de controlo de fonte autorizado na página do Centro de **Implementação** e selecione **Continuar**. Para GitHub ou Bitbucket, também pode selecionar a **conta Alterar** para alterar a conta autorizada. 
   
   > [!NOTE]
   > Para utilizar o Azure Repos, certifique-se de que a sua organização Azure DevOps Services está ligada à sua subscrição Azure. Para mais informações, consulte [A configuração de uma conta Azure DevOps Services](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops)para que possa ser implementada para uma aplicação web .
   
1. Para o GitHub ou Azure Repos, na página do **fornecedor Build,** selecione o serviço de construção do **Serviço de Aplicações,** e depois selecione **Continuar**. O Bitbucket usa sempre o serviço de construção do Serviço de Aplicações.
   
   ![Selecione serviço de construção de serviço de aplicações e, em seguida, selecione Continuar.](media/app-service-continuous-deployment/choose-kudu.png)
   
1. Na página **Configure:**
   
   - Para o GitHub, desça e selecione a **Organização**, **Repositório**e **Ramo** que pretende implementar continuamente.
     
     > [!NOTE]
     > Se não vir quaisquer repositórios, poderá ter de autorizar o Azure App Service no GitHub. Navegue no seu repositório GitHub e vá a**Aplicações OAuth autorizadas**de > **Definições.** >  **Settings** Selecione O Serviço de **Aplicações Azure**e, em seguida, selecione **Grant**. Para os repositórios da organização, você deve ser um proprietário da organização para conceder as permissões.
     
   - Para bitbucket, selecione a **Equipa**Bitbucket, **repositório**e **Branch** que pretende implementar continuamente.
     
   - Para o Azure Repos, selecione a **Organização Azure DevOps**, **Projeto,** **Repositório**e **Ramo** que pretende implementar continuamente.
     
     > [!NOTE]
     > Se a sua organização Azure DevOps não estiver listada, certifique-se de que está ligada à sua subscrição Azure. Para mais informações, consulte [A configuração de uma conta Azure DevOps Services](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops)para que possa ser implementada para uma aplicação web ..
     
1. Selecione **Continuar**.
   
   ![Preencha as informações de repositório e, em seguida, selecione Continuar.](media/app-service-continuous-deployment/configure-kudu.png)
   
1. Depois de configurar o fornecedor de construção, reveja as definições na página **Resumo** e, em seguida, selecione **Terminar**.
   
1. Novos compromissos no repositório e sucursal selecionados agora implantam-se continuamente na sua app App Service. Pode rastrear os compromissos e implementações na página do Centro de **Implantação.**
   
   ![Track compromete e implanta no Centro de Implantação](media/app-service-continuous-deployment/github-finished.png)

### <a name="option-2-azure-pipelines"></a>Opção 2: Gasodutos Azure 

Se a sua conta tiver as permissões necessárias, pode configurar os Gasodutos Azure para implantar continuamente a partir do GitHub ou do Azure Repos. Para obter mais informações sobre a implementação através dos Oleodutos Azure, consulte [a Implementação de uma aplicação web para](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps)o Azure App Services .

#### <a name="prerequisites"></a>Pré-requisitos

Para que o Serviço de Aplicações Azure crie entregas contínuas utilizando pipelines Azure, a sua organização Azure DevOps deve ter as seguintes permissões: 

- A sua conta Azure deve ter permissões para escrever ao Azure Ative Directory e criar um serviço. 
  
- A sua conta Azure deve ter o papel **de Proprietário** na sua subscrição Azure.

- Deve ser administrador no projeto Azure DevOps que pretende utilizar.

#### <a name="github--azure-pipelines"></a>Gasodutos GitHub + Azure

1. No [portal Azure,](https://portal.azure.com)procure serviços de **aplicações**e, em seguida, selecione o Serviço de Aplicações que pretende implementar. 
   
1. Na página da aplicação, selecione **Deployment Center** no menu esquerdo.

1. Selecione **gitHub** como o fornecedor de controlo de fonte na página do Centro de **Implementação** e selecione **Continuar**. Para **o GitHub,** pode selecionar a **Conta De Alteração** para alterar a conta autorizada.

    ![controlo de fontes](media/app-service-continuous-deployment/deployment-center-src-control.png)
   
1. Na página **'Fornecedor de Construção',** selecione **Pipelines Azure (Pré-visualização)**, e, em seguida, selecione **Continuar**.

    ![provedor de construção](media/app-service-continuous-deployment/select-build-provider.png)
   
1. Na página **Configure,** na secção **Código,** selecione a **Organização,** **Repositório**e **Ramo** que pretende implementar continuamente e selecionar **Continuar**.
     
     > [!NOTE]
     > Se não vir quaisquer repositórios, poderá ter de autorizar o Azure App Service no GitHub. Navegue no seu repositório GitHub e vá a**Aplicações OAuth autorizadas**de > **Definições.** >  **Settings** Selecione O Serviço de **Aplicações Azure**e, em seguida, selecione **Grant**. Para os repositórios da organização, você deve ser um proprietário da organização para conceder as permissões.
       
    Na secção **Build,** especifique a Organização Azure DevOps, Projeto, enquadramento linguístico que os Oleodutos Azure devem usar para executar tarefas de construção e, em seguida, selecionar **Continuar**.

   ![provedor de construção](media/app-service-continuous-deployment/build-configure.png)

1. Depois de configurar o fornecedor de construção, reveja as definições na página **Resumo** e, em seguida, selecione **Terminar**.

   ![provedor de construção](media/app-service-continuous-deployment/summary.png)
   
1. Novos compromissos no repositório e sucursal selecionados agora implantam-se continuamente no seu Serviço de Aplicações. Pode rastrear os compromissos e implementações na página do Centro de **Implantação.**
   
   ![Track compromete e implanta no Centro de Implantação](media/app-service-continuous-deployment/github-finished.png)

#### <a name="azure-repos--azure-pipelines"></a>Oleodutos Azure Repos + Azure

1. No [portal Azure,](https://portal.azure.com)procure serviços de **aplicações**e, em seguida, selecione o Serviço de Aplicações que pretende implementar. 
   
1. Na página da aplicação, selecione **Deployment Center** no menu esquerdo.

1. Selecione **O Azure Repos** como fornecedor de controlo de fontes na página do Centro de **Implantação** e selecione **Continuar**.

    ![controlo de fontes](media/app-service-continuous-deployment/deployment-center-src-control.png)

1. Na página **'Fornecedor de Construção',** selecione **Pipelines Azure (Pré-visualização)**, e, em seguida, selecione **Continuar**.

    ![controlo de fontes](media/app-service-continuous-deployment/azure-pipelines.png)

1. Na página **Configure,** na secção **Código,** selecione a **Organização,** **Repositório**e **Ramo** que pretende implementar continuamente e selecionar **Continuar**.

   > [!NOTE]
   > Se a sua organização Azure DevOps existente não estiver listada, poderá ter de ligá-la à sua subscrição Azure. Para mais informações, consulte [Defina o seu pipeline](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd)de lançamento de CD .

   Na secção **Build,** especifique a Organização Azure DevOps, Projeto, enquadramento linguístico que os Oleodutos Azure devem usar para executar tarefas de construção e, em seguida, selecionar **Continuar**.

   ![provedor de construção](media/app-service-continuous-deployment/build-configure.png)

1. Depois de configurar o fornecedor de construção, reveja as definições na página **Resumo** e, em seguida, selecione **Terminar**.  
     
   ![provedor de construção](media/app-service-continuous-deployment/summary-azure-pipelines.png)

1. Novos compromissos no repositório e sucursal selecionados agora implantam-se continuamente no seu Serviço de Aplicações. Pode rastrear os compromissos e implementações na página do Centro de **Implantação.**

## <a name="disable-continuous-deployment"></a>Desativar a implementação contínua

Para desativar a implementação contínua, selecione **Disconnect** na parte superior da página do Centro de **Implementação** da sua aplicação.

![Desativar a implementação contínua](media/app-service-continuous-deployment/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="use-unsupported-repos"></a>Use repos não apoiados

Para aplicações Windows, pode configurar manualmente a implementação contínua a partir de um rescrito git ou mercurial que o portal não suporta diretamente, como o [GitLab](https://gitlab.com/). Faça-o escolhendo a caixa Externa na página do Centro de **Implantação.** Para mais informações, consulte [A instalação contínua de implantação utilizando passos manuais](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps).

## <a name="additional-resources"></a>Recursos adicionais

* [Investigar questões comuns com implantação contínua](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Utilizar o Azure PowerShell](/powershell/azureps-cmdlets-docs)
* [Documentação git](https://git-scm.com/documentation)
* [Kudu do projeto](https://github.com/projectkudu/kudu/wiki)

[Criar um repositório (GitHub)]: https://help.github.com/articles/create-a-repo
[Criar um repositório (BitBucket)]: https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html
[Crie um novo Git repo (Azure Repos)]: /azure/devops/repos/git/creatingrepo
