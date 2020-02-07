---
title: Crie um oleoduto CI/CD para Node.js com projetos Azure DevOps
description: A DevOps Projects facilita o início do Azure. Ajuda-o a utilizar o seu próprio código e o repositório GitHub para lançar uma aplicação num serviço do Azure à sua escolha com alguns passos rápidos.
ms.prod: devops
ms.technology: devops-cicd
services: vsts
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
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019
monikerRange: vsts
ms.openlocfilehash: 35eebeaa393ff75ada11752aaf9f195efddfa12b
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/06/2020
ms.locfileid: "77049781"
---
#  <a name="quickstart-create-a-cicd-pipeline-in-azure-pipelines-for-nodejs-with-azure-devops-projects"></a>Quickstart: Criar um oleoduto CI/CD em Pipelines Azure para Node.js com projetos Azure DevOps

Neste arranque rápido, utiliza a experiência simplificada de Projetos Azure DevOps para criar um pipeline de integração contínua (CI) e entrega contínua (CD) para a sua app Node.js em Pipelines Azure. Pode utilizar projetos Azure DevOps para configurar tudo o que precisa para desenvolver, implementar e monitorizar a sua aplicação. 

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuitamente.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) 
- Uma conta [azure DevOps](https://azure.microsoft.com/services/devops/) e organização.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

A DevOps Projects cria um oleoduto CI/CD em Pipelines Azure. Você pode criar uma nova organização de DevOps do Azure ou usar uma organização existente. Projetos de DevOps também cria os recursos do Azure na subscrição do Azure à sua escolha.

1. Inscreva-se no [portal Azure](https://portal.azure.com), e no painel esquerdo, selecione **Criar um recurso**. 

   ![Criar um recurso Azure no portal Azure](_img/azure-devops-project-nodejs/create-azure-resource.png)

1. Procure e selecione **Projetos DevOps,** e, em seguida, **selecione Criar**.

## <a name="select-a-sample-application-and-azure-service"></a>Selecione um exemplo de aplicação e o serviço do Azure

1. Selecione a aplicação da amostra Node.js.  
    Os exemplos Node.js incluem várias opções de arquiteturas de aplicações.

1. A estrutura da amostra padrão é Express.js. Deixe a definição predefinida e, em seguida, selecione **Seguinte**.  
    **A Aplicação Web no Windows** é o destino de implementação predefinido.  O quadro de aplicação, que escolheu anteriormente, dita o tipo de alvo de implementação de serviço seleções Azure aqui disponível.  

2. Deixe o serviço predefinido e, em seguida, selecione **Seguinte**.
 
## <a name="configure-azure-devops-and-an-azure-subscription"></a>Configurar o Azure DevOps e uma subscrição do Azure 

1. Crie uma nova organização Azure DevOps ou escolha uma organização existente. 
   
   1. Insira um nome para o seu projeto.
      
   1. Selecione a sua subscrição e localização Azure, introduza um nome para a sua aplicação e, em seguida, selecione **Done**.  
      Após alguns minutos, o painel de instrumentos DevOps Projects é apresentado no portal Azure. Uma aplicação de amostra é configurada num repositório na sua organização Azure DevOps, uma construção é executada, e a sua aplicação é implantada para o Azure. Este dashboard fornece visibilidade para o repositório de código, o pipeline de CI/CD e a sua aplicação no Azure.
   
1. **Selecione Browse** para visualizar a sua aplicação de execução.
   
   ![Vista do painel de instrumentos do oleoduto CI/CD](_img/azure-devops-project-nodejs/devops-projects-dashboard.png) 

Os Projetos DevOps configuraram automaticamente um gatilho de construção e libertação de CI.  Está agora pronto para colaborar com uma equipa numa aplicação Node.js com um processo CI/CD que implementa automaticamente o seu mais recente trabalho no seu site.

## <a name="commit-code-changes-and-execute-cicd"></a>Consolidar as alterações de código e executar o CI/CD

A DevOps Projects cria um repositório Git em Azure Repos ou GitHub. Tome os seguintes passos para ver o repositório e efaça alterações de código na sua aplicação.

1. À esquerda do painel de projetos DevOps, selecione o link para o seu ramo principal.  
Esta ligação abre uma vista para o repositório Git recentemente criado.

1. Para ver o URL do clone repositório, selecione **Clone** no canto superior direito do navegador.   
    Pode clonar o repositório Git no seu IDE preferido. Nos próximos passos, pode utilizar o browser para fazer e consolidar alterações de código diretamente no ramo principal.

1. Do lado esquerdo do navegador, vá ao ficheiro **views/index.pug.**

1. **Selecione Editar**e, em seguida, fazer uma alteração na rubrica h2.  
    Por exemplo, insira **Começar imediatamente com projetos Azure DevOps** ou fazer alguma outra alteração.

1. Selecione **'Cometer'** e, em seguida, guardar as suas alterações.

1. No seu navegador, vá ao painel de projetos DevOps.   
Agora deve ver uma construção em andamento. As alterações que acabou de fazer são automaticamente construídas e implantadas através de um oleoduto CI/CD.

## <a name="examine-the-azure-cicd-pipeline"></a>Examinar o pipeline de CI/CD do Azure

No passo anterior, os Projetos DevOps configuraram automaticamente um pipeline CI/CD completo. Explore e personalize o pipeline, conforme necessário. Tome os seguintes passos para se familiarizar com os oleodutos de construção e libertação.

1. No topo do painel de projetos DevOps, selecione **Build Pipelines**.  
Este link abre um separador de navegador e o pipeline de construção para o seu novo projeto.

1. Aponte para o campo **Status** e, em seguida, selecione a elipse (...).  
    Esta ação abre um menu onde pode iniciar várias atividades como fazer fila de uma nova construção, fazer uma pausa na construção e editar o pipeline de construção.

1. Selecione **Editar**.

1. Neste painel, pode examinar as várias tarefas para o seu pipeline de compilação.  
A construção executa uma variedade de tarefas como obter fontes do repositório Git, restaurar dependências e publicar saídas que são usadas para implementações.

1. Na parte superior do pipeline de compilação, selecione o nome do pipeline de compilação.

1. Mude o nome do seu pipeline de construção para algo mais descritivo, selecione **Save & queue,** e, em seguida, selecione **Guardar**.

1. No nome do pipeline de compilação, selecione **Histórico**.   
No painel **história,** você vê um rasto de auditoria das suas recentes mudanças para a construção.  Pipelines do Azure mantém um registro de quaisquer alterações efetuadas no pipeline de compilação e permite-lhe comparar versões.

1. Selecione **Acionadores**.   
 Projetos de DevOps criado automaticamente um acionador de CI e cada consolidação no repositório inicia uma nova compilação.  Opcionalmente, pode optar por incluir ou excluir os ramos do processo de CI.

1. Selecione **Retenção**.   
Dependendo do seu cenário, pode especificar políticas de manter ou remover um determinado número de compilações.

1. Selecione **Construir e Soltar**e, em seguida, selecione **Lançamentos**.  
 A DevOps Projects cria um oleoduto de libertação para gerir as implantações para o Azure.

1. À esquerda, selecione a elipse (...) junto ao seu gasoduto de libertação e, em seguida, **selecione Editar**.  
O gasoduto de libertação define o processo de libertação.

12. Em **Artefactos**, selecione **Remover**.  
    O pipeline de compilação que examinou nos passos anteriores produz a saída utilizada para o artefacto. 

1. Ao lado do ícone **Drop,** selecione o **gatilho de implantação Contínua**.  
Esta versão de pipeline tem um acionador de CD ativado, o que é executada uma implantação sempre que houver um artefacto de compilação nova disponível. Opcionalmente, pode desativar o acionador para que as suas implementações exigem execução manual. 


1. À esquerda, selecione **Tarefas**.   
As tarefas são as atividades que executa o processo de implementação. Neste exemplo, foi criada uma tarefa para implantar no Azure App Service.


1. À direita, selecione **versões**.  
Esta vista mostra um histórico das versões.

1. Selecione a elipse (...) ao lado de um dos seus lançamentos e, em seguida, selecione **Open**.  
Existem vários menus para explorar, como um resumo de lançamento, itens de trabalho associados e testes.

1. Selecione **Consolidações**.   
Esta vista mostra as confirmações de código que estão associadas a implementação específica.

1. Selecione **Registos**.  
Os registos contêm informações úteis sobre o processo de implementação. Podem ser vistos durante e após as implementações.

## <a name="clean-up-resources"></a>Limpar recursos

É possível eliminar o serviço de aplicações do Azure e outros recursos relacionados que criou quando não precisar delas. Utilize a funcionalidade **Eliminar** no painel de instrumentos de Projetos DevOps.


## <a name="next-steps"></a>Passos seguintes

Quando configurao o seu processo CI/CD, os gasodutos de construção e de libertação foram automaticamente criados. Pode modificar estes pipelines de compilação e de lançamento para satisfazer as necessidades da sua equipa. Para saber mais sobre o oleoduto CI/CD, consulte:

> [!div class="nextstepaction"]
> [Personalizar o processo de CD](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

## <a name="videos"></a>Vídeos

> [!VIDEO https://www.youtube.com/embed/3etwjubReJs]
