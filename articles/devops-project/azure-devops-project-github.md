---
title: 'Tutorial: criar um pipeline de CI/CD para seu código existente usando Azure DevOps Projects'
description: Azure DevOps Projects facilita a introdução ao Azure. Em algumas etapas rápidas, DevOps Projects ajuda a usar seu próprio código e o repositório GitHub para iniciar um aplicativo em um serviço do Azure.
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
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615134"
---
# <a name="tutorial-create-a-cicd-pipeline-for-your-existing-code-by-using-azure-devops-projects"></a>Tutorial: criar um pipeline de CI/CD para seu código existente usando Azure DevOps Projects

Azure DevOps Projects apresenta um processo simplificado para a criação de um pipeline de CI (integração contínua) e de CD (entrega contínua) para o Azure. Você pode colocar seu código existente e o repositório git, ou pode selecionar um aplicativo de exemplo.

Irá:

> [!div class="checklist"]
> * Usar DevOps Projects para criar um pipeline de CI/CD
> * Configurar o acesso ao seu repositório GitHub e escolher uma estrutura
> * Configurar o Azure DevOps e uma assinatura do Azure 
> * Confirmar alterações no GitHub e implantá-las automaticamente no Azure
> * Examinar o pipeline de CI/CD Azure Pipelines
> * Limpar recursos

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Pode obter uma subscrição gratuita através do [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).
* Acesso a um repositório git externo ou GitHub que contém .NET, Java, PHP, Node. js, Python ou código da Web estático.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Azure DevOps Projects cria um pipeline de CI/CD no Azure Pipelines. Você pode criar uma nova organização de DevOps do Azure ou usar uma organização existente. Azure DevOps Projects também cria recursos do Azure na assinatura do Azure de sua escolha.

1. Iniciar sessão no [portal do Azure](https://portal.azure.com).

2. No menu portal do Azure, selecione **criar um recurso**.

   ![Menu portal do Azure – criar um recurso](_img/azure-devops-project-github/createaresource.png)

3. Selecione **DevOps** > **DevOps projeto**.

   ![O painel DevOps Projects](_img/azure-devops-project-github/azuredashboard.png)

1. Selecione **traga seu próprio código**e, em seguida, selecione **Avançar**.

## <a name="configure-access-to-your-github-repo-and-select-a-framework"></a>Configurar o acesso ao repositório GitHub e selecionar uma estrutura

1. Selecione **GitHub** ou um repositório de código **git** externo. Para este tutorial, selecione **GitHub**. Talvez seja necessário autenticar-se com o GitHub pela primeira vez para permitir que o Azure acesse seu repositório GitHub.

1. Selecione um **repositório** e uma **ramificação**e, em seguida, selecione **Avançar**.

1. Se você estiver usando contêineres do Docker, a alteração **será o aplicativo Docker** para **Sim**. Para este tutorial, **não** deixe selecionado e, em seguida, selecione **Avançar**. Para obter mais informações sobre como usar contêineres do Docker, passe o mouse sobre o ícone **i** .

   ![Seleção de estrutura de aplicativo no menu suspenso](_img/azure-devops-project-github/appframework.png)

1. Nos menus suspensos, selecione um tempo de **execução de aplicativo** e uma **estrutura de aplicativo**e, em seguida, selecione **Avançar**. A estrutura do aplicativo determina o tipo de destino de implantação do serviço do Azure que está disponível.

1. Selecione um **serviço do Azure** para implantar o aplicativo e, em seguida, selecione **Avançar**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Configurar o Azure DevOps e uma assinatura do Azure

1. Insira um nome para o **nome do projeto**.

1. Crie uma nova organização gratuita na **organização DevOps do Azure** ou selecione uma organização existente no menu suspenso.

1. Selecione sua assinatura na **assinatura do Azure**e insira um nome no **aplicativo Web** ou use o padrão. Selecione um **local**e, em seguida, selecione **concluído**. Após alguns minutos, a visão geral da implantação de DevOps Projects é exibida na portal do Azure.

1. Selecione **ir para o recurso** para exibir o painel de DevOps Projects. No canto superior direito, fixe o **projeto** ao seu painel para acesso rápido. Azure DevOps Projects configura automaticamente um gatilho de Build e versão de CI. Seu código permanece em seu repositório do GitHub ou em outro repositório externo, e um aplicativo de exemplo é configurado em um repositório na **organização do Azure DevOps**. Azure DevOps Projects executa a compilação e implanta o aplicativo no Azure.

   ![Exibição do painel de Azure DevOps Projects](_img/azure-devops-project-github/projectsdashboard.png)

1. O painel mostra seu repositório de códigos, seu pipeline de CI/CD e seu aplicativo no Azure. À direita, em recursos do Azure, selecione **procurar** para exibir seu aplicativo em execução.

## <a name="commit-changes-to-github-and-automatically-deploy-them-to-azure"></a>Confirmar alterações no GitHub e implantá-las automaticamente no Azure

Agora você está pronto para colaborar em seu aplicativo com uma equipe. O processo de CI/CD implanta automaticamente seu trabalho mais recente em seu site. Cada alteração no repositório do GitHub inicia uma compilação no Azure DevOps, e um pipeline de CD executa uma implantação no Azure.

1. No painel DevOps Projects, selecione **repositórios**. O repositório do GitHub é aberto em uma nova guia do navegador. faça uma alteração em seu aplicativo e, em seguida, selecione **confirmar alterações**.

1. Após alguns instantes, uma compilação é iniciada no Azure Pipelines. Você pode monitorar o status da compilação no painel do DevOps Projects. Você também pode monitorá-lo em sua organização do DevOps do Azure selecionando a guia **criar pipelines** no painel DevOps Projects.

## <a name="examine-the-azure-pipelines-cicd-pipeline"></a>Examinar o pipeline de CI/CD Azure Pipelines

Azure DevOps Projects configura automaticamente um pipeline de CI/CD no Azure Pipelines. Explore e personalize o pipeline, conforme necessário. Para se familiarizar com os pipelines de compilação e versão, faça o seguinte:

1. No painel DevOps Projects, selecione **criar pipelines**.

1. Depois que a página **Azure pipelines** for aberta, você verá um histórico das compilações mais recentes e o status de cada compilação.

   ![Página de builds Azure Pipelines](_img/azure-devops-project-github/pipelinesbuildpage.png)

1. No canto superior direito da página de **compilações** , você pode selecionar **Editar** para alterar a compilação atual, a **fila** para adicionar uma nova compilação ou o botão de reticências verticais **&#8942;** () para abrir um menu com mais opções. Selecione **Editar**.

1. A compilação realiza várias tarefas, como a busca de fontes do repositório, a restauração de dependências e a publicação de saídas para implantações. À direita, em **nome**, altere o nome do pipeline de compilação para algo mais descritivo. Selecione **salvar & fila**e, em seguida, selecione **salvar**. Insira um comentário e, em seguida, selecione **salvar** novamente.

   ![Página de compilações do Azure DevOps](_img/azure-devops-project-github/buildpage.png)

1. Para ver uma trilha de auditoria de suas alterações recentes para a compilação, selecione a guia **histórico** .  O Azure DevOps rastreia as alterações feitas no pipeline de compilação e permite que você compare as versões.

1. Selecione a guia **gatilhos** . Azure DevOps Projects cria automaticamente um gatilho de CI com algumas configurações padrão. Você pode definir gatilhos como **habilitar a integração contínua** para executar uma compilação cada vez que você confirmar uma alteração de código. Você também pode definir gatilhos para agendar compilações a serem executadas em horários específicos.

## <a name="clean-up-resources"></a>Limpar recursos

Quando você não precisar mais de Azure App serviço e os recursos relacionados que criou neste tutorial, poderá excluí-los. Use a funcionalidade **excluir** no painel DevOps Projects.

## <a name="next-steps"></a>Passos seguintes

Quando você configurou seu processo de CI/CD neste tutorial, você criou automaticamente um pipeline de Build e versão no Azure DevOps Projects. Pode modificar estes pipelines de compilação e de lançamento para satisfazer as necessidades da sua equipa.

Para saber mais sobre o pipeline de CI/CD, consulte:

> [!div class="nextstepaction"]
> [Definir o pipeline de implantação contínua de vários estágios (CD)](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

Para saber mais sobre o monitoramento de aplicativos, consulte:
  
 > [!div class="nextstepaction"]
 > [O que é o Azure monitor?](https://docs.microsoft.com/azure/azure-monitor/overview)
