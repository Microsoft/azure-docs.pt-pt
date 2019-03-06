---
title: 'Tutorial: Implementar a aplicação de ASP.NET Core no Azure Service Fabric usando os projetos de DevOps do Azure'
description: Projetos de DevOps do Azure torna mais fácil começar a utilizar no Azure. Com os projetos de DevOps, pode implementar a sua aplicação ASP.NET Core no Azure Service Fabric em alguns passos rápidos.
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 8ba217cb9ce849e57b15d3e6cc73529c78bf340e
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57453776"
---
# <a name="tutorial-deploy-your-aspnet-core-app-to-azure-service-fabric-by-using-azure-devops-projects"></a>Tutorial: Implementar a aplicação de ASP.NET Core no Azure Service Fabric usando os projetos de DevOps do Azure

Projetos de DevOps do Azure apresenta uma experiência simplificada de onde pode colocar o seu código existente e o repositório de Git ou escolha um aplicativo de exemplo para criar um pipeline de entrega contínua (CD) para o Azure e a integração contínua (CI). 

Projetos de DevOps também:
* Cria automaticamente os recursos do Azure, como o Azure Service Fabric.
* Cria e configura um pipeline de lançamento em DevOps do Azure que configura um pipeline CI/CD.
* Cria um recurso do Application Insights do Azure para monitorização.

Neste tutorial, irá:

> [!div class="checklist"]
> * Utilizar projetos de DevOps para criar uma aplicação ASP.NET Core e implementá-lo para o Service Fabric
> * Configurar o Azure DevOps e uma subscrição do Azure 
> * Examinar o pipeline de CI
> * Examinar o pipeline de CD
> * Consolidar as alterações ao Git e implementar automaticamente no Azure
> * Limpar recursos

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Pode obter uma subscrição gratuita através do [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="use-devops-projects-to-create-an-aspnet-core-app-and-deploy-it-to-service-fabric"></a>Utilizar projetos de DevOps para criar uma aplicação ASP.NET Core e implementá-lo para o Service Fabric

Projetos de DevOps cria um pipeline CI/CD em Pipelines do Azure. Pode criar uma nova organização de DevOps do Azure ou utilizar uma organização existente. Projetos de DevOps também cria os recursos do Azure, como um cluster do Service Fabric, na subscrição do Azure à sua escolha.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. No painel esquerdo, selecione **criar um recurso**.

1. Na caixa de pesquisa, escreva **projetos de DevOps**e, em seguida, selecione **criar**.

    ![O dashboard de projetos de DevOps](_img/azure-devops-project-github/fullbrowser.png)

1. Selecione **.NET**e, em seguida, selecione **próxima**.

1. Sob **escolher uma estrutura de aplicações**, selecione **ASP.NET Core**e, em seguida, selecione **seguinte**.

1. Selecione **Cluster do Service Fabric**e, em seguida, selecione **próxima**. 

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Configurar o Azure DevOps e uma subscrição do Azure

1. Crie uma nova organização do Azure DevOps ou selecione uma organização existente. 

1. Introduza um nome para o seu projeto de DevOps do Azure. 

1. Selecione a sua subscrição do Azure.

1. Para ver as definições de configuração do Azure adicionais e para identificar o tamanho de máquina virtual do nó e o sistema operativo para o cluster do Service Fabric, selecione **alteração**.  
    Este painel apresenta várias opções de configuração, o tipo e a localização de serviços do Azure.
 
1. Sair da área de configuração do Azure e, em seguida, selecione **feito**.  
    Após alguns minutos, o processo é concluído. Um exemplo de aplicação ASP.NET Core é configurado num repositório de Git na sua organização de DevOps do Azure, é criado um cluster do Service Fabric, um pipeline CI/CD é executado e a aplicação é implementada para o Azure. 

    Depois de tudo isso é concluído, é apresentado o dashboard de projetos de DevOps no portal do Azure. Também pode ir para o dashboard de projetos de DevOps diretamente a partir **todos os recursos** no portal do Azure. 

    Este dashboard fornece visibilidade em seu repositório de código de DevOps do Azure, o seu pipeline CI/CD e o cluster do Service Fabric. Pode configurar opções adicionais para o seu pipeline de CI/CD no Azure repositórios. No lado direito, selecione **procurar** para ver a sua aplicação em execução.

## <a name="examine-the-ci-pipeline"></a>Examinar o pipeline de CI

Projetos de DevOps configura automaticamente um pipeline CI/CD em Pipelines do Azure. Pode explorar e personalizar o pipeline. Para se familiarizar com ele, faça o seguinte:

1. Aceda ao dashboard do projeto de DevOps.

1. Na parte superior do dashboard de projetos de DevOps, selecione **Criar pipelines**.  
    Um separador do browser apresenta o pipeline de compilação para o novo projeto.

1. Aponte para o **estado** campo e, em seguida, selecione as reticências (...).  
    Um menu apresenta várias opções, como a colocação em fila uma nova compilação, colocar em pausa uma compilação e editar o pipeline de compilação.

1. Selecione **Editar**.

1. Neste painel, pode examinar as várias tarefas para o seu pipeline de compilação.  
    A compilação executa várias tarefas, tais como origens ao obter a partir do Git saídas do repositório, a restauração de dependências e a publicação é utilizada para implementações.

1. Na parte superior do pipeline de compilação, selecione o nome do pipeline de compilação. 

1. No nome do pipeline de compilação, selecione **Histórico**.  
    Este painel apresenta uma trilha de auditoria das alterações recentes para a compilação. DevOps do Azure mantém um registro de todas as alterações feitas no pipeline de compilação e permite-lhe comparar versões.

1. Selecione **Acionadores**.  
    Projetos de DevOps cria automaticamente um acionador de CI e cada consolidação para o repositório inicia uma nova compilação. Opcionalmente, pode optar por incluir ou excluir os ramos do processo de CI.

1. Selecione **Retenção**.  
    Dependendo do seu cenário, pode especificar políticas de manter ou remover um determinado número de compilações.

## <a name="examine-the-cd-pipeline"></a>Examinar o pipeline de CD

Projetos de DevOps automaticamente cria e configura os passos necessários para implementar a partir de sua organização de DevOps do Azure à sua subscrição do Azure. Estes passos incluem a configuração de uma ligação de serviço do Azure para autenticar o DevOps do Azure à sua subscrição do Azure. A automação também cria um pipeline de lançamento, que fornece o CD para o Azure. Para saber mais sobre o pipeline de lançamento, faça o seguinte:

1. Selecione **criar e lançar**e, em seguida, selecione **versões**.  
    Projetos de DevOps cria um pipeline de lançamento para gerir implementações para o Azure.

1. Selecione as reticências (...) junto ao seu pipeline de lançamento e, em seguida, selecione **editar**.  
    O pipeline de lançamento contém um *pipeline*, que define o processo de lançamento.

1. Em **Artefactos**, selecione **Remover**.  
    O pipeline de compilação que é examinado anteriormente produz a saída que é utilizada para o artefacto. 

1. No lado direito do **Drop** ícone, selecione **acionador de implementação contínua**.  
    Esta versão de pipeline tem um acionador de CD ativado, o que é executada uma implantação sempre que um novo artefacto de compilação está disponível. Opcionalmente, pode desativar o acionador para que as suas implementações exigem execução manual. 

1. No lado direito, selecione **ver versões** para apresentar um histórico de versões.

1. Selecione as reticências (...) junto a uma versão e, em seguida, selecione **aberto**.  
    Pode explorar vários menus, como um resumo de lançamento, itens de trabalho associados e testes.

1. Selecione **Consolidações**.  
    Esta vista mostra as confirmações de código que estão associadas esta implementação. Compare as versões para ver as diferenças de consolidação entre implementações.

1. Selecione **Registos**.  
    Os registos contêm informações úteis sobre o processo de implementação. Pode visualizá-las durante e depois das implementações.

## <a name="commit-changes-to-git-and-automatically-deploy-them-to-azure"></a>Consolide as alterações com o Git e implementá-las automaticamente para o Azure 

 > [!NOTE]
 > O procedimento seguinte testa o pipeline de CI/CD ao fazer uma alteração de texto simples.

Agora, está pronto para colaborar com uma equipa na sua aplicação ao utilizar um processo de CI/CD que implementa automaticamente o seu trabalho mais recente ao seu Web site. Cada alteração para o repositório de Git inicia uma compilação, e uma versão implementa as suas alterações para o Azure. Siga o procedimento nesta secção, ou utilize outra técnica para consolidar as alterações ao seu repositório. Por exemplo, pode clonar o repositório de Git na sua ferramenta favorita ou IDE, e, em seguida, enviar alterações para este repositório.

1. No menu de DevOps do Azure, selecione **código** > **ficheiros**e, em seguida, aceda ao seu repositório.

1. Vá para o *vistas \ início* diretório, selecione as reticências (...) junto aos *Index. cshtml* de ficheiros e, em seguida, selecione **editar**.

1. Fazer uma alteração para o ficheiro, por exemplo, adicionar algum texto dentro de uma das etiquetas div. 

1. Na parte superior direita, selecione **consolidar**e, em seguida, selecione **consolidar** novamente para enviar sua alteração.  
    Após alguns instantes, uma compilação é iniciada e, em seguida, uma versão executa para implementar as alterações. Pode monitorizar o estado de compilação no dashboard de projetos de DevOps ou no browser com o registo em tempo real de DevOps do Azure.

1. Depois de concluída a versão, atualize a aplicação para verificar as suas alterações.

## <a name="clean-up-resources"></a>Limpar recursos

Se estiver a testar, pode evitar acumulação de encargos de faturas por limpar seus recursos. Quando já não são necessários, pode eliminar o cluster do Azure Service Fabric e os recursos relacionados que criou neste tutorial. Para tal, utilize o **eliminar** funcionalidade no dashboard de projetos de DevOps.

> [!IMPORTANT]
> O procedimento seguinte elimina permanentemente recursos. O *eliminar* funcionalidade destrói os dados que são criados pelo projeto em projetos de DevOps no Azure e do Azure DevOps, e não será possível recuperá-la. Utilize este procedimento apenas depois de ler atentamente as instruções.

1. No portal do Azure, vá para o dashboard de projetos de DevOps.
1. Na parte superior direita, selecione **eliminar**. 
1. Na linha de comandos, selecione **Sim** ao *eliminar permanentemente* os recursos.

## <a name="next-steps"></a>Passos Seguintes

Opcionalmente, pode modificar os pipelines de CI/CD do Azure para satisfazer as necessidades da sua equipa. Também pode utilizar este padrão de CI/CD como modelo para outros pipelines. Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Utilizar projetos de DevOps para criar uma aplicação ASP.NET Core e implementá-lo para o Service Fabric
> * Configurar o Azure DevOps e uma subscrição do Azure 
> * Examinar o pipeline de CI
> * Examinar o pipeline de CD
> * Consolide as alterações com o Git e implementá-las automaticamente para o Azure
> * Limpar recursos

Para saber mais sobre o Service Fabric e os microsserviços, veja:

> [!div class="nextstepaction"]
> [Utilizar a abordagem de microsserviços para criar aplicações](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
