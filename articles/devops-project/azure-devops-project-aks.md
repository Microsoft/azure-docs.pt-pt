---
title: 'Tutorial: Implemente ASP.NET aplicações Core para o Serviço Azure Kubernetes com projetos Azure DevOps'
description: A Azure DevOps Projects facilita o início do Azure. Com projetos DevOps, você pode implementar a sua aplicação ASP.NET Core com o Serviço Azure Kubernetes (AKS) em alguns passos rápidos.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: b27d56d78296dc5500f97802f811a8923c4e87a8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "71969677"
---
# <a name="tutorial-deploy-aspnet-core-apps-to-azure-kubernetes-service-with-azure-devops-projects"></a>Tutorial: Implemente ASP.NET aplicações Core para o Serviço Azure Kubernetes com projetos Azure DevOps

A Azure DevOps Projects apresenta uma experiência simplificada onde pode trazer o seu código existente e git repo ou escolher uma aplicação de amostra para criar um oleoduto de integração contínua (CI) e entrega contínua (CD) ao Azure. 

Projetos DevOps também:
* Cria automaticamente recursos Azure, como o Serviço Azure Kubernetes (AKS).
* Cria e configura um oleoduto de libertação em Azure DevOps que cria um oleoduto de construção e libertação para CI/CD.
* Cria um recurso Azure Application Insights para monitorização.
* Permite que o [Monitor Azure para os contentores](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview) monitorize o desempenho das cargas de trabalho dos contentores no cluster AKS

Neste tutorial, irá:

> [!div class="checklist"]
> * Use projetos DevOps para implementar uma aplicação ASP.NET Core para AKS
> * Configure Azure DevOps e uma subscrição Azure 
> * Examinar o cluster do AKS
> * Examinar o pipeline de CI
> * Examinar o pipeline de CD
> * Comprometa alterações a Git e as implemente automaticamente para o Azure
> * Limpar recursos

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Pode obter uma gratuita através do [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="use-devops-projects-to-deploy-an-aspnet-core-app-to-aks"></a>Use projetos DevOps para implementar uma aplicação ASP.NET Core para AKS

A DevOps Projects cria um oleoduto CI/CD em Pipelines Azure. Você pode criar uma nova organização Azure DevOps ou usar uma organização existente. A DevOps Projects também cria recursos Azure, como um cluster AKS, na subscrição Azure à sua escolha.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

1. No painel esquerdo, selecione **Criar um recurso**.

1. Na caixa de pesquisa, digite **Projetos DevOps,** e, em seguida, **selecione Criar**.

    ![O painel de projetos DevOps](_img/azure-devops-project-github/fullbrowser.png)

1. Selecione **.NET**, e, em seguida, selecione **Next**.

1. Em **'Escolha uma estrutura de aplicação**,selecione **ASP.NET Core**.

1. Selecione **O Serviço Kubernetes**e, em seguida, selecione **Next**. 

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Configure Azure DevOps e uma subscrição Azure

1. Crie uma nova organização Azure DevOps, ou selecione uma organização existente. 

1. Insira um nome para o seu projeto Azure DevOps. 

1. Selecione a sua subscrição do Azure.

1. Para ver configurações adicionais de configuração do Azure e identificar o número de nós para o cluster AKS, selecione **Change**.  
    Este painel apresenta várias opções para configurar o tipo e localização dos serviços Azure.
 
1. Saia da área de configuração Azure e, em seguida, selecione **Done**.  
    Após alguns minutos, o processo está concluído. Uma amostra ASP.NET aplicação Core é configurada num repo Git na sua organização Azure DevOps, é criado um cluster AKS, um pipeline CI/CD é executado, e a sua aplicação é implantada para o Azure. 

    Depois de tudo isto estar concluído, o painel de instrumentos do Projeto Azure DevOps é apresentado no portal Azure. Você também pode ir ao dashboard DevOps Projects diretamente de **todos os recursos** no portal Azure. 

    Este dashboard proporciona visibilidade no seu repositório de código Azure DevOps, no seu pipeline CI/CD e no seu cluster AKS. Pode configurar opções adicionais de CI/CD no seu pipeline Azure DevOps. À direita, **selecione Browse** para visualizar a sua aplicação de execução.

## <a name="examine-the-aks-cluster"></a>Examinar o cluster do AKS

Os Projetos DevOps configuram automaticamente um cluster AKS, que pode explorar e personalizar. Para se familiarizar com o cluster AKS, faça o seguinte:

1. Vá ao painel de projetos DevOps.

1. À direita, selecione o serviço AKS.  
    Abre-se uma vidraça para o aglomerado AKS. Nesta perspetiva, pode realizar várias ações, tais como monitorizar a saúde dos contentores, pesquisar troncos e abrir o painel de instrumentos kubernetes.

1. À direita, selecione **View Kubernetes dashboard**.  
    Opcionalmente, siga os passos para abrir o painel kubernetes.

## <a name="examine-the-ci-pipeline"></a>Examinar o pipeline de CI

Os Projetos DevOps configuram automaticamente um oleoduto CI/CD na sua organização Azure DevOps. Pode explorar e personalizar o pipeline. Para se familiarizar com ele, faça o seguinte:

1. Vá ao painel de projetos DevOps.

1. No topo do painel de projetos DevOps, selecione **Build Pipelines**.  
    Um separador de navegador exibe o pipeline de construção para o seu novo projeto.

1. Aponte para o campo **Status** e, em seguida, selecione a elipse (...).  
    Um menu exibe várias opções, como fazer fila de uma nova construção, parar uma construção e editar o pipeline de construção.

1. Selecione **Editar**.

1. Neste painel, pode examinar as várias tarefas para o seu pipeline de construção.  
    A construção executa várias tarefas, tais como a busca de fontes do repo Git, o restabelecimento de dependências e a publicação de saídas usadas para implantações.

1. Na parte superior do pipeline de compilação, selecione o nome do pipeline de compilação.

1. Mude o nome do seu oleoduto de construção para algo mais descritivo, selecione **Guardar & fila**e, em seguida, selecione **Guardar**.

1. No nome do pipeline de compilação, selecione **Histórico**.  
    Este painel apresenta um rasto de auditoria das suas recentes alterações para a construção. A Azure DevOps acompanha quaisquer alterações feitas ao pipeline de construção, e permite-lhe comparar versões.

1. Selecione **Triggers**.  
    A DevOps Projects cria automaticamente um gatilho ci, e cada compromisso com o repo inicia uma nova construção. Opcionalmente, pode optar por incluir ou excluir balcões do processo de CI.

1. Selecione **Retenção**.  
    Dependendo do seu cenário, pode especificar políticas para manter ou remover um determinado número de construções.

## <a name="examine-the-cd-release-pipeline"></a>Examine o gasoduto de libertação do CD

Os Projetos DevOps criam e confundem automaticamente os passos necessários para implementar da sua organização Azure DevOps para a sua subscrição Azure. Estes passos incluem configurar uma ligação de serviço Azure para autenticar O Azure DevOps à sua subscrição Azure. A automatização também cria um oleoduto de libertação, que fornece o CD ao Azure. Para saber mais sobre o gasoduto de libertação, faça o seguinte:

1. Selecione **Construir e Soltar**e, em seguida, selecione **Lançamentos**.  
    A DevOps Projects cria um oleoduto de libertação para gerir as implantações para o Azure.

1. Selecione a elipsis (...) junto ao seu gasoduto de libertação e, em seguida, **selecione Editar**.  
    O pipeline de lançamento contém um *pipeline*, que define o processo de lançamento.

1. Em **Artefactos**, selecione **Remover**.  
    O oleoduto de construção que examinou nos passos anteriores produz a saída que é usada para o artefacto. 

1. À direita do ícone **Drop,** selecione **O gatilho de implantação contínua**.  
    Este gasoduto de libertação tem um gatilho de CD ativado, que executa uma implantação sempre que um novo artefacto de construção está disponível. Opcionalmente, pode desativar o gatilho de modo a que as suas implementações exijam execução manual. 

1. À direita, selecione **ver ver ver para** mostrar um histórico de lançamentos.

1. Selecione a elipse (...) junto a um lançamento e, em seguida, **selecione Open**.  
    Pode explorar vários menus, tais como um resumo de lançamento, itens de trabalho associados e testes.

1. Selecione **Consolidações**.  
    Esta visão mostra código compromete-se que estão associados a esta implantação. Compare as versões para ver as diferenças de consolidação entre implementações.

1. Selecionar **Registos**.  
    Os registos contêm informações úteis sobre o processo de implementação. Pode vê-los durante e após as implantações.

## <a name="commit-changes-to-azure-repos-and-automatically-deploy-them-to-azure"></a>Comprometa alterações ao Azure Repos e desempene-as automaticamente para o Azure 

 > [!NOTE]
 > O procedimento seguinte testa o gasoduto CI/CD fazendo uma simples alteração de texto.

Está agora pronto para colaborar com uma equipa na sua aplicação utilizando um processo CI/CD que implementa automaticamente o seu mais recente trabalho no seu website. Cada alteração para o repo Git inicia uma construção em Azure DevOps, e um pipeline de CD executa uma implantação para Azure. Siga o procedimento nesta secção ou utilize outra técnica para efetuar alterações no seu repo. Por exemplo, pode clonar o repo Git na sua ferramenta favorita ou IDE, e, em seguida, empurrar alterações para este repo.

1. No menu Azure DevOps, selecione **Code** > **Files**e, em seguida, vá ao seu repo.

1. Vá ao diretório *Views\Home,* selecione a elipsis (...) ao lado do ficheiro *Index.cshtml* e, em seguida, selecione **Editar**.

1. Faça uma alteração no ficheiro, como por exemplo, adicionar algum texto dentro de uma das etiquetas de div. 

1. Na parte superior direita, selecione **Comprometer**- e, em seguida, selecione **Comprometer** novamente para empurrar a sua mudança.  
    Após alguns momentos, uma construção começa em Azure DevOps e uma libertação executa para implementar as mudanças. Monitorize o estado de construção no painel de projetos DevOps ou no navegador com a sua organização Azure DevOps.

1. Depois de concluído o lançamento, refresque a sua aplicação para verificar as suas alterações.

## <a name="clean-up-resources"></a>Limpar recursos

Se estiver a testar, pode evitar acumular taxas de faturação limpando os seus recursos. Quando já não são necessários, pode eliminar o cluster AKS e os recursos relacionados que criou neste tutorial. Para isso, utilize a funcionalidade **Eliminar** no painel de instrumentos de Projetos DevOps.

> [!IMPORTANT]
> O procedimento seguinte elimina permanentemente os recursos. A funcionalidade *Delete* destrói os dados criados pelo projeto em Projetos DevOps tanto no Azure como no Azure DevOps, e não poderá recuperá-los. Utilize este procedimento apenas depois de ter lido atentamente as instruções.

1. No portal Azure, vá ao painel de projetos DevOps.
2. Na parte superior direita, selecione **Delete**. 
3. No momento, selecione **Sim** para *apagar permanentemente* os recursos.

## <a name="next-steps"></a>Passos seguintes

Opcionalmente, pode modificar estes pipelines de compilação e versão para satisfazer as necessidades da sua equipa. Também pode utilizar este padrão de CI/CD como modelo para outros pipelines. Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Use projetos DevOps para implementar uma aplicação ASP.NET Core para AKS
> * Configure Azure DevOps e uma subscrição Azure 
> * Examinar o cluster do AKS
> * Examinar o pipeline de CI
> * Examinar o pipeline de CD
> * Comprometa alterações a Git e as implemente automaticamente para o Azure
> * Limpar recursos

Para saber mais sobre a utilização do dashboard Kubernetes, consulte:

> [!div class="nextstepaction"]
> [Utilizar o dashboard do Kubernetes](https://docs.microsoft.com/azure/aks/kubernetes-dashboard)
