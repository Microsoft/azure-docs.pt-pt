---
title: 'Início rápido: Criar um pipeline CI/CD para o Ruby on Rails com projetos de DevOps do Azure'
description: Projetos de DevOps do Azure torna mais fácil começar a utilizar no Azure. Pode iniciar uma aplicação Ruby web no serviço do Azure em alguns passos rápidos.
ms.prod: devops
ms.technology: devops-cicd
services: vsts
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
ms.openlocfilehash: 4cf3feeb92f04b4e97cbdc83c539c206790a78c8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60558536"
---
# <a name="create-a-cicd-pipeline-for-ruby-on-rails-by-using-azure-devops-projects"></a>Criar um pipeline CI/CD para o Ruby on Rails com projetos de DevOps do Azure

Configure integração contínua (CI) e a entrega contínua (CD) para a sua aplicação Ruby on Rails usando os projetos de DevOps do Azure. Projetos de DevOps simplifica a configuração inicial de uma compilação de DevOps do Azure e a versão pipeline.

Se não tiver uma subscrição do Azure, pode obter uma subscrição gratuita através do [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Projetos de DevOps do Azure cria um pipeline CI/CD no Azure repositórios. Pode criar uma nova organização de DevOps do Azure ou utilizar uma organização existente. Projetos de DevOps também cria os recursos do Azure na subscrição do Azure à sua escolha.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. No painel esquerdo, selecione **criar um recurso**.

1. Na caixa de pesquisa, escreva **projetos de DevOps**e, em seguida, selecione **criar**.

    ![O dashboard de projetos de DevOps](_img/azure-devops-project-github/fullbrowser.png)

## <a name="select-a-sample-app-and-azure-service"></a>Selecione uma aplicação de exemplo e o serviço do Azure

1. Selecione o **Ruby** aplicação de exemplo.

1. Selecione o framework da aplicação **Ruby on Rails**. Quando tiver terminado, selecione **seguinte**.

1. **A Aplicação Web no Linux** é o destino de implementação predefinido.  
    Opcionalmente, pode selecionar **aplicação Web para contentores**. A estrutura da aplicação, que escolheu anteriormente, dita o tipo de destino de implementação de serviço do Azure está disponível aqui. 
    
1. Selecione o serviço de destino à sua escolha e, em seguida, selecione **seguinte**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Configurar o Azure DevOps e uma subscrição do Azure 

1. Crie uma nova organização do Azure DevOps gratuita ou escolher uma organização existente. 

1. Introduza um nome para o seu projeto de DevOps do Azure. 

1. Selecione a subscrição do Azure e a localização, introduza um nome para a sua aplicação e, em seguida, selecione **feito**.  
    Após alguns minutos, é apresentado o dashboard de projetos de DevOps no portal do Azure. Uma aplicação de exemplo está configurada num repositório na sua organização de DevOps do Azure, uma compilação é executada e a aplicação é implementada para o Azure. 
    
    O dashboard fornece visibilidade em seu repositório de código, seu pipeline CI/CD e sua aplicação no Azure. No lado direito, selecione **procurar** para ver a sua aplicação em execução.

    ![Vista do Dashboard](_img/azure-devops-project-go/dashboardnopreview.png) 

## <a name="commit-your-code-changes-and-execute-the-cicd"></a>Consolidar as alterações de código e executar o CI/CD

Projetos de DevOps do Azure cria um repositório de Git no Pipelines do Azure ou do GitHub. Para ver o repositório e efetuar alterações de código à sua aplicação, faça o seguinte:

1. No dashboard de projetos de DevOps, à esquerda, selecione a ligação para o ramo principal.  
    A hiperligação abre uma vista para o repositório de Git recém-criado.

1. Para ver o URL de clone do repositório, selecione **Clone** no canto superior direito.  
    Pode clonar o repositório de Git no seu IDE preferido. Nos próximos passos, pode utilizar o browser para fazer e consolidar alterações de código diretamente no ramo principal.

1. À esquerda, vá para o *app/views/pages/home.html.erb* do ficheiro e, em seguida, selecione **editar**.

1. Fazer uma alteração no arquivo. Por exemplo, modificar algum texto dentro de uma das etiquetas div.

1. Selecione **consolidar**e, em seguida, guarde as alterações.

1. No seu browser, aceda ao dashboard de projetos de DevOps.  
    Uma compilação deve estar em curso. As alterações que efetuou são automaticamente criadas e implementadas por meio de um pipeline CI/CD.

## <a name="examine-the-azure-pipelines-cicd-pipeline"></a>Examine o pipeline do Azure Pipelines CI/CD

Projetos de DevOps do Azure configura automaticamente um pipeline de CI/CD completo na sua organização de DevOps do Azure. Explore e personalize o pipeline, conforme necessário. Familiarize-se com a compilação de DevOps do Azure e lançar pipelines, efetue o seguinte procedimento:

1. Vá para o dashboard de projetos de DevOps.

1. Na parte superior, selecione **Criar pipelines**.  
    Um separador do browser apresenta o pipeline de compilação para o novo projeto.

1. Aponte para o **estado** campo e, em seguida, selecione as reticências (...).  
    Um menu apresenta várias opções, como a colocação em fila uma nova compilação, colocar em pausa uma compilação e editar o pipeline de compilação.

1. Selecione **Editar**.

1. Neste painel, pode examinar as várias tarefas para o seu pipeline de compilação.  
    A compilação executa várias tarefas, tais como origens ao obter a partir do Git saídas do repositório, a restauração de dependências e a publicação é utilizada para implementações.

1. Na parte superior do pipeline de compilação, selecione o nome do pipeline de compilação.

1. Altere o nome do seu pipeline de compilação para algo mais descritivo, selecione **guardar e colocar em fila**e, em seguida, selecione **guardar**.

1. No nome do pipeline de compilação, selecione **Histórico**.  
    Este painel apresenta uma trilha de auditoria das alterações recentes para a compilação. DevOps do Azure mantém um registro de todas as alterações feitas no pipeline de compilação e permite-lhe comparar versões.

1. Selecione **Acionadores**.  
    Projetos de DevOps cria automaticamente um acionador de CI e cada consolidação para o repositório inicia uma nova compilação. Opcionalmente, pode optar por incluir ou excluir os ramos do processo de CI.

1. Selecione **Retenção**.  
    Dependendo do seu cenário, pode especificar políticas de manter ou remover um determinado número de compilações.

1. Selecione **criar e lançar**e, em seguida, selecione **versões**.  
    Projetos de DevOps cria um pipeline de lançamento para gerir implementações para o Azure.

1. Selecione as reticências (...) junto ao seu pipeline de lançamento e, em seguida, selecione **editar**.  
    O pipeline de lançamento contém um *pipeline*, que define o processo de lançamento.

1. Em **Artefactos**, selecione **Remover**.  
    O pipeline de compilação que é examinado anteriormente produz a saída que é utilizada para o artefacto. 

1. No lado direito do **Drop** ícone, selecione **acionador de implementação contínua**.  
    Esta versão de pipeline tem um acionador de CD ativado, o que é executada uma implantação sempre que um novo artefacto de compilação está disponível. Opcionalmente, pode desativar o acionador para que as suas implementações exigem execução manual. 

1. No lado esquerdo, selecione **tarefas**.  
    As tarefas são as atividades que executa o processo de implementação. Neste exemplo, foi criada uma tarefa para implantar o App Service do Azure.

1. No lado direito, selecione **ver versões** para apresentar um histórico de versões.

1. Selecione as reticências (...) junto a uma versão e, em seguida, selecione **aberto**.  
    Pode explorar vários menus, como um resumo de lançamento, itens de trabalho associados e testes.

1. Selecione **Consolidações**.  
    Esta vista mostra as confirmações de código que estão associadas esta implementação. 

1. Selecionar **Registos**.  
    Os registos contêm informações úteis sobre o processo de implementação. Pode visualizá-las durante e depois das implementações.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não são necessários, pode eliminar a instância de serviço de aplicações do Azure e os recursos relacionados que criou neste início rápido. Para tal, utilize o **eliminar** funcionalidade no dashboard de projetos de DevOps.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre como modificar a compilação e lançar pipelines para satisfazer as necessidades da sua equipe, consulte:

> [!div class="nextstepaction"]
> [Definir o seu pipeline de vários estágios de implementação contínua (CD)](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
