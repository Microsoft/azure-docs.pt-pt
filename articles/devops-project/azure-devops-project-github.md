---
title: 'Tutorial: criar um pipeline de CI/CD para seu código existente usando Azure DevOps Projects'
description: Azure DevOps Projects facilita a introdução ao Azure. DevOps Projects ajuda a usar seu próprio código e o repositório GitHub para iniciar um aplicativo em um serviço do Azure de sua escolha em algumas etapas rápidas.
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
ms.openlocfilehash: 15ac201a078864717d8e0079801507cf5fc0fe3b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73481086"
---
# <a name="tutorial-create-a-cicd-pipeline-for-your-existing-code-by-using-azure-devops-projects"></a>Tutorial: criar um pipeline de CI/CD para seu código existente usando Azure DevOps Projects

Azure DevOps Projects apresenta uma experiência simplificada em que você pode colocar seu código existente e o repositório git ou escolher um aplicativo de exemplo para criar um pipeline de CI (integração contínua) e um CD (entrega contínua) para o Azure.

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
* Acesso a um repositório git externo ou GitHub que contém .NET, Java, PHP, Node, Python ou código da Web estático.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Azure DevOps Projects cria um pipeline de CI/CD no Azure Pipelines. Você pode criar uma nova organização de DevOps do Azure ou usar uma organização existente. Azure DevOps Projects também cria recursos do Azure na assinatura do Azure de sua escolha.

1. Iniciar sessão no [portal do Azure](https://portal.azure.com).

2. No menu portal do Azure, selecione **criar um recurso**.

   ![Menu portal do Azure – criar um recurso](_img/azure-devops-project-github/createaresource.png)

3. Selecione **DevOps > DevOps projeto**.

   ![O painel DevOps Projects](_img/azure-devops-project-github/azuredashboard.png)

3. Selecione **traga seu próprio código**e, em seguida, selecione **Avançar**.

## <a name="configure-access-to-your-github-repo-and-choose-a-framework"></a>Configurar o acesso ao seu repositório GitHub e escolher uma estrutura

1. Selecione **GitHub** ou um repositório de código **git** externo. Para este tutorial, selecione **GitHub**. Talvez seja necessário autenticar-se com o GitHub pela primeira vez para permitir que o Azure acesse seu repositório GitHub.

2. Conclua selecionando o **repositório** e a **ramificação**, selecione **Avançar**.

3. Se você estiver usando a alteração de contêineres do Docker **é Docker do aplicativo** para **Sim**, para este tutorial **não** deixe selecionado, selecione **Avançar**. Para obter mais informações sobre como usar os contêineres do Docker, passe o mouse sobre o ícone **i** .

   ![.NET Framework](_img/azure-devops-project-github/appframework.png)

4. Nos menus suspensos, selecione o **tempo de execução do aplicativo** e a **estrutura**e, em seguida, selecione **Avançar**. A estrutura do aplicativo, que você escolhe, determina o tipo de destino de implantação de serviço do Azure que está disponível.

5. Selecione o **serviço do Azure** para implantar o aplicativo também e, em seguida, selecione **Avançar**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Configurar o Azure DevOps e uma assinatura do Azure

1. Insira um **nome de projeto**.

2. Crie uma nova **organização gratuita do Azure DevOps** ou escolha uma organização existente na lista suspensa.

3. Selecione sua **assinatura do Azure**, insira um nome para seu **aplicativo Web** ou use o padrão. Selecione um **local**e, em seguida, selecione **concluído**. Após alguns minutos, a visão geral da implantação do projeto DevOps é exibida na portal do Azure.

4. Selecione **ir para o recurso** para exibir o painel do projeto DevOps. No canto superior direito, fixe o **projeto** ao seu painel para acesso rápido. Azure DevOps Projects configura automaticamente um gatilho de Build e versão de CI. Seu código permanece em seu repositório GitHub ou outro repositório externo. Um aplicativo de exemplo é configurado em um repositório em sua **organização DevOps do Azure**. Uma compilação é executada e seu aplicativo é implantado no Azure.

   ![Exibição do painel de DevOps Projects](_img/azure-devops-project-github/projectsdashboard.png)

5. O painel fornece visibilidade do seu repositório de código, seu pipeline de CI/CD e seu aplicativo no Azure. À direita, em recursos do Azure, selecione **procurar** para exibir seu aplicativo em execução.

## <a name="commit-changes-to-github-and-automatically-deploy-them-to-azure"></a>Confirmar alterações no GitHub e implantá-las automaticamente no Azure

Agora você está pronto para colaborar com uma equipe em seu aplicativo usando um processo de CI/CD que implanta automaticamente seu trabalho mais recente em seu site. Cada alteração no repositório do GitHub inicia uma compilação no Azure DevOps e um pipeline de CD executa uma implantação no Azure.

1. No painel de projeto do DevOps, selecione **repositórios**. O repositório do GitHub é aberto em uma nova guia do navegador. faça uma alteração em seu aplicativo e clique em **confirmar alterações**.

2. Após alguns instantes, uma compilação é iniciada no Azure Pipelines. Você pode monitorar o status da compilação no painel do DevOps Projects ou monitorá-lo em sua organização do Azure DevOps selecionando a guia **criar pipelines** no painel do projeto.

## <a name="examine-the-azure-pipelines-cicd-pipeline"></a>Examinar o pipeline de CI/CD Azure Pipelines

Azure DevOps Projects configura automaticamente um pipeline de CI/CD no Azure Pipelines. Explore e personalize o pipeline, conforme necessário. Para se familiarizar com os pipelines de compilação e versão, faça o seguinte:

1. No painel DevOps Projects, selecione **criar pipelines**.

2. Quando a página **Azure pipelines** for aberta, você verá um histórico das compilações mais recentes e o status de cada compilação.

   ![Compilações do pipeline DevOps do Azure](_img/azure-devops-project-github/pipelinesbuildpage.png)

3. No canto superior direito da página **Builds** , você verá opções para **Editar** a compilação atual, **fila** para enfileirar uma nova compilação e as reticências **&#8942;** () para abrir um menu com mais opções, selecione **Editar**.

4. A compilação executa várias tarefas, como buscar fontes do repositório, restaurar dependências e publicar saídas para implantações. À direita em **nome**, altere o nome do pipeline de compilação para algo mais descritivo. Selecione **salvar & fila**, em seguida, **salve**, deixe um comentário e, em seguida, selecione **salvar** novamente.

   ![Página de compilações do Azure DevOps](_img/azure-devops-project-github/buildpage.png)

5. Para ver uma trilha de auditoria de suas alterações recentes para a compilação, selecione a guia **histórico** . o Azure DevOps rastreia as alterações feitas no pipeline de compilação e permite que você compare as versões.

6. Selecione a guia **gatilhos** . o projeto DevOps do Azure cria automaticamente um gatilho de CI com algumas configurações padrão. Gatilhos como **habilitar a integração contínua** podem ser definidos para executar uma compilação cada vez que uma alteração de código tiver sido confirmada ou agendar compilações para serem executadas em horários específicos.

## <a name="clean-up-resources"></a>Limpar recursos

Quando eles não forem mais necessários, você poderá excluir o serviço Azure App e os recursos relacionados que você criou neste tutorial. Para fazer isso, use a funcionalidade **excluir** no painel DevOps Projects.

## <a name="next-steps"></a>Passos seguintes

Quando você configurou seu processo de CI/CD neste tutorial, um pipeline de compilação e versão foi criado automaticamente no Azure DevOps Projects. Pode modificar estes pipelines de compilação e de lançamento para satisfazer as necessidades da sua equipa. Aprendeu a:

> [!div class="checklist"]
>  * Usar DevOps Projects para criar um pipeline de CI/CD
> * Configurar o acesso ao seu repositório GitHub e escolher uma estrutura
> * Configurar o Azure DevOps e uma assinatura do Azure
> * Confirmar alterações no GitHub e implantá-las automaticamente no Azure
> * Examinar o pipeline de CI/CD Azure Pipelines
> * Limpar recursos

Para saber mais sobre o pipeline de CI/CD, consulte:

> [!div class="nextstepaction"]
> [Definir o pipeline de implantação contínua de vários estágios (CD)](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

Para saber mais sobre o monitoramento de aplicativos, consulte:
  
 > [!div class="nextstepaction"]
 > [O que é o Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview)
