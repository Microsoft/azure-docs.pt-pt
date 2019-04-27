---
title: 'Tutorial: Criar um pipeline de CI/CD para o seu código existente usando os projetos de DevOps do Azure'
description: Projetos de DevOps do Azure torna mais fácil começar a utilizar no Azure. Projetos de DevOps ajuda-o a utilizar o seu próprio código e o repositório do GitHub para executar um aplicativo num serviço do Azure à sua escolha em alguns passos rápidos.
services: vsts
documentationcenter: vs-devops-build
ms.author: mlearned
ms.manager: douge
editor: ''
ms.assetid: ''
ms.workload: web
ms.prod: devops
ms.technology: devops-cicd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: 88ee15a3b5cc53542d9e098dee485b8a526bb9a6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60555352"
---
# <a name="tutorial-create-a-cicd-pipeline-for-your-existing-code-by-using-azure-devops-projects"></a>Tutorial: Criar um pipeline de CI/CD para o seu código existente usando os projetos de DevOps do Azure

Projetos de DevOps do Azure apresenta uma experiência simplificada de onde pode colocar o seu código existente e o repositório de Git ou escolha um aplicativo de exemplo para criar um pipeline de entrega contínua (CD) para o Azure e a integração contínua (CI).

Irá:

> [!div class="checklist"]
> * Utilizar projetos de DevOps para criar um pipeline CI/CD
> * Configurar o acesso ao seu repositório do GitHub e escolha uma estrutura
> * Configurar o Azure DevOps e uma subscrição do Azure 
> * Consolide as alterações para o GitHub e implementá-las automaticamente para o Azure
> * Examine o pipeline do Azure Pipelines CI/CD
> * Configurar a monitorização do Azure Application Insights
> * Limpar recursos

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Pode obter uma subscrição gratuita através do [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).
* Acesso a um GitHub ou o repositório de Git externo que contém o .NET, Java, PHP, nó, Python ou código web estático.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Projetos de DevOps do Azure cria um pipeline CI/CD em Pipelines do Azure. Pode criar uma nova organização de DevOps do Azure ou utilizar uma organização existente. Projetos de DevOps do Azure também cria os recursos do Azure na subscrição do Azure à sua escolha.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. No painel esquerdo, selecione **New**.

1. Na caixa de pesquisa, escreva **projetos de DevOps**e, em seguida, selecione **criar**.

    ![O dashboard de projetos de DevOps](_img/azure-devops-project-github/fullbrowser.png)

1. Selecione **traga o seu próprio código**e, em seguida, selecione **próxima**.

## <a name="configure-access-to-your-github-repo-and-choose-a-framework"></a>Configurar o acesso ao seu repositório do GitHub e escolha uma estrutura

1. Selecione **GitHub** ou um repositório de Git externo e, em seguida, selecione o seu repositório e o ramo que contém a sua aplicação.

1. Selecione a sua estrutura de web e, em seguida, selecione **seguinte**.

    ![.NET Framework](_img/azure-devops-project-github/webframework.png)

    A estrutura da aplicação, que escolheu anteriormente, dita o tipo de destino de implementação de serviço do Azure está disponível aqui. 
    
1. Selecione o serviço de destino e, em seguida, selecione **seguinte**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Configurar o Azure DevOps e uma subscrição do Azure 

1. Crie uma nova organização do Azure DevOps ou selecione uma organização existente.

    a. Introduza um nome para o seu projeto de DevOps do Azure. 
    
    b. Selecione a subscrição do Azure e a localização, introduza um nome para a sua aplicação e, em seguida, selecione **feito**.

    Após alguns minutos, é apresentado o dashboard de projetos de DevOps no portal do Azure. Um aplicativo de exemplo está configurado num repositório na sua organização de DevOps do Azure, uma compilação é executada e a aplicação é implementada para o Azure. Este dashboard fornece visibilidade em seu repositório de código do GitHub, o pipeline de CI/CD e a sua aplicação no Azure. 
    
1. Selecione **procurar** para ver a sua aplicação em execução.

    ![Vista de dashboard de projetos de DevOps](_img/azure-devops-project-github/dashboardnopreview.png) 
    
Projetos de DevOps do Azure configura automaticamente uma compilação CI e a versão de Acionador. O código permanece no seu repositório do GitHub ou outro repositório externo. 

## <a name="commit-changes-to-github-and-automatically-deploy-them-to-azure"></a>Consolide as alterações para o GitHub e implementá-las automaticamente para o Azure 

Agora, está pronto para colaborar com uma equipa na sua aplicação ao utilizar um processo de CI/CD que implementa automaticamente o seu trabalho mais recente ao seu Web site. Cada alteração para o repositório do GitHub inicia uma compilação em DevOps do Azure e um pipeline CD executa uma implementação para o Azure.

1. Faça uma alteração à aplicação e, em seguida, confirme a alteração para o seu repositório do GitHub.  
    Após alguns instantes, uma compilação é iniciado em Pipelines do Azure. Pode monitorizar o estado de compilação no dashboard de projetos de DevOps ou, pode monitorizá-lo no navegador com a sua organização de DevOps do Azure.

1. Depois de concluída a compilação, atualize a aplicação para verificar as suas alterações.

## <a name="examine-the-azure-pipelines-cicd-pipeline"></a>Examine o pipeline do Azure Pipelines CI/CD

Projetos de DevOps do Azure configura automaticamente um pipeline CI/CD em Pipelines do Azure. Explore e personalize o pipeline, conforme necessário. Para se familiarizar com os pipelines de compilação e versão, faça o seguinte:

1. Na parte superior do dashboard de projetos de DevOps, selecione **Criar pipelines**.  
    Um separador do browser apresenta o pipeline de compilação para o novo projeto.

1. Aponte para o **estado** campo e, em seguida, selecione as reticências (...).  
    Um menu apresenta várias opções, como a colocação em fila uma nova compilação, colocar em pausa uma compilação e editar o pipeline de compilação.

1. Selecione **Editar**.

1. Neste painel, pode examinar as várias tarefas para o seu pipeline de compilação.  
    A compilação executa várias tarefas, tais como origens ao obter a partir do Git saídas do repositório, a restauração de dependências e a publicação é utilizada para implementações.

1. Na parte superior do pipeline de compilação, selecione o nome do pipeline de compilação.

1. Altere o nome do seu pipeline de compilação para algo mais descritivo, selecione **guardar e colocar em fila**e, em seguida, selecione **guardar**.

1. No nome do pipeline de compilação, selecione **Histórico**.  
    Verá um registo de auditoria das alterações recentes da compilação. DevOps do Azure mantém um registro de todas as alterações feitas no pipeline de compilação e permite-lhe comparar versões.

1. Selecione **Acionadores**.  
    Projetos de DevOps do Azure cria automaticamente um acionador de CI e cada consolidação para o repositório inicia uma nova compilação. Opcionalmente, pode optar por incluir ou excluir os ramos do processo de CI.

1. Selecione **Retenção**.  
        Dependendo do seu cenário, pode especificar políticas de manter ou remover um determinado número de compilações.

1. Selecione **criar e lançar**e, em seguida, selecione **versões**.  
    Projetos de DevOps do Azure cria um pipeline de lançamento para gerir implementações para o Azure.

1. Selecione as reticências (...) junto ao seu pipeline de lançamento e, em seguida, selecione **editar**.  
    O pipeline de lançamento contém um *pipeline*, que define o processo de lançamento. 
    
1. Em **Artefactos**, selecione **Remover**.  
    O pipeline de compilação examinado nos passos anteriores produz a saída que é utilizada para o artefacto. 

1. Junto a **Drop** ícone, selecione **acionador de implementação contínua**.  
    Este pipeline de lançamento tem um acionador de CD ativado, que executa uma implementação sempre que estiver disponível um novo artefacto de compilação. Opcionalmente, pode desativar o acionador para que as suas implementações exigem execução manual. 

1. No lado esquerdo, selecione **tarefas**.  
    As tarefas são as atividades que executa o processo de implementação. Neste exemplo, foi criada uma tarefa para implementar o serviço de aplicações do Azure.

1. No lado direito, selecione **ver versões** para apresentar um histórico de versões.

1. Selecione as reticências (...) junto a uma versão e, em seguida, selecione **aberto**.  
    Existem vários menus para explorar, como um resumo de lançamento, itens de trabalho associados e testes.

1. Selecione **Consolidações**.  
    Esta vista mostra as confirmações de código que estão associadas esta implementação. 

1. Selecionar **Registos**.  
    Os registos contêm informações úteis sobre o processo de implementação. Pode visualizá-las durante e depois das implementações.

## <a name="configure-azure-application-insights-monitoring"></a>Configurar a monitorização do Azure Application Insights

Com o Azure Application Insights, pode monitorizar facilmente o desempenho e a utilização da aplicação. Projetos de DevOps do Azure configura automaticamente um recurso do Application Insights para a sua aplicação. Pode configurar vários alertas e capacidades de monitorização, conforme necessário.

1. No portal do Azure, vá para o dashboard de projetos de DevOps. 

1. Na parte inferior direita, selecione o **Application Insights** ligação para a sua aplicação.  
    O **Application Insights** painel abre-se. Esta vista contém informações sobre a monitorização da utilização, do desempenho e da disponibilidade da aplicação.

    ![O painel do Application Insights](_img/azure-devops-project-github/appinsights.png) 

1. Selecione **intervalo de tempo**e, em seguida, selecione **última hora**. Para filtrar os resultados, selecione **atualização**.  
    Agora, pode ver todas as atividades dos últimos 60 minutos. Para sair do intervalo de tempo, selecione **x**.

1. Selecione **alertas**e, em seguida, selecione **Adicionar alerta de métrica**. 

1. Introduza um nome para o alerta.

1. Na **origem Alter no** na lista pendente, selecione seu **recursos de serviço de aplicações.** <!-- Please confirm whether this should be "Source Alter on" or "Source Alert on" -->

1. Na **métrica** pendente lista, examine as várias métricas de alerta.  
    O alerta predefinido é para um **tempo de resposta de servidor superior a 1 segundo**. Pode configurar facilmente vários alertas para melhorar as capacidades de monitorização da aplicação.

1. Selecione o **notificar através de proprietários de E-Mail, contribuintes e leitores** caixa de verificação.  
    Opcionalmente, pode realizar ações adicionais quando um alerta é exibido ao executar uma aplicação lógica do Azure.

1. Selecione **OK** para criar o alerta.  
    Após alguns instantes, o alerta aparece como ativo no dashboard.
    
1. Saída de **alertas** área e volte à **Application Insights** painel.

1. Selecione **disponibilidade**e, em seguida, selecione **adicionar teste**. 

1. Introduza um nome de teste e, em seguida, selecione **criar**.  
    Um teste de ping simples é criado para verificar a disponibilidade da sua aplicação. Após alguns minutos, os resultados do teste ficam disponíveis e o dashboard do Application Insights apresenta um estado de disponibilidade.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não são necessários, pode eliminar o serviço de aplicações do Azure e os recursos relacionados que criou neste tutorial. Para tal, utilize o **eliminar** funcionalidade no dashboard de projetos de DevOps.

## <a name="next-steps"></a>Passos Seguintes

Quando configurou o seu processo de CI/CD neste tutorial, um pipeline de compilação e versão foram criadas automaticamente em projetos de DevOps do Azure. Pode modificar estes pipelines de compilação e de lançamento para satisfazer as necessidades da sua equipa. Aprendeu a:

> [!div class="checklist"]
> * Utilizar projetos de DevOps para criar um pipeline CI/CD
> * Configurar o acesso ao seu repositório do GitHub e escolha uma estrutura
> * Configurar o Azure DevOps e uma subscrição do Azure 
> * Consolide as alterações para o GitHub e implementá-las automaticamente para o Azure
> * Examine o pipeline do Azure Pipelines CI/CD
> * Configurar a monitorização do Azure Application Insights
> * Limpar recursos

Para saber mais sobre o pipeline de CI/CD, consulte:

> [!div class="nextstepaction"]
> [Definir o seu pipeline de vários estágios de implementação contínua (CD)](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
