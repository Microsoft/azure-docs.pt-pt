---
title: 'Quickstart: Criar um pipeline CI/CD para .NET com projetos Azure DevOps'
description: A Azure DevOps Projects facilita o início do Azure. Ajuda-o a iniciar uma aplicação .NET num serviço do Azure à sua escolha com alguns passos rápidos.
ms.prod: devops
ms.technology: devops-cicd
services: azure-devops-project
documentationcenter: vs-devops-build
author: mlearned
manager: gwallace
editor: ''
ms.assetid: ''
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 07/09/2018
ms.author: mlearned
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: 7d2ccdfa1fe553d0795a82856dd255f4a54138bf
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "70898028"
---
# <a name="create-a-cicd-pipeline-for-net-with-azure-devops-projects"></a>Crie um oleoduto CI/CD para .NET com projetos Azure DevOps

Configure a integração contínua (CI) e a entrega contínua (CD) para o seu núcleo .NET ou aplicação ASP.NET com projetos DevOps. A DevOps Projects simplifica a configuração inicial de um oleoduto de construção e lançamento em Pipelines Azure.

Se não tiver uma subscrição do Azure, pode obter uma subscrição gratuita através do [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

A DevOps Projects cria um oleoduto CI/CD em Azure DevOps. Você pode criar uma nova organização Azure DevOps ou usar uma organização existente. A DevOps Projects também cria recursos Azure na subscrição Azure à sua escolha.

1. Inscreva-se no [portal Microsoft Azure](https://portal.azure.com).

1. No painel esquerdo, selecione **Criar um** ícone de recurso na barra de navegação esquerda e, em seguida, procurar **projetos DevOps**.  

3.  Selecione **Criar**.

    ![A Iniciar a Entrega Contínua](_img/azure-devops-project-aspnet-core/fullbrowser.png)

## <a name="select-a-sample-application-and-azure-service"></a>Selecione um exemplo de aplicação e serviço do Azure

1. Selecione o exemplo de aplicação **.NET**. Os exemplos de .NET incluem uma escolha da arquitetura de ASP.NET open source ou da arquitetura de .NET Core para várias plataformas.

   ![.NET Framework](_img/azure-devops-project-aspnet-core/chooselanguagedotnet.png)

2. Este exemplo corresponde a uma aplicação MVC ASP.NET Core. Selecione a estrutura de aplicação **.NET Core** e, em seguida, selecione **Next**.    
    
3. Selecione **o Windows Web App** como um alvo de implementação e, em seguida, selecione **Next**. Opcionalmente, pode escolher outros serviços Azure para a sua implementação. O quadro de aplicação, que escolheu anteriormente, dita o tipo de alvo de implantação de serviços Azure disponível aqui.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Configure Azure DevOps e uma subscrição Azure 

1. Introduza um **nome de Projeto**.

2. Crie uma nova **Organização Azure DevOps** gratuita ou escolha uma organização existente a partir do dropdown.

3. Selecione a sua **Subscrição Azure,** introduza um nome para a sua **aplicação Web** ou pegue o predefinido e, em seguida, selecione **Done**. Após alguns minutos, a visão geral de implantação de projetos DevOps é exibida no portal Azure. 

4. Selecione **Recorrer** para ver o painel de instrumentos do Projeto DevOps. No canto superior direito, coloque o **Projeto** no seu painel para um acesso rápido. Uma aplicação de amostra é configurada num repo na sua **Organização Azure DevOps**. Uma construção é executada, e a sua aplicação está implantada para o Azure.

5. O dashboard proporciona visibilidade no seu repo de código, no seu pipeline CI/CD e na sua aplicação em Azure. Na direita sob os recursos do Azure, **selecione Browse** para ver a sua aplicação de execução.

   ![Vista do Dashboard](_img/azure-devops-project-aspnet-core/dashboardnopreview.png) 

## <a name="commit-code-changes-and-execute-cicd"></a>Consolidar as alterações de código e executar o CI/CD

A DevOps Projects criou um repositório Git em Azure Repos ou GitHub. Para visualizar o repositório e fazer alterações de código na sua aplicação, faça o seguinte:

1. À esquerda do painel de projetos DevOps, selecione o link para o seu ramo **principal.** Esta ligação abre uma vista para o repositório Git recentemente criado.

2. Nos próximos passos, pode utilizar o navegador web para fazer e comprometer alterações de código diretamente no ramo **principal.** Também pode clonar o seu repositório Git no seu IDE favorito, selecionando **Clone** a partir da parte superior direita da página de repositório. 

3. À esquerda, navegue na estrutura do ficheiro de aplicação para **Application/aspnet-core-dotnet-core/Pages/Index.cshtml**.

4. **Selecione Editar**e, em seguida, fazer uma alteração na rubrica h2. Por exemplo, tipo **Get começou imediatamente com os Projetos Azure DevOps** ou fazer alguma sonorcultura.

      ![Edições de código](_img/azure-devops-project-aspnet-core/codechange.png)

5. Selecione **Comprometer,** deixe um comentário e selecione **Comprometer** novamente.

6. No seu navegador, vá ao painel de instrumentos do Projeto Azure DevOps.  Deverá agora ver que está em curso uma compilação. As alterações efetuadas são automaticamente construídas e implantadas através de um gasoduto CI/CD.

## <a name="examine-the-cicd-pipeline"></a>Examinar o gasoduto CI/CD

No passo anterior, a Azure DevOps Projects configuraautomaticamente um pipeline CI/CD completo. Explore e personalize o pipeline, conforme necessário. Tome os seguintes passos para se familiarizar com os oleodutos Azure DevOps.

1. No topo do painel de projetos DevOps, selecione **Build Pipelines**.  
Este link abre um separador de navegador e o Azure DevOps constrói um pipeline para o seu novo projeto.

1. Selecione a elipse (...).  Esta ação abre um menu onde pode iniciar várias atividades como fazer fila de uma nova construção, fazer uma pausa na construção e editar o pipeline de construção.

1. Selecione **Editar**.

    ![Pipeline de compilação](_img/azure-devops-project-aspnet-core/builddef.png)

1. Neste painel, pode examinar as várias tarefas para o seu pipeline de construção.  
 A construção executa várias tarefas, tais como a busca de fontes do repositório Git, restaurar dependências e publicar saídas usadas que são usadas para implantações.

1. Na parte superior do pipeline de compilação, selecione o nome do pipeline de compilação.

1. Mude o nome do seu oleoduto de construção para algo mais descritivo, selecione **Guardar & fila**e, em seguida, selecione **Guardar**.

1. No nome do pipeline de compilação, selecione **Histórico**.   
No painel **história,** você vê um rasto de auditoria das suas recentes mudanças para a construção.  A Azure Pipelines acompanha quaisquer alterações que sejam feitas no pipeline de construção, e permite comparar versões.

1. Selecione **Triggers**.  
A DevOps Projects criou automaticamente um gatilho ci, e cada compromisso com o repositório inicia uma nova construção.  Opcionalmente, pode optar por incluir ou excluir os ramos do processo de CI.

1. Selecione **Retenção**.  
Dependendo do seu cenário, pode especificar políticas para manter ou remover um determinado número de construções.

1. Selecione **Construir e Soltar**e, em seguida, selecione **Lançamentos**.  
A DevOps Projects cria um oleoduto de libertação para gerir as implantações para o Azure.

1.  À esquerda, selecione a elipse (...) junto ao seu gasoduto de libertação e, em seguida, **selecione Editar**.  
O pipeline de lançamento contém um pipeline, que define o processo de lançamento.  

1. Em **Artefactos**, selecione **Remover**.  O pipeline de compilação que examinou nos passos anteriores produz a saída utilizada para o artefacto. 

1. Ao lado do ícone **Drop,** selecione o **gatilho de implantação Contínua**.  
Este oleoduto de libertação tem um gatilho de CD ativado, que executa uma implantação sempre que há um novo artefacto de construção disponível. Opcionalmente, pode desativar o gatilho de modo a que as suas implementações exijam execução manual.  

1. À esquerda, selecione **Tarefas**.   
As tarefas são as atividades que o seu processo de implantação realiza. Neste exemplo, foi criada uma tarefa para implantar no Azure App Service.

1. À direita, selecione **versões**. Esta vista mostra um histórico das versões.

1. Selecione a elipse (...) ao lado de um dos seus lançamentos e, em seguida, selecione **Open**.  
Existem vários menus para explorar, como um resumo de lançamento, itens de trabalho associados e testes.


1. Selecione **Consolidações**.   
Esta visão mostra os compromissos de código que estão associados à implantação específica. 

1. Selecionar **Registos**.  
Os registos contêm informações úteis sobre o processo de implementação. Podem ser vistos durante e após as implementações.


## <a name="clean-up-resources"></a>Limpar recursos

Pode eliminar o Serviço de Aplicações Azure e outros recursos relacionados que criou quando já não precisa deles. Utilize a funcionalidade **Eliminar** no painel de instrumentos de Projetos DevOps.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como modificar os pipelines de compilação e de lançamento para satisfazer as necessidades da sua equipa, veja este tutorial:

> [!div class="nextstepaction"]
> [Personalizar o processo de CD](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

## <a name="videos"></a>Vídeos

> [!VIDEO https://www.youtube.com/embed/itwqMf9aR0w]
