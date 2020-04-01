---
title: 'Tutorial: Crie um pipeline CI/CD para o seu código existente utilizando projetos Azure DevOps'
description: A Azure DevOps Projects facilita o início do Azure. Em alguns passos rápidos, a DevOps Projects ajuda-o a usar o seu próprio código e o repo GitHub para lançar uma aplicação num serviço Azure.
services: vsts
documentationcenter: vs-devops-build
ms.author: mlearned
ms.manager: gwallace
editor: ''
ms.assetid: ''
ms.workload: web
ms.prod: devops
ms.technology: devops-cicd
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 06/27/2019
author: mlearned
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: a0496999a9a5dc3e9bfd57df0ec035e6db77d620
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "73615134"
---
# <a name="tutorial-create-a-cicd-pipeline-for-your-existing-code-by-using-azure-devops-projects"></a>Tutorial: Crie um pipeline CI/CD para o seu código existente utilizando projetos Azure DevOps

A Azure DevOps Projects apresenta um processo simplificado para a criação de um oleoduto de integração contínua (CI) e entrega contínua (CD) ao Azure. Pode trazer o seu código existente e git repo, ou pode selecionar uma aplicação de amostra.

Irá:

> [!div class="checklist"]
> * Utilize projetos DevOps para criar um pipeline CI/CD
> * Configure o acesso ao seu repo GitHub e escolha uma estrutura
> * Configure Azure DevOps e uma subscrição Azure 
> * Comprometa alterações ao GitHub e implemente-as automaticamente para o Azure
> * Examinar o gasoduto Azure CI/CD
> * Limpar recursos

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Pode obter uma gratuita através do [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).
* Acesso a um GitHub ou repo externo git que contém .NET, Java, PHP, Node.js, Python ou código web estático.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

A Azure DevOps Projects cria um oleoduto CI/CD em Pipelines Azure. Você pode criar uma nova organização Azure DevOps ou usar uma organização existente. A Azure DevOps Projects também cria recursos Azure na subscrição Azure à sua escolha.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

2. A partir do menu do portal Azure, selecione **Criar um recurso**.

   ![Menu portal Azure - Criar um recurso](_img/azure-devops-project-github/createaresource.png)

3. Selecione **DevOps** > **DevOps Project**.

   ![O painel de projetos DevOps](_img/azure-devops-project-github/azuredashboard.png)

1. Selecione **Trazer o seu próprio código**e, em seguida, selecione **Next**.

## <a name="configure-access-to-your-github-repo-and-select-a-framework"></a>Configure o acesso ao seu repo GitHub e selecione uma estrutura

1. Selecione **gitHub** ou um repositório externo de código **Git.** Para este tutorial, selecione **GitHub**. Pode ser obrigado a autenticar com o GitHub a primeira vez que permite ao Azure aceder ao seu repositório GitHub.

1. Selecione um **Repositório** e um **Ramo**e, em seguida, selecione **Next**.

1. Se estiver a usar recipientes Docker, altere **a aplicação Dockerized** para **YES**. Para este tutorial, não deixe **nenhum** selecionado e, em seguida, selecione **Next**. Para mais informações sobre a utilização de recipientes Docker, paire sobre o ícone **i.**

   ![Seleção de quadros de aplicações no menu suspenso](_img/azure-devops-project-github/appframework.png)

1. A partir dos menus suspensos, selecione um tempo de execução da **aplicação** e um quadro de **aplicação**, e, em seguida, selecione **Next**. O quadro de aplicação dita o tipo de alvo de implantação de serviço sintetizador do serviço Azure que está disponível.

1. Selecione um **serviço Azure** para implementar a aplicação e, em seguida, selecione **Next**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Configure Azure DevOps e uma subscrição Azure

1. Introduza um nome para **o nome do Projeto**.

1. Crie uma nova organização gratuita na **Organização Azure DevOps** ou selecione uma organização existente a partir do menu suspenso.

1. Selecione a sua subscrição na **Subscrição Azure,** e introduza um nome na **aplicação Web** ou utilize o predefinido. Selecione um **Local**, e, em seguida, selecione **Done**. Após alguns minutos, a visão geral da implementação dos Projetos DevOps é exibida no portal Azure.

1. Selecione **Ir recorrer** para ver o dashboard DevOps Projects. No canto superior direito, coloque o **Projeto** no seu painel para um acesso rápido. Os Projetos Azure DevOps configuram automaticamente um gatilho de construção e libertação de CI. O seu código permanece no seu repo GitHub ou noutro repo externo, e uma aplicação de amostra é configurada num repo na **Azure DevOps Organization**. A Azure DevOps Projects gere a construção e implementa a app para o Azure.

   ![Vista para o dashboard projetos Azure DevOps](_img/azure-devops-project-github/projectsdashboard.png)

1. O painel de instrumentos mostra o seu repo de código, o seu pipeline CI/CD e a sua aplicação em Azure. À direita, sob os recursos do Azure, selecione **Browse** para ver a sua aplicação de execução.

## <a name="commit-changes-to-github-and-automatically-deploy-them-to-azure"></a>Comprometa alterações ao GitHub e implemente-as automaticamente para o Azure

Está agora pronto para colaborar na sua aplicação com uma equipa. O processo CI/CD implementa automaticamente o seu mais recente trabalho no seu website. Cada alteração para o repo GitHub inicia uma construção em Azure DevOps, e um pipeline de CD executa uma implantação para Azure.

1. A partir do seu painel de projetos DevOps, selecione **Repositórios**. O seu repositório GitHub abre num novo separador de navegador. **Commit changes**

1. Depois de alguns momentos, uma construção começa em Azure Pipelines. Pode monitorizar o estado de construção no painel de instrumentos de Projetos DevOps. Também pode monitorizá-lo na sua organização Azure DevOps selecionando o separador **de gasodutos Build** do dashboard DevOps Projects.

## <a name="examine-the-azure-pipelines-cicd-pipeline"></a>Examinar o gasoduto Azure CI/CD

Os projetos Azure DevOps configuram automaticamente um oleoduto CI/CD em Pipelines Azure. Explore e personalize o pipeline, conforme necessário. Para se familiarizar com os oleodutos de construção e libertação, faça o seguinte:

1. A partir do dashboard DevOps Projects, **selecione pipelines Build**.

1. Após a abertura da sua página **azure Pipelines,** você verá uma história das construções mais recentes e o estado para cada construção.

   ![Azure Pipelines constrói página](_img/azure-devops-project-github/pipelinesbuildpage.png)

1. No canto superior direito da página **Builds,** pode selecionar **Editar** para alterar a construção atual, **fila** para adicionar uma nova construção, ou o botão de elipse vertical** (&#8942;) **para abrir um menu com mais opções. Selecione **Editar**.

1. A construção faz várias tarefas, tais como a busca de fontes do repo, o restabelecimento de dependências e a publicação de saídas para implantações. À direita, em **Nome,** mude o nome do pipeline de construção para algo mais descritivo. Selecione **Guardar & fila**e, em seguida, selecione **Guardar**. Introduza um comentário e, em seguida, selecione **Guardar** novamente.

   ![Azure DevOps constrói página](_img/azure-devops-project-github/buildpage.png)

1. Para ver um rasto de auditoria das suas recentes alterações para a construção, selecione o separador **História.**  O Azure DevOps rastreia quaisquer alterações feitas ao pipeline de construção e permite-lhe comparar versões.

1. Selecione o separador **Triggers.** Os projetos Azure DevOps criam automaticamente um gatilho DE CI com algumas definições predefinidas. Pode configurar gatilhos como Permitir a **integração contínua** para executar uma construção cada vez que comete uma alteração de código. Também pode definir gatilhos para agendar construções para executar em momentos específicos.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não precisa do Azure App Service e dos recursos relacionados que criou neste tutorial, pode eliminá-los. Utilize a funcionalidade **Eliminar** no painel de instrumentos de Projetos DevOps.

## <a name="next-steps"></a>Passos seguintes

Quando configurao o seu processo CI/CD neste tutorial, criou automaticamente um pipeline de construção e lançamento em Projetos Azure DevOps. Pode modificar estes pipelines de compilação e de lançamento para satisfazer as necessidades da sua equipa.

Para saber mais sobre o oleoduto CI/CD, consulte:

> [!div class="nextstepaction"]
> [Defina o seu gasoduto de implantação contínua em várias fases (CD)](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

Para saber mais sobre a monitorização da aplicação, consulte:
  
 > [!div class="nextstepaction"]
 > [O que é o monitor Azure?](https://docs.microsoft.com/azure/azure-monitor/overview)
