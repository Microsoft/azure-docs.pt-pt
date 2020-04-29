---
title: Centro de Implantação de Azure Kubernetes
description: Centro de Implantação em Azure DevOps simplifica a criação de um robusto oleoduto Azure DevOps para a sua aplicação
ms.author: puagarw
ms.topic: tutorial
ms.date: 07/12/2019
author: pulkitaggarwl
ms.openlocfilehash: b0a9597e370648faab3787218c7d038798dbd455
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80048113"
---
# <a name="deployment-center-for-azure-kubernetes"></a>Centro de Implantação de Azure Kubernetes

O Centro de Implantação em Azure DevOps simplifica a criação de um robusto oleoduto Azure DevOps para a sua aplicação. Por padrão, o Centro de Implantação configura um pipeline Azure DevOps para implementar as atualizações da sua aplicação para o cluster Kubernetes. Pode estender o pipeline configurado por predefinição do Azure DevOps e também adicionar capacidades mais ricas: a capacidade de obter aprovação antes de implementar, fornecer recursos adicionais do Azure, executar scripts, atualizar a sua aplicação e até realizar mais testes de validação.

Neste tutorial, irá:

> [!div class="checklist"]
> * Configure um gasoduto Azure DevOps para implementar as atualizações da sua aplicação para o cluster Kubernetes.
> * Examinar o gasoduto de integração contínua (CI).
> * Examine o gasoduto de entrega contínua (CD).
> * Limpe os recursos.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Pode obter uma gratuita através do [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

* Um cluster azure Kubernetes Service (AKS).

## <a name="create-an-aks-cluster"></a>Criar um cluster do AKS (Create an AKS cluster)

1. Inscreva-se no seu [portal Azure.](https://portal.azure.com/)

1. Selecione a opção [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) no lado direito da barra de menus no portal Azure.

1. Para criar o cluster AKS, executar os seguintes comandos:

    ```azurecli
    # Create a resource group in the South India location:

    az group create --name azooaks --location southindia

    # Create a cluster named azookubectl with one node.

    az aks create --resource-group azooaks --name azookubectl --node-count 1 --enable-addons monitoring --generate-ssh-keys
    ```

## <a name="deploy-application-updates-to-a-kubernetes-cluster"></a>Implementar atualizações de aplicações para um cluster Kubernetes

1. Vá ao grupo de recursos que criou na secção anterior.

1. Selecione o cluster AKS e, em seguida, selecione **Centro de Implantação (pré-visualização)** na lâmina esquerda. Selecione **Começar**.

   ![settings](media/deployment-center-launcher/settings.png)

1. Escolha a localização do código e selecione **Seguinte**. Em seguida, selecione um dos repositórios atualmente suportados: **[Azure Repos](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)** ou **GitHub**.

    O Azure Repos é um conjunto de ferramentas de controlo de versão que o ajudam a gerir o seu código. Se o seu projeto de software é grande ou pequeno, usar o controlo da versão o mais cedo possível é uma boa ideia.

    - **Azure Repos**: Escolha um repositório do seu projeto e organização existentes.

        ![Repositórios do Azure](media/deployment-center-launcher/azure-repos.gif)

    - **GitHub**: Autorize e selecione o repositório para a sua conta GitHub.

        ![GitHub](media/deployment-center-launcher/github.gif)


1. O Centro de Implantação analisa o repositório e deteta o seu Dockerfile. Se quiser atualizar o Dockerfile, pode editar o número de porta identificado.

    ![Definições de Aplicação](media/deployment-center-launcher/application-settings.png)

    Se o repositório não contiver o Dockerfile, o sistema exibe uma mensagem para cometer uma.

    ![Dockerfile](media/deployment-center-launcher/dockerfile.png)

1. Selecione um registo de contentores existente ou crie um e, em seguida, selecione **Terminar**. O gasoduto é criado automaticamente e faz fila para uma construção em [Pipelines Azure.](https://docs.microsoft.com/azure/devops/pipelines/index?view=azure-devops)

    O Azure Pipelines é um serviço na nuvem que pode utilizar para construir e testar automaticamente o seu projeto de código e disponibilizá-lo a outros utilizadores. Os Oleodutos Azure combinam integração contínua e entrega contínua para testar e construir constantemente e consistentemente o seu código e enviar-o para qualquer alvo.

    ![Registo de Contentor](media/deployment-center-launcher/container-registry.png)

1. Selecione o link para ver o gasoduto em curso.

1. Verá os registos bem sucedidos após a implementação estar completa.

    ![Registos](media/deployment-center-launcher/logs.png)

## <a name="examine-the-ci-pipeline"></a>Examinar o pipeline de CI

O Centro de Implantação configura automaticamente o oleoduto CI/CD da organização Azure DevOps. O oleoduto pode ser explorado e personalizado.

1. Vá ao painel do Centro de Implantação.  

1. Selecione o número de construção da lista de registos bem sucedidos para visualizar o pipeline de construção para o seu projeto.

1. Selecione a elipse (...) no canto superior direito. Um menu mostra várias opções, como fazer fila de uma nova construção, manter uma construção e editar o pipeline de construção. Selecione **o pipeline Editar**. 

1. Você pode examinar as diferentes tarefas para o seu oleoduto de construção neste painel. A construção executa várias tarefas, tais como a recolha de fontes do repositório Git, a criação de uma imagem, o impulso de uma imagem para o registo de contentores e a publicação de saídas que são usadas para implantações.

1. Selecione o nome do gasoduto de construção na parte superior do gasoduto.

1. Mude o nome do seu pipeline de construção para algo mais descritivo, selecione **Guardar & fila**, e, em seguida, selecione **Guardar**.

1. Sob o seu oleoduto de construção, selecione **History**. Este painel mostra um rasto de auditoria das suas recentes mudanças de construção. O Azure DevOps monitoriza quaisquer alterações feitas no pipeline de construção e permite comparar versões.

1. Selecione **Triggers**. Pode incluir ou excluir balcões do processo de CI.

1. Selecione **Retenção**. Pode especificar políticas para manter ou remover uma série de construções, dependendo do seu cenário.

## <a name="examine-the-cd-pipeline"></a>Examinar o pipeline de CD

O Centro de Implantação cria e confunde automaticamente a relação entre a sua organização Azure DevOps e a sua subscrição Azure. Os passos envolvidos incluem a criação de uma ligação de serviço Azure para autenticar a sua subscrição Azure com a Azure DevOps. O processo automatizado também cria um pipeline de libertação, que fornece entrega contínua ao Azure.

1. Selecione **Pipelines**, e, em seguida, selecione **Lançamentos**.

1. Para editar o gasoduto de lançamento, selecione **Editar**.

1. Selecione **Drop** da lista **de Artefactos.** Nos passos anteriores, o gasoduto de construção que examinou produz a saída utilizada para o artefacto. 

1. Selecione o gatilho de **implantação Contínua** à direita da opção **Drop.** Este gasoduto de libertação tem um gatilho de CD ativado que executa uma implantação sempre que um novo artefacto de construção está disponível. Também pode desativar o gatilho para exigir a execução manual para as suas implementações.

1. Para examinar todas as tarefas do seu pipeline, selecione **Tasks**. O lançamento define o ambiente de `imagePullSecrets` leme, configura o parâmetro, instala ferramentas Helm e implanta as tabelas Helm para o cluster Kubernetes.

1. Para ver o histórico de lançamentos, selecione **ver lançamentos**.

1. Para ver o resumo, selecione **Libertar**. Selecione qualquer uma das etapas para explorar vários menus, tais como um resumo de lançamento, itens de trabalho associados e testes. 

1. Selecione **Consolidações**. Esta visão mostra que o código se compromete com esta implantação. Compare os lançamentos para ver as diferenças de compromisso entre implementações.

1. Selecionar **Registos**. Os registos contêm informações úteis de implementação, que pode visualizar durante e após as implementações.

## <a name="clean-up-resources"></a>Limpar recursos

Pode eliminar os recursos relacionados que criou quando já não precisa deles. Utilize a funcionalidade de eliminar no painel de instrumentos de Projetos DevOps.

## <a name="next-steps"></a>Passos seguintes

Pode modificar estes pipelines de compilação e de lançamento para satisfazer as necessidades da sua equipa. Ou, pode usar este modelo CI/CD como modelo para os outros oleodutos.
