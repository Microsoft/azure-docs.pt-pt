---
title: 'Tutorial: Implementar aplicações node.js alimentadas por Azure Cosmos DB com Starter Azure DevOps'
description: O Arranque azure DevOps facilita o arranque do Azure. Com o DevOps Starter, pode implementar a sua aplicação Node.js que é alimentada por Azure Cosmos DB para windows Web App em alguns passos rápidos.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 03/24/2020
author: mlearned
ms.openlocfilehash: 07579cf22738e195e3e4ae7a2aa18ffeb885bbe2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82233269"
---
# <a name="deploy-nodejs-apps-powered-by-azure-cosmos-db-with-devops-starter"></a>Implementar aplicativos Node.js alimentados por Azure Cosmos DB com DevOps Starter

O Azure DevOps Starter oferece uma experiência simplificada onde pode criar um oleoduto de integração contínua (CI) e de implantação contínua (CD) para o Azure. Fá-lo utilizando o código existente e o repositório Git (repo) ou selecionando uma aplicação de amostra.

DevOps Starter também:

* Cria automaticamente recursos Azure, tais como Azure Cosmos DB, Azure Application Insights, Azure App Service e app service

* Cria e configura um oleoduto de libertação CI/CD em Azure DevOps

Neste tutorial, irá:

> [!div class="checklist"]
> * Use DevOps Starter para implementar uma app Node.js alimentada por Azure Cosmos DB
> * Configure Azure DevOps e uma subscrição Azure
> * Examine Azure Cosmos DB
> * Examinar o pipeline de CI
> * Examinar o pipeline de CD
> * Comprometa as alterações a Git e as implemente automaticamente para o Azure
> * Limpar os recursos

## <a name="prerequisites"></a>Pré-requisitos

Você precisa de uma subscrição Azure, que você pode obter através [do Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) gratuitamente.

## <a name="use-devops-starter-to-deploy-nodejs-app"></a>Use devOps Starter para implementar app Node.js

DevOps Starter cria um oleoduto CI/CD em Pipelines Azure. Você pode criar uma nova organização Azure DevOps ou usar uma organização existente. A DevOps Starter também cria recursos Azure, tais como Azure Cosmos DB, Application Insights, App Service e App Service, na subscrição Azure à sua escolha.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Na caixa de pesquisa, digite **DevOps Starter**e, em seguida, selecione. Clique em **Adicionar** para criar um novo.

    ![O painel de arranque de DevOps](_img/azure-devops-starter-aks/search-devops-starter.png)

1. Selecione **Node.js** como o tempo de execução e, em seguida, selecione **Next**. Em **'Escolha uma estrutura de aplicação**', selecione **Express.js**.

1. Ative a secção **Adicionar uma base de dados** para **Cosmos DB,** e, em seguida, selecionar **Seguinte**.

    ![Adicione uma base de dados](_img/azure-devops-project-cosmos-db/add-database.png)

    O Azure DevOps Starter suporta vários quadros de aplicações, tais como **express.js,** **sample node.js app**, e **Sail.js**. Neste tutorial, usamos **Express.js.**

1. Selecione um serviço Azure para implementar a aplicação e, em seguida, selecione **Next**. As suas opções incluem Windows Web App, Serviço Azure Kubernetes e Web App Azure para contentores. Neste tutorial, utilizamos a **Aplicação Web do Windows**.

## <a name="configure-azure-devops-and-azure-subscription"></a>Configure Azure DevOps e subscrição Azure

1. Insira um nome para o seu projeto Azure DevOps.

1. Crie uma nova organização Azure DevOps, ou selecione uma organização existente.

1. Selecione a sua subscrição do Azure.

1. Para ver configurações adicionais de configuração do Azure ou identificar o nível de preços e a localização, selecione **Configurações adicionais**. Este painel mostra várias opções para configurar o nível de preços e a localização dos serviços Azure.

1. Saia da área de configuração Azure e, em seguida, selecione **Done**.

1. O processo termina passados alguns minutos. Uma aplicação node.js de amostra é configurada num repo Git na sua organização Azure DevOps. Em seguida, Azure Cosmos DB, App Service, App Service plan e Application Insights recursos são criados, bem como um pipeline CI/CD. A sua aplicação é então implantada para o Azure.

   Depois de todos estes processos terminarem, o painel de instrumentos de arranque Azure DevOps exibe no portal Azure. Também pode ir ao painel de arranque de DevOps diretamente de **todos os recursos** no portal Azure.

   Este dashboard proporciona visibilidade no seu repositório de código Azure DevOps, no seu pipeline CI/CD e na sua base de dados Azure Cosmos DB. Pode configurar opções adicionais de CI/CD no seu pipeline Azure DevOps. No lado direito do tablier, selecione **Azure Cosmos DB** para ver estas opções.

## <a name="examine-azure-cosmos-db"></a>Examine Azure Cosmos DB

DevOps Starter configura automaticamente Azure Cosmos DB, que você pode explorar e personalizar. Para se familiarizar com o Azure Cosmos DB, faça o seguinte:

1. Vá ao painel de arranque de DevOps.

    ![Painel de projetos de DevOps](_img/azure-devops-project-cosmos-db/devops-starter-dashboard.png)

1. À direita, selecione Azure Cosmos DB. Abre-se um painel para o Azure Cosmos DB. Nesta perspetiva, pode realizar várias ações, tais como operações de monitorização e registos de pesquisa.

    ![Painel DE DB Azure Cosmos](_img/azure-devops-project-cosmos-db/cosmos-db.png)

## <a name="examine-the-ci-pipeline"></a>Examinar o pipeline de CI

DevOps Starter configura automaticamente um pipeline CI/CD na sua organização Azure DevOps. Pode explorar e personalizar o pipeline. Para se familiarizar com ele, faça o seguinte:

1. Vá ao painel de arranque de DevOps.

1. Selecione a hiperligação sob **a Build**. Um separador de navegador exibe o pipeline de construção para o seu novo projeto.

    ![Construir painel](_img/azure-devops-project-cosmos-db/build.png)

1. Selecione **Editar**. Neste painel, pode examinar as várias tarefas para o seu pipeline de construção. A construção executa várias tarefas, tais como a obtenção do código fonte do repo Git, a construção da aplicação, a execução de testes de unidades e a publicação de saídas que são usadas para implantações.

1. Selecione **Triggers**. DevOps Starter cria automaticamente um gatilho CI, e cada compromisso com o repo inicia uma nova construção. Pode optar por incluir ou excluir balcões do processo de CI.

1. Selecione **Retenção**. Dependendo do seu cenário, pode especificar políticas para manter ou remover um determinado número de construções.

1. Na parte superior do pipeline de compilação, selecione o nome do pipeline de compilação.

1. Mude o nome do seu oleoduto de construção para algo mais descritivo e, em seguida, selecione **Save** from the **Save & dropdown** de fila.

1. No nome do pipeline de compilação, selecione **Histórico**. Este painel apresenta um rasto de auditoria das suas recentes alterações para a construção. A Azure DevOps acompanha quaisquer alterações feitas ao pipeline de construção, e permite-lhe comparar versões.

## <a name="examine-the-cd-release-pipeline"></a>Examine o gasoduto de libertação do CD

DevOps Starter cria e configura automaticamente os passos necessários para implementar da sua organização Azure DevOps para a sua subscrição Azure. Estes passos incluem configurar uma ligação de serviço Azure para autenticar O Azure DevOps à sua subscrição Azure. A automatização também cria um oleoduto de libertação, que fornece o CD ao Azure. Para saber mais sobre o gasoduto de libertação, faça o seguinte:

1. Vá a **Pipelines** e selecione **Lançamentos**.

1. Selecione **Editar**.

1. Em **Artefactos**, selecione **Remover**. O oleoduto de construção que examinou nos passos anteriores produz a saída que é usada para o artefacto.

1. À direita do ícone **Drop,** selecione **O gatilho de implantação contínua**. Este gasoduto de libertação permitiu um gatilho de implantação contínua, que executa uma implantação sempre que um novo artefacto de construção está disponível. Pode desativar o gatilho de modo a que as suas implementações executem manualmente.

1. À direita, selecione a secção **Ver lançamentos** para apresentar um histórico de lançamentos.

1. Selecione a libertação, que irá exibir o gasoduto. Selecione qualquer ambiente para verificar o resumo de lançamento, compromete-se ou artigos de trabalho associados.

1. Selecione **Consolidações**. Esta visão mostra código compromete-se que estão associados a esta implantação. Compare as versões para ver as diferenças de consolidação entre implementações.

1. Selecione **Ver Registos**. Os registos contêm informações úteis sobre o processo de implementação. Pode vê-los durante e após as implantações.

## <a name="commit-code-changes-and-execute-the-cicd-pipeline"></a>Comprometa alterações de código e execute o gasoduto CI/CD

> [!NOTE]
> O procedimento seguinte testa o gasoduto CI/CD fazendo uma simples alteração de texto.

Está agora pronto para colaborar com uma equipa na sua aplicação utilizando um processo CI/CD que implementa o seu mais recente trabalho no seu Serviço de Aplicações. Cada alteração para o repo Git inicia uma construção em Azure DevOps, e um pipeline de CD executa uma implantação para Azure. Siga o procedimento nesta secção ou utilize outra técnica para efetuar alterações no seu repo. Por exemplo, pode clonar o repo Git na sua ferramenta favorita ou IDE, e, em seguida, empurrar alterações para este repo.

1. No menu Azure DevOps, selecione **Repos** e, em seguida, **Ficheiros**. Então vá ao seu repo.

1. O repo já contém código baseado na linguagem de aplicação que escolheu no processo de criação. Abra o ficheiro **Application/views/index.pug.**

1. **Selecione Editar**e, em seguida, faça uma alteração para a **linha número 15**. Por exemplo, pode mudá-lo para "My First deployment to Azure App Service powered by Azure Cosmos DB".

1. No canto superior direito, selecione **Commit**, e, em seguida, selecione **Comprometer** novamente para empurrar a sua mudança.

     Após alguns segundos, uma construção começa em Azure DevOps e uma libertação executa para implementar as alterações. Monitorize o estado de construção no painel de arranque de DevOps ou no navegador com a sua organização Azure DevOps.

## <a name="clean-up-resources"></a>Limpar recursos

Apague os recursos relacionados que criou quando já não precisa deles. Utilize a funcionalidade **Eliminar** no painel de arranque de DevOps.

## <a name="next-steps"></a>Passos seguintes

Pode modificar estes pipelines de compilação e de lançamento para satisfazer as necessidades da sua equipa. Também pode utilizar este padrão de CI/CD como modelo para outros pipelines. Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Use DevOps Starter para implementar uma app Node.js alimentada por Azure Cosmos DB
> * Configure Azure DevOps e uma subscrição Azure 
> * Examine Azure Cosmos DB
> * Examinar o pipeline de CI
> * Examinar o pipeline de CD
> * Comprometa alterações a Git e as implemente automaticamente para o Azure
> * Limpar recursos

Consulte [Definir o seu oleoduto de implantação contínua em várias fases (CD)](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=azure-devops&viewFallbackFrom=vsts) para obter mais informações e próximos passos.


