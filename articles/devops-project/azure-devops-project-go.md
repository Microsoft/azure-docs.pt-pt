---
title: 'Início rápido: Criar um pipeline de CI/CD para a linguagem de programação go usando Azure DevOps Projects'
description: DevOps Projects facilita a introdução ao Azure. Ele ajuda a iniciar um aplicativo Web da linguagem de programação Go em um serviço do Azure em algumas etapas rápidas.
ms.prod: devops
ms.technology: devops-cicd
services: vsts
documentationcenter: vs-devops-build
author: mlearned
manager: gwallace
editor: ''
ms.assetid: ''
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 07/09/2018
ms.author: mlearned
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: f7429a6de05a301b579354d722ad354b78ce6cbf
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70899680"
---
# <a name="create-a-cicd-pipeline-for-go-by-using-azure-devops-projects"></a>Criar um pipeline de CI/CD para o go usando Azure DevOps Projects

Configure a CI (integração contínua) e o CD (entrega contínua) para seu aplicativo go usando Azure DevOps Projects. DevOps Projects simplifica a configuração inicial de um pipeline de versão e compilação DevOps do Azure.

Se não tiver uma subscrição do Azure, pode obter uma subscrição gratuita através do [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

DevOps Projects cria um pipeline de CI/CD no Azure Pipelines. Você pode criar uma nova organização de DevOps do Azure ou usar uma organização existente. Projetos de DevOps também cria os recursos do Azure na subscrição do Azure à sua escolha.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. No painel esquerdo, selecione **criar um recurso**.

1. Na caixa de pesquisa, digite **projeto DevOps**e, em seguida, selecione **criar**.

    ![O painel DevOps Projects](_img/azure-devops-project-github/fullbrowser.png)

## <a name="select-a-sample-app-and-azure-service"></a>Selecionar um aplicativo de exemplo e um serviço do Azure

1. Selecione o aplicativo de exemplo **go** e, em seguida, selecione **Avançar**.  
    
1. **A Aplicação Go simples** é a estrutura predefinida. Selecione **Seguinte**.  
    A estrutura do aplicativo, que você escolheu anteriormente, determina o tipo de destinos de implantação de serviço do Azure que estão disponíveis para implantação. 
    
1. Deixe o serviço do Azure padrão e selecione **Avançar**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Configurar o Azure DevOps e uma subscrição do Azure 

1. Crie uma nova organização do Azure DevOps gratuita ou escolher uma organização existente. 

1. Insira um nome para seu projeto DevOps do Azure. 

1. Selecione sua assinatura e local do Azure, insira um nome para seu aplicativo e, em seguida, selecione **concluído**.  
    Após alguns minutos, é apresentado o dashboard de projetos de DevOps no portal do Azure. Um aplicativo de exemplo é configurado em um repositório em sua organização DevOps do Azure, uma compilação é executada e seu aplicativo é implantado no Azure. 
    
    O painel fornece visibilidade do seu repositório de código, seu pipeline de CI/CD e seu aplicativo no Azure. À direita, selecione **procurar** para exibir o aplicativo em execução.

    ![Vista do Dashboard](_img/azure-devops-project-go/dashboardnopreview.png) 

## <a name="commit-your-code-changes-and-execute-the-cicd"></a>Confirmar as alterações de código e executar o CI/CD

DevOps Projects cria um repositório git no Azure Repos ou no GitHub. Para exibir o repositório e fazer alterações de código em seu aplicativo, faça o seguinte:

1. Na DevOps Projects, à esquerda, selecione o link para o Branch mestre.  
    O link abre uma exibição para o repositório git recém-criado.

1. Para exibir a URL de clone do repositório, selecione **clonar** na parte superior direita.  
    Você pode clonar seu repositório git em seu IDE favorito. Nos próximos passos, pode utilizar o browser para fazer e consolidar alterações de código diretamente no ramo principal.

1. À esquerda, vá para o arquivo *views/index.html* e, em seguida, selecione **Editar**.

1. Faça uma alteração no arquivo. Por exemplo, modifique um texto dentro de uma das marcas div.

1. Selecione **consolidar**e, em seguida, guarde as alterações.

1. No navegador, vá para o painel de DevOps Projects.  
    Uma compilação deve estar em andamento. As alterações que efetuou são automaticamente criadas e implementadas por meio de um pipeline CI/CD.

## <a name="examine-the-cicd-pipeline"></a>Examine o pipeline de CI/CD

DevOps Projects configura automaticamente um pipeline de CI/CD completo no Azure Repos. Explore e personalize o pipeline, conforme necessário. Para se familiarizar com os pipelines de versão e compilação DevOps do Azure, faça o seguinte:

1. Vá para o painel de DevOps Projects.

1. Na parte superior, selecione **criar pipelines**.  
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

1. Selecione **Compilar e liberar**e, em seguida, selecione **versões**.  
    DevOps Projects cria um pipeline de liberação para gerenciar implantações no Azure.

1. Selecione as reticências (...) ao lado de seu pipeline de lançamento e, em seguida, selecione **Editar**.  
    O pipeline de lançamento contém um *pipeline*, que define o processo de lançamento.

1. Em **Artefactos**, selecione **Remover**.  
    O pipeline de compilação examinado anteriormente produz a saída usada para o artefato. 

1. À direita do ícone de **soltar** , selecione **gatilho de implantação contínua**.  
    Este pipeline de lançamento tem um gatilho de CD habilitado, que executa uma implantação sempre que um novo artefato de compilação está disponível. Opcionalmente, pode desativar o acionador para que as suas implementações exigem execução manual. 

1. À esquerda, selecione **tarefas**.  
    As tarefas são as atividades que seu processo de implantação executa. Neste exemplo, uma tarefa foi criada para ser implantada no serviço Azure App.

1. À direita, selecione **Exibir liberações** para exibir um histórico de versões.

1. Selecione as reticências (...) ao lado de uma versão e, em seguida, selecione **abrir**.  
    Você pode explorar vários menus, como um resumo de lançamento, itens de trabalho associados e testes.

1. Selecione **Consolidações**.  
    Esta exibição mostra as confirmações de código que estão associadas a essa implantação. 

1. Selecionar **Registos**.  
    Os registos contêm informações úteis sobre o processo de implementação. Você pode exibi-los durante e após as implantações.

## <a name="clean-up-resources"></a>Limpar recursos

Quando eles não forem mais necessários, você poderá excluir a instância de serviço Azure App e os recursos relacionados que você criou neste guia de início rápido. Para fazer isso, use a funcionalidade **excluir** no painel DevOps Projects.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como modificar os pipelines de Build e versão para atender às necessidades de sua equipe, consulte:

> [!div class="nextstepaction"]
> [Definir o pipeline de implantação contínua de vários estágios (CD)](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
