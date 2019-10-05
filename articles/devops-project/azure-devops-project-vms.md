---
title: 'Tutorial: Implantar seu aplicativo ASP.NET em máquinas virtuais do Azure usando Azure DevOps Projects'
description: DevOps Projects facilita a introdução ao Azure e a implantação do aplicativo ASP.NET em máquinas virtuais do Azure em algumas etapas rápidas.
ms.author: mlearned
manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: b03fb27c46d4fd925b91b8927fdd50ff4912aaec
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2019
ms.locfileid: "71969561"
---
# <a name="tutorial-deploy-your-aspnet-app-to-azure-virtual-machines-by-using-azure-devops-projects"></a>Tutorial: Implantar seu aplicativo ASP.NET em máquinas virtuais do Azure usando Azure DevOps Projects

Azure DevOps Projects apresenta uma experiência simplificada em que você pode colocar seu código existente e o repositório git ou escolher um aplicativo de exemplo para criar um pipeline de CI (integração contínua) e um CD (entrega contínua) para o Azure. 

DevOps Projects também:
* Cria automaticamente recursos do Azure, como uma nova VM (máquina virtual) do Azure.
* Cria e configura um pipeline de liberação no Azure DevOps que inclui um pipeline de compilação para CI.
* Configura um pipeline de liberação para CD. 
* Cria um recurso de informações de Aplicativo Azure para monitoramento.

Neste tutorial, irá:

> [!div class="checklist"]
> * Usar DevOps Projects para implantar seu aplicativo ASP.NET
> * Configurar o Azure DevOps e uma subscrição do Azure 
> * Examinar o pipeline de CI
> * Examinar o pipeline de CD
> * Confirmar alterações para Azure Repos e implantá-las automaticamente no Azure
> * Configurar a monitorização do Azure Application Insights
> * Limpar recursos

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Pode obter uma subscrição gratuita através do [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="use-devops-projects-to-deploy-your-aspnet-app"></a>Usar DevOps Projects para implantar seu aplicativo ASP.NET

DevOps Projects cria um pipeline de CI/CD no Azure Pipelines. Você pode criar uma nova organização de DevOps do Azure ou usar uma organização existente. DevOps Projects também cria recursos do Azure, como máquinas virtuais na assinatura do Azure de sua escolha.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. No painel esquerdo, selecione **novo**.

1. Na caixa de pesquisa, digite **DevOps Projects**e, em seguida, selecione **criar**.

    ![O painel DevOps Projects](_img/azure-devops-project-github/fullbrowser.png)

1. Selecione **.net**e, em seguida, selecione **Avançar**.

1. Em **escolher uma estrutura de aplicativo**, selecione **ASP.net**e, em seguida, selecione **Avançar**.  
    A estrutura do aplicativo, que você escolheu em uma etapa anterior, determina o tipo de destino de implantação do serviço do Azure que está disponível aqui. 

1. Selecione a máquina virtual e, em seguida, selecione **Avançar**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Configurar o Azure DevOps e uma subscrição do Azure

1. Crie uma nova organização de DevOps do Azure ou selecione uma organização existente. 

1. Insira um nome para seu projeto DevOps do Azure. 

1. Selecione os serviços de assinatura do Azure.  
    Opcionalmente, você pode selecionar **alterar** e, em seguida, inserir mais detalhes de configuração, como o local dos recursos do Azure.
 
1. Insira um nome de máquina virtual, nome de usuário e senha para o novo recurso de máquina virtual do Azure e, em seguida, selecione **concluído**.  
    Após alguns minutos, a máquina virtual do Azure estará pronta. Um aplicativo ASP.NET de exemplo é configurado em um repositório em sua organização do DevOps do Azure, um Build e uma versão são executados e seu aplicativo é implantado na VM do Azure recém-criada. 

    Após a conclusão, o painel de DevOps Projects é exibido na portal do Azure. Você também pode navegar até o painel diretamente de **todos os recursos** no portal do Azure. 

    O painel fornece visibilidade do repositório de código do Azure DevOps, do pipeline de CI/CD e do aplicativo em execução no Azure.   

    ![Vista do Dashboard](_img/azure-devops-project-vms/dashboardnopreview.png)

DevOps Projects configura automaticamente um gatilho de compilação e liberação de CI que implanta alterações de código em seu repositório. Pode configurar opções adicionais no Azure DevOps. Para exibir o aplicativo em execução, selecione **procurar**.
    
## <a name="examine-the-ci-pipeline"></a>Examinar o pipeline de CI
 
DevOps Projects configurado automaticamente um pipeline de CI/CD no Azure Pipelines. Pode explorar e personalizar o pipeline. Para se familiarizar com o pipeline de compilação, faça o seguinte:

1. Na parte superior do dashboard de projetos de DevOps, selecione **criar Pipelines**.  
    Uma guia do navegador exibe o pipeline de Build para seu novo projeto.

1. Aponte para o campo **status** e, em seguida, selecione as reticências (...).  
    Um menu exibe várias opções, como enfileirar uma nova compilação, pausar uma compilação e editar o pipeline de compilação.

1. Selecione **Editar**.

1. Neste painel, pode examinar as várias tarefas para o seu pipeline de compilação.  
    A compilação executa várias tarefas, como a busca de fontes do repositório git, a restauração de dependências e a publicação de saídas usadas para implantações.

1. Na parte superior do pipeline de compilação, selecione o nome do pipeline de compilação.

1. Altere o nome do seu pipeline de compilação para algo mais descritivo, selecione **guardar e colocar em fila**e, em seguida, selecione **guardar**.

1. No nome do pipeline de compilação, selecione **Histórico**.  
    Esse painel exibe uma trilha de auditoria de suas alterações recentes para a compilação. O DevOps do Azure controla as alterações feitas no pipeline de compilação e permite que você compare as versões.

1. Selecione **Acionadores**.  
    DevOps Projects cria automaticamente um gatilho de CI e todas as confirmações para o repositório iniciam uma nova compilação. Opcionalmente, você pode optar por incluir ou excluir ramificações do processo de CI.

1. Selecione **Retenção**.  
    Dependendo do seu cenário, pode especificar políticas de manter ou remover um determinado número de compilações.

## <a name="examine-the-cd-pipeline"></a>Examinar o pipeline de CD

DevOps Projects cria e configura automaticamente as etapas necessárias para implantar de sua organização do Azure DevOps em sua assinatura do Azure. Essas etapas incluem a configuração de uma conexão de serviço do Azure para autenticar o Azure DevOps em sua assinatura do Azure. A automação também cria um pipeline de CD, que fornece o CD para a máquina virtual do Azure. Para saber mais sobre o pipeline do CD do Azure DevOps, faça o seguinte:

1. Selecione **Compilar e liberar**e, em seguida, selecione **versões**.  
    DevOps Projects cria um pipeline de liberação para gerenciar implantações no Azure.

1. Selecione as reticências (...) ao lado de seu pipeline de lançamento e, em seguida, selecione **Editar**.  
    O pipeline de lançamento contém um *pipeline*, que define o processo de lançamento.

1. Em **Artefactos**, selecione **Remover**.  
    O pipeline de compilação que você examinou nas etapas anteriores produz a saída usada para o artefato. 

1. Ao lado do ícone de **soltar** , selecione **gatilho de implantação contínua**.  
    Este pipeline de lançamento tem um gatilho de CD habilitado, que executa uma implantação sempre que um novo artefato de compilação está disponível. Opcionalmente, pode desativar o acionador para que as suas implementações exigem execução manual. 

1. À esquerda, selecione **tarefas**e, em seguida, selecione seu ambiente.  
    As tarefas são as atividades que seu processo de implantação executa e são agrupadas em fases. Este pipeline de versão ocorre em duas fases:
    * A primeira fase contém uma tarefa de implantação de grupo de recursos do Azure que faz duas coisas:
      * Configura a VM para implantação
      * Adiciona a nova VM a um grupo de implantação DevOps do Azure. O grupo de implantação de VM no Azure DevOps gerencia grupos lógicos de máquinas de destino de implantação.
    * Na segunda fase, uma tarefa de gerenciamento de aplicativo Web do IIS cria um site do IIS na VM. Uma segunda tarefa de implantação do aplicativo Web do IIS é criada para implantar o site.

1. À direita, selecione **Exibir liberações** para exibir um histórico de versões.

1. Selecione as reticências (...) ao lado de uma versão e, em seguida, selecione **abrir**.  
    Você pode explorar vários menus, como um resumo de lançamento, itens de trabalho associados e testes.

1. Selecione **Consolidações**.  
    Esta exibição mostra as confirmações de código que estão associadas a essa implantação. Compare as versões para ver as diferenças de consolidação entre implementações.

1. Selecione **Registos**.  
    Os registos contêm informações úteis sobre o processo de implementação. Você pode exibi-los durante e após as implantações.

## <a name="commit-changes-to-azure-repos-and-automatically-deploy-them-to-azure"></a>Confirmar alterações para Azure Repos e implantá-las automaticamente no Azure 

Agora você está pronto para colaborar com uma equipe em seu aplicativo usando um processo de CI/CD que implanta automaticamente seu trabalho mais recente em seu site. Cada alteração no repositório git inicia uma compilação no Azure DevOps e um pipeline de CD executa uma implantação no Azure. Siga o procedimento nesta seção ou use outra técnica para confirmar as alterações no seu repositório. As alterações de código iniciam o processo de CI/CD e implantam automaticamente as alterações no site do IIS na VM do Azure.

1. No painel esquerdo, selecione **código**e, em seguida, vá para o repositório.

1. Vá para o diretório *views\home* , selecione as reticências (...) ao lado do arquivo *index. cshtml* e, em seguida, selecione **Editar**.

1. Faça uma alteração no arquivo, como adicionar texto dentro de uma das marcas div. 

1. No canto superior direito, selecione **confirmar**e, em seguida, selecione **confirmar** novamente para enviar por push sua alteração.  
    Depois de alguns instantes, uma compilação é iniciada no Azure DevOps e uma versão é executada para implantar as alterações. Monitore o status da compilação no painel de DevOps Projects ou no navegador com sua organização DevOps do Azure.

1. Depois que o lançamento for concluído, atualize seu aplicativo para verificar suas alterações.

## <a name="configure-azure-application-insights-monitoring"></a>Configurar a monitorização do Azure Application Insights

Com o Azure Application Insights, pode monitorizar facilmente o desempenho e a utilização da aplicação. DevOps Projects configura automaticamente um recurso de Application Insights para seu aplicativo. Pode configurar vários alertas e capacidades de monitorização, conforme necessário.

1. Na portal do Azure, vá para o painel de DevOps Projects. 

1. No canto inferior direito, selecione o link **Application insights** para seu aplicativo.  
    O painel de **Application insights** é aberto. Esta vista contém informações sobre a monitorização da utilização, do desempenho e da disponibilidade da aplicação.

    ![O painel de Application Insights](_img/azure-devops-project-github/appinsights.png) 

1. Selecione **intervalo de tempo**e, em seguida, selecione **última hora**. Para filtrar os resultados, selecione **Atualizar**.  
    Agora você pode exibir todas as atividades dos últimos 60 minutos. 
    
1. Para sair do intervalo de tempo, selecione **x**.

1. Selecione **alertas**e, em seguida, selecione **adicionar alerta de métrica**. 

1. Insira um nome para o alerta.

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

Se você estiver testando, poderá evitar a acumulação de encargos de cobrança limpando seus recursos. Quando eles não forem mais necessários, você poderá excluir a máquina virtual do Azure e os recursos relacionados que você criou neste tutorial. Para fazer isso, use a funcionalidade **excluir** no painel do projeto DevOps. 

> [!IMPORTANT]
> O procedimento a seguir exclui permanentemente os recursos. A funcionalidade de *exclusão* destrói os dados criados pelo projeto em DevOps Projects no Azure e no Azure DevOps, e você não poderá recuperá-los. Use este procedimento somente depois de ler atentamente os prompts.

1. Na portal do Azure, vá para o painel de DevOps Projects.
1. No canto superior direito, selecione **excluir**. 
1. No prompt, selecione **Sim** para *excluir permanentemente* os recursos.

Opcionalmente, pode modificar estes pipelines de compilação e versão para satisfazer as necessidades da sua equipa. Também pode utilizar este padrão de CI/CD como modelo para outros pipelines. 

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Usar DevOps Projects para implantar seu aplicativo ASP.NET
> * Configurar o Azure DevOps e uma subscrição do Azure 
> * Examinar o pipeline de CI
> * Examinar o pipeline de CD
> * Confirmar alterações para Azure Repos e implantá-las automaticamente no Azure
> * Configurar a monitorização do Azure Application Insights
> * Limpar recursos

Para saber mais sobre o pipeline de CI/CD, consulte:

> [!div class="nextstepaction"]
> [Definir o pipeline de implantação contínua de vários estágios (CD)](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
