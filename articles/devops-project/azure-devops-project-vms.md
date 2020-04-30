---
title: 'Tutorial: Implemente a sua app ASP.NET para máquinas virtuais Azure utilizando o Starter Azure DevOps'
description: DevOps Starter facilita o arranque do Azure e a implementação da sua aplicação ASP.NET para máquinas virtuais Azure em alguns passos rápidos.
ms.author: mlearned
manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 03/24/2020
author: mlearned
ms.openlocfilehash: 80a590ff97cc6595f2da6d1e573820324a46c2d5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82231521"
---
# <a name="tutorial-deploy-your-aspnet-app-to-azure-virtual-machines-by-using-azure-devops-starter"></a>Tutorial: Implemente a sua app ASP.NET para máquinas virtuais Azure utilizando o Starter Azure DevOps

O Azure DevOps Starter apresenta uma experiência simplificada onde pode trazer o seu código existente e git repo ou escolher uma aplicação de amostra para criar um oleoduto de integração contínua (CI) e entrega contínua (CD) ao Azure. 

DevOps Starter também:
* Cria automaticamente recursos Azure, como uma nova máquina virtual Azure (VM).
* Cria e configura um oleoduto de libertação em Azure DevOps que inclui um pipeline de construção para CI.
* Estabelece um oleoduto de libertação para CD. 
* Cria um recurso Azure Application Insights para monitorização.

Neste tutorial, irá:

> [!div class="checklist"]
> * Use devOps Starter para implementar a sua aplicação ASP.NET
> * Configure Azure DevOps e uma subscrição Azure 
> * Examinar o pipeline de CI
> * Examinar o pipeline de CD
> * Comprometa alterações ao Azure Repos e desempene-as automaticamente para o Azure
> * Configurar a monitorização do Azure Application Insights
> * Limpar recursos

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Pode obter uma gratuita através do [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="use-devops-starter-to-deploy-your-aspnet-app"></a>Use devOps Starter para implementar a sua aplicação ASP.NET

DevOps Starter cria um oleoduto CI/CD em Pipelines Azure. Você pode criar uma nova organização Azure DevOps ou usar uma organização existente. A DevOps Projects também cria recursos Azure, como máquinas virtuais na subscrição Azure à sua escolha.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Na caixa de pesquisa, digite **DevOps Starter**e, em seguida, selecione. Clique em **Adicionar** para criar um novo.

    ![O painel de arranque de DevOps](_img/azure-devops-starter-aks/search-devops-starter.png)

1. Selecione **.NET**, e, em seguida, selecione **Next**.

1. Em **'Escolha uma estrutura de aplicação**,selecione **ASP.NET**, e, em seguida, selecione **Next**. O quadro de aplicação, que escolheu num passo anterior, dita o tipo de alvo de implantação de serviços Azure que está disponível aqui. 

1. Selecione a máquina virtual e, em seguida, selecione **Next**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Configure Azure DevOps e uma subscrição Azure

1. Crie uma nova organização Azure DevOps ou selecione uma organização existente. 

1. Insira um nome para o seu projeto Azure DevOps. 

1. Selecione os seus serviços de subscrição Azure. Opcionalmente, pode selecionar **Alterar** e, em seguida, introduzir mais detalhes de configuração, como a localização dos recursos Azure.
 
1. Introduza um nome de máquina virtual, nome de utilizador e palavra-passe para o seu novo recurso de máquina virtual Azure e, em seguida, selecione **Done**. Após alguns minutos, a máquina virtual Azure estará pronta. Uma amostra ASP.NET aplicação é configurada num repo na sua organização Azure DevOps, uma construção e lançamento é executada, e a sua aplicação é implementada para o recém-criado Azure VM. 

   Depois de concluído, o painel de arranque de DevOps é apresentado no portal Azure. Também pode navegar para o tablier diretamente a partir de **todos os recursos** no portal Azure. 

   O dashboard proporciona visibilidade no seu repo de código Azure DevOps, no seu pipeline CI/CD e na sua aplicação de execução em Azure.   

   ![Vista do Dashboard](_img/azure-devops-project-vms/vm-starter-dashboard.png)

DevOps Starter configura automaticamente um gatilho de construção e libertação de CI que implementa alterações de código no seu repo. Pode configurar opções adicionais no Azure DevOps. Para ver a sua aplicação de execução, **selecione Browse**.
    
## <a name="examine-the-ci-pipeline"></a>Examinar o pipeline de CI
 
DevOps Starter configuraautomaticamente um oleoduto CI/CD em Pipelines Azure. Pode explorar e personalizar o pipeline. Para se familiarizar com o oleoduto de construção, faça o seguinte:

1. Na parte superior do painel de arranque de DevOps, selecione **Build Pipelines**. Um separador de navegador exibe o pipeline de construção para o seu novo projeto.

1. Aponte para o campo **Status** e, em seguida, selecione a elipse (...). Um menu exibe várias opções, como fazer fila de uma nova construção, parar uma construção e editar o pipeline de construção.

1. Selecione **Editar**.

1. Neste painel, pode examinar as várias tarefas para o seu pipeline de construção. A construção executa várias tarefas, tais como a busca de fontes do repo Git, o restabelecimento de dependências e a publicação de saídas usadas para implantações.

1. Na parte superior do pipeline de compilação, selecione o nome do pipeline de compilação.

1. Mude o nome do seu oleoduto de construção para algo mais descritivo, selecione **Guardar & fila**e, em seguida, selecione **Guardar**.

1. No nome do pipeline de compilação, selecione **Histórico**. Este painel apresenta um rasto de auditoria das suas recentes alterações para a construção. A Azure DevOps acompanha quaisquer alterações feitas ao pipeline de construção, e permite-lhe comparar versões.

1. Selecione **Triggers**. DevOps Starter cria automaticamente um gatilho CI, e cada compromisso com o repo inicia uma nova construção. Opcionalmente, pode optar por incluir ou excluir balcões do processo de CI.

1. Selecione **Retenção**. Dependendo do seu cenário, pode especificar políticas para manter ou remover um determinado número de construções.

## <a name="examine-the-cd-pipeline"></a>Examinar o pipeline de CD

DevOps Starter cria e configura automaticamente os passos necessários para implementar da sua organização Azure DevOps para a sua subscrição Azure. Estes passos incluem configurar uma ligação de serviço Azure para autenticar O Azure DevOps à sua subscrição Azure. A automatização também cria um pipeline de CD, que fornece o CD à máquina virtual Azure. Para saber mais sobre o pipeline de CD Azure DevOps, faça o seguinte:

1. Selecione **Construir e Soltar**e, em seguida, selecione **Lançamentos**.  DevOps Starter cria um gasoduto de libertação para gerir as implementações para o Azure.

1. Selecione a elipsis (...) junto ao seu gasoduto de libertação e, em seguida, **selecione Editar**. O pipeline de lançamento contém um *pipeline*, que define o processo de lançamento.

1. Em **Artefactos**, selecione **Remover**. O oleoduto de construção que examinou em passos anteriores produz a saída que é usada para o artefacto. 

1. Ao lado do ícone **Drop,** selecione **O gatilho de implantação contínua**. Este gasoduto de libertação tem um gatilho de CD ativado, que executa uma implantação cada vez que um novo artefacto de construção está disponível. Opcionalmente, pode desativar o gatilho de modo a que as suas implementações exijam execução manual. 

1. À esquerda, selecione **Tarefas**e, em seguida, selecione o seu ambiente. As tarefas são as atividades que o seu processo de implantação executa, e estão agrupadas por fases. Este gasoduto de libertação acontece em duas fases:

    - A primeira fase contém uma tarefa de implantação do Grupo de Recursos Azure que faz duas coisas:
     
        - Configura o VM para implantação
        -   Adiciona o novo VM a um grupo de implantação Azure DevOps. O grupo de implantação vm em Azure DevOps gere grupos lógicos de máquinas-alvo de implantação
     
    - Na segunda fase, uma tarefa IIS Web App Manage cria um website IIS no VM. Uma segunda tarefa de implementação de aplicações Web IIS é criada para implementar o site.

1. À direita, selecione **ver ver ver para** mostrar um histórico de lançamentos.

1. Selecione a elipse (...) junto a um lançamento e, em seguida, **selecione Open**. Pode explorar vários menus, tais como um resumo de lançamento, itens de trabalho associados e testes.

1. Selecione **Consolidações**. Esta visão mostra código compromete-se que estão associados a esta implantação. Compare as versões para ver as diferenças de consolidação entre implementações.

1. Selecionar **Registos**. Os registos contêm informações úteis sobre o processo de implementação. Pode vê-los durante e após as implantações.

## <a name="commit-changes-to-azure-repos-and-automatically-deploy-them-to-azure"></a>Comprometa alterações ao Azure Repos e desempene-as automaticamente para o Azure 

Está agora pronto para colaborar com uma equipa na sua aplicação utilizando um processo CI/CD que implementa automaticamente o seu mais recente trabalho no seu website. Cada alteração para o repo Git inicia uma construção em Azure DevOps, e um pipeline de CD executa uma implantação para Azure. Siga o procedimento nesta secção ou utilize outra técnica para efetuar alterações no seu repo. As alterações de código iniciam o processo CI/CD e implementam automaticamente as suas alterações no website do IIS no VM Azure.

1. No painel esquerdo, selecione **Código**, e depois vá ao seu repo.

1. Vá ao diretório *Views\Home,* selecione a elipsis (...) ao lado do ficheiro *Index.cshtml* e, em seguida, selecione **Editar**.

1. Faça uma alteração no ficheiro, como por exemplo, adicionar algum texto dentro de uma das etiquetas de div. 

1. Na parte superior direita, selecione **Comprometer**- e, em seguida, selecione **Comprometer** novamente para empurrar a sua mudança. Após alguns momentos, uma construção começa em Azure DevOps e uma libertação executa para implementar as mudanças. Monitorize o estado de construção no painel de arranque de DevOps ou no navegador com a sua organização Azure DevOps.

1. Depois de concluída a libertação, refresque a sua aplicação para verificar as suas alterações.

## <a name="configure-azure-application-insights-monitoring"></a>Configurar a monitorização do Azure Application Insights

Com o Azure Application Insights, pode monitorizar facilmente o desempenho e a utilização da aplicação. DevOps Starter configura automaticamente um recurso DeVOps Para a sua aplicação. Pode configurar vários alertas e capacidades de monitorização, conforme necessário.

1. No portal Azure, vá ao painel de arranque de DevOps. 

1. Na direita inferior, selecione o link Insights de **Aplicação** para a sua aplicação. Abre-se o painel De Insights de **Aplicação.** Esta vista contém informações sobre a monitorização da utilização, do desempenho e da disponibilidade da aplicação.

   ![O painel de Insights de Aplicação](_img/azure-devops-project-github/appinsights.png) 

1. Selecione **intervalo de tempo**e, em seguida, selecione Última **hora**. Para filtrar os resultados, selecione **Atualizar**. Agora pode ver toda a atividade dos últimos 60 minutos. 
    
1. Para sair do intervalo de tempo, selecione **x**.

1. Selecione **Alertas,** e, em seguida, **selecione Adicionar alerta métrico**. 

1. Insira um nome para o alerta.

1. Na lista de abandono **métrico,** examine as várias métricas de alerta. A predefinição de alerta é para um **tempo de resposta de servidor superior a 1 segundo**. Pode configurar facilmente vários alertas, para melhorar as capacidades de monitorização da aplicação.

1. Selecione o **Notificar via E-mail proprietários, colaboradores e leitores** verifiquem a caixa. Opcionalmente, pode realizar ações adicionais quando um alerta é exibido executando uma aplicação lógica Azure.

1. Selecione **OK** para criar o alerta. Após alguns momentos, o alerta aparece como ativo no painel de instrumentos. 

1. Saia da área de **Alertas** e volte ao painel de Insights de **Aplicação.**

1. **Selecione Disponibilidade,** e, em seguida, selecione **Adicionar teste**. 

1. Introduza um nome de teste e, em seguida, selecione **Criar**. Um teste de ping simples é criado para verificar a disponibilidade da sua aplicação. Após alguns minutos, os resultados do teste ficam disponíveis e o dashboard do Application Insights apresenta um estado de disponibilidade.

## <a name="clean-up-resources"></a>Limpar recursos

Se estiver a testar, pode evitar acumular taxas de faturação limpando os seus recursos. Quando já não são necessários, pode eliminar a máquina virtual Azure e os recursos relacionados que criou neste tutorial. Para isso, utilize a funcionalidade **Eliminar** no painel de arranque de DevOps. 

> [!IMPORTANT]
> O procedimento seguinte elimina permanentemente os recursos. A funcionalidade *Delete* destrói os dados criados pelo projeto em DevOps Starter tanto no Azure como no Azure DevOps, e não poderá recuperá-los. Utilize este procedimento apenas depois de ter lido atentamente as instruções.

1. No portal Azure, vá ao painel de arranque de DevOps.
1. Na parte superior direita, selecione **Delete**. 
1. No momento, selecione **Sim** para *apagar permanentemente* os recursos.

Opcionalmente, pode modificar estes pipelines de compilação e versão para satisfazer as necessidades da sua equipa. Também pode utilizar este padrão de CI/CD como modelo para outros pipelines. 

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Use devOps Starter para implementar a sua aplicação ASP.NET
> * Configure Azure DevOps e uma subscrição Azure 
> * Examinar o pipeline de CI
> * Examinar o pipeline de CD
> * Comprometa alterações ao Azure Repos e desempene-as automaticamente para o Azure
> * Configurar a monitorização do Azure Application Insights
> * Limpar recursos

Para saber mais sobre o oleoduto CI/CD, consulte:

> [!div class="nextstepaction"]
> [Defina o seu gasoduto de implantação contínua em várias fases (CD)](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
