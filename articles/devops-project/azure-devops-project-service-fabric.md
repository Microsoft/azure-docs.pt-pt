---
title: 'Tutorial: Implemente a sua aplicação Core ASP.NET para o Azure Service Fabric utilizando o Azure DevOps Starter'
description: O Azure DevOps Starter facilita o arranque do Azure. Com projetos DevOps, pode implementar a sua aplicação Core ASP.NET para a Azure Service Fabric em alguns passos rápidos.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 03/24/2020
author: mlearned
ms.openlocfilehash: 723f46652643883dc8e718468a9ca9eead50e1aa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91856029"
---
# <a name="tutorial-deploy-your-aspnet-core-app-to-azure-service-fabric-by-using-azure-devops-starter"></a>Tutorial: Implemente a sua aplicação Core ASP.NET para o Azure Service Fabric utilizando o Azure DevOps Starter

O Azure DevOps Starter apresenta uma experiência simplificada onde pode trazer o seu código existente e git repo ou escolher uma aplicação de amostra para criar um pipeline de integração contínua (CI) e entrega contínua (CD) para Azure. 

DevOps Starter também:

* Cria automaticamente recursos Azure, como o Azure Service Fabric.
* Cria e configura um gasoduto de libertação em Azure DevOps que configura um gasoduto CI/CD.
* Cria um recurso Azure Application Insights para monitorização.

Neste tutorial, irá:

> [!div class="checklist"]
> * Use o DevOps Starter para criar uma aplicação Core ASP.NET e implementá-la no Tecido de Serviço
> * Configurar Azure DevOps e uma subscrição da Azure 
> * Examinar o pipeline de CI
> * Examinar o pipeline de CD
> * Consolidar as alterações ao Git e implementar automaticamente no Azure
> * Limpar os recursos

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Pode obter uma gratuita através do [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="use-devops-starter-to-create-an-aspnet-core-app-and-deploy-it-to-service-fabric"></a>Use o DevOps Starter para criar uma aplicação Core ASP.NET e implementá-la no Tecido de Serviço

DevOps Starter cria um gasoduto CI/CD em Pipelines Azure. Pode criar uma nova organização Azure DevOps ou utilizar uma organização existente. O DevOps Starter também cria recursos Azure, como um cluster de Tecido de Serviço, na subscrição Azure à sua escolha.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Na caixa de pesquisa, escreva **DevOps Starter**e, em seguida, selecione. Clique em **Adicionar** para criar um novo.

    ![O painel de arranque de DevOps](_img/azure-devops-starter-aks/search-devops-starter.png) 

1. Selecione **.NET**e, em seguida, selecione **Seguinte**.

1. Em **Escolha uma estrutura de aplicação,** selecione ASP.NET **Core**e, em seguida, selecione **Seguinte**.

1. Selecione **o Cluster de Tecidos de Serviço**e, em seguida, selecione **Seguinte**. 

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Configurar Azure DevOps e uma subscrição da Azure

1. Crie uma nova organização Azure DevOps ou selecione uma organização existente. 

1. Insira um nome para o seu projeto Azure DevOps. 

1. Selecione a sua subscrição do Azure.

1. Para visualizar as definições adicionais de configuração do Azure e identificar o tamanho e o sistema operativo da máquina virtual do nó para o cluster de Tecido de Serviço, selecione **Change**. Este painel apresenta várias opções para configurar o tipo e a localização dos serviços Azure.
 
1. Saia da área de configuração Azure e, em seguida, selecione **Fazer**.  
    Após alguns minutos, o processo está concluído. Uma amostra ASP.NET aplicação Core é criada num git repo na sua organização Azure DevOps, um cluster de Tecido de Serviço é criado, um pipeline CI/CD é executado, e a sua app é implementada para Azure. 

    Depois de tudo isto estar concluído, o painel de arranque de DevOps é apresentado no portal Azure. Também pode ir ao painel de arranque de DevOps diretamente de **todos os recursos** do portal Azure. 

    Este dashboard proporciona visibilidade ao seu repo de código Azure DevOps, ao seu pipeline CI/CD e ao cluster de Tecido de Serviço. Pode configurar opções adicionais para o seu oleoduto CI/CD em Azure Repos. À direita, **selecione Procurar** para ver a sua aplicação de execução.

## <a name="examine-the-ci-pipeline"></a>Examinar o pipeline de CI

O DevOps Starter configura automaticamente um gasoduto CI/CD em Pipelines Azure. Pode explorar e personalizar o pipeline. Para se familiarizar com ele, faça o seguinte:

1. Vá ao painel de arranque do DevOps.

1. No topo do painel de arranque de DevOps, selecione **Construir oleodutos**. Um separador de navegador exibe o pipeline de construção para o seu novo projeto.

1. Aponte para o campo **Status** e, em seguida, selecione a elipse (...). Um menu apresenta várias opções, como fazer fila de uma nova construção, parar uma construção e editar o pipeline de construção.

1. Selecione **Editar**.

1. Neste painel, pode examinar as várias tarefas para o seu oleoduto de construção. A construção executa várias tarefas, tais como buscar fontes do repo Git, restaurar dependências e publicar saídas usadas para implementações.

1. Na parte superior do pipeline de compilação, selecione o nome do pipeline de compilação. 

1. No nome do pipeline de compilação, selecione **Histórico**. Este painel apresenta um rasto de auditoria das suas recentes alterações para a construção. O Azure DevOps regista quaisquer alterações feitas ao pipeline de construção, e permite comparar versões.

1. Selecione **Triggers**. O DevOps Starter cria automaticamente um gatilho de CI, e cada compromisso com o repo inicia uma nova construção. Opcionalmente, pode optar por incluir ou excluir os ramos do processo de IC.

1. Selecione **Retenção**. Dependendo do seu cenário, pode especificar políticas para manter ou remover um determinado número de construções.

## <a name="examine-the-cd-pipeline"></a>Examinar o pipeline de CD

O DevOps Starter cria e configura automaticamente os passos necessários para implementar da sua organização Azure DevOps para a sua subscrição Azure. Estes passos incluem configurar uma ligação de serviço Azure para autenticar Azure DevOps para a sua subscrição Azure. A automatização também cria um gasoduto de libertação, que fornece o CD ao Azure. Para saber mais sobre o oleoduto de lançamento, faça o seguinte:

1. Selecione **Construir e Soltar**e, em seguida, selecione **Versões**. O DevOps Starter cria um oleoduto de libertação para gerir as implementações para o Azure.

1. Selecione a elipse (...) ao lado do seu pipeline de lançamento e, em seguida, **selecione Editar**. O pipeline de lançamento contém um *pipeline*, que define o processo de lançamento.

1. Em **Artefactos**, selecione **Remover**. O oleoduto de construção que examinou anteriormente produz a saída que é usada para o artefacto. 

1. À direita do ícone **Drop,** selecione **o gatilho de implementação contínua**. Este oleoduto de lançamento tem um disparador de CD ativado, que executa uma implantação sempre que um novo artefacto de construção está disponível. Opcionalmente, pode desativar o gatilho para que as suas implementações exijam uma execução manual. 

1. À direita, **selecione Ver ver lançamentos** para exibir um histórico de lançamentos.

1. Selecione a elipse (...) ao lado de uma versão e, em seguida, selecione **Abrir**. Pode explorar vários menus, como um resumo de lançamento, itens de trabalho associados e testes.

1. Selecione **Consolidações**. Esta visão mostra compromissos de código que estão associados a esta implementação. Compare as versões para ver as diferenças de consolidação entre implementações.

1. Selecionar **Registos**. Os registos contêm informações úteis sobre o processo de implementação. Pode vê-los durante e após as missões.

## <a name="commit-changes-to-git-and-automatically-deploy-them-to-azure"></a>Cometa alterações no Git e desloque-as automaticamente para a Azure 

 > [!NOTE]
 > O procedimento a seguir testa o gasoduto CI/CD através de uma simples alteração de texto.

Está agora pronto para colaborar com uma equipa na sua aplicação, utilizando um processo ci/CD que implementa automaticamente o seu último trabalho no seu website. Cada mudança para o git repo inicia uma construção, e um lançamento implementa as suas alterações para Azure. Siga o procedimento nesta secção ou use outra técnica para comprometer alterações no seu repo. Por exemplo, pode clonar o repo Git na sua ferramenta preferida ou IDE e, em seguida, empurrar alterações para este repo.

1. No menu Azure DevOps, selecione **Code**  >  **Code Files**e, em seguida, vá para o seu repo.

1. Vá ao *diretório Views\Home,* selecione a elipse (...) ao lado do ficheiro *Index.cshtml* e, em seguida, selecione **Editar**.

1. Faça uma alteração no ficheiro, como por exemplo adicionar algum texto dentro de uma das tags de div. 

1. No topo direito, **selecione Commit**e, em seguida, selecione **Comprometa-se** novamente para empurrar a sua mudança.  
    Após alguns momentos, uma construção começa e, em seguida, um lançamento executa para implementar as alterações. Pode monitorizar o estado de construção no painel de partidas de DevOps ou no navegador com a Azure DevOps em tempo real.

1. Depois de concluída a versão, refresque a sua aplicação para verificar as suas alterações.

## <a name="clean-up-resources"></a>Limpar os recursos

Se estiver a testar, pode evitar acumular encargos de faturação limpando os seus recursos. Quando já não são necessários, pode eliminar o cluster Azure Service Fabric e os recursos conexos que criou neste tutorial. Para tal, utilize a funcionalidade **Eliminar** no painel de arranque de DevOps.

> [!IMPORTANT]
> O procedimento que se segue elimina permanentemente os recursos. A funcionalidade *Delete* destrói os dados criados pelo projeto no DevOps Starter tanto em Azure como em Azure DevOps, e não poderá recuperá-los. Utilize este procedimento apenas depois de ter lido atentamente as indicações.

1. No portal Azure, vá ao painel de arranque de DevOps.
1. No topo direito, **selecione Delete**. 
1. A pedido, selecione **Sim** para *eliminar permanentemente* os recursos.

## <a name="next-steps"></a>Passos seguintes

Opcionalmente, pode modificar os pipelines de CI/CD do Azure para satisfazer as necessidades da sua equipa. Também pode utilizar este padrão de CI/CD como modelo para outros pipelines. Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Use o DevOps Starter para criar uma aplicação Core ASP.NET e implementá-la no Tecido de Serviço
> * Configurar Azure DevOps e uma subscrição da Azure 
> * Examinar o pipeline de CI
> * Examinar o pipeline de CD
> * Cometa alterações no Git e desloque-as automaticamente para a Azure
> * Limpar os recursos

Para saber mais sobre o Service Fabric e microserviços, consulte:

> [!div class="nextstepaction"]
> [Utilizar a abordagem de microsserviços para criar aplicações](/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)