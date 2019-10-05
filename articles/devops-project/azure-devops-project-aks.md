---
title: 'Tutorial: Implantar aplicativos ASP.NET Core no serviço kubernetes do Azure com Azure DevOps Projects'
description: Projetos de DevOps do Azure torna mais fácil começar a utilizar no Azure. Com DevOps Projects, você pode implantar seu aplicativo ASP.NET Core com o serviço de kubernetes do Azure (AKS) em algumas etapas rápidas.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: b27d56d78296dc5500f97802f811a8923c4e87a8
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2019
ms.locfileid: "71969677"
---
# <a name="tutorial-deploy-aspnet-core-apps-to-azure-kubernetes-service-with-azure-devops-projects"></a>Tutorial: Implantar aplicativos ASP.NET Core no serviço kubernetes do Azure com Azure DevOps Projects

Azure DevOps Projects apresenta uma experiência simplificada em que você pode colocar seu código existente e o repositório git ou escolher um aplicativo de exemplo para criar um pipeline de CI (integração contínua) e um CD (entrega contínua) para o Azure. 

DevOps Projects também:
* Cria automaticamente recursos do Azure, como o AKS (serviço kubernetes do Azure).
* Cria e configura um pipeline de liberação no Azure DevOps que configura um pipeline de compilação e versão para CI/CD.
* Cria um recurso de informações de Aplicativo Azure para monitoramento.
* Habilita [Azure monitor para contêineres](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview) monitorarem o desempenho das cargas de trabalho do contêiner no cluster AKs

Neste tutorial, irá:

> [!div class="checklist"]
> * Usar DevOps Projects para implantar um aplicativo de ASP.NET Core no AKS
> * Configurar o Azure DevOps e uma subscrição do Azure 
> * Examinar o cluster do AKS
> * Examinar o pipeline de CI
> * Examinar o pipeline de CD
> * Confirmar alterações no git e implantá-las automaticamente no Azure
> * Limpar recursos

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Pode obter uma subscrição gratuita através do [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="use-devops-projects-to-deploy-an-aspnet-core-app-to-aks"></a>Usar DevOps Projects para implantar um aplicativo de ASP.NET Core no AKS

DevOps Projects cria um pipeline de CI/CD no Azure Pipelines. Você pode criar uma nova organização de DevOps do Azure ou usar uma organização existente. DevOps Projects também cria recursos do Azure, como um cluster AKS, na assinatura do Azure de sua escolha.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. No painel esquerdo, selecione **criar um recurso**.

1. Na caixa de pesquisa, digite **DevOps Projects**e, em seguida, selecione **criar**.

    ![O painel DevOps Projects](_img/azure-devops-project-github/fullbrowser.png)

1. Selecione **.net**e, em seguida, selecione **Avançar**.

1. Em **escolher uma estrutura de aplicativo**, selecione **ASP.NET Core**.

1. Selecione **serviço kubernetes**e, em seguida, selecione **Avançar**. 

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Configurar o Azure DevOps e uma subscrição do Azure

1. Crie uma nova organização de DevOps do Azure ou selecione uma organização existente. 

1. Insira um nome para seu projeto DevOps do Azure. 

1. Selecione a sua subscrição do Azure.

1. Para exibir as definições de configuração adicionais do Azure e identificar o número de nós para o cluster AKS, selecione **alterar**.  
    Esse painel exibe várias opções para configurar o tipo e o local dos serviços do Azure.
 
1. Saia da área de configuração do Azure e, em seguida, selecione **concluído**.  
    Após alguns minutos, o processo é concluído. Um aplicativo de ASP.NET Core de exemplo é configurado em um repositório git em sua organização DevOps do Azure, um cluster AKS é criado, um pipeline de CI/CD é executado e seu aplicativo é implantado no Azure. 

    Depois que tudo isso for concluído, o painel do projeto DevOps do Azure será exibido na portal do Azure. Você também pode acessar o painel de DevOps Projects diretamente de **todos os recursos** na portal do Azure. 

    Esse painel fornece visibilidade do seu repositório de código DevOps do Azure, seu pipeline de CI/CD e seu cluster AKS. Você pode configurar opções adicionais de CI/CD no pipeline DevOps do Azure. À direita, selecione **procurar** para exibir o aplicativo em execução.

## <a name="examine-the-aks-cluster"></a>Examinar o cluster do AKS

DevOps Projects configura automaticamente um cluster AKS, que você pode explorar e personalizar. Para se familiarizar com o cluster AKS, faça o seguinte:

1. Vá para o painel de DevOps Projects.

1. À direita, selecione o serviço AKS.  
    Um painel é aberto para o cluster AKS. Nessa exibição, você pode executar várias ações, como monitoramento da integridade do contêiner, pesquisa de logs e abertura do painel do kubernetes.

1. À direita, selecione **exibir painel do kubernetes**.  
    Opcionalmente, siga as etapas para abrir o painel do kubernetes.

## <a name="examine-the-ci-pipeline"></a>Examinar o pipeline de CI

DevOps Projects configura automaticamente um pipeline de CI/CD em sua organização DevOps do Azure. Pode explorar e personalizar o pipeline. Para se familiarizar com ele, faça o seguinte:

1. Vá para o painel de DevOps Projects.

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

## <a name="examine-the-cd-release-pipeline"></a>Examinar o pipeline de liberação de CD

DevOps Projects cria e configura automaticamente as etapas necessárias para implantar de sua organização do Azure DevOps em sua assinatura do Azure. Essas etapas incluem a configuração de uma conexão de serviço do Azure para autenticar o Azure DevOps em sua assinatura do Azure. A automação também cria um pipeline de lançamento, que fornece o CD para o Azure. Para saber mais sobre o pipeline de lançamento, faça o seguinte:

1. Selecione **Compilar e liberar**e, em seguida, selecione **versões**.  
    DevOps Projects cria um pipeline de liberação para gerenciar implantações no Azure.

1. Selecione as reticências (...) ao lado de seu pipeline de lançamento e, em seguida, selecione **Editar**.  
    O pipeline de lançamento contém um *pipeline*, que define o processo de lançamento.

1. Em **Artefactos**, selecione **Remover**.  
    O pipeline de Build que você examinou nas etapas anteriores produz a saída usada para o artefato. 

1. À direita do ícone de **soltar** , selecione **gatilho de implantação contínua**.  
    Este pipeline de lançamento tem um gatilho de CD habilitado, que executa uma implantação sempre que um novo artefato de compilação está disponível. Opcionalmente, pode desativar o acionador para que as suas implementações exigem execução manual. 

1. À direita, selecione **Exibir liberações** para exibir um histórico de versões.

1. Selecione as reticências (...) ao lado de uma versão e, em seguida, selecione **abrir**.  
    Você pode explorar vários menus, como um resumo de lançamento, itens de trabalho associados e testes.

1. Selecione **Consolidações**.  
    Esta exibição mostra as confirmações de código que estão associadas a essa implantação. Compare as versões para ver as diferenças de consolidação entre implementações.

1. Selecione **Registos**.  
    Os registos contêm informações úteis sobre o processo de implementação. Você pode exibi-los durante e após as implantações.

## <a name="commit-changes-to-azure-repos-and-automatically-deploy-them-to-azure"></a>Confirmar alterações para Azure Repos e implantá-las automaticamente no Azure 

 > [!NOTE]
 > O procedimento a seguir testa o pipeline de CI/CD fazendo uma alteração de texto simples.

Agora você está pronto para colaborar com uma equipe em seu aplicativo usando um processo de CI/CD que implanta automaticamente seu trabalho mais recente em seu site. Cada alteração no repositório git inicia uma compilação no Azure DevOps e um pipeline de CD executa uma implantação no Azure. Siga o procedimento nesta seção ou use outra técnica para confirmar as alterações no seu repositório. Por exemplo, você pode clonar o repositório git em sua ferramenta favorita ou IDE e, em seguida, enviar por push as alterações para esse repositório.

1. No menu DevOps do Azure, selecione **código** > **arquivos**e, em seguida, vá para o repositório.

1. Vá para o diretório *views\home* , selecione as reticências (...) ao lado do arquivo *index. cshtml* e, em seguida, selecione **Editar**.

1. Faça uma alteração no arquivo, como adicionar texto dentro de uma das marcas div. 

1. No canto superior direito, selecione **confirmar**e, em seguida, selecione **confirmar** novamente para enviar por push sua alteração.  
    Depois de alguns instantes, uma compilação é iniciada no Azure DevOps e uma versão é executada para implantar as alterações. Monitore o status da compilação no painel DevOps Projects ou no navegador com sua organização DevOps do Azure.

1. Depois que o lançamento for concluído, atualize seu aplicativo para verificar suas alterações.

## <a name="clean-up-resources"></a>Limpar recursos

Se você estiver testando, poderá evitar a acumulação de encargos de cobrança limpando seus recursos. Quando eles não forem mais necessários, você poderá excluir o cluster AKS e os recursos relacionados que você criou neste tutorial. Para fazer isso, use a funcionalidade **excluir** no painel DevOps Projects.

> [!IMPORTANT]
> O procedimento a seguir exclui permanentemente os recursos. A funcionalidade de *exclusão* destrói os dados criados pelo projeto em DevOps Projects no Azure e no Azure DevOps, e você não poderá recuperá-los. Use este procedimento somente depois de ler atentamente os prompts.

1. Na portal do Azure, vá para o painel de DevOps Projects.
2. No canto superior direito, selecione **excluir**. 
3. No prompt, selecione **Sim** para *excluir permanentemente* os recursos.

## <a name="next-steps"></a>Passos seguintes

Opcionalmente, pode modificar estes pipelines de compilação e versão para satisfazer as necessidades da sua equipa. Também pode utilizar este padrão de CI/CD como modelo para outros pipelines. Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Usar DevOps Projects para implantar um aplicativo de ASP.NET Core no AKS
> * Configurar o Azure DevOps e uma subscrição do Azure 
> * Examinar o cluster do AKS
> * Examinar o pipeline de CI
> * Examinar o pipeline de CD
> * Confirmar alterações no git e implantá-las automaticamente no Azure
> * Limpar recursos

Para saber mais sobre como usar o painel do kubernetes, consulte:

> [!div class="nextstepaction"]
> [Utilizar o dashboard do Kubernetes](https://docs.microsoft.com/azure/aks/kubernetes-dashboard)
