---
title: 'Tutorial: Implementar ASP.NET aplicações para funções Azure com Azure DevOps Starter'
description: O Azure DevOps Starter facilita o arranque do Azure. Com o DevOps Starter, pode implementar a sua aplicação ASP.NET para Azure Functions em alguns passos rápidos.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 03/24/2020
author: mlearned
ms.custom: devx-track-csharp
ms.openlocfilehash: 3277fefb5615fd492b2859b741ef8e6f991eaf23
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91355479"
---
# <a name="deploy-to-azure-functions-with-devops-starter"></a>Implementar para funções Azure com devOps Starter

O Azure DevOps Starter apresenta uma experiência simplificada onde pode trazer o seu código existente e git repo ou escolher uma aplicação de amostra para criar um pipeline de integração contínua (CI) e entrega contínua (CD) para Azure.

DevOps Starter também:

* Cria automaticamente recursos Azure, tais como Funções Azure

* Cria e configura um gasoduto de libertação em Azure DevOps para CI/CD

Neste tutorial, vai:

> [!div class="checklist"]
>* Utilize o DevOps Starter para implementar uma aplicação ASP.NET para a Função Azure
>* Configurar Azure DevOps e uma subscrição da Azure
>* Examinar a Função Azure
>* Examinar o pipeline de CI
>* Examinar o pipeline de CD
>* Cometa alterações no Git e desloque-as automaticamente para a Azure
>* Limpar os recursos

Atualmente, os tempos de funcionamento suportados para funções são **.NET** e **Node.js**. Nós usamos. Tempo de execução NET para este tutorial para implementar para as Funções Azure. 

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Você pode obter um grátis através [do Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/)

## <a name="use-devops-starter-to-deploy-an-aspnet-app-to-azure-functions"></a>Utilize o DevOps Starter para implementar uma aplicação ASP.NET para funções Azure

DevOps Starter cria um gasoduto CI/CD em Pipelines Azure. Pode criar uma nova organização Azure DevOps ou utilizar uma organização existente. DevOps Projects também cria recursos Azure, como um IoTHub, na subscrição Azure da sua escolha.

1. Inicie sessão no [portal do Azure](https://portal.azure.com)

1. Na caixa de pesquisa, escreva **DevOps Starter** e, em seguida, selecione. Clique em **Adicionar** para criar um novo.

    ![O painel de arranque de DevOps](_img/azure-devops-starter-aks/search-devops-starter.png)

1. Selecione **.NET** e, em seguida, selecione **Seguinte**. Em **Escolha uma estrutura de aplicação,** selecione **ASP.NET** e clique em **Seguinte**.

1. Selecione **a App de Função** e, em seguida, selecione **Seguinte**.

## <a name="configure-azure-devops-and-azure-subscription"></a>Configurar a subscrição Azure DevOps e Azure

1. Insira um nome para o seu projeto Azure DevOps.

1. Crie uma nova organização Azure DevOps ou selecione uma organização existente.

1. Selecione a sua subscrição do Azure.

1. Para ver as definições adicionais de configuração do Azure e identificar o nível de preços e a localização, clique em **definições adicionais**. Este painel apresenta várias opções para configurar o nível de preços e a localização dos serviços Azure.

1. Saia da área de configuração Azure e, em seguida, selecione **Fazer**.

1. Após alguns minutos, o processo está concluído. Uma amostra ASP.NET aplicação é criada num git repo na sua organização Azure DevOps, uma App de Função e Insights de Aplicação é criado, um pipeline CI/CD é executado, e a sua aplicação é implementada para o Azure.

   Depois de tudo isto estar concluído, o painel de arranque Azure DevOps é apresentado no portal Azure. Também pode ir ao painel de arranque de DevOps diretamente de **todos os recursos** do portal Azure.

   Este painel proporciona visibilidade ao repositório de códigoS Azure DevOps, ao seu pipeline CI/CD e à sua Função Azure. Pode configurar opções adicionais de CI/CD no seu pipeline Azure DevOps. À direita, selecione **App de função** para visualizar.

## <a name="examine-the-function-app"></a>Examinar a App de Função

DevOps Starter configura automaticamente a aplicação de função, que pode explorar e personalizar. Para conhecer a aplicação de função, faça o seguinte:

1. Vá ao painel de arranque do DevOps.

    ![Painel de projetos de devops](_img/azure-devops-project-functions/fapp-dashboard.png)

1. À direita, selecione a aplicação de função. Abre-se um painel para a aplicação de função. A partir desta vista pode realizar várias ações, tais como monitorização de operações, pesquisa de registos.

    ![Aplicação de Funções](_img/azure-devops-project-functions/function-app.png)

## <a name="examine-the-ci-pipeline"></a>Examinar o pipeline de CI

O DevOps Starter configura automaticamente um pipeline CI/CD na sua organização Azure DevOps. Pode explorar e personalizar o pipeline. Para se familiarizar com ele, faça o seguinte:

1. Vá ao painel de arranque do DevOps.

1. Clique na hiperligação em **Build**. Um separador de navegador exibe o pipeline de construção para o seu novo projeto.

    ![A screenshot mostra o painel de arranque do portal Azure DevOps com uma seta apontando para uma ligação sob a construção no gasoduto C I/C D.](_img/azure-devops-project-functions/build.png)

1. Selecione **Editar**. Neste painel, pode examinar as várias tarefas para o seu oleoduto de construção. A construção executa várias tarefas, tais como o código fonte de busca do repo Git, a construção da aplicação, a execução de testes unitários e a publicação de saídas que são usadas para implementações.

1. Selecione **Triggers**. O DevOps Starter cria automaticamente um gatilho de CI, e cada compromisso com o repo inicia uma nova construção. Opcionalmente, pode optar por incluir ou excluir os ramos do processo de IC.

1. Selecione **Retenção**. Dependendo do seu cenário, pode especificar políticas para manter ou remover um determinado número de construções.

1. Na parte superior do pipeline de compilação, selecione o nome do pipeline de compilação.

1. Mude o nome do seu pipeline de construção para algo mais descritivo e, em seguida, **selecione Save** from the Save & de retirada da **fila.**

1. No nome do pipeline de compilação, selecione **Histórico**. Este painel apresenta um rasto de auditoria das suas recentes alterações para a construção. O Azure DevOps acompanha quaisquer alterações feitas ao pipeline de construção, e permite comparar versões.

## <a name="examine-the-cd-release-pipeline"></a>Examine o oleoduto de libertação de CD

O DevOps Starter cria e configura automaticamente os passos necessários para implementar da sua organização Azure DevOps para a sua subscrição Azure. Estes passos incluem configurar uma ligação de serviço Azure para autenticar Azure DevOps para a sua subscrição Azure. A automatização também cria um gasoduto de libertação, que fornece o CD ao Azure. Para saber mais sobre o oleoduto de lançamento, faça o seguinte:

1. Navegue até ao **pipelines | Lançamentos**.

1. Clique em **Editar.**

1. Em **Artefactos**, selecione **Remover**. O gasoduto de construção que examinou nos passos anteriores produz a saída que é usada para o artefacto.

1. À direita do ícone **Drop,** selecione **o gatilho de implementação contínua**. Este oleoduto de lançamento tem um disparador de CD ativado, que executa uma implantação sempre que um novo artefacto de construção está disponível. Opcionalmente, pode desativar o gatilho para que as suas implementações exijam uma execução manual.

1. À direita, **selecione Ver ver lançamentos** para exibir um histórico de lançamentos.

1. Clique no desbloqueio, que irá exibir o oleoduto. Clique em qualquer ambiente para verificar o **lançamento Resumo, Compromissos,** Itens de **Trabalho associados**.

1. Selecione **Consolidações**. Esta visão mostra compromissos de código que estão associados a esta implementação. Compare as versões para ver as diferenças de consolidação entre implementações.

1. Selecione **Ver Registos**. Os registos contêm informações úteis sobre o processo de implementação. Pode vê-los durante e após as missões.

## <a name="commit-code-changes-and-execute-cicd"></a>Consolidar as alterações de código e executar o CI/CD

> [!NOTE]
> O procedimento a seguir testa o gasoduto CI/CD através de uma simples alteração de texto.

Está agora pronto para colaborar com uma equipa na sua aplicação utilizando um processo CI/CD que implementa automaticamente o seu mais recente trabalho na sua Função Azure. Cada mudança para o git repo inicia uma construção em Azure DevOps, e um oleoduto de CD executa uma implantação para Azure. Siga o procedimento nesta secção ou use outra técnica para comprometer alterações no seu repo. Por exemplo, pode clonar o repo Git na sua ferramenta preferida ou IDE e, em seguida, empurrar alterações para este repo.

1. No menu Azure DevOps, selecione **Repos | Ficheiros,** e depois ir para o seu repo.

1. O repositório já contém código chamado **SampleFunctionApp** com base no idioma de aplicação que escolheu no processo de criação. Abra o ficheiro **Application/SampleFunctionApp/Function1.cs.**

1. **Selecione Editar** e, em seguida, faça uma alteração para a **linha número 31** . Por exemplo, pode atualizá-lo para **Hello lá! Bem-vindo às Funções Azure usando DevOps Starter**

1. No topo direito, **selecione Commit** e, em seguida, selecione **Comprometa-se** novamente para empurrar a sua mudança.

1. Abra o ficheiro **Application/SampleFunctionApp.Test/Function1TestRunner.cs.** 

1. **Selecione Editar** e, em seguida, faça uma alteração para a **linha número 21**. Por exemplo, pode atualizá-lo para **Hello lá! Bem-vindo às Funções Azure utilizando o Azure DevOps Starter**.

     Após alguns momentos, uma construção começa em Azure DevOps e um lançamento executa para implementar as alterações. Monitorize o estado de construção no painel de partidas de DevOps ou no navegador com a sua organização Azure DevOps.

## <a name="clean-up-resources"></a>Limpar os recursos

Pode eliminar os recursos relacionados que criou quando já não precisa deles. Utilize a funcionalidade **Eliminar** no painel de arranque de devOps.

## <a name="next-steps"></a>Passos seguintes

Opcionalmente, pode modificar estes pipelines de compilação e versão para satisfazer as necessidades da sua equipa. Também pode utilizar este padrão de CI/CD como modelo para outros pipelines. Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Utilize o DevOps Starter para implementar uma aplicação Core ASP.NET para Azure Function
> * Configurar Azure DevOps e uma subscrição da Azure 
> * Examinar a Função Azure
> * Examinar o pipeline de CI
> * Examinar o pipeline de CD
> * Cometa alterações no Git e desloque-as automaticamente para a Azure
> * Limpar os recursos

