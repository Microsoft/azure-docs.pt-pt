---
title: 'Tutorial: Implementar a aplicação ASP.NET para máquinas virtuais do Azure, usando os projetos de DevOps do Azure'
description: Projetos de DevOps torna mais fácil de começar a utilizar no Azure e para implementar a aplicação ASP.NET em máquinas virtuais do Azure em alguns passos rápidos.
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 05643f342d51d99645d3c9204d6e63adcf2a0a73
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60546499"
---
# <a name="tutorial-deploy-your-aspnet-app-to-azure-virtual-machines-by-using-azure-devops-projects"></a>Tutorial: Implementar a aplicação ASP.NET para máquinas virtuais do Azure, usando os projetos de DevOps do Azure

Projetos de DevOps do Azure apresenta uma experiência simplificada de onde pode colocar o seu código existente e o repositório de Git ou escolha um aplicativo de exemplo para criar um pipeline de entrega contínua (CD) para o Azure e a integração contínua (CI). 

Projetos de DevOps também:
* Cria automaticamente os recursos do Azure, como uma nova máquina virtual do Azure (VM).
* Cria e configura um pipeline de lançamento em DevOps do Azure, que inclui um pipeline de compilação para CI.
* Define um pipeline de lançamento para o CD. 
* Cria um recurso do Application Insights do Azure para monitorização.

Neste tutorial, irá:

> [!div class="checklist"]
> * Utilizar projetos de DevOps para implementar a aplicação ASP.NET
> * Configurar o Azure DevOps e uma subscrição do Azure 
> * Examinar o pipeline de CI
> * Examinar o pipeline de CD
> * Consolide as alterações para repositórios do Azure e implementá-las automaticamente para o Azure
> * Configurar a monitorização do Azure Application Insights
> * Limpar recursos

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Pode obter uma subscrição gratuita através do [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="use-devops-projects-to-deploy-your-aspnet-app"></a>Utilizar projetos de DevOps para implementar a aplicação ASP.NET

Projetos de DevOps cria um pipeline CI/CD em Pipelines do Azure. Pode criar uma nova organização de DevOps do Azure ou utilizar uma organização existente. Projetos de DevOps também cria os recursos do Azure como máquinas virtuais na subscrição do Azure à sua escolha.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. No painel esquerdo, selecione **New**.

1. Na caixa de pesquisa, escreva **projetos de DevOps**e, em seguida, selecione **criar**.

    ![O dashboard de projetos de DevOps](_img/azure-devops-project-github/fullbrowser.png)

1. Selecione **.NET**e, em seguida, selecione **próxima**.

1. Sob **escolher um aplicativo Framework**, selecione **ASP.NET**e, em seguida, selecione **seguinte**.  
    A estrutura de aplicativos, que selecionou no passo anterior, dita o tipo de destino de implementação de serviço do Azure está disponível aqui. 

1. Selecione a máquina virtual e, em seguida, selecione **seguinte**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Configurar o Azure DevOps e uma subscrição do Azure

1. Crie uma nova organização do Azure DevOps ou selecione uma organização existente. 

1. Introduza um nome para o seu projeto de DevOps do Azure. 

1. Selecione os serviços de subscrição do Azure.  
    Opcionalmente, pode selecionar **alteração** e, em seguida, introduza mais detalhes de configuração, como a localização dos recursos do Azure.
 
1. Introduza um nome de máquina virtual, nome de utilizador e palavra-passe para o seu novo recurso de máquina virtual do Azure e, em seguida, selecione **feito**.  
    Após alguns minutos, a máquina virtual do Azure estará pronta. Um exemplo de aplicação de ASP.NET está configurado num repositório na sua organização de DevOps do Azure, uma compilação e lançamento é executado e a aplicação é implementada a VM do Azure recentemente criado. 

    Depois de concluída, é apresentado o dashboard de projetos de DevOps no portal do Azure. Também pode navegar para o dashboard diretamente a partir **todos os recursos** no portal do Azure. 

    O dashboard fornece visibilidade em seu repositório de código do Azure DevOps, seu pipeline CI/CD e sua aplicação em execução no Azure.   

    ![Vista do Dashboard](_img/azure-devops-project-vms/dashboardnopreview.png)

Projetos de DevOps configura automaticamente uma compilação CI e alterações de Acionador de versão que implementa o código ao seu repositório. Pode configurar opções adicionais no Azure DevOps. Para ver a sua aplicação em execução, selecione **procurar**.
    
## <a name="examine-the-ci-pipeline"></a>Examinar o pipeline de CI
 
Projetos de DevOps configurado automaticamente um pipeline CI/CD em Pipelines do Azure. Pode explorar e personalizar o pipeline. Para se familiarizar com o pipeline de compilação, faça o seguinte:

1. Na parte superior do dashboard de projetos de DevOps, selecione **criar Pipelines**.  
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

## <a name="examine-the-cd-pipeline"></a>Examinar o pipeline de CD

Projetos de DevOps automaticamente cria e configura os passos necessários para implementar a partir de sua organização de DevOps do Azure à sua subscrição do Azure. Estes passos incluem a configuração de uma ligação de serviço do Azure para autenticar o DevOps do Azure à sua subscrição do Azure. A automação também cria um pipeline de CD, que fornece o CD para a máquina virtual do Azure. Para saber mais sobre o pipeline de CD de DevOps do Azure, faça o seguinte:

1. Selecione **criar e lançar**e, em seguida, selecione **versões**.  
    Projetos de DevOps cria um pipeline de lançamento para gerir implementações para o Azure.

1. Selecione as reticências (...) junto ao seu pipeline de lançamento e, em seguida, selecione **editar**.  
    O pipeline de lançamento contém um *pipeline*, que define o processo de lançamento.

1. Em **Artefactos**, selecione **Remover**.  
    O pipeline de compilação examinado nos passos anteriores produz a saída que é utilizada para o artefacto. 

1. Junto a **Drop** ícone, selecione **acionador de implementação contínua**.  
    Esta versão de pipeline tem um acionador de CD ativado, o que é executada uma implantação sempre que um novo artefacto de compilação está disponível. Opcionalmente, pode desativar o acionador para que as suas implementações exigem execução manual. 

1. No lado esquerdo, selecione **tarefas**e, em seguida, selecione o seu ambiente.  
    As tarefas são as atividades que executa o seu processo de implantação e estão agrupadas em fases. Este pipeline de lançamento ocorrerá em duas fases:
    * A primeira fase contém uma tarefa de implementação de grupo de recursos do Azure que faz duas coisas:
      * Configura a VM para a implementação
      * Adiciona a nova VM a um grupo de implementação do Azure DevOps. O grupo de implementação de VM do Azure DevOps faz a gestão de grupos lógicos de máquinas de destino da implementação.
    * Na segunda fase, uma tarefa de gerenciar de aplicação Web do IIS cria um Web site do IIS na VM. Uma segunda tarefa de implementação de aplicação Web do IIS é criada para implementar o site.

1. No lado direito, selecione **ver versões** para apresentar um histórico de versões.

1. Selecione as reticências (...) junto a uma versão e, em seguida, selecione **aberto**.  
    Pode explorar vários menus, como um resumo de lançamento, itens de trabalho associados e testes.

1. Selecione **Consolidações**.  
    Esta vista mostra as confirmações de código que estão associadas esta implementação. Compare as versões para ver as diferenças de consolidação entre implementações.

1. Selecione **Registos**.  
    Os registos contêm informações úteis sobre o processo de implementação. Pode visualizá-las durante e depois das implementações.

## <a name="commit-changes-to-azure-repos-and-automatically-deploy-them-to-azure"></a>Consolide as alterações para repositórios do Azure e implementá-las automaticamente para o Azure 

Agora, está pronto para colaborar com uma equipa na sua aplicação ao utilizar um processo de CI/CD que implementa automaticamente o seu trabalho mais recente ao seu Web site. Cada alteração para o repositório de Git inicia uma compilação em DevOps do Azure e um pipeline CD executa uma implementação para o Azure. Siga o procedimento nesta secção, ou utilize outra técnica para consolidar as alterações ao seu repositório. As alterações de código iniciam o processo de CI/CD e implementar automaticamente as suas alterações para o site do IIS na VM do Azure.

1. No painel esquerdo, selecione **código**e, em seguida, aceda ao seu repositório.

1. Vá para o *vistas \ início* diretório, selecione as reticências (...) junto aos *Index. cshtml* de ficheiros e, em seguida, selecione **editar**.

1. Fazer uma alteração para o ficheiro, por exemplo, adicionar algum texto dentro de uma das etiquetas div. 

1. Na parte superior direita, selecione **consolidar**e, em seguida, selecione **consolidar** novamente para enviar sua alteração.  
    Após alguns instantes, uma compilação é iniciada no Azure DevOps e uma versão executa para implementar as alterações. Monitorize o estado de compilação no dashboard de projetos de DevOps ou no navegador com a sua organização de DevOps do Azure.

1. Depois de concluída a versão, atualize a aplicação para verificar as suas alterações.

## <a name="configure-azure-application-insights-monitoring"></a>Configurar a monitorização do Azure Application Insights

Com o Azure Application Insights, pode monitorizar facilmente o desempenho e a utilização da aplicação. Projetos de DevOps configura automaticamente um recurso do Application Insights para a sua aplicação. Pode configurar vários alertas e capacidades de monitorização, conforme necessário.

1. No portal do Azure, vá para o dashboard de projetos de DevOps. 

1. Na parte inferior direita, selecione o **Application Insights** ligação para a sua aplicação.  
    O **Application Insights** painel abre-se. Esta vista contém informações sobre a monitorização da utilização, do desempenho e da disponibilidade da aplicação.

    ![O painel do Application Insights](_img/azure-devops-project-github/appinsights.png) 

1. Selecione **intervalo de tempo**e, em seguida, selecione **última hora**. Para filtrar os resultados, selecione **atualização**.  
    Agora, pode ver todas as atividades dos últimos 60 minutos. 
    
1. Para sair do intervalo de tempo, selecione **x**.

1. Selecione **alertas**e, em seguida, selecione **Adicionar alerta de métrica**. 

1. Introduza um nome para o alerta.

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

Se estiver a testar, pode evitar acumulação de encargos de faturas por limpar seus recursos. Quando já não são necessários, pode eliminar a máquina virtual do Azure e os recursos relacionados que criou neste tutorial. Para tal, utilize o **eliminar** funcionalidade no dashboard do projeto de DevOps. 

> [!IMPORTANT]
> O procedimento seguinte elimina permanentemente recursos. O *eliminar* funcionalidade destrói os dados que são criados pelo projeto em projetos de DevOps no Azure e do Azure DevOps, e não será possível recuperá-la. Utilize este procedimento apenas depois de ler atentamente as instruções.

1. No portal do Azure, vá para o dashboard de projetos de DevOps.
1. Na parte superior direita, selecione **eliminar**. 
1. Na linha de comandos, selecione **Sim** ao *eliminar permanentemente* os recursos.

Opcionalmente, pode modificar estes pipelines de compilação e versão para satisfazer as necessidades da sua equipa. Também pode utilizar este padrão de CI/CD como modelo para outros pipelines. 

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Utilizar projetos de DevOps para implementar a aplicação ASP.NET
> * Configurar o Azure DevOps e uma subscrição do Azure 
> * Examinar o pipeline de CI
> * Examinar o pipeline de CD
> * Consolide as alterações para repositórios do Azure e implementá-las automaticamente para o Azure
> * Configurar a monitorização do Azure Application Insights
> * Limpar recursos

Para saber mais sobre o pipeline de CI/CD, consulte:

> [!div class="nextstepaction"]
> [Definir o seu pipeline de vários estágios de implementação contínua (CD)](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
