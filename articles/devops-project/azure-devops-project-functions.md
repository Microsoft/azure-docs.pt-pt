---
title: 'Tutorial: Implementar aplicações ASP.NET para as funções do Azure com projetos de Azure DevOps'
description: Projetos de DevOps do Azure torna mais fácil começar a utilizar no Azure. Com os projetos de DevOps, pode implementar a aplicação ASP.NET para as funções do Azure em alguns passos rápidos.
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 06/20/2019
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 6894f9bc6c803e2692afb54d7459adf6b0e6dbd6
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67828093"
---
# <a name="continuously-deploy-to-azure-functions-with-devops-projects"></a>Implemente continuamente para funções do Azure com projetos de DevOps

Projetos de DevOps do Azure apresenta uma experiência simplificada de onde pode colocar o seu código existente e o repositório de Git ou escolha um aplicativo de exemplo para criar um pipeline de entrega contínua (CD) para o Azure e a integração contínua (CI).

Projetos de DevOps também:

* Cria automaticamente os recursos do Azure, como as funções do Azure

* Cria e configura um pipeline de lançamento em DevOps do Azure para CI/CD

Neste tutorial, irá:

> [!div class="checklist"]
>* Utilizar projetos de DevOps para implementar uma aplicação ASP.NET para a função do Azure
>* Configurar o Azure DevOps e uma subscrição do Azure
>* Examinar a função do Azure
>* Examinar o pipeline de CI
>* Examinar o pipeline de CD
>* Consolide as alterações com o Git e implementá-las automaticamente para o Azure
>* Limpar recursos

Atualmente, os tempos de execução suportados para as funções são **.NET** e **node. js**. Usamos. Tempo de execução NET para este tutorial implementar as funções do Azure. 

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Pode obter uma gratuito [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/)

## <a name="use-devops-projects-to-deploy-an-aspnet-app-to-azure-functions"></a>Utilizar projetos de DevOps para implementar uma aplicação ASP.NET para as funções do Azure

Projetos de DevOps cria um pipeline CI/CD em Pipelines do Azure. Pode criar uma nova organização de DevOps do Azure ou utilizar uma organização existente. Projetos de DevOps também cria os recursos do Azure, como um IoTHub na subscrição do Azure à sua escolha.

1. Inicie sessão no [portal do Azure](https://portal.azure.com)

1. No painel esquerdo, selecione **criar um recurso**.

1. Na caixa de pesquisa, escreva **projetos de DevOps**e, em seguida, clique em **Add**.

   ![DevOps Projects](_img/azure-devops-project-functions/devops-project.png)

1. Selecione **.NET**e, em seguida, selecione **próxima**. Sob **escolher uma estrutura de aplicações**, selecione **ASP.NET** e clique em **seguinte**.

1. Selecione **aplicação de funções** e, em seguida, selecione **próxima**.

## <a name="configure-azure-devops-and-azure-subscription"></a>Configurar o Azure DevOps e subscrição do Azure

1. Introduza um nome para o seu projeto de DevOps do Azure.

1. Crie uma nova organização do Azure DevOps ou selecione uma organização existente.

1. Selecione a sua subscrição do Azure.

1. Para ver as definições de configuração do Azure adicionais e para identificar o escalão de preços e a localização, clique em definições adicionais. Este painel apresenta várias opções de configuração, o escalão de preços e a localização de serviços do Azure.

1. Sair da área de configuração do Azure e, em seguida, selecione concluído.

1. Após alguns minutos, o processo é concluído. Um exemplo de aplicação ASP.NET está configurado num repositório de Git na sua organização de DevOps do Azure, uma aplicação de funções, Application Insights é criado, um pipeline CI/CD é executado e a aplicação é implementada para o Azure.

   Depois de tudo isso é concluído, é apresentado o dashboard do projeto de DevOps do Azure no portal do Azure. Também pode ir para o dashboard de projetos de DevOps diretamente a partir **todos os recursos** no portal do Azure.

   Este dashboard fornece visibilidade para o repositório de código do Azure DevOps, seu pipeline CI/CD e sua função do Azure. Pode configurar opções adicionais de CI/CD no seu pipeline de DevOps do Azure. No lado direito, selecione **aplicação de funções** para ver.

## <a name="examine-the-function-app"></a>Examine a aplicação de funções

Projetos de DevOps configura automaticamente a aplicação de funções, que pode explorar e personalizar. Para obter a aplicação de funções de saber, faça o seguinte:

1. Vá para o dashboard de projetos de DevOps.

    ![Dashboard de projetos de DevOps](_img/azure-devops-project-functions/devops-projects-dashboard.png)

1. No lado direito, selecione a aplicação de função. Um painel abre-se para a aplicação de funções. A partir desta vista pode realizar várias ações como operações de monitorização, pesquisa de registos.

    ![Aplicação de Funções](_img/azure-devops-project-functions/function-app.png)

## <a name="examine-the-ci-pipeline"></a>Examinar o pipeline de CI

Projetos de DevOps configura automaticamente um pipeline CI/CD na sua organização de DevOps do Azure. Pode explorar e personalizar o pipeline. Para se familiarizar com ele, faça o seguinte:

1. Vá para o dashboard de projetos de DevOps.

1. Clicar no hiperlink sob **criar**. Um separador do browser apresenta o pipeline de compilação para o novo projeto.

    ![Compilação](_img/azure-devops-project-functions/build.png)

1. Selecione **Editar**. Neste painel, pode examinar as várias tarefas para o seu pipeline de compilação. A compilação executa várias tarefas, como ao obter código-fonte do repositório Git, compilando o aplicativo, executando testes de unidade e publicação saídas que são utilizadas para implementações.

1. Selecione **Acionadores**. Projetos de DevOps cria automaticamente um acionador de CI e cada consolidação para o repositório inicia uma nova compilação. Opcionalmente, pode optar por incluir ou excluir os ramos do processo de CI.

1. Selecione **Retenção**. Dependendo do seu cenário, pode especificar políticas de manter ou remover um determinado número de compilações.

1. Na parte superior do pipeline de compilação, selecione o nome do pipeline de compilação.

1. Alterar o nome do seu pipeline de compilação para algo mais descritivo e, em seguida, selecione **salvar** partir a **guardar e colocar em fila** lista pendente.

1. No nome do pipeline de compilação, selecione **Histórico**. Este painel apresenta uma trilha de auditoria das alterações recentes para a compilação. DevOps do Azure mantenha um registo de todas as alterações feitas no pipeline de compilação e permite-lhe comparar versões.

## <a name="examine-the-cd-release-pipeline"></a>Examine o pipeline de lançamento do CD

Projetos de DevOps automaticamente cria e configura os passos necessários para implementar a partir de sua organização de DevOps do Azure à sua subscrição do Azure. Estes passos incluem a configuração de uma ligação de serviço do Azure para autenticar o DevOps do Azure à sua subscrição do Azure. A automação também cria um pipeline de lançamento, que fornece o CD para o Azure. Para saber mais sobre o pipeline de lançamento, faça o seguinte:

1. Navegue para o **Pipelines | Versões**.

1. Clique em **editar**.

1. Em **Artefactos**, selecione **Remover**. O pipeline de compilação examinado nos passos anteriores produz a saída que é utilizada para o artefacto.

1. No lado direito do **Drop** ícone, selecione **acionador de implementação contínua**. Esta versão de pipeline tem um acionador de CD ativado, o que é executada uma implantação sempre que um novo artefacto de compilação está disponível. Opcionalmente, pode desativar o acionador para que as suas implementações exigem execução manual.

1. No lado direito, selecione **ver versões** para apresentar um histórico de versões.

1. Clique na versão, que exibirá o pipeline. Clique em qualquer ambiente para verificar a versão **resumo, consolidações**, associado **itens de trabalho**.

1. Selecione **Consolidações**. Esta vista mostra as confirmações de código que estão associadas esta implementação. Compare as versões para ver as diferenças de consolidação entre implementações.

1. Selecione **ver registos**. Os registos contêm informações úteis sobre o processo de implementação. Pode visualizá-las durante e depois das implementações.

## <a name="commit-code-changes-and-execute-cicd"></a>Consolidar as alterações de código e executar o CI/CD

> [!NOTE]
> O procedimento seguinte testa o pipeline de CI/CD ao fazer uma alteração de texto simples.

Agora, está pronto para colaborar com uma equipa na sua aplicação ao utilizar um processo de CI/CD que implementa automaticamente o seu trabalho mais recente para a sua função do Azure. Cada alteração para o repositório de Git inicia uma compilação em DevOps do Azure e um pipeline CD executa uma implementação para o Azure. Siga o procedimento nesta secção, ou utilize outra técnica para consolidar as alterações ao seu repositório. Por exemplo, pode clonar o repositório de Git na sua ferramenta favorita ou IDE, e, em seguida, enviar alterações para este repositório.

1. No menu de DevOps do Azure, selecione **repositórios | Ficheiros**e, em seguida, aceda ao seu repositório.

1. O repositório já contém o código chamado **SampleFunctionApp** baseada na linguagem de aplicação que escolheu no processo de criação. Abra o **Application/SampleFunctionApp/Function1.cs** ficheiro.

1. Selecione **edite**e, em seguida, faça uma alteração ao **31 do número de linha** . Por exemplo, pode atualizá-la para **Olá lá! Bem-vindo às funções do Azure, usando os projetos de DevOps**

1. Na parte superior direita, selecione **consolidar**e, em seguida, selecione **consolidar** novamente para enviar sua alteração.

1. Abra o **Application/SampleFunctionApp.Test/Function1TestRunner.cs** ficheiro. 

1. Selecione **edite**e, em seguida, faça uma alteração ao **número 21 de linha**. Por exemplo, pode atualizá-la para **Olá lá! Bem-vindo às funções do Azure, usando os projetos de DevOps do Azure**.

     Após alguns instantes, uma compilação é iniciada no Azure DevOps e uma versão executa para implementar as alterações. Monitorize o estado de compilação no dashboard de projetos de DevOps ou no navegador com a sua organização de DevOps do Azure.

## <a name="clean-up-resources"></a>Limpar recursos

Pode eliminar os recursos relacionados que criou quando não precisar delas. Utilize o **eliminar** funcionalidade no dashboard de projetos de DevOps.

## <a name="next-steps"></a>Passos Seguintes

Opcionalmente, pode modificar estes pipelines de compilação e versão para satisfazer as necessidades da sua equipa. Também pode utilizar este padrão de CI/CD como modelo para outros pipelines. Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Utilizar projetos de DevOps para implementar uma aplicação ASP.NET Core em função do Azure
> * Configurar o Azure DevOps e uma subscrição do Azure 
> * Examinar a função do Azure
> * Examinar o pipeline de CI
> * Examinar o pipeline de CD
> * Consolide as alterações com o Git e implementá-las automaticamente para o Azure
> * Limpar recursos

