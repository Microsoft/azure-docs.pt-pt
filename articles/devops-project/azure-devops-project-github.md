---
title: 'Tutorial: Crie um pipeline CI/CD para o seu código existente utilizando o Azure DevOps Starter'
description: O Azure DevOps Starter facilita o arranque do Azure. Em alguns passos rápidos, a DevOps Projects ajuda-o a usar o seu próprio código e o GitHub repo para lançar uma aplicação num serviço Azure.
services: vsts
documentationcenter: vs-devops-build
ms.author: mlearned
ms.manager: gwallace
ms.workload: web
ms.prod: devops
ms.technology: devops-cicd
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/24/2020
author: mlearned
ms.custom: mvc
ms.openlocfilehash: 41f2ee9806b8f6818f2b2dd7a48920ac9907ca79
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91854652"
---
# <a name="create-a-cicd-pipeline-for-github-repo-using-azure-devops-starter"></a>Crie um pipeline CI/CD para gitHub repo usando Azure DevOps Starter

O Azure DevOps Starter apresenta um processo simplificado para criar um pipeline de integração contínua (CI) e entrega contínua (CD) para a Azure. Pode trazer o seu código existente e git repo, ou pode selecionar uma aplicação de amostra.

Irá:

> [!div class="checklist"]
> * Utilize o DevOps Starter para criar um pipeline CI/CD
> * Configure o acesso ao seu gitHub e escolha uma estrutura
> * Configurar Azure DevOps e uma subscrição da Azure 
> * Comprometa alterações no GitHub e desloque-as automaticamente para a Azure
> * Examinar o gasoduto Azure Pipelines CI/CD
> * Limpar os recursos

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Pode obter uma gratuita através do [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).
* Acesso a um gitHub ou repo git externo que contém .NET, Java, PHP, Node.js, Python ou código web estático.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

O Azure DevOps Starter cria um gasoduto CI/CD em Pipelines Azure. Pode criar uma nova organização Azure DevOps ou utilizar uma organização existente. O Azure DevOps Starter também cria recursos Azure na subscrição Azure da sua escolha.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Na caixa de pesquisa, escreva **DevOps Starter**e, em seguida, selecione. Clique em **Adicionar** para criar um novo.

    ![O painel de arranque de DevOps](_img/azure-devops-starter-aks/search-devops-starter.png)
    
1. **Selecione Traga o seu próprio código**e, em seguida, selecione **Seguinte**.

## <a name="configure-access-to-your-github-repo-and-select-a-framework"></a>Configure o acesso ao seu repo GitHub e selecione uma estrutura

1. Selecione **GitHub** ou um repositório de código **Git** externo. Para este tutorial, selecione **GitHub**. Poderá ser-lhe exigido que autente com o GitHub pela primeira vez para permitir que o Azure aceda ao seu repositório GitHub.

1. Selecione um **Repositório** e um **Ramo**e, em seguida, selecione **Seguinte**.

1. Se estiver a utilizar os recipientes Docker, mude **a aplicação S Dockerized** para **SIM**. Para este tutorial, deixe **NÃO** selecionado e, em seguida, selecione **Next**. Para obter mais informações sobre a utilização de recipientes Docker, sobressaia o ícone **i.**

   ![Seleção de quadros de aplicação no menu suspenso](_img/azure-devops-project-github/appframework.png)

1. A partir dos menus suspensos, selecione um **tempo de execução da aplicação** e um **quadro de aplicação**, e, em seguida, selecione **Next**. O quadro de aplicação dita o tipo de alvo de implantação de serviço Azure que está disponível.

1. Selecione um **serviço Azure** para implementar a aplicação e, em seguida, selecione **Seguinte**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Configurar Azure DevOps e uma subscrição da Azure

1. Insira um nome para **o nome do projeto.**

1. Crie uma nova organização gratuita na **Azure DevOps Organization** ou selecione uma organização existente a partir do menu suspenso.

1. Selecione a sua subscrição na **Subscrição Azure**e introduza um nome na **aplicação Web** ou utilize o padrão. Selecione uma **localização**e, em seguida, **selecione Fazer**. Após alguns minutos, a visão geral da implementação do DevOps Starter é exibida no portal Azure.

1. Selecione **Vá para** o recurso para ver o painel de arranque de devOps. No canto superior direito, coloque o **Projeto** no seu painel de instrumentos para um acesso rápido. O Azure DevOps Starter configura automaticamente uma construção de CI e desbloqueia o gatilho. O seu código permanece no seu repo GitHub ou em outra repo externa, e uma aplicação de amostra é criada num repo na **Azure DevOps Organization**. O Azure DevOps Starter executa a construção e implementa a aplicação para o Azure.

   ![Azure DevOps Projects vista do painel de instrumentos](_img/azure-devops-project-github/projectsdashboard.png)

1. O painel mostra o seu código repo, o seu pipeline CI/CD e a sua aplicação em Azure. À direita, sob os recursos Azure, **selecione Procurar** para ver a sua aplicação de execução.

## <a name="commit-changes-to-github-and-automatically-deploy-them-to-azure"></a>Comprometa alterações no GitHub e desloque-as automaticamente para a Azure

Está agora pronto para colaborar na sua aplicação com uma equipa. O processo CI/CD implementa automaticamente o seu último trabalho no seu website. Cada mudança para o gitHub repo inicia uma construção em Azure DevOps, e um oleoduto de CD executa uma implantação para Azure.

1. A partir do seu painel de arranque DevOps, **selecione Repositórios**. O seu repositório GitHub abre-se num novo separador de navegador. Faça uma alteração na sua aplicação e, em seguida, **selecione Alterar o compromisso**.

1. Após alguns momentos, uma construção começa em Azure Pipelines. Pode monitorizar o estado de construção no painel de arranque de devOps. Também pode monitorá-lo na sua organização Azure DevOps selecionando o separador **de gasodutos Build** a partir do painel de arranque de DevOps.

## <a name="examine-the-azure-pipelines-cicd-pipeline"></a>Examinar o gasoduto Azure Pipelines CI/CD

O Azure DevOps Starter configura automaticamente um gasoduto CI/CD em Pipelines Azure. Explore e personalize o pipeline, conforme necessário. Para se familiarizar com os oleodutos de construção e libertação, faça o seguinte:

1. A partir do painel de arranque de DevOps, selecione **Construir gasodutos**.

1. Depois da abertura da sua página **Azure Pipelines,** verá uma história das construções mais recentes e o estado de cada construção.

   ![Azure Pipelines constrói página](_img/azure-devops-project-github/pipelinesbuildpage.png)

1. No canto superior direito da página **Builds,** pode selecionar **Editar** para alterar a construção atual, **fila** para adicionar uma nova construção, ou o botão de elipse vertical** (&#8942;**) para abrir um menu com mais opções. Selecione **Editar**.

1. A construção faz várias tarefas, tais como buscar fontes do repo, restaurar dependências e publicar saídas para implementações. À direita, em **Nome,** mude o nome do pipeline de construção para algo mais descritivo. **Selecione Guardar & fila**e, em seguida, selecione **Guardar**. Introduza um comentário e, em seguida, **selecione Guardar** novamente.

   ![Azure DevOps constrói página](_img/azure-devops-project-github/buildpage.png)

1. Para ver um rasto de auditoria das suas recentes alterações para a construção, selecione o **separador Histórico.**  O Azure DevOps rastreia quaisquer alterações feitas ao pipeline de construção e permite comparar versões.

1. Selecione o **separador Gatilhos.** Azure DevOps Projects cria automaticamente um gatilho CI com algumas definições predefinidas. Pode definir gatilhos tais como Permitir a **integração contínua** para executar uma construção cada vez que cometer uma alteração de código. Também pode definir gatilhos para agendar construções para executar em momentos específicos.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não precisar do Azure App Service e dos recursos relacionados que criou neste tutorial, pode eliminá-los. Utilize a funcionalidade **Eliminar** no painel de projetos de DevOps.

## <a name="next-steps"></a>Passos seguintes

Quando configuraste o teu processo ci/CD neste tutorial, criaste automaticamente um pipeline de construção e libertação em Projetos Azure DevOps. Pode modificar estes pipelines de compilação e de lançamento para satisfazer as necessidades da sua equipa.

Para saber mais sobre o oleoduto CI/CD, consulte:

> [!div class="nextstepaction"]
> [Defina o seu pipeline de implantação contínua em várias fases (CD)](/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

Para saber mais sobre a monitorização de aplicações, consulte:
  
 > [!div class="nextstepaction"]
 > [O que é o monitor Azure?](../azure-monitor/overview.md)