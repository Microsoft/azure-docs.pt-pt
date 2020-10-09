---
title: 'Tutorial: Implemente a sua aplicação ASP.NET para máquinas virtuais Azure utilizando o Azure DevOps Starter'
description: O DevOps Starter facilita o arranque no Azure e a implementação da sua aplicação ASP.NET para máquinas virtuais Azure em alguns passos rápidos.
ms.author: mlearned
manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 03/24/2020
author: mlearned
ms.openlocfilehash: ce11fe5b65cd49cc880713eb4e47b081b6f3b44f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2020
ms.locfileid: "91855978"
---
# <a name="tutorial-deploy-your-aspnet-app-to-azure-virtual-machines-by-using-azure-devops-starter"></a>Tutorial: Implemente a sua aplicação ASP.NET para máquinas virtuais Azure utilizando o Azure DevOps Starter

O Azure DevOps Starter apresenta uma experiência simplificada onde pode trazer o seu código existente e git repo ou escolher uma aplicação de amostra para criar um pipeline de integração contínua (CI) e entrega contínua (CD) para Azure. 

DevOps Starter também:
* Cria automaticamente recursos Azure, como uma nova máquina virtual Azure (VM).
* Cria e configura um oleoduto de libertação em Azure DevOps que inclui um pipeline de construção para CI.
* Cria um oleoduto de libertação para CD. 
* Cria um recurso Azure Application Insights para monitorização.

Neste tutorial, irá:

> [!div class="checklist"]
> * Utilize o DevOps Starter para implementar a sua aplicação ASP.NET
> * Configurar Azure DevOps e uma subscrição da Azure 
> * Examinar o pipeline de CI
> * Examinar o pipeline de CD
> * Cometa alterações no Azure Repos e desloque-os automaticamente para a Azure
> * Configurar a monitorização do Azure Application Insights
> * Limpar os recursos

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Pode obter uma gratuita através do [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="use-devops-starter-to-deploy-your-aspnet-app"></a>Utilize o DevOps Starter para implementar a sua aplicação ASP.NET

DevOps Starter cria um gasoduto CI/CD em Pipelines Azure. Pode criar uma nova organização Azure DevOps ou utilizar uma organização existente. DevOps Projects também cria recursos Azure, como máquinas virtuais na subscrição Azure à sua escolha.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Na caixa de pesquisa, escreva **DevOps Starter**e, em seguida, selecione. Clique em **Adicionar** para criar um novo.

    ![O painel de arranque de DevOps](_img/azure-devops-starter-aks/search-devops-starter.png)

1. Selecione **.NET**e, em seguida, selecione **Seguinte**.

1. Em **Escolha um Quadro de Aplicações**, selecione **ASP.NET**e, em seguida, selecione **Seguinte**. O quadro de aplicação, que escolheu num passo anterior, dita o tipo de alvo de implantação de serviço Azure que está disponível aqui. 

1. Selecione a máquina virtual e, em seguida, selecione **Seguinte**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Configurar Azure DevOps e uma subscrição da Azure

1. Crie uma nova organização Azure DevOps ou selecione uma organização existente. 

1. Insira um nome para o seu projeto Azure DevOps. 

1. Selecione os seus serviços de subscrição Azure. Opcionalmente, pode selecionar **Alterar** e, em seguida, introduzir mais detalhes de configuração, como a localização dos recursos Azure.
 
1. Introduza um nome de máquina virtual, nome de utilizador e senha para o seu novo recurso de máquina virtual Azure e, em seguida, selecione **Feito**. Após alguns minutos, a máquina virtual Azure estará pronta. Uma amostra ASP.NET aplicação é criada num repo na sua organização Azure DevOps, uma construção e lançamento é executado, e a sua aplicação é implementada para o recém-criado Azure VM. 

   Depois de concluído, o painel de arranque de DevOps é apresentado no portal Azure. Também pode navegar para o painel diretamente a partir de **todos os recursos** do portal Azure. 

   O painel de instrumentos proporciona visibilidade ao seu repo de código Azure DevOps, ao seu pipeline CI/CD e à sua aplicação de execução em Azure.   

   ![Vista do Dashboard](_img/azure-devops-project-vms/vm-starter-dashboard.png)

O DevOps Starter configura automaticamente uma construção de CI e lança o gatilho que implementa alterações de código no seu repo. Pode configurar opções adicionais no Azure DevOps. Para ver a sua aplicação de execução, **selecione Procurar**.
    
## <a name="examine-the-ci-pipeline"></a>Examinar o pipeline de CI
 
O DevOps Starter configurau automaticamente um gasoduto CI/CD em Pipelines Azure. Pode explorar e personalizar o pipeline. Para se familiarizar com o gasoduto de construção, faça o seguinte:

1. No topo do painel de arranque de DevOps, selecione **Build Pipelines**. Um separador de navegador exibe o pipeline de construção para o seu novo projeto.

1. Aponte para o campo **Status** e, em seguida, selecione a elipse (...). Um menu apresenta várias opções, como fazer fila de uma nova construção, parar uma construção e editar o pipeline de construção.

1. Selecione **Editar**.

1. Neste painel, pode examinar as várias tarefas para o seu oleoduto de construção. A construção executa várias tarefas, tais como buscar fontes do repo Git, restaurar dependências e publicar saídas usadas para implementações.

1. Na parte superior do pipeline de compilação, selecione o nome do pipeline de compilação.

1. Mude o nome do seu pipeline de construção para algo mais descritivo, **selecione Save & fila**e, em seguida, selecione **Guardar**.

1. No nome do pipeline de compilação, selecione **Histórico**. Este painel apresenta um rasto de auditoria das suas recentes alterações para a construção. O Azure DevOps regista quaisquer alterações feitas ao pipeline de construção, e permite comparar versões.

1. Selecione **Triggers**. O DevOps Starter cria automaticamente um gatilho de CI, e cada compromisso com o repo inicia uma nova construção. Opcionalmente, pode optar por incluir ou excluir os ramos do processo de IC.

1. Selecione **Retenção**. Dependendo do seu cenário, pode especificar políticas para manter ou remover um determinado número de construções.

## <a name="examine-the-cd-pipeline"></a>Examinar o pipeline de CD

O DevOps Starter cria e configura automaticamente os passos necessários para implementar da sua organização Azure DevOps para a sua subscrição Azure. Estes passos incluem configurar uma ligação de serviço Azure para autenticar Azure DevOps para a sua subscrição Azure. A automatização também cria um pipeline de CD, que fornece o CD à máquina virtual Azure. Para saber mais sobre o oleoduto Azure DevOps CD, faça o seguinte:

1. Selecione **Construir e Soltar**e, em seguida, selecione **Versões**.  O DevOps Starter cria um oleoduto de libertação para gerir as implementações para o Azure.

1. Selecione a elipse (...) ao lado do seu pipeline de lançamento e, em seguida, **selecione Editar**. O pipeline de lançamento contém um *pipeline*, que define o processo de lançamento.

1. Em **Artefactos**, selecione **Remover**. O gasoduto de construção que examinou em passos anteriores produz a saída que é usada para o artefacto. 

1. Ao lado do ícone **Drop,** selecione **o gatilho de implementação contínua**. Este oleoduto de desbloqueio tem um disparador de CD ativado, que executa uma implantação cada vez que um novo artefacto de construção está disponível. Opcionalmente, pode desativar o gatilho para que as suas implementações exijam uma execução manual. 

1. À esquerda, selecione **Tarefas**e, em seguida, selecione o seu ambiente. As tarefas são as atividades que o seu processo de implantação executa, e estão agrupadas por fases. Este gasoduto de libertação acontece em duas fases:

    - A primeira fase contém uma tarefa de implantação do Grupo de Recursos Azure que faz duas coisas:
     
        - Configura o VM para implantação
        -   Adiciona o novo VM a um grupo de implementação Azure DevOps. O grupo de implantação VM em Azure DevOps gere grupos lógicos de máquinas-alvo de implantação
     
    - Na segunda fase, uma tarefa IIS Web App Manage cria um website IIS no VM. Uma segunda tarefa de implementação de aplicações web do IIS é criada para implementar o site.

1. À direita, **selecione Ver ver lançamentos** para exibir um histórico de lançamentos.

1. Selecione a elipse (...) ao lado de uma versão e, em seguida, selecione **Abrir**. Pode explorar vários menus, como um resumo de lançamento, itens de trabalho associados e testes.

1. Selecione **Consolidações**. Esta visão mostra compromissos de código que estão associados a esta implementação. Compare as versões para ver as diferenças de consolidação entre implementações.

1. Selecionar **Registos**. Os registos contêm informações úteis sobre o processo de implementação. Pode vê-los durante e após as missões.

## <a name="commit-changes-to-azure-repos-and-automatically-deploy-them-to-azure"></a>Cometa alterações no Azure Repos e desloque-os automaticamente para a Azure 

Está agora pronto para colaborar com uma equipa na sua aplicação, utilizando um processo ci/CD que implementa automaticamente o seu último trabalho no seu website. Cada mudança para o git repo inicia uma construção em Azure DevOps, e um oleoduto de CD executa uma implantação para Azure. Siga o procedimento nesta secção ou use outra técnica para comprometer alterações no seu repo. As alterações de código iniciam o processo CI/CD e implementam automaticamente as suas alterações no website do IIS no Azure VM.

1. No painel esquerdo, selecione **Código**e, em seguida, vá para o seu repo.

1. Vá ao *diretório Views\Home,* selecione a elipse (...) ao lado do ficheiro *Index.cshtml* e, em seguida, selecione **Editar**.

1. Faça uma alteração no ficheiro, como por exemplo adicionar algum texto dentro de uma das tags de div. 

1. No topo direito, **selecione Commit**e, em seguida, selecione **Comprometa-se** novamente para empurrar a sua mudança. Após alguns momentos, uma construção começa em Azure DevOps e um lançamento executa para implementar as alterações. Monitorize o estado de construção no painel de partidas de DevOps ou no navegador com a sua organização Azure DevOps.

1. Depois de concluída a versão, refresque a sua aplicação para verificar as suas alterações.

## <a name="configure-azure-application-insights-monitoring"></a>Configurar a monitorização do Azure Application Insights

Com o Azure Application Insights, pode monitorizar facilmente o desempenho e a utilização da aplicação. O DevOps Starter configura automaticamente um recurso Application Insights para a sua aplicação. Pode configurar vários alertas e capacidades de monitorização, conforme necessário.

1. No portal Azure, vá ao painel de arranque de DevOps. 

1. No centro direita, selecione o link **Application Insights** para a sua aplicação. O painel **de Insights de Aplicação** abre. Esta vista contém informações sobre a monitorização da utilização, do desempenho e da disponibilidade da aplicação.

   ![O painel de Insights de Aplicação](_img/azure-devops-project-github/appinsights.png) 

1. Selecione **o intervalo de tempo**e, em seguida, selecione Última **hora**. Para filtrar os resultados, selecione **Update**. Agora pode ver toda a atividade dos últimos 60 minutos. 
    
1. Para sair do intervalo de tempo, selecione **x**.

1. Selecione **Alertas**e, em seguida, **selecione Adicionar alerta métrico**. 

1. Insira um nome para o alerta.

1. Na lista **de recuos métricos,** examine as várias métricas de alerta. A predefinição de alerta é para um **tempo de resposta de servidor superior a 1 segundo**. Pode configurar facilmente vários alertas, para melhorar as capacidades de monitorização da aplicação.

1. Selecione a **Notificação através de e-mail proprietários, colaboradores e caixa** de verificação de leitores. Opcionalmente, pode executar ações adicionais quando um alerta é exibido executando uma aplicação lógica Azure.

1. Selecione **OK** para criar o alerta. Após alguns momentos, o alerta aparece como ativo no painel de instrumentos. 

1. Saia da área **de Alertas** e volte para o painel **de Insights de Aplicação.**

1. Selecione **Disponibilidade**e, em seguida, selecione **Adicionar teste**. 

1. Introduza um nome de teste e, em seguida, **selecione Criar**. Um teste de ping simples é criado para verificar a disponibilidade da sua aplicação. Após alguns minutos, os resultados do teste ficam disponíveis e o dashboard do Application Insights apresenta um estado de disponibilidade.

## <a name="clean-up-resources"></a>Limpar os recursos

Se estiver a testar, pode evitar acumular encargos de faturação limpando os seus recursos. Quando já não são necessários, pode eliminar a máquina virtual Azure e os recursos relacionados que criou neste tutorial. Para tal, utilize a funcionalidade **Eliminar** no painel de arranque de DevOps. 

> [!IMPORTANT]
> O procedimento que se segue elimina permanentemente os recursos. A funcionalidade *Delete* destrói os dados criados pelo projeto no DevOps Starter tanto em Azure como em Azure DevOps, e não poderá recuperá-los. Utilize este procedimento apenas depois de ter lido atentamente as indicações.

1. No portal Azure, vá ao painel de arranque de DevOps.
1. No topo direito, **selecione Delete**. 
1. A pedido, selecione **Sim** para *eliminar permanentemente* os recursos.

Opcionalmente, pode modificar estes pipelines de compilação e versão para satisfazer as necessidades da sua equipa. Também pode utilizar este padrão de CI/CD como modelo para outros pipelines. 

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Utilize o DevOps Starter para implementar a sua aplicação ASP.NET
> * Configurar Azure DevOps e uma subscrição da Azure 
> * Examinar o pipeline de CI
> * Examinar o pipeline de CD
> * Cometa alterações no Azure Repos e desloque-os automaticamente para a Azure
> * Configurar a monitorização do Azure Application Insights
> * Limpar os recursos

Para saber mais sobre o oleoduto CI/CD, consulte:

> [!div class="nextstepaction"]
> [Defina o seu pipeline de implantação contínua em várias fases (CD)](/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)