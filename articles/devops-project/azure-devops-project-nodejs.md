---
title: 'Início rápido: Criar um pipeline de CI/CD para node. js com Azure DevOps Projects'
description: DevOps Projects facilita a introdução ao Azure. Ajuda-o a utilizar o seu próprio código e o repositório GitHub para lançar uma aplicação num serviço do Azure à sua escolha com alguns passos rápidos.
ms.prod: devops
ms.technology: devops-cicd
services: vsts
documentationcenter: vs-devops-build
author: mlearned
manager: douge
editor: ''
ms.assetid: ''
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/09/2018
ms.author: mlearned
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: eeab0433310892f27b9cb2d2eb6f64e17e6f14ef
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/23/2019
ms.locfileid: "69991728"
---
#  <a name="create-a-cicd-pipeline-for--nodejs-with-azure-devops-projects"></a>Criar um pipeline de CI/CD para node. js com Azure DevOps Projects 

Azure DevOps Projects apresenta uma experiência simplificada que cria recursos do Azure e configura um pipeline de integração contínua (CI) e entrega contínua (CD) para seu aplicativo node. js no Azure Pipelines.  

Se não tiver uma subscrição do Azure, pode obter uma subscrição gratuita através do [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

DevOps Projects cria um pipeline de CI/CD no Azure Pipelines. Você pode criar uma nova organização de DevOps do Azure ou usar uma organização existente. Projetos de DevOps também cria os recursos do Azure na subscrição do Azure à sua escolha.

1. Inicie sessão no [Portal do Microsoft Azure](https://portal.azure.com).

1. No painel esquerdo, selecione **criar um recurso**e procure **DevOps Projects**. 

    ![Iniciar a configuração da Entrega Contínua](_img/azure-devops-project-nodejs/fullbrowser.png)

## <a name="select-a-sample-application-and-azure-service"></a>Selecione um exemplo de aplicação e serviço do Azure

1. Selecione o aplicativo de exemplo node. js.  
    Os exemplos Node.js incluem várias opções de arquiteturas de aplicações.

1. A estrutura de exemplo padrão é Express. js. Deixe a configuração padrão e, em seguida, selecione **Avançar**.  
    **A Aplicação Web no Windows** é o destino de implementação predefinido.  A estrutura do aplicativo, que você escolheu anteriormente, dita o tipo de destino de implantação de serviço do Azure disponível aqui.  

2. Deixe o serviço padrão e, em seguida, selecione **Avançar**.
 
## <a name="configure-azure-devops-and-an-azure-subscription"></a>Configurar o Azure DevOps e uma subscrição do Azure 

1. Crie uma nova organização de DevOps do Azure ou escolha uma organização existente. 

    a. Insira um nome para o seu projeto.

    b. Selecione sua assinatura e local do Azure, insira um nome para seu aplicativo e, em seguida, selecione **concluído**.  
    Depois de alguns minutos, o painel de DevOps Projects é exibido na portal do Azure. Um aplicativo de exemplo é configurado em um repositório em sua organização DevOps do Azure, uma compilação é executada e seu aplicativo é implantado no Azure. Este dashboard fornece visibilidade para o repositório de código, o pipeline de CI/CD e a sua aplicação no Azure.
     
3. Selecione **procurar** para exibir o aplicativo em execução.

    ![Vista do Dashboard](_img/azure-devops-project-nodejs/dashboardnopreview.png) 
    
DevOps Projects configurou automaticamente um gatilho de Build e versão de CI.  Agora você está pronto para colaborar com uma equipe em um aplicativo node. js com um processo de CI/CD que implanta automaticamente o trabalho mais recente em seu site da Web.

## <a name="commit-code-changes-and-execute-cicd"></a>Consolidar as alterações de código e executar o CI/CD

DevOps Projects cria um repositório git no Azure Repos ou no GitHub. Execute as etapas a seguir para exibir o repositório e fazer alterações de código em seu aplicativo.

1. À esquerda do painel de DevOps Projects, selecione o link para o Branch mestre.  
Esta ligação abre uma vista para o repositório Git recentemente criado.

1. Para exibir a URL de clone do repositório , selecione clonar na parte superior direita do navegador.   
    Pode clonar o repositório Git no seu IDE preferido. Nos próximos passos, pode utilizar o browser para fazer e consolidar alterações de código diretamente no ramo principal.

1. No lado esquerdo do navegador, vá para o arquivo **views/index. Pug** .

1. Selecione **Editar**e, em seguida, faça uma alteração no título H2.  
    Por exemplo, digite introdução imediatamente **com Azure DevOps Projects** ou faça alguma outra alteração.

1. Selecione **consolidar**e, em seguida, guarde as alterações.

1. No navegador, vá para o painel de DevOps Projects.   
Agora você deve ver uma compilação em andamento. As alterações que você acabou de fazer são criadas automaticamente e implantadas por meio de um pipeline de CI/CD.

## <a name="examine-the-azure-cicd-pipeline"></a>Examinar o pipeline de CI/CD do Azure

Na etapa anterior, DevOps Projects configurou automaticamente um pipeline de CI/CD completo. Explore e personalize o pipeline, conforme necessário. Execute as etapas a seguir para se familiarizar com os pipelines de compilação e versão.

1. Na parte superior do dashboard de projetos de DevOps, selecione **criar Pipelines**.  
Esse link abre uma guia do navegador e o pipeline de compilação para seu novo projeto.

1. Aponte para o campo **status** e, em seguida, selecione as reticências (...).  
    Essa ação abre um menu em que você pode iniciar várias atividades, como enfileirar uma nova compilação, pausar uma compilação e editar o pipeline de compilação.

1. Selecione **Editar**.

1. Neste painel, pode examinar as várias tarefas para o seu pipeline de compilação.  
A compilação executa uma variedade de tarefas, como busca de fontes do repositório git, restauração de dependências e publicação de saídas que são usadas para implantações.

1. Na parte superior do pipeline de compilação, selecione o nome do pipeline de compilação.

1. Altere o nome do seu pipeline de compilação para algo mais descritivo, selecione **guardar e colocar em fila**e, em seguida, selecione **guardar**.

1. No nome do pipeline de compilação, selecione **Histórico**.   
Na **histórico** painel, verá uma trilha de auditoria das alterações recentes para a compilação.  Pipelines do Azure mantém um registro de quaisquer alterações efetuadas no pipeline de compilação e permite-lhe comparar versões.

1. Selecione **Acionadores**.   
 Projetos de DevOps criado automaticamente um acionador de CI e cada consolidação no repositório inicia uma nova compilação.  Opcionalmente, pode optar por incluir ou excluir os ramos do processo de CI.

1. Selecione **Retenção**.   
Dependendo do seu cenário, pode especificar políticas de manter ou remover um determinado número de compilações.

1. Selecione **Compilar e liberar**e, em seguida, selecione **versões**.  
 DevOps Projects cria um pipeline de liberação para gerenciar implantações no Azure.

1. À esquerda, selecione as reticências (...) ao lado de seu pipeline de lançamento e, em seguida, selecione **Editar**.  
O pipeline de lançamento define o processo de liberação.

12. Em **Artefactos**, selecione **Remover**.  
    O pipeline de compilação que examinou nos passos anteriores produz a saída utilizada para o artefacto. 

1. Junto a **Drop** ícone, selecione a **acionador de implementação contínua**.  
Esta versão de pipeline tem um acionador de CD ativado, o que é executada uma implantação sempre que houver um artefacto de compilação nova disponível. Opcionalmente, pode desativar o acionador para que as suas implementações exigem execução manual. 


1. No lado esquerdo, selecione **tarefas**.   
As tarefas são as atividades que executa o processo de implementação. Neste exemplo, uma tarefa foi criada para ser implantada no serviço Azure App.


1. À direita, selecione **ver versões**.  
Esta vista mostra um histórico das versões.

1. Selecione as reticências (...) junto a um dos seus lançamentos e, em seguida, selecione **aberto**.  
Existem vários menus para explorar, como um resumo de lançamento, itens de trabalho associados e testes.

1. Selecione **Consolidações**.   
Esta vista mostra as confirmações de código que estão associadas a implementação específica.

1. Selecionar **Registos**.  
Os registos contêm informações úteis sobre o processo de implementação. Podem ser vistos durante e após as implementações.

## <a name="clean-up-resources"></a>Limpar recursos

É possível eliminar o serviço de aplicações do Azure e outros recursos relacionados que criou quando não precisar delas. Utilize o **eliminar** funcionalidade no dashboard de projetos de DevOps.


## <a name="next-steps"></a>Passos Seguintes

Quando você configurou o processo de CI/CD, os pipelines de compilação e de versão foram criados automaticamente. Pode modificar estes pipelines de compilação e de lançamento para satisfazer as necessidades da sua equipa. Para saber mais sobre o pipeline de CI/CD, consulte:

> [!div class="nextstepaction"]
> [Personalizar o processo de CD](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

## <a name="videos"></a>Os meus vídeos

> [!VIDEO https://www.youtube.com/embed/3etwjubReJs]
