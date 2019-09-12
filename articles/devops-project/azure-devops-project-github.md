---
title: 'Tutorial: Criar um pipeline de CI/CD para seu código existente usando Azure DevOps Projects'
description: Projetos de DevOps do Azure torna mais fácil começar a utilizar no Azure. DevOps Projects ajuda a usar seu próprio código e o repositório GitHub para iniciar um aplicativo em um serviço do Azure de sua escolha em algumas etapas rápidas.
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
ms.date: 07/09/2018
author: mlearned
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: 2abe24ad65e1e8997b48a28b35ec0e65162022f2
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70898016"
---
# <a name="tutorial-create-a-cicd-pipeline-for-your-existing-code-by-using-azure-devops-projects"></a>Tutorial: Criar um pipeline de CI/CD para seu código existente usando Azure DevOps Projects

Azure DevOps Projects apresenta uma experiência simplificada em que você pode colocar seu código existente e o repositório git ou escolher um aplicativo de exemplo para criar um pipeline de CI (integração contínua) e um CD (entrega contínua) para o Azure.

Irá:

> [!div class="checklist"]
> * Usar DevOps Projects para criar um pipeline de CI/CD
> * Configurar o acesso ao seu repositório GitHub e escolher uma estrutura
> * Configurar o Azure DevOps e uma subscrição do Azure 
> * Confirmar alterações no GitHub e implantá-las automaticamente no Azure
> * Examinar o pipeline de CI/CD Azure Pipelines
> * Configurar a monitorização do Azure Application Insights
> * Limpar recursos

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Pode obter uma subscrição gratuita através do [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).
* Acesso a um repositório git externo ou GitHub que contém .NET, Java, PHP, Node, Python ou código da Web estático.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Azure DevOps Projects cria um pipeline de CI/CD no Azure Pipelines. Você pode criar uma nova organização de DevOps do Azure ou usar uma organização existente. Azure DevOps Projects também cria recursos do Azure na assinatura do Azure de sua escolha.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. No painel esquerdo, selecione **novo**.

1. Na caixa de pesquisa, digite **DevOps Projects**e, em seguida, selecione **criar**.

    ![O painel DevOps Projects](_img/azure-devops-project-github/fullbrowser.png)

1. Selecione **traga seu próprio código**e, em seguida, selecione **Avançar**.

## <a name="configure-access-to-your-github-repo-and-choose-a-framework"></a>Configurar o acesso ao seu repositório GitHub e escolher uma estrutura

1. Selecione **GitHub** ou um repositório git externo e, em seguida, selecione seu repositório e a ramificação que contém seu aplicativo.

1. Selecione sua estrutura da Web e, em seguida, selecione **Avançar**.

    ![.NET Framework](_img/azure-devops-project-github/webframework.png)

    A estrutura do aplicativo, que você escolheu anteriormente, dita o tipo de destino de implantação do serviço do Azure que está disponível aqui. 
    
1. Selecione o serviço de destino e, em seguida, selecione **Avançar**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Configurar o Azure DevOps e uma subscrição do Azure 

1. Crie uma nova organização de DevOps do Azure ou selecione uma organização existente.

    a. Insira um nome para seu projeto no Azure DevOps. 
    
    b. Selecione sua assinatura e local do Azure, insira um nome para seu aplicativo e, em seguida, selecione **concluído**.

    Após alguns minutos, é apresentado o dashboard de projetos de DevOps no portal do Azure. Um aplicativo de exemplo é configurado em um repositório em sua organização DevOps do Azure, uma compilação é executada e seu aplicativo é implantado no Azure. Esse painel fornece visibilidade sobre o repositório de código do GitHub, o pipeline de CI/CD e seu aplicativo no Azure. 
    
1. Selecione **procurar** para exibir o aplicativo em execução.

    ![Exibição do painel de DevOps Projects](_img/azure-devops-project-github/dashboardnopreview.png) 
    
Azure DevOps Projects configura automaticamente um gatilho de Build e versão de CI. Seu código permanece em seu repositório GitHub ou outro repositório externo. 

## <a name="commit-changes-to-github-and-automatically-deploy-them-to-azure"></a>Confirmar alterações no GitHub e implantá-las automaticamente no Azure 

Agora você está pronto para colaborar com uma equipe em seu aplicativo usando um processo de CI/CD que implanta automaticamente seu trabalho mais recente em seu site. Cada alteração no repositório do GitHub inicia uma compilação no Azure DevOps e um pipeline de CD executa uma implantação no Azure.

1. Faça uma alteração em seu aplicativo e, em seguida, confirme a alteração em seu repositório GitHub.  
    Após alguns instantes, uma compilação é iniciada no Azure Pipelines. Você pode monitorar o status da compilação no painel do DevOps Projects ou pode monitorá-lo no navegador com sua organização DevOps do Azure.

1. Depois que a compilação for concluída, atualize seu aplicativo para verificar suas alterações.

## <a name="examine-the-azure-pipelines-cicd-pipeline"></a>Examinar o pipeline de CI/CD Azure Pipelines

Azure DevOps Projects configura automaticamente um pipeline de CI/CD no Azure Pipelines. Explore e personalize o pipeline, conforme necessário. Para se familiarizar com os pipelines de compilação e versão, faça o seguinte:

1. Na parte superior do painel de DevOps Projects, selecione **criar pipelines**.  
    Uma guia do navegador exibe o pipeline de Build para seu novo projeto.

1. Aponte para o campo **status** e, em seguida, selecione as reticências (...).  
    Um menu exibe várias opções, como enfileirar uma nova compilação, pausar uma compilação e editar o pipeline de compilação.

1. Selecione **Editar**.

1. Neste painel, pode examinar as várias tarefas para o seu pipeline de compilação.  
    A compilação executa várias tarefas, como a busca de fontes do repositório git, a restauração de dependências e a publicação de saídas usadas para implantações.

1. Na parte superior do pipeline de compilação, selecione o nome do pipeline de compilação.

1. Altere o nome do seu pipeline de compilação para algo mais descritivo, selecione **guardar e colocar em fila**e, em seguida, selecione **guardar**.

1. No nome do pipeline de compilação, selecione **Histórico**.  
    Verá um registo de auditoria das alterações recentes da compilação. O DevOps do Azure controla as alterações feitas no pipeline de compilação e permite que você compare as versões.

1. Selecione **Acionadores**.  
    Azure DevOps Projects cria automaticamente um gatilho de CI e todas as confirmações para o repositório iniciam uma nova compilação. Opcionalmente, você pode optar por incluir ou excluir ramificações do processo de CI.

1. Selecione **Retenção**.  
        Dependendo do seu cenário, pode especificar políticas de manter ou remover um determinado número de compilações.

1. Selecione **Compilar e liberar**e, em seguida, selecione **versões**.  
    Azure DevOps Projects cria um pipeline de liberação para gerenciar implantações no Azure.

1. Selecione as reticências (...) ao lado de seu pipeline de lançamento e, em seguida, selecione **Editar**.  
    O pipeline de lançamento contém um *pipeline*, que define o processo de lançamento. 
    
1. Em **Artefactos**, selecione **Remover**.  
    O pipeline de Build que você examinou nas etapas anteriores produz a saída usada para o artefato. 

1. Ao lado do ícone de **soltar** , selecione **gatilho de implantação contínua**.  
    Este pipeline de lançamento tem um acionador de CD ativado, que executa uma implementação sempre que estiver disponível um novo artefacto de compilação. Opcionalmente, pode desativar o acionador para que as suas implementações exigem execução manual. 

1. À esquerda, selecione **tarefas**.  
    As tarefas são as atividades que seu processo de implantação executa. Neste exemplo, uma tarefa foi criada para ser implantada no serviço de Azure App.

1. À direita, selecione **Exibir liberações** para exibir um histórico de versões.

1. Selecione as reticências (...) ao lado de uma versão e, em seguida, selecione **abrir**.  
    Existem vários menus para explorar, como um resumo de lançamento, itens de trabalho associados e testes.

1. Selecione **Consolidações**.  
    Esta exibição mostra as confirmações de código que estão associadas a essa implantação. 

1. Selecionar **Registos**.  
    Os registos contêm informações úteis sobre o processo de implementação. Você pode exibi-los durante e após as implantações.

## <a name="configure-azure-application-insights-monitoring"></a>Configurar a monitorização do Azure Application Insights

Com o Azure Application Insights, pode monitorizar facilmente o desempenho e a utilização da aplicação. Azure DevOps Projects configura automaticamente um recurso de Application Insights para seu aplicativo. Pode configurar vários alertas e capacidades de monitorização, conforme necessário.

1. Na portal do Azure, vá para o painel de DevOps Projects. 

1. No canto inferior direito, selecione o link **Application insights** para seu aplicativo.  
    O painel de **Application insights** é aberto. Esta vista contém informações sobre a monitorização da utilização, do desempenho e da disponibilidade da aplicação.

    ![O painel de Application Insights](_img/azure-devops-project-github/appinsights.png) 

1. Selecione **intervalo de tempo**e, em seguida, selecione **última hora**. Para filtrar os resultados, selecione **Atualizar**.  
    Agora você pode exibir todas as atividades dos últimos 60 minutos. Para sair do intervalo de tempo, selecione **x**.

1. Selecione **alertas**e, em seguida, selecione **adicionar alerta de métrica**. 

1. Insira um nome para o alerta.

1. Na lista suspensa **alteração na fonte** , selecione o **recurso do serviço de aplicativo.** <!-- Please confirm whether this should be "Source Alter on" or "Source Alert on" -->

1. Na lista suspensa **métrica** , examine as várias métricas de alerta.  
    O alerta predefinido é para um **tempo de resposta de servidor superior a 1 segundo**. Pode configurar facilmente vários alertas para melhorar as capacidades de monitorização da aplicação.

1. Marque a caixa de seleção **notificar por meio de proprietários de email, colaboradores e leitores** .  
    Opcionalmente, você pode executar ações adicionais quando um alerta é exibido executando um aplicativo lógico do Azure.

1. Selecione **OK** para criar o alerta.  
    Após alguns instantes, o alerta aparecerá como ativo no painel.
    
1. Saia da área de **alertas** e volte para o painel de **Application insights** .

1. Selecione **disponibilidade**e, em seguida, selecione **Adicionar teste**. 

1. Insira um nome de teste e, em seguida, selecione **criar**.  
    Um teste de ping simples é criado para verificar a disponibilidade da sua aplicação. Após alguns minutos, os resultados do teste ficam disponíveis e o dashboard do Application Insights apresenta um estado de disponibilidade.

## <a name="clean-up-resources"></a>Limpar recursos

Quando eles não forem mais necessários, você poderá excluir o serviço Azure App e os recursos relacionados que você criou neste tutorial. Para fazer isso, use a funcionalidade **excluir** no painel DevOps Projects.

## <a name="next-steps"></a>Passos Seguintes

Quando você configurou seu processo de CI/CD neste tutorial, um pipeline de compilação e versão foi criado automaticamente no Azure DevOps Projects. Pode modificar estes pipelines de compilação e de lançamento para satisfazer as necessidades da sua equipa. Aprendeu a:

> [!div class="checklist"]
> * Usar DevOps Projects para criar um pipeline de CI/CD
> * Configurar o acesso ao seu repositório GitHub e escolher uma estrutura
> * Configurar o Azure DevOps e uma subscrição do Azure 
> * Confirmar alterações no GitHub e implantá-las automaticamente no Azure
> * Examinar o pipeline de CI/CD Azure Pipelines
> * Configurar a monitorização do Azure Application Insights
> * Limpar recursos

Para saber mais sobre o pipeline de CI/CD, consulte:

> [!div class="nextstepaction"]
> [Definir o pipeline de implantação contínua de vários estágios (CD)](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
