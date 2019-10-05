---
title: Centro de implantação para kubernetes do Azure
description: A central de implantação no Azure DevOps simplifica a configuração de um pipeline DevOps do Azure robusto para seu aplicativo
ms.author: puagarw
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/12/2019
author: pulkitaggarwl
monikerRange: vsts
ms.openlocfilehash: 5384180720d391c6b4ae830f9316a70e80003063
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2019
ms.locfileid: "71972982"
---
# <a name="deployment-center-for-azure-kubernetes"></a>Centro de implantação para kubernetes do Azure

A central de implantação no Azure DevOps simplifica a configuração de um pipeline DevOps do Azure robusto para seu aplicativo. Por padrão, a central de implantação configura um pipeline de DevOps do Azure para implantar as atualizações do aplicativo no cluster kubernetes. Você pode estender o pipeline de DevOps do Azure configurado padrão e também adicionar recursos mais avançados: a capacidade de obter aprovação antes da implantação, provisionar recursos adicionais do Azure, executar scripts, atualizar seu aplicativo e até mesmo executar mais testes de validação.

Neste tutorial, irá:

> [!div class="checklist"]
> * Configure um pipeline DevOps do Azure para implantar as atualizações do aplicativo no cluster kubernetes.
> * Examine o pipeline de CI (integração contínua).
> * Examine o pipeline de entrega contínua (CD).
> * Limpe os recursos.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Pode obter uma subscrição gratuita através do [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

* Um cluster do AKS (serviço de kubernetes do Azure).

## <a name="create-an-aks-cluster"></a>Criar um cluster do AKS (Create an AKS cluster)

1. Entre em seu [portal do Azure](https://portal.azure.com/).

1. Selecione a opção [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) no lado direito da barra de menus na portal do Azure.

1. Para criar o cluster AKS, execute os seguintes comandos:

    ```cmd
    # Create a resource group in the South India location:

    az group create --name azooaks --location southindia

    # Create a cluster named azookubectl with one node.

    az aks create --resource-group azooaks --name azookubectl --node-count 1 --enable-addons monitoring --generate-ssh-keys
    ```

## <a name="deploy-application-updates-to-a-kubernetes-cluster"></a>Implantar atualizações de aplicativo em um cluster kubernetes

1. Vá para o grupo de recursos que você criou na seção anterior.

1. Selecione o cluster AKS e, em seguida, selecione **central de implantação (versão prévia)** na folha à esquerda. Selecione **introdução**.

   ![definições](media/deployment-center-launcher/settings.png)

1. Escolha o local do código e selecione **Avançar**. Em seguida, selecione um dos repositórios com suporte no momento: **[Azure Repos](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)** ou **GitHub**.

    Azure Repos é um conjunto de ferramentas de controle de versão que ajudam a gerenciar seu código. Se o seu projeto de software é grande ou pequeno, usar o controle de versão o mais cedo possível é uma boa ideia.

    - **Azure Repos**: Escolha um repositório de seu projeto e organização existentes.

        ![Azure Repos](media/deployment-center-launcher/azure-repos.gif)

    - **GitHub**: Autorize e selecione o repositório para sua conta do GitHub.

        ![GitHub](media/deployment-center-launcher/github.gif)


1. A central de implantação analisa o repositório e detecta seu Dockerfile. Se você quiser atualizar o Dockerfile, poderá editar o número da porta identificada.

    ![Definições da Aplicação](media/deployment-center-launcher/application-settings.png)

    Se o repositório não contiver o Dockerfile, o sistema exibirá uma mensagem para confirmar uma.

    ![Dockerfile](media/deployment-center-launcher/dockerfile.png)

1. Selecione um registro de contêiner existente ou crie um e, em seguida, selecione **concluir**. O pipeline é criado automaticamente e enfileira uma compilação em [Azure pipelines](https://docs.microsoft.com/azure/devops/pipelines/index?view=azure-devops).

    Azure Pipelines é um serviço de nuvem que você pode usar para criar e testar automaticamente seu projeto de código e torná-lo disponível para outros usuários. O Azure Pipelines combina integração contínua e entrega contínua para testar e criar seu código de forma constante e consistente e enviá-lo para qualquer destino.

    ![Registo de Contentor](media/deployment-center-launcher/container-registry.png)

1. Selecione o link para ver o pipeline em andamento.

1. Você verá os logs bem-sucedidos após a conclusão da implantação.

    ![Registos](media/deployment-center-launcher/logs.png)

## <a name="examine-the-ci-pipeline"></a>Examinar o pipeline de CI

A central de implantação configura automaticamente o pipeline de CI/CD de sua organização do Azure DevOps. O pipeline pode ser explorado e personalizado.

1. Vá para o painel da central de implantação.  

1. Selecione o número de Build na lista de logs bem-sucedidos para exibir o pipeline de compilação para seu projeto.

1. Selecione as reticências (...) no canto superior direito. Um menu mostra várias opções, como enfileirar uma nova compilação, reter uma compilação e editar o pipeline de compilação. Selecione **Editar pipeline**. 

1. Você pode examinar as diferentes tarefas para o pipeline de compilação neste painel. A compilação executa várias tarefas, como coletar fontes do repositório git, criar uma imagem, enviar uma imagem por push para o registro de contêiner e publicar saídas que são usadas para implantações.

1. Selecione o nome do pipeline de compilação na parte superior do pipeline.

1. Altere o nome do pipeline de compilação para algo mais descritivo, selecione **salvar & fila**e, em seguida, selecione **salvar**.

1. Em seu pipeline de compilação, selecione **histórico**. Esse painel mostra uma trilha de auditoria de suas alterações de compilação recentes. O Azure DevOps monitora as alterações feitas no pipeline de compilação e permite que você compare as versões.

1. Selecione **Acionadores**. Você pode incluir ou excluir ramificações do processo de CI.

1. Selecione **Retenção**. Você pode especificar políticas para manter ou remover várias compilações, dependendo do seu cenário.

## <a name="examine-the-cd-pipeline"></a>Examinar o pipeline de CD

A central de implantação cria e configura automaticamente a relação entre sua organização do Azure DevOps e sua assinatura do Azure. As etapas envolvidas incluem a configuração de uma conexão de serviço do Azure para autenticar sua assinatura do Azure com o Azure DevOps. O processo automatizado também cria um pipeline de lançamento, que fornece entrega contínua ao Azure.

1. Selecione **pipelines**e, em seguida, selecione **versões**.

1. Para editar o pipeline de liberação, selecione **Editar**.

1. Selecione **descartar** na lista **artefatos** . Nas etapas anteriores, o pipeline de construção que você examinou produz a saída usada para o artefato. 

1. Selecione o gatilho de **implantação contínua** à direita da opção **drop** . Este pipeline de lançamento tem um gatilho de CD habilitado que executa uma implantação sempre que um novo artefato de compilação está disponível. Você também pode desabilitar o gatilho para exigir a execução manual de suas implantações.

1. Para examinar todas as tarefas de seu pipeline, selecione **tarefas**. A versão define o ambiente do gaveta, configura o parâmetro `imagePullSecrets`, instala as ferramentas do Helm e implanta os gráficos do Helm no cluster kubernetes.

1. Para exibir o histórico de versões, selecione **Exibir versões**.

1. Para ver o resumo, selecione **liberar**. Selecione qualquer um dos estágios para explorar vários menus, como um resumo de lançamento, itens de trabalho associados e testes. 

1. Selecione **Consolidações**. Esta exibição mostra as confirmações de código relacionadas a essa implantação. Compare as versões para ver as diferenças de confirmação entre implantações.

1. Selecionar **Registos**. Os logs contêm informações úteis de implantação, que você pode exibir durante e após as implantações.

## <a name="clean-up-resources"></a>Limpar recursos

Você pode excluir os recursos relacionados que criou quando não precisa mais deles. Use a funcionalidade excluir no painel DevOps Projects.

## <a name="next-steps"></a>Passos seguintes

Pode modificar estes pipelines de compilação e de lançamento para satisfazer as necessidades da sua equipa. Ou você pode usar esse modelo de CI/CD como um modelo para seus outros pipelines.
