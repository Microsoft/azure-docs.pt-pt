---
title: Centro de Implantação de Azure Kubernetes
description: O Centro de Implantação em Azure DevOps simplifica a criação de um robusto oleoduto Azure DevOps para a sua aplicação
ms.author: puagarw
ms.topic: tutorial
ms.date: 07/12/2019
author: pulkitaggarwl
ms.openlocfilehash: 15b0413eabcfae7e3a4b28243caf2a708260ccae
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96932222"
---
# <a name="deployment-center-for-azure-kubernetes"></a>Centro de Implantação de Azure Kubernetes

O Centro de Implementação em Azure DevOps simplifica a criação de um robusto oleoduto Azure DevOps para a sua aplicação. Por predefinição, o Centro de Implementação configura um oleoduto Azure DevOps para implementar as atualizações da sua aplicação no cluster Kubernetes. Pode estender o pipeline Azure DevOps configurado por defeito e também adicionar capacidades mais ricas: a capacidade de obter aprovação antes de implementar, a disponibilização de recursos adicionais de Azure, executar scripts, atualizar a sua aplicação e até executar mais testes de validação.

Neste tutorial, vai:

> [!div class="checklist"]
> * Configure um oleoduto Azure DevOps para implementar as atualizações da sua aplicação no cluster Kubernetes.
> * Examinar o gasoduto de integração contínua (CI).
> * Examine o gasoduto de entrega contínua (CD).
> * Limpe os recursos.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Pode obter uma gratuita através do [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

* Um cluster de serviço Azure Kubernetes (AKS).

## <a name="create-an-aks-cluster"></a>Criar um cluster do AKS (Create an AKS cluster)

1. Inscreva-se no seu [portal Azure](https://portal.azure.com/).

1. Selecione a opção [Cloud Shell](../cloud-shell/overview.md) no lado direito da barra de menu no portal Azure.

1. Para criar o cluster AKS, executar os seguintes comandos:

    ```azurecli
    # Create a resource group in the South India location:

    az group create --name azooaks --location southindia

    # Create a cluster named azookubectl with one node.

    az aks create --resource-group azooaks --name azookubectl --node-count 1 --enable-addons monitoring --generate-ssh-keys
    ```

## <a name="deploy-application-updates-to-a-kubernetes-cluster"></a>Implementar atualizações de aplicações para um cluster Kubernetes

1. Vá ao grupo de recursos que criou na secção anterior.

1. Selecione o cluster AKS e, em seguida, selecione **o Centro de Implementação (pré-visualização)** na lâmina esquerda. **Selecione Começar**.

   ![A screenshot mostra o portal Azure com uma seta apontando para o centro de implantação.](media/deployment-center-launcher/settings.png)

1. Escolha a localização do código e selecione **Seguinte**. Em seguida, selecione um dos repositórios atualmente **[suportados: Azure Repos](/azure/devops/repos/index)** ou **GitHub**.

    Azure Repos é um conjunto de ferramentas de controlo de versão que o ajudam a gerir o seu código. Se o seu projeto de software é grande ou pequeno, usar o controlo de versão o mais cedo possível é uma boa ideia.

    - **Azure Repos**: Escolha um repositório do seu projeto e organização existentes.

        ![Repositórios do Azure](media/deployment-center-launcher/azure-repos.gif)

    - **GitHub**: Autorize e selecione o repositório para a sua conta GitHub.

        ![A animação mostra um processo no GitHub de selecionar o GitHub como fonte e, em seguida, selecionar o seu repositório.](media/deployment-center-launcher/github.gif)


1. O Centro de Implantação analisa o repositório e deteta o seu Dockerfile. Se quiser atualizar o Dockerfile, pode editar o número de porta identificado.

    ![Definições de Aplicação](media/deployment-center-launcher/application-settings.png)

    Se o repositório não contiver o Dockerfile, o sistema apresenta uma mensagem para cometer uma.

    ![A screenshot mostra o centro de implantação com uma mensagem Não foi possível encontrar Dockerfile no repositório.](media/deployment-center-launcher/dockerfile.png)

1. Selecione um registo de contentores existente ou crie um e, em seguida, **selecione Terminar**. O gasoduto é criado automaticamente e faz fila para construir em [Azure Pipelines](/azure/devops/pipelines/index).

    A Azure Pipelines é um serviço de nuvem que pode utilizar para construir e testar automaticamente o seu projeto de código e disponibilizá-lo a outros utilizadores. A Azure Pipelines combina integração contínua e entrega contínua para testar e construir constantemente e de forma consistente o seu código e enviá-lo para qualquer alvo.

    ![Container Registry](media/deployment-center-launcher/container-registry.png)

1. Selecione o link para ver o gasoduto em curso.

1. Verá os registos bem sucedidos após a implementação estar concluída.

    ![A screenshot mostra o centro de implantação com o Release-1 marcado com um ícone de marca de verificação verde.](media/deployment-center-launcher/logs.png)

## <a name="examine-the-ci-pipeline"></a>Examinar o pipeline de CI

O Centro de Implementação configura automaticamente o pipeline CI/CD da organização Azure DevOps. O gasoduto pode ser explorado e personalizado.

1. Vá ao painel do Centro de Implantação.  

1. Selecione o número de construção da lista de registos bem sucedidos para ver o pipeline de construção para o seu projeto.

1. Selecione a elipse (...) no canto superior direito. Um menu mostra várias opções, como fazer fila de uma nova construção, reter uma construção e editar o pipeline de construção. **Selecione o pipeline Edit**. 

1. Pode examinar as diferentes tarefas para o seu oleoduto de construção neste painel. A construção executa várias tarefas, tais como recolher fontes do repositório Git, criar uma imagem, empurrar uma imagem para o registo do contentor, e publicar saídas que são usadas para implantações.

1. Selecione o nome do gasoduto de construção na parte superior do oleoduto.

1. Mude o nome do pipeline de construção para algo mais descritivo, **selecione Save & fila** e, em seguida, selecione **Save**.

1. Sob o seu oleoduto de construção, selecione **Histórico**. Este painel mostra um rasto de auditoria das suas recentes mudanças de construção. O Azure DevOps monitoriza quaisquer alterações feitas ao pipeline de construção e permite comparar versões.

1. Selecione **Triggers**. Pode incluir ou excluir filiais do processo de IC.

1. Selecione **Retenção**. Pode especificar políticas para manter ou remover uma série de construções, dependendo do seu cenário.

## <a name="examine-the-cd-pipeline"></a>Examinar o pipeline de CD

O Centro de Implementação cria e configura automaticamente a relação entre a sua organização Azure DevOps e a sua subscrição Azure. As etapas envolvidas incluem a criação de uma ligação de serviço Azure para autenticar a sua subscrição Azure com a Azure DevOps. O processo automatizado também cria um gasoduto de libertação, que fornece entregas contínuas ao Azure.

1. Selecione **Pipelines** e, em seguida, **selecione Versões**.

1. Para editar o pipeline de lançamento, **selecione Editar**.

1. Selecione **Drop** da lista **de artefactos.** Nos passos anteriores, o gasoduto de construção que examinou produz a produção utilizada para o artefacto. 

1. Selecione o gatilho **de implementação contínua** à direita da opção **Drop.** Este oleoduto de desbloqueio tem um gatilho de CD ativado que executa uma implantação sempre que um novo artefacto de construção está disponível. Também pode desativar o gatilho para exigir uma execução manual para as suas implementações.

1. Para examinar todas as tarefas do seu pipeline, selecione **Tarefas**. O desbloqueio define o ambiente do leme, configura o `imagePullSecrets` parâmetro, instala as ferramentas Helm e implanta as tabelas Helm para o cluster Kubernetes.

1. Para visualizar o histórico de lançamentos, **selecione Ver ver lançamentos**.

1. Para ver o resumo, **selecione Ver**. Selecione qualquer uma das fases para explorar vários menus, como um resumo de lançamento, itens de trabalho associados e testes. 

1. Selecione **Consolidações**. Esta visão mostra que o código comete compromissos relacionados com esta implementação. Compare as versões para ver as diferenças de compromisso entre implementações.

1. Selecionar **Registos**. Os registos contêm informações úteis de implantação, que pode ver durante e após as implementações.

## <a name="clean-up-resources"></a>Limpar os recursos

Pode eliminar os recursos relacionados que criou quando já não precisa deles. Utilize a funcionalidade de exclusão no painel de instrumentos DevOps Projects.

## <a name="next-steps"></a>Passos seguintes

Pode modificar estes pipelines de compilação e de lançamento para satisfazer as necessidades da sua equipa. Ou, pode usar este modelo ci/CD como modelo para os seus outros oleodutos.
