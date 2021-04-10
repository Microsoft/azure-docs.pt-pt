---
title: 'Tutorial: Implementar Node.js aplicações alimentadas pela Azure Cosmos DB com Azure DevOps Starter'
description: O Azure DevOps Starter facilita o arranque do Azure. Com o DevOps Starter, pode implementar a sua Node.js aplicação que é alimentada pela Azure Cosmos DB para a Windows Web App em alguns passos rápidos.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 03/24/2020
author: mlearned
ms.custom: devx-track-js
ms.openlocfilehash: d1bdae976d5e5b7d98311d33bb38b5035f2557ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102548704"
---
# <a name="deploy-nodejs-apps-powered-by-azure-cosmos-db-with-devops-starter"></a>Implementar Node.js aplicações alimentadas pela Azure Cosmos DB com DevOps Starter

O Azure DevOps Starter oferece uma experiência simplificada onde pode criar um pipeline de integração contínua (CI) e implementação contínua (CD) para a Azure. Fá-lo utilizando o seu código existente e o repositório de Git (repo) ou selecionando uma aplicação de amostra.

DevOps Starter também:

* Cria automaticamente recursos Azure, tais como Azure Cosmos DB, Azure Application Insights, Azure App Service e planos de Serviço de Aplicações

* Cria e configura um oleoduto de libertação de CI/CD em Azure DevOps

Neste tutorial, vai:

> [!div class="checklist"]
> * Utilize o DevOps Starter para implementar uma aplicação de Node.js alimentada pela Azure Cosmos DB
> * Configurar Azure DevOps e uma subscrição da Azure
> * Examine Azure Cosmos DB
> * Examinar o pipeline de CI
> * Examinar o pipeline de CD
> * Comprometa as alterações ao Git e desloque-as automaticamente para a Azure
> * Limpar os recursos

## <a name="prerequisites"></a>Pré-requisitos

Você precisa de uma subscrição Azure, que você pode obter através [do Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) gratuitamente.

## <a name="use-devops-starter-to-deploy-nodejs-app"></a>Use o DevOps Starter para implementar Node.js app

DevOps Starter cria um gasoduto CI/CD em Pipelines Azure. Pode criar uma nova organização Azure DevOps ou utilizar uma organização existente. O DevOps Starter também cria recursos Azure, tais como Azure Cosmos DB, Application Insights, App Service e App Service, na subscrição Azure da sua escolha.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Na caixa de pesquisa, escreva **DevOps Starter** e, em seguida, selecione. Clique em **Adicionar** para criar um novo.

    ![O painel de arranque de DevOps](_img/azure-devops-starter-aks/search-devops-starter.png)

1. Selecione **Node.js** como tempo de execução e, em seguida, selecione **Seguinte**. No **Âmbito de Escolha um quadro de aplicação**, selecione **Express.js**.

1. Ativar a secção **Adicione uma base de dados** para **Cosmos DB** e, em seguida, selecione **Seguinte**.

    ![Adicionar uma base de dados](_img/azure-devops-project-cosmos-db/add-database.png)

    O Azure DevOps Starter suporta vários quadros de aplicações, tais como **Express.js**, **app sample Node.js**, e **Sail.js**. Neste tutorial, **usamosExpress.js.**

1. Selecione um serviço Azure para implementar a aplicação e, em seguida, selecione **Seguinte**. As suas opções incluem a Windows Web App, o Azure Kubernetes Service e a Azure Web App para contentores. Neste tutorial, utilizamos a **Aplicação Web do Windows.**

## <a name="configure-azure-devops-and-azure-subscription"></a>Configurar a subscrição Azure DevOps e Azure

1. Insira um nome para o seu projeto Azure DevOps.

1. Crie uma nova organização Azure DevOps ou selecione uma organização existente.

1. Selecione a sua subscrição do Azure.

1. Para visualizar definições adicionais de configuração do Azure ou identificar o nível de preços e a localização, selecione **Definições adicionais**. Este painel mostra várias opções para configurar o nível de preços e localização dos serviços Azure.

1. Saia da área de configuração Azure e, em seguida, selecione **Fazer**.

1. O processo termina após alguns minutos. Uma amostra Node.js app é criada num git repo na sua organização Azure DevOps. Em seguida, a Azure Cosmos DB, App Service, App Service plan e Application Insights são criados, bem como um pipeline CI/CD. A sua aplicação é então implantada para Azure.

   Depois de todos estes processos terminarem, o painel de arranque Azure DevOps aparece no portal Azure. Também pode ir ao painel de arranque de DevOps diretamente de **todos os recursos** do portal Azure.

   Este dashboard proporciona visibilidade ao repositório de códigoS Azure DevOps, ao seu pipeline CI/CD e à sua base de dados DB Azure Cosmos. Pode configurar opções adicionais de CI/CD no seu pipeline Azure DevOps. No lado direito do painel, selecione **Azure Cosmos DB** para ver estas opções.

## <a name="examine-azure-cosmos-db"></a>Examine Azure Cosmos DB

DevOps Starter configura automaticamente Azure Cosmos DB, que você pode explorar e personalizar. Para se familiarizar com a Azure Cosmos DB, faça o seguinte:

1. Vá ao painel de arranque do DevOps.

    ![Painel de projetos de devops](_img/azure-devops-project-cosmos-db/devops-starter-dashboard.png)

1. À direita, selecione Azure Cosmos DB. Abre-se um painel para a Azure Cosmos DB. A partir desta vista, pode realizar várias ações, como operações de monitorização e pesquisa de registos.

    ![Painel DB de Azure Cosmos](_img/azure-devops-project-cosmos-db/cosmos-db.png)

## <a name="examine-the-ci-pipeline"></a>Examinar o pipeline de CI

O DevOps Starter configura automaticamente um pipeline CI/CD na sua organização Azure DevOps. Pode explorar e personalizar o pipeline. Para se familiarizar com ele, faça o seguinte:

1. Vá ao painel de arranque do DevOps.

1. Selecione a hiperligação em **Build**. Um separador de navegador exibe o pipeline de construção para o seu novo projeto.

    ![Construir painel](_img/azure-devops-project-cosmos-db/build.png)

1. Selecione **Editar**. Neste painel, pode examinar as várias tarefas para o seu oleoduto de construção. A construção executa várias tarefas, tais como o código fonte de busca do repo Git, a construção da aplicação, a execução de testes unitários e a publicação de saídas que são usadas para implementações.

1. Selecione **Triggers**. O DevOps Starter cria automaticamente um gatilho de CI, e cada compromisso com o repo inicia uma nova construção. Pode optar por incluir ou excluir os ramos do processo de IC.

1. Selecione **Retenção**. Dependendo do seu cenário, pode especificar políticas para manter ou remover um determinado número de construções.

1. Na parte superior do pipeline de compilação, selecione o nome do pipeline de compilação.

1. Mude o nome do seu pipeline de construção para algo mais descritivo e, em seguida, **selecione Save** from the Save & de retirada da **fila.**

1. No nome do pipeline de compilação, selecione **Histórico**. Este painel apresenta um rasto de auditoria das suas recentes alterações para a construção. O Azure DevOps regista quaisquer alterações feitas ao pipeline de construção, e permite comparar versões.

## <a name="examine-the-cd-release-pipeline"></a>Examine o oleoduto de libertação de CD

O DevOps Starter cria e configura automaticamente os passos necessários para implementar da sua organização Azure DevOps para a sua subscrição Azure. Estes passos incluem configurar uma ligação de serviço Azure para autenticar Azure DevOps para a sua subscrição Azure. A automatização também cria um gasoduto de libertação, que fornece o CD ao Azure. Para saber mais sobre o oleoduto de lançamento, faça o seguinte:

1. Vá a **Pipelines** e selecione **Lançamentos**.

1. Selecione **Editar**.

1. Em **Artefactos**, selecione **Remover**. O gasoduto de construção que examinou nos passos anteriores produz a saída que é usada para o artefacto.

1. À direita do ícone **Drop,** selecione **o gatilho de implementação contínua**. Este gasoduto de libertação permitiu o disparo contínuo de implantação, que executa uma implantação sempre que um novo artefacto de construção está disponível. Pode desativar o gatilho para que as suas implementações executem manualmente.

1. À direita, selecione a secção **Ver ver para** visualizar um histórico de lançamentos.

1. Selecione o desbloqueio, que irá exibir o pipeline. Selecione qualquer ambiente para verificar o resumo do lançamento, compromete-se ou itens de trabalho associados.

1. Selecione **Consolidações**. Esta visão mostra compromissos de código que estão associados a esta implementação. Compare as versões para ver as diferenças de consolidação entre implementações.

1. Selecione **Ver Registos**. Os registos contêm informações úteis sobre o processo de implementação. Pode vê-los durante e após as missões.

## <a name="commit-code-changes-and-execute-the-cicd-pipeline"></a>Cometer alterações de código e executar o gasoduto CI/CD

> [!NOTE]
> O procedimento a seguir testa o gasoduto CI/CD através de uma simples alteração de texto.

Está agora pronto para colaborar com uma equipa na sua aplicação, utilizando um processo ci/CD que implementa o seu mais recente trabalho no seu Serviço de Aplicações. Cada mudança para o git repo inicia uma construção em Azure DevOps, e um oleoduto de CD executa uma implantação para Azure. Siga o procedimento nesta secção ou use outra técnica para comprometer alterações no seu repo. Por exemplo, pode clonar o repo Git na sua ferramenta preferida ou IDE e, em seguida, empurrar alterações para este repo.

1. No menu Azure DevOps, selecione **Repos** e, em seguida, **Ficheiros**. Então vai para o teu repo.

1. O repo já contém código baseado na linguagem de aplicação que escolheu no processo de criação. Abra o ficheiro **Aplicação/vistas/index.pug.**

1. **Selecione Editar** e, em seguida, faça uma alteração para a **linha número 15**. Por exemplo, pode alterá-lo para "A minha primeira implementação para o Azure App Service powered by Azure Cosmos DB."

1. No canto superior direito, **selecione Commit** e, em seguida, selecione **Comprometa-se** novamente para empurrar a sua mudança.

     Após alguns segundos, uma construção começa em Azure DevOps e um lançamento executa para implementar as alterações. Monitorize o estado de construção no painel de partidas de DevOps ou no navegador com a sua organização Azure DevOps.

## <a name="clean-up-resources"></a>Limpar os recursos

Elimine os recursos relacionados que criou quando já não precisa deles. Utilize a funcionalidade **Eliminar** no painel de arranque de devOps.

## <a name="next-steps"></a>Passos seguintes

Pode modificar estes pipelines de compilação e de lançamento para satisfazer as necessidades da sua equipa. Também pode utilizar este padrão de CI/CD como modelo para outros pipelines. Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Utilize o DevOps Starter para implementar uma aplicação de Node.js alimentada pela Azure Cosmos DB
> * Configurar Azure DevOps e uma subscrição da Azure 
> * Examine Azure Cosmos DB
> * Examinar o pipeline de CI
> * Examinar o pipeline de CD
> * Cometa alterações no Git e desloque-as automaticamente para a Azure
> * Limpar os recursos

Consulte [o seu pipeline de implantação contínua em várias fases (CD)](/azure/devops/pipelines/release/define-multistage-release-process) para obter mais informações e próximos passos.