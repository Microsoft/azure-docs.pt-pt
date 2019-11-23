---
title: 'Tutorial: implantar aplicativos ASP.NET para Azure Functions com Azure DevOps Projects'
description: Projetos de DevOps do Azure torna mais fácil começar a utilizar no Azure. Com DevOps Projects, você pode implantar seu aplicativo ASP.NET para Azure Functions em algumas etapas rápidas.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 06/20/2019
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 4e7e9428af86f131632650f18d45e7dd48f4b5cb
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2019
ms.locfileid: "71971564"
---
# <a name="continuously-deploy-to-azure-functions-with-devops-projects"></a>Implantar continuamente em Azure Functions com DevOps Projects

Azure DevOps Projects apresenta uma experiência simplificada em que você pode colocar seu código existente e o repositório git ou escolher um aplicativo de exemplo para criar um pipeline de CI (integração contínua) e um CD (entrega contínua) para o Azure.

DevOps Projects também:

* Cria automaticamente recursos do Azure, como Azure Functions

* Cria e configura um pipeline de liberação no Azure DevOps para CI/CD

Neste tutorial, irá:

> [!div class="checklist"]
>* Usar DevOps Projects para implantar um aplicativo ASP.NET no Azure function
>* Configurar o Azure DevOps e uma subscrição do Azure
>* Examinar a função do Azure
>* Examinar o pipeline de CI
>* Examinar o pipeline de CD
>* Confirmar alterações no git e implantá-las automaticamente no Azure
>* Limpar recursos

Atualmente, os tempos de execução com suporte para funções são **.net** e **node. js**. Usamos. Tempo de execução de rede deste tutorial a ser implantado no Azure Functions. 

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Você pode obter uma gratuitamente por meio de [Visual Studio dev Essentials](https://visualstudio.microsoft.com/dev-essentials/)

## <a name="use-devops-projects-to-deploy-an-aspnet-app-to-azure-functions"></a>Use DevOps Projects para implantar um aplicativo ASP.NET no Azure Functions

DevOps Projects cria um pipeline de CI/CD no Azure Pipelines. Você pode criar uma nova organização de DevOps do Azure ou usar uma organização existente. DevOps Projects também cria recursos do Azure, como um IoTHub, na assinatura do Azure de sua escolha.

1. Inicie sessão no [portal do Azure](https://portal.azure.com)

1. No painel esquerdo, selecione **Criar um recurso**.

1. Na caixa de pesquisa, digite **DevOps Projects**e clique em **Adicionar**.

   ![DevOps Projects](_img/azure-devops-project-functions/devops-project.png)

1. Selecione **.net**e, em seguida, selecione **Avançar**. Em **escolher uma estrutura de aplicativo**, selecione **ASP.net** e clique em **Avançar**.

1. Selecione **aplicativo de funções** e, em seguida, selecione **Avançar**.

## <a name="configure-azure-devops-and-azure-subscription"></a>Configurar o Azure DevOps e a assinatura do Azure

1. Insira um nome para seu projeto DevOps do Azure.

1. Crie uma nova organização de DevOps do Azure ou selecione uma organização existente.

1. Selecione a sua subscrição do Azure.

1. Para exibir as definições de configuração adicionais do Azure e identificar o tipo de preço e o local, clique em configurações adicionais. Esse painel exibe várias opções para configurar o tipo de preço e o local dos serviços do Azure.

1. Saia da área de configuração do Azure e, em seguida, selecione concluído.

1. Após alguns minutos, o processo é concluído. Um aplicativo ASP.NET de exemplo é configurado em um repositório git em sua organização do Azure DevOps, um Aplicativo de funções e Application Insights é criado, um pipeline de CI/CD é executado e seu aplicativo é implantado no Azure.

   Depois que tudo isso for concluído, o painel do projeto DevOps do Azure será exibido na portal do Azure. Você também pode acessar o painel de DevOps Projects diretamente de **todos os recursos** na portal do Azure.

   Esse painel fornece visibilidade do seu repositório de código DevOps do Azure, seu pipeline de CI/CD e sua função do Azure. Você pode configurar opções adicionais de CI/CD no pipeline DevOps do Azure. À direita, selecione **aplicativo de funções** para exibir.

## <a name="examine-the-function-app"></a>Examinar o Aplicativo de funções

DevOps Projects configura automaticamente o aplicativo de funções, que você pode explorar e personalizar. Para conhecer o aplicativo de funções, faça o seguinte:

1. Vá para o painel de DevOps Projects.

    ![Painel do DevOps Projects](_img/azure-devops-project-functions/devops-projects-dashboard.png)

1. À direita, selecione o aplicativo de funções. Um painel é aberto para o aplicativo de funções. Nessa exibição, você pode executar várias ações, como monitoramento de operações, pesquisando logs.

    ![Aplicação de Funções](_img/azure-devops-project-functions/function-app.png)

## <a name="examine-the-ci-pipeline"></a>Examinar o pipeline de CI

DevOps Projects configura automaticamente um pipeline de CI/CD em sua organização DevOps do Azure. Pode explorar e personalizar o pipeline. Para se familiarizar com ele, faça o seguinte:

1. Vá para o painel de DevOps Projects.

1. Clique no hiperlink em **Compilar**. Uma guia do navegador exibe o pipeline de Build para seu novo projeto.

    ![Compilação](_img/azure-devops-project-functions/build.png)

1. Selecione **Editar**. Neste painel, pode examinar as várias tarefas para o seu pipeline de compilação. A compilação executa várias tarefas, como a busca de código-fonte do repositório git, a criação do aplicativo, a execução de testes de unidade e a publicação de saídas que são usadas para implantações.

1. Selecione **Acionadores**. DevOps Projects cria automaticamente um gatilho de CI e todas as confirmações para o repositório iniciam uma nova compilação. Opcionalmente, você pode optar por incluir ou excluir ramificações do processo de CI.

1. Selecione **Retenção**. Dependendo do seu cenário, pode especificar políticas de manter ou remover um determinado número de compilações.

1. Na parte superior do pipeline de compilação, selecione o nome do pipeline de compilação.

1. Altere o nome do pipeline de compilação para algo mais descritivo e, em seguida, selecione **salvar** na lista suspensa **Salvar & fila** .

1. No nome do pipeline de compilação, selecione **Histórico**. Esse painel exibe uma trilha de auditoria de suas alterações recentes para a compilação. O Azure DevOps controla as alterações feitas no pipeline de compilação e permite que você compare as versões.

## <a name="examine-the-cd-release-pipeline"></a>Examinar o pipeline de liberação de CD

DevOps Projects cria e configura automaticamente as etapas necessárias para implantar de sua organização do Azure DevOps em sua assinatura do Azure. Essas etapas incluem a configuração de uma conexão de serviço do Azure para autenticar o Azure DevOps em sua assinatura do Azure. A automação também cria um pipeline de lançamento, que fornece o CD para o Azure. Para saber mais sobre o pipeline de lançamento, faça o seguinte:

1. Navegue até os **pipelines | Versões**do.

1. Clique em **Editar**.

1. Em **Artefactos**, selecione **Remover**. O pipeline de Build que você examinou nas etapas anteriores produz a saída usada para o artefato.

1. À direita do ícone de **soltar** , selecione **gatilho de implantação contínua**. Este pipeline de lançamento tem um gatilho de CD habilitado, que executa uma implantação sempre que um novo artefato de compilação está disponível. Opcionalmente, pode desativar o acionador para que as suas implementações exigem execução manual.

1. À direita, selecione **Exibir liberações** para exibir um histórico de versões.

1. Clique na versão, que exibirá o pipeline. Clique em qualquer ambiente para verificar o resumo da versão **, confirmações**, **itens de trabalho**associados.

1. Selecione **Consolidações**. Esta exibição mostra as confirmações de código que estão associadas a essa implantação. Compare as versões para ver as diferenças de consolidação entre implementações.

1. Selecione **Exibir logs**. Os registos contêm informações úteis sobre o processo de implementação. Você pode exibi-los durante e após as implantações.

## <a name="commit-code-changes-and-execute-cicd"></a>Consolidar as alterações de código e executar o CI/CD

> [!NOTE]
> O procedimento a seguir testa o pipeline de CI/CD fazendo uma alteração de texto simples.

Agora você está pronto para colaborar com uma equipe em seu aplicativo usando um processo de CI/CD que implanta automaticamente seu trabalho mais recente em sua função do Azure. Cada alteração no repositório git inicia uma compilação no Azure DevOps e um pipeline de CD executa uma implantação no Azure. Siga o procedimento nesta seção ou use outra técnica para confirmar as alterações no seu repositório. Por exemplo, você pode clonar o repositório git em sua ferramenta favorita ou IDE e, em seguida, enviar por push as alterações para esse repositório.

1. No menu DevOps do Azure, selecione **repositórios |** E, em seguida, vá para o repositório.

1. O repositório já contém um código chamado **SampleFunctionApp** com base no idioma do aplicativo que você escolheu no processo de criação. Abra o arquivo **Application/SampleFunctionApp/function1. cs** .

1. Selecione **Editar**e, em seguida, faça uma alteração na **linha número 31** . Por exemplo, você pode atualizá-lo para **Olá! Bem-vindo ao Azure Functions usando DevOps Projects**

1. No canto superior direito, selecione **confirmar**e, em seguida, selecione **confirmar** novamente para enviar por push sua alteração.

1. Abra o arquivo **Application/SampleFunctionApp. Test/Function1TestRunner. cs** . 

1. Selecione **Editar**e, em seguida, faça uma alteração no **número de linha 21**. Por exemplo, você pode atualizá-lo para **Olá! Bem-vindo ao Azure Functions usando Azure DevOps Projects**.

     Depois de alguns instantes, uma compilação é iniciada no Azure DevOps e uma versão é executada para implantar as alterações. Monitore o status da compilação no painel DevOps Projects ou no navegador com sua organização DevOps do Azure.

## <a name="clean-up-resources"></a>Limpar recursos

Você pode excluir os recursos relacionados que criou quando não precisa mais deles. Utilize o **eliminar** funcionalidade no dashboard de projetos de DevOps.

## <a name="next-steps"></a>Passos seguintes

Opcionalmente, pode modificar estes pipelines de compilação e versão para satisfazer as necessidades da sua equipa. Também pode utilizar este padrão de CI/CD como modelo para outros pipelines. Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Usar DevOps Projects para implantar um aplicativo ASP.NET Core no Azure function
> * Configurar o Azure DevOps e uma subscrição do Azure 
> * Examinar a função do Azure
> * Examinar o pipeline de CI
> * Examinar o pipeline de CD
> * Confirmar alterações no git e implantá-las automaticamente no Azure
> * Limpar recursos

