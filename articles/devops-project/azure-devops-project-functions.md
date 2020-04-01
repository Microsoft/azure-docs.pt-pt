---
title: 'Tutorial: Implementar aplicações ASP.NET para funções azure com projetos Azure DevOps'
description: A Azure DevOps Projects facilita o início do Azure. Com projetos DevOps, você pode implementar a sua app ASP.NET para funções Azure em alguns passos rápidos.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 06/20/2019
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 4e7e9428af86f131632650f18d45e7dd48f4b5cb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "71971564"
---
# <a name="continuously-deploy-to-azure-functions-with-devops-projects"></a>Implantação contínua para funções azure com projetos DevOps

A Azure DevOps Projects apresenta uma experiência simplificada onde pode trazer o seu código existente e git repo ou escolher uma aplicação de amostra para criar um oleoduto de integração contínua (CI) e entrega contínua (CD) ao Azure.

Projetos DevOps também:

* Cria automaticamente recursos Azure, como funções Azure

* Cria e configura um oleoduto de libertação em Azure DevOps para CI/CD

Neste tutorial, irá:

> [!div class="checklist"]
>* Use projetos DevOps para implementar uma app ASP.NET para a Função Azure
>* Configure Azure DevOps e uma subscrição Azure
>* Examinar a Função Azure
>* Examinar o pipeline de CI
>* Examinar o pipeline de CD
>* Comprometa alterações a Git e as implemente automaticamente para o Azure
>* Limpar recursos

Atualmente, os tempos de execução suportados para funções são **.NET** e **Node.js**. Nós usamos. Tempo de funcionamento do NET para este tutorial ser implantado para funções Azure. 

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Você pode obter um grátis através [do Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/)

## <a name="use-devops-projects-to-deploy-an-aspnet-app-to-azure-functions"></a>Use projetos DevOps para implementar uma app ASP.NET para funções azure

A DevOps Projects cria um oleoduto CI/CD em Pipelines Azure. Você pode criar uma nova organização Azure DevOps ou usar uma organização existente. A DevOps Projects também cria recursos Azure, como um IoTHub, na subscrição Azure à sua escolha.

1. Inscreva-se no [portal Azure](https://portal.azure.com)

1. No painel esquerdo, selecione **Criar um recurso**.

1. Na caixa de pesquisa, digite **Projetos DevOps,** e, em seguida, clique em **Adicionar**.

   ![DevOps Projects](_img/azure-devops-project-functions/devops-project.png)

1. Selecione **.NET**, e, em seguida, selecione **Next**. Em 'Escolha uma estrutura de **aplicação',** selecione **ASP.NET** e clique **em Seguinte**.

1. Selecione **'App função'** e, em seguida, selecione **Next**.

## <a name="configure-azure-devops-and-azure-subscription"></a>Configure Azure DevOps e subscrição Azure

1. Insira um nome para o seu projeto Azure DevOps.

1. Crie uma nova organização Azure DevOps, ou selecione uma organização existente.

1. Selecione a sua subscrição do Azure.

1. Para ver configurações adicionais de configuração do Azure e para identificar o nível de preços e a localização, clique em configurações adicionais. Este painel apresenta várias opções para configurar o nível de preços e a localização dos serviços Azure.

1. Saia da área de configuração Azure e, em seguida, selecione Done.

1. Após alguns minutos, o processo está concluído. Uma amostra ASP.NET aplicação é configurada num repo Git na sua organização Azure DevOps, uma App de Funções e A aplicação Insights é executada, um pipeline CI/CD é executado, e a sua aplicação é implantada para o Azure.

   Depois de tudo isto estar concluído, o painel de instrumentos do Projeto Azure DevOps é apresentado no portal Azure. Você também pode ir ao dashboard DevOps Projects diretamente de **todos os recursos** no portal Azure.

   Este dashboard proporciona visibilidade no seu repositório de código Azure DevOps, no seu pipeline CI/CD e na sua Função Azure. Pode configurar opções adicionais de CI/CD no seu pipeline Azure DevOps. À direita, selecione **App de Funções** para visualizar.

## <a name="examine-the-function-app"></a>Examinar a App função

DevOps Projects configura automaticamente a aplicação de função, que pode explorar e personalizar. Para conhecer a aplicação de funções, faça o seguinte:

1. Vá ao painel de projetos DevOps.

    ![Painel de Projetos DevOps](_img/azure-devops-project-functions/devops-projects-dashboard.png)

1. À direita, selecione a aplicação de funções. Abre-se um painel para a aplicação de funções. Nesta perspetiva, pode realizar várias ações, tais como monitorização de operações, registos de pesquisa.

    ![Aplicação de Funções](_img/azure-devops-project-functions/function-app.png)

## <a name="examine-the-ci-pipeline"></a>Examinar o pipeline de CI

Os Projetos DevOps configuram automaticamente um oleoduto CI/CD na sua organização Azure DevOps. Pode explorar e personalizar o pipeline. Para se familiarizar com ele, faça o seguinte:

1. Vá ao painel de projetos DevOps.

1. Clique na hiperligação sob **build**. Um separador de navegador exibe o pipeline de construção para o seu novo projeto.

    ![Compilação](_img/azure-devops-project-functions/build.png)

1. Selecione **Editar**. Neste painel, pode examinar as várias tarefas para o seu pipeline de construção. A construção executa várias tarefas, tais como a obtenção do código fonte do repo Git, a construção da aplicação, a execução de testes de unidades e a publicação de saídas que são usadas para implantações.

1. Selecione **Triggers**. A DevOps Projects cria automaticamente um gatilho ci, e cada compromisso com o repo inicia uma nova construção. Opcionalmente, pode optar por incluir ou excluir balcões do processo de CI.

1. Selecione **Retenção**. Dependendo do seu cenário, pode especificar políticas para manter ou remover um determinado número de construções.

1. Na parte superior do pipeline de compilação, selecione o nome do pipeline de compilação.

1. Mude o nome do seu oleoduto de construção para algo mais descritivo e, em seguida, selecione **Save** from the **Save & dropdown** de fila.

1. No nome do pipeline de compilação, selecione **Histórico**. Este painel apresenta um rasto de auditoria das suas recentes alterações para a construção. Os Azure DevOps acompanham quaisquer alterações feitas ao pipeline de construção, e permite-lhe comparar versões.

## <a name="examine-the-cd-release-pipeline"></a>Examine o gasoduto de libertação do CD

Os Projetos DevOps criam e confundem automaticamente os passos necessários para implementar da sua organização Azure DevOps para a sua subscrição Azure. Estes passos incluem configurar uma ligação de serviço Azure para autenticar O Azure DevOps à sua subscrição Azure. A automatização também cria um oleoduto de libertação, que fornece o CD ao Azure. Para saber mais sobre o gasoduto de libertação, faça o seguinte:

1. Navegar para os **Oleodutos [ Lançamentos**.

1. Clique em **Editar**.

1. Em **Artefactos**, selecione **Remover**. O oleoduto de construção que examinou nos passos anteriores produz a saída que é usada para o artefacto.

1. À direita do ícone **Drop,** selecione **O gatilho de implantação contínua**. Este gasoduto de libertação tem um gatilho de CD ativado, que executa uma implantação sempre que um novo artefacto de construção está disponível. Opcionalmente, pode desativar o gatilho de modo a que as suas implementações exijam execução manual.

1. À direita, selecione **ver ver ver para** mostrar um histórico de lançamentos.

1. Clique no lançamento, que irá exibir o gasoduto. Clique em qualquer ambiente para verificar o resumo de **lançamento, Compromete,** itens de **trabalho associados**.

1. Selecione **Consolidações**. Esta visão mostra código compromete-se que estão associados a esta implantação. Compare as versões para ver as diferenças de consolidação entre implementações.

1. Selecione **Ver Registos**. Os registos contêm informações úteis sobre o processo de implementação. Pode vê-los durante e após as implantações.

## <a name="commit-code-changes-and-execute-cicd"></a>Consolidar as alterações de código e executar o CI/CD

> [!NOTE]
> O procedimento seguinte testa o gasoduto CI/CD fazendo uma simples alteração de texto.

Está agora pronto para colaborar com uma equipa na sua aplicação utilizando um processo CI/CD que implementa automaticamente o seu mais recente trabalho na sua Função Azure. Cada alteração para o repo Git inicia uma construção em Azure DevOps, e um pipeline de CD executa uma implantação para Azure. Siga o procedimento nesta secção ou utilize outra técnica para efetuar alterações no seu repo. Por exemplo, pode clonar o repo Git na sua ferramenta favorita ou IDE, e, em seguida, empurrar alterações para este repo.

1. No menu Azure DevOps, selecione **Repos / Arquivos,** e depois ir para o seu repo.

1. O repositório já contém código chamado **SampleFunctionApp** com base no idioma da aplicação que escolheu no processo de criação. Abra o ficheiro **Application/SampleFunctionApp/Function1.cs.**

1. **Selecione Editar**e, em seguida, faça uma alteração para a **linha número 31** . Por exemplo, pode atualizá-lo para **Hello lá! Bem-vindo às Funções Azure usando projetos DevOps**

1. Na parte superior direita, selecione **Comprometer**- e, em seguida, selecione **Comprometer** novamente para empurrar a sua mudança.

1. Abra o ficheiro **Application/SampleFunctionApp.Test/Function1TestRunner.cs.** 

1. **Selecione Editar**e, em seguida, faça uma alteração para a **linha número 21**. Por exemplo, pode atualizá-lo para **Hello lá! Bem-vindo às Funções Azure utilizando projetos Azure DevOps.**

     Após alguns momentos, uma construção começa em Azure DevOps e uma libertação executa para implementar as mudanças. Monitorize o estado de construção no painel de projetos DevOps ou no navegador com a sua organização Azure DevOps.

## <a name="clean-up-resources"></a>Limpar recursos

Pode eliminar os recursos relacionados que criou quando já não precisa deles. Utilize a funcionalidade **Eliminar** no painel de instrumentos de Projetos DevOps.

## <a name="next-steps"></a>Passos seguintes

Opcionalmente, pode modificar estes pipelines de compilação e versão para satisfazer as necessidades da sua equipa. Também pode utilizar este padrão de CI/CD como modelo para outros pipelines. Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Use projetos DevOps para implementar uma app ASP.NET Core para a Função Azure
> * Configure Azure DevOps e uma subscrição Azure 
> * Examinar a Função Azure
> * Examinar o pipeline de CI
> * Examinar o pipeline de CD
> * Comprometa alterações a Git e as implemente automaticamente para o Azure
> * Limpar recursos

