---
title: 'Tutorial: Implementar aplicações node. js com tecnologia do Azure Cosmos DB com projetos de DevOps do Azure'
description: Projetos de DevOps do Azure torna mais fácil começar a utilizar no Azure. Com os projetos de DevOps, pode implementar a sua aplicação de node. js com tecnologia do Azure Cosmos DB para a aplicação de Web do Windows em alguns passos rápidos.
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/11/2019
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 4310807423600b96078ee48a04a5ad6dab68cd7e
ms.sourcegitcommit: af31deded9b5836057e29b688b994b6c2890aa79
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67813067"
---
# <a name="deploy-nodejs-apps-powered-by-azure-cosmos-db-with-devops-projects"></a>Implementar aplicações node. js com tecnologia do Azure Cosmos DB com projetos de DevOps

Projetos de DevOps do Azure oferece uma experiência simplificada, onde pode colocar o seu código existente e o repositório de Git ou selecione um aplicativo de exemplo para criar um pipeline de entrega contínua (CD) para o Azure e a integração contínua (CI).

Projetos de DevOps também:

* Crie recursos do Azure automaticamente, como o Azure Cosmos DB, Application Insights, serviço de aplicações e serviço de aplicações.

* Cria e configura um pipeline de lançamento em DevOps do Azure para CI/CD

Neste tutorial, irá:

> [!div class="checklist"]
> * Utilizar projetos de DevOps para implementar uma aplicação node. js com tecnologia do Azure Cosmos DB
> * Configurar o Azure DevOps e uma subscrição do Azure
> * Examine o Azure Cosmos DB
> * Examinar o pipeline de CI
> * Examinar o pipeline de CD
> * Confirmar as alterações no Git e implementá-las automaticamente para o Azure
> * Limpar os recursos

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Pode obter uma gratuito [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/)

## <a name="use-devops-projects-to-deploy-nodejs-app"></a>Utilizar projetos de DevOps para implementar a aplicação node. js

Projetos de DevOps cria um pipeline CI/CD em Pipelines do Azure. Pode criar uma nova organização de DevOps do Azure ou utilizar uma organização existente. Projetos de DevOps também cria os recursos do Azure, como o Azure Cosmos DB, o Application Insights, o serviço de aplicações e o plano do serviço de aplicações, na subscrição do Azure à sua escolha.

1. Inicie sessão no [portal do Azure](https://portal.azure.com)

1. No painel esquerdo, selecione a secção **criar um recurso**.

1. Na caixa de pesquisa, escreva **projetos de DevOps**e, em seguida, clique em **Add**.

   ![DevOps Projects](_img/azure-devops-project-cosmos-db/devops-project.png)

1. Selecione **node. js** como o tempo de execução e, em seguida, selecione **próxima**. Sob **escolher uma estrutura de aplicações**, selecione **Express. js**.

1. Ativar a seção **adicionar uma base de dados** para **Cosmos DB** e clique em **seguinte**.

    ![Adicionar base de dados](_img/azure-devops-project-cosmos-db/add-database.png)

    O cosmos DB suporta várias estruturas de aplicativo como **Express. js**, **aplicação node. js de exemplo**, e **Sail.js**. Neste tutorial, permite que considere **Express. js**.

1. Selecione um serviço do Azure para implementar a aplicação. Tem diferentes serviços, como aplicação de Web do Windows, o serviço do Kubernetes e aplicação Web para contentores. Para este tutorial, utilizamos **aplicação de Web do Windows**. Clique em **seguinte**.

## <a name="configure-azure-devops-and-azure-subscription"></a>Configurar o Azure DevOps e subscrição do Azure

1. Introduza um nome para o seu projeto de DevOps do Azure.

1. Crie uma nova organização do Azure DevOps ou selecione uma organização existente.

1. Selecione a sua subscrição do Azure.

1. Para ver as definições de configuração do Azure adicionais e para identificar o escalão de preços e a localização, clique em definições adicionais. Este painel apresenta várias opções de configuração, o escalão de preços e a localização de serviços do Azure.

1. Sair da área de configuração do Azure e, em seguida, selecione **feito**.

1. Após alguns minutos, o processo é concluído. Uma aplicação de node. js de exemplo está configurada num repositório de Git na sua organização de DevOps do Azure, é criado um Azure Cosmos DB, de um serviço de aplicações, de um plano do serviço de aplicações e Application Insights, um pipeline CI/CD é executado e a aplicação é implementada para o Azure.

   Depois de tudo isso é concluído, é apresentado o dashboard do projeto de DevOps do Azure no portal do Azure. Também pode ir para o dashboard de projetos de DevOps diretamente a partir **todos os recursos** no portal do Azure.

   Este dashboard fornece visibilidade no seu repositório de código do Azure DevOps, seu pipeline de CI/CD e no Azure Cosmos DB. Pode configurar opções adicionais de CI/CD no seu pipeline de DevOps do Azure. No lado direito, selecione **do Azure Cosmos DB** para ver.

## <a name="examine-the-azure-cosmos-db"></a>Examine o Azure Cosmos DB

Projetos de DevOps configura automaticamente o Cosmos DB, que pode explorar e personalizar. Familiarize-se com o Cosmos DB, faça o seguinte:

1. Vá para o dashboard de projetos de DevOps.

    ![Dashboard de projetos de DevOps](_img/azure-devops-project-cosmos-db/devops-project-dashboard.png)

1. No lado direito, selecione o Cosmos DB. É aberto um painel para o Cosmos DB. A partir desta vista pode realizar várias ações como operações de monitorização e a pesquisa de registos.

    ![Aplicação de Funções](_img/azure-devops-project-cosmos-db/cosmos-db.png)

## <a name="examine-the-ci-pipeline"></a>Examinar o pipeline de CI

Projetos de DevOps configura automaticamente um pipeline CI/CD na sua organização de DevOps do Azure. Pode explorar e personalizar o pipeline. Para se familiarizar com ele, faça o seguinte:

1. Vá para o dashboard de projetos de DevOps.

1. Clicar no hiperlink sob **criar**. Um separador do browser apresenta o pipeline de compilação para o novo projeto.

    ![Compilação](_img/azure-devops-project-cosmos-db/build.png)

1. Selecione **Editar**. Neste painel, pode examinar as várias tarefas para o seu pipeline de compilação. A compilação executa várias tarefas, como ao obter código-fonte do repositório Git, compilando o aplicativo, executando testes de unidade e publicação saídas que são utilizadas para implementações.

1. Selecione **Acionadores**. Projetos de DevOps cria automaticamente um acionador de CI e cada consolidação para o repositório inicia uma nova compilação. Opcionalmente, pode optar por incluir ou excluir os ramos do processo de CI.

1. Selecione **Retenção**. Dependendo do seu cenário, pode especificar políticas de manter ou remover um determinado número de compilações.

1. Na parte superior do pipeline de compilação, selecione o nome do pipeline de compilação.

1. Alterar o nome do seu pipeline de compilação para algo mais descritivo e, em seguida, selecione **salvar** partir a **guardar e colocar em fila** lista pendente.

1. No nome do pipeline de compilação, selecione **Histórico**. Este painel apresenta uma trilha de auditoria das alterações recentes para a compilação. DevOps do Azure mantém um registro de todas as alterações feitas no pipeline de compilação e permite-lhe comparar versões.

## <a name="examine-the-cd-release-pipeline"></a>Examine o pipeline de lançamento do CD

Projetos de DevOps automaticamente cria e configura os passos necessários para implementar a partir de sua organização de DevOps do Azure à sua subscrição do Azure. Estes passos incluem a configuração de uma ligação de serviço do Azure para autenticar o DevOps do Azure à sua subscrição do Azure. A automação também cria um pipeline de lançamento, que fornece o CD para o Azure. Para saber mais sobre o pipeline de lançamento, faça o seguinte:

1. Navegue para o **Pipelines | Versões**.

1. Clique em **editar**.

1. Em **Artefactos**, selecione **Remover**. O pipeline de compilação examinado nos passos anteriores produz a saída que é utilizada para o artefacto.

1. No lado direito do **Drop** ícone, selecione **acionador de implementação contínua**. Este pipeline de lançamento ativou o acionador de CD, que executa uma implantação sempre que um novo artefacto de compilação está disponível. Opcionalmente, pode desativar o acionador para que as suas implementações exigem execução manual.

1. No lado direito, selecione a secção **ver versões** para apresentar um histórico de versões.

1. Clique na versão, que exibirá o pipeline. Clique em qualquer ambiente para verificar a versão **resumo, consolidações**, associado **itens de trabalho**.

1. Selecione **Consolidações**. Esta vista mostra as confirmações de código que estão associadas esta implementação. Compare as versões para ver as diferenças de consolidação entre implementações.

1. Selecione **ver registos**. Os registos contêm informações úteis sobre o processo de implementação. Pode visualizá-las durante e depois das implementações.

## <a name="commit-code-changes-and-execute-cicd"></a>Consolidar as alterações de código e executar o CI/CD

> [!NOTE]
> O procedimento seguinte testa o pipeline de CI/CD ao fazer uma alteração de texto simples.

Agora, está pronto para colaborar com uma equipa na sua aplicação ao utilizar um processo de CI/CD que implementa automaticamente o seu trabalho mais recente para o serviço de aplicações do Azure. Cada alteração para o repositório de Git inicia uma compilação em DevOps do Azure e um pipeline CD executa uma implementação para o Azure. Siga o procedimento nesta secção, ou utilize outra técnica para consolidar as alterações ao seu repositório. Por exemplo, pode clonar o repositório de Git na sua ferramenta favorita ou IDE, e, em seguida, enviar alterações para este repositório.

1. No menu de DevOps do Azure, selecione **repositórios | Ficheiros**e, em seguida, aceda ao seu repositório.

1. O repositório já contém o código com base na linguagem de aplicação que escolheu no processo de criação. Abra o **Application/views/index.pug** ficheiro.

1. Selecione **edite**e, em seguida, faça uma alteração ao **linha número 15** . Por exemplo, pode atualizá-la para **minha primeira implementação para o serviço de aplicações do Azure com tecnologia do Azure Cosmos DB**

1. Na parte superior direita, selecione **consolidar**e, em seguida, selecione **consolidar** novamente para enviar sua alteração.

     Após alguns instantes, uma compilação é iniciada no Azure DevOps e uma versão executa para implementar as alterações. Monitorize o estado de compilação no dashboard de projetos de DevOps ou no navegador com a sua organização de DevOps do Azure.

## <a name="clean-up-resources"></a>Limpar recursos

Pode eliminar os recursos relacionados que criou quando não precisar delas. Utilize o **eliminar** funcionalidade no dashboard de projetos de DevOps.

## <a name="next-steps"></a>Passos Seguintes

Opcionalmente, pode modificar estes pipelines de compilação e versão para satisfazer as necessidades da sua equipa. Também pode utilizar este padrão de CI/CD como modelo para outros pipelines. Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Utilizar projetos de DevOps para implementar uma aplicação node. js com tecnologia do Azure Cosmos DB
> * Configurar o Azure DevOps e uma subscrição do Azure 
> * Examine o Azure Cosmos DB
> * Examinar o pipeline de CI
> * Examinar o pipeline de CD
> * Consolide as alterações com o Git e implementá-las automaticamente para o Azure
> * Limpar recursos
