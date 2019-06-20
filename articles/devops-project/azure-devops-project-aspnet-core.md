---
title: 'Início rápido: Criar um pipeline CI/CD para .NET com projetos de DevOps do Azure'
description: Projetos de DevOps do Azure torna mais fácil começar a utilizar no Azure. Ajuda-o a iniciar uma aplicação .NET num serviço do Azure à sua escolha com alguns passos rápidos.
ms.prod: devops
ms.technology: devops-cicd
services: azure-devops-project
documentationcenter: vs-devops-build
author: mlearned
manager: douge
editor: ''
ms.assetid: ''
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/09/2018
ms.author: mlearned
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: 88fb243af7628b159a2255c2cdae2ab86f39ca1e
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67275481"
---
# <a name="create-a-cicd-pipeline-for-net-with-azure-devops-projects"></a>Criar um pipeline CI/CD para .NET com projetos de DevOps do Azure

Configure integração contínua (CI) e a entrega contínua (CD) para o .NET core ou a aplicação ASP.NET com projetos de DevOps. Projetos de DevOps simplifica a configuração inicial de um pipeline de compilação e versão nos Pipelines do Azure.

Se não tiver uma subscrição do Azure, pode obter uma subscrição gratuita através do [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Projetos de DevOps cria um pipeline CI/CD no Azure DevOps. Pode criar uma nova organização de DevOps do Azure ou utilizar uma organização existente. Projetos de DevOps também cria os recursos do Azure na subscrição do Azure à sua escolha.

1. Inicie sessão no [Portal do Microsoft Azure](https://portal.azure.com).

1. No painel esquerdo, selecione **criar um recurso** ícone na barra de navegação esquerda e, em seguida, procure **projetos de DevOps**.  

3.  Selecione **Criar**.

    ![A Iniciar a Entrega Contínua](_img/azure-devops-project-aspnet-core/fullbrowser.png)

## <a name="select-a-sample-application-and-azure-service"></a>Selecione um exemplo de aplicação e serviço do Azure

1. Selecione o exemplo de aplicação **.NET**. Os exemplos de .NET incluem uma escolha da arquitetura de ASP.NET open source ou da arquitetura de .NET Core para várias plataformas.

    ![.NET Framework](_img/azure-devops-project-aspnet-core/chooselanguagedotnet.png)

2. Este exemplo corresponde a uma aplicação MVC ASP.NET Core. Selecione o **.NET Core** estrutura de aplicativo, em seguida, selecione **próxima**.    
    
3. Selecione **aplicação de Web do Windows** como destino de implantação, em seguida, selecione **próxima**. Opcionalmente, pode escolher outros serviços do Azure para a sua implementação. A estrutura da aplicação, que escolheu anteriormente, dita o tipo do destino de implementação de serviço do Azure disponível aqui.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Configurar o Azure DevOps e uma subscrição do Azure 

1. Crie uma nova organização do Azure DevOps gratuita ou escolher uma organização existente.

    a. Escolha um nome para o seu projeto. 

    b. Selecione a subscrição do Azure e a localização, escolha um nome para a sua aplicação e, em seguida, selecione **feito**.  
    Após alguns minutos, é apresentado o dashboard de projetos de DevOps no portal do Azure. Um aplicativo de exemplo está configurado num repositório na sua organização de DevOps do Azure, uma compilação é executada e a aplicação é implementada para o Azure. Este dashboard fornece visibilidade para o repositório de código, o pipeline de CI/CD e a sua aplicação no Azure.
    

2. À direita do dashboard, selecione **procurar** para ver a sua aplicação em execução.

    ![Vista do Dashboard](_img/azure-devops-project-aspnet-core/dashboardnopreview.png) 

## <a name="commit-code-changes-and-execute-cicd"></a>Consolidar as alterações de código e executar o CI/CD

 Projetos de DevOps criado um repositório de Git em repositórios do Azure ou do GitHub. Para ver o repositório e fazer alterações de código no aplicativo, faça o seguinte:

1. À esquerda do dashboard de projetos de DevOps, selecione a ligação para o seu **mestre** ramo.  
Esta ligação abre uma vista para o repositório Git recentemente criado.

1. Para ver o URL de clone do repositório, selecione **Clone** na parte superior direita do browser.  
Pode clonar o repositório Git no seu IDE preferido.  Nos próximos passos, pode utilizar o browser para fazer e consolidar alterações de código diretamente no ramo principal.

1. No lado esquerdo do browser, vá para o **Views/Home/index.cshtml** ficheiro.

1. Selecione **editar**e, em seguida, faça uma alteração para o título h2. Por exemplo, digite **dar os primeiros passos com os projetos de DevOps do Azure** ou fazer alguma alteração.

    ![Edições de código](_img/azure-devops-project-aspnet-core/codechange.png)

1. Selecione **consolidar**e, em seguida, guarde as alterações.

1. No seu browser, aceda ao dashboard do projeto de DevOps do Azure.  Deverá agora ver que está em curso uma compilação. As alterações que efetuou são automaticamente criadas e implementadas por meio de um pipeline CI/CD.

## <a name="examine-the-cicd-pipeline"></a>Examine o pipeline de CI/CD

No passo anterior, os projetos de DevOps do Azure configurado automaticamente um pipeline de CI/CD completo. Explore e personalize o pipeline, conforme necessário. Siga os passos seguintes para familiarizar-se com a compilação de DevOps do Azure e lançar pipelines.

1. Na parte superior do dashboard de projetos de DevOps, selecione **criar Pipelines**.  
Esse link abre um separador do browser e o Azure DevOps criar pipeline para o novo projeto.

1. Selecione as reticências (...).  Esta ação abre um menu onde pode iniciar várias atividades, como a colocação em fila uma nova compilação, colocar em pausa uma compilação e editar o pipeline de compilação.

1. Selecione **Editar**.

    ![Pipeline de compilação](_img/azure-devops-project-aspnet-core/builddef.png)

1. Neste painel, pode examinar as várias tarefas para o seu pipeline de compilação.  
 A compilação executa várias tarefas, tais como a obter origens de repositório de Git, restauro de dependências e publicação saídas utilizadas que são utilizadas para implementações.

1. Na parte superior do pipeline de compilação, selecione o nome do pipeline de compilação.

1. Altere o nome do seu pipeline de compilação para algo mais descritivo, selecione **guardar e colocar em fila**e, em seguida, selecione **guardar**.

1. No nome do pipeline de compilação, selecione **Histórico**.   
Na **histórico** painel, verá uma trilha de auditoria das alterações recentes para a compilação.  Pipelines do Azure mantém um registro de quaisquer alterações efetuadas no pipeline de compilação e permite-lhe comparar versões.

1. Selecione **Acionadores**.  
Projetos de DevOps criado automaticamente um acionador de CI e cada consolidação no repositório inicia uma nova compilação.  Opcionalmente, pode optar por incluir ou excluir os ramos do processo de CI.

1. Selecione **Retenção**.  
Dependendo do seu cenário, pode especificar políticas de manter ou remover um determinado número de compilações.

1. Selecione **criar e lançar**, em seguida, selecione **versões**.  
Projetos de DevOps cria um pipeline de lançamento para gerir implementações para o Azure.

1.  No lado esquerdo, selecione as reticências (...) junto ao seu pipeline de lançamento e, em seguida, selecione **editar**.  
O pipeline de lançamento contém um pipeline, que define o processo de liberação.  

1. Em **Artefactos**, selecione **Remover**.  O pipeline de compilação que examinou nos passos anteriores produz a saída utilizada para o artefacto. 

1. Junto a **Drop** ícone, selecione a **acionador de implementação contínua**.  
Esta versão de pipeline tem um acionador de CD ativado, o que é executada uma implantação sempre que houver um artefacto de compilação nova disponível. Opcionalmente, pode desativar o acionador para que as suas implementações exigem execução manual.  

1. No lado esquerdo, selecione **tarefas**.   
As tarefas são as atividades que executa o processo de implementação. Neste exemplo, foi criada uma tarefa para implantar o App Service do Azure.

1. À direita, selecione **ver versões**. Esta vista mostra um histórico das versões.

1. Selecione as reticências (...) junto a um dos seus lançamentos e, em seguida, selecione **aberto**.  
Existem vários menus para explorar, como um resumo de lançamento, itens de trabalho associados e testes.


1. Selecione **Consolidações**.   
Esta vista mostra as confirmações de código que estão associadas a implementação específica. 

1. Selecionar **Registos**.  
Os registos contêm informações úteis sobre o processo de implementação. Podem ser vistos durante e após as implementações.


## <a name="clean-up-resources"></a>Limpar recursos

É possível eliminar o serviço de aplicações do Azure e outros recursos relacionados que criou quando não precisar delas. Utilize o **eliminar** funcionalidade no dashboard de projetos de DevOps.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre como modificar os pipelines de compilação e de lançamento para satisfazer as necessidades da sua equipa, veja este tutorial:

> [!div class="nextstepaction"]
> [Personalizar o processo de CD](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

## <a name="videos"></a>Vídeos

> [!VIDEO https://www.youtube.com/embed/itwqMf9aR0w]
