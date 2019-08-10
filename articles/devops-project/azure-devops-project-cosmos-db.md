---
title: 'Tutorial: Implantar aplicativos node. js fornecidos por Azure Cosmos DB com Azure DevOps Projects'
description: Projetos de DevOps do Azure torna mais fácil começar a utilizar no Azure. Com DevOps Projects, você pode implantar seu aplicativo node. js que é alimentado por Azure Cosmos DB ao aplicativo Web do Windows em algumas etapas rápidas.
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/11/2019
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 38fc4aa04269924ad0acd529e961dd3228ec236e
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68884408"
---
# <a name="deploy-nodejs-apps-powered-by-azure-cosmos-db-with-devops-projects"></a>Implantar aplicativos node. js fornecidos por Azure Cosmos DB com DevOps Projects

O Azure DevOps Projects oferece uma experiência simplificada, na qual você pode criar um pipeline de CI (integração contínua) e um CD (implantação contínua) no Azure. Você faz isso usando o seu código existente e o repositório git, ou selecionando um aplicativo de exemplo.

DevOps Projects também:

* Cria automaticamente recursos do Azure, como Azure Cosmos DB, informações de Aplicativo Azure, serviços de Azure App e planos do serviço de aplicativo

* Cria e configura um pipeline de liberação de CI/CD no Azure DevOps

Neste tutorial, irá:

> [!div class="checklist"]
> * Use DevOps Projects para implantar um aplicativo node. js fornecido pela Azure Cosmos DB
> * Configurar o Azure DevOps e uma subscrição do Azure
> * Examinar Azure Cosmos DB
> * Examinar o pipeline de CI
> * Examinar o pipeline de CD
> * Confirmar as alterações no git e implantá-las automaticamente no Azure
> * Limpar os recursos

## <a name="prerequisites"></a>Pré-requisitos

Você precisa de uma assinatura do Azure, que pode ser obtida por meio de [Visual Studio dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) gratuitamente.

## <a name="use-devops-projects-to-deploy-nodejs-app"></a>Usar DevOps Projects para implantar o aplicativo node. js

DevOps Projects cria um pipeline de CI/CD no Azure Pipelines. Você pode criar uma nova organização de DevOps do Azure ou usar uma organização existente. DevOps Projects também cria recursos do Azure, como Azure Cosmos DB, Application Insights, serviço de aplicativo e planos de serviço de aplicativo, na assinatura do Azure de sua escolha.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. No painel esquerdo, selecione **criar um recurso**.

1. Na caixa de pesquisa, digite **DevOps Projects**e, em seguida, selecione **Adicionar**.

   ![Painel de DevOps Projects](_img/azure-devops-project-cosmos-db/devops-project.png)

1. Selecione **node. js** como o tempo de execução e, em seguida, selecione **Avançar**. Em **escolher uma estrutura de aplicativo**, selecione **Express. js**.

1. Habilite a seção **Adicionar um banco de dados** para **Cosmos DB**e, em seguida, selecione **Avançar**.

    ![Adicionar um banco de dados](_img/azure-devops-project-cosmos-db/add-database.png)

    O Azure DevOps Projects dá suporte a várias estruturas de aplicativo, como **Express. js**, **exemplo de aplicativo node. js**e **tomar. js**. Neste tutorial, usamos o **Express. js**.

1. Selecione um serviço do Azure para implantar o aplicativo e, em seguida, selecione **Avançar**. Suas opções incluem o aplicativo Web do Windows, o serviço kubernetes do Azure e o Azure Aplicativo Web para Contêineres. Neste tutorial, usamos o **aplicativo Web do Windows**.

## <a name="configure-azure-devops-and-azure-subscription"></a>Configurar o Azure DevOps e a assinatura do Azure

1. Insira um nome para seu projeto DevOps do Azure.

1. Crie uma nova organização de DevOps do Azure ou selecione uma organização existente.

1. Selecione a sua subscrição do Azure.

1. Para exibir definições de configuração adicionais do Azure ou identificar o tipo de preço e o local, selecione **configurações adicionais**. Esse painel mostra várias opções para configurar o tipo de preço e o local dos serviços do Azure.

1. Saia da área de configuração do Azure e, em seguida, selecione **concluído**.

1. O processo é concluído após alguns minutos. Um aplicativo node. js de exemplo é configurado em um repositório git em sua organização DevOps do Azure. Em seguida, Azure Cosmos DB, serviço de aplicativo, plano do serviço de aplicativo e Application Insights recursos são criados, bem como um pipeline de CI/CD. Seu aplicativo é então implantado no Azure.

   Após a conclusão de todos esses processos, o painel do projeto DevOps do Azure é exibido na portal do Azure. Você também pode acessar o painel de DevOps Projects diretamente de **todos os recursos** na portal do Azure.

   Esse painel fornece visibilidade do seu repositório de código DevOps do Azure, seu pipeline de CI/CD e seu banco de dados Azure Cosmos DB. Você pode configurar opções adicionais de CI/CD no pipeline DevOps do Azure. No lado direito do painel, selecione **Azure Cosmos DB** para exibir essas opções.

## <a name="examine-azure-cosmos-db"></a>Examinar Azure Cosmos DB

DevOps Projects configura automaticamente Azure Cosmos DB, que você pode explorar e personalizar. Para se familiarizar com Azure Cosmos DB, faça o seguinte:

1. Vá para o painel de DevOps Projects.

    ![Painel do DevOps Projects](_img/azure-devops-project-cosmos-db/devops-project-dashboard.png)

1. À direita, selecione Azure Cosmos DB. Um painel é aberto para Azure Cosmos DB. Nessa exibição, você pode executar várias ações, como operações de monitoramento e logs de pesquisa.

    ![Painel de Azure Cosmos DB](_img/azure-devops-project-cosmos-db/cosmos-db.png)

## <a name="examine-the-ci-pipeline"></a>Examinar o pipeline de CI

DevOps Projects configura automaticamente um pipeline de CI/CD em sua organização DevOps do Azure. Pode explorar e personalizar o pipeline. Para se familiarizar com ele, faça o seguinte:

1. Vá para o painel de DevOps Projects.

1. Selecione o hiperlink em **Compilar**. Uma guia do navegador exibe o pipeline de Build para seu novo projeto.

    ![Painel de compilação](_img/azure-devops-project-cosmos-db/build.png)

1. Selecione **Editar**. Neste painel, pode examinar as várias tarefas para o seu pipeline de compilação. A compilação executa várias tarefas, como a busca de código-fonte do repositório git, a criação do aplicativo, a execução de testes de unidade e a publicação de saídas que são usadas para implantações.

1. Selecione **Acionadores**. DevOps Projects cria automaticamente um gatilho de CI e todas as confirmações para o repositório iniciam uma nova compilação. Você pode optar por incluir ou excluir ramificações do processo de CI.

1. Selecione **Retenção**. Dependendo do seu cenário, pode especificar políticas de manter ou remover um determinado número de compilações.

1. Na parte superior do pipeline de compilação, selecione o nome do pipeline de compilação.

1. Altere o nome do pipeline de compilação para algo mais descritivo e, em seguida, selecione **salvar** na lista suspensa **Salvar & fila** .

1. No nome do pipeline de compilação, selecione **Histórico**. Esse painel exibe uma trilha de auditoria de suas alterações recentes para a compilação. O DevOps do Azure controla as alterações feitas no pipeline de compilação e permite que você compare as versões.

## <a name="examine-the-cd-release-pipeline"></a>Examinar o pipeline de liberação de CD

DevOps Projects cria e configura automaticamente as etapas necessárias para implantar de sua organização do Azure DevOps em sua assinatura do Azure. Essas etapas incluem a configuração de uma conexão de serviço do Azure para autenticar o Azure DevOps em sua assinatura do Azure. A automação também cria um pipeline de lançamento, que fornece o CD para o Azure. Para saber mais sobre o pipeline de lançamento, faça o seguinte:

1. Vá para **pipelines** e selecione **versões**.

1. Selecione **Editar**.

1. Em **Artefactos**, selecione **Remover**. O pipeline de Build que você examinou nas etapas anteriores produz a saída usada para o artefato.

1. À direita do ícone de **soltar** , selecione **gatilho de implantação contínua**. Este pipeline de lançamento habilitou o gatilho de implantação contínua, que executa uma implantação sempre que um novo artefato de compilação está disponível. Você pode desabilitar o gatilho para que suas implantações sejam executadas manualmente.

1. À direita, selecione a seção **Exibir versões** para exibir um histórico de versões.

1. Selecione a versão, que exibirá o pipeline. Selecione qualquer ambiente para verificar o resumo da versão, as confirmações ou os itens de trabalho associados.

1. Selecione **Consolidações**. Esta exibição mostra as confirmações de código que estão associadas a essa implantação. Compare as versões para ver as diferenças de consolidação entre implementações.

1. Selecione **Exibir logs**. Os registos contêm informações úteis sobre o processo de implementação. Você pode exibi-los durante e após as implantações.

## <a name="commit-code-changes-and-execute-the-cicd-pipeline"></a>Confirmar alterações de código e executar o pipeline de CI/CD

> [!NOTE]
> O procedimento a seguir testa o pipeline de CI/CD fazendo uma alteração de texto simples.

Agora você está pronto para colaborar com uma equipe em seu aplicativo usando um processo de CI/CD que implanta seu trabalho mais recente em seu serviço de aplicativo. Cada alteração no repositório git inicia uma compilação no Azure DevOps e um pipeline de CD executa uma implantação no Azure. Siga o procedimento nesta seção ou use outra técnica para confirmar as alterações no seu repositório. Por exemplo, você pode clonar o repositório git em sua ferramenta favorita ou IDE e, em seguida, enviar por push as alterações para esse repositório.

1. No menu DevOps do Azure, selecione **repositórios** e, em seguida, **arquivos**. Em seguida, acesse seu repositório.

1. O repositório já contém código com base no idioma do aplicativo que você escolheu no processo de criação. Abra o arquivo **Application/views/index. Pug** .

1. Selecione **Editar**e, em seguida, faça uma alteração na **linha número 15**. Por exemplo, você pode alterá-lo para "minha primeira implantação para Azure App serviço da plataforma Azure Cosmos DB".

1. No canto superior direito, selecione **confirmar**e, em seguida, selecione **confirmar** novamente para enviar por push sua alteração.

     Depois de alguns segundos, uma compilação é iniciada no Azure DevOps e uma versão é executada para implantar as alterações. Monitore o status da compilação no painel DevOps Projects ou no navegador com sua organização DevOps do Azure.

## <a name="clean-up-resources"></a>Limpar recursos

Exclua os recursos relacionados que você criou quando não precisar mais deles. Utilize o **eliminar** funcionalidade no dashboard de projetos de DevOps.

## <a name="next-steps"></a>Passos seguintes

Pode modificar estes pipelines de compilação e de lançamento para satisfazer as necessidades da sua equipa. Também pode utilizar este padrão de CI/CD como modelo para outros pipelines. Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Use DevOps Projects para implantar um aplicativo node. js fornecido pela Azure Cosmos DB
> * Configurar o Azure DevOps e uma subscrição do Azure 
> * Examinar Azure Cosmos DB
> * Examinar o pipeline de CI
> * Examinar o pipeline de CD
> * Confirmar alterações no git e implantá-las automaticamente no Azure
> * Limpar recursos

Consulte [definir seu pipeline de implantação contínua de vários estágios (CD)](https://docs.microsoft.com/en-us/azure/devops/pipelines/release/define-multistage-release-process?view=azure-devops&viewFallbackFrom=vsts) para obter mais informações e próximas etapas.


