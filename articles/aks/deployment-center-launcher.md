---
title: Utilize o Centro de implementação no serviço de aplicações do Azure
description: Central de implantação de DevOps do Azure simplifica a configuração de um pipeline de DevOps do Azure robusto para a sua aplicação
ms.author: puagarw
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/12/2019
author: pulkitaggarwl
monikerRange: vsts
ms.openlocfilehash: 8d1e467906b74c97c8b4f4e5c14af0814dd098f7
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/12/2019
ms.locfileid: "67854639"
---
# <a name="deployment-center-launcher"></a>Iniciador da central de implantação

Central de implantação de DevOps do Azure simplifica a configuração de cópia de segurança de um pipeline de DevOps robusto para a sua aplicação. Por predefinição, configura um pipeline de DevOps para implementar as atualizações de aplicação no cluster de kubernetes. Pode estender o padrão configurados DevOps de pipelines e adicione os recursos mais avançados do DevOps - aprovações antes de implementar, o aprovisionamento de recursos do Azure adicionais, executar scripts, atualizar a sua aplicação ou até mesmo a execução de testes de validação adicional.

Neste tutorial, irá:

> [!div class="checklist"]
> * Configurar um pipeline de DevOps para implementar as atualizações de aplicação no cluster de k8s
> * Examinar o pipeline de CI
> * Examinar o pipeline de CD
> * Limpar os recursos

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Pode obter uma gratuito [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/)

* Cluster do Azure Kubernetes Service (AKS)

## <a name="create-aks-cluster"></a>Criar um cluster do AKS

1. Inicie sessão no seu [portal do Azure](https://portal.azure.com/)

1. Selecione o [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) botão no menu no canto superior direito do portal do Azure.

1. Para criar o cluster do AKS, execute os seguintes comandos.

    ```cmd
    # The below command creates Resource Group in the south india location

    az group create --name azooaks --location southindia

    # The below command creates a cluster named azookubectl with one node. 

    az aks create --resource-group azooaks --name azookubectl --node-count 1 --enable-addons monitoring --generate-ssh-keys
    ```

## <a name="deploy-application-updates-to-k8s-cluster"></a>Implementar atualizações de aplicações em K8s Cluster

1. Navegue para o procedimento acima criado o grupo de recursos.

1. Selecione o cluster do AKS e clique em definições do painel esquerdo no **central de implantação (pré-visualização)** . Clique em **começar**.

   ![settings](media/deployment-center-launcher/settings.png)

1. Escolha a localização do código e clique em **seguinte**. Atualmente, os repositórios suportados são **[Azure repositórios](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)** e **GitHub**. Pode seguir os passos abaixo, com base na seleção de repositório.

    Repositórios do Azure é um conjunto de ferramentas de controle de versão que pode utilizar para gerir o seu código. Se seu projeto de software é grande ou pequena, usando o controle de versão logo que possível é uma boa idéia.

    - **Repositórios do Azure**: Escolha um repositório do seu projeto existente e a organização.

        ![Repositórios do Azure](media/deployment-center-launcher/azure-repos.gif)

    - **GitHub**: Autorizar e selecione o repositório para a sua conta do GitHub.

        ![GitHub](media/deployment-center-launcher/github.gif)


1. Vamos analisar o repositório e detetar o Dockerfile. Se quiser atualizá-lo, pode editar o número da porta identificados.

    ![Definições da aplicação](media/deployment-center-launcher/application-settings.png)

    Se o repositório não contém o Dockerfile, o sistema irá apresentar uma mensagem para consolidar uma. 

    ![Dockerfile](media/deployment-center-launcher/dockerfile.png)

1. Selecione ou crie um registo de contentor existente e clique em **concluir**. O pipeline, será criado automaticamente e uma fila de compilação na [Pipelines do Azure](https://docs.microsoft.com/azure/devops/pipelines/index?view=azure-devops).

    Pipelines do Azure é um serviço cloud que pode utilizar para criar automaticamente e testar o seu projeto de código e disponibilizá-la a outros utilizadores. Pipelines do Azure combina a integração contínua (CI) e a entrega contínua (CD) para constantemente e consistente de teste e compile seu código e enviá-lo para qualquer destino.

    ![Registo de Contentor](media/deployment-center-launcher/container-registry.png)

1. Clique na ligação para ver o pipeline em curso.

1. Irá ver os registos com êxito, conforme mostrado quando a implementação estiver concluída.

    ![Registos](media/deployment-center-launcher/logs.png)

## <a name="examine-the-ci-pipeline"></a>Examinar o pipeline de CI

Configura o Centro de implementação CI da sua organização do Azure DevOps / CD do pipeline automaticamente. O pipeline pode ser explorado e personalizado. 

1. Aceda ao dashboard do Centro de implementação.  

1. Clique no número de compilação na lista de registos com êxito para ver o pipeline de compilação para o seu projeto. 

1. Clique em ellipsis(...) do canto superior direito. Um menu mostra várias opções como enfileirar uma nova compilação, mantendo a compilação e o pipeline de compilação de edição. Escolha o **edição pipeline**. 

1. Pode examinar as diferentes tarefas para o seu pipeline de compilação neste painel. A compilação executa várias tarefas, tais como recolha de origens a partir do repositório de Git, criação de uma imagem, enviar uma imagem para o registo de contentor e saídas que são utilizadas para implementações de publicação.

1. Selecione o nome do pipeline de compilação na parte superior do pipeline de compilação.

1. Altere o seu nome de pipeline de compilação para algo mais descritivo, selecione **guardar e colocar em fila**e, em seguida, selecione **guardar**.

1. Selecione **histórico** em seu pipeline de compilação. Este painel mostra uma trilha de auditoria das suas alterações recentes de compilação. DevOps do Azure monitoriza as alterações feitas no pipeline de compilação e permite-lhe comparar versões.

1. Escolher **Acionadores**. Opcionalmente, ramificações podem ser incluídas ou excluídas do processo de CI.

1. Escolher **retenção**. Pode especificar políticas de manter ou remover um número de compilações, dependendo do seu cenário.

## <a name="examine-the-cd-pipeline"></a>Examinar o pipeline de CD

Central de implantação cria e configura automaticamente as etapas necessárias da sua organização de DevOps do Azure à sua subscrição do Azure. Estes passos incluem como configurar uma ligação de serviço do Azure para autenticar a sua subscrição do Azure com o Azure DevOps. A automação também cria um pipeline de lançamento, que fornece o CD para o Azure.

1. Escolher **Pipelines**e, em seguida, escolha **versões**.

1. Para editar o pipeline de lançamento, clique em **editar** .

1. Selecione **Drop** partir **artefactos**. Nos passos anteriores, o pipeline de construção que é examinado produz a saída utilizada para o artefacto. 

1. Selecione **implementação contínua** acionador à direita dos **Drop** ícone. Esta versão de pipeline tem um acionador de CD ativado, que executa uma implantação sempre que um novo artefacto de compilação está disponível. Opcionalmente, pode desativar o acionador para exigir execução manual para as suas implementações.

1. Para examinar todas as tarefas para o seu pipeline, clique em **tarefas**. A versão define o ambiente do tiller, configura o imagePullSecrets, instala as ferramentas do Helm e implementa os gráficos do Helm no cluster de K8s.

1. Para ver o histórico de versões, clique em **ver versões**. 

1. Para ver o resumo, clique nas **versão**. Clique em qualquer um ao palco para explorar vários menus, por exemplo, uma versão em Resumo, testes e itens de trabalho associados. 

1. Selecione **Consolidações**. Esta vista mostra as confirmações de código relacionada com esta implementação. Compare as versões para ver as diferenças de consolidação entre implementações.

1. Selecionar **Registos**. Os registos contêm informações úteis de implementação. Durante e depois das implementações, pode visualizá-los.

## <a name="clean-up-resources"></a>Limpar recursos

Pode eliminar os recursos relacionados que criou quando não precisar delas. Utilize a funcionalidade de exclusão no dashboard de projetos de DevOps.

## <a name="next-steps"></a>Passos Seguintes

Opcionalmente, pode modificar estes pipelines de compilação e versão para satisfazer as necessidades da sua equipa. Também pode utilizar este padrão de CI/CD como modelo para outros pipelines. Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Configurar um pipeline de DevOps para implementar as atualizações de aplicação no cluster de k8s
> * Examinar o pipeline de CI
> * Examinar o pipeline de CD
> * Limpar os recursos
