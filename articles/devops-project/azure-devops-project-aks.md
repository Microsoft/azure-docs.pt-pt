---
title: Implementar aplicativos core ASP.NET para o serviço Azure Kubernetes com Azure DevOps Starter
description: O Azure DevOps Starter facilita o arranque do Azure. Com o DevOps Starter, pode implementar a sua aplicação Core ASP.NET com o Serviço Azure Kubernetes (AKS) em alguns passos rápidos.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 03/24/2020
author: mlearned
ms.openlocfilehash: 9ccf28f5431a92f71b1c18e609639d0abf309c06
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100590859"
---
# <a name="deploy-aspnet-core-apps-to-azure-kubernetes-service-with-azure-devops-starter"></a>Implementar aplicativos core ASP.NET para o serviço Azure Kubernetes com Azure DevOps Starter

O Azure DevOps Starter apresenta uma experiência simplificada onde pode trazer o seu código existente e git repo ou escolher uma aplicação de amostra para criar um pipeline de integração contínua (CI) e entrega contínua (CD) para Azure. 

DevOps Starter também:

* Cria automaticamente recursos Azure, como o Serviço Azure Kubernetes (AKS).
* Cria e configura um gasoduto de libertação em Azure DevOps que cria um gasoduto de construção e libertação para CI/CD.
* Cria um recurso Azure Application Insights para monitorização.
* Permite ao [Monitor Azure para os contentores](../azure-monitor/containers/container-insights-overview.md) monitorizar o desempenho das cargas de trabalho dos contentores no cluster AKS

Neste tutorial, vai:

> [!div class="checklist"]
> * Utilize o DevOps Starter para implementar uma aplicação core ASP.NET para AKS
> * Configurar Azure DevOps e uma subscrição da Azure 
> * Examinar o cluster do AKS
> * Examinar o pipeline de CI
> * Examinar o pipeline de CD
> * Cometa alterações no Git e desloque-as automaticamente para a Azure
> * Limpar os recursos

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Pode obter uma gratuita através do [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="use-devops-starter-to-deploy-an-aspnet-core-app-to-aks"></a>Utilize o DevOps Starter para implementar uma aplicação core ASP.NET para AKS

DevOps Starter cria um gasoduto CI/CD em Pipelines Azure. Pode criar uma nova organização Azure DevOps ou utilizar uma organização existente. O DevOps Starter também cria recursos Azure, como um cluster AKS, na subscrição Azure da sua escolha.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Na caixa de pesquisa, escreva **DevOps Starter** e, em seguida, selecione. Clique em **Adicionar** para criar um novo.

    ![O painel de arranque de DevOps](_img/azure-devops-starter-aks/search-devops-starter.png)

1. Selecione **.NET** e, em seguida, selecione **Seguinte**.

1. Em **Escolha uma estrutura de aplicação,** selecione ASP.NET **Core** e, em seguida, selecione **Seguinte**.

1. Selecione **o Serviço Kubernetes** e, em seguida, selecione **Next**. 

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Configurar Azure DevOps e uma subscrição da Azure

1. Crie uma nova organização Azure DevOps ou selecione uma organização existente. 

1. Insira um nome para o seu projeto Azure DevOps. 

1. Selecione a sua subscrição do Azure.

1. Para visualizar as definições adicionais de configuração do Azure e identificar o número de nós para o cluster AKS, selecione **Change**. Este painel apresenta várias opções para configurar o tipo e a localização dos serviços Azure.
 
1. Saia da área de configuração Azure e, em seguida, selecione **Fazer**. Após alguns minutos, o processo está concluído. Uma amostra ASP.NET aplicação Core é criada num git repo na sua organização Azure DevOps, um cluster AKS é criado, um pipeline CI/CD é executado, e sua app é implementada para Azure. 

    Depois de tudo isto estar concluído, o painel de arranque Azure DevOps é apresentado no portal Azure. Também pode ir ao painel de arranque de DevOps diretamente de **todos os recursos** do portal Azure. 

    Este dashboard proporciona visibilidade ao repositório de códigoS Azure DevOps, ao seu pipeline CI/CD e ao seu cluster AKS. Pode configurar opções adicionais de CI/CD no seu pipeline Azure DevOps. À direita, **selecione Procurar** para ver a sua aplicação de execução.

## <a name="examine-the-aks-cluster"></a>Examinar o cluster do AKS

O DevOps Starter configura automaticamente um cluster AKS, que pode explorar e personalizar. Para se familiarizar com o cluster AKS, faça o seguinte:

1. Vá ao painel de arranque do DevOps.

1. À direita, selecione o serviço AKS. Abre-se um painel para o aglomerado AKS. A partir desta vista, você pode executar várias ações, tais como monitorizar a saúde do recipiente, pesquisar registos e abrir o dashboard Kubernetes.

1. À direita, selecione **ver o painel de instrumentos de Kubernetes**. Opcionalmente, siga os passos para abrir o tablier Kubernetes.

## <a name="examine-the-ci-pipeline"></a>Examinar o pipeline de CI

O DevOps Starter configura automaticamente um pipeline CI/CD na sua organização Azure DevOps. Pode explorar e personalizar o pipeline. Para se familiarizar com ele, faça o seguinte:

1. Vá ao painel de arranque do DevOps.

1. No topo do painel de arranque de DevOps, selecione **Build Pipelines**.  Um separador de navegador exibe o pipeline de construção para o seu novo projeto.

1. Aponte para o campo **Status** e, em seguida, selecione a elipse (...).  Um menu apresenta várias opções, como fazer fila de uma nova construção, parar uma construção e editar o pipeline de construção.

1. Selecione **Editar**.

1. Neste painel, pode examinar as várias tarefas para o seu oleoduto de construção. A construção executa várias tarefas, tais como buscar fontes do repo Git, restaurar dependências e publicar saídas usadas para implementações.

1. Na parte superior do pipeline de compilação, selecione o nome do pipeline de compilação.

1. Mude o nome do seu pipeline de construção para algo mais descritivo, **selecione Save & fila** e, em seguida, selecione **Guardar**.

1. No nome do pipeline de compilação, selecione **Histórico**. Este painel apresenta um rasto de auditoria das suas recentes alterações para a construção. O Azure DevOps regista quaisquer alterações feitas ao pipeline de construção, e permite comparar versões.

1. Selecione **Triggers**. O DevOps Starter cria automaticamente um gatilho de CI, e cada compromisso com o repo inicia uma nova construção. Opcionalmente, pode optar por incluir ou excluir os ramos do processo de IC.

1. Selecione **Retenção**. Dependendo do seu cenário, pode especificar políticas para manter ou remover um determinado número de construções.

## <a name="examine-the-cd-release-pipeline"></a>Examine o oleoduto de libertação de CD

O DevOps Starter cria e configura automaticamente os passos necessários para implementar da sua organização Azure DevOps para a sua subscrição Azure. Estes passos incluem configurar uma ligação de serviço Azure para autenticar Azure DevOps para a sua subscrição Azure. A automatização também cria um gasoduto de libertação, que fornece o CD ao Azure. Para saber mais sobre o oleoduto de lançamento, faça o seguinte:

1. Selecione **Construir e Soltar** e, em seguida, selecione **Versões**.  O DevOps Starter cria um oleoduto de libertação para gerir as implementações para o Azure.

1. Selecione a elipse (...) ao lado do seu pipeline de lançamento e, em seguida, **selecione Editar**. O pipeline de lançamento contém um *pipeline*, que define o processo de lançamento.

1. Em **Artefactos**, selecione **Remover**. O gasoduto de construção que examinou nos passos anteriores produz a saída que é usada para o artefacto. 

1. À direita do ícone **Drop,** selecione **o gatilho de implementação contínua**. Este oleoduto de lançamento tem um disparador de CD ativado, que executa uma implantação sempre que um novo artefacto de construção está disponível. Opcionalmente, pode desativar o gatilho para que as suas implementações exijam uma execução manual. 

1. À direita, **selecione Ver ver lançamentos** para exibir um histórico de lançamentos.

1. Selecione a elipse (...) ao lado de uma versão e, em seguida, selecione **Abrir**. Pode explorar vários menus, como um resumo de lançamento, itens de trabalho associados e testes.

1. Selecione **Consolidações**. Esta visão mostra compromissos de código que estão associados a esta implementação. Compare as versões para ver as diferenças de consolidação entre implementações.

1. Selecionar **Registos**. Os registos contêm informações úteis sobre o processo de implementação. Pode vê-los durante e após as missões.

## <a name="commit-changes-to-azure-repos-and-automatically-deploy-them-to-azure"></a>Cometa alterações no Azure Repos e desloque-os automaticamente para a Azure 

 > [!NOTE]
 > O procedimento a seguir testa o gasoduto CI/CD através de uma simples alteração de texto.

Está agora pronto para colaborar com uma equipa na sua aplicação, utilizando um processo ci/CD que implementa automaticamente o seu último trabalho no seu website. Cada mudança para o git repo inicia uma construção em Azure DevOps, e um oleoduto de CD executa uma implantação para Azure. Siga o procedimento nesta secção ou use outra técnica para comprometer alterações no seu repo. Por exemplo, pode clonar o repo Git na sua ferramenta preferida ou IDE e, em seguida, empurrar alterações para este repo.

1. No menu Azure DevOps, selecione   >  **Code Files** e, em seguida, vá para o seu repo.

1. Vá ao *diretório Views\Home,* selecione a elipse (...) ao lado do ficheiro *Index.cshtml* e, em seguida, selecione **Editar**.

1. Faça uma alteração no ficheiro, como por exemplo adicionar algum texto dentro de uma das tags de div. 

1. No topo direito, **selecione Commit** e, em seguida, selecione **Comprometa-se** novamente para empurrar a sua mudança. Após alguns momentos, uma construção começa em Azure DevOps e um lançamento executa para implementar as alterações. Monitorize o estado de construção no painel de partidas de DevOps ou no navegador com a sua organização Azure DevOps.

1. Depois de concluída a versão, refresque a sua aplicação para verificar as suas alterações.

## <a name="clean-up-resources"></a>Limpar os recursos

Se estiver a testar, pode evitar acumular encargos de faturação limpando os seus recursos. Quando já não são necessários, pode eliminar o cluster AKS e os recursos relacionados que criou neste tutorial. Para tal, utilize a funcionalidade **Eliminar** no painel de arranque de DevOps.

> [!IMPORTANT]
> O procedimento que se segue elimina permanentemente os recursos. A funcionalidade *Delete* destrói os dados criados pelo projeto no DevOps Starter tanto em Azure como em Azure DevOps, e não poderá recuperá-los. Utilize este procedimento apenas depois de ter lido atentamente as indicações.

1. No portal Azure, vá ao painel de arranque de DevOps.
1. No topo direito, **selecione Delete**. 
1. A pedido, selecione **Sim** para *eliminar permanentemente* os recursos.

## <a name="next-steps"></a>Passos seguintes

Opcionalmente, pode modificar estes pipelines de compilação e versão para satisfazer as necessidades da sua equipa. Também pode utilizar este padrão de CI/CD como modelo para outros pipelines. Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Utilize o DevOps Starter para implementar uma aplicação core ASP.NET para AKS
> * Configurar Azure DevOps e uma subscrição da Azure 
> * Examinar o cluster do AKS
> * Examinar o pipeline de CI
> * Examinar o pipeline de CD
> * Cometa alterações no Git e desloque-as automaticamente para a Azure
> * Limpar os recursos

Para saber mais sobre a utilização do painel Kubernetes, consulte:

> [!div class="nextstepaction"]
> [Utilizar o dashboard do Kubernetes](../aks/kubernetes-dashboard.md)